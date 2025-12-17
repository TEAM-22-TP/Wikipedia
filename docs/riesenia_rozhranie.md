Pre vizualizáciu digital twin existujú možné riešenia, každé s istými výhodami a nevýhodami.



### Grafana



- je známa a má bohatý ekosystém,

- má množstvo pluginov,

- podporuje alerty,

- interaguje s Prometheus a Influx.



Nevýhody:

- neposkytuje natívny twin renderer,

- pravdepodobne by bolo potrebné vyvinúť vlastné panely a ovládanie,

- realtime latencia nemusí byť pod 100 ms.



### Node-RED Dashboard



- jednoduchý na použitie,

- rýchlo nasaditeľný.



Nevýhody:

- softvér je EOL (end-of-life).



### Node-RED Contrib



- umožňuje no-code a low-code techniky,

- je agnostický na frontend framework  

&nbsp; - Vue  

&nbsp; - React  

&nbsp; - Svelte



Nevýhody:

- je potrebná práca na vyvinutie frontendu.



### Ignition Perspective



- poskytuje SCADA-grade HMI.



Nevýhody:

- ide o platený produkt.



### ThingsBoard



- podporuje natívny ingest:

&nbsp; - MQTT

&nbsp; - HTTP

&nbsp; - CoAP

- alarmy,

- dashboardy,

- device management.



Nevýhody:

- nastavenie je komplexné,

- softvér je skôr zameraný na IoT než na SCADA/HMI úroveň.



### Elastic



- dobrá podpora pre dashboardy,

- alerty.



Nevýhody:

- enterprise licencovanie nie je vhodné pre náš projekt,

- neposkytuje 3D renderer.



### Metabase



- podporuje visual query builder,

- umožňuje rýchle reporty.



Nevýhody:

- nie je realtime,

- má obmedzené možnosti analytiky.



### Tableau



- podporuje vizualizáciu,

- pokročilé dashboardy.



Nevýhody:

- licencia je nákladná.



### KeplerGL



- 3D vizualizácia v prehliadači,

- geospatial-oriented.



Nevýhody:

- potrebuje integráciu s frontendom,

- nie je 3D engine pre strojársky digital twin.



### Prometheus



- dobrá integrácia so softvérom Grafana,

- určený pre timeseries monitoring.



Nevýhody:

- pull-model,

- nie je praktickejší než OPC UA → TSDB.



---



Možnosťou pre 2D dashboard je aj vlastné riešenie cez viacúčelový desktop cockpit pre výrobu s nízkou latenciou pre realtime vizualizáciu stavu linky. Daný frontend/middleware by mohol byť postavený na technológiách **Python** a **PyQt5** a pomocou Python knižníc.



Tento cockpit je možné poskladať modulárne a priebežne ho dopĺňať o hotové nástroje. Architektúra Python + Qt5 umožňuje:

- spúšťať softvér na akomkoľvek operačnom systéme,

- interagovať s ním pomocou rôznych metód vstupu.



Komunikácia by následne mohla prebiehať cez:

- MQTT klient,

- WebSockety,

- REST rozhranie (FastAPI).



Kľúčovými modulmi možného vlastného GUI sú:

- živé HMI panely s interaktívnou schémou,

- možnosti v stavovej lište,

- vlastné rozloženie schémy (štýlom drag-and-drop),

- trendy a grafy s kurzorom a legendou,

- alarmy a označovanie udalostí,

- prípadné zvukové a vizuálne upozornenia.



Systém by mohol cez webhooks interagovať s inými relevantnými systémami.



Táto cesta otvára možnosť:

- čiastočne implementovať vlastné GUI,

- spoliehať sa na integrácie s existujúcimi nástrojmi:

&nbsp; - Kibana,

&nbsp; - Grafana.



Výhodou daného systému je:

- nízka latencia,

- možnosť offline bufferu v prípade zlyhania spojenia,

- eskalácia pri poruche,



čo ostatné nástroje nemusia podporovať.



