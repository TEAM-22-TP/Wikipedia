## Charakteristika a požiadavky senzorických dát

Digitálne dvojča výrobnej prevádzky (v našom prípade továrne na spracovanie zemiakov) vyžaduje nepretržité získavanie, spracovanie a uchovávanie veľkého množstva senzorických dát v reálnom čase [1]. Tieto dáta zahŕňajú rôzne fyzikálne veličiny – predpokladané sú senzory teploty, vlhkosti, hmotnosti (váhy), tlaku, prípadne vibrácií či spotreby energie a ďalších parametrov prostredia a strojov [2]. Každé meranie je spravidla označené časovou pečiatkou, takže vzniká časová postupnosť údajov (tzv. time series dáta). V prostredí IIoT (Priemyselného internetu vecí) generujú stovky až tisíce senzorov obrovské objemy dát kontinuálne [1].

Systém pre digitálne dvojča preto musí zvládnuť vysokú frekvenciu zápisu (pri mnohočetných senzoroch môžu pribúdať nové hodnoty každú sekundu alebo častejšie) a veľký objem údajov z dlhodobého hľadiska.

Okrem čisto dátového objemu je kľúčová aj rýchlosť spracovania a dostupnosť v reálnom čase. Digitálne dvojča musí takmer okamžite reflektovať zmeny v reálnej továrni, aby umožnilo monitorovanie a prípadne zásahy do procesu. Zároveň je potrebné údaje archivovať na neskoršiu analýzu – či už na historické vyhodnocovanie výkonnosti, hľadanie trendov, alebo tréning modelov pre prediktívnu údržbu.

Teda systém musí podporovať dvojitý účel: on-line analýzu v reálnom čase aj dlhodobú archiváciu dát [3]. To typicky znamená zavedenie tzv. hot path pre najnovšie dáta (okamžité spracovanie a ukladanie aktuálnych údajov pre rýchle dotazy) a paralelne cold path pre dlhodobé uloženie väčších objemov dát na lacnejšom úložisku [4].

Výzvou v tomto projekte je neznalosť konkrétnych senzorov a ich rozhraní v danej fabrike – nie je jasné, aké typy senzorov budú použité ani akým spôsobom budú komunikovať (či už pôjde o jednotné digitálne rozhranie, alebo len analógové výstupy). Analýza preto musí pokryť rôzne scenáre integrácie senzorov a zberu dát.

V nasledujúcich častiach preto rozoberáme typy senzorov a ich rozhrania, vhodné middleware pre zber a prenos senzorických údajov a napokon samotné databázové systémy vhodné na ukladanie senzorických dát, vrátane porovnania tradičných relačných databáz s časovými databázami a NoSQL prístupmi. Neopomenieme ani stratégiu real-time spracovania vs. archivácie údajov.

## Typy senzorov a ich rozhrania v priemyselnej továrni

V továrni môžeme očakávať širokú škálu senzorov – od teplotných čidiel v tepelných procesoch, cez váhové senzory na meranie hmotnosti surovín či produktov, vlhkostné senzory (napr. pri sušení zemiakov alebo kontrole skladovania), tlakové senzory (v parných zariadeniach alebo hydraulike), až po snímače vibrácií v motoroch a pod. [2].

Každý typ senzora môže mať iný spôsob výstupu údajov a rozhrania.

### Analógové senzory

Analógové senzory poskytujú spojitý elektrický signál – typicky napäťový (napr. 0–10 V) alebo prúdový (napr. 4–20 mA) úmerný meranej hodnote. Príkladom môžu byť termočlánky (milivoltový signál úmerný teplote), odporové teplomery (mení sa odpor), tlakové senzory s analógovou membránou, tenzometre v mostíku pre meranie hmotnosti, atď. [5][6].

Takéto senzory nemajú digitálne rozhranie, a preto vyžadujú prevod analógového signálu na digitálne dáta. V priemysle sa na to často používajú PLC jednotky s analógovými vstupnými modulmi alebo samostatné ADC moduly.

Každý analógový vstup PLC obsahuje ADC prevodník, ktorý kontinuálny elektrický signál konvertuje na digitálne číslo spracovateľné počítačom [7].

Inými slovami, každý analógový senzor musí byť pripojený cez príslušnú vstupnú kartu (modul) PLC alebo iného zberného zariadenia, ktorá zabezpečí digitalizáciu analógového signálu [7].

Moderné PLC systémy podporujú oba typy senzorov – analógové aj digitálne, no pre analógové potrebujú špeciálne analógové vstupné karty (s ADC), kým digitálne senzory sa pripájajú na bežné digitálne vstupy [8].

### Digitálne senzory

Digitálne senzory poskytujú výstup už v digitálnej forme – buď ako digitálny signál (napríklad binárny stav alebo pulzy), alebo komunikujú cez nejaký komunikačný protokol.

Príklady:
- snímače prítomnosti môžu dávať priamo digitálny (on/off) signál alebo impulzy
- enkodéry dávajú pulzy
- sofistikovanejšie senzory (napr. inteligentné teplotné a vlhkostné čidlá) môžu mať integrovaný mikrokontrolér

Takéto senzory môžu komunikovať cez rozhrania typu:
- I²C
- SPI
- RS-485
- USB
- Ethernet

V priemyselnom prostredí sú bežné aj štandardy ako Modbus (RS-485), prípadne Profibus, DeviceNet, Ethernet/IP a ďalšie poľné zbernice pre PLC systémy [11].

Mnohé moderné senzory podporujú priame pripojenie do siete – napríklad cez Ethernet alebo WiFi s protokolmi ako MQTT alebo HTTP (čo sa často využíva v IoT zariadeniach). Bezdrôtové senzory môžu zas používať protokoly typu Zigbee, Bluetooth LE alebo pri priemyselnom nasadení časť štandardu IO-Link Wireless a podobne.

Pre náš projekt je podstatné, že rôzne senzory môžu mať rozličné rozhrania a komunikačné štandardy. Ak továreň nemá jednotný systém, integrácia takýchto senzorov si vyžiada konverziu a zjednotenie dátového rozhrania.

Napríklad:
- ak teplotné čidlo poskytuje len analógový signál, musíme ho prečítať PLCčkom alebo IoT bránou
- ak iné zariadenie komunikuje cez RS-485/Modbus, potrebujeme modul alebo bránu, ktorá vie túto komunikáciu čítať a prekladať
- ak niektoré nové IoT senzory posielajú dáta cez MQTT po WiFi, musíme v infraštruktúre počítať aj s MQTT brokerom

Rozdielne signály a komunikačné štandardy medzi senzormi si vyžadujú dodatočné vybavenie na prevod, čo zvyšuje zložitosť systému [12].

Preto, ak je to možné, je výhodné vybrať senzory aj podľa rozhrania, aby sme minimalizovali počet potrebných prevodníkov a mohli údaje zbierať jednotným spôsobom [12].

---

## OPC UA ako zjednocujúca vrstva

V ideálnom prípade by továreň mala unifikovanú vrstvu pre zber dát zo senzorov. V priemysle 4.0 sa ako štandardné riešenie často používa protokol OPC UA (OLE for Process Control Unified Architecture).

OPC UA môže fungovať ako nadstavba nad rôznymi zariadeniami a senzormi – napríklad PLC všetkých výrobcov – a poskytuje jednotné dátové rozhranie pre vyššie vrstvy systémov.

Inými slovami, OPC UA server dokáže agregovať dáta z rôznych senzorov a riadiacich jednotiek a navonok ich prezentovať v jednotnej štruktúrovanej podobe vrátane možnosti konfigurácie a monitorovania [13].

Podľa štúdií nasadenia OPC UA v senzorových sieťach táto technológia poskytuje zjednotené rozhranie pre prístup k dátam, konfiguráciu, monitorovanie a kalibráciu zariadení, čo zjednodušuje integráciu, zlepšuje škálovateľnosť a otvára možnosti ako je integrované riadenie kvality či vytvorenie digitálneho dvojčaťa [13].

Ak by teda vo fabrike existovala OPC UA vrstva (či už priamo v PLC systéme alebo cez externý gateway), digitálne dvojča by mohlo získavať údaje jednoduchšie týmto štandardizovaným spôsobom.

V opačnom prípade – ak žiadna jednotná platforma nie je – bude potrebné počítať s vývojom vlastnej integračnej vrstvy, ktorá zahrnie rôzne vstupy, napríklad modul na čítanie analógových vstupov, modul pre Modbus/RS-485 linky, modul odoberajúci správy z MQTT alebo iných IoT protokolov.

Táto vrstva môže bežať na priemyselnom počítači alebo IoT bráne (gateway) priamo v továrni a zabezpečí zosúladenie dát do spoločného formátu (napr. JSON správy s poľami čas, typ senzora, hodnota atď.). Následne sa dáta budú odosielať do centrálnych systémov.

## Translation Layer

Na to, aby bolo možné efektívne prenášať dáta z prostredia výrobných zariadení do informačných systémov, je potrebné zabezpečiť vrstvu, ktorá dokáže spracovať surové OPC UA dáta a transformovať ich do jednotného interného formátu. Táto časť architektúry, označovaná ako Translation Layer, stojí medzi OPC UA serverom a middleware (napr. MQTT alebo RabbitMQ brokerom) a rieši všetky úlohy spojené s normalizáciou, filtrovaním a prekladom dát.

OPC UA síce poskytuje bohatý dátový model, no nie je priamo vhodný na rýchlu distribúciu do GUI, databáz alebo kontrolných komponentov. Jednotlivé OPC UA uzly môžu mať nejednotné názvy, štruktúry alebo rôzne kvalitatívne atribúty, ktoré je potrebné pretransformovať do jednoduchej štruktúry (napr. JSON), aby sa s nimi dali pracovať v middleware, v GUI aj pri ďalšej analytike.

Translation Layer preto rieši tieto úlohy:
- výber relevantných parametrov z OPC UA servera
- mapovanie údajov na interný dátový model
- doplnenie metaúdajov (timestamp, ID linky, kvalita signálu)
- konverziu do formátu vhodného na prenos cez middleware
- odoslanie transformovaných dát do message brokeru

Keďže projekt predpokladá škálovanie a budúcu možnosť rozšírenia o optimalizačné algoritmy, je vhodné použiť taký prekladový nástroj, ktorý dokáže bežať ako edge komponent medzi PLC a cloudovou infraštruktúrou. Na tento účel existujú špecializované priemyselné riešenia, ktoré sú navrhnuté presne na spracovanie a transformáciu OPC UA dát.

### Hopit Edge IIoT Gateway

Hopit Edge predstavuje modernú edge gateway, ktorá umožňuje preklad údajov medzi priemyselnými protokolmi (vrátane OPC UA) a IT systémami. Ponúka možnosť definovať dátové mapovania, transformácie, zjednocovanie tagov a konverziu do JSON alebo iných štruktúr.

Je vhodná najmä tam, kde potrebujeme pevný, konfiguráciou riadený nástroj, ktorý zabezpečí:
- OPC UA → JSON transformáciu
- filtráciu a výber dôležitých signálov
- obohatenie údajov o kontext
- odoslanie dát smerom do MQTT, REST API alebo databázových služieb

V porovnaní s inými riešeniami ponúka jednoduché grafické prostredie a schopnosť fungovať priamo na edge zariadeniach, čo znižuje záťaž na vyššie vrstvy systému.

### EMQX Neuron

EMQX Neuron je priemyselná IoT edge brána, ktorá je navrhnutá na efektívne získavanie dát z PLC a ich transformáciu do ľahko spracovateľných formátov. Podporuje OPC UA, Modbus, Siemens S7 a ďalšie protokoly, pričom umožňuje vytvárať dátové profily, konverzie a mapovania.

Výhody pre náš systém:
- jednoduchá konfigurácia OPC UA dátových tokov
- automatická konverzia do JSON telemetry
- schopnosť nízkolatenčného spracovania dát pred odoslaním do middleware
- stabilná 24/7 prevádzka vhodná pre priemyselné prostredia

Neuron dokáže pôsobiť ako „predspracovacia stanica“, ktorá zabezpečí, že middleware a GUI dostávajú už čisté, rovnaké a systematicky štruktúrované dáta.

### Node-RED (ak je použitý s doplnkami OPC UA)

Hoci v predchádzajúcich častiach dokumentu vystupoval Node-RED skôr ako middleware komponent, je možné ho zaradiť aj do úlohy Translation Layer. Node-RED ponúka doplnky ako node-red-contrib-opcua, ktoré umožňujú:
- čítať a odoberať dáta z OPC UA servera
- spracovať tieto dáta cez funkčné bloky
- transformovať ich na JSON alebo iné štruktúry
- prípadne ich obohatiť o kontext alebo logiku
- následne ich poslať cez MQTT alebo HTTP

Výhodou Node-RED je flexibilita a rýchle prototypovanie, ktoré je vhodné pre akademické alebo výskumné projekty. Nevýhodou je, že nejde o čisto priemyselný produkt, preto nemusí byť vhodný pre dlhodobú, kritickú prevádzku vo výrobe.


## Middleware pre zber a prenos senzorických dát

Middleware v kontexte IoT a digitálnych dvojčiat predstavuje sprostredkovateľskú vrstvu, ktorá zabezpečuje spoľahlivý tok dát od senzorov (resp. od lokálnych riadiacich systémov) do databáz a aplikačných systémov digitálneho dvojčaťa. Táto vrstva je kľúčová najmä v prostredí, kde treba prepojiť nové IoT riešenie s existujúcimi (legacy) systémami továrne – typicky integrácia starších strojov a PLC s moderným cloudovým alebo databázovým systémom nie je priamočiara.

Štúdie uvádzajú, že práve integrácia viacerých zdrojov dát a systémov patrí medzi hlavné výzvy pri implementácii digitálneho dvojčaťa a často si vyžaduje nasadenie middleware a API vrstiev na zaistenie plynulého toku dát medzi novými a starými systémami [8]. Inak povedané, na prekonanie nekompatibility a uzavretosti starších zariadení je nutné použiť dodatočný softvér, ktorý sprostredkuje komunikáciu (napr. OPC UA wrapper pre starý PLC, alebo IoT gateway, ktorá odčíta údaje z čidiel a pošle ich ďalej moderným protokolom).

Pre náš projekt prichádza do úvahy viacero middleware komponentov, napríklad:

### Message broker / Event streaming platforma

Ide o systémy určené na príjem, vyrovnávanie a distribúciu správ zo zariadení. V IoT prostredí sa často používa protokol MQTT (lightweight broker pre publikovanie/odber správ), alebo pre väčšie objemy a komplexnejšie spracovanie Apache Kafka (distribuovaná streamingová platforma).

MQTT broker (ako Mosquitto, HiveMQ a pod.) je vhodný na odosielanie telemetrie z embedovaných senzorov, najmä keď potrebujeme efektívnosť na slabom hardvéri a sieťach – ide o otvorený štandard podporovaný mnohými IoT zariadeniami [14].

Apache Kafka je priemyselný štandard pre vysokoprúdové dáta. Je často používaný ako centrálna streamingová platforma pre škálovateľné a spoľahlivé digitálne dvojča, pretože dokáže spracúvať obrovské množstvo senzorových udalostí v reálnom čase a ukladať ich v distribuovanom clustri [15].

Kafka funguje ako ústredná dátová chrbtica, kam senzory (resp. IoT brány) publikujú udalosti a z ktorej si rôzni spotrebitelia (consumery) môžu tieto dáta odoberať podľa potreby. Napríklad:
- jedna služba údaje ukladá do databázy
- iná aktualizuje simulačný model digitálneho dvojčaťa
- ďalšia vykonáva real-time analytické výpočty

Tento prístup zaisťuje silné oddelenie producentov a konzumentov dát a jednoduchú škálovateľnosť. Nové komponenty sa môžu pripojiť na odber dát bez toho, aby ovplyvnili ostatných [16]. Navyše, Kafka uchováva log udalostí po určitú dobu, čím umožňuje aj dodatočné spracovanie alebo replay starších udalostí.

V praxi sa často kombinuje IoT protokol s event-streamingom. Napríklad na okraji siete bežia senzory komunikujúce cez MQTT a existuje bridge do Kafky pre centrálny zber, alebo sa využijú Kafka Connect moduly na priame prepojenie s PLC (napr. pomocou Apache PLC4X) [16].

### IoT platforma (cloudová alebo on-premise)

Existujú hotové riešenia ako Azure IoT Hub, AWS IoT Core, Google IoT Core a podobne, ktoré poskytujú manažovaný ingest dát z IoT zariadení, ich prechodné uloženie, spracovanie v prúde a odosielanie do ďalších služieb.

Napríklad Azure IoT Hub umožňuje definovať tzv. routing – správy zo senzorov môžu ísť paralelne do viacerých cieľov, napríklad:
- jeden smer priamo do databázy pre hot storage
- druhý smer do lacného blob úložiska ako cold archive [4]

Tieto platformy často spolupracujú so serverless funkciami alebo stream analytickými službami (Azure Stream Analytics, Amazon Kinesis a pod.), ktoré v reálnom čase vyhodnocujú prichádzajúce dáta (napr. detekujú alarmy, agregujú údaje) [17][18].

Výhodou cloud IoT platforiem je, že odbremeňujú systém od časti starostí – škálujú príjem dát a ponúkajú pripravené integrácie (napr. dočasné ukladanie do Time Series Insights alebo priamy zápis do SQL/NoSQL databázy). Nevýhodou môže byť závislosť na konkrétnom vendorovi a nutnosť mať spoľahlivé pripojenie do internetu z výrobného prostredia.

### OPC UA servery a IIoT frameworky

Ak fabrika už používa OPC UA, tento server sám o sebe funguje ako middleware – klient digitálneho dvojčaťa by sa mohol pripojiť a odčítavať hodnoty premenných (senzorov) v reálnom čase.

Pre veľmi rýchle procesy alebo uzavreté regulačné slučky však OPC UA (najmä v režime klient–server) môže prinášať dodatočnú latenciu. V takých prípadoch by realtime riadenie zostalo na PLC a digitálne dvojča by dostávalo dáta s malým oneskorením len na monitorovanie.

Existujú aj moderné prístupy OPC UA Pub/Sub, ktoré umožňujú streamovať dáta z OPC UA serverov do brokerov (napr. MQTT broker alebo Kafka topic) pre lepšiu integráciu.

Ďalšie open-source IoT middlewares zahŕňajú napríklad Node-RED (grafický nástroj na integráciu senzorov, vhodný pre rýchly prototyp zberu dát) alebo Eclipse IoT komponenty (Kura, Kapua, Ditto a iné), ktoré poskytujú základ pre IoT brány a manažment zariadení [14].

Voľba middleware teda závisí od konkrétnych podmienok projektu. Ak je cieľom open-source riešenie a modularita, Kafka alebo MQTT s vlastnou logikou spracovania sú vhodné. Ak preferujeme rýchle nasadenie a neprekáža cloud, IoT platforma ako Azure alebo AWS IoT môže výrazne urýchliť vývoj.

Každopádne sa odporúča zaradiť do architektúry vrstvu sprostredkovania správ namiesto priameho zápisu senzorov do databázy. Middleware poskytne bufferovanie (ochranu databázy pred zahltením nárazovými dávkami dát), umožní real-time spracovanie (napr. výpočet agregácií alebo detekciu anomálií v streame ešte pred uložením dát) a zlepší škálovateľnosť a odolnosť systému.

Napríklad architektúra digitálneho dvojčaťa môže fungovať tak, že IoT platforma alebo broker zbiera všetky senzorické správy, tie sa cez stream processing transformujú a filtrujú a následne:
- sa odosielajú do časovej databázy pre historické uloženie a vizualizácie (dashboards)
- paralelne idú do komponentu, ktorý aktualizuje virtuálny model (simuláciu) digitálneho dvojčaťa
- prípadne sa niektoré dáta ukladajú surovo do tzv. data lake alebo archívu pre offline analýzy

Týmto spôsobom je zabezpečená ako aktuálnosť digitálneho dvojčaťa (dáta tečú priebežne), tak aj dlhodobá uchovateľnosť informácií.

## Databázové systémy pre senzorické dáta

Hlavnou časťou riešenia je databázový systém, kde budú senzorické dáta uchovávané. Pre tento účel existuje viac prístupov – relačné databázy (SQL), špecializované časové databázy (Time-Series DB) a rôzne NoSQL databázy (dokumentové, širokostĺpcové atď.). Keďže nemáme vopred preferovanú technológiu, rozoberieme výhody a nevýhody jednotlivých riešení v kontexte senzorických dát a digital twins.

### Relačné databázy (SQL) pre senzorické dáta

Tradičné relačné databázy ako PostgreSQL, MySQL, Oracle, Microsoft SQL Server a pod. ukladajú dáta do tabuliek s vopred definovanou štruktúrou (schémou) a používajú jazyk SQL na dotazovanie. Relačné systémy sú osvedčené desaťročiami vývoja – poskytujú ACID vlastnosti (transakčnú spoľahlivosť), silné mechanizmy na zaistenie integrity dát (cudzie kľúče, constraints) a možnosť definovať vzťahy medzi tabuľkami (normalizovaný dátový model).

Pri zbere dát z IoT senzorov by mohla relačná DB obsahovať napríklad tabuľku `Measurements` so stĺpcami: čas, senzor_id, typ, hodnota, a ďalšie tabuľky popisujúce senzory, zariadenia a podobne, čím by sa dali údaje prepojiť s meta-informáciami (napr. senzor patrí k určitému stroju, linke atď.) [19][20].

#### Výhody relačných DB v IoT kontexte

- **Datová integrita a konzistencia**  
  Vysoká spoľahlivosť ukladania – vďaka transakciám sa nestane, že by čiastočne zapísané alebo nekonzistentné dáta poškodili databázu. To je dôležité, ak okrem samotných meraní potrebujeme uchovávať aj iné prepojené údaje (napr. výrobné dávky, receptúry, logistické informácie).

- **SQL dotazy a analytické možnosti**  
  SQL umožňuje komplexné dopyty, spájanie tabuliek (JOINy) a agregácie. Ak by sme chceli analyzovať senzorické dáta spolu s biznis dátami (napr. kvalita produktu vs. teplota v procese a pod.), relačná DB to dokáže jedným dotazom, pokiaľ sú dáta v spoločnom systéme [21][20].

- **Zrelý ekosystém**  
  Existuje množstvo nástrojov na správu, vizualizáciu a ladenie relačných DB, ako aj odborníci, ktorí ich dobre poznajú. Škálovanie vertikálne (výkonnejší hardvér) je pomerne priamočiare a relačné DB zvládnu slušné objemy dát, pokiaľ sa správne nastavia indexy a partície.

#### Nevýhody a výzvy

- **Výkon pri vysokom vkladaní časových dát**  
  Senzorické dáta predstavujú typicky tzv. time-series workload – veľa malých záznamov pridávaných rýchlo v čase. Relačné DB to zvládnu, ale pri veľmi vysokých frekvenciách zápisu môžu narážať na limity. Zápis stoviek tisíc riadkov za sekundu vyžaduje starostlivé ladenie. Veľké tabuľky a dotazy nad dlhými časovými rozsahmi môžu byť pomalšie, ak nie sú použité špeciálne optimalizácie. Vo všeobecnosti platí, že pri veľmi vysokých rýchlostiach ingestu je nutné relačnú DB optimalizovať (particionovanie dát podľa času, indexy na časových stĺpcoch atď.), inak výkon klesá [22].

- **Nutnosť schémy a menšia flexibilita**  
  Je potrebné dopredu definovať štruktúru tabuliek. Hoci to nie je zásadný problém (senzorické dáta mávajú jednoduchú schému: čas, senzor, hodnota), pridávanie nových typov údajov alebo zmena formátu môže vyžadovať migrácie schémy. V heterogénnom IoT prostredí, kde rôzne senzory môžu posielať rôzne formáty dát, to môže byť obmedzujúce (NoSQL riešenia sú v tomto flexibilnejšie).

- **Úložisko a údržba historických dát**  
  Relačné DB nemajú inherentné mechanizmy na automatické starnutie dát. Veľké množstvo historických záznamov teda buď zostáva v tabuľkách (zaberá miesto a spomaľuje dotazy), alebo je potrebné implementovať vlastné skripty na archiváciu alebo vymazávanie starých dát po určitom čase. Pre dlhodobý historický archív môže byť klasická SQL databáza nákladná na úložisko, ak by sa všetky dáta držali online.

Napriek týmto výzvam relačné databázy v IoT projektoch nachádzajú uplatnenie, najmä ak je potrebné prepojiť senzorické dáta s inými podnikovými dátami. Napríklad môžeme mať relačnú DB, kde okrem meraní senzorov existujú aj tabuľky výrobných lotov, informácie o šaržiach zemiakov či časy odstávok liniek, a jedným SQL dopytom je možné analyzovať súvislosti.

Navyše, niektoré moderné relačné DB (napr. PostgreSQL s rozšírením TimescaleDB) dokážu kombinovať výhody oboch svetov – poskytujú SQL rozhranie a integritu, ale zároveň optimalizujú ukladanie časových dát pomocou partícií po časových úsekoch, kompresie starých dát a ďalších mechanizmov.

**Zhrnutie:**  
Relačnú databázu zvolíme v prípade, že potrebujeme komplexné vzťahy v dátach, striktnú konzistenciu a integráciu s inými biznis dátami, a zároveň očakávané zaťaženie nepresiahne možnosti tejto databázy (prípadne sme ochotní investovať do škálovania a ladenia). Pri extrémne rýchlom streame dát však zvyčajne prichádzajú na rad špecializované riešenia.

## Časové (time-series) databázy pre senzorické dáta

Časové databázy sú systémy navrhnuté špeciálne na ukladanie a dotazovanie časovo značených dát (time series), aké produkujú senzory, metriky serverov, finančné tickery a pod. Príklady populárnych open-source time-series databáz sú InfluxDB, Prometheus, TimescaleDB (nadstavba Postgresu), OpenTSDB, Graphite a ďalšie. Tieto systémy prinášajú prispôsobenú architektúru a funkcie práve pre dátové sekvencie typu čas → hodnota.

### Výhody časových databáz pre IoT senzory

- **Optimalizácia pre rýchly zápis a čítanie časových údajov**  
  Time-series databázy sú stavané na veľmi vysokú priepustnosť zápisu (mnoho tisíc udalostí za sekundu) bez výrazného poklesu výkonu [23][24]. Dáta sú indexované primárne podľa času (prípadne kombináciou čas + senzor), čo umožňuje efektívne dotazy v časových intervaloch. Typické dopyty ako „daj mi údaje z tohto senzora za poslednú hodinu“ alebo „vypočítaj priemer teploty za každý deň v mesiaci“ sú v TSDB veľmi rýchle, práve vďaka časovej optimalizácii.

- **Komprimácia a úspora miesta**  
  Senzorické dáta sú často redundatné alebo postupné, a TSDB to dokážu využiť. Implementujú agresívne kompresné algoritmy a ukladajú dáta v columnar formáte, čím výrazne znižujú nároky na disk [24].  
  Mnohé TSDB umožňujú definovať aj tzv. downsampling – automatické znižovanie rozlíšenia starších dát. Napríklad detailné sekundové hodnoty sa uchovávajú jeden mesiac, zatiaľ čo pre staršie obdobia sa uloží už len päťminútový priemer. Tým sa zachovajú trendy bez potreby držať obrovské množstvo detailných údajov [25][26].

- **Retention policy (politiky uchovávania dát)**  
  Na rozdiel od bežných SQL databáz časové databázy často natívne podporujú nastavenie automatického mazania alebo presunu dát starších než určitý časový interval. Napríklad v InfluxDB je možné pre konkrétny bucket nastaviť, že dáta staršie ako jeden rok automaticky expirujú a vymažú sa [24].  
  Niektoré TSDB (napr. InfluxDB IOx, QuasarDB a ďalšie) podporujú aj multi-tier storage, kde „hot“ dáta s krátkou históriou zostávajú na rýchlom úložisku a „cold“ historické dáta sa presúvajú na lacnejšie úložisko (napr. cloudové S3) transparentne [27]. Systém tak dokáže škálovať dlhodobé uloženie veľkých objemov dát nákladovo efektívne, pričom aktuálne údaje sú veľmi rýchlo dostupné [27].

- **Špecializované funkcie pre časové dáta**  
  TSDB často ponúkajú priamočiare nástroje na výpočty bežiacich priemerov, maxím a miním v čase, Fourierove transformácie signálu, detekciu medzier v dátach, interpoláciu chýbajúcich vzoriek a podobne.  
  Umožňujú tak jednoduchšie vytvárať analýzy senzorických dát priamo na úrovni databázy. Niektoré používajú vlastný dotazovací jazyk prispôsobený práci s časom (InfluxQL, PromQL), iné rozširujú SQL o time-series funkcie (napr. TimescaleDB nad PostgreSQL).

### Nevýhody alebo obmedzenia time-series databáz

- **Menej vhodné na relačné prepojenia**  
  Tieto systémy sú optimalizované na append-only časové rady. Ak je potrebné vykonávať zložité JOIN operácie s inými tabuľkami (napr. kombinovať merania s tabuľkami výrobkov alebo zákaziek), TSDB to buď neumožňujú, alebo je to neefektívne.  
  Hoci sú často schema-less pre samotné merania, nie sú stavané na transakčnú prácu s viacerými entitami naraz, a preto nenahrádzajú plnohodnotnú SQL databázu v prípade potreby silnej integrity dát.

- **Špecifický ekosystém**  
  Keďže nejde o klasické SQL databázy, je potrebné používať špecifické nástroje a dotazovacie jazyky. Aj keď napríklad Grafana poskytuje výbornú podporu pre TSDB, učenie sa nového dotazovacieho jazyka predstavuje dodatočnú krivku učenia.

- **Horizontálna škálovateľnosť**  
  Niektoré open-source TSDB (napr. klasická InfluxDB OSS) fungujú len na jednom uzle. Pre horizontálne škálovanie sú potrebné enterprise alebo cloudové verzie, prípadne použitie iných TSDB navrhnutých ako distribuované systémy (napr. riešenia postavené nad Cassandrou, M3DB a podobne).  
  Preto je nutné vopred overiť, do akého objemu dát konkrétna databáza spoľahlivo funguje na jednom serveri a kedy je potrebný cluster.

Vo výsledku time-series databázy v IoT doméne často prevládajú, pretože väčšina use-casov presne zodpovedá tomu, na čo sú navrhnuté – ukladanie sekvencií senzorických meraní a efektívne časové dotazy a agregácie. Ako uvádza literatúra, ak zariadenie posiela čítania teploty, vlhkosti či tlaku každú sekundu, time-series databázy sú optimalizované na rýchly zápis, rýchle čítanie a časovo podmienené dotazy [28]. Ich vstavané mechanizmy retencie a kompresie zároveň výrazne uľahčujú archiváciu a šetria úložisko [24].

Pre náš projekt by špecializovaná time-series databáza (napr. InfluxDB alebo TimescaleDB) dávala zmysel na ukladanie surových senzorických dát, najmä ak predpokladáme vysokú frekvenciu a dlhodobé uchovávanie. Uľahčila by správu historických dát – napríklad nastavením retencie šesť mesiacov pre sekundové dáta a dlhšou retenciou pre agregované údaje.  

Ak by však vznikla potreba prepájať tieto dáta s inými relačnými údajmi, je možné použiť hybridný prístup (viď nižšie) alebo siahnuť po riešeniach typu TimescaleDB, ktoré umožňujú SQL dotazy nad časovými dátami.

---

## NoSQL databázy a ďalšie úložiská

Okrem vyššie uvedených prístupov stojí za zmienku aj kategória NoSQL databáz, ktoré môžu v IoT riešeniach plniť úlohu úložiska senzorických dát, najmä ak je požiadavkou extrémna škálovateľnosť, geografická distribúcia alebo flexibilná schéma.

### Wide-column databázy (Cassandra a klony)

Apache Cassandra je širokostĺpcová distribuovaná databáza, ktorá exceluje v scenároch s obrovským objemom zápisov a požiadavkou na škálovanie naprieč viacerými dátovými centrami. Cassandra je navrhnutá tak, aby bežala na clusteri viacerých uzlov bez jediného bodu zlyhania a s lineárnou škálovateľnosťou – pridanie ďalších uzlov zvyšuje kapacitu systému [29][30].

Pre IoT aplikácie je Cassandra často odporúčaná, pretože je ideálna na ingestovanie, ukladanie a spracovanie prúdov senzorických dát v reálnom čase. Zároveň poskytuje vysokú dostupnosť a schopnosť fungovať aj pri výpadku niektorých uzlov alebo celých lokalít [31].

Údaje v Cassandre sú organizované podľa partition key, ktorým môže byť identifikátor senzora alebo zariadenia, a sort key býva čas. Tým sa prirodzene ukladajú časové rady pre jednotlivé senzory pohromade. Výhodou je aj absencia pevnej schémy stĺpcov – rôzne senzory môžu mať rôzne atribúty v rámci jednej tabuľky. Nevýhodou je slabšia podpora ad-hoc analytických dotazov, keďže Cassandra nepodporuje ľubovoľné filtrovanie ani JOIN operácie bez použitia doplnkových nástrojov (napr. Apache Spark).

V našom kontexte by Cassandra dávala zmysel v prípade skutočne masívnych dátových objemov (rádovo miliardy záznamov) alebo pri potrebe globálnej distribúcie dát medzi viaceré závody. Mnohé veľké IoT implementácie (napr. mestské senzorické siete alebo telekomunikačné monitoringy) používajú Cassandru práve pre jej spoľahlivosť a škálovateľnosť [31].

### Dokumentové databázy (MongoDB a pod.)

Dokumentové databázy ako MongoDB je možné použiť najmä vtedy, ak dáta z rôznych senzorov majú výrazne odlišnú štruktúru alebo je potrebné ukladať komplexnejšie dokumenty (napr. JSON so stavom viacerých čidiel naraz).

Pre jednoduché časové rady však nejde o typickú voľbu, keďže dokumentové databázy nemajú takú efektivitu časových indexov ako špecializované TSDB. Novšie verzie MongoDB síce ponúkajú time-series collections, ktoré optimalizujú ukladanie časových dát, no pri vysokofrekvenčných zápisoch je stále potrebné dôkladné ladenie indexov a často aj shardovanie dát podľa času alebo zariadenia.

Vo väčších IoT nasadeniach sa MongoDB používa skôr na metadata a menej frekventované dáta, zatiaľ čo vysokofrekvenčné časové dáta sa ukladajú do iných systémov [32].

### Data lakes a súborové úložiská

Pri archivácii veľmi veľkého množstva historických dát, ktoré nie je nutné držať neustále online, prichádza do úvahy ukladanie dát v súborovej forme, napríklad ako CSV alebo Parquet súbory v Hadoop ekosystéme alebo cloudovom storage.

Tieto dáta nie sú okamžite dotazovateľné v reálnom čase, ale dajú sa analyzovať pomocou big data nástrojov ako Spark alebo Hive. Často ide o doplnok k databázovým riešeniam – napríklad detailná história sa po určitom čase presunie do data lake a v hlavnej databáze zostanú len agregované údaje. Cloudové architektúry Azure a AWS tento prístup podporujú v rámci tzv. cold path [33].

Pre náš projekt by data lake dával zmysel najmä pri potrebe veľmi dlhej histórie alebo offline analýz pomocou ML nástrojov.

---

## Zhrnutie voľby databázového riešenia

Bez vopred určenej technologickej preferencie sa ako najvhodnejší javí hybridný prístup. V literatúre sa často odporúča kombinácia InfluxDB a PostgreSQL – senzorické dáta sa ukladajú do InfluxDB kvôli rýchlosti zápisu a optimalizácii časových dotazov, zatiaľ čo metadáta o zariadeniach, používateľoch a biznis logike sa ukladajú do relačnej databázy [34].

Tento prístup poskytuje výhody oboch svetov – vysoký výkon pri práci s časovými radmi a zároveň možnosť relačných prepojov a transakcií pre ostatné dáta [34]. Ak je cieľom minimalizovať počet technológií, alternatívou môže byť TimescaleDB, ktorá umožňuje pracovať s časovými aj bežnými tabuľkami v rámci jednej databázy.

V prípade extrémnej škálovateľnosti alebo požiadavky na takmer nulový downtime môže prichádzať do úvahy Cassandra alebo iné NoSQL riešenia. Tie sú navrhnuté na nepretržitú prevádzku 24/7 aj pri výpadkoch a zvládajú masívne objemy zápisov [31]. Táto komplexnosť je však opodstatnená najmä pri veľmi veľkých továrňach s tisíckami senzorov generujúcimi dáta globálne.

## Analýza v reálnom čase vs. archivácia dát

Ako bolo zdôraznené, náš systém musí podporovať dva režimy využitia dát:

### 1. On-line spracovanie a analýza v reálnom čase

Digitálne dvojča potrebuje aktuálne informácie zo senzorov, aby mohlo simulovať a zobrazovať stav výroby takmer okamžite. To znamená, že od momentu, kedy senzor nameria hodnotu, po moment, kedy sa táto hodnota prejaví v digitálnom dvojčati (napr. na dashboarde alebo v logike modelu), musí uplynúť veľmi krátky čas (ideálne v ráde sekúnd či milisekúnd).

Ako to dosiahnuť?

- **Stream processing**  
  Už spomínaná middleware vrstva môže obsahovať streamovacie spracovanie – napr. pomocou Apache Spark Streaming, Flink, alebo cloud služby (Azure Stream Analytics). Tieto nástroje vedia údaje z prúdu okamžite analyzovať, spúšťať nad nimi pravidlá (napr. ak teplota > X, vytvor upozornenie) alebo počítať agregáty v pohyblivom okne. Výsledky môžu posielať do databázy aj do samotného vizualizačného systému.

- **Rýchle dotazy na „hot“ dáta**  
  Time-series databázy často držia najčerstvejšie dáta v pamäti alebo vo veľmi rýchlom úložisku, takže napríklad dotaz „aká je aktuálna hodnota teploty a aký bol priemer za posledných 5 minút“ môže byť vykonaný okamžite. InfluxDB napríklad má koncept tzv. hot-storage v pamäti pre najnovšie údaje [27]. Podobne TimescaleDB vie využívať predzahrievanie cache pre posledné partície. To umožňuje, že digitálne dvojča si môže v reálnom čase pýtať z databázy nové dáta každú sekundu a databáza to zvládne.

- **In-memory caching**  
  V niektorých prípadoch sa implementuje aj samostatná pamäťová vrstva – napr. posledné hodnoty všetkých senzorov sa držia v pamäťovej databáze typu Redis (key-value store), odkiaľ ich UI alebo simulačný modul čerpá veľmi rýchlo, a paralelne ide plný tok do perzistentnej databázy. Ide však o dizajnový detail navyše, ktorý nemusí byť nutný, ak to zvládne time-series databáza priamo.

- **Vizualizácia a notifikácia**  
  Pre reálny čas je dôležité mať nástroje, ktoré vedia kontinuálne zobrazovať dáta (dashboardy s auto-refreshom, event-driven grafy) a notifikovať v prípade problémov. Mnohé time-series platformy integrujú vizualizácie (napr. Grafana nad InfluxDB s live panelmi). Digitálne dvojča by malo obsahovať UI nad týmito real-time dátami, aby obsluha mala okamžitý prehľad o stave výroby.

### 2. Dlhodobá archivácia a historická analýza

Ukladanie dát po dlhú dobu umožní spätne analyzovať výkonnosť, kvalitu, príčiny porúch a podobne. Dôležité je preto mať stratégiu, ako tieto historické dáta uchovávať lacno, ale pritom dostupne pre analýzy.

Niekoľko odporúčaní z praxe:

- **Definícia retenčných období**  
  Pre každý typ dát je potrebné určiť, ako dlho sa uchovávajú v plnom detaile. Napríklad senzory vibrácií môžu generovať obrovské objemy dát – detailné dáta môžu stačiť na jeden mesiac a následne sa uchovajú už len denné štatistiky. Naopak kritické parametre kvality môže byť potrebné uchovávať po celé roky. Odporúča sa zamerať na dáta, ktoré prinášajú dlhodobú hodnotu, a ostatné agregovať alebo zahadzovať [35][25].

- **Automatizácia archivácie**  
  Je vhodné využiť funkcie databázy na expiráciu alebo presun dát. V time-series databázach je možné nastaviť retention policies – napr. InfluxDB dokáže automaticky mazať dáta staršie než definovaný časový interval [25]. Ďalšou možnosťou je downsampling, teda nahradenie starých dát redšími reprezentáciami (napr. po šiestich mesiacoch uložiť už len hodinové priemery). Tým sa výrazne zníži objem dát a zároveň sa zachová prehľad o trendoch [25][26].

- **Viacvrstvová úložná architektúra**  
  Ide o princíp hot vs. cold storage. Najčerstvejšie údaje sa nachádzajú na rýchlom úložisku (hot), strednodobé dáta na pomalšom úložisku (warm) a veľmi staré dáta (cold) sa presúvajú napríklad do cloudového storage alebo archivujú do data lake [27][36]. Niektoré moderné databázy podporujú tiered storage automaticky, inde sa to rieši manuálne, napríklad exportom mesačných partícií do súborov.

- **Využitie archívnych dát**  
  Historické dáta majú význam pre analytiku, reporting a strojové učenie. Pre prediktívnu údržbu platí, že kvalita predikcií závisí od dĺžky a kvality historických záznamov porúch, trendov opotrebenia a ďalších faktorov [37][38]. Archivácia teda neslúži len ako „odklad“, ale je aktívnym vstupom pre dátovú vedu.  
  V našom prípade by napríklad dáta z jedného roka výroby mohli odhaliť, že určitá kombinácia teploty a vlhkosti v procese vedie k horšej kvalite chipsov, alebo že stroj vykazoval jemné vibrácie mesiace pred poruchou. Z regulačného hľadiska v potravinárstve môže byť navyše povinné uchovávať určité dáta (napr. teplotné záznamy) kvôli auditom bezpečnosti potravín.

### Realizácia v systéme

On-line spracovanie a archivácia by mali fungovať spoločne. Ako príklad je možné nastaviť infraštruktúru tak, že IoT hub posiela dáta paralelne do dvoch cieľov: do operatívnej time-series databázy a zároveň do dlhodobého úložiska (napr. Azure Blob Storage, Hadoop alebo sekundárnej databázy).

Azure IoT Hub umožňuje práve takýto paralelný tok – správy môžu ísť jedným routom cez stream analytiku do SQL alebo TS databázy (hot path) a druhým routom priamo do blob storage (cold path) [39][40].

Podobný princíp sa dá aplikovať aj pri nasadení Apache Kafka. Je možné použiť Kafka Connect, ktorý odoberá dáta z Kafka topicu a ukladá ich do time-series databázy pre on-line dotazy, a paralelne iný Connect job, ktorý ukladá tie isté dáta do dlhodobého S3 úložiska ako Parquet súbory.

Pre používateľa je tento mechanizmus transparentný – v UI digitálneho dvojčaťa vidí posledné hodnoty a grafy (z time-series databázy), zatiaľ čo data scientist má k dispozícii celý surový dataset v archíve.

Na záver netreba zabúdať ani na zálohovanie a bezpečnosť dát. Databáza by mala byť pravidelne zálohovaná, prípadne pri distribuovaných systémoch replikovaná na viac uzlov alebo dátových centier. Prístup k citlivým údajom musí byť riadený. Aj keď senzorické dáta z výroby spravidla neobsahujú osobné údaje, môžu predstavovať cenné know-how (napr. receptúry procesov), a preto je potrebné ich primerane chrániť.

