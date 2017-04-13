<properties 
   pageTitle="Hardware per StorSimple 10 GbE interfacce | Microsoft Azure"
   description="Descrive i ricetrasmettitori (funzione) collegabili small form factor supportati, cavi e i parametri 10 GbE interfacce di rete sul dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware supportato 10 GbE interfacce di rete in un dispositivo StorSimple

## <a name="overview"></a>Panoramica

In questo articolo vengono fornite informazioni supplementare hardware che funziona con il dispositivo di Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Elenco dei dispositivi testati da Microsoft

Microsoft ha verificato il seguente i ricetrasmettitori (funzione) collegabili small form factor, cavi e parametri per garantire che funzionano in modo ottimale con i dispositivi. (Verranno aggiornate nelle tabelle seguenti in base alla verifica di nuovo hardware.)

### <a name="sfp-transceivers"></a>Funzione + ricetrasmettitori

|Rendere|Modello|
|---|---|
|Cisco|FUNZIONE-10G-SR|

### <a name="cables"></a>Cavi

|S. No. |Rendere|Modello|
|---|---|---|
| 1.|Cisco|FUNZIONE-H10GB-CU1M|
| 2.|Cisco|FUNZIONE-H10GB-CU2M|
| 3.|Cisco|FUNZIONE-H10GB-CU3M|
| 4.|Tripp Lite|N820 - 05M (OM3)|

### <a name="switches"></a>Parametri

|S. No.|Rendere|Modello|
|---|---|---|
| 1. |Cisco|10GE DI C3172PQ N3K|
| 2. |Cisco|N3K-C3048 ZM F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Elenco dei dispositivi testati nel campo

In questa sezione contiene l'elenco dei dispositivi che sono state distribuite correttamente nel campo dai clienti StorSimple. Questi non sono stati testati da Microsoft, ma è probabile che per l'uso con il dispositivo StorSimple.
 
| Parametro                         | Valore                                    |
|-----------------------------------|------------------------------------------|
| Assicurarsi di parametro                     | Juniper                                  |
| Modello di parametro                    | ex4550 32F                               |
| Versione del sistema operativo cambia | JunOS 12.3R9.4                           |
| Modello blade                     | Porte integrata (PIC 0)                    |
| Creazione di ricetrasmettitore                  | Juniper                                  |
| Modello di ricetrasmettitore               | Numero parte 740 021308 <br></br> Numero parte 740 030658                   |
| Versione del firmware ricetrasmettitore    | REV 01 versione 0,0 (segnalati)            |
| Modello di cavo                     | Fronte retro ponticello LC/LC 50/125µ, OM3, LSZH |
| Modello StorSimple                | 8600                                     |
| Versione del software StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Elenco dei dispositivi testati dal provider OEM (Mellanox)  

Si è verificato Mellanox i seguenti i ricetrasmettitori (funzione) collegabili small form factor, cavi e i parametri per garantire che funzionano in modo ottimale con interfacce di rete Mellanox, ad esempio le interfacce di rete GbE 10 in un dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cavi e moduli supportati da Mellanox 

La tabella seguente elenca i cavi e moduli supportati da Mellanox. Questi non sono stati testati da Microsoft, ma è probabile che per l'uso con il dispositivo StorSimple.

| S. No. | Velocità | Modello                 | Descrizione                                            | Rendere                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-FUNZIONE-FUNZIONE - 1M        | Funzione + 10 Gb/s 1M i cavi in rame passivo                   | Arista                |
| 2.     | 10 GbE| CAB-FUNZIONE-FUNZIONE - 2M        | cavo rame passiva funzione + 10 Gb/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB-FUNZIONE-FUNZIONE - 3M        | cavo rame passiva funzione + 10 Gb/s 3m                   | Arista                |
| 4.     | 10 GbE| CAB-FUNZIONE-FUNZIONE - 5M        | cavo rame passiva funzione + 10 Gb/s 5m                   | Arista                |
| 5.     | 10 GbE| Funzione Cisco-H10GBCU1M   | Funzione + cavo Cisco                                       | Cisco                 |
| 6.     | 10 GbE| Funzione Cisco-H10GBCU3M   | Funzione + cavo Cisco                                       | Cisco                 |
| 7.     |10 GbE | Funzione Cisco-H10GBCU5M   | Funzione + cavo Cisco                                       | Cisco                 |
| 8.     | 10 GbE| HP J9281B X242 10 G    | Funzione + alla funzione + 1M per indirizzare collegare il cavo di rame             | HP                    |
| 9.     | 10 GbE| 455883 B21 HP BLc     | 10Gb SR funzione + acconsentire                                       | HP                    |
| 10.    | 10 GbE| 455886 B21 HP BLc     | 10Gb LR funzione + acconsentire                                       | HP                    |
| 11.    |10 GbE | 487649 B21 HP BLc     | Funzione + 0,5 m 10GbE cavo di rame                           | HP                    |
| 12.    |10 GbE | 487652 B21 HP BLc     | Funzione + 1M 10GbE cavo di rame                             | HP                    |
| 13.    |10 GbE | 487655 B21 HP BLc     | Funzione + 3m 10GbE cavo di rame                             | HP                    |
| 14.    |10 GbE | 487658 B21 HP BLc     | Funzione + 7m 10GbE cavo di rame                             | HP                    |
| 15.    |10 GbE | 537963 B21 HP BLc     | Funzione + 5m 10GbE cavo di rame                             | HP                    |
| 16.    |10 GbE | HP AP784A             | Cavo serie C passiva rame funzione + 3m                  | HP                    |
| 17.    |10 GbE | HP AP785A             | Cavo serie C passiva rame funzione + 5m                  | HP                    |
| 18.    |10 GbE | HP AP818A             | Funzione di rame Active + cavo di B serie 1M                   | HP                    |
| 19.    |10 GbE | HP AP819A             | Funzione di rame Active + cavo di B serie 3m                   | HP                    |
| 20.    |10 GbE | HP J9150A             | X132 G 10 funzione + ricetrasmettitore di SR LC                        | HP                    |
| 21.    |10 GbE | HP J9151A             | X132 G 10 funzione + ricetrasmettitore di LR LC                        | HP                    |
| 22.    |10 GbE | HP J9283B             | X242 10 G funzione + funzione + 3 m DAC cavo                        | HP                    |
| 23.    |10 GbE | HP J9285B             | X242 10 G funzione + funzione + 7 DAC cavo m                        | HP                    |
| 24.    | 10 GbE| HP JD095B             | X240 10 G funzione + funzione + 0,65 DAC cavo m                     | HP                    |
| 25.    | 10 GbE| HP JD096B             | X240 10 G funzione + funzione + 1,2 DAC cavo m                      | HP                    |
| 26.    | 10 GbE| HP JD097B             | X240 10 G funzione + funzione + cavo 3 m padre                        | HP                    |
| 27.    | 10 GbE| MAM1Q00A QSA Mellanox | QSFP alla funzione + scheda                                   | Tecnologie Mellanox |
| 28.    | 10 GbE| MC2309124 006 Mt      | Cavo di rame passivo 1 x SFP+ a QSFP 10 Gb/s 24awg 7 m   | Tecnologie Mellanox |
| 29.    | 10 GbE| 007 MC2309124 Mt      | Cavo di rame passivo 1 x SFP+ a QSFP 10 Gb/s 24awg 7 m   | Tecnologie Mellanox |
| 30.    | 10 GbE| MC2309130 003 Mt      | Cavo di rame passivo 1 x SFP+ a QSFP 10 Gb/s 30awg 3 m   | Tecnologie Mellanox |
| 31.    | 10 GbE| MC2309130 00A Mt      | Cavo di rame passivo 1 x SFP+ a QSFP 10 Gb/s 30awg 0,5 m | Tecnologie Mellanox |
| 32.    | 10 GbE| MC3309124 005 Mt      | 1 24awg di 10 Gb/s x SFP+ i cavi in rame passiva 5 m           | Tecnologie Mellanox |
| 33.    | 10 GbE| 007 MC3309124 Mt      | 1 24awg di 10 Gb/s x SFP+ i cavi in rame passiva 7 m           | Tecnologie Mellanox |
| 34.    | 10 GbE| MC3309130 003 Mt      | 1 30awg di 10 Gb/s x SFP+ i cavi in rame passiva 3 m           | Tecnologie Mellanox |
| 35.    | 10 GbE| MC3309130 00A Mt      | 1 30awg di 10 Gb/s x SFP+ i cavi in rame passiva 0,5 m         | Tecnologie Mellanox |

### <a name="switches-supported-by-mellanox"></a>Opzioni supportate da Mellanox 

La tabella seguente elenca le opzioni supportate da Mellanox. Questi non sono stati testati da Microsoft, ma è probabile che per l'uso con il dispositivo StorSimple.

| S. No. | Velocità | Modello | Descrizione                                                         | Rendere              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733 B21  | HP 6120XG ProCurve 10GbE Ethernet Blade Switch                      | HP    |
| 2.     | 10GbE | 538113 B21  | Modulo di pass-through 10GbE HP (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | Modulo di IBM PureFlex sistema tessuti EN4093 Gigabit 10 cambia Scalable | IBM   |
| 4.     | 1GbE  | 3020        | Opzione blade di Cisco Catalyst 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Blade di parametro Cisco Catalyst 3020 X 1GbE                              | Cisco |
| 6.     | 1GbE  | 438030 B21  | Modulo di parametro 1GbE HP - GbE2c livello 2/3 Ethernet Blade passare       | HP    |
| 7.     | 1GbE  | 6120G       | Blade di HP ProCurve 6120G/XG 1GbE parametro                              | HP    |

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sui componenti hardware StorSimple e lo stato](storsimple-monitor-hardware-status.md).
