<properties
   pageTitle="Panoramica della configurazione di Azure tessuti affidabile servizi | Microsoft Azure"
   description="Informazioni sulla configurazione di servizi affidabili informazioni sullo stato di Azure servizio tessuti."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="sumukhs"/>

# <a name="configure-stateful-reliable-services"></a>Configurare i servizi affidabili informazioni sullo stato

Sono disponibili due serie di impostazioni di configurazione per servizi affidabili. Uno di essi è globale per tutti i servizi affidabili del cluster mentre altri set è specifico per un particolare servizio affidabile.

## <a name="global-configuration"></a>Configurazione globale

La configurazione del servizio affidabile globale viene specificata nel manifesto cluster per il cluster nella sezione KtlLogger. Consente la configurazione di posizione log condiviso e dimensioni più i limiti di memoria globale utilizzati da parte del logger. Manifesto cluster è un unico file XML che contiene le impostazioni e configurazioni che si applicano a tutti i nodi e i servizi del cluster. Il file viene in genere chiamato ClusterManifest.xml. È possibile visualizzare il cluster manifesto per il cluster utilizzando il comando di powershell Get-ServiceFabricClusterManifest.

### <a name="configuration-names"></a>Nomi di configurazione

|Nome|Unità|Valore predefinito|Note|
|----|----|-------------|-------|
|WriteBufferMemoryPoolMinimumInKB|Kilobyte.|8388608 MB elevato|Numero minimo di KB allocare in modalità kernel per il pool di memoria buffer di scrittura logger. Questo pool di memoria viene usato per la memorizzazione nella cache le informazioni sullo stato prima della scrittura su disco.|
|WriteBufferMemoryPoolMaximumInKB|Kilobyte.|Nessun limite|Dimensione massima in cui il logger scrivere pool di memoria buffer espandibile.|
|SharedLogId|GUID|""|Specifica un GUID da utilizzare per individuare il file di log condiviso predefinito usato da tutti i servizi affidabili su tutti i nodi del cluster che non si specificano il SharedLogId nella configurazione specifiche del servizio. Se SharedLogId viene specificato, deve specificato anche SharedLogPath.|
|SharedLogPath|Nome completo del percorso|""|Specifica il percorso completo in cui il file di log condiviso utilizzato da tutti i servizi affidabili su tutti i nodi del cluster che non si specificano il SharedLogPath nella configurazione specifiche del servizio. Tuttavia, se SharedLogPath viene specificato, quindi SharedLogId deve essere specificato anche.|
|SharedLogSizeInMB|Megabyte|8192|Specifica il numero di MB di spazio su disco per l'allocazione statica per il log condiviso. Il valore deve essere maggiore di 2048.|

### <a name="sample-cluster-manifest-section"></a>Sezione manifesto cluster di esempio
```xml
   <Section Name="KtlLogger">
     <Parameter Name="WriteBufferMemoryPoolMinimumInKB" Value="8192" />
     <Parameter Name="WriteBufferMemoryPoolMaximumInKB" Value="8192" />
     <Parameter Name="SharedLogId" Value="{7668BB54-FE9C-48ed-81AC-FF89E60ED2EF}"/>
     <Parameter Name="SharedLogPath" Value="f:\SharedLog.Log"/>
     <Parameter Name="SharedLogSizeInMB" Value="16383"/>
   </Section>
```

### <a name="remarks"></a>Note
Il logger ha un pool globale di memoria allocata dalla memoria kernel non per pagina che è disponibile per tutti i servizi affidabili su un nodo per la memorizzazione nella cache dati relativi allo stato prima scritti nel log dedicato associato replica service affidabile. La dimensione del pool dipende dalle impostazioni WriteBufferMemoryPoolMinimumInKB e WriteBufferMemoryPoolMaximumInKB. WriteBufferMemoryPoolMinimumInKB specifica la dimensione iniziale del pool di memoria e le dimensioni più bassa al quale può ridurre il pool di memoria. WriteBufferMemoryPoolMaximumInKB è la dimensione massima a cui può aumentare il pool di memoria. Ogni replica service affidabile che viene aperto potrebbe aumentare le dimensioni del pool di memoria una quantità di sistema definito fino a WriteBufferMemoryPoolMaximumInKB. Se c'è più richiesta per la memoria dal pool di memoria rispetto a quella disponibile, le richieste di memoria saranno ritardate fino a quando non è disponibile memoria. Pertanto se il pool di memoria buffer di scrittura è troppo piccolo per una particolare configurazione potrebbero subire delle prestazioni.

Le impostazioni di SharedLogId e SharedLogPath vengono sempre utilizzate insieme per definire il GUID e il percorso per il log condiviso predefinito per tutti i nodi del cluster. Il registro condiviso predefinito viene utilizzato per tutti i servizi affidabili che non si specificano le impostazioni in Settings per il servizio specifico. Per ottenere prestazioni ottimali, condivisa file di log devono trovarsi su dischi utilizzato esclusivamente per il file di log condiviso per ridurre il conflitto.

SharedLogSizeInMB specifica la quantità di spazio su disco per preallocare per il log condiviso predefinito su tutti i nodi.  SharedLogId e SharedLogPath non è necessario specificare affinché SharedLogSizeInMB specificare.


## <a name="service-specific-configuration"></a>Informazioni di configurazione specifiche
È possibile modificare le configurazioni predefinite di informazioni sullo stato affidabile dei servizi tramite il pacchetto di configurazione (Config) o l'implementazione del servizio (codice).

+ **Configurazione** - configurazione tramite il pacchetto di configurazione viene eseguita modificando il file Settings generati nella radice sotto la cartella di configurazione per ogni servizio nell'applicazione di Microsoft Visual Studio.
+ **Codice** - configurazione tramite codice viene eseguita mediante la creazione di un ReliableStateManager utilizzando un oggetto ReliableStateManagerConfiguration con il set di opzioni appropriate.

Per impostazione predefinita, il runtime tessuti servizio Azure consente di cercare i nomi delle sezioni predefinite nel file Settings e utilizza i valori di configurazione durante la creazione di componenti di runtime sottostante.

>[AZURE.NOTE] Eseguire **non** Elimina i nomi delle sezioni delle seguenti configurazioni di Settings file generato della soluzione di Visual Studio, a meno che non si prevede di configurare il servizio tramite codice.
Ridenominazione di nomi di pacchetto o una nuova sezione config richiede una modifica al codice quando si configura il ReliableStateManager.


### <a name="replicator-security-configuration"></a>Configurazione della protezione di replica
Configurazione della protezione di replica vengono utilizzati per proteggere il canale di comunicazione utilizzato durante la replica. Ciò significa che servizi non sarà possibile vedere di altro traffico, verificare che i dati che vengono reso disponibili anche è protetti. Per impostazione predefinita, una sezione di configurazione di sicurezza vuoti impedisce la sicurezza della replica.

### <a name="default-section-name"></a>Nome della sezione predefinito
ReplicatorSecurityConfig

>[AZURE.NOTE] Per modificare il nome della sezione, ignorare il parametro replicatorSecuritySectionName al costruttore ReliableStateManagerConfiguration quando si creano ReliableStateManager per il servizio.


### <a name="replicator-configuration"></a>Configurazione di replica
Configurazioni di replica configurare il servizio di replica è responsabile dell'esecuzione dello stato del servizio affidabile informazioni sullo stato altamente affidabili da replicare e mantenere lo stato in locale.
La configurazione predefinita generata dal modello di Visual Studio e dovrebbe essere sufficiente. In questa sezione parla configurazioni aggiuntive disponibili ottimizzare la replica.

### <a name="default-section-name"></a>Nome della sezione predefinito
ReplicatorConfig

>[AZURE.NOTE] Per modificare il nome della sezione, ignorare il parametro replicatorSettingsSectionName al costruttore ReliableStateManagerConfiguration quando si creano ReliableStateManager per il servizio.


### <a name="configuration-names"></a>Nomi di configurazione
|Nome|Unità|Valore predefinito|Note|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0.015|Periodo di tempo per il quale replica in attesa secondario dopo aver ricevuto un'operazione prima di inviare di nuovo una conferma in primario. Altri riconoscimenti devono essere inviati per le operazioni di elaborazione all'interno dell'intervallo vengono inviati come una risposta.|
|ReplicatorEndpoint|N/D|Nessun valore predefinito, ossia parametro obbligatorio|Indirizzo IP e porte utilizzate per comunicare con altri replicatori di replica di replica primario e secondario. Questo deve fare riferimento a un endpoint di risorsa TCP nel manifesto di servizio. Fare riferimento alle [risorse manifesto del servizio](service-fabric-service-manifest-resources.md) per ulteriori informazioni sulla definizione di risorse endpoint in un manifesto di servizio. |
|MaxPrimaryReplicationQueueSize|Numero di operazioni|8192|Numero massimo di operazioni nella coda principale. Un'operazione si libera dopo replica primaria riceve la conferma da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 e una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|16384|Numero massimo di operazioni nella coda secondaria. Un'operazione si libera dopo avere apportato relativo stato di disponibilità tramite persistenza. Questo valore deve essere maggiore di 64 e una potenza di 2.|
|CheckpointThresholdInMB|MB|50|Quantità di spazio di file di log trascorso il quale viene eseguito un checkpoint lo stato.|
|MaxRecordSizeInKB|KB|1024|Dimensione massima record scritto la replica nel registro. Questo valore deve essere un multiplo di 4 e maggiori di 16.|
|MinLogSizeInMB|MB|0 (sistema determinato)|Dimensione minima del registro delle transazioni. Il log non sarà possibile troncare di una dimensione inferiore a questa impostazione. 0 indica che il servizio di replica determinerà la dimensione minima del registro. Aumentando questo valore aumenta la possibilità di eseguire copie parziali e incrementali dal possibilità di record di log pertinenti vengano troncati viene ridotta.|
|TruncationThresholdFactor|Fattore|2|Determina le dimensioni del log, verrà attivata troncato. Soglia di troncamento dipende dalla MinLogSizeInMB moltiplicato per TruncationThresholdFactor. TruncationThresholdFactor deve essere maggiore di 1. MinLogSizeInMB * TruncationThresholdFactor deve essere minore di MaxStreamSizeInMB.|
|ThrottlingThresholdFactor|Fattore|4|Determina quali dimensione del Registro di replica di inizierà viene limitata. Limitazione soglia (in MB) dipende dalla Max ((MinLogSizeInMB *ThrottlingThresholdFactor),(CheckpointThresholdInMB* ThrottlingThresholdFactor)). Limitazione soglia (in MB) deve essere maggiore del limite di troncamento (in MB). Soglia di troncamento (in MB) deve essere minore di MaxStreamSizeInMB.|
|MaxAccumulatedBackupLogSizeInMB|MB|800|Max accumulata dimensioni (in MB) dei registri di backup nella catena di registro di backup specificato. Una richiesta di backup incrementale avrà esito negativo se il backup incrementale genera un file di log backup che fa sì che i registri di backup accumulati dal pertinenti backup completo di dimensioni superiori. In tal caso, utente viene richiesto di eseguire un backup completo.|
|SharedLogId|GUID|""|Specifica un GUID da utilizzare per individuare il file di log condiviso utilizzato con la replica. In genere, i servizi di non utilizzare questa impostazione. Tuttavia, se SharedLogId viene specificato, quindi SharedLogPath deve essere specificato anche.|
|SharedLogPath|Nome completo del percorso|""|Specifica il percorso completo in cui verrà creato il file di log condiviso per la replica. In genere, i servizi di non utilizzare questa impostazione. Tuttavia, se SharedLogPath viene specificato, quindi SharedLogId deve essere specificato anche.|
|SlowApiMonitoringDuration|Secondi|300|Imposta l'intervallo di monitoraggio per le chiamate API gestite. Esempio: funzione callback backup fornito dall'utente. L'intervallo è già trascorsa, un rapporto di stato di avviso verrà inviato al gestore di integrità.|

### <a name="sample-configuration-via-code"></a>Esempio di configurazione tramite codice
```csharp
class Program
{
    /// <summary>
    /// This is the entry point of the service host process.
    /// </summary>
    static void Main()
    {
        ServiceRuntime.RegisterServiceAsync("HelloWorldStatefulType",
            context => new HelloWorldStateful(context, 
                new ReliableStateManager(context, 
        new ReliableStateManagerConfiguration(
                        new ReliableStateManagerReplicatorSettings()
            {
                RetryInterval = TimeSpan.FromSeconds(3)
                        }
            )))).GetAwaiter().GetResult();
    }
}    
```
```csharp
class MyStatefulService : StatefulService
{
    public MyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica stateManager)
        : base(context, stateManager)
    { }
    ...
}
```


### <a name="sample-configuration-file"></a>File di configurazione di esempio
```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="ReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="ReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
      <Parameter Name="CheckpointThresholdInMB" Value="512" />
   </Section>
   <Section Name="ReplicatorSecurityConfig">
      <Parameter Name="CredentialType" Value="X509" />
      <Parameter Name="FindType" Value="FindByThumbprint" />
      <Parameter Name="FindValue" Value="9d c9 06 b1 69 dc 4f af fd 16 97 ac 78 1e 80 67 90 74 9d 2f" />
      <Parameter Name="StoreLocation" Value="LocalMachine" />
      <Parameter Name="StoreName" Value="My" />
      <Parameter Name="ProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="AllowedCommonNames" Value="My-Test-SAN1-Alice,My-Test-SAN1-Bob" />
   </Section>
</Settings>
```


### <a name="remarks"></a>Note
BatchAcknowledgementInterval controlla la latenza di replica. Un valore di "0" restituisce come risultato della latenza possibili più bassa, al costo di velocità (come più messaggi di risposta da inviare ed elaborati, ciascuno contenente un numero minore di riconoscimenti).
Maggiore sarà il valore BatchAcknowledgementInterval, maggiore sarà la replica velocità complessiva, al costo di latenza operazione superiore. Converte direttamente nella latenza del commit delle transazioni.

Il valore di CheckpointThresholdInMB controlla la quantità di spazio su disco che la replica consente di memorizzare informazioni sullo stato nel file di log dedicato della replica. Questo aumento su un valore maggiore rispetto a quello predefinito potrebbe causare velocizzare i tempi di riconfigurazione quando viene aggiunta una nuova replica per il set. Ciò è dovuto il trasferimento dello stato parziale che entrerà in vigore a causa la disponibilità della cronologia delle altre operazioni nel registro. Potenzialmente aumentando il tempo di ripristino di una replica dopo un arresto anomalo.

L'impostazione MaxRecordSizeInKB definisce la dimensione massima di un record che può essere scritta tramite la replica nel file di log. Nella maggior parte dei casi, le dimensioni di 1024 KB record predefinito sono ottimale. Tuttavia, se il servizio causa elementi di dati più grandi da includere le informazioni sullo stato, questo valore potrebbe essere necessario aumentare. Esiste un piccolo vantaggio rendere MaxRecordSizeInKB inferiori a 1024, come record più piccoli utilizzare solo lo spazio necessario per il record più piccolo. Si prevede che questo valore dovrà essere modificati solo raramente.

Le impostazioni di SharedLogId e SharedLogPath vengono sempre utilizzate insieme per rendere un servizio di utilizzare un file di log condiviso separate da log condiviso predefinito per il nodo. Per ottenere migliori prestazioni rispetto a numerosi servizi possibili necessario specificare lo stesso log condiviso. Condivisa file di log devono trovarsi su dischi utilizzato esclusivamente per il file di log condiviso per ridurre i conflitti di spostamento principale. Si prevede che questo valore dovrà essere modificati solo raramente.

## <a name="next-steps"></a>Passaggi successivi
 - [Il debug dell'applicazione di servizio tessuti in Visual Studio](service-fabric-debugging-your-application.md)
 - [Riferimenti per sviluppatori per servizi affidabili](https://msdn.microsoft.com/library/azure/dn706529.aspx)
