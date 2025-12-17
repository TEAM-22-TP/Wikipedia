Základ digitálneho dvojčaťa tvoria najmä:

\- časové rady telemetrie,

\- udalosti a stavové prechody,

\- väzby na výrobný tok (objednávka → šarža/lot → krok stanice → kus),

\- údaje o kvalite a metrológii,

\- údaje o údržbe,

\- energetické a environmentálne údaje.



Odporúča sa používať jednotný \*\*kanonický model záznamu\*\* s políčkami:

\- čas v UTC (`ts`, ISO-8601),

\- stabilné identifikátory (`site\_id`, `line\_id`, `asset\_id`),

\- názov signálu (`signal` alebo PLC tag),

\- hodnota (`value`) s jednotkou (`unit`),

\- kvalita merania (`quality`),

\- podľa potreby väzby na výrobu (`batch\_id`, `order\_id`).



Praktické je aj monotónne rastúce číslo `seq` na deduplikáciu.



Telemetria sa obvykle prenáša a ukladá po jednotlivých vzorkách; vhodný formát pre prenos aj „landing“ úložisko je \*\*JSON\*\*:



{

&nbsp; "ts":"2025-10-27T06:15:03.412Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1",

&nbsp; "asset\_id":"Mix-01",

&nbsp; "signal":"temp\_jacket",

&nbsp; "value":72.4,

&nbsp; "unit":"°C",

&nbsp; "quality":"GOOD",

&nbsp; "source":"opcua-gw-1",

&nbsp; "batch\_id":"B2025-10-27-001",

&nbsp; "seq":1456721

}



{

&nbsp; "ts":"2025-10-27T06:15:03.512Z",

&nbsp; "site\_id":"SK-LM",

&nbsp; "line\_id":"L1",

&nbsp; "asset\_id":"Motor-A",

&nbsp; "signal":"current\_rms",

&nbsp; "value":8.93,

&nbsp; "unit":"A",

&nbsp; "quality":"GOOD",

&nbsp; "source":"opcua-gw-1",

&nbsp; "seq":1456722

}

{

&nbsp; "ts":"2025-10-27T06:15:03.620Z",

&nbsp; "site\_id":"SK-LM",

&nbsp; "line\_id":"L1",

&nbsp; "asset\_id":"Conv-02",

&nbsp; "signal":"speed",

&nbsp; "value":0.85,

&nbsp; "unit":"m/s",

&nbsp; "quality":"GOOD",

&nbsp; "source":"plc-1200",

&nbsp; "seq":1456723

}



Okrem plynulého merania je nevyhnutná aj evidencia udalostí a stavových prechodov (napr. spustenie/zastavenie, prechod do poruchy, zmena receptúry, alarm). Každý záznam by mal obsahovať typ udalosti, závažnosť a prípadné kódy alarmov, čo následne umožňuje analýzu príčin prestojov, výpočet MTBF/MTTR a audit.



{

&nbsp; "ts":"2025-10-27T06:20:10.005Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1","asset\_id":"Mix-01",

&nbsp; "event\_type":"STATE\_CHANGED",

&nbsp; "from\_state":"RUN","to\_state":"FAULT",

&nbsp; "severity":"HIGH",

&nbsp; "message":"Overtemperature alarm",

&nbsp; "alarm\_code":"ALM-MIX-OT-001",

&nbsp; "batch\_id":"B2025-10-27-001"

}



Pri receptúrach je vhodné zaznamenávať uplatnené nastavenia (setpointy), aby sa dalo porovnávať „požadované verzus dosiahnuté“ a spätne to korelovať s kvalitou výstupu:



{

&nbsp; "ts":"2025-10-27T06:00:00.000Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1","asset\_id":"Mix-01",

&nbsp; "event\_type":"RECIPE\_APPLIED",

&nbsp; "recipe\_id":"REC-CHIPS-07",

&nbsp; "setpoints":{"temp\_jacket":70.0,"rpm":120,"time\_s":180},

&nbsp; "operator\_id":"op\_034"

}



Prepojenie na výrobný tok podľa ISA-95/88 (objednávka → šarža/lot → krok stanice → kus) je zásadné. Umožňuje prepájať procesné údaje s plánovaním a s vysledovateľnosťou (traceabilitou). Príklad objednávky rozdelenej na šarže:



{

&nbsp; "order\_id":"SO-2025-10-27-15",

&nbsp; "product\_id":"POTATO-CHIPS-45g",

&nbsp; "planned\_start":"2025-10-27T05:50:00Z",

&nbsp; "planned\_qty":120000,

&nbsp; "unit":"pcs",

&nbsp; "batches":\[

&nbsp;   {"batch\_id":"B2025-10-27-001","qty":60000},

&nbsp;   {"batch\_id":"B2025-10-27-002","qty":60000}

&nbsp; ]

}



Ak sa vyžaduje kusová vysledovateľnosť, zaznamenáva sa prechod konkrétneho kusu cez stanicu, jeho výsledok a väzby na šaržu/lot:



{

&nbsp; "ts":"2025-10-27T06:22:41.200Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1","asset\_id":"Pack-03",

&nbsp; "serial":"SN-45G-0005678123",

&nbsp; "lot\_id":"LOT-2025-3001",

&nbsp; "batch\_id":"B2025-10-27-001",

&nbsp; "station\_step":"PACKING",

&nbsp; "result":"PASS"

}



Kvalita a metrológia (SPC) si vyžadujú uchovávať jednotlivé merania, špecifikácie (LSL/USL/target) a výsledok vyhodnotenia, čo umožní zostavovať riadiace diagramy a počítať ukazovatele (Cp/Cpk), ako aj spätne korelovať kvalitu s parametrami procesu:



{

&nbsp; "ts":"2025-10-27T06:25:30.000Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1","asset\_id":"Scale-01",

&nbsp; "qc\_check":"NET\_WEIGHT",

&nbsp; "sample\_no":132,

&nbsp; "value":45.3,

&nbsp; "unit":"g",

&nbsp; "spec":{"lsl":44.0,"usl":46.0,"target":45.0},

&nbsp; "result":"PASS",

&nbsp; "batch\_id":"B2025-10-27-001"

}



Rovnako dôležité je systematicky evidovať údržbu – nielen výskyt poruchy, ale aj vykonané zásahy, trvanie prestoja a parametre MTTR/MTBF. Tieto údaje sú kľúčové pre prediktívnu údržbu aj pre dôveryhodný výpočet OEE.



{

&nbsp; "ts":"2025-10-27T06:21:00.000Z",

&nbsp; "site\_id":"SK-LM","line\_id":"L1","asset\_id":"Mix-01",

&nbsp; "maintenance":{"type":"CORRECTIVE","work\_order":"WO-987654"},

&nbsp; "failure\_code":"BEARING\_OVERHEAT",

&nbsp; "downtime\_s":420,

&nbsp; "mttr\_s":360,

&nbsp; "technician\_id":"tech\_011"

}



Digitálne dvojča dotvárajú energetické a environmentálne údaje, najmä spotreba elektriny, stlačeného vzduchu a plynu, ako aj podmienky okolia. Ich integrácia umožňuje optimalizovať náklady a lepšie interpretovať odchýlky v kvalite či výkone.



{

&nbsp; "ts":"2025-10-27T06:15:05Z",

&nbsp; "site\_id":"SK-LM",

&nbsp; "line\_id":"L1",

&nbsp; "asset\_id":"MainMeter",

&nbsp; "signal":"energy\_kwh",

&nbsp; "value":0.36,

&nbsp; "unit":"kWh",

&nbsp; "quality":"GOOD",

&nbsp; "source":"ems-gw",

&nbsp; "seq":200145

}

{

&nbsp; "ts":"2025-10-27T06:15:05Z",

&nbsp; "site\_id":"SK-LM",

&nbsp; "line\_id":"L1",

&nbsp; "asset\_id":"Ambient",

&nbsp; "signal":"temp",

&nbsp; "value":23.1,

&nbsp; "unit":"°C",

&nbsp; "quality":"GOOD",

&nbsp; "source":"env-sensor-2",

&nbsp; "seq":200146

}



