\## Úloha vizualizácie v digitálnom dvojčati



Digitálne dvojča výrobnej linky je v princípe softvérový model, ktorý v reálnom čase odráža stav fyzickej linky na základe senzorických dát a udalostí. Aby však malo praktický prínos pre operátorov, údržbu a manažment, kľúčová je vrstva vizualizácie a používateľského rozhrania (UI). Tá sprostredkuje komplexné časové rady, stavové prechody, alarmy a simulačné výsledky do podoby, ktorej človek rýchlo rozumie a vie podľa nej konať.



V moderných implementáciách sa používa kombinácia:

\- klasických \*\*2D dashboardov\*\* (trendové grafy, tabuľky, KPI),

\- \*\*3D digitálnych modelov\*\* zariadení či celých fabrík,



často postavených na herných engine-och (Unity, Unreal Engine) alebo špecializovaných 3D vizualizačných platformách. :contentReference\[oaicite:0]{index=0}



Praktická skúsenosť z priemyslu ukazuje, že digitálne dvojča nie je „iba model“, ale aj \*\*interaktívny kokpit pre rozhodovanie\*\*. Umožňuje:

\- monitorovať aktuálny stav,

\- analyzovať historické trendy,

\- testovať hypotetické scenáre (\*what-if\*),

\- trénovať personál v bezpečnom virtuálnom prostredí.



Výrobcovia typu Siemens, Rockwell či špecializované firmy na digital twin riešenia zdôrazňujú, že vizualizácia pokrýva celý životný cyklus – od návrhu linky, cez virtuálne uvedenie do prevádzky, až po operatívny monitoring a tréning obsluhy.



---



\## Typy vizualizácie: od 2D trendov po 3D „virtuálnu fabriku“



Z hľadiska nášho projektu má zmysel rozlišovať tri hlavné vrstvy vizualizácie digitálneho dvojčaťa výrobnej linky.



\### 1. 2D HMI/SCADA a dashboardy



Základ tvoria klasické 2D vizualizácie známe zo SCADA systémov a HMI panelov:

\- schematické zobrazenia linky,

\- stavové farby (RUN / STOP / FAULT),

\- aktuálne hodnoty kľúčových veličín,

\- alarmový zoznam.



Pre digitálne dvojča je typické, že tieto obrazovky dopĺňajú:

\- trendové grafy (time-series vizualizácia teplôt, tlakov, rýchlostí konvencií, OEE),

\- agregované ukazovatele (napr. priemerná teplota v časti procesu za poslednú hodinu),

\- prepojenie na kvalitatívne a údržbové údaje (napr. preklik z alarmu na historický graf a posledné zásahy údržby).



Mnohé priemyselné platformy dnes ponúkajú práve takýto typ „digital twin dashboardu“, kde je twin chápaný ako kombinácia viactrendových grafov, HMI obrazoviek a analytických panelov nad historickými dátami.



Tieto 2D dashboardy sú vhodné najmä:

\- pre operátorov v reálnej prevádzke, ktorí potrebujú rýchlo vidieť, či proces beží v špecifikáciách,

\- pre procesných inžinierov, ktorí analyzujú trendy a korelácie.



---



\### 2. 3D model výrobnej linky (3D dashboard / „virtuálna fabrika“)



Ďalšou vrstvou je 3D vizualizácia digitálneho dvojčaťa – priestorový model linky alebo celej haly, ktorý je prepojený s reálnymi dátami.



V praxi sa využívajú najmä herné enginy (Unity, Unreal Engine), ktoré:

\- pracujú s 3D geometrickými modelmi strojov (CAD),

\- v reálnom čase menia textúry, farby a animácie podľa hodnôt zo senzorov.



Každý komponent v 3D scéne (dopravník, miešačka, baliaci stroj) je naviazaný na konkrétne signály z PLC alebo TSDB:

\- farba objektu sa mení podľa stavu (RUN / FAULT),

\- rýchlosť animácie dopravníka odráža skutočnú rýchlosť pásu,

\- nad tankom sa zobrazuje aktuálna teplota a hladina.



V posledných rokoch sa objavujú aj koncepty \*\*3D dashboardov\*\*, kde je 3D model linky priamo hlavným UI a klasické grafy sú do neho vložené ako widgety. Napríklad:

\- klik na stroj otvorí 3D panel s trendom vibrácií,

\- zobrazí históriu porúch,

\- ukáže plán údržby.



Takéto riešenia sú používané najmä v kontexte Industry 4.0, keď chce podnik sprístupniť digitálne dvojča aj mimo úzkeho OT tímu (manažment, zákazníci, návštevy) vo forme interaktívnej virtuálnej prehliadky.



Pre náš use case (spracovanie zemiakov) by 3D model mohol zobrazovať celú výrobnú linku – od príjmu surových zemiakov, cez čistenie, krájanie, praženie, až po balenie – s real-time zvýraznením kritických miest (úzke hrdlá, alarmy, energeticky náročné úseky).



---



\### 3. AR/VR rozhrania a tréning



Rozšírenou nadstavbou vizualizácie sú AR/VR rozhrania.



V prípade \*\*VR (virtuálna realita)\*\* môže digitálne dvojča fungovať ako tréningový simulátor:

\- operátor sa v headsete pohybuje po virtuálnej fabrike,

\- spúšťa a zastavuje stroje,

\- rieši simulované poruchy,

\- učí sa postupy bez rizika poškodenia reálneho zariadenia alebo ohrozenia bezpečnosti.



Case studies z výrobného prostredia potvrdzujú, že takéto tréningové twins:

\- skracujú nábeh nových pracovníkov,

\- umožňujú precvičiť zriedkavé kritické situácie, ktoré by sa v realite nedali bezpečne simulovať.



V \*\*AR (augmented reality)\*\* sa naopak 3D twin „prilepí“ na reálne stroje:

\- technik vidí cez tablet alebo AR okuliare prekryté informácie,

\- aktuálne dáta zo senzorov,

\- históriu porúch,

\- odporúčané kroky údržby.



Výskum ukazuje, že takýto kontextovo uvedomelý systém dokáže personalizovať informácie pre konkrétneho používateľa (operátor vs. údržbár), čím znižuje kognitívnu záťaž a urýchľuje rozhodovanie.



---



\## Používateľské role a ich potreby pri vizualizácii



Pri návrhu vizualizačnej vrstvy digitálneho dvojčaťa je dôležité definovať používateľské persony a ich dátové požiadavky.



\### Operátor linky



\- potrebuje rýchlo vidieť stav zariadení,

\- alarmy,

\- základné procesné veličiny,

\- jednoduché inštrukcie „čo urobiť teraz“.



Kľúčová je prehľadná 2D HMI/SCADA s minimom rušivých prvkov, signálnymi farbami a jasnou hierarchiou alarmov.



\### Údržbár / technik



\- zaujíma ho história porúch,

\- trendy vibrácií,

\- teploty ložísk,

\- čas od poslednej údržby,

\- odporúčané zásahy.



Digitálne dvojča by mu malo umožniť:

\- preklik z alarmu do historického grafu,

\- 3D zobrazenie presnej polohy súčiastky,

\- zobrazenie odporúčaného postupu výmeny.



\### Procesný inžinier



\- potrebuje detailné časové rady,

\- korelácie medzi parametrami (napr. teplota praženia vs. kvalita chipsov),

\- export dát do analytických nástrojov,

\- možnosť spúšťať \*what-if\* simulácie.



Vhodné sú pokročilé dashboardy (napr. Grafana) a 3D twin s možnosťou simulovať zmeny receptúr.



\### Manažment / kvalita



\- zaujíma ho zhrnutie:

&nbsp; - KPI (OEE, výťažnosť, počet porúch, reklamácie),

&nbsp; - heatmapy úzkych miest,

&nbsp; - dlhodobé trendy.



Postačuje jednoduchší manažérsky dashboard alebo 3D prehliadka linky s farebným zvýraznením problémových úsekov.



Literatúra poukazuje na to, že jednotlivé oddelenia (Engineering, Maintenance, Production) majú odlišné požiadavky na vizualizáciu digitálneho dvojčaťa, preto je vhodné navrhovať UI rolovo orientované.



---



\## Architektúra vizualizačnej vrstvy pre náš projekt



Nad už navrhnutým zberom dát (OPC UA / MQTT → middleware → InfluxDB / PostgreSQL) môžeme vizualizačnú vrstvu koncipovať nasledovne.



\### API / backend pre vizualizáciu



\- nad time-series DB (InfluxDB alebo TimescaleDB) beží backend služba,

\- poskytuje REST / GraphQL API a WebSockety pre real-time dáta,

\- API prekladá interný kanonický model záznamu  

&nbsp; (`ts`, `site\_id`, `line\_id`, `asset\_id`, `signal`, `value`)  

&nbsp; na jednoduchšie endpointy pre UI,

\- súčasťou môže byť:

&nbsp; - agregácia (moving average, min/max, downsampling),

&nbsp; - prístup k udalostiam (STATE\_CHANGED, RECIPE\_APPLIED, maintenance eventy).



\### 2D dashboardy



\- pre operátorov a procesných inžinierov:

&nbsp; - Grafana alebo custom webová aplikácia (React / Angular),

\- dashboardy zobrazujú:

&nbsp; - live trendové grafy,

&nbsp; - alarmové panely,

&nbsp; - SPC grafy pre kvalitu (Cp / Cpk),

&nbsp; - prehľad OEE a prestojov.



\### 3D digitálne dvojča linky



\- 3D model linky (import CAD modelov strojov),

\- vytvorený v Unity alebo Unreal Engine,

\- beží ako desktop aplikácia alebo cez WebGL v prehliadači,

\- runtime aplikácia sa pripája cez WebSocket / MQTT / OPC UA client,

\- mapuje dátové streamy na objekty v scéne,

\- v 3D view sú dostupné kontextové panely:

&nbsp; - klik na stroj → detailný graf,

&nbsp; - plán údržby,

&nbsp; - štatistiky kvality.



\### AR/VR nadstavba (voliteľná)



\- 3D twin je znovupoužitý pre VR tréning,

\- umožňuje virtuálne commissioning a tréning obsluhy,

\- pre AR sa využívajú existujúce toolkit-y (napr. Unity),

\- stav stroja je prekrytý pri pohľade cez tablet alebo okuliare.



Takto navrhnutá vrstva rešpektuje existujúcu architektúru projektu a dopĺňa ju o „face“ systému, ktorú vidia koncoví používatelia.



---



\## Prínosy vizualizačnej vrstvy pre náš use case



Implementácia silnej vizualizačnej vrstvy nad digitálnym dvojčaťom prináša viacero praktických benefitov:



\- \*\*Rýchlejšia diagnostika problémov\*\* – operátor vidí v 3D a v trendoch, kde sa tvoria úzke hrdlá, časté zastavenia a aké procesné podmienky k nim viedli.

\- \*\*Lepší tréning personálu\*\* – nový operátor môže prejsť simulované scenáre porúch v bezpečnom prostredí.

\- \*\*Podpora kontinuálneho zlepšovania\*\* – procesný inžinier má dlhodobé vizualizácie na identifikáciu opakujúcich sa vzorov.

\- \*\*Zdieľaná „jediná verzia pravdy“\*\* – všetci používatelia pracujú s rovnakými dátami a rovnakým digitálnym modelom, čo znižuje misinterpretácie a zvyšuje dôveru v rozhodovanie.



