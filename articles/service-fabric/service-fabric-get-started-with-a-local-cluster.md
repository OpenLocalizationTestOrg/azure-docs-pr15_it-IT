<properties
   pageTitle="Guida introduttiva a distribuire e aggiornare App il cluster locale | Microsoft Azure"
   description="Configurare un cluster di servizio tessuti locale, distribuire un'applicazione esistente e quindi eseguire l'aggiornamento dell'applicazione."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Iniziare con la distribuzione e aggiornamento di applicazioni il cluster locale
Azure servizio tessuti SDK include un ambiente di sviluppo locale completo che è possibile utilizzare per iniziare rapidamente con la distribuzione e gestione delle applicazioni in un cluster locale. In questo articolo si crea un cluster locale, distribuire un'applicazione esistente e quindi aggiorna l'applicazione a una nuova versione da Windows PowerShell.

> [AZURE.NOTE] In questo articolo si presuppone che è già stato [configurato l'ambiente di sviluppo](service-fabric-get-started.md).

## <a name="create-a-local-cluster"></a>Creare un cluster locale
Un cluster di servizio tessuti rappresenta un set di risorse hardware che è possibile distribuire applicazioni per. In genere, un cluster è costituito da un punto qualsiasi da cinque a migliaia di computer. Tuttavia, Service tessuti SDK include una configurazione cluster che è possibile eseguire su un unico computer.

È importante tenere presente che il cluster locale tessuti servizio non sia un emulatore o simulatore. Esegue lo stesso codice piattaforma disponibile su cluster più computer. L'unica differenza è che venga eseguito i processi di piattaforma che sono in genere suddivisi in cinque computer in un computer.

SDK in due modi per configurare un cluster locale: uno script di Windows PowerShell e l'app sulla barra delle applicazioni del sistema di gestione Cluster locale. In questa esercitazione si utilizzare lo script di PowerShell.

> [AZURE.NOTE] Se è già stato creato un cluster locale distribuendo un'applicazione da Visual Studio, è possibile ignorare questa sezione.


1. Avviare una nuova finestra di PowerShell come amministratore.

2. Eseguire lo script di installazione cluster dalla cartella SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```

    La configurazione cluster consente di qualche minuto. Al termine dell'installazione, è necessario vedere output simile a:

    ![Output di configurazione cluster][cluster-setup-success]

    A questo punto si è pronti provare a distribuire un'applicazione per il cluster.

## <a name="deploy-an-application"></a>Distribuire un'applicazione
Service tessuti SDK include una vasta gamma di Framework e sviluppo alternativo per la creazione di applicazioni. Se si è interessati imparare a creare applicazioni in Visual Studio, vedere [creare la prima applicazione di servizio tessuti in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

In questa esercitazione verrà usata un'applicazione di esempio esistente (denominata WordCount) in modo che è possibile concentrarsi su aspetti della gestione della piattaforma, ossia la distribuzione, monitoraggio e aggiornamento.


1. Avviare una nuova finestra di PowerShell come amministratore.

2. Importare il modulo servizio tessuti SDK PowerShell.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Creare una directory per l'archiviazione dell'applicazione in cui è possibile scaricare e distribuire, ad esempio C:\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Scaricare l'applicazione WordCount](http://aka.ms/servicefabric-wordcountapp) nella posizione è stata creata.  Nota: il browser Microsoft Edge consente di salvare il file con estensione *zip* .  Modificare l'estensione di file in *.sfpkg*.

5. Connettersi a cluster locale:

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Creare una nuova applicazione tramite il comando di distribuzione del SDK con un nome e un percorso per il pacchetto dell'applicazione.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Se tutto va bene, è necessario verificare output simile al seguente:

    ![Distribuire un'applicazione in cluster locale][deploy-app-to-local-cluster]

7. Per visualizzare l'applicazione in azione, aprire il browser e passare a [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Verrà visualizzato:

    ![Applicazione distribuita dell'interfaccia utente][deployed-app-ui]

    L'applicazione WordCount è molto semplice. Include codice JavaScript lato client per generare casuale cinque caratteri "le parole", che vengono quindi inoltrate all'applicazione tramite API Web ASP.NET. Un servizio informazioni sullo stato tiene traccia del numero di parole contati. Si sono suddivisi in base al primo carattere della parola. È possibile trovare il codice sorgente per l'app WordCount nella [Guida introduttiva esempi](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/).

    L'applicazione è distribuiti contiene quattro partizioni. In modo che le parole che iniziano con una lettera e G sono archiviate in prima partizione, le parole che iniziano con H a N sono archiviate nella seconda partizione e così via.

## <a name="view-application-details-and-status"></a>Visualizzare i dettagli dell'applicazione e lo stato
Ora che è stato distribuito l'applicazione, esaminiamo alcuni dettagli app PowerShell.

1. Tutte le applicazioni nel cluster della query:

    ```powershell
    Get-ServiceFabricApplication
    ```

    Supponendo che è stata distribuita solo app WordCount, visualizzata sarà simile a:

    ![Tutte le applicazioni di PowerShell della query][ps-getsfapp]

2. Passare a livello avanzato eseguendo il set di servizi inclusi nell'applicazione WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Servizi di elenco per l'applicazione di PowerShell][ps-getsfsvc]

    L'applicazione è costituito da due servizi - web front-end e il servizio informazioni sullo stato che gestisce le parole.

3. Infine, consultare l'elenco delle partizioni per WordCountService:

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Visualizzare le partizioni del servizio di PowerShell][ps-getsfpartitions]

    L'insieme di comandi che è stata utilizzata, ad esempio servizio tessuti PowerShell di tutti i comandi disponibili per qualsiasi cluster che è possibile connettersi a locale o remoto.

    Per interagire con i cluster in modo visivo, è possibile utilizzare lo strumento di servizio tessuti Explorer basata sul web, passare a [http://localhost:19080/Explorer](http://localhost:19080/Explorer) nel browser.

    ![Visualizzare i dettagli dell'applicazione di servizio tessuti Explorer][sfx-service-overview]

    > [AZURE.NOTE] Per ulteriori informazioni su Esplora tessuti servizio, vedere [la visualizzazione del cluster con Esplora risorse tessuti servizio](service-fabric-visualizing-your-cluster.md).

## <a name="upgrade-an-application"></a>Aggiornare un'applicazione
Servizio tessuti offre aggiornamenti no tempo di inattività, il monitoraggio dell'integrità dell'applicazione come che transita all'interno del cluster. Di seguito eseguire un semplice aggiornamento dell'applicazione WordCount.

La nuova versione dell'applicazione ora conta solo le parole che iniziano con una vocale. Durante l'aggiornamento transita, è possibile vedere due modifiche di comportamento dell'applicazione. Prima di tutto necessario rallentare la frequenza con cui aumenta il numero totale di, poiché vengono contate le parole di un numero minore. In secondo luogo, poiché la prima partizione ha due vocali (a ed E) e tutte le altre partizioni contengano un solo ogni, il conteggio dovrebbe iniziare alla fine a outpace sulle altre colonne.

1. [Scaricare il pacchetto di v2 WordCount](http://aka.ms/servicefabric-wordcountappv2) nella stessa posizione in cui è stato scaricato il pacchetto v1.

2. Tornare alla finestra di PowerShell e utilizzare comando per l'aggiornamento del SDK per registrare la nuova versione del cluster. Iniziare l'aggiornamento dell'infrastruttura: / applicazione WordCount.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Verrà visualizzato output di PowerShell che ha un aspetto simile al seguente come l'aggiornamento inizia.

    ![Aggiornare lo stato di avanzamento di PowerShell][ps-appupgradeprogress]

3. Durante l'aggiornamento è procedere, può risultare più facile monitorare lo stato dal servizio tessuti Explorer. Avviare una finestra del browser e passare a [Http://localhost:19080/soluzioni](http://localhost:19080/Explorer). Espandere **applicazioni** nell'albero a sinistra, quindi scegliere **WordCount**e infine **tessuti: / WordCount**. Nella scheda essentials, verrà visualizzato lo stato dell'aggiornamento durante il trasferimento attraverso domini l'aggiornamento del cluster.

    ![Aggiornare lo stato di avanzamento in Esplora tessuti servizio][sfx-upgradeprogress]

    Durante l'aggiornamento a ogni dominio, viene verificata l'integrità per assicurarsi che l'applicazione funziona correttamente.

4. Se è eseguire nuovamente la query precedente per il set di servizi di infrastruttura: / WordCount applicazione, si noti che la versione di WordCountService modificata ma la versione di WordCountWebService non:

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Servizi dell'applicazione della query dopo l'aggiornamento][ps-getsfsvc-postupgrade]

    Evidenzia come servizio tessuti vengono gestiti gli aggiornamenti delle applicazioni. Tocca solo il set di servizi (o pacchetti di codice/configurazione all'interno di tali servizi) che sono state modificate, semplificando così il processo di aggiornamento più veloce e affidabile.

5. Infine, tornare al browser per osservare il comportamento della nuova versione dell'applicazione. Come previsto, il numero totale di avanzamento più lentamente e la prima partizione finisce per con leggermente più del volume.

    ![Visualizzare la nuova versione dell'applicazione nel browser][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Pulizia

Prima di conclusioni, è importante tenere presente che il cluster locale è di tipo real. Le applicazioni continuano per l'esecuzione in background finché non viene rimosso.  A seconda della natura delle applicazioni, un'app in esecuzione può richiedere risorse significative nel computer in uso. Sono disponibili diverse opzioni per la gestione applicazioni e il cluster:

1. Per rimuovere una singola applicazione e i relativi dati, eseguire le operazioni seguenti:

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    In alternativa, è possibile eliminare l'applicazione di servizio tessuti Explorer dal menu **Azioni** o menu di scelta rapida nella visualizzazione elenco applicazione del riquadro sinistro.

    ![Eliminare un'applicazione è servizio tessuti Explorer][sfe-delete-application]

2. Dopo l'eliminazione dell'applicazione dal cluster, è possibile annullare la registrazione di versioni 1.0.0 e 2.0.0 del tipo di applicazione WordCount. L'eliminazione rimuove i pacchetti di applicazioni, incluso il codice e configurazione di archivio di immagini del cluster.

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    In alternativa, in Esplora tessuti servizio, scegliere **Il tipo di annullamento del provisioning** per l'applicazione.

3. Per arrestare il cluster, mantenendo i dati delle applicazioni e tracce, fare clic su **Interrompi Cluster locale** nell'app sulla barra delle applicazioni di sistema.

4. Per eliminare completamente il cluster, fare clic su **Rimuovi Cluster locale** nell'app sulla barra delle applicazioni di sistema. Questa opzione verrà generato in un'altra distribuzione lenta la volta successiva che si preme F5 in Visual Studio. Rimuovere il cluster locale solo se non si prevede di usarlo per un certo tempo o se è necessario recuperare risorse.

## <a name="1-node-and-5-node-cluster-mode"></a>1 nodo e 5 modalità cluster di nodo

Quando si utilizza il cluster locale per lo sviluppo di applicazioni, che spesso svolte rapide iterazioni di codice, il debug e modifica di codice, debug e così via. Per informazioni su come ottimizzare il processo, è possibile eseguire il cluster locale in due modi: 1 nodo o 5. Entrambe le modalità cluster sono i vantaggi.
Modalità di cluster nodo 5 consente di lavorare con un cluster reale. È possibile testare il failover scenari, lavorare con più istanze e repliche dei servizi.
1 modalità cluster nodo ottimizzata per eseguire la distribuzione rapida e la registrazione dei servizi per convalidare rapidamente codice utilizzando il runtime del servizio tessuti.

1 nodo cluster modalità sia la modalità di 5 nodo cluster non è un emulatore o simulatore. Esegue lo stesso codice piattaforma disponibile su cluster più computer.

> [AZURE.NOTE] Questa caratteristica è disponibile in SDK versione 5.2 e versioni successive.

Per modificare la modalità cluster in un cluster di nodo 1, usare Gestione Cluster di servizio tessuti locale o utilizzare PowerShell nel modo seguente:

1. Avviare una nuova finestra di PowerShell come amministratore.

2. Eseguire lo script di installazione cluster dalla cartella SDK:

    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```

    La configurazione cluster consente di qualche minuto. Al termine dell'installazione, è necessario vedere output simile a:
    
    ![Output di configurazione cluster][cluster-setup-success-1-node]

Se si utilizza il gestore Cluster locale tessuti:

![Cambia modalità di raggruppamento][switch-cluster-mode]

> [AZURE.WARNING] Quando si modifica la modalità cluster, cluster corrente viene rimosso dal sistema e viene creato un nuovo cluster. I dati è necessario archiviati in cluster, verranno eliminati quando si modifica la modalità cluster.

## <a name="next-steps"></a>Passaggi successivi
- Ora che è distribuito e aggiornare alcune applicazioni predefinite, è possibile [provare a creare nel forum relativo a Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Tutte le azioni eseguite sul cluster locale in questo articolo possono essere eseguite in un [cluster di Azure](service-fabric-cluster-creation-via-portal.md) anche.
- L'aggiornamento eseguite in questo articolo è stato base. Vedere [la documentazione di aggiornamento](service-fabric-application-upgrade.md) per ulteriori informazioni su come le caratteristiche degli aggiornamenti del servizio tessuti.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png
