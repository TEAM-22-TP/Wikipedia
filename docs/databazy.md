\## Charakteristika a požiadavky senzorických dát



Digitálne dvojča výrobnej prevádzky (v našom prípade továrne na spracovanie zemiakov) vyžaduje nepretržité získavanie, spracovanie a uchovávanie veľkého množstva senzorických dát v reálnom čase \[1]. Tieto dáta zahŕňajú rôzne fyzikálne veličiny – predpokladané sú senzory teploty, vlhkosti, hmotnosti (váhy), tlaku, prípadne vibrácií či spotreby energie a ďalších parametrov prostredia a strojov \[2]. Každé meranie je spravidla označené časovou pečiatkou, takže vzniká časová postupnosť údajov (tzv. \*time series\* dáta). V prostredí IIoT (Priemyselného internetu vecí) generujú stovky až tisíce senzorov obrovské objemy dát kontinuálne \[1].



Systém pre digitálne dvojča preto musí zvládnuť vysokú frekvenciu zápisu (pri mnohočetných senzoroch môžu pribúdať nové hodnoty každú sekundu alebo častejšie) a veľký objem údajov z dlhodobého hľadiska.



Okrem čisto dátového objemu je kľúčová aj rýchlosť spracovania a dostupnosť v reálnom čase. Digitálne dvojča musí takmer okamžite reflektovať zmeny v reálnej továrni, aby umožnilo monitorovanie a prípadne zásahy do procesu. Zároveň je potrebné údaje archivovať na neskoršiu analýzu – či už na historické vyhodnocovanie výkonnosti, hľadanie trendov, alebo tréning modelov pre prediktívnu údržbu.



Teda systém musí podporovať dvojitý účel:



\- on-line analýzu v reálnom čase

\- dlhodobú archiváciu dát \[3]



To typicky znamená zavedenie tzv.:



\- \*\*hot path\*\* – pre najnovšie dáta (okamžité spracovanie a ukladanie aktuálnych údajov pre rýchle dotazy)

\- \*\*cold path\*\* – pre dlhodobé uloženie väčších objemov dát na lacnejšom úložisku \[4]



Výzvou v tomto projekte je neznalosť konkrétnych senzorov a ich rozhraní v danej fabrike – nie je jasné, aké typy senzorov budú použité ani akým spôsobom budú komunikovať (či už pôjde o jednotné digitálne rozhranie, alebo len analógové výstupy). Analýza preto musí pokryť rôzne scenáre integrácie senzorov a zberu dát.



V nasledujúcich častiach preto rozoberáme:



\- typy senzorov a ich rozhrania,

\- vhodné middleware pre zber a prenos senzorických údajov,

\- databázové systémy vhodné na ukladanie senzorických dát,

\- porovnanie relačných databáz, časových databáz a NoSQL prístupov,

\- stratégiu real-time spracovania vs. archivácie údajov.



---



\## Typy senzorov a ich rozhrania v priemyselnej továrni



V továrni môžeme očakávať širokú škálu senzorov – od teplotných čidiel v tepelných procesoch, cez váhové senzory na meranie hmotnosti surovín či produktov, vlhkostné senzory (napr. pri sušení zemiakov alebo kontrole skladovania), tlakové senzory (v parných zariadeniach alebo hydraulike), až po snímače vibrácií v motoroch a pod. \[2].



Každý typ senzora môže mať iný spôsob výstupu údajov a rozhrania.



\### Analógové senzory



Analógové senzory poskytujú spojitý elektrický signál – typicky:



\- napäťový (napr. 0–10 V)

\- prúdový (napr. 4–20 mA)



Signál je úmerný meranej hodnote. Príkladmi sú:



\- termočlánky (milivoltový signál úmerný teplote),

\- odporové teplomery (mení sa odpor),

\- tlakové senzory s analógovou membránou,

\- tenzometre v mostíku pre meranie hmotnosti atď. \[5]\[6].



Takéto senzory nemajú digitálne rozhranie, a preto vyžadujú prevod analógového signálu na digitálne dáta. V priemysle sa na to často používajú:



\- PLC jednotky s analógovými vstupnými modulmi,

\- samostatné ADC moduly.



Každý analógový vstup PLC obsahuje ADC prevodník, ktorý kontinuálny elektrický signál konvertuje na digitálne číslo spracovateľné počítačom \[7].



Inými slovami, každý analógový senzor musí byť pripojený cez príslušnú vstupnú kartu (modul) PLC alebo iného zberného zariadenia, ktorá zabezpečí digitalizáciu analógového signálu \[7].



Moderné PLC systémy podporujú oba typy senzorov – analógové aj digitálne, no:



\- pre analógové potrebujú špeciálne analógové vstupné karty (s ADC),

\- digitálne senzory sa pripájajú na bežné digitálne vstupy \[8].



\### Digitálne senzory



Digitálne senzory poskytujú výstup už v digitálnej forme:



\- digitálny signál (binárny stav alebo pulzy),

\- alebo komunikujú cez komunikačný protokol.



Príklady:



\- snímače prítomnosti – digitálny (on/off) signál alebo impulzy,

\- enkodéry – pulzy,

\- inteligentné senzory – integrovaný mikrokontrolér.



Používané rozhrania:



\- I²C

\- SPI

\- RS-485

\- USB

\- Ethernet



V priemyselnom prostredí sú bežné aj štandardy ako:



\- Modbus (RS-485),

\- Profibus,

\- DeviceNet,

\- Ethernet/IP,

\- ďalšie poľné zbernice pre PLC systémy \[11].



Mnohé moderné senzory podporujú priame pripojenie do siete:



\- Ethernet

\- Wi-Fi

\- protokoly MQTT alebo HTTP (IoT zariadenia)



Bezdrôtové senzory môžu používať:



\- Zigbee

\- Bluetooth LE

\- IO-Link Wireless a podobne.



Pre náš projekt je podstatné, že rôzne senzory môžu mať rozličné rozhrania a komunikačné štandardy. Ak továreň nemá jednotný systém, integrácia takýchto senzorov si vyžiada konverziu a zjednotenie dátového rozhrania.



Príklady integračných požiadaviek:



\- analógové čidlo → PLC alebo IoT brána,

\- RS-485 / Modbus → komunikačný modul alebo gateway,

\- MQTT senzory → MQTT broker.



Rozdielne signály a komunikačné štandardy medzi senzormi si vyžadujú dodatočné vybavenie na prevod, čo zvyšuje zložitosť systému \[12].



Preto, ak je to možné, je výhodné vybrať senzory aj podľa rozhrania, aby sa minimalizoval počet potrebných prevodníkov a údaje sa zbierali jednotným spôsobom \[12].



---



\## OPC UA ako zjednocujúca vrstva



V ideálnom prípade by továreň mala unifikovanú vrstvu pre zber dát zo senzorov. V priemysle 4.0 sa ako štandardné riešenie často používa protokol \*\*OPC UA (OLE for Process Control Unified Architecture)\*\*.



OPC UA môže fungovať ako nadstavba nad rôznymi zariadeniami a senzormi – napríklad PLC všetkých výrobcov – a poskytuje jednotné dátové rozhranie pre vyššie vrstvy systémov.



OPC UA server dokáže:



\- agregovať dáta z rôznych senzorov,

\- prezentovať ich v jednotnej štruktúrovanej podobe,

\- umožniť konfiguráciu a monitorovanie \[13].



Podľa štúdií nasadenia OPC UA v senzorových sieťach táto technológia:



\- zjednodušuje integráciu,

\- zlepšuje škálovateľnosť,

\- otvára možnosti integrovaného riadenia kvality,

\- umožňuje vytvorenie digitálneho dvojčaťa \[13].



Ak by teda vo fabrike existovala OPC UA vrstva (v PLC systéme alebo cez externý gateway), digitálne dvojča by mohlo získavať údaje štandardizovaným spôsobom.



V opačnom prípade bude potrebné vyvinúť vlastnú integračnú vrstvu, ktorá zahrnie:



\- modul na čítanie analógových vstupov,

\- modul pre Modbus / RS-485,

\- modul pre MQTT a iné IoT protokoly.



Táto vrstva môže bežať na priemyselnom počítači alebo IoT gateway priamo v továrni a zabezpečí zosúladenie dát do spoločného formátu (napr. JSON s poľami čas, typ senzora, hodnota).



---



\## Translation Layer



Na efektívny prenos dát z výrobného prostredia do informačných systémov je potrebná \*\*Translation Layer\*\*, ktorá:



\- spracúva surové OPC UA dáta,

\- transformuje ich do jednotného interného formátu.



Táto vrstva stojí medzi:



\- OPC UA serverom

\- middleware (napr. MQTT alebo RabbitMQ)



a rieši:



\- normalizáciu dát,

\- filtrovanie,

\- preklad do prenosového formátu.



OPC UA poskytuje bohatý dátový model, no nie je priamo vhodný na rýchlu distribúciu do GUI, databáz alebo analytických komponentov. Translation Layer rieši:



\- výber relevantných parametrov,

\- mapovanie na interný dátový model,

\- doplnenie metaúdajov (timestamp, ID linky, kvalita),

\- konverziu do JSON,

\- odoslanie do message brokeru.



\### Hopit Edge IIoT Gateway



Hopit Edge je moderná edge gateway umožňujúca:



\- OPC UA → JSON transformáciu,

\- filtráciu signálov,

\- obohatenie o kontext,

\- odoslanie do MQTT, REST API alebo databáz.



Je vhodná pre edge nasadenia a ponúka grafické rozhranie.



\### EMQX Neuron



EMQX Neuron je priemyselná IoT edge brána, ktorá:



\- podporuje OPC UA, Modbus, Siemens S7,

\- umožňuje mapovanie a konverziu dát,

\- produkuje JSON telemetry,

\- je vhodná na 24/7 priemyselnú prevádzku.



\### Node-RED (s OPC UA doplnkami)



Node-RED môže plniť úlohu Translation Layer pomocou OPC UA doplnkov. Umožňuje:



\- čítanie dát z OPC UA servera,

\- spracovanie dát pomocou funkčných blokov,

\- transformáciu dát do JSON formátu,

\- odosielanie dát cez MQTT alebo HTTP.



Výhodou Node-RED je vysoká flexibilita a rýchle prototypovanie riešení. Nevýhodou je nižšia vhodnosť pre kritickú priemyselnú prevádzku, keďže ide primárne o nástroj určený na integračné a experimentálne scenáre.



---



\## Middleware pre zber a prenos senzorických dát



Middleware predstavuje sprostredkovateľskú vrstvu medzi zdrojmi dát (senzormi, PLC, OPC UA servermi) a cieľovými systémami digitálneho dvojčaťa. Jeho úlohou je zabezpečiť spoľahlivý, škálovateľný a asynchrónny prenos dát medzi jednotlivými komponentmi systému.



Pri veľkom počte senzorov a vysokej frekvencii meraní nie je vhodné zapisovať dáta priamo do databázy, ale použiť middleware, ktorý poskytuje:



\- oddelenie producentov a konzumentov dát,

\- bufferovanie pri výpadkoch,

\- horizontálnu škálovateľnosť,

\- možnosť paralelného spracovania dát.



\### Typy middleware riešení



\#### Message brokery a event streaming



Najčastejšie používaným prístupom je využitie message brokerov alebo event streaming platforiem:



\- \*\*MQTT\*\*

&nbsp; - ľahký publish/subscribe protokol,

&nbsp; - vhodný pre IoT a edge zariadenia,

&nbsp; - nízka režijnosť,

&nbsp; - typické implementácie: Mosquitto, HiveMQ.



\- \*\*Apache Kafka\*\*

&nbsp; - distribuovaná event streaming platforma,

&nbsp; - vysoká priepustnosť,

&nbsp; - persistencia správ,

&nbsp; - vhodná pre veľké objemy dát a analytické spracovanie.



Tieto systémy umožňujú, aby senzory alebo Translation Layer publikovali dáta do tém (topics), zatiaľ čo databázy, analytické moduly a vizualizačné nástroje sa k týmto dátam pripájajú ako konzumenti.



\#### IoT platformy



Niektoré riešenia využívajú komplexné IoT platformy, ktoré integrujú zber dát, správu zariadení a ukladanie dát:



\- Azure IoT Hub,

\- AWS IoT Core.



Tieto platformy poskytujú:

\- bezpečný príjem dát zo zariadení,

\- routing dát do rôznych úložísk,

\- integráciu s analytickými službami.



Nevýhodou môže byť závislosť na cloudovom prostredí a vyššie náklady.



\#### OPC UA middleware



OPC UA podporuje nielen client/server model, ale aj \*\*publish/subscribe\*\* model, ktorý umožňuje streamovanie dát. Tento prístup môže slúžiť ako middleware najmä v prostredí, kde je OPC UA už dominantnou technológiou.



---



\## Databázové systémy pre senzorické dáta



Ukladanie senzorických dát predstavuje jednu z najväčších výziev digitálneho dvojčaťa. Databázový systém musí zvládnuť:



\- vysokú rýchlosť zápisu,

\- veľké objemy dát,

\- časové dotazy,

\- dlhodobú archiváciu.



\### Relačné databázy (SQL)



Relačné databázy, ako napríklad PostgreSQL alebo MySQL, sú tradične používané v priemyselných informačných systémoch.



\*\*Výhody:\*\*

\- silné záruky dátovej integrity (ACID),

\- komplexné SQL dotazy,

\- jednoduché prepojenie s ďalšími tabuľkami (napr. výroba, údržba).



\*\*Nevýhody:\*\*

\- nižší výkon pri veľmi vysokofrekvenčnom zápise dát,

\- potreba pevnej schémy,

\- zložitejšia práca s dlhodobými časovými radmi.



\### Časové databázy (Time-Series Databases)



Časové databázy sú špeciálne optimalizované pre ukladanie a spracovanie časových radov.



Príklady:

\- InfluxDB,

\- TimescaleDB,

\- Prometheus.



\*\*Výhody:\*\*

\- optimalizované pre rýchly zápis,

\- efektívna kompresia dát,

\- podpora retenčných politík,

\- downsampling dát.



\*\*Nevýhody:\*\*

\- obmedzené relačné možnosti,

\- špecifický ekosystém nástrojov,

\- v niektorých prípadoch obmedzená škálovateľnosť v open-source verziách.



\### NoSQL databázy a dátové úložiská



Ďalšou možnosťou sú NoSQL databázy alebo dátové jazerá:



\- \*\*Cassandra\*\*

&nbsp; - vysoká škálovateľnosť,

&nbsp; - vhodná pre masívne objemy dát.



\- \*\*MongoDB\*\*

&nbsp; - flexibilná schéma,

&nbsp; - menej vhodná pre extrémne vysokofrekvenčné časové rady.



\- \*\*Data lakes\*\*

&nbsp; - archivácia historických dát,

&nbsp; - ukladanie vo formátoch CSV, Parquet alebo ORC,

&nbsp; - vhodné pre neskoršiu analytiku a strojové učenie.



---



\## Analýza v reálnom čase vs. archivácia dát



Digitálne dvojča musí podporovať spracovanie dát v reálnom čase aj ich dlhodobé ukladanie.



\### On-line spracovanie dát



On-line spracovanie zahŕňa:



\- streamové spracovanie dát (Apache Spark, Apache Flink),

\- rýchle dotazy nad aktuálnymi dátami,

\- in-memory caching (napr. Redis),

\- real-time vizualizáciu a notifikácie.



\### Dlhodobá archivácia dát



Archivácia dát zahŕňa:



\- definovanie retenčných období,

\- automatické mazanie alebo presun starých dát,

\- downsampling časových radov,

\- rozdelenie úložísk na hot, warm a cold storage,

\- využitie historických dát pre analytiku a strojové učenie.



\### Kombinovaný prístup



V praxi sa často používa kombinovaný prístup, kde dáta tečú paralelne:



\- do časovej databázy pre rýchle dotazy (hot path),

\- do dlhodobého úložiska pre archiváciu (cold path).



Súčasťou riešenia musia byť aj mechanizmy pre:



\- zálohovanie dát,

\- zabezpečenie dát,

\- riadenie prístupových práv.





