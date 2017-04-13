<properties
   pageTitle="Panoramica della configurazione di Azure servizio tessuti affidabile operatori KVSActorStateProvider | Microsoft Azure"
   description="Informazioni sulla configurazione di operatori di informazioni sullo stato di Azure servizio tessuti di tipo KVSActorStateProvider."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="sumukhs"/>

# <a name="configuring-reliable-actors--kvsactorstateprovider"></a>Configurare gli operatori affidabili - KVSActorStateProvider
È possibile modificare la configurazione predefinita di KVSActorStateProvider modificando il file Settings generati nella radice di package Microsoft Visual Studio all'interno della cartella di configurazione per attore specificato.

Il runtime tessuti servizio Azure consente di cercare i nomi delle sezioni predefinite nel file Settings e utilizza i valori di configurazione durante la creazione di componenti di runtime sottostante.

>[AZURE.NOTE] Eseguire **non** eliminare o modificare i nomi delle sezioni delle configurazioni seguenti nel file Settings generati della soluzione di Visual Studio.

## <a name="replicator-security-configuration"></a>Configurazione della protezione di replica
Configurazione della protezione di replica vengono utilizzati per proteggere il canale di comunicazione utilizzato durante la replica. Questo errore indica che servizi non è possibile vedere di altro traffico, verificare che i dati che vengono reso disponibili anche è protetti.
Per impostazione predefinita, una sezione di configurazione di sicurezza vuoti impedisce la sicurezza della replica.

### <a name="section-name"></a>Nome della sezione
&lt;ActorName&gt;ServiceReplicatorSecurityConfig

## <a name="replicator-configuration"></a>Configurazione di replica
Configurazioni di replica configurare il servizio di replica è responsabile dell'esecuzione altamente affidabile lo stato di Provider dello stato dell'attore.
La configurazione predefinita generata dal modello di Visual Studio e dovrebbe essere sufficiente. In questa sezione parla configurazioni aggiuntive disponibili ottimizzare la replica.

### <a name="section-name"></a>Nome della sezione
&lt;ActorName&gt;ServiceReplicatorConfig

### <a name="configuration-names"></a>Nomi di configurazione

|Nome|Unità|Valore predefinito|Note|
|----|----|-------------|-------|
|BatchAcknowledgementInterval|Secondi|0.015|Periodo di tempo per il quale replica in attesa secondario dopo aver ricevuto un'operazione prima di inviare di nuovo una conferma in primario. Altri riconoscimenti devono essere inviati per le operazioni di elaborazione all'interno dell'intervallo vengono inviati come una risposta.|
|ReplicatorEndpoint|N/D|Nessun valore predefinito, ossia parametro obbligatorio|Indirizzo IP e porte utilizzate per comunicare con altri replicatori di replica di replica primario e secondario. Questo deve fare riferimento a un endpoint di risorsa TCP nel manifesto di servizio. Fare riferimento alle [risorse manifesto del servizio](service-fabric-service-manifest-resources.md) per ulteriori informazioni sulla definizione di risorse endpoint nel manifesto di servizio. |
|RetryInterval|Secondi|5|Periodo di tempo trascorso il quale la replica invia un messaggio nuovamente se non riceve una conferma per un'operazione.|
|MaxReplicationMessageSize|Byte|50 MB|Dimensioni massime dei dati di replica che possono essere trasmessi in un singolo messaggio.|
|MaxPrimaryReplicationQueueSize|Numero di operazioni|1024|Numero massimo di operazioni nella coda principale. Un'operazione si libera dopo replica primaria riceve la conferma da tutti i replicatori secondari. Questo valore deve essere maggiore di 64 e una potenza di 2.|
|MaxSecondaryReplicationQueueSize|Numero di operazioni|2048|Numero massimo di operazioni nella coda secondaria. Un'operazione si libera dopo avere apportato relativo stato di disponibilità tramite persistenza. Questo valore deve essere maggiore di 64 e una potenza di 2.|

## <a name="store-configuration"></a>Configurazione dell'archivio
Archivio configurazioni vengono utilizzate per configurare l'archivio locale è utilizzata per mantenere lo stato è in corso la replica.
La configurazione predefinita generata dal modello di Visual Studio e dovrebbe essere sufficiente. In questa sezione parla configurazioni aggiuntive disponibili ottimizzare l'archivio locale.

### <a name="section-name"></a>Nome della sezione
&lt;ActorName&gt;ServiceLocalStoreConfig

### <a name="configuration-names"></a>Nomi di configurazione

|Nome|Unità|Valore predefinito|Note|
|----|----|-------------|-------|
|MaxAsyncCommitDelayInMilliseconds|Millisecondi|200|Imposta il valore massimo in batch intervallo per il commit permanente archivio locale.|
|MaxVerPages|Numero di pagine|16384|Il numero massimo di pagine della versione in locale memorizzata database. Determina il numero massimo di transazioni in attesa.|

## <a name="sample-configuration-file"></a>File di configurazione di esempio

```xml
<?xml version="1.0" encoding="utf-8"?>
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Section Name="MyActorServiceReplicatorConfig">
      <Parameter Name="ReplicatorEndpoint" Value="MyActorServiceReplicatorEndpoint" />
      <Parameter Name="BatchAcknowledgementInterval" Value="0.05"/>
   </Section>
   <Section Name="MyActorServiceLocalStoreConfig">
      <Parameter Name="MaxVerPages" Value="8192" />
   </Section>
   <Section Name="MyActorServiceReplicatorSecurityConfig">
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
## <a name="remarks"></a>Note

Il parametro BatchAcknowledgementInterval controlla la latenza di replica. Un valore di "0" restituisce come risultato della latenza possibili più bassa, al costo di velocità (come più messaggi di risposta da inviare ed elaborati, ciascuno contenente un numero minore di riconoscimenti).
Maggiore sarà il valore BatchAcknowledgementInterval, maggiore sarà la replica velocità complessiva, al costo di latenza operazione superiore. Converte direttamente nella latenza del commit delle transazioni.
