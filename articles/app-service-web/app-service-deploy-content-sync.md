<properties
    pageTitle="Sincronizza il contenuto da una cartella cloud al servizio App Azure"
    description="Informazioni su come distribuire l'app al servizio App Azure tramite sincronizzazione del contenuto da una cartella cloud."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="sync-content-from-a-cloud-folder-to-azure-app-service"></a>Sincronizza il contenuto da una cartella cloud al servizio App Azure

In questa esercitazione viene descritto come distribuire al [Servizio App Azure](http://go.microsoft.com/fwlink/?LinkId=529714) attraverso la sincronizzazione del contenuto da servizi di archiviazione cloud popolari come Dropbox e OneDrive. 

## <a name="overview"></a>Panoramica della distribuzione di sincronizzazione del contenuto

La distribuzione di sincronizzazione di contenuto su richiesta è una tecnologia il [motore di distribuzione Kudu](https://github.com/projectkudu/kudu/wiki) integrata con il servizio di App. Nel [Portale di Azure](https://portal.azure.com), è possibile scegliere una cartella in archiviazione cloud, utilizzare il proprio codice app e contenuto della cartella e sincronizzare al servizio App con un semplice clic del mouse. Sincronizzazione del contenuto utilizza il processo di Kudu per la compilazione e distribuzione. 
    
## <a name="contentsync"></a>Come attivare la distribuzione di sincronizzazione del contenuto
Per abilitare la sincronizzazione del contenuto dal [Portale di Azure](https://portal.azure.com), procedere come segue:

1. In blade dell'app nel portale di Azure, fare clic su **Impostazioni** > **Origine di distribuzione**. Fare clic su **Scegli origine**e quindi selezionare **OneDrive** o **Dropbox** come origine per la distribuzione. 

    ![Sincronizzazione del contenuto](./media/app-service-deploy-content-sync/deployment_source.png)

    >[AZURE.NOTE] A causa di differenze di base nelle API, **OneDrive for Business** non è supportata in questo momento. 

2. Eseguire il flusso di lavoro di autorizzazione per attivare il servizio di App accedere a un percorso definito predefinito specifico per OneDrive o Dropbox tutto il contenuto di App servizio in cui verrà archiviato.  
    Dopo l'autorizzazione del servizio di App piattaforma fornirà l'opzione per creare una cartella del contenuto in percorso definito contenuto o per scegliere una cartella esistente contenuta in questo percorso contenuto definito. I percorsi contenuti designati in account di archiviazione cloud utilizzato per la sincronizzazione del servizio di App sono i seguenti:  
    * **OneDrive**:`Apps\Azure Web Apps` 
    * **Dropbox**:`Dropbox\Apps\Azure`

3. Dopo la sincronizzazione iniziale del contenuto è possibile avviare la sincronizzazione del contenuto su richiesta dal portale di Azure. La cronologia di distribuzione è disponibile con e il **distribuzioni** .

    ![Cronologia di distribuzione](./media/app-service-deploy-content-sync/onedrive_sync.png)
 
Altre informazioni per la distribuzione di Dropbox sono disponibile in [Distribuisci da Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). 


