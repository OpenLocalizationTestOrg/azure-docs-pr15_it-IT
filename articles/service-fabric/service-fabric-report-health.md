<properties
   pageTitle="Aggiungere i report personalizzati integrità servizio tessuti | Microsoft Azure"
   description="In questo articolo viene descritto come inviare report di stato personalizzato alle entità integrità Azure servizio tessuti. Vengono forniti consigli per progettare e implementare report di stato della qualità."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="add-custom-service-fabric-health-reports"></a>Aggiungere report di stato servizio tessuti personalizzato
Azure servizio tessuti introduce un [modello di integrità](service-fabric-health-introduction.md) progettato per contrassegnare cluster non corretti e condizioni di applicazione su entità specifiche. Il modello di integrità utilizza **reporters integrità** (componenti di sistema e watchdog). L'obiettivo è facile e veloce diagnosi e ripristino. Gli autori del servizio necessario considerare attività dello stato. Qualsiasi condizione che influiscono sull'integrità dovrebbe essere segnalati, soprattutto se può essere utile problemi contrassegno vicino radice. Informazioni sullo stato di risparmiare tempo e basata su debug e indagini una volta il servizio sia in esecuzione in scala nel cloud (privato o Azure).

Il monitor reporters tessuti servizio identificati condizioni di interesse. Si riferiscono tali condizioni in base alla loro visualizzazione locale. [Integrità archiviare](service-fabric-health-introduction.md#health-store) aggrega i dati di integrità provenienti dai reporter tutti per determinare se entità sono globalmente integro. Il modello deve essere RTF, flessibile e facile da usare. La qualità di un report di stato determina la precisione della visualizzazione dello stato del cluster. Falsi che mostrano erroneamente problemi non corretti possono influire negativamente sulle aggiornamenti o altri servizi che utilizzano integrità dei dati. Esempi di tali servizi sono meccanismi avvisi e i servizi di ripristino. Di conseguenza, alcune pensiero è necessaria per fornire i rapporti in grado di registrare le condizioni di interesse nel miglior modo possibile.

Per progettare e implementare la segnalazione dello stato, watchdog e componenti di sistema necessario:

- Definire la condizione che sono interessati, il modo in cui che si monitorare e l'impatto sulle funzionalità cluster o dell'applicazione. In base a queste informazioni, scegliere Proprietà report dello stato di integrità.

- Determinare l' [entità](service-fabric-health-introduction.md#health-entities-and-hierarchy) che si riferisce il report.

- Determinare dove si trova la segnalazione fatto, all'interno del servizio o da un controllo interno o esterno.

- Definire un'origine utilizzata per identificare chi.

- Scegliere una strategia di creazione di report, periodicamente o su transizioni. È consigliabile periodicamente, come richiede codice più semplice e meno soggetta a errori.

- Determinare il periodo di tempo il report per condizioni non corretti deve rimanere nell'archivio di integrità e come deve essere deselezionata. Usa queste informazioni, decidere il periodo di validità del report e il comportamento Rimuovi in scadenza.

Come detto, creazione di report possono essere eseguite da:

- La replica di servizio servizio tessuti monitorata.

- Watchdog interno distribuito come un servizio tessuti (servizio, ad esempio, un servizio tessuti senza informazioni sullo stato che esegue il monitoraggio condizioni e problemi report). Il watchdog può essere distribuito un tutti i nodi o possibile affinità al servizio monitorato.

- Watchdog interno che eseguono nei nodi tessuti servizio *non* è implementata come servizi di infrastruttura di servizio.

- Watchdog esterni che verifica la risorsa da *all'esterno* del servizio tessuti cluster (ad esempio servizio di monitoraggio come compresi Gomez).

> [AZURE.NOTE] All'esterno della casella cluster verrà inserito i report di stato provenienti dai componenti del sistema. Per ulteriori al [report di stato di sistema di utilizzo per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). Report utente deve essere inviato [entità integrità](service-fabric-health-introduction.md#health-entities-and-hierarchy) che sono già stati creati dal sistema.

Una volta sull'integrità dei report struttura è deselezionata, report di stato è possibile inviare facilmente. Se il cluster non è [protetta](service-fabric-cluster-security.md) o se il client tessuti dispone di privilegi di amministratore, è possibile utilizzare [FabricClient](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.aspx) per integrità dei report. Può essere eseguita tramite l'API tramite [FabricClient.HealthManager.ReportHealth](https://msdn.microsoft.com/library/system.fabric.fabricclient.healthclient.reporthealth.aspx), PowerShell o resto. Comandi di configurazione batch report per migliorare le prestazioni.

> [AZURE.NOTE] Inviare una segnalazione dello stato icona del e rappresenta solo le attività di convalida sul lato client. Il fatto che il report è stato accettato dal client integrità o la `Partition` o `CodePackageActivationContext` oggetti non significa che è applicato nell'archivio. Vengono inviate in modo asincrono ed eventualmente in batch con altri report. Elaborazione sul server ancora potrebbe non riuscire: il numero di sequenza può essere obsoleto, l'entità in cui deve essere applicato il report è stata eliminata, e così via.

## <a name="health-client"></a>Client di integrità
Report di stato vengono inviati all'archivio di integrità tramite un client integrità che si trova all'interno del client tessuti. Il client di integrità può essere configurato con le operazioni seguenti:

- **HealthReportSendInterval**: il ritardo tra il momento il report viene aggiunto al client e l'ora di invio all'archivio di integrità. Utilizzato per i report batch in un singolo messaggio invece di inviare un messaggio per ogni rapporto. Il batch migliora le prestazioni. Predefinito: 30 secondi.

- **HealthReportRetrySendInterval**: l'intervallo in cui il client di integrità rinvia accumulato integrità dei report per l'archivio di integrità. Predefinito: 30 secondi.

- **HealthOperationTimeout**: il timeout di un messaggio di report inviato all'archivio di integrità. Se un messaggio di timeout, il client di integrità Ritenta essa finché l'archivio di integrità conferma che il report è stato elaborato. Predefinito: due minuti.

> [AZURE.NOTE] Quando si esegue il batch i report, il client tessuti dovrà essere conservato per almeno HealthReportSendInterval per garantire che sono stati inviati. Se il messaggio viene perso o l'archivio di integrità non può essere applicato a causa errori temporanei, il client tessuti dovrà essere conservato più assegnarle la possibilità di riprovare.

Il buffer sul client verrà visualizzata l'univocità dei report in considerazione. Se, ad esempio, un particolare strumento di creazione report errata segnalato 100 report al secondo sulla stessa proprietà della stessa entità, i report vengono sostituiti con l'ultima versione. Nella coda client è presente almeno un solo tale relazione. Se il batch è configurato, il numero di report inviato all'archivio di integrità è solo uno per l'intervallo di invio. Questo report è l'ultimo aggiunto, che indica lo stato più recente dell'entità.
Tutti i parametri di configurazione possono essere specificato quando `FabricClient` viene creato passando [FabricClientSettings](https://msdn.microsoft.com/library/azure/system.fabric.fabricclientsettings.aspx) con i valori desiderati per le voci di salute.

Di seguito viene creato un client tessuti e specifica che i report devono essere inviati quando vengono aggiunti. Timeout e gli errori che possono essere riprovati tentativi verificano ogni 40 secondi.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Quando si crea una connessione a un cluster tramite PowerShell, è possibile specificare parametri stesso. Di seguito avvia una connessione a un cluster locale:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

> [AZURE.NOTE] Per assicurarsi che servizi non autorizzati non è possibile inviare una segnalazione dello stato con l'entità del cluster, il server può configurato per accettare le richieste solo dai client protetto. Il `FabricClient` utilizzato per il report è necessario che sicurezza abilitato la possibilità di comunicare con il cluster (ad esempio, con l'autenticazione Kerberos o certificato). Altre informazioni sulla [protezione del cluster](service-fabric-cluster-security.md).

## <a name="report-from-within-low-privilege-services"></a>Un report all'interno di servizi di privilegi limitati
All'interno di servizi di infrastruttura di servizio che non dispongono di accesso dell'amministratore del cluster, è possibile segnalare integrità su entità nel contesto corrente tramite `Partition` o `CodePackageActivationContext`.

- Per i servizi senza informazioni sullo stato, utilizzare [IStatelessServicePartition.ReportInstanceHealth](https://msdn.microsoft.com/library/system.fabric.istatelessservicepartition.reportinstancehealth.aspx) per segnalare l'istanza corrente del servizio.

- Per i servizi di informazioni sullo stato, utilizzare [IStatefulServicePartition.ReportReplicaHealth](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.reportreplicahealth.aspx) per creare un report sul replica corrente.

- Utilizzare [IServicePartition.ReportPartitionHealth](https://msdn.microsoft.com//library/system.fabric.iservicepartition.reportpartitionhealth.aspx) per creare un report sul entità partizione corrente.

- Utilizzare [CodePackageActivationContext.ReportApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportapplicationhealth.aspx) per creare un report sul applicazione corrente.

- Utilizzare [CodePackageActivationContext.ReportDeployedApplicationHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth.aspx) per segnalare l'applicazione corrente distribuita sul nodo corrente.

- Utilizzare [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth.aspx) per segnalare un pacchetto di servizio per l'applicazione corrente distribuito sul nodo corrente.

> [AZURE.NOTE] Internamente, il `Partition` e `CodePackageActivationContext` tenere un client di stato configurato con le impostazioni predefinite. Le stesse considerazioni spiegazione per [client integrità](service-fabric-report-health.md#health-client) applicare - report sono in blocco e invio di un timer in modo che gli oggetti devono essere conservati avere la possibilità di inviare il rapporto.

## <a name="design-health-reporting"></a>Progettare report di integrità
Il primo passaggio per generare il report di alta qualità è identificare le condizioni che possono influire sull'integrità del servizio. Qualsiasi condizione che può risultare utili contrassegno problemi nel servizio o cluster data di inizio, o, ancora meglio, prima che si verifichi un problema, ossia possono salvare miliardi di euro. I vantaggi includono i tempi di inattività, meno notte ore spese in analisi e il ripristino problemi e soddisfazione del cliente.

Dopo aver identificate le condizioni, è necessario stabilire il modo migliore per eseguire il monitoraggio delle loro per equilibrio tra il sovraccarico e utilità writer di controllo. Si consideri ad esempio un servizio che consente di calcoli complessi che utilizzano alcuni file temporanei in una condivisione. Viene quindi monitorare Condividi per assicurarsi che sia disponibile sufficiente spazio. Impossibile ascoltare le notifiche delle modifiche apportate directory o file. Impossibile segnalare un avviso quando si raggiunge una soglia iniziale e genera un errore se l'opzione Condividi è completo. In un messaggio di avviso, un sistema di ripristino Impossibile avviare la pulizia del file più vecchi presenti nella condivisione. Un errore, un sistema di ripristino Impossibile spostare la replica di servizi a un altro nodo. Si noti come vengono descritti gli stati condizione in termini di integrità: lo stato della condizione che può essere considerata integro o non corretti (avviso o errore).

Dopo aver impostati i dettagli di monitoraggio, è necessario capire come implementare il controllo un writer di controllo. Se all'interno del servizio, è possono determinare le condizioni da, il controllo può far parte del servizio monitorato. Ad esempio codice servizio possibile controllare l'utilizzo di condivisione e quindi report tramite un client locale tessuti ogni volta che si tenta di creare un file. Il vantaggio di questo approccio è che reporting è semplice. È necessario prestare attenzione per impedire l'impatto attraverso la funzionalità del servizio di controllo errori.

Creazione di report dall'interno monitorato servizio non è sempre un'opzione. Viene quindi all'interno del servizio potrebbero non riuscire rilevare le condizioni. Non può avere la logica o dati per eseguire tale operazione. Il sovraccarico di monitoraggio alle condizioni potrebbe essere elevato. Le condizioni inoltre potrebbero non essere specifiche in un servizio, ma invece influisce interazioni tra i servizi. È inoltre possibile inserire watchdog nel cluster come processi separati. Il watchdog semplicemente monitorare le condizioni e un report, senza influenzare i servizi principali in alcun modo. Ad esempio, è Impossibile implementata questi watchdog come senza informazioni sullo stato servizi nella stessa applicazione distribuito in tutti i nodi o gli stessi nodi del servizio.

In alcuni casi, un controllo in esecuzione nel cluster non è un'opzione uno. Se la condizione monitorata disponibilità o funzionalità del servizio come utenti di visualizzarlo, è preferibile disporre di watchdog nella stessa posizione i client dell'utente. Possono, testare le operazioni nello stesso modo gli utenti chiamata. Ad esempio, è possibile impostare un controllo che si trova all'esterno del cluster e le richieste di problemi relativi al servizio e quindi controlla la latenza e la correttezza del risultato. (Per un servizio di calcolo, ad esempio, 2 + 2 restituiscono 4 certo tempo?)

Una volta i dettagli di controllo sono stati completati, è necessario decidere su un ID di origine che identifica in modo univoco. Se più watchdog dello stesso tipo sono che vivono in cluster, sia devono creare un report sul entità diverse oppure, se si riferiscono alla stessa entità, è necessario assicurarsi che l'ID di origine o la proprietà è diversa. In questo modo, i report possono coesistere. Proprietà del report di stato deve acquisire la condizione monitorata. (Ad esempio precedente, la proprietà potrebbe essere **ShareSize**.) Se più report applicare la stessa condizione, la proprietà deve contenere alcune informazioni dinamiche che consente di coesistere. Ad esempio, se necessario monitorare più azioni, il nome della proprietà può essere **ShareSize condivisione**.

> [AZURE.NOTE] Archivio integrità dovrebbe *non* essere utilizzato per mantenere le informazioni sullo stato. Solo le informazioni correlate integrità devono essere segnalate come integrità, come la valutazione dell'integrità di un'entità influisce di queste informazioni. Archivio di integrità non è stata progettata come un archivio di interesse generale. Utilizza la logica di valutazione di integrità per aggregare tutti i dati nello stato di integrità. Invio di informazioni non correlate alla salute (ad esempio la segnalazione dello stato con uno stato di integrità di OK) non ha un impatto lo stato di integrità aggregato, ma possono influire negativamente sulle prestazioni dell'archivio di integrità.

Il punto di decisione successivo è quale entità al report. La maggior parte dei casi, si tratta più evidente, in base alla condizione. È consigliabile scegliere l'entità con granularità possibili migliore. Se una condizione influisce su ogni replica in una partizione, creare un report sul partizione, non sul servizio. Esistono casi nell'angolo in maggiore attenzione è necessaria attraverso. Se la condizione influisce un'entità, ad esempio una replica, ma la desiderata consiste nell'utilizzare la condizione contrassegnato per altre informazioni oltre la durata di vita di replica, quindi deve essere segnalato sulla partizione. In caso contrario, quando viene eliminata la replica, tutti i report è associati verranno rimosse dall'archivio. Questo errore indica che gli autori di controllo anche necessario prendere in considerazione la durata dell'entità e il report. Deve essere chiaro quando deve essere pulito un report da un archivio (ad esempio quando un messaggio di errore segnalato per un'entità non è più valida).

Esaminiamo un esempio che inserisce insieme punti descritto. Valutare la possibilità di che un'applicazione di servizio tessuti è composto da un servizio di persistente informazioni sullo stato master e servizi senza informazioni sullo stato secondari distribuiti in tutti i nodi (un tipo di servizio secondario per ogni tipo di attività). Lo schema dispone di una coda di elaborazione che include i comandi deve essere eseguita dal database secondari. Database secondario esegue le richieste in arrivo e invia segnali conferma indietro. Una condizione che è possibile monitorare è la lunghezza della coda di elaborazione master. Se la lunghezza della coda master raggiunge una soglia, viene segnalato un avviso. L'avviso indica che i database secondari non è possibile gestire il carico. Se coda raggiunge la lunghezza massima e i comandi vengono eliminati, viene segnalato un errore, come il servizio non è possibile recuperare. I report possono essere sulla proprietà **QueueStatus**. Il controllo si trova all'interno del servizio, mentre viene inviato periodicamente sulla replica master principale. La durata è di due minuti e viene inviato periodicamente ogni 30 secondi. Se si blocca primario, il report è cancellato automaticamente dall'archivio. Se la replica di servizi, ma è bloccata o altri problemi, il report scade tra l'archivio di integrità. In questo caso, viene valutata l'entità in errore.

Un'altra condizione che è possibile monitorare è tempo di esecuzione delle attività. Lo schema distribuisce attività al database secondari in base al tipo di attività. A seconda della progettazione, lo schema Impossibile sondaggio database secondari per lo stato delle attività. Inoltre Impossibile attendere secondari sia inviare conferma posteriore segnali quando devono essere completate. Nel secondo caso, è necessario prestare attenzione per individuare le situazioni in cui interruzione dei secondari o i messaggi vengono persi. Un'opzione possibile consiste per lo schema inviare una richiesta di ping allo stesso secondario, che invia il suo stato. Se non si riceve stato, lo schema considera un errore e ripianifica l'attività. Questo comportamento si presuppone che le attività siano idempotenti.

Se l'attività non viene eseguito in una determinata ora (**t1**, ad esempio 10 minuti), è possibile tradurre la condizione monitorata come avviso. Se l'attività non completata nel tempo (**t2**, ad esempio 20 minuti), la condizione monitorata può essere convertita come errore. Questi report può essere eseguito in molti modi diversi:

- La replica master principale report sul stesso periodicamente. È possibile impostare una proprietà per tutte le attività in sospeso nella coda. Se almeno un'attività richiede più tempo, lo stato del report sulla proprietà **PendingTasks** è un avviso o errore, in base alle esigenze. Se non esistono attività in sospeso o tutte le attività iniziate esecuzione, lo stato del report è OK. Le attività sono permanenti. Se primario viene interrotto, è possibile continuare appena alzati di livello principale segnalare correttamente.

- Un altro processo di controllo (nel cloud o esterno) controlla le attività (dall'esterno, in base il risultato desiderato delle attività) per verificare se sono completate. Se non rispettino le soglie, il servizio master viene inviato un report. Un report viene inviato anche a ogni attività che include l'identificatore di attività, ad esempio **PendingTask + ID attività**. Report devono essere inviati solo su stati non corretti. Impostare l'ora a live per alcuni minuti e contrassegnare i report da rimuovere alla scadenza per garantire la pulizia.

- Secondario che esegue un'attività riporta quando richiede più tempo del previsto per eseguirlo. Report sull'istanza del servizio nella proprietà **PendingTasks**. Il report individua l'istanza del servizio che presenta problemi, ma non di acquisizione la situazione in cui l'istanza muore. I report vengono cancellati quindi. Impossibile segnalare il servizio secondario. Se secondario completamento dell'attività, l'istanza secondaria Cancella report dall'archivio. Il report non acquisisce la situazione in cui il messaggio di conferma viene perso e l'attività non completata dal punto di vista del master.

Tuttavia la segnalazione viene eseguita nei casi descritti sopra, i report vengono acquisiti dell'integrità durante la valutazione dell'integrità.

## <a name="report-periodically-vs-on-transition"></a>Report periodicamente e sulla transizione
Utilizzando il modello di creazione di report dello stato, watchdog inviare rapporti periodicamente o le transizioni. Il modo consigliato per i report di controllo è periodicamente, poiché il codice è molto semplice e meno soggetto a errori. Il watchdog deve sforzandosi di essere costituiti semplicemente possibili evitare errori che impostare un trigger report corretti. Non corretti *Integro* report effetti in termini di scenari in base a stato, inclusi gli aggiornamenti e le valutazioni di integrità. Report non corretta di tipo *Integro* nascondere i problemi del cluster, non si desidera.

Per i report periodici, è possibile implementare il controllo con un timer. Su un callback timer, il controllo può controllare lo stato e inviare un report in base allo stato corrente. Non è necessario visualizzare il report è stato inviato in precedenza o effettuare le ottimizzazioni in termini di messaggistica. Il client di integrità ha batch logica per migliorare le prestazioni. Mentre il client di integrità viene mantenuto attivo, effettua tentativi internamente fino a quando il report viene riconosciuto dall'archivio di integrità o il controllo genera un report più recente con la stessa entità, proprietà e origine.

Creazione di report sulle transizioni richiede attenzione a gestione dello stato. Il controllo controlla alcune condizioni e segnala solo quando le condizioni di modificano. Il vantaggio di questo approccio è che sono necessari report meno. Lo svantaggio è che la logica del controllo è complessa. Il controllo deve mantenere le condizioni o i report, in modo che possono essere esaminati per determinare le modifiche di stato. In caso di failover, è necessario prestare attenzione quando viene inviato un report che potrebbero non sono state inviate in precedenza (nella coda, ma non ancora inviato all'archivio di stato). Il numero di sequenza deve essere crescente. In caso contrario, i report vengono rifiutati perché non aggiornati. In rari casi in cui si verifica la perdita di dati, la sincronizzazione potrebbe essere necessario tra lo stato di chi e lo stato dell'archivio di integrità.

Report sulle transizioni significativo per i servizi di creazione di report su se stessi, tramite `Partition` o `CodePackageActivationContext`. Quando l'oggetto locale (replica o servizio distribuito pacchetto / distribuita applicazione) viene rimosso, vengono rimosse anche tutti i report. Questo pulizia automatica rilassa la necessità di sincronizzazione tra strumento di creazione report e archivio di integrità. Se il report partizione padre o applicazione padre, è necessario prestare attenzione in caso di failover per evitare obsoleti nei report nell'archivio di integrità. Per mantenere lo stato corretto e cancellare il report dall'archivio quando non è più necessario, è necessario aggiungere logica.

## <a name="implement-health-reporting"></a>Implementare l'integrità dei report
Dopo aver Cancella i dettagli dell'entità e report, l'invio di report di stato può essere eseguita tramite l'API, PowerShell o resto.

### <a name="api"></a>API
Per inviare una segnalazione tramite l'API, è necessario creare un rapporto di stato specifico al tipo di entità che si desidera includere nel rapporto. Assegnare al report a un client di integrità. In alternativa, creare informazioni di integrità e passarlo per correggere i metodi di creazione di report in `Partition` o `CodePackageActivationContext` segnalazione entità corrente.

Nell'esempio seguente viene periodico report da un controllo all'interno del cluster. Il controllo viene verificato se è possibile accedere da una risorsa esterna all'interno di un nodo. La risorsa è necessario un manifesto servizio all'interno dell'applicazione. Se la risorsa è disponibile, è possibile che gli altri servizi all'interno dell'applicazione ancora possano funzionare correttamente. Di conseguenza, il report viene inviato per l'entità di pacchetto servizio distribuito ogni 30 secondi.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
Inviare report di stato con HealthReport *EntityType *Invia ServiceFabric*** *.

Nell'esempio seguente viene periodico dei report sui valori di CPU su un nodo. I report devono essere inviati ogni 30 secondi e hanno un periodo di validità di due minuti. Se scadono, chi ha problemi, in modo che il nodo viene valutato in errore. Quando la CPU è superiore al limite, il report contiene uno stato di integrità di avviso. Quando la CPU rimane superiore al limite per più di tempo configurato, verrà segnalato come errore. In caso contrario, chi invia uno stato di integrità di OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Nell'esempio seguente segnala un avviso temporaneo su una replica. Prima di tutto Ottiene l'ID di partizione e quindi l'ID replica per il servizio che è interessata. Invia quindi un report da **PowershellWatcher** sulla proprietà **ResourceDependency**. Il report è di interesse per due minuti e viene rimosso dall'archivio automaticamente.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>RESTO
Inviare i report di stato con resto richieste POST passare a entità desiderata e nel corpo la descrizione del report di stato. Ad esempio, vedere come inviare resto [report di stato cluster](https://msdn.microsoft.com/library/azure/dn707640.aspx) o un [report di stato del servizio](https://msdn.microsoft.com/library/azure/dn707640.aspx). Sono supportate tutte le entità.

## <a name="next-steps"></a>Passaggi successivi

In base ai integrità dei dati, gli autori del servizio e gli amministratori applicazione/cluster possono pensare modi per utilizzare le informazioni. Ad esempio, è possibile impostare avvisi in base allo stato di integrità intercettare gravi problemi prima che provocare interruzioni. Gli amministratori possono impostare anche i sistemi di ripristino per risolvere i problemi automaticamente.

[Introduzione a integrità dei servizi tessuti monitoraggio](service-fabric-health-introduction.md)

[Visualizza report integrità servizio tessuti](service-fabric-view-entities-aggregated-health.md)

[Come segnalare e verificare l'integrità del servizio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Utilizzare il report di stato di sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Monitorare e diagnosticare servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md)
