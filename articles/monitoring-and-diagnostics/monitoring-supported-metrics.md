<properties
    pageTitle="Azure metriche Monitor - metriche supportate per ogni tipo di risorsa | Microsoft Azure"
    description="Elenco dei parametri disponibili per ogni tipo di risorsa con Azure Monitor."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Metrica supportata con Azure Monitor

Monitor Azure offre diversi modi per interagire con metriche, ad esempio creazione di grafici nel portale, ad accedervi tramite l'API REST o query loro tramite PowerShell o CLI. Di seguito è attualmente disponibile con pipeline metrica del Monitor Azure un elenco completo di tutte le metriche.

> [AZURE.NOTE] Altre metriche siano disponibili nel portale di oppure utilizzando API legacy. L'elenco include solo metriche anteprima pubblica disponibili utilizzando l'anteprima della pipeline metrica Azure Monitor consolidata pubblico.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CoreCount|Conteggio di base|Conta. numeri|Totale|Numero totale di core nell'account batch|
|TotalNodeCount|Conteggio dei nodi|Conta. numeri|Totale|Numero totale di nodi nella finestra account batch|
|CreatingNodeCount|Creazione di conteggio dei nodi|Conta. numeri|Totale|Numero dei nodi da creare|
|StartingNodeCount|Conteggio dei nodi iniziale|Conta. numeri|Totale|Numero dei nodi avvio|
|WaitingForStartTaskNodeCount|In attesa di conteggio dei nodi per inizio delle attività|Conta. numeri|Totale|Numero dei nodi attendere il completamento dell'attività iniziare|
|StartTaskFailedNodeCount|Inizio non è riuscito conteggio dei nodi|Conta. numeri|Totale|Numero dei nodi in cui non è riuscita Inizia attività|
|IdleNodeCount|Conteggio dei nodi inattive|Conta. numeri|Totale|Numero dei nodi inattivi|
|OfflineNodeCount|Conteggio dei nodi in modalità offline|Conta. numeri|Totale|Numero dei nodi non in linea|
|RebootingNodeCount|Il riavvio di conteggio dei nodi|Conta. numeri|Totale|Numero di il riavvio nodi|
|ReimagingNodeCount|Ricostruzione conteggio dei nodi|Conta. numeri|Totale|Numero dei nodi implicavano|
|RunningNodeCount|Conteggio dei nodi in esecuzione|Conta. numeri|Totale|Numero dei nodi funzionanti|
|LeavingPoolNodeCount|Uscire da conteggio dei nodi in Pool|Conta. numeri|Totale|Numero dei nodi lasciando il Pool|
|UnusableNodeCount|Conteggio dei nodi inutilizzabile|Conta. numeri|Totale|Numero dei nodi inutilizzabili|
|TaskStartEvent|Eventi di avvio di attività|Conta. numeri|Totale|Numero totale di attività che ha avviato|
|TaskCompleteEvent|Eventi di completamento delle attività|Conta. numeri|Totale|Numero totale di attività completate|
|TaskFailEvent|Attività Fail eventi|Conta. numeri|Totale|Numero totale di attività completate in uno stato non riuscito|
|PoolCreateEvent|Pool di creare eventi|Conta. numeri|Totale|Numero totale di pool che sono stati creati|
|PoolResizeStartEvent|Eventi di avvio di ridimensionamento del pool|Conta. numeri|Totale|Numero totale di Ridimensiona pool che ha avviato|
|PoolResizeCompleteEvent|Eventi completo del pool di ridimensionamento|Conta. numeri|Totale|Numero totale di Ridimensiona pool che sono state completate|
|PoolDeleteStartEvent|Eventi di avvio Elimina pool|Conta. numeri|Totale|Numero totale di eliminazioni pool che ha avviato|
|PoolDeleteCompleteEvent|Eventi del pool di completamento Elimina|Conta. numeri|Totale|Numero totale di eliminazioni pool che sono state completate|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|connectedclients|Client connessi|Conta. numeri|Massimo||
|totalcommandsprocessed|Operazioni totali|Conta. numeri|Totale||
|cachehits|Accessi|Conta. numeri|Totale||
|cachemisses|Errori cache|Conta. numeri|Totale||
|GetCommands|Ottiene|Conta. numeri|Totale||
|setcommands|Set|Conta. numeri|Totale||
|evictedkeys|Chiavi eliminate|Conta. numeri|Totale||
|totalkeys|Totale chiavi|Conta. numeri|Massimo||
|expiredkeys|Chiavi scadute|Conta. numeri|Totale||
|usedmemory|Memoria utilizzata|Byte|Massimo||
|usedmemoryRss|Memoria utilizzata RSS|Byte|Massimo||
|serverLoad|Carico del server|Percentuale|Massimo||
|cacheWrite|Scrittura di cache|BytesPerSecond|Massimo||
|cacheRead|Lettura di cache|BytesPerSecond|Massimo||
|percentProcessorTime|CPU|Percentuale|Massimo||
|connectedclients0|Client connessi (condiviso 0)|Conta. numeri|Massimo||
|totalcommandsprocessed0|Totale operazioni (condiviso 0)|Conta. numeri|Totale||
|cachehits0|Accessi (condiviso 0)|Conta. numeri|Totale||
|cachemisses0|Errori cache (condiviso 0)|Conta. numeri|Totale||
|getcommands0|Ottiene (condiviso 0)|Conta. numeri|Totale||
|setcommands0|Set (condiviso 0)|Conta. numeri|Totale||
|evictedkeys0|Chiavi eliminate (condiviso 0)|Conta. numeri|Totale||
|totalkeys0|Totale chiavi (nodo 0)|Conta. numeri|Massimo||
|expiredkeys0|Chiavi scadute (condiviso 0)|Conta. numeri|Totale||
|usedmemory0|Memoria utilizzata (condiviso 0)|Byte|Massimo||
|usedmemoryRss0|Memoria utilizzata RSS (condiviso 0)|Byte|Massimo||
|serverLoad0|Carico del server (condiviso 0)|Percentuale|Massimo||
|cacheWrite0|Scrittura di cache (condiviso 0)|BytesPerSecond|Massimo||
|cacheRead0|Lettura di cache (condiviso 0)|BytesPerSecond|Massimo||
|percentProcessorTime0|CPU (condiviso 0)|Percentuale|Massimo||
|connectedclients1|Client connessi (condiviso 1)|Conta. numeri|Massimo||
|totalcommandsprocessed1|Totale operazioni (condiviso 1)|Conta. numeri|Totale||
|cachehits1|Accessi (condiviso 1)|Conta. numeri|Totale||
|cachemisses1|Errori cache (condiviso 1)|Conta. numeri|Totale||
|getcommands1|Ottiene (condiviso 1)|Conta. numeri|Totale||
|setcommands1|Set (condiviso 1)|Conta. numeri|Totale||
|evictedkeys1|Chiavi eliminate (condiviso 1)|Conta. numeri|Totale||
|totalkeys1|Totale chiavi (nodo 1)|Conta. numeri|Massimo||
|expiredkeys1|Chiavi scadute (condiviso 1)|Conta. numeri|Totale||
|usedmemory1|Memoria utilizzata (condiviso 1)|Byte|Massimo||
|usedmemoryRss1|Memoria utilizzata RSS (condiviso 1)|Byte|Massimo||
|serverLoad1|Carico del server (condiviso 1)|Percentuale|Massimo||
|cacheWrite1|Scrittura di cache (condiviso 1)|BytesPerSecond|Massimo||
|cacheRead1|Lettura di cache (condiviso 1)|BytesPerSecond|Massimo||
|percentProcessorTime1|CPU (condiviso 1)|Percentuale|Massimo||
|connectedclients2|Client connessi (condiviso 2)|Conta. numeri|Massimo||
|totalcommandsprocessed2|Totale operazioni (condiviso 2)|Conta. numeri|Totale||
|cachehits2|Accessi (condiviso 2)|Conta. numeri|Totale||
|cachemisses2|Errori cache (condiviso 2)|Conta. numeri|Totale||
|getcommands2|Ottiene (condiviso 2)|Conta. numeri|Totale||
|setcommands2|Set (condiviso 2)|Conta. numeri|Totale||
|evictedkeys2|Chiavi eliminate (condiviso 2)|Conta. numeri|Totale||
|totalkeys2|Totale chiavi (nodo 2)|Conta. numeri|Massimo||
|expiredkeys2|Chiavi scadute (condiviso 2)|Conta. numeri|Totale||
|usedmemory2|Memoria utilizzata (condiviso 2)|Byte|Massimo||
|usedmemoryRss2|Memoria utilizzata RSS (condiviso 2)|Byte|Massimo||
|serverLoad2|Carico del server (condiviso 2)|Percentuale|Massimo||
|cacheWrite2|Scrittura di cache (condiviso 2)|BytesPerSecond|Massimo||
|cacheRead2|Lettura di cache (condiviso 2)|BytesPerSecond|Massimo||
|percentProcessorTime2|CPU (condiviso 2)|Percentuale|Massimo||
|connectedclients3|Client connessi (condiviso 3)|Conta. numeri|Massimo||
|totalcommandsprocessed3|Totale operazioni (condiviso 3)|Conta. numeri|Totale||
|cachehits3|Accessi (condiviso 3)|Conta. numeri|Totale||
|cachemisses3|Errori cache (condiviso 3)|Conta. numeri|Totale||
|getcommands3|Ottiene (condiviso 3)|Conta. numeri|Totale||
|setcommands3|Set (condiviso 3)|Conta. numeri|Totale||
|evictedkeys3|Chiavi eliminate (condiviso 3)|Conta. numeri|Totale||
|totalkeys3|Totale chiavi (nodo 3)|Conta. numeri|Massimo||
|expiredkeys3|Chiavi scadute (condiviso 3)|Conta. numeri|Totale||
|usedmemory3|Memoria utilizzata (condiviso 3)|Byte|Massimo||
|usedmemoryRss3|Memoria utilizzata RSS (condiviso 3)|Byte|Massimo||
|serverLoad3|Carico del server (condiviso 3)|Percentuale|Massimo||
|cacheWrite3|Scrittura di cache (condiviso 3)|BytesPerSecond|Massimo||
|cacheRead3|Lettura di cache (condiviso 3)|BytesPerSecond|Massimo||
|percentProcessorTime3|CPU (condiviso 3)|Percentuale|Massimo||
|connectedclients4|Client connessi (condiviso 4)|Conta. numeri|Massimo||
|totalcommandsprocessed4|Totale operazioni (condiviso 4)|Conta. numeri|Totale||
|cachehits4|Accessi (condiviso 4)|Conta. numeri|Totale||
|cachemisses4|Errori cache (condiviso 4)|Conta. numeri|Totale||
|getcommands4|Ottiene (condiviso 4)|Conta. numeri|Totale||
|setcommands4|Set (condiviso 4)|Conta. numeri|Totale||
|evictedkeys4|Chiavi eliminate (condiviso 4)|Conta. numeri|Totale||
|totalkeys4|Totale chiavi (nodo 4)|Conta. numeri|Massimo||
|expiredkeys4|Chiavi scadute (condiviso 4)|Conta. numeri|Totale||
|usedmemory4|Memoria utilizzata (condiviso 4)|Byte|Massimo||
|usedmemoryRss4|Memoria utilizzata RSS (condiviso 4)|Byte|Massimo||
|serverLoad4|Carico del server (condiviso 4)|Percentuale|Massimo||
|cacheWrite4|Scrittura di cache (condiviso 4)|BytesPerSecond|Massimo||
|cacheRead4|Lettura di cache (condiviso 4)|BytesPerSecond|Massimo||
|percentProcessorTime4|CPU (condiviso 4)|Percentuale|Massimo||
|connectedclients5|Client connessi (condiviso 5)|Conta. numeri|Massimo||
|totalcommandsprocessed5|Totale operazioni (condiviso 5)|Conta. numeri|Totale||
|cachehits5|Accessi (condiviso 5)|Conta. numeri|Totale||
|cachemisses5|Errori cache (condiviso 5)|Conta. numeri|Totale||
|getcommands5|Ottiene (condiviso 5)|Conta. numeri|Totale||
|setcommands5|Set (condiviso 5)|Conta. numeri|Totale||
|evictedkeys5|Chiavi eliminate (condiviso 5)|Conta. numeri|Totale||
|totalkeys5|Totale chiavi (nodo 5)|Conta. numeri|Massimo||
|expiredkeys5|Chiavi scadute (condiviso 5)|Conta. numeri|Totale||
|usedmemory5|Memoria utilizzata (condiviso 5)|Byte|Massimo||
|usedmemoryRss5|Memoria utilizzata RSS (condiviso 5)|Byte|Massimo||
|serverLoad5|Carico del server (condiviso 5)|Percentuale|Massimo||
|cacheWrite5|Scrittura di cache (condiviso 5)|BytesPerSecond|Massimo||
|cacheRead5|Lettura di cache (condiviso 5)|BytesPerSecond|Massimo||
|percentProcessorTime5|CPU (condiviso 5)|Percentuale|Massimo||
|connectedclients6|Client connessi (condiviso 6)|Conta. numeri|Massimo||
|totalcommandsprocessed6|Totale operazioni (condiviso 6)|Conta. numeri|Totale||
|cachehits6|Accessi (condiviso 6)|Conta. numeri|Totale||
|cachemisses6|Errori cache (condiviso 6)|Conta. numeri|Totale||
|getcommands6|Ottiene (condiviso 6)|Conta. numeri|Totale||
|setcommands6|Set (condiviso 6)|Conta. numeri|Totale||
|evictedkeys6|Chiavi eliminate (condiviso 6)|Conta. numeri|Totale||
|totalkeys6|Totale chiavi (nodo 6)|Conta. numeri|Massimo||
|expiredkeys6|Chiavi scadute (condiviso 6)|Conta. numeri|Totale||
|usedmemory6|Memoria utilizzata (condiviso 6)|Byte|Massimo||
|usedmemoryRss6|Memoria utilizzata RSS (condiviso 6)|Byte|Massimo||
|serverLoad6|Carico del server (condiviso 6)|Percentuale|Massimo||
|cacheWrite6|Scrittura di cache (condiviso 6)|BytesPerSecond|Massimo||
|cacheRead6|Lettura di cache (condiviso 6)|BytesPerSecond|Massimo||
|percentProcessorTime6|CPU (condiviso 6)|Percentuale|Massimo||
|connectedclients7|Client connessi (condiviso 7)|Conta. numeri|Massimo||
|totalcommandsprocessed7|Totale operazioni (condiviso 7)|Conta. numeri|Totale||
|cachehits7|Accessi (condiviso 7)|Conta. numeri|Totale||
|cachemisses7|Errori cache (condiviso 7)|Conta. numeri|Totale||
|getcommands7|Ottiene (condiviso 7)|Conta. numeri|Totale||
|setcommands7|Set (condiviso 7)|Conta. numeri|Totale||
|evictedkeys7|Chiavi eliminate (condiviso 7)|Conta. numeri|Totale||
|totalkeys7|Totale chiavi (nodo 7)|Conta. numeri|Massimo||
|expiredkeys7|Chiavi scadute (condiviso 7)|Conta. numeri|Totale||
|usedmemory7|Memoria utilizzata (condiviso 7)|Byte|Massimo||
|usedmemoryRss7|Memoria utilizzata RSS (condiviso 7)|Byte|Massimo||
|serverLoad7|Carico del server (condiviso 7)|Percentuale|Massimo||
|cacheWrite7|Scrittura di cache (condiviso 7)|BytesPerSecond|Massimo||
|cacheRead7|Lettura di cache (condiviso 7)|BytesPerSecond|Massimo||
|percentProcessorTime7|CPU (condiviso 7)|Percentuale|Massimo||
|connectedclients8|Client connessi (condiviso 8)|Conta. numeri|Massimo||
|totalcommandsprocessed8|Totale operazioni (condiviso 8)|Conta. numeri|Totale||
|cachehits8|Accessi (condiviso 8)|Conta. numeri|Totale||
|cachemisses8|Errori cache (condiviso 8)|Conta. numeri|Totale||
|getcommands8|Ottiene (condiviso 8)|Conta. numeri|Totale||
|setcommands8|Set (condiviso 8)|Conta. numeri|Totale||
|evictedkeys8|Chiavi eliminate (condiviso 8)|Conta. numeri|Totale||
|totalkeys8|Totale chiavi (nodo 8)|Conta. numeri|Massimo||
|expiredkeys8|Chiavi scadute (condiviso 8)|Conta. numeri|Totale||
|usedmemory8|Memoria utilizzata (condiviso 8)|Byte|Massimo||
|usedmemoryRss8|Memoria utilizzata RSS (condiviso 8)|Byte|Massimo||
|serverLoad8|Carico del server (condiviso 8)|Percentuale|Massimo||
|cacheWrite8|Scrittura di cache (condiviso 8)|BytesPerSecond|Massimo||
|cacheRead8|Lettura di cache (condiviso 8)|BytesPerSecond|Massimo||
|percentProcessorTime8|CPU (condiviso 8)|Percentuale|Massimo||
|connectedclients9|Client connessi (condiviso 9)|Conta. numeri|Massimo||
|totalcommandsprocessed9|Totale operazioni (condiviso 9)|Conta. numeri|Totale||
|cachehits9|Accessi (condiviso 9)|Conta. numeri|Totale||
|cachemisses9|Errori cache (condiviso 9)|Conta. numeri|Totale||
|getcommands9|Ottiene (condiviso 9)|Conta. numeri|Totale||
|setcommands9|Set (condiviso 9)|Conta. numeri|Totale||
|evictedkeys9|Chiavi eliminate (condiviso 9)|Conta. numeri|Totale||
|totalkeys9|Totale chiavi (nodo 9)|Conta. numeri|Massimo||
|expiredkeys9|Chiavi scadute (condiviso 9)|Conta. numeri|Totale||
|usedmemory9|Memoria utilizzata (condiviso 9)|Byte|Massimo||
|usedmemoryRss9|Memoria utilizzata RSS (condiviso 9)|Byte|Massimo||
|serverLoad9|Carico del server (condiviso 9)|Percentuale|Massimo||
|cacheWrite9|Scrittura di cache (condiviso 9)|BytesPerSecond|Massimo||
|cacheRead9|Lettura di cache (condiviso 9)|BytesPerSecond|Massimo||
|percentProcessorTime9|CPU (condiviso 9)|Percentuale|Massimo||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|NumberOfCalls|Numero totale di chiamate API|Conta. numeri|Totale|Numero totale di chiamate API.|
|NumberOfFailedCalls|Numero totale di chiamate API non riuscite|Conta. numeri|Totale|Numero totale di chiamate API non riuscite.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Percentuale di CPU|Percentuale di CPU|Percentuale|Media|La percentuale di unità di elaborazione allocato attualmente in uso per le macchine virtuali|
|Rete In|Rete In|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete per la macchine virtuali (traffico in arrivo)|
|Fuori rete|Fuori rete|Byte|Totale|Il numero di byte in uscita su tutte le interfacce di rete per la macchine virtuali (traffico in uscita)|
|Byte letti disco|Byte letti disco|Byte|Totale|Totale byte letti da disco durante il periodo di monitoraggio|
|Byte di scrittura su disco|Byte di scrittura su disco|Byte|Totale|Numero totale di byte scritti su disco durante il periodo di monitoraggio|
|Disco operazioni di lettura/Sec|Disco operazioni di lettura/Sec|CountPerSecond|Media|Lettura disco IOPS|
|Operazioni di scrittura su disco al secondo|Operazioni di scrittura su disco al secondo|CountPerSecond|Media|Scrittura su disco IOPS|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Percentuale di CPU|Percentuale di CPU|Percentuale|Media|La percentuale di unità di elaborazione allocato attualmente in uso per le macchine virtuali|
|Rete In|Rete In|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete per la macchine virtuali (traffico in arrivo)|
|Fuori rete|Fuori rete|Byte|Totale|Il numero di byte in uscita su tutte le interfacce di rete per la macchine virtuali (traffico in uscita)|
|Byte letti disco|Byte letti disco|Byte|Totale|Totale byte letti da disco durante il periodo di monitoraggio|
|Byte di scrittura su disco|Byte di scrittura su disco|Byte|Totale|Numero totale di byte scritti su disco durante il periodo di monitoraggio|
|Disco operazioni di lettura/Sec|Disco operazioni di lettura/Sec|CountPerSecond|Media|Lettura disco IOPS|
|Operazioni di scrittura su disco al secondo|Operazioni di scrittura su disco al secondo|CountPerSecond|Media|Scrittura su disco IOPS|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Percentuale di CPU|Percentuale di CPU|Percentuale|Media|La percentuale di unità di elaborazione allocato attualmente in uso per le macchine virtuali|
|Rete In|Rete In|Byte|Totale|Il numero di byte ricevuti su tutte le interfacce di rete per la macchine virtuali (traffico in arrivo)|
|Fuori rete|Fuori rete|Byte|Totale|Il numero di byte in uscita su tutte le interfacce di rete per la macchine virtuali (traffico in uscita)|
|Byte letti disco|Byte letti disco|Byte|Totale|Totale byte letti da disco durante il periodo di monitoraggio|
|Byte di scrittura su disco|Byte di scrittura su disco|Byte|Totale|Numero totale di byte scritti su disco durante il periodo di monitoraggio|
|Disco operazioni di lettura/Sec|Disco operazioni di lettura/Sec|CountPerSecond|Media|Lettura disco IOPS|
|Operazioni di scrittura su disco al secondo|Operazioni di scrittura su disco al secondo|CountPerSecond|Media|Scrittura su disco IOPS|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Tentativi di invio di messaggi di telemetria|Conta. numeri|Totale|Numero di dispositivo nel Cloud i messaggi di telemetria tentativi di invio per il tuo fulcro IoT|
|d2c.telemetry.Ingress.Success|Messaggi di telemetria inviati|Conta. numeri|Totale|Numero di dispositivo ai messaggi di telemetria Cloud inviate all'hub IoT|
|c2d.Commands.egress.complete.Success|Comandi completati|Conta. numeri|Totale|Numero di Cloud ai comandi di dispositivo è stato completato correttamente dal dispositivo|
|c2d.Commands.egress.Abandon.Success|Comandi chiusa|Conta. numeri|Totale|Numero di Cloud ai comandi di dispositivo abbandonata dal dispositivo|
|c2d.Commands.egress.Reject.Success|Comandi rifiutati|Conta. numeri|Totale|Numero di Cloud ai comandi di dispositivo rifiutato dal dispositivo|
|devices.totalDevices|Dispositivi totali|Conta. numeri|Totale|Numero di dispositivi registrati per l'hub IoT|
|devices.connectedDevices.allProtocol|Dispositivi collegati|Conta. numeri|Totale|Numero di dispositivi connessi al tuo fulcro IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|INREQS|Richieste in arrivo|Conta. numeri|Totale|Evento Hub in arrivo messaggio velocità di esecuzione per uno spazio dei nomi|
|SUCCREQ|Richieste completate|Conta. numeri|Totale|Totale richieste esito negativo per uno spazio dei nomi|
|FAILREQ|Richieste non riuscite|Conta. numeri|Totale|Totale richieste non riuscite per uno spazio dei nomi|
|SVRBSY|Server occupato errori|Conta. numeri|Totale|Totale errori del server occupato uno spazio dei nomi|
|INTERR|Errore interno del Server|Conta. numeri|Totale|Totale errore interno del server dei nomi|
|MISCERR|Altri errori|Conta. numeri|Totale|Totale richieste non riuscite per uno spazio dei nomi|
|INMSGS|Messaggi in arrivo|Conta. numeri|Totale|Totali messaggi in arrivo per uno spazio dei nomi|
|OUTMSGS|Messaggi in uscita|Conta. numeri|Totale|Totale messaggi per uno spazio dei nomi in uscita|
|EHINMBS|Byte in ingresso al secondo|BytesPerSecond|Totale|Evento Hub in arrivo messaggio velocità di esecuzione per uno spazio dei nomi|
|EHOUTMBS|In uscita byte al secondo|BytesPerSecond|Totale|Totale messaggi per uno spazio dei nomi in uscita|
|EHABL|Archiviare i messaggi di backlog|Conta. numeri|Totale|Messaggi di archivio Hub di evento in backlog per uno spazio dei nomi|
|EHAMSGS|Archiviare i messaggi|Conta. numeri|Totale|Hub evento archiviare i messaggi in uno spazio dei nomi|
|EHAMBS|Velocità di messaggio archivio|BytesPerSecond|Totale|Evento Hub messaggio archiviato la velocità di trasmissione in uno spazio dei nomi|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|RunsStarted|Viene eseguito avviato|Conta. numeri|Totale|Numero di flusso di lavoro viene eseguito avviato.|
|RunsCompleted|Esecuzione completata|Conta. numeri|Totale|Numero di flusso di lavoro viene eseguito completata.|
|RunsSucceeded|Viene eseguito ha avuto esito positivo|Conta. numeri|Totale|Numero di flusso di lavoro viene eseguito riuscita.|
|RunsFailed|Verrà eseguito non è riuscito|Conta. numeri|Totale|Numero di flusso di lavoro viene eseguito non riuscito.|
|RunsCancelled|Viene eseguito annullata|Conta. numeri|Totale|Numero di flusso di lavoro viene eseguito annullata.|
|RunLatency|Eseguire la latenza|Secondi|Media|Viene eseguita la latenza del flusso di lavoro completato.|
|RunSuccessLatency|Eseguire la latenza di successo|Secondi|Media|Viene eseguita la latenza del flusso di lavoro completato.|
|RunThrottledEvents|Eseguire accelerati eventi|Conta. numeri|Totale|Numero di azioni del flusso di lavoro o trigger limitato eventi.|
|RunFailurePercentage|Eseguire la percentuale di errore|Percentuale|Totale|Percentuale del flusso di lavoro viene eseguito non riuscito.|
|ActionsStarted|Azioni avviate |Conta. numeri|Totale|Numero di azioni del flusso di lavoro avviato.|
|ActionsCompleted|Azioni effettuate |Conta. numeri|Totale|Numero di azioni del flusso di lavoro completato.|
|ActionsSucceeded|Azioni ha avuto esito positivo |Conta. numeri|Totale|Numero di azioni del flusso di lavoro completata.|
|ActionsFailed|Azioni non è riuscite|Conta. numeri|Totale|Numero di azioni del flusso di lavoro non è riuscita.|
|ActionsSkipped|Azioni ignorate |Conta. numeri|Totale|Numero di azioni del flusso di lavoro è stato ignorato.|
|ActionLatency|Latenza di azione |Secondi|Media|Latenza delle azioni di flusso di lavoro completati.|
|ActionSuccessLatency|Latenza di successo di azione |Secondi|Media|Latenza delle azioni del flusso di lavoro completato.|
|ActionThrottledEvents|Azione limitata eventi|Conta. numeri|Totale|Numero di azioni del flusso di lavoro limitato eventi..|
|TriggersStarted|Trigger avviato |Conta. numeri|Totale|Numero di trigger di flusso di lavoro avviato.|
|TriggersCompleted|Trigger completato |Conta. numeri|Totale|Numero di trigger di flusso di lavoro completato.|
|TriggersSucceeded|Trigger ha avuto esito positivo |Conta. numeri|Totale|Numero di trigger di flusso di lavoro completata.|
|TriggersFailed|Trigger non è riuscito |Conta. numeri|Totale|Numero di trigger di flusso di lavoro non è riuscita.|
|TriggersSkipped|Trigger ignorato|Conta. numeri|Totale|Numero di trigger di flusso di lavoro è stato ignorato.|
|TriggersFired|Trigger attivati |Conta. numeri|Totale|Numero di flusso di lavoro trigger generato.|
|TriggerLatency|Latenza trigger |Secondi|Media|Latenza di trigger di flusso di lavoro completati.|
|TriggerFireLatency|Latenza Fire trigger |Secondi|Media|Latenza di generato trigger del flusso di lavoro.|
|TriggerSuccessLatency|Latenza di successo di trigger |Secondi|Media|Latenza di trigger riuscita del flusso di lavoro.|
|TriggerThrottledEvents|Impostare un trigger accelerati eventi|Conta. numeri|Totale|Numero di trigger di flusso di lavoro limitato eventi.|
|BillableActionExecutions|Esecuzioni azione fatturabili|Conta. numeri|Totale|Numero di esecuzioni di azioni del flusso di lavoro visualizzato effettuata.|
|BillableTriggerExecutions|Esecuzioni Trigger fatturabili|Conta. numeri|Totale|Numero di flusso di lavoro trigger esecuzioni Guida effettuata.|
|TotalBillableExecutions|Esecuzioni fatturabili totali|Conta. numeri|Totale|Numero di esecuzioni del flusso di lavoro visualizzato effettuata.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|Velocità di trasmissione|Velocità di trasmissione|BytesPerSecond|Media||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|SearchLatency|Latenza ricerca|Secondi|Media|Latenza media di ricerca per il servizio di ricerca|
|SearchQueriesPerSecond|Query di ricerca al secondo|CountPerSecond|Media|Query di ricerca al secondo per il servizio di ricerca|
|ThrottledSearchQueriesPercentage|Percentuale di query di ricerca accelerata|Percentuale|Media|Percentuale di query di ricerca che sono state limitato per il servizio di ricerca|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CPUXNS|Utilizzo della CPU dello spazio dei nomi|Percentuale|Massimo|Servizio bus premium dello spazio dei nomi CPU utilizzo unità di misura metriche|
|WSXNS|Utilizzo di dimensioni della memoria per spazio dei nomi|Percentuale|Massimo|Metrica l'uso della memoria di servizio bus premium dello spazio dei nomi|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|cpu_percent|Percentuale di CPU|Percentuale|Media|Percentuale di CPU|
|physical_data_read_percent|Percentuale di IO dati|Percentuale|Media|Percentuale di IO dati|
|log_write_percent|Registro IO percentuale|Percentuale|Media|Registro IO percentuale|
|dtu_consumption_percent|Percentuale DTU|Percentuale|Media|Percentuale DTU|
|spazio di archiviazione|Dimensioni totali del database|Byte|Massimo|Dimensioni totali del database|
|connection_successful|Connessioni riuscite|Conta. numeri|Totale|Connessioni riuscite|
|connection_failed|Connessioni non riuscite|Conta. numeri|Totale|Connessioni non riuscite|
|blocked_by_firewall|Bloccato da Firewall|Conta. numeri|Totale|Bloccato da Firewall|
|blocco critico|Blocchi critici|Conta. numeri|Totale|Blocchi critici|
|storage_percent|Percentuale delle dimensioni del database|Percentuale|Massimo|Percentuale delle dimensioni del database|
|xtp_storage_percent|Percent(Preview) archiviazione OLTP in memoria|Percentuale|Media|Percent(Preview) archiviazione OLTP in memoria|
|workers_percent|Percentuale di collaboratori|Percentuale|Media|Percentuale di collaboratori|
|sessions_percent|Percentuale di sessioni|Percentuale|Media|Percentuale di sessioni|
|dtu_limit|Limite DTU|Conta. numeri|Media|Limite DTU|
|dtu_used|DTU utilizzato|Conta. numeri|Media|DTU utilizzato|
|service_level_objective|Obiettivo del livello del database|Conta. numeri|Totale|Obiettivo del livello del database|
|dwu_limit|limite dwu|Conta. numeri|Massimo|limite dwu|
|dwu_consumption_percent|Percentuale DWU|Percentuale|Media|Percentuale DWU|
|dwu_used|DWU utilizzato|Conta. numeri|Media|DWU utilizzato|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|cpu_percent|Percentuale di CPU|Percentuale|Media|Percentuale di CPU|
|physical_data_read_percent|Percentuale di IO dati|Percentuale|Media|Percentuale di IO dati|
|log_write_percent|Registro IO percentuale|Percentuale|Media|Registro IO percentuale|
|dtu_consumption_percent|Percentuale DTU|Percentuale|Media|Percentuale DTU|
|storage_percent|Percentuale di spazio di archiviazione|Percentuale|Media|Percentuale di spazio di archiviazione|
|workers_percent|Percentuale di collaboratori|Percentuale|Media|Percentuale di collaboratori|
|sessions_percent|Percentuale di sessioni|Percentuale|Media|Percentuale di sessioni|
|eDTU_limit|limite eDTU|Conta. numeri|Media|limite eDTU|
|storage_limit|Limite di archiviazione|Byte|Media|Limite di archiviazione|
|eDTU_used|eDTU utilizzato|Conta. numeri|Media|eDTU utilizzato|
|storage_used|Spazio di archiviazione utilizzato|Byte|Media|Spazio di archiviazione utilizzato|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|ResourceUtilization|Utilizzo di % SO|Percentuale|Massimo|Utilizzo di % SO|
|InputEvents|Eventi di input|Conta. numeri|Totale|Eventi di input|
|InputEventBytes|Byte evento di input|Byte|Totale|Byte evento di input|
|LateInputEvents|Eventi di Input in ritardo|Conta. numeri|Totale|Eventi di Input in ritardo|
|OutputEvents|Eventi di output|Conta. numeri|Totale|Eventi di output|
|ConversionErrors|Errori di conversione dei dati|Conta. numeri|Totale|Errori di conversione dei dati|
|Errori|Errori di runtime|Conta. numeri|Totale|Errori di runtime|
|DroppedOrAdjustedEvents|Eventi nell'ordine corretto|Conta. numeri|Totale|Eventi nell'ordine corretto|
|AMLCalloutRequests|Richieste di funzioni|Conta. numeri|Totale|Richieste di funzioni|
|AMLCalloutFailedRequests|Richieste di funzione non riuscita|Conta. numeri|Totale|Richieste di funzione non riuscita|
|AMLCalloutInputEvents|Funzione eventi|Conta. numeri|Totale|Funzione eventi|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuPercentage|Percentuale di CPU|Percentuale|Media|Percentuale di CPU|
|MemoryPercentage|Percentuale di memoria|Percentuale|Media|Percentuale di memoria|
|DiskQueueLength|Coda del disco|Conta. numeri|Totale|Coda del disco|
|HttpQueueLength|Lunghezza coda http|Conta. numeri|Totale|Lunghezza coda http|
|BytesReceived|Dati In|Byte|Totale|Dati In|
|BytesSent|Dati indietro|Byte|Totale|Dati indietro|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Richieste di|Richieste di|Conta. numeri|Totale|Richieste di|
|BytesReceived|Dati In|Byte|Totale|Dati In|
|BytesSent|Dati indietro|Byte|Totale|Dati indietro|
|Http2xx|2xx http|Conta. numeri|Totale|2xx http|
|Http3xx|3xx http|Conta. numeri|Totale|3xx http|
|Http401|HTTP 401|Conta. numeri|Totale|HTTP 401|
|Http403|HTTP 403|Conta. numeri|Totale|HTTP 403|
|Http404|HTTP 404|Conta. numeri|Totale|HTTP 404|
|Http406|HTTP 406|Conta. numeri|Totale|HTTP 406|
|Http4xx|4xx http|Conta. numeri|Totale|4xx http|
|Http5xx|Errori di Server http|Conta. numeri|Totale|Errori di Server http|
|MemoryWorkingSet|Working set della memoria|Byte|Totale|Working set della memoria|
|AverageMemoryWorkingSet|Media della memoria insieme di lavoro|Byte|Media|Media della memoria insieme di lavoro|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Unità di misura metriche|Nome visualizzato metriche|Unità|Tipo di aggregazione|Descrizione|
|---|---|---|---|---|
|CpuTime|Tempo CPU|Secondi|Totale|Tempo CPU|
|Richieste di|Richieste di|Conta. numeri|Totale|Richieste di|
|BytesReceived|Dati In|Byte|Totale|Dati In|
|BytesSent|Dati indietro|Byte|Totale|Dati indietro|
|Http2xx|2xx http|Conta. numeri|Totale|2xx http|
|Http3xx|3xx http|Conta. numeri|Totale|3xx http|
|Http401|HTTP 401|Conta. numeri|Totale|HTTP 401|
|Http403|HTTP 403|Conta. numeri|Totale|HTTP 403|
|Http404|HTTP 404|Conta. numeri|Totale|HTTP 404|
|Http406|HTTP 406|Conta. numeri|Totale|HTTP 406|
|Http4xx|4xx http|Conta. numeri|Totale|4xx http|
|Http5xx|Errori di Server http|Conta. numeri|Totale|Errori di Server http|
|MemoryWorkingSet|Working set della memoria|Byte|Totale|Working set della memoria|
|AverageMemoryWorkingSet|Media della memoria insieme di lavoro|Byte|Media|Media della memoria insieme di lavoro|
|AverageResponseTime|Tempo medio di risposta|Secondi|Media|Tempo medio di risposta|

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulle metriche di Azure Monitor](./monitoring-overview.md#monitoring-sources)
- [Consente di creare avvisi sui dati statistici relativi](./insights-receive-alert-notifications.md)
- [Esportare metriche per lo spazio di archiviazione, evento Hub o Analitica Log](./monitoring-overview-of-diagnostic-logs.md)
