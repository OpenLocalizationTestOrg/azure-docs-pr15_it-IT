<properties 
   pageTitle="Sostituzione di componenti hardware StorSimple | Microsoft Azure"
   description="In questo articolo viene descritto in modo sicuro sostituire il PCMs batteria, moduli controller, EBOD controller, unità disco e chassis di un dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Sostituzione di componenti hardware StorSimple

## <a name="overview"></a>Panoramica

Le esercitazioni di sostituzione componenti hardware descrivono i componenti hardware del dispositivo Microsoft Azure StorSimple 8000 serie e i passaggi necessari per rimuovere e sostituirli. In questo articolo vengono descritte le icone di sicurezza, vengono forniti collegamenti alle esercitazioni dettagliate e sono elencati i componenti sostituibili.

>[AZURE.IMPORTANT] Prima di rimuovere o sostituire qualsiasi componente StorSimple, verificare che le [convenzioni sull'icona di sicurezza](#safety-icon-conventions) e altre [precauzioni di sicurezza](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Convenzioni sull'icona di sicurezza

Nella tabella seguente descrive le icone di sicurezza usate in queste esercitazioni. Prestare particolare attenzione a queste icone di sicurezza durante il lavoro tramite la procedura per rimuovere e sostituire componenti del dispositivo.

| Icona | Testo | Ulteriori informazioni |
|:---- |:---- |:-----------|
|![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png)| **RISCHIO!** | Indica una situazione pericolosa che, se non è evitato, darà come risultato in morte o gravemente. La parola segnale è limitata ai più estreme.|
|![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png)| **AVVISO.** | Indica una situazione pericolosa che, se non è evitato, potrebbe verificarsi in morte o gravemente.|
|![Icona di avviso](./media/storsimple-hardware-component-replacement/Caution.png)| **ATTENZIONE!** |Indica una situazione pericolosa che, se non è evitato, potrebbe causare pregiudizio secondario o moderato.|
|![Icona di avviso](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **NOTA:** | Indica informazioni considerate importante, ma non correlati rischio.|
|![Icona di impianti elettrici elettriche](./media/storsimple-hardware-component-replacement/Electric.png) | **Impianti elettrici elettriche rischio** | Indica ad alta tensione.|
|![Icona approfondita](./media/storsimple-hardware-component-replacement/Weight.png)| **Approfondita**| |
|![Icona di manutenzione parti nessun utente](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Parti di manutenzione nessun utente** | Non hanno accesso a meno che non sia stato formato adeguatamente.|
|![Icona di istruzioni di lettura](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Leggere per primo tutte le istruzioni**| |
|![Icona di rischio suggerimento](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Suggerimento rischio**| |

### <a name="before-you-begin"></a>Prima di iniziare

Acquisire familiarità con le informazioni di sicurezza le icone di dispositivo e la sicurezza utilizzate in questa esercitazione. Passare a [in modo sicuro installare e utilizzare un dispositivo StorSimple](storsimple-safety.md) per informazioni complete. Assicurarsi di esaminare [le misure di sicurezza](storsimple-safety.md#handling-precautions) prima di aver gestito il dispositivo StorSimple. 

Prima di tentare di sostituire un componente, considerare le seguenti informazioni.

![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png) ![sull'icona di impianti elettrici elettriche](./media/storsimple-hardware-component-replacement/Electric.png) **avviso!** 

- Collegarsi a terra correttamente utilizzando un elettrostatica o bordatura antistatiche durante la gestione di moduli e i componenti del dispositivo StorSimple.

- Toccare qualsiasi circuiti. Utilizzare le guide e punti di manipolazione di fornito durante la gestione componenti esposti potrebbero circuiti.

![Icona di avviso](./media/storsimple-hardware-component-replacement/Warning.png) ![un'icona a forma](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **Segnalatore:**

Quando si sostituisce un modulo, **non lasciare mai un alloggiamento vuoto nella parte posteriore del simbolo**. Ottenere un modulo vuoto o sostituzione prima di rimuovere la parte del problema.

## <a name="hardware-component-replacement-procedures"></a>Procedure di sostituzione componenti hardware

Il dispositivo di serie 8000 StorSimple è costituita da più moduli plug-in di principale e/o allegati EBOD. Il modello 8100 ha un singolo simbolo principale, mentre il 8600 è un dispositivo simbolo doppio con un simbolo primario e un simbolo EBOD.

Nelle tabelle seguenti vengono riepilogati i componenti hardware principale del dispositivo. Fare clic sul collegamento nella colonna **routine sostitutiva** per passare all'esercitazione associato.

|Componenti|# Presenta|Modulo plug-in?|Procedura di sostituzione
|:---------|:--------|:--------------|:---------------------|
| Chassis|1|No|[Sostituire chassis sul dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controller principale|2|Sì| [Sostituire un modulo controller sul dispositivo StorSimple](storsimple-controller-replacement.md) |
|Power 764W e raffreddamento moduli (PCMs)|2|Sì| [Sostituire una potenza e raffreddamento modulo in un dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Riserva|2|Sì| [Sostituire il modulo di riserva sul dispositivo StorSimple](storsimple-battery-replacement.md) |
|Unità disco|12|Sì| [Sostituire un'unità disco sul dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabella 1** Componenti hardware in simbolo principale

Simbolo principale e il simbolo EBOD differenti i moduli i/o. Inoltre, le PCMs essere potenza diverso. PCMs in simbolo principale sono 764 W, mentre quelli disponibili nelle simbolo EBOD sono 580 W. PCMs in simbolo principale contengono anche un modulo di riserva.

|Componenti|# Presenta|Modulo plug-in?| Procedura di sostituzione
|:---------|:--------|:--------------|:---------------------|
|Chassis|1|No| [Sostituire chassis sul dispositivo StorSimple](storsimple-chassis-replacement.md) |
|Controller EBOD|2|Sì| [Sostituire un controller EBOD nel dispositivo StorSimple](storsimple-ebod-controller-replacement.md) |
|Power 580W e raffreddamento moduli (PCMs)|2|Sì| [Sostituire una potenza e raffreddamento modulo in un dispositivo StorSimple](storsimple-power-cooling-module-replacement.md) |
|Unità disco|12|Sì| [Sostituire un'unità disco sul dispositivo StorSimple](storsimple-disk-drive-replacement.md) |

**Tabella 2** Componenti hardware in simbolo EBOD

Nel seguente anteriore e posteriori diagrammi vengono evidenziati i moduli plug-in sul dispositivo. È possibile utilizzare questi diagrammi per determinare la posizione dei vari moduli plug-in, se è necessaria una sostituzione. Il diagramma anteriore Mostra le unità disco e i diagrammi posteriori di simbolo EBOD e viene visualizzato un simbolo principale moduli plug-in.

![Frontplane del dispositivo con unità disco](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figura 1** Primo piano del dispositivo

|Etichetta|Descrizione|
|:----|:----------|
|0 - 11|Unità disco (totale 12)|

Simbolo principale e il simbolo EBOD dispongono di unità carrier moduli. Chassis ospita dodici 3,5" unità disco disposte in un formato di 3, 4.

![Base dei moduli di simbolo principale dispositivo](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figura 2** Che quello posteriore simbolo principale

|Etichetta|Descrizione|
|:----|:----------|
|1|PCM 0|
|2|1 PCM|
|3|Controller 0|
|4|Controller 1|

![Base di moduli plug-in dispositivo EBOD simbolo](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figura 3** Che quello posteriore simbolo EBOD

|Etichetta|Descrizione|
|:----|:----------|
|1|PCM 0|
|2|1 PCM|
|3|Controller EBOD 0|
|4|EBOD Controller di 1|

## <a name="field-replaceable-units"></a>FRU

Il seguente FRU (FRU) sono disponibili per il dispositivo StorSimple.

- Chassis (incluso il pannello di operazioni integrata)

- 764 W AC PCM

- 580 W AC PCM

- Unità disco rigido con modulo vettore unità

- Modulo controller

- Modulo controller EBOD

- Modulo di riserva

- Rack kit rail di montaggio

Informazioni [contattare il supporto Microsoft](storsimple-contact-microsoft-support.md) per acquistare uno di questi unità di sostituzione.

## <a name="next-steps"></a>Passaggi successivi

Rivedere tutte le [informazioni sulla sicurezza](storsimple-safety.md) prima di tentare di sostituire un componente hardware StorSimple.
