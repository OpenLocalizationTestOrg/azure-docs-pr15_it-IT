<properties
   pageTitle="Aggiornare un cluster di tessuti servizio autonomo in Windows Server | Microsoft Azure"
   description="Aggiornare il codice servizio tessuti e/o una configurazione che viene eseguito un cluster di tessuti servizio autonomo, inclusa l'impostazione delle modalità di aggiornamento cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Aggiornamento di un cluster tessuti servizio autonomo in Windows Server

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonomo](service-fabric-cluster-upgrade-windows-server.md)

Per i sistemi moderno, progettazione per l'aggiornamento è fondamentale per ottenere il successo a lungo termine del prodotto. Un cluster di servizio tessuti è una risorsa che si è proprietari. Questo articolo descrive come è possibile assicurarsi che il cluster esegue sempre versioni di tessuti servizio configurazioni e codice.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllare la versione di tessuti che viene eseguito il cluster

È possibile impostare il cluster per il download di aggiornamenti di tessuti del servizio quando Microsoft rilascia una nuova versione o scegliere per selezionare una versione supportata tessuti si desidera che il cluster essere acceso. 

È mediante l'impostazione di configurazione del cluster "fabricClusterAutoupgradeEnabled" su true o false.


>[AZURE.NOTE] Assicurarsi di mantenere il cluster sempre in esecuzione una versione supportata di servizio tessuti. Come e quando le ultime del rilascio di una nuova versione di tessuti servizio, la versione precedente è contrassegnata per fine del supporto tecnico dopo un minimo di 60 giorni che vanno dalla data. Le nuove versioni sono annunciata [nel blog del team servizio tessuti](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nuova versione è disponibile per scegliere quindi. 


È possibile aggiornare il cluster alla nuova versione solo se si utilizza una configurazione nodo lo stile di produzione, in cui ogni nodo servizio tessuti allocata in una macchina virtuale o fisica separata. Se si dispone di un cluster di sviluppo, in cui sono presenti più nodi tessuti servizio in un singolo fisici o virtuali, è necessario chiudere il cluster e ricrearlo con la nuova versione.


Sono disponibili due flussi di lavoro distinti per l'aggiornamento del cluster a più tardi o una versione di tessuti supportati. Uno per i cluster che dispone di una connessione per scaricare la versione più recente automaticamente e la seconda per i cluster che non sono connettività a scaricare la versione più recente di servizio tessuti.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster con connettività a scaricare il codice e configurazione più recenti 

Utilizzare questa procedura per aggiornare il cluster a una versione supportata se i nodi del cluster dispongano la connettività internet per [http://download.microsoft.com](http://download.microsoft.com) 

Per i cluster che dispone di una connessione a [http://download.microsoft.com](http://download.microsoft.com), abbiamo periodicamente verificare la disponibilità di nuove versioni di tessuti di servizio.


Quando una nuova versione tessuti servizio è disponibile, il pacchetto viene scaricato localmente al cluster e viene completato il provisioning per l'aggiornamento. Inoltre per informare i clienti di questa nuova versione, viene inserito automaticamente un avviso di sicurezza cluster espliciti simile al seguente:

"Cluster quella corrente [versione #] il supporto termina [Data].", dopo il cluster è in esecuzione la versione più recente, il messaggio di avviso si risolve.


#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro aggiornamento cluster.
 
Una volta che viene visualizzato l'avviso di sicurezza cluster, è necessario eseguire le operazioni seguenti:

1. Connettersi al cluster da qualsiasi computer dotato di accesso come amministratore per tutti i computer elencati come nodi del cluster. Il computer in cui viene eseguito questo script non può essere parte del cluster

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Ottenere l'elenco di tessuti servizio versioni che è possibile eseguire l'aggiornamento

    ```powershell

    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    si dovrebbe ottenere un risultato simile alla seguente:

    ![ottenere tessuti versioni][getfabversions]

3. Avviare un aggiornamento di cluster a una delle versioni è disponibile tramite [PowerShell Start ServiceFabricClusterUpgrade cmd](https://msdn.microsoft.com/library/mt125872.aspx)

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
È possibile monitorare l'avanzamento dell'aggiornamento explorer tessuti servizio o eseguendo il seguente comando shell power

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the Start-ServiceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Dopo aver risolto i problemi che hanno generato il ripristino, è necessario avviare di nuovo l'aggiornamento seguendo la stessa procedura descritta in precedenza.


### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Aggiornare i cluster con <U>nessuna connettività</u> per scaricare il codice e configurazione più recenti

Utilizzare questa procedura per aggiornare il cluster a una versione supportata se il cluster nodi **non dispone di** connettività internet per [http://download.microsoft.com](http://download.microsoft.com) 


>[AZURE.NOTE]Se si esegue un cluster che non è connessi a internet, sarà necessario monitorare il servizio di blog del team tessuti per ricevere una notifica di una nuova versione. Il sistema **non** inserire alcun avviso integrità cluster per avvisare che ne.  

1. Modificare la configurazione del cluster per impostare le proprietà seguenti su false.

        "fabricClusterAutoupgradeEnabled": false,

e avviare un aggiornamento di configurazione. Fare riferimento [all'inizio ServiceFabricClusterUpgrade PS cmd](https://msdn.microsoft.com/library/mt125872.aspx) per dettagli relativi all'uso. La versione manifesto cluster è la versione che si è nel clusterConfig.JSON. Assicurarsi di aggiornarla prima dell'avvio dell'aggiornamento di configurazione.

```powershell

    Start-ServiceFabricClusterUpgrade [-Config] [-ClusterConfigVersion] -FailureAction Rollback -Monitored 

```

#### <a name="cluster-upgrade-workflow"></a>Flusso di lavoro aggiornamento cluster.
 


1. Scaricare la versione più recente del pacchetto dal documento [Crea servizio tessuti cluster per windows server](service-fabric-cluster-creation-for-windows-server.md) 


1. Connettersi al cluster da qualsiasi computer dotato di accesso come amministratore per tutti i computer elencati come nodi del cluster. Il computer in cui viene eseguito questo script non può essere parte del cluster 

    ```powershell

    ###### connect to the cluster
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3" 
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Copiare il pacchetto scaricato l'archivio di immagini cluster.

    ```powershell

    ###### Get the list of available service fabric versions 
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

    ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"


    ```

2. Registrare il pacchetto copiato 

    ```powershell

    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 

    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```


3. Avviare un aggiornamento di cluster a una delle versioni è disponibile. 

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
    
    ```
È possibile monitorare l'avanzamento dell'aggiornamento explorer tessuti servizio o eseguendo il seguente comando shell power

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    If the cluster health policies are not met, the upgrade is rolled back. You can specify custom health policies at the time for the start-serviceFabricClusterUpgrade command refer to [this document](https://msdn.microsoft.com/library/mt125872.aspx) for details. 

Dopo aver risolto i problemi che hanno generato il ripristino, è necessario avviare di nuovo l'aggiornamento seguendo la stessa procedura descritta in precedenza.



## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come personalizzare alcune delle [impostazioni del servizio tessuti cluster tessuti](service-fabric-cluster-fabric-settings.md)
- Informazioni su come [ridurre i cluster avanti e indietro](service-fabric-cluster-scale-up-down.md)
- Informazioni sugli [Aggiornamenti applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG
