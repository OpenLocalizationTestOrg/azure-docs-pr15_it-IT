<properties
   pageTitle="Configurare un cluster di servizio tessuti utilizzando Visual Studio | Microsoft Azure"
   description="Descrive come configurare un cluster di servizio tessuti tramite Gestione risorse Azure modello creato da un progetto di gruppo di risorse Azure in Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/06/2016"
   ms.author="karolz@microsoft.com"/>

# <a name="set-up-a-service-fabric-cluster-by-using-visual-studio"></a>Configurare un cluster di servizio tessuti mediante Visual Studio
In questo articolo viene descritto come configurare un cluster di Azure servizio tessuti mediante Visual Studio e un modello di gestione di risorse Azure. Un progetto di gruppo di risorse di Visual Studio Azure verrà usata per creare il modello. Dopo aver creato il modello, può essere distribuito direttamente in Azure da Visual Studio. Può inoltre essere utilizzato da uno script o come parte del fondo integrazione continua (CI).

## <a name="create-a-service-fabric-cluster-template-by-using-an-azure-resource-group-project"></a>Creare un modello di cluster tessuti servizio tramite un progetto di gruppo di risorse Azure
Per iniziare, aprire Visual Studio e creare un progetto di gruppo di risorse Azure (è disponibile nella cartella **Cloud** ):

![Finestra di dialogo Nuovo progetto con project gruppo risorse Azure selezionata][1]

Creare una nuova soluzione di Visual Studio per il progetto o aggiungerla a una soluzione esistente.

>[AZURE.NOTE] Se non è disponibile il progetto di gruppo risorse Azure nodo Cloud, non è installato il SDK Azure. Avviare l'installazione guidata piattaforma Web ([installarlo ora](http://www.microsoft.com/web/downloads/platform.aspx) se si dispone non è già) e quindi eseguire una ricerca per "Azure SDK per .NET" e installare la versione è compatibile con la versione di Visual Studio.

Dopo avere di premere il pulsante OK, Visual Studio viene richiesto di selezionare il modello di Manager delle risorse che si desidera creare:

![Finestra di dialogo di Azure modello seleziona con il modello di servizio tessuti Cluster selezionato][2]

Selezionare il modello di servizio tessuti Cluster e fare nuovamente clic sul pulsante OK. Il progetto e il modello di gestione risorse è stati creati.

## <a name="prepare-the-template-for-deployment"></a>Preparare il modello per la distribuzione
Prima che il modello viene distribuito per creare il cluster, è necessario specificare i valori per i parametri di modello richiesto. Questi valori di parametro vengono letti dalla `ServiceFabricCluster.parameters.json` file, ossia nel `Templates` cartella del progetto di gruppo di risorse. Aprire il file e specificare i valori seguenti:

|Nome parametro           |Descrizione|
|-----------------------  |--------------------------|
|adminUserName            |Il nome dell'account dell'amministratore per tessuti servizio computer (nodi).|
|certificateThumbprint    |Identificazione personale del certificato che consente di proteggere il cluster.|
|sourceVaultResourceId    |*ID risorsa* dell'archivio di chiave in cui è archiviato il certificato che consente di proteggere il cluster.|
|certificateUrlValue      |L'URL del certificato di sicurezza cluster.|

Il modello di gestione risorse di Visual Studio servizio tessuti crea un cluster sicuro è protetto da un certificato. Il certificato è identificato dai parametri del modello ultime tre (`certificateThumbprint`, `sourceVaultValue`, e `certificateUrlValue`), e deve essere presente in un **Archivio di chiave Azure**. Per ulteriori informazioni su come creare il certificato di sicurezza cluster, vedere l'articolo [tessuti servizio cluster scenari di protezione](service-fabric-cluster-security.md#x509-certificates-and-service-fabric) .

## <a name="optional-change-the-cluster-name"></a>Facoltativo: modificare il nome del cluster
Ogni cluster tessuti servizio ha un nome. Quando viene creato un cluster di tessuti in Azure, il nome del sistema DNS (Domain Name) per il cluster nome cluster determina (insieme area Azure). Ad esempio, se è il nome del cluster `myBigCluster`e il percorso (area Azure) del gruppo di risorse contenenti il nuovo cluster è Stati Uniti orientali, il nome DNS del cluster sarà `myBigCluster.eastus.cloudapp.azure.com`.

Per impostazione predefinita il nome del cluster generato automaticamente e univoco associando un suffisso casuale a un prefisso "cluster". In questo modo molto semplice da utilizzare il modello come parte di un sistema di **integrazione continua** (CI). Se si desidera utilizzare un nome per il cluster specifico, ovvero significativo per l'utente, impostare il valore della `clusterName` variabile nel file del modello Manager delle risorse (`ServiceFabricCluster.json`) sul proprio nome scelto. È la prima variabile definita in tale file.

## <a name="optional-add-public-application-ports"></a>Facoltativo: aggiungere le porte applicazione pubblico
È anche possibile scegliere di modificare le porte applicazione pubblico per il cluster prima di distribuirlo. Per impostazione predefinita, il modello apre solo due porte TCP pubbliche (80 e 8081). Se sono necessarie informazioni per le applicazioni, modificare la definizione di bilanciamento del carico Azure nel modello. La definizione viene memorizzata nel file del modello principale (`ServiceFabricCluster.json`). Aprire il file e cercare `loadBalancedAppPort`. Ogni porta è associata a tre elementi:

1. Variabile di modello che definisce il valore di porte TCP per la porta:

    ```json
    "loadBalancedAppPort1": "80"
    ```

2. *Verifica* che definisce la frequenza e la durata del carico Azure bilanciamento tenta di utilizzare un nodo servizio tessuti specifico prima di eseguire il su uno a altro. Le ricerche fanno parte della risorsa di bilanciamento del carico. Ecco la definizione di verifica per la prima porta applicazione predefinita:

    ```json
    {
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
    ```

3. Una *regola di bilanciamento del carico* che collega la porta e la ricerca, che consente di bilanciamento del carico tra un set di nodi cluster tessuti servizio:

    ```json
    {
        "name": "AppPortLBRule1",
        "properties": {
            "backendAddressPool": {
                "id": "[variables('lbPoolID0')]"
            },
            "backendPort": "[variables('loadBalancedAppPort1')]",
            "enableFloatingIP": false,
            "frontendIPConfiguration": {
                "id": "[variables('lbIPConfig0')]"
            },
            "frontendPort": "[variables('loadBalancedAppPort1')]",
            "idleTimeoutInMinutes": 5,
            "probe": {
                "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
            },
            "protocol": "Tcp"
        }
    }
    ```
Se le applicazioni che si desidera distribuire al cluster necessarie più porte, è possibile aggiungere creazione ulteriori analisi e il bilanciamento del carico le definizioni delle regole. Per ulteriori informazioni su come lavorare con bilanciamento del carico Azure tramite modelli di Manager delle risorse, vedere [informazioni introduttive sulla creazione di un utilizzando un modello di bilanciamento del carico interno](../load-balancer/load-balancer-get-started-ilb-arm-template.md).

## <a name="deploy-the-template-by-using-visual-studio"></a>Distribuire il modello utilizzando Visual Studio
Dopo aver salvato tutti i valori di parametro obbligatorio il`ServiceFabricCluster.param.dev.json` file, si è pronti distribuire il modello e creare il cluster servizio tessuti. Fare clic sul progetto gruppo risorse in Esplora soluzioni di Visual Studio e scegliere **Distribuisci | Nuova distribuzione …**. Se necessario, verrà visualizzato della finestra di dialogo **Distribuisci al gruppo di risorse** che richiede l'autenticazione in Azure:

![Distribuire alla finestra di dialogo gruppo di risorse][3]

Nella finestra di dialogo consente di scegliere un gruppo di risorse Manager delle risorse esistente per il cluster e offre la possibilità di crearne uno nuovo. In genere opportuno per usare un gruppo di risorse separato per un cluster di servizio tessuti.

Dopo che fare clic sul pulsante Distribuisci, Visual Studio verrà chiesto di confermare i valori dei parametri di modello. Fare clic sul pulsante **Salva** . Un parametro non è un valore persistente: la password dell'account amministrativo per il cluster. È necessario fornire un valore di password quando viene richiesto Visual Studio per uno.

>[AZURE.NOTE] A partire da Azure SDK 2.9, Visual Studio supporta le password di lettura **dall'Archivio di Azure chiave** durante la distribuzione. Nella finestra di dialogo parametri modello si noti che la `adminPassword` casella di testo parametro ha una piccola icona "chiave" a destra. Questa icona consente di selezionare un segreto archivio chiave esistente come la password amministrativa per il cluster. Assicurarsi che per abilitare primo accesso Manager delle risorse di Azure per la distribuzione dei modelli di criteri di accesso avanzate dell'archivio chiave. 

È possibile monitorare l'avanzamento del processo di distribuzione nella finestra di output Visual Studio. Una volta completata la distribuzione dei modelli, è possibile utilizzare il nuovo cluster!

>[AZURE.NOTE] Se PowerShell non è mai utilizzato per amministrare Azure dal computer in cui è attualmente in uso, è necessario eseguire le attività di manutenzione un po'.
>1. Abilitare gli script di PowerShell eseguendo la [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) comando. Per i computer di sviluppo, il criterio "senza restrizioni" è in genere accettabile.
>2. Decidere se per una raccolta di dati di diagnostica nei comandi PowerShell di Azure ed eseguire [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) o [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) in base alle esigenze. Istruzioni non necessarie evitare durante la distribuzione dei modelli.

Se sono presenti errori, accedere al [portale di Azure](https://portal.azure.com/) e aprire il gruppo di risorse che è stato distribuito a. Fare clic su **tutte le impostazioni**, quindi fare clic su **distribuzioni** sulla e impostazioni. Una distribuzione di gruppo di risorse non riuscita lascia sono informazioni di diagnostica dettagliate.

>[AZURE.NOTE] Servizio tessuti cluster richiedono un certo numero dei nodi sia disponibile per garantire la disponibilità e mantenere stato - denominato "gestione di base". Non è quindi necessario spegnere tutti i computer del cluster a meno che non è stato eseguito prima di tutto un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sulla configurazione cluster tessuti servizio tramite il portale di Azure](service-fabric-cluster-creation-via-portal.md)
- [Informazioni su come gestire e distribuire le applicazioni di servizio tessuti utilizzando Visual Studio](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png
