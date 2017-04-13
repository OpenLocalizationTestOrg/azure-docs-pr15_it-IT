<properties 
   pageTitle="Tecniche di StorSimple | Microsoft Azure"
   description="Descrive le tecniche e informazioni sulla conformità alle normative standard per i componenti hardware StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Tecniche e conformità per il dispositivo StorSimple

## <a name="overview"></a>Panoramica

I componenti hardware del dispositivo Microsoft Azure StorSimple rispettare le specifiche tecniche e standard normativi descritte in questo articolo. Specifiche tecniche descrivono la potenza e moduli raffreddamento (PCMs), unità disco, capacità di archiviazione e allegati. Informazioni sulla conformità copre ad esempio standard internazionali, sicurezza ed emissioni e cablaggio.


## <a name="power-and-cooling-module-specifications"></a>Specifiche di alimentazione e raffreddamento modulo  

Il dispositivo StorSimple include due 100-240V doppia ventola, SBB conforme raffreddamento moduli di alimentazione (PCMs). In questo modo una configurazione di alimentazione. Se un PCM non riesce, il dispositivo continua a funzionare normalmente in altri PCM finché non viene sostituito il modulo non riuscito.  

Simbolo EBOD utilizza un PCM W 580 e simbolo principale un 764 PCM W. Le tabelle seguenti elencano le specifiche tecniche associate il PCMs.

| Specifica           | 580 W PCM (EBOD)                                    | 764 W PCM (principale)                                |
|------------------------ | --------------------------------------------------- | -------------------------------------------------- |
| Potenza massima in uscita    | 580 W                                               | 764                                                |
| Frequenza               | 50/60Hz                                            | 50/60Hz                                           |
| Selezione dell'intervallo tensione | Automatico che vanno: 90-264 V AC, 47/63 Hz               | Automatico che vanno: 90-264 V AC, 47/63 Hz               |
| Picco massimo di corrente  | 20 A                                                | 20 A                                               |
| Correzione fattore di potenza | > voltaggio nominale 95%                          | > voltaggio nominale 95%                         |
| Armonica               | Soddisfa EN61000-3-2                                   | Soddisfa EN61000-3-2                                  |
| Output                  | Tensione Standby 5 v @ 2.0 A                          | Tensione Standby 5 v @ 2.7 A                         |
|                         | + 5 v @ 42 A                                          | + 5 v @ 40 A                                         |
|                         | + 12 v @ 38 A                                         | + 12 v @ 38 A                                        |
| Caldo           |  Sì                                                | Sì                                                |
| LED e parametri       | AC interruttore e un indicatore di stato quattro LED     | AC interruttore e un indicatore di stato sei LED     |
| Simbolo di raffreddamento       | Assiale raffreddamento ventole con il variabile controllo  | Assiale raffreddamento ventole con il variabile controllo |

 
## <a name="power-consumption-statistics"></a>Statistiche consumo di energia  

Nella tabella seguente sono elencati i dati di consumo power tipico (valori effettivi possono variare dal pubblicato) per i diversi modelli di dispositivo StorSimple. 
 
 Condizioni | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC 
 ---------- | -------- | -------- | -------- | -------- | -------- | -------- 
 Unità di tempo, ventole inattiva | 1,45 RISPOSTE  |0.31 kW | 1057.76 BTU/h | 3.19 RISPOSTE | 0.34 kW | 1160.13 BTU/h 
 Unità di tempo, ventole accesso | 1,54 RISPOSTE | 0.33 kW | 1126.01 BTU/h | 3.27 RISPOSTE | 0,36 ai ricavi kW | 1228.37 BTU/h 
 Con tecnologia due PSUs unità veloci, ventole inattiva, | 2.14 A | 0.49 kW  | 1671.95 BTU/h | 4,99 RISPOSTE | 0,54 kW | 1842.56 BTU/h 
 Ventole veloce, unità inattivo, una semplificato tecnologia uno inattive | 2.05 RISPOSTE | 0.48 kW | 1637.83 BTU/h | 4.58 A | 0,50 kW | 1706.07 BTU/h 
 Unità veloci, ventole accedere, due PSUs con tecnologia | 2.26 RISPOSTE | 0.51 kW | 1740.19 BTU/h | 4,95 RISPOSTE | 0,54 kW | 1842.56 BTU/h 
 Ventole veloce, unità accedere, uno semplificato con tecnologia uno inattive | 2.14 A |0.49 kW | 1671.95 BTU/h | 4.81 A  | 0.53 kW | 1808.44 BTU/h 

## <a name="disk-drive-specifications"></a>Specifiche di un'unità disco  

Dispositivo StorSimple supporta fino a 12 3,5 modulo fattore seriale collegato SCSI (SA) le unità disco. Le unità effettive possono essere una combinazione di mercato unità (SSDs) o dischi rigidi (dischi rigidi), a seconda della configurazione di prodotti. Le 12 bande unità disco si trovano in una configurazione 3, 4 che precede il simbolo. Simbolo EBOD consente di spazio di archiviazione aggiuntivo per un'altra unità disco 12. Questi sono sempre dischi rigidi.  

## <a name="storage-specifications"></a>Specifiche di spazio di archiviazione
I dispositivi StorSimple dispongono di una combinazione di unità disco rigido e mercato per il modello 8100 e 8600. La capacità utilizzabile totale per il modello 8100 e 8600 sono circa 15 TB e 38 TB. Nella tabella seguente vengono illustrati i dettagli di SSD, sul disco rigido e capacità cloud nel contesto della capacità di soluzione StorSimple.

| Modello di dispositivo / capacità                         | 8100                                                    | 8600                                                    |
|------------------------------------------------|---------------------------------------------------------|---------------------------------------------------------|
| Numero di unità disco rigido (dischi rigidi)              |   8                                                     |  19                                                     |
| Numero di unità di mercato (SSDs)            |   4                                                     |  5                                                      |
| Singola capacità del disco rigido                            |   4 TB                                                  |  4 TB                                                   |
| Capacità SSD singola                            |   400 GB                                                |  800 GB                                                 |
| Capacità di riserva                                 |   4 TB                                                  | 4 TB                                                    |
| Capacità del disco rigido utilizzabile                            | TB 14                                                   | 36 TB                                                   |
| Capacità SSD utilizzabile                            | 800 GB                                                  | 2 TB                                                    |
| Totale utilizzabile capacità *                         | ~ TB 15                                                 | ~ 38 TB                                                 |
| Capacità di soluzione massimo (inclusi cloud)    | 200 TB                                                  | 500 TB                                                  |


<sup> * </sup> -  *La capacità utilizzabile totale include la capacità disponibile per i dati, metadati e buffers.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Specifiche lo spessore e le dimensioni di simbolo  

Le tabelle seguenti elencano le varie specifiche simbolo per le dimensioni e lo spessore.  

### <a name="enclosure-dimensions"></a>Dimensioni di simbolo
La tabella seguente elenca le dimensioni del simbolo in millimetri e pollici.

|Simbolo |Millimetri |Pollici |
|----------|------------|-------| 
| Altezza |87,9 | 3,46 |
| Larghezza tra flangia di montaggio | 483 | 19.02 |
| Larghezza tra corpo del simbolo | 443 | 17.44 |
| Profondità da flangia di montaggio anteriore a estremità del corpo del simbolo | 577 | 22.72 |
| Antero-posteriore dal Pannello di operazioni a più estremità di simbolo | 630.5 | 24.82 |
| Profondità da flangia di montaggio a più estremità di simbolo |   603 | 23.74 |

### <a name="enclosure-weight"></a>Peso simbolo  

A seconda della configurazione, un simbolo principale completa di valutare da 21 a 33 kg e richiede due persone risolvere il problema. 
 
| Simbolo | Peso |
|-----------|--------| 
| Peso massimo (dipende dalla configurazione) |30 kg: 33 kg |
| Vuoto (nessuna unità corretti in modo che) |21-kg 23 |

## <a name="enclosure-environment-specifications"></a>Specifiche di ambiente simbolo  

In questa sezione sono elencati specifiche relative all'ambiente di simbolo. Temperatura, umidità, altitudine, elettriche, vibrazione, orientamento, sicurezza e la compatibilità elettromagnetica (CEM) sono inclusi in questa categoria.  

### <a name="temperature-and-humidity"></a>Temperatura e umidità

| Simbolo        | Intervallo di temperatura  | Ambiente umidità | Massimo bagnato lampadina   |
|------------------|----------------------------|---------------------------|--------------------|
| Operativi      | 5-35° C (41° F - 95° F)    | 20-80% non-condensa- | 28° C (82° F)        |
| Non operativi  | -40-70° C (40° F - 158° F) | 5-100% senza condensa  | 29° C (84° F)        |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Ventilazione, altitudine, elettriche, vibrazione, orientamento, sicurezza ed EMC
 
| Simbolo          | Specifiche operative                                                |
|--------------------|---------------------------------------------------------------------------| 
| Aria            | Aria sistema è anteriore a posteriore. Sistema deve essere gestito con un'installazione a bassa pressione, scarico posteriore. La pressione creata da rack porte e ostacoli non deve superare 5 Pascal (colonna d'acqua 0,5 mm). |
| Altitudine operativi  | -30 metri a 3045 metri (-100 a 10.000 piedi) con temperatura massima deselezionare con 5 ° C sopra piedi 7000. |
| Altitudine non operative  | metri da-305 m a 12.192 metri (-1.000 a 40.000 piedi) |
| Elettriche operativi  | seno di 10 ms ½ 5g |
| Elettriche non operative  | seno di 30g 10 ms ½ |
| Vibrazione operativa  | g 0.21 RMS 5-500Hz casuale |
| Vibrazione non operative  | g 1.04 RMS 2-200Hz casuale |
| Vibrazione, trasferimento  | seno di 2-200Hz 3G |
| Orientamento e montaggio  | installazione a rack 19"(unità EIA 2) |
| Fisse  | Per adattare antero-posteriore minimo 700 mm (31.50 pollici) sistemi rack compatibile con 297 IEC |
| Sicurezza e le approvazioni  |   CE e UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| EMC  | EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Conformità standard internazionali
Il dispositivo di Microsoft Azure StorSimple è conforme alle norme internazionali seguenti:  

- CE - EN 60950-1  
- Report CB per IEC 60950-1  
- UL e cUL a UL 60950-1  

## <a name="safety-compliance"></a>Conformità di sicurezza  

Il dispositivo di Microsoft Azure StorSimple soddisfa le valutazioni di sicurezza seguenti:  

- Approvazione di tipo prodotto di sistema: UL, cUL, CE  
- Conformità di sicurezza: UL 60950 IEC 60950, EN 60950  

## <a name="emc-compliance"></a>Conformità EMC 

Il dispositivo di Microsoft Azure StorSimple soddisfa le valutazioni EMC seguenti.  

### <a name="emissions"></a>Emissioni

Il dispositivo è compatibile con EMC per i livelli condotti e irradiata emissioni.  

- Emissioni condotti limitare livelli: CFR 47 Part 15 ter classe EN55022 Classe A CISPR classe una  
- Emissioni irradiate limitare livelli: CFR 47 Part 15 ter classe EN55022 Classe A CISPR classe una   

### <a name="harmonics-and-flicker"></a>Armonica e flicker  

Il dispositivo è conforme alle EN61000-3-2/3.  

### <a name="immunity-limit-levels"></a>Livelli di immunità limite  
Il dispositivo è conforme alle EN55024.  

## <a name="ac-power-cord-compliance"></a>Conformità cavo di alimentazione AC
  
Il plug e assembly cavo power completa deve soddisfare i criteri appropriati per il paese in cui viene utilizzato il dispositivo e hanno le approvazioni di sicurezza che sono accettabili nel paese. Le tabelle seguenti elencano standard per USA ed Europa.  

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Cavi di alimentazione AC - negli Stati Uniti (deve essere elencato NRTL)

| Componente       | Specifica                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo di alimentazione       | SV o SVT 18 minimo AWG, direttore 3, lunghezza massima di metri 2.0 |
| Plug            | Plug di tipo di terra allegato 5-15P NEMA nominale 120 V, 10 risposte; o IEC 320 C14, 250 V, 10 risposte |
| Socket          | IEC 320 C-13, 250 V, 10 RISPOSTE                                         |

### <a name="ac-power-cords---europe"></a>Cavi di alimentazione AC - Europa

| Componente       | Specifica                                                     |
| --------------- | ----------------------------------------------------------------- | 
| Tipo di alimentazione       | Armonizzate, 3G1.0 di VVF H05                                         |
| Socket          | IEC 320 C-13, 250 V, 10 RISPOSTE                                         |

## <a name="supported-network-cables"></a>Cavi di rete supportati  

10 GbE interfacce di rete, dati 2 e 3 di dati, fare riferimento all' [elenco dei moduli e i cavi di rete supportati](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Passaggi successivi

A questo punto si è pronti distribuire un dispositivo StorSimple nel centro dati. Per ulteriori informazioni, vedere [distribuzione dispositivo locale](storsimple-deployment-walkthrough-u2.md).  
