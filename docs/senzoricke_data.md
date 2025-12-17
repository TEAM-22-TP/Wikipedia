Všetky zdroje sú po anglicky, takže text je zatiaľ vedený v angličtine a bude preložený neskôr.



---



\## Operational Technology (OT)



\- physical world (machines, valves, robots)

\- monitoring and controlling physical devices, processes, and events

\- industrial and critical infrastructure environments

\- robust, proprietary, and designed for longevity



\### Control operation levels



\- \*\*Level 0\*\* – field devices / sensors + final control elements  

&nbsp; - sensors  

&nbsp; - actuators  

&nbsp; - electric motors  

&nbsp; - console lights  

&nbsp; - switches  

&nbsp; - valves  

&nbsp; - contactors



\- \*\*Level 1\*\* – industrialized input/output modules and processors



\- \*\*Level 2\*\* – supervisory computers  

&nbsp; - collate information from Level 1



\- \*\*Level 3\*\* – production control  

&nbsp; - production and target monitoring  

&nbsp; - warehouse management  

&nbsp; - maintenance tracking



\- \*\*Level 4\*\* – production scheduling  

&nbsp; - ERP  

&nbsp; - product lifecycle management  

&nbsp; - customer relationship management  

&nbsp; - human resource management



\### SCADA



\- hierarchical architecture for high-level supervision of machines and processes

\- high-level operator interface  

&nbsp; - monitoring  

&nbsp; - issuing of process commands

\- subordinated operations  

&nbsp; - control logic  

&nbsp; - calculations  

&nbsp; - performed by networked modules connected to field sensors and actuators



\### DCS



\- control system for a process or plant

\- autonomous controllers are distributed throughout the system

\- no central operator supervisory control

\- reliability due to the distribution of control processing around nodes in the system



> Functionality of SCADA and DCS systems is very similar, but DCS tends to be used on large continuous process plants where high reliability and security is important.



\### PLC



\- programmable Level 1 device

\- connects to Level 0 devices  

&nbsp; - reads their values  

&nbsp; - controls them

\- connects to higher levels  

&nbsp; - Ethernet  

&nbsp; - Wi-Fi



\### RTU



\- remote Level 1 device

\- mostly used for monitoring

\- sometimes has features of PLC



\### Industrial control gateway



\- connects industrial protocols to an IP network



---



\## Information Technology (IT)



\- digital world (email, databases, finance)

\- computing systems, software, and infrastructure

\- managing and processing data to support business operations

\- confidentiality, integrity, and availability of digital information

\- servers, PCs, standard networks  

&nbsp; - Ethernet  

&nbsp; - Wi-Fi

\- general protocols  

&nbsp; - TCP/IP  

&nbsp; - HTTP(S)

\- office and data center environments

\- systems use standard operating systems  

&nbsp; - Windows  

&nbsp; - Linux

\- refreshed relatively frequently (3–5 years)



---



\## Primitive connection



\- current loops (4–20 mA)  

&nbsp; - single analog value over two wires  

&nbsp; - current-based (4 mA low, 20 mA high)  

&nbsp; - used between sensors and controllers  

&nbsp; - used between controllers and modulating field devices  

&nbsp;   - control valves

\- digital I/O

\- raw serial  

&nbsp; - RS-232



---



\## Fieldbus



\- IEC standardized

\- networking protocol

\- connection from Level 1 to Level 0

\- controller has a single port that can connect to many devices

\- many network topologies  

&nbsp; - daisy-chain  

&nbsp; - star  

&nbsp; - ring  

&nbsp; - branch  

&nbsp; - tree

\- many different \*profiles\*  

&nbsp; - standardized  

&nbsp; - not intercompatible



\### Most important fieldbus protocols



\#### EtherNet/IP



\- IP = Industrial Protocol (not Internet Protocol)

\- Ethernet (TCP/UDP)

\- adapts the Common Industrial Protocol (CIP) to standard Ethernet

\- uses standard IT tools and infrastructure



\#### Modbus



\- one of the oldest and most widespread protocols

\- openly published and royalty-free

\- open standard

\- nearly every industrial device supports it



\*\*Mediums:\*\*

\- RTU / ASCII  

&nbsp; - serial (RS-485 / RS-232)

\- TCP/IP  

&nbsp; - Ethernet



\#### PROFIBUS DP \& PA



\- PROFIBUS = Process Field Bus

\- serial (RS-485)

\- centralized master/slave model

\- master polls devices sequentially

\- topology  

&nbsp; - line



\*\*Types:\*\*



\- \*\*PROFIBUS DP (Decentralised Peripherals)\*\*  

&nbsp; - discrete manufacturing  

&nbsp; - operates sensors and actuators via centralised controller



\- \*\*PROFIBUS PA (Process Automation)\*\*  

&nbsp; - process automation in harsh or hazardous areas  

&nbsp; - allows power delivery over the bus to field instruments  

&nbsp; - limited amount of devices



\- massive installed base



\#### PROFINET



\- Process Field Network

\- optional IRT protocol  

&nbsp; - Isochronous Real-Time

\- Ethernet based (TCP/IP)

\- Provider / Consumer model

\- full duplex

\- real-time, high speed

\- higher speed and more devices compared to PROFIBUS

\- topology  

&nbsp; - star  

&nbsp; - line  

&nbsp; - ring  

&nbsp; - tree



\#### HART (Highway Addressable Remote Transducer)



\- smart field devices  

&nbsp; - transmitters  

&nbsp; - valves

\- used mainly in process industries

\- uses existing 4–20 mA wiring

\- hybrid analog + digital



\*\*Topology:\*\*

\- point-to-point  

&nbsp; - single device  

&nbsp; - single analog signal  

&nbsp; - multiple digital signals

\- multi-drop (max 64 devices)  

&nbsp; - digital signals only



---



\## Other industrial protocols



\- AFDX

\- AS-Interface

\- CANopen

\- CompoNet

\- ControlNet

\- CC-Link

\- DeviceNet

\- EtherCAT

\- Ethernet Powerlink

\- Interbus

\- LonWorks

\- SERCOS III

\- SERCOS interface

\- Foundation Fieldbus H1

\- Foundation HSE

\- RAPIEnet



---



\## OPC Unified Architecture (OPC UA)



\- Open Platform Communications Unified Architecture

\- bridges the gap between IT and OT

\- cross-platform

\- open-source

\- IEC 62541 standard

\- replaces old OPC (Windows-only)



\*\*Purpose:\*\*

\- standard for data exchange from sensors to cloud applications

\- moves data between:

&nbsp; - industrial systems  

&nbsp;   - PLCs  

&nbsp;   - HMIs  

&nbsp;   - SCADA

&nbsp; - higher-level IT / enterprise systems  

&nbsp;   - MES  

&nbsp;   - ERP  

&nbsp;   - Cloud



\*\*Protocol mappings:\*\*

\- TCP/IP

\- UDP/IP

\- WebSockets

\- AMQP

\- MQTT



\*\*Communication models:\*\*

\- client / server

\- publish / subscribe



---



\## Notes



\### ERP (Enterprise Resource Planning)



\- integrated management of main business processes, often in real time

\- business management software

\- collect, store, manage, and interpret data from many business activities

\- planning all resources required to meet business objectives



\### MES (Manufacturing Execution System)



\- real-time monitoring system

\- tracks and documents transformation of raw materials to finished goods

\- captures data, processes, and outcomes of manufacturing

\- helps manufacturing decision-makers understand how current plant-floor conditions can be optimized to improve production output



\### HMI (Human-Machine Interface)



\- user interface between human and machine



---



\## Zhrnutie



Senzorické dáta sú posielané cez veľký počet protokolov do PLC, RTU, industrial gateway alebo podobných zariadení, čo je mimo našej priamej kontroly. Tieto dáta budú s najväčšou pravdepodobnosťou cez TCP/IP posielané do supervisory počítačov.



Mali by sme mať k dispozícii systém, ktorý používa niečo ako \*\*OPC UA\*\*, z ktorého vieme získať potrebné informácie. Náš projekt je podobný konceptu \*\*MES\*\*, takže na náš use case by už mali byť existujúce interface v existujúcich systémoch, ktoré sú pre nás vhodné.



