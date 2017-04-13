<properties 
    pageTitle="Indicatori di monitoraggio StorSimple | Microsoft Azure" 
    description="Vengono descritti i light – emissione diodi (LED) e gli avvisi acustici utilizzati per controllare lo stato del dispositivo StorSimple."
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
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Consente di gestire il dispositivo StorSimple indicatori di monitoraggio   

## <a name="overview"></a>Panoramica

Il dispositivo StorSimple include light – emissione diodi (LED) e generato l'avviso che è possibile utilizzare per monitorare lo stato generale del dispositivo StorSimple e il moduli. Gli indicatori di monitoraggio sono disponibili in componenti hardware del simbolo principale del dispositivo e il simbolo EBOD. Gli indicatori di monitoraggio possono essere LED o gli avvisi acustici.

Sono disponibili tre stati LED utilizzati per indicare lo stato di un modulo: verde lampeggiante verde giallo rosso o giallo rosso.  

- LED verde rappresentano uno stato operativo integro.  
- Lampeggiante verde per rappresentare LED giallo rosso la presenza di condizioni non critici che può implicare anche da parte dell'utente.  
- LED giallo rosso indicano la presenza di un errore critico presenta all'interno del modulo.  

Il resto di questo articolo vengono illustrati i vari monitoraggio LED, posizioni sul dispositivo StorSimple, lo stato dispositivo è in base a stati LED e qualsiasi avvisi acustici associati.

## <a name="front-panel-indicator-leds"></a>LED del pannello anteriore

Pannello anteriore, noto anche come il *Pannello di operazioni* o *Pannello operazioni*, verrà visualizzato lo stato di aggregato di tutti i moduli nel sistema. Pannello anteriore è identico su StorSimple principale e il simbolo EBOD e viene riportato di seguito.  

   ![Pannello anteriore dispositivo][1]
 
Pannello anteriore contiene indicatori seguenti:  

1. Pulsante Disattiva
2. Indicatore di alimentazione LED (rosso-LED)
3. Indicatore di errore modulo è (in rosso-giallo/OFF)
4. Indicatore di errore logico è (in rosso-giallo /
5. Visualizzazione di ID unità  

La differenza tra il pannello anteriore LED del dispositivo e quelle per il simbolo EBOD è il **Numero di identificazione di unità di sistema** visualizzata sul display a LED. L'ID di unità predefinito visualizzato nel dispositivo è **00**, mentre l'ID di unità predefinita visualizzata sul simbolo EBOD è **01**. In questo modo si distinguere velocemente il dispositivo e il simbolo EBOD quando il dispositivo è attivato. Se il dispositivo è disattivato, usare le informazioni fornite in [attivare un nuovo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) per distinguere tra il dispositivo dal simbolo EBOD.  

## <a name="front-panel-led-status"></a>Stato del LED del pannello anteriore  

Utilizzare la tabella seguente per identificare lo stato contrassegnato con LED sul pannello anteriore per il dispositivo o il simbolo EBOD.  

|Alimentazione del sistema | Errore di modulo | Errore logico | Avviso | Stato|
|-------------|---------------|-----------------|-------|-------|
|Giallo rosso | DISATTIVARE LA FUNZIONE     | DISATTIVARE LA FUNZIONE | N/D | Alimentazione perdita, che viene applicato in alimentazione di backup o alimentazione e controller moduli sono stati rimossi.|
|Verde | VIA | VIA | N/D | Stato test power pannello operazioni su (5s)|
|Verde | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | N/D | Accendere, tutte le funzioni ottimale|
|Verde | VIA |N/D | Errore PCM LED, guasto ventola LED | Qualsiasi guasto PCM, guasto ventola, sopra o sotto temperatura|
| Verde | VIA | N/D | Modulo dei / o LED  | Qualsiasi errore modulo|
| Verde | VIA | N/D | N/D | Errore di logica di simbolo|
| Verde | Flash | N/D | Stato del modulo LED nel modulo controller. Errore PCM LED, guasto ventola LED | Tipo di modulo controller sconosciuto installato, I2C bus di un errore controller modulo prodotto fondamentale dati (VPD) configurazione |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power raffreddamento LED di modulo (PCM)   

Potenza raffreddamento modulo (PCM) LED sono disponibili nella parte posteriore del simbolo principale o del simbolo EBOD su ciascun modulo PCM. In questo argomento viene illustrato come utilizzare i LED seguenti per controllare lo stato del dispositivo StorSimple.  

- LED PCM per il simbolo principale
- LED PCM per il simbolo EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>LED PCM per il simbolo principale  

Il dispositivo StorSimple dispone di un modulo PCM 764W con un Wh. La figura seguente mostra il pannello LED del dispositivo.  

   ![LED PCM sulla simbolo principale][2]

Legenda LED:

1. Alimentazione AC
2. Errore ventola
3. Guasto batteria
4. OK PCM
5. Errore di controller di dominio
6. Batteria ottima  

Lo stato di PCM è indicato nel riquadro LED. Il pannello di LED PCM dispositivo presenta sei LED. Quattro di questi LED visualizzare lo stato di alimentazione e la ventola. Due LED rimanente indicano lo stato del modulo di riserva in PCM. È possibile utilizzare le tabelle seguenti per determinare lo stato di PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indicatore PCM LED di alimentazione e ventola
| Stato | PCM OK (verde) | Fail AC (giallo) | Fail ventola (giallo) | Fail controller di dominio (giallo) |
|--------|----------------|-----------------------|------------------|----------------------|
| Nessuna alimentazione (a simbolo) | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE|
| Nessuna alimentazione (solo questo PCM) | DISATTIVARE LA FUNZIONE | VIA | DISATTIVARE LA FUNZIONE | VIA |
| AC presentare via PCM - OK     | VIA | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Fail PCM (ventola fail) | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | VIA | N/D |
| Errore PCM (sopra amp su tensione su corrente) | DISATTIVARE LA FUNZIONE | VIA | VIA | VIA |
| PCM (ventola fuori tolleranza) | VIA | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | VIA |
| Modalità di standby | Lampeggiante | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Download del firmware PCM | DISATTIVARE LA FUNZIONE | Lampeggiante | Lampeggiante | Lampeggiante |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM LED per la riserva  

| Stato | Buona batteria (verde) | Guasto batteria (giallo) |
|--------|----------------------|-----------------------|
| Batteria non è presenta | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Batteria presenta e carica | VIA | DISATTIVARE LA FUNZIONE |
| Svuotamento in carica o manutenzione batteria | Lampeggiante | DISATTIVARE LA FUNZIONE |
| Errore "software" batteria (recuperati) | DISATTIVARE LA FUNZIONE | Lampeggiante |
| Errore "hardware" batteria (non è reversibile) | DISATTIVARE LA FUNZIONE | VIA |
| Batteria disarmed | Lampeggiante | DISATTIVARE LA FUNZIONE |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>LED PCM per il simbolo EBOD  

Simbolo EBOD un PCM 580W e non Wh. Il riquadro PCM per il simbolo EBOD è LED solo per l'alimentazione e la ventola. L'illustrazione seguente mostra questi LED.

   ![LED PCM sulla simbolo EBOD][3] 
 
È possibile utilizzare la tabella seguente per determinare lo stato di PCM.  

| Stato | PCM OK (verde) | Fail AC (giallo) | Fail ventola (giallo) | Fail controller di dominio (giallo) |
|--------|---------------|------------------------|------------------|----------------------|
| Nessuna alimentazione (a simbolo) | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Nessuna alimentazione (solo questo PCM) | DISATTIVARE LA FUNZIONE | VIA | DISATTIVARE LA FUNZIONE | VIA |
| AC presentare via PCM-OK | VIA | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Fail PCM (ventola fail) | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | VIA | X |
| Errore PCM (sopra amp su tensione su corrente | DISATTIVARE LA FUNZIONE | VIA | VIA | VIA |
| PCM (ventola fuori tolleranza) | VIA | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | VIA |
| Modello standby | Lampeggiante | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE |
| Download del firmware PCM | DISATTIVARE LA FUNZIONE | Lampeggiante | Lampeggiante | Lampeggiante |

## <a name="controller-module-indicator-leds"></a>Controller modulo LED  

Il dispositivo StorSimple contiene LED per i moduli controller EBOD e controller primario.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitoraggio LED del controller principale
La figura seguente consente di identificare i LED sul controller primario. (Tutti i componenti sono elencati per facilitare l'orientamento.)  

   ![Monitoraggio LED - controller primario][4]
 
Utilizzare la tabella seguente per determinare se il modulo controller funziona correttamente.  

### <a name="controller-indicator-leds"></a>Indicatore di controller LED  

| LED | Descrizione                                                                            
|---- | ----------- |
| ID LED (blu) | Indica il modulo identificato. Se è lampeggiare LED blu su un controller in esecuzione, il controller è l'attivo e l'altro è un controller di standby. Per ulteriori informazioni, vedere [identificare controller attivo nel dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Errore LED (giallo) | Indica un errore nel controllo.        
| OK LED (verde) | Luce verde indica che il controller sia OK. Lampeggiante verde indica un errore di configurazione VPD controller. |
| Attività SA LED (verde) | Luce verde indica una connessione con alcuna attività corrente. Lampeggiante verde indica che la connessione è un'attività in corso. |
| Stato Ethernet LED | Sul lato destro indicato attività di collegamento/rete: collegamento (verde costante) attiva (lampeggiante verde) attività di rete. Sul lato sinistro indica la velocità della rete: (schema giallo) 1000 Mb/s, 100 Mb/s (verde) e () 10 Mb/s. A seconda del modello componente questa luce potrebbe lampeggiare anche se l'interfaccia di rete non è attivato. |
| LED POST | Indica lo stato di avanzamento di avvio quando il controller è attivato. Se il dispositivo StorSimple ha esito negativo per l'avvio, questo LED aiuta supporto Microsoft a identificare il punto nel processo di avvio dell'errore. |

>[AZURE.IMPORTANT] 
Se l'errore LED è acceso, si verifica un problema con il modulo controller che potrebbe essere risolto riavviando il controller. Se il riavvio del controller non consente di risolvere il problema, contattare il supporto Microsoft.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitoraggio LED per EBOD (simbolo EBOD)  

Ognuno dei controller Gb/s SAS EBOD 6 è LED che indicano lo stato, come illustrato nella figura seguente.  

  ![Monitoraggio LED - simbolo EBOD][5]

Utilizzare la tabella seguente per determinare se il modulo controller EBOD funziona normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>EBOD controller modulo LED  

|Stato | Modulo dei / o OK (verde) | Errore di modulo i/o (giallo) | Attività delle porte host (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Modulo controller OK | VIA | DISATTIVARE LA FUNZIONE | - |
| Errore modulo | DISATTIVARE LA FUNZIONE | VIA | - |
| Nessuna connessione alla porta host esterno | - | - | DISATTIVARE LA FUNZIONE |
| Collegamento alla porta host esterno: nessuna attività | - | - | VIA |
| Connessione alla porta host esterno - attività | - | - | Lampeggiante |
| Errore di metadati del modulo di controller | Lampeggiante | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Unità disco LED per il simbolo principale e simbolo EBOD

Il dispositivo StorSimple include unità disco in simbolo principale e il simbolo EBOD. Ogni unità disco contiene monitoraggio LED, come descritto in questa sezione. 

Per le unità disco, lo stato di unità è indicato in un verde LED e un LED giallo rosso installati sul lato anteriore di ogni modulo vettore unità. L'illustrazione seguente mostra questi LED.

  ![Unità disco LED][6]
 
Utilizzare la tabella seguente per determinare lo stato di ogni unità disco, che a sua volta influisce sul pannello anteriore complessivo LED stato.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Unità disco LED per il simbolo EBOD  

| Stato | Attività OK LED (verde) | Errore LED (giallo rosso) | Associato LED sul pannello operazioni |
|-------|--------------------------|----------------------|-------------------------|
| Nessuna unità installato | DISATTIVARE LA FUNZIONE | DISATTIVARE LA FUNZIONE | Nessuno |
| Unità installata e attivata | Attivazione/disattivazione lampeggiante con un'attività | X | Nessuno |
| Set di identità di dispositivo SCSI simbolo Services (se) | VIA | Lampeggiante 1 secondo su 1/secondo disattivato | Nessuno |
| Se dispositivo guasto bit set | VIA | VIA | Errore logico (rosso) |
| Errore di circuito controllo alimentazione | DISATTIVARE LA FUNZIONE | VIA | Errore di modulo (rosso) |

## <a name="audible-alarms"></a>Avvisi acustici  

Un dispositivo StorSimple contiene gli avvisi acustici associati simbolo principale e il simbolo EBOD. Un segnale acustico è disponibile nel pannello anteriore (noto anche come nel Pannello di operazioni) di entrambi allegati. Avviso acustico indica quando è presenta una condizione di errore. Le seguenti condizioni attiverà all'avviso:  

- Errore di ventola o errore
- Tensione compresa nell'intervallo
- Sopra o sotto condizione di temperatura
- Sovraccarico termica
- Errore di sistema
- Errore logico
- Errore alimentazione
- Rimozione di una potenza raffreddamento modulo (PCM)  

Nella tabella seguente descrive i vari stati di avviso.  

### <a name="alarm-states"></a>Stati di avviso  

| Stato di avviso | Azione | Azione con la pressione del pulsante Disattiva |
|------------|---------|---------------------------------|
| S0 | Modalità normale: invisibile | Segnale acustico due volte |
| S1 | Modalità di errore: 1 secondo su 1/secondo disattivato | Transizione S2 o S3 (vedere note) |
| S2 | Ricordare modalità: segnale acustico intermittente | Nessuno |
| S3 | Modalità disattivata: invisibile | Nessuno |
| S4 | Modalità di errore critico: avviso continuo | Non è disponibile: disattiva non attivo |

> [AZURE.NOTE] 

>  - In stato di avviso S1, se non si preme riattivare all'interno di 2 minuti, lo stato passa automaticamente a S2 o S3.  
>  - Avviso stati S1 S4 tornare a S0 dopo la condizione di errore sia deselezionata.  
>  - Stato di errore critico S4 può essere immesse da un altro stato.  

Si possono disattivare l'audio di allarmi acustici facendo clic sul pulsante Disattiva nel riquadro di operazioni. La disattivazione dell'audio automatico verificherà dopo due minuti se l'opzione Disattiva non è gestito manualmente. Quando l'avviso è disattivato, continuerà a suono con intermittente segnali acustici per indicare che un problema persiste. L'avviso sarà invisibile all'utente quando tutti i problemi siano deselezionati.  

Nella tabella seguente descrive le varie condizioni di avviso.  

### <a name="alarm-conditions"></a>Condizione di avviso  

| Stato | Gravità | Avviso | Operazioni pannello LED |
|--------|---------|--------|----------------|
| Avviso PCM-perdita di alimentazione CC da una singola PCM | Errore: senza perdere la ridondanza | S1 | Errore di modulo|
|Avviso PCM-perdita di alimentazione CC da una singola PCM | Errore: perdita di ridondanza | S1 | Errore di modulo |
| Fail ventola PCM | Errore: perdita di ridondanza | S1 | Errore di modulo |
| SBB rilevato modulo PCM guasto | Guasto | S1 | Errore di modulo |
| PCM rimosso | Errore di configurazione | Nessuno | Errore di modulo |
| Errore di configurazione di simbolo | Errore: critico | S1 | Errore di modulo |
| Avviso di temperatura bassa | Avviso | S1 | Errore di modulo |
| Avviso di temperature elevate | Avviso | S1 | Errore di modulo |
| Su avviso temperatura | Errore: critico | S1 | Errore di modulo |
| Errore bus I2C | Errore: perdita di ridondanza | S1 | Errore di modulo |
| Operazioni pannello errore di comunicazione (I2C) | Errore: critico     | S1 | Errore di modulo |
| Errore del controller | Errore: critico | S1 | Errore di modulo |
| Errore di modulo interfaccia SBB | Errore: critico | S1 | Errore di modulo |
| Errore di modulo interfaccia SBB – non funzionanti moduli rimanente | Errore: critico | S4 | Errore di modulo |
| Modulo di interfaccia SBB rimosso | Avviso | Nessuno | Errore di modulo |
| Unità power controllo guasto | Avviso-senza perdita di power unità | S1 | Errore di modulo |
| Unità power controllo guasto | Errore: critico; interruzioni di corrente di unità | S1 | Errore di modulo |
| Unità rimossa | Avviso | Nessuno | Errore di modulo |
| Alimentazione insufficiente | Avviso | Nessuno | Errore di modulo |

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni [sullo stato e componenti hardware StorSimple](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
