## 1. Charakteristika a požiadavky senzorických dát



- Senzorické dáta sú generované v reálnom čase stovkami až tisíckami čidiel:

&nbsp; - teplota,

&nbsp; - vlhkosť,

&nbsp; - tlak,

&nbsp; - vibrácie,

&nbsp; - výkon strojov,

&nbsp; - spotreba energie.

- Každé meranie obsahuje časovú pečiatku → vznikajú rozsiahle časové rady (*time-series*).

- Digitálne dvojča potrebuje okamžité dáta pre vizualizáciu a simuláciu → nízka latencia je kritická.

- Z dlhodobého hľadiska je potrebné uchovávať históriu pre:

&nbsp; - analýzy,

&nbsp; - reporting,

&nbsp; - audit,

&nbsp; - prediktívnu údržbu.

- Architektúra musí oddeliť:

&nbsp; - rýchle dáta (*hot*),

&nbsp; - dlhodobú archiváciu (*cold*).



---



## 2. Typy senzorov a dopad na výber technológie



- **Analógové senzory** (0–10 V, 4–20 mA, odpor, termočlánky):

&nbsp; - vyžadujú PLC s ADC,

&nbsp; - generujú stabilný, častý stream,

&nbsp; - vhodné pre TSDB.

- **Digitálne senzory** (RS-485/Modbus, I²C, SPI, MQTT):

&nbsp; - poskytujú vyššiu granularitu a kontext,

&nbsp; - ideálne pre streamingové spracovanie.

- **IoT senzory cez MQTT/WiFi**:

&nbsp; - menšia periodicita,

&nbsp; - vhodné pre broker + TSDB kombináciu.

- Rozličné rozhrania znamenajú potrebu unifikačnej vrstvy:

&nbsp; - OPC UA,

&nbsp; - alebo IoT gateway.



---



## 3. Translation Layer – preklad OPC UA dát do jednotného formátu



- Pred odoslaním do middleware je potrebné dáta z OPC UA normalizovať:

&nbsp; - vybrať iba relevantné tagy,

&nbsp; - doplniť timestamp,

&nbsp; - kvalitu,

&nbsp; - previesť ich do jednotného formátu (najčastejšie JSON).

- Na tento účel sú vhodné edge gateway riešenia:

&nbsp; - Hopit Edge IIoT Gateway,

&nbsp; - EMQX Neuron,

&nbsp; ktoré umožňujú:

&nbsp; - mapovanie OPC UA uzlov,

&nbsp; - transformáciu dát,

&nbsp; - nízkolatenčné doručovanie do MQTT / stream vrstvy.

- Tieto gatewaye výrazne zjednodušujú integráciu, keďže digitálne dvojča pracuje už len s:

&nbsp; - čistým,

&nbsp; - homogenizovaným dátovým modelom,

&nbsp; nie so surovou OPC UA hierarchiou.

- Ako flexibilná alternatíva pre vývoj môže slúžiť:

&nbsp; - Node-RED (s OPC UA pluginmi),

&nbsp; no pre dlhodobú prevádzku sú robustnejšie dedikované edge gatewaye.



---



## 4. Middleware – čo je vhodné pre jednotlivé scenáre



- **MQTT broker**:

&nbsp; - pre jednoduché IoT zariadenia,

&nbsp; - nízke nároky na priepustnosť,

&nbsp; - jednoduché škálovanie,

&nbsp; - vhodné pre výrobnú linku s menšími dátovými tokmi.

- **Apache Kafka**:

&nbsp; - pre veľké objemy dát,

&nbsp; - viacero konzumentov naraz,

&nbsp; - replay,

&nbsp; - vysoká odolnosť,

&nbsp; - vhodné pre závody s tisíckami senzorov a komplexnými dátovými tokmi.

- **OPC UA**:

&nbsp; - vhodné tam, kde existuje PLC infraštruktúra,

&nbsp; - potreba jednotného API pre čítanie údajov.

- **Cloud IoT platformy (Azure / AWS)**:

&nbsp; - ideálne pri centralizovanom cloudovom digitálnom dvojčati,

&nbsp; - vyžadujú stabilné internetové pripojenie.



---



## 5. Porovnanie databáz – ktorý systém je vhodný pre ktorý use case



- **Relačné databázy** (PostgreSQL, MySQL):

&nbsp; - silné pri metaúdajoch,

&nbsp; - relačných väzbách,

&nbsp; - výrobnej logistike,

&nbsp; - nevhodné pre vysokofrekvenčný ingest.

- **Time-Series databázy** (InfluxDB, TimescaleDB):

&nbsp; - optimalizované na rýchly zápis,

&nbsp; - dotazy v časových rozsahoch,

&nbsp; - ideálne pre senzorické dáta.

- **InfluxDB**:

&nbsp; - extrémne rýchly ingest,

&nbsp; - jednoduchá správa retention polícií,

&nbsp; - vhodné pre výrobný závod s veľkým množstvom jednorozmerných meraní.

- **TimescaleDB**:

&nbsp; - výhoda SQL,

&nbsp; - optimalizácie pre časové rady,

&nbsp; - vhodné pri kombinácii s výrobou, údržbou a ďalšími tabuľkami.

- **Apache Cassandra**:

&nbsp; - pre masívne objemy (miliardy záznamov mesačne),

&nbsp; - geograficky distribuované závody,

&nbsp; - príliš robustné pre menší enterprise závod.

- **MongoDB**:

&nbsp; - vhodné pre heterogénne JSON dáta,

&nbsp; - nie ideálne pre primárny senzorický stream.



---



## 6. Odporúčaný prístup pre náš use case  

### (digitálne dvojča spracovne zemiakov)



- Senzory budú generovať kontinuálne dáta:

&nbsp; - teplota,

&nbsp; - vlhkosť,

&nbsp; - tlak,

&nbsp; - hmotnosť  

&nbsp; → primárny dátový typ je *time-series*.

- Počet senzorov je pravdepodobne stredný (desiatky až stovky):

&nbsp; - Kafka nie je nevyhnutná,

&nbsp; - vhodný je MQTT + prípadne OPC UA.

- Neexistuje potreba globálnej distribúcie:

&nbsp; - Cassandra by bola zbytočne komplexná.

- Potrebná je reálna vizualizácia stavu linky:

&nbsp; - TSDB s rýchlym prístupom k posledným hodnotám je ideálna.

- Zároveň je potrebné ukladať informácie o:

&nbsp; - zariadeniach,

&nbsp; - linkách,

&nbsp; - šaržiach  

&nbsp; → patrí do relačnej DB (PostgreSQL).

- Najvhodnejšia architektúra:

&nbsp; - MQTT / OPC UA → Stream → InfluxDB (senzorické dáta) + PostgreSQL (riadiace / organizačné dáta).



---



## 7. Reálny čas vs. archivácia – čo je pre náš projekt kľúčové



- Digitálne dvojča musí aktualizovať vizualizáciu v reálnom čase:

&nbsp; - TSDB s nízkou latenciou.

- Dlhodobé dáta slúžia na optimalizáciu výroby:

&nbsp; - napr. teplota sušenia vs. kvalita produktu.

- Retention policies:

&nbsp; - sekundové dáta: 6 mesiacov,

&nbsp; - agregované dáta: napr. 5 rokov.

- Downsampling:

&nbsp; - hodinové / denné priemery,

&nbsp; - šetrí disk bez straty trendov.

- Pri prediktívnej údržbe je kvalitná historická databáza nevyhnutná.



---



## 8. Finálne odporúčanie architektúry pre fabriku na spracovanie zemiakov



- **Unifikačná vrstva**:

&nbsp; - OPC UA (ak sú PLC),

&nbsp; - inak MQTT IoT gateway.

- **Middleware**:

&nbsp; - MQTT broker pre zber telemetrie,

&nbsp; - možnosť rozšírenia o Kafka pri raste závodu.

- **Primárna databáza**:

&nbsp; - InfluxDB pre všetky senzorické dáta

&nbsp; - rýchly zápis,

&nbsp; - retention,

&nbsp; - downsampling.

- **Sekundárna databáza**:

&nbsp; - PostgreSQL pre metaúdaje:

&nbsp;   - stroje,

&nbsp;   - linky,

&nbsp;   - šarže,

&nbsp;   - parametre.

- **Hot path**:

&nbsp; - posledné dáta pre digitálne dvojča

&nbsp; - vizualizácia,

&nbsp; - alarmy.

- **Cold path**:

&nbsp; - export historických dát do lacného uloženia:

&nbsp;   - Parquet,

&nbsp;   - S3 / Blob,

&nbsp; - pre analýzy a ML.

- Tento model:

&nbsp; - minimalizuje náklady,

&nbsp; - zjednodušuje správu,

&nbsp; - poskytuje vysokú spoľahlivosť.



---



## 9. Možné riešenia HMI / GUI



### Grafana + Prometheus / Influx / Timescale



- dashboardy,

- alerty,

- monitoring,

- vhodné pre KPI a historické trendy,

- možnosť doplniť vlastné panely.



### Vlastné riešenie (Python + PyQt5)



- nízka latencia pre realtime HMI,

- interaktívna schéma,

- trendy,

- alarmy,

- vizuálne upozornenia,

- flexibilita deploymentu.

- Komunikácia:

&nbsp; - MQTT,

&nbsp; - WebSocket,

&nbsp; - REST.

- Možnosť:

&nbsp; - fallback bufferu,

&nbsp; - zmeny správania pri poruche.

- Integrácie:

&nbsp; - webhooks,

&nbsp; - embedding externých prvkov.



### Kombinovaný prístup



- realtime HMI cez vlastné middleware riešenie,

- KPI a alerty cez Grafanu.

- Pri geospatial dátach:

&nbsp; - možnosť použiť KeplerGL

&nbsp; - integrovaný do frontendu alebo ako embedded panel.



