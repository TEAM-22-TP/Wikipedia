## Návrh architektúry digitálneho dvojčaťa



Na základe vyššie uvedenej analýzy je zrejmé, že pre úspešnú realizáciu digitálneho dvojčaťa zemiakárenskej továrne je nutné navrhnúť flexibilnú a škálovateľnú architektúru schopnú pracovať s rôznymi typmi senzorov a veľkým objemom časovo štruktúrovaných dát.



---



### Integrácia senzorov



Keďže nemáme presné informácie o senzoroch, počítame so scenármi od analógových výstupov po digitálne protokoly. Odporúča sa nasadiť unifikačnú vrstvu (napr. OPC UA alebo IoT gateway), ktorá umožní zbierať údaje z rôznorodých zdrojov do jednej dátovej linky [13]. Tým sa zjednoduší napojenie digitálneho dvojčaťa – nemusí poznať detaily každého senzora, ale komunikuje len s touto vrstvou.



V prípade analógových senzorov treba zabezpečiť:

- kvalitné ADC moduly (napr. rozširujúce karty PLC),

- kalibráciu,



aby dáta boli presné a spoľahlivé [7].



Pre digitálne senzory treba definovať zbernicu (ak by nebola daná existujúcim systémom). V novom systéme by bolo výhodné, ak možno preferovať senzory s podporou štandardných digitálnych rozhraní, napríklad:

- Modbus RTU / TCP,

- priame publikovanie cez MQTT,



čím sa minimalizuje potreba rôznorodých prevodníkov [41].



---



### Spracovanie dát z OPC UA – translation vrstva



Predtým, než je možné odoslať dáta z výrobných senzorov do middleware alebo databázovej vrstvy, je nevyhnutné zabezpečiť ich konverziu z OPC UA do jednotného a spracovateľného formátu.



Na tento účel odporúčame použiť priemyselné edge gateway riešenia, ako napríklad:

- Hopit Edge IIoT Gateway,

- EMQX Neuron.



Tieto nástroje umožňujú:

- spoľahlivý zber a mapovanie OPC UA tagov,

- normalizáciu dát,

- transformáciu do JSON alebo telemetrických formátov vhodných pre ďalšie spracovanie.



Zároveň poskytujú konfigurovateľné dátové modely a nízku latenciu, pričom výrazne odľahčujú aplikačnú logiku digitálneho dvojčaťa.



Alternatívne je možné využiť aj Node-RED, ktoré vďaka rozšíreniam pre OPC UA umožňuje:

- rýchle prototypovanie,

- jednoduché dátové transformácie.



Pre dlhodobú priemyselnú prevádzku však odporúčame skôr špecializované edge gateway riešenia. Týmto spôsobom vznikne jednotná translation vrstva, ktorá zabezpečí konzistentný dátový formát pre middleware, databázy aj vizualizačné nástroje digitálneho dvojčaťa.



---



### Middleware a prenos dát



Na prepojenie senzorov s databázou a aplikáciou digitálneho dvojčaťa navrhujeme použiť middleware vrstvu v podobe message brokera alebo IoT platformy. Táto vrstva zabezpečí:

- spoľahlivé doručenie dát (aj pri dočasnej nedostupnosti databázy),

- real-time odbočenie dát na viac spracovateľov (napr. alertovací modul),

- jednoduchšiu integráciu so staršími systémami [8].



Konkrétna voľba môže byť:

- **pre menší rozsah a on-premise riešenie**:

&nbsp; - MQTT broker pre zber telemetrie,

&nbsp; - prípadne Apache Kafka pri potrebe škálovania a komplexnejšieho stream spracovania;

- **v prípade využitia cloudu**:

&nbsp; - napr. Azure IoT Hub so stream analytics.



Nech už je zvolené riešenie akékoľvek, architektúra musí podporovať:

- modularitu,

- rozšíriteľnosť,



teda aby pridanie nového typu senzora alebo ďalšej továrne nevyžadovalo prerábku celého systému, ale len pripojenie nového konektora do brokeru alebo IoT hubu [15].



---



### Databázová vrstva



Pre samotné ukladanie senzorických dát odporúčame primárne využiť časovú databázu (napr. InfluxDB alebo TimescaleDB). Tieto systémy sú priamo stavané na daný use case a poskytujú:

- vysoký výkon pri zápise veľkého množstva meraní za sekundu,

- efektívne dotazovanie podľa času,

- vstavané mechanizmy retencie a kompresie dát [23][24].



InfluxDB môže slúžiť ako centrálne úložisko všetkých meraní z fabriky, pričom je možné nastaviť politiky uchovávania, napríklad:

- detailné dáta po dobu 6 mesiacov,

- agregované trendy po dobu 5 rokov [42][26].



TimescaleDB umožňuje zostať v prostredí SQL, čo môže uľahčiť integráciu s existujúcimi IT systémami.



Zároveň je vhodné zvážiť použitie relačnej databázy popri TSDB, najmä na dáta, ktoré nie sú čisto senzorické, ale kontextové:

- informácie o strojoch,

- údržba,

- výrobné dávky,

- recepty procesu.



Tieto údaje môžu byť uložené v PostgreSQL alebo MS SQL, kde sa dobre modelujú relácie a zaručuje sa konzistencia. Hybridný prístup, kde sú senzorické dáta uložené v TSDB a ostatné v SQL databáze, je osvedčený postup [34].



Aplikácia digitálneho dvojčaťa môže:

- načítavať časové dáta z TSDB,

- obohacovať ich o kontext z relačnej DB (napr. názvy produktov, šarže),



čo je výhodné pre vizualizáciu aj analytické spracovanie.



NoSQL alternatívy, ako napríklad Cassandra, by boli vhodné až v prípade, že by objem dát alebo požiadavky (napr. geografická redundancia medzi viacerými závodmi) prekročili možnosti vyššie uvedených riešení. Cassandra garantuje:

- 24/7 dostupnosť aj pri výpadkoch uzlov,

- lineárnu škálovateľnosť pridaním nových serverov [43][31].



Nevýhodou je komplikovanejší ekosystém a obmedzenejšie dotazovanie. Pokiaľ zostaneme pri jednom závode a objem dát sa pohybuje v rádoch jednotiek až nízkych desiatok miliárd záznamov ročne, kvalitná TSDB na škálovateľnom hardvéri je postačujúca.



---



### Real-time spracovanie vs. archivácia



Pre splnenie oboch požiadaviek navrhujeme systém, ktorý paralelne rieši online aj offline dáta, konkrétne implementáciou dvojkoľajného spracovania (hot/cold path) [3]:



- **Hot path**  

&nbsp; Dáta z každého senzora putujú takmer okamžite do operatívnej databázy (TSDB) cez stream pipeline. Digitálne dvojča z nej čerpá aktuálne hodnoty a krátkodobé trendy, čo umožňuje:

&nbsp; - real-time vizualizáciu,

&nbsp; - rýchle alarmovanie.



&nbsp; Napríklad pri prekročení teplotného limitu sa alert vygeneruje už v streame a zapíše do databázy, odkiaľ ho dashboard zobrazí v priebehu niekoľkých sekúnd.



- **Cold path**  

&nbsp; Tie isté dáta sa simultánne (alebo s miernym oneskorením) ukladajú do archívu. Môže ísť o:

&nbsp; - druhý bucket InfluxDB s dlhšou retenciou,

&nbsp; - alebo iné úložisko (cloud blob storage, Hadoop).



&nbsp; Z archívu je možné vykonávať:

&nbsp; - hĺbkové off-line analýzy,

&nbsp; - tréning AI modelov,

&nbsp; - auditné a reportovacie výstupy pre manažment,



&nbsp; bez negatívneho vplyvu na výkon on-line systému. Archív zároveň slúži ako dodatočná ochrana dát v prípade havárie operatívnej databázy.



---



## Záver



Digitálne dvojča spracovne zemiakov bude úspešné, ak dokáže:

- spoľahlivo zhromažďovať heterogénne dáta zo všetkých senzorov,

- v reálnom čase ich využívať na modelovanie,

- budovať historickú pamäť pre kontinuálne zlepšovanie procesov.



Navrhnutá architektúra by mala byť:

- otvorená,

- škálovateľná,

- založená na otvorených štandardoch integrácie (MQTT, OPC UA),

- postavená na databázach, ktoré netvoria dátové silo a umožňujú budúce rozšírenie alebo migráciu.



Kombináciou IoT middleware a výkonných časových databáz dosiahneme vysokú aktuálnosť dát aj efektívnu archiváciu. Táto kombinácia umožní analytikom aj operátorom získať z dát maximum – od okamžitých zásahov pri odchýlkach až po dlhodobé strategické poznatky z veľkých dát [8][37].



---



### Poznámka na záver



Pri nasadzovaní takéhoto systému je vhodné:

- začať pilotným projektom na menšej časti (napr. jedna linka, obmedzený počet senzorov),

- doladiť detaily integrácie a výkonnosti,

- zabezpečiť školenie personálu, najmä v oblasti práce s analytickými nástrojmi a interpretácie výstupov digitálneho dvojčaťa.



Zapojenie kľúčových stakeholderov od začiatku a ich porozumenie prínosom technológie výrazne zvyšuje šancu na úspešnú adopciu, ktorá je často rovnako dôležitá ako samotné technické riešenie [44].



