<properties
   pageTitle="Creare e gestire un cluster di Azure servizio tessuti autonoma | Microsoft Azure"
   description="Creare e gestire un cluster di Azure servizio tessuti computer (fisica o virtuale) che eseguono Windows Server, se è locale o in qualsiasi cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="dkshir;chackdan"/>


# <a name="create-and-manage-a-cluster-running-on-windows-server"></a>Creare e gestire un cluster che esegue Windows Server

È possibile utilizzare Azure servizio tessuti per creare tessuti servizio cluster in qualsiasi macchine virtuali o un computer che eseguono Windows Server. Indica che è possibile distribuire e applicazioni di servizio tessuti in qualsiasi ambiente che contiene un insieme di computer Windows Server interconnessi, ovvero in locale o con un provider del cloud. Servizio tessuti fornisce un pacchetto di installazione per creare cluster tessuti servizio denominato il pacchetto di Windows Server autonomo.

In questo articolo sono illustrati i passaggi per la creazione di un cluster utilizzando il pacchetto autonomo per tessuti servizio locale, anche se possono essere facilmente modificata per qualsiasi altro ambiente, ad esempio altri provider di servizi cloud.

>[AZURE.NOTE] Questo pacchetto di Windows Server autonomo potrebbe contenere caratteristiche che sono attualmente in anteprima e non sono supportati per uso commerciale. Per visualizzare l'elenco delle caratteristiche disponibili in anteprima, vedere "anteprima caratteristiche incluse nel pacchetto". È inoltre possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) ora.


<a id="getsupport"></a>
## <a name="get-support-for-the-service-fabric-standalone-package"></a>Ottenere supporto per il pacchetto di tessuti servizio autonomo

- Chiedi alla community sul pacchetto tessuti servizio autonomo per Windows Server nel [forum di Azure servizio tessuti](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureServiceFabric?).

- Aprire un ticket di [Supporto tecnico per tessuti servizio](http://support.microsoft.com/oas/default.aspx?prid=16146 ).  Altre informazioni di [Supporto tecnico Microsoft](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).

<a id="downloadpackage"></a>
## <a name="download-the-service-fabric-standalone-package"></a>Download del pacchetto di tessuti servizio autonomo


[Scaricare il pacchetto autonomo per servizio tessuti per Windows Server 2012 R2 e versioni successive](http://go.microsoft.com/fwlink/?LinkId=730690), denominato Microsoft.Azure.ServiceFabric.WindowsServer. &lt;versione&gt;ZIP.


Nel pacchetto di download, si possono trovare i file seguenti:

|**Nome del file**|**Breve descrizione**|
|-----------------------|--------------------------|
|MicrosoftAzureServiceFabric.cab|File CAB contenente i file binari distribuiti in ogni computer del cluster.|
|ClusterConfig.Unsecure.DevCluster.json|Un file di esempio configurazione cluster che contiene le impostazioni per un non protetta, tre nodi di tipo solo computer (o la macchina virtuale) sviluppo cluster, incluse le informazioni per ogni nodo del cluster. |
|ClusterConfig.Unsecure.MultiMachine.json|Un file di esempio configurazione cluster che contiene le impostazioni per un cluster protetto, su più computer (o macchina virtuale), comprese le informazioni per ogni computer del cluster.|
|ClusterConfig.Windows.DevCluster.json|Un file di esempio configurazione cluster che contiene tutte le impostazioni per un sicura, tre nodi di tipo solo computer (o la macchina virtuale) sviluppo cluster, incluse le informazioni per ogni nodo cluster. Il cluster è protetta utilizzando [l'identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.Windows.MultiMachine.json|Un file di esempio configurazione cluster che contiene tutte le impostazioni per un cluster su più computer (o la macchina virtuale) sicura, la sicurezza di Windows, tra cui le informazioni relative a ogni computer che cluster protetto. Il cluster è protetta utilizzando [l'identità di Windows](https://msdn.microsoft.com/library/ff649396.aspx).|
|ClusterConfig.x509.DevCluster.json|Un file di esempio configurazione cluster che contiene tutte le impostazioni per un sicura, tre nodi di tipo solo computer (o la macchina virtuale) sviluppo cluster, incluse le informazioni per ogni nodo del cluster. Il cluster è protetto tramite x509 certificati.|
|ClusterConfig.x509.MultiMachine.json|Un file di esempio configurazione cluster che contiene tutte le impostazioni per il cluster protetta, su più computer (o macchina virtuale), incluse le informazioni per ogni nodo cluster protetto. Il cluster è protetto tramite x509 certificati.|
|EULA_ENU.txt|Condizioni di licenza per l'utilizzo del pacchetto di Windows Server autonomo di Microsoft Azure servizio tessuti. È possibile [scaricare una copia del contratto di licenza](http://go.microsoft.com/fwlink/?LinkID=733084) .|
|Leggimi|Collegamento alle note e le istruzioni di installazione di base. È un sottoinsieme delle istruzioni fornite in questo documento.|
|CreateServiceFabricCluster.ps1|Script di PowerShell che crea cluster usare le impostazioni di ClusterConfig.json.|
|RemoveServiceFabricCluster.ps1|Script di PowerShell che consente di rimuovere un cluster di usare le impostazioni di ClusterConfig.json.|
|ThirdPartyNotice.rtf |Avviso del software di terze parti nel pacchetto.|
|AddNode.ps1|Uno script di PowerShell per l'aggiunta di un nodo a una distribuzione cluster.|
|RemoveNode.ps1|Uno script di PowerShell per la rimozione di un nodo da un oggetto esistente distribuito cluster.|
|CleanFabric.ps1|Script di PowerShell per la pulizia autonomo installazione servizio tessuti disattivare sul computer in uso. Installazioni precedenti MSI devono essere rimosso usando le proprie uninstallers associato.|
|TestConfiguration.ps1|Script di PowerShell per l'analisi dell'infrastruttura come specificato nel Cluster.json.|


## <a name="plan-and-prepare-your-cluster-deployment"></a>Pianificare e preparare la distribuzione cluster
Prima di creare il cluster, eseguire i passaggi seguenti.

### <a name="step-1-plan-your-cluster-infrastructure"></a>Passaggio 1: Pianificare l'infrastruttura cluster
Sta per creare un cluster di servizio tessuti computer che si è proprietari, in modo che è possibile decidere quali tipi di errori da cluster a. Ad esempio, sono necessarie separati power linee o connessioni Internet fornite a queste macchine? Inoltre, prendere in considerazione la protezione fisica di questi computer. Dove si trovano i computer e chi deve accedervi? Dopo aver apportato queste decisioni, è possibile mappare i computer in modo logico per i diversi domini guasto (vedere Passaggio 4). L'infrastruttura di pianificazione dei cluster di produzione è più complessa di quella per i cluster di test.

<a id="preparemachines"></a>
### <a name="step-2-prepare-the-machines-to-meet-the-prerequisites"></a>Passaggio 2: Configurare i computer per soddisfare i prerequisiti
Prerequisiti per ogni computer che si desidera aggiungere al cluster:

- È consigliabile un minimo di 16 GB di RAM.
- Si consiglia di almeno 40 di GB di spazio disponibile su disco.
- È consigliabile un core 4 o CPU maggiore.
- Connettività a una rete protetta o reti per tutti i computer.
- Windows Server 2012 R2 o Windows Server 2012 (è necessario disporre [KB2858668](https://support.microsoft.com/kb/2858668) installato).
- [.NET framework 4.5.1 o versioni successive](https://www.microsoft.com/download/details.aspx?id=40773), eseguire l'installazione completa.
- [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell).
- [Servizio RemoteRegistry](https://technet.microsoft.com/library/cc754820) deve essere eseguito in tutti i computer.

L'amministratore di cluster distribuzione e la configurazione del cluster deve disporre [dei privilegi di amministratore](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) in ciascuno dei computer. Non è possibile installare servizio tessuti sul controller di dominio.

### <a name="step-3-determine-the-initial-cluster-size"></a>Passaggio 3: Determinare le dimensioni di cluster iniziale
Ogni nodo in un cluster di tessuti servizio autonomo presenta la struttura di servizio runtime distribuito e un membro del cluster. In una distribuzione di produzione tipica è un nodo per ogni istanza del sistema operativo (fisica o virtuale). La dimensione dei cluster dipende dalla esigenze aziendali. Tuttavia, è necessario disporre di una dimensione minima del cluster di tre nodi (computer o macchine virtuali).
In fase di sviluppo, è più di un nodo su un computer specifico. In un ambiente di produzione servizio tessuti supporta solo un nodo per ogni macchina virtuale o fisica.

### <a name="step-4-determine-the-number-of-fault-domains-and-upgrade-domains"></a>Passaggio 4: Determinare il numero di domini guasto ed eseguire l'aggiornamento di domini
Un *dominio* (FD) è un'unità fisica di errore e direttamente correlati all'infrastruttura fisica nei data center. Un dominio è costituito da componenti hardware (computer, opzioni, reti e altro) che condividono un singolo punto di errore. Anche se non vi è alcun mapping 1:1 tra domini guasto e rack, si parla, ogni rack può essere considerato un dominio. Se si sceglie i nodi del cluster, è consigliabile che i nodi distribuito tra almeno tre domini guasto.

Quando si specifica FDs in ClusterConfig.json, è possibile scegliere il nome di ogni FD. Servizio tessuti supporta FDs gerarchica, in modo che si può riflettere la topologia di infrastruttura presenti.  Ad esempio FDs seguenti sono valide:

- "faultDomain": "fd: / Room1/Rack1/Computer1"
- "faultDomain": "fd: / FD1"
- "faultDomain": "fd: / Room1/Rack1/PDU1/M1"


Un *aggiornamento di dominio* (UD) è un'unità logica dei nodi. Durante l'aggiornamento di servizio tessuti gestito (un aggiornamento dell'applicazione o un aggiornamento cluster), tutti i nodi di un UD provengono verso il basso per eseguire l'aggiornamento, mentre i nodi in altri UDs rimangono disponibili per elaborare le richieste. Aggiornamento del firmware eseguire nel computer non rispetta i UDs, in modo che è necessario procedere loro computer alla volta.

Il modo più semplice da considerare questi concetti è da considerare FDs come unità di errore non pianificato e UDs come unità di manutenzione pianificata.

Quando si specifica UDs in ClusterConfig.json, è possibile scegliere il nome di ogni UD. Ad esempio i nomi seguenti sono validi:

- "upgradeDomain": "UD0"
- "upgradeDomain": "UD1A"
- "upgradeDomain": "DomainRed"
- "upgradeDomain": "Blu"

Per informazioni più dettagliate sulle guasto e i domini di aggiornamento, vedere [la descrizione di un cluster di servizio tessuti](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="step-5-download-the-service-fabric-standalone-package-for-windows-server"></a>Passaggio 5: Download del pacchetto di autonoma tessuti servizio per Windows Server
[Scaricare il pacchetto autonomo tessuti servizio per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimere il pacchetto, su un computer di distribuzione che non fa parte del cluster o a uno dei computer da una parte del cluster. È possibile rinominare la cartella decompresso `Microsoft.Azure.ServiceFabric.WindowsServer`.

<a id="createcluster"></a>
## <a name="create-your-cluster"></a>Creare il cluster

Dopo avere eliminato i passaggi pianificazione e preparazione, si è pronti creare il cluster.

### <a name="step-1-modify-cluster-configuration"></a>Passaggio 1: Modifica della configurazione cluster
Il cluster viene descritta in ClusterConfig.json. Per informazioni dettagliate sulle sezioni nel file, vedere [impostazioni di configurazione per cluster di Windows autonomo](service-fabric-cluster-manifest.md).
Aprire un file ClusterConfig.json dal pacchetto scaricato e modificare le impostazioni seguenti:

<!--Loc Comment: Please, check that line 129 the clause has been modified to "that you use as placement constraints" instead of using "you are used as placement constraints"-->

|**Impostazione di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|**NodeTypes**|Tipi di nodo consentono di separare i nodi del cluster in vari gruppi. Un cluster deve avere almeno un nodo. Tutti i nodi di un gruppo presentano le seguenti caratteristiche comuni: <br> **Nome** - questo è il nome del tipo di nodo. <br>**Porte di endpoint** - sono diversi denominati punti finali (porte) che sono associati a questo tipo di nodo. È possibile utilizzare qualsiasi numero di porta che si desidera, purché non sono in conflitto con un altro punto questo manifesto e non sono già in uso da qualsiasi altra applicazione in esecuzione nel computer/macchine Virtuali. <br> **Proprietà di posizionamento** - questi sono descritte le proprietà per questo tipo di nodo utilizzate come vincoli di posizionamento per i servizi di sistema o il provider di servizi. Queste proprietà sono coppie di parole chiave/valore definite dall'utente che forniscono dati aggiuntivi di metadati per un determinato nodo. Esempi di proprietà del nodo sono se il nodo ha un disco rigido o scheda grafica, il numero di assi il disco rigido, Core e altre proprietà fisiche. <br> **Capacità** - nodo capacità di definiscono il nome e l'importo di una determinata risorsa contenente un determinato nodo disponibile per il consumo. Ad esempio, può definire un nodo che disponga della capacità di una metrica denominata "MemoryInMb" e che abbia 2048 MB disponibili per impostazione predefinita. Queste capacità vengono utilizzate in fase di esecuzione per assicurarsi che i servizi che richiedono una particolare quantità di risorse vengono inseriti nei nodi contenenti le risorse disponibili in quantità necessarie.<br>**IsPrimary** - se si dispone di più di un nodo definito assicurarsi che solo un viene impostata a principale con il valore *vero*, che è in eseguono i servizi di sistema. Tutti gli altri tipi di nodo devono essere impostate su valore *false*|
|**Nodi**|Questi sono i dettagli per ognuno dei nodi che fanno parte di cluster (tipo di nodo, nodo nome, indirizzo IP, dominio e dominio l'aggiornamento del nodo). Il computer si desidera il cluster da creare sulla necessità di essere elencati qui con gli indirizzi IP. <br> Se si usa lo stesso indirizzo IP per tutti i nodi, quindi una casella viene creato un cluster, che è possibile utilizzare a scopo di testing. Non utilizzare cluster di una casella per la distribuzione di carichi di lavoro di produzione.|


### <a name="step-2-run-the-testconfiguration-script"></a>Passaggio 2: Eseguire lo script TestConfiguration

Lo script TestConfiguration verifica dell'infrastruttura come definito nella cluster.json per assicurarsi che si assegnano le autorizzazioni necessarie, i computer connessi tra loro e altri attributi sono definiti in modo che è possibile eseguire correttamente la distribuzione. Si tratta in sostanza un mini Best Practices Analyzer. Si continuerà a aggiungere ulteriori operazioni di convalida a questo strumento nel tempo per renderla più efficace.

Questo script può essere eseguito su qualsiasi computer dotato di accesso come amministratore per tutti i computer elencati come nodi nel file di configurazione cluster. Non è necessario che il computer in cui viene eseguito questo script faccia parte del cluster.

```powershell

PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True


```

### <a name="step-3-run-the-create-cluster-script"></a>Passaggio 3: Esecuzione script cluster create
Dopo aver modificato la configurazione di cluster in un documento JSON e aggiunto tutte le informazioni di nodo su di esso, eseguire la creazione di cluster script di PowerShell *CreateServiceFabricCluster.ps1* dalla cartella pacchetto e passare il percorso al file di configurazione JSON. Al termine dell'operazione, accettare il contratto di licenza.

Questo script può essere eseguito su qualsiasi computer dotato di accesso come amministratore per tutti i computer elencati come nodi nel file di configurazione cluster. Non è necessario che il computer in cui viene eseguito questo script faccia parte del cluster.

```
#Create an unsecured local development cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```
```
#Create an unsecured multi-machine cluster

.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

>[AZURE.NOTE] I registri di distribuzione sono disponibili in locale del computer macchine Virtuali/eseguiti CreateServiceFabricCluster PowerShell su. Si tratta di una sottocartella denominata DeploymentTraces all'interno della cartella in cui è stato eseguito il comando PowerShell. Per vedere se è stato distribuito servizio tessuti correttamente in un computer, è possibile trovare i file installati nella directory C:\ProgramData e i processi di FabricHost.exe e Fabric.exe possono essere visualizzati in esecuzione in Gestione attività.

### <a name="step-4-connect-to-the-cluster"></a>Passaggio 4: Connettersi al cluster

Per connettersi a un cluster sicuro, vedere [connettersi a cluster sicuro tessuti di servizio](service-fabric-connect-to-secure-cluster.md).

Per connettersi a un cluster non protetto, eseguire il comando PowerShell seguente:

```powershell

Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>

Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.2345:19000

```
### <a name="step-5-bring-up-service-fabric-explorer"></a>Passaggio 5: Visualizzare servizio tessuti Explorer

A questo punto è possibile connettersi a cluster con Esplora risorse tessuti servizio direttamente da uno dei computer con http://localhost:19080/Explorer/index.html o quando in modalità remota con http://<*IPAddressofaMachine*>: 19080/Explorer/index.html.



## <a name="add-and-remove-nodes"></a>Aggiungere e rimuovere nodi

È possibile aggiungere o rimuovere nodi al cluster tessuti servizio autonomo come variano le esigenze aziendali. Per informazioni dettagliate, vedere [aggiungere o rimuovere nodi in un cluster di tessuti servizio autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md) .


## <a name="remove-a-cluster"></a>Rimuovere un cluster

Per rimuovere un cluster, eseguire lo script di PowerShell *RemoveServiceFabricCluster.ps1* dalla cartella pacchetto e passare il percorso al file di configurazione JSON. Facoltativamente, è possibile specificare un percorso per il log dell'eliminazione.

Questo script può essere eseguito su qualsiasi computer dotato di accesso come amministratore per tutti i computer elencati come nodi nel file di configurazione cluster. Non è necessario che il computer in cui viene eseguito questo script faccia parte del cluster.

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json   
```


<a id="telemetry"></a>
## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dati di telemetria raccolti e su come rifiutare esplicitazione il

Per impostazione predefinita, il prodotto raccoglie telemetria sull'utilizzo del servizio tessuti per migliorare il prodotto. Analizzatore procedure consigliate che viene eseguita come parte dei controlli di configurazione per la connettività a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se non è raggiungibile, l'installazione non riesce a meno che non rifiutare esplicitazione telemetria.

  1. La pipeline di telemetria tenta di caricare i dati seguenti [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una volta al giorno. È il caricamento di un usufruendo e non influisce sulla funzionalità cluster. Di telemetria viene inviato solo dal nodo che viene eseguito il failover manager principale. Altri nodi di non inviano telemetria.

  2. Di telemetria è costituito da:

-            Numero dei servizi
-            Numero di ServiceTypes
-            Numero di applicazioni
-            Numero di ApplicationUpgrades
-            Numero di FailoverUnits
-            Numero di InBuildFailoverUnits
-            Numero di UnhealthyFailoverUnits
-            Numero di repliche
-            Numero di InBuildReplicas
-            Numero di StandByReplicas
-            Numero di OfflineReplicas
-            CommonQueueLength
-            QueryQueueLength
-            FailoverUnitQueueLength
-            CommitQueueLength
-            Numero dei nodi
-            IsContextComplete: Vero/falso
-            ClusterId: Si tratta di un GUID casuale per ogni cluster
-            ServiceFabricVersion
-             Indirizzo IP del computer virtuali o computer da cui viene caricato di telemetria


Per disattivare telemetry, aggiungere quanto segue alle *proprietà* nel file config cluster: *enableTelemetry: false*.

<a id="previewfeatures"></a>
## <a name="preview-features-included-in-this-package"></a>Funzionalità di anteprima inclusa nel pacchetto

Nessuno.

>[AZURE.NOTE] Con la nuova [versione GA del cluster autonoma per Windows Server (versione 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/), è possibile aggiornare il cluster a versioni future, manualmente o automaticamente. Poiché questa caratteristica non è disponibile per le versioni di anteprima, sarà necessario creare un cluster utilizzando la versione GA ed eseguire la migrazione dei dati e delle applicazioni dal cluster di anteprima. Continuare a leggere per ulteriori informazioni su questa caratteristica.


## <a name="next-steps"></a>Passaggi successivi
- [Impostazioni di configurazione per cluster di Windows autonomo](service-fabric-cluster-manifest.md)
- [Aggiungere o rimuovere nodi in un cluster di tessuti servizio autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Creare un cluster di tessuti servizio autonomo con macchine virtuali di Azure che esegue Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
- [Proteggere un cluster autonomo in Windows la sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows utilizzando X509 certificati](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
