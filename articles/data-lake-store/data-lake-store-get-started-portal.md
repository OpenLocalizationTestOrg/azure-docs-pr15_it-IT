<properties 
   pageTitle="Guida introduttiva a archivio dati Lake | Azure" 
   description="Utilizzare il portale per creare un account di archivio di dati Lake ed eseguire operazioni di base nell'archivio dati Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Linguaggio SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI Azure](data-lake-store-get-started-cli.md)
- [Node](data-lake-store-manage-use-nodejs.md)

Informazioni su come usare il portale di Azure per creare un account Azure dati Lake Store ed eseguire le operazioni di base, ad esempio durante la creazione di cartelle, caricare e scaricare file di dati, eliminare l'account e così via. Per ulteriori informazioni sui dati Lake Store, vedere [Panoramica di Azure Lake archivio](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-fast-with-videos"></a>Trovare rapidamente con video?

Guardare i video seguenti per iniziare a utilizzare dati Lake Store.

* [Creare un account di archivio di dati Lake](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gestire i dati nell'archivio di Lake dati tramite Esplora aree di dati](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account Azure dati Lake archivio

1. Effettuare l'accesso con il nuovo [Portale Azure](https://portal.azure.com).

2. Fare clic su **Nuovo**, fare clic su **dati + spazio di archiviazione**e quindi fare clic su **Archivio Lake dati di Azure**. Leggere le informazioni e **l'Archivio Lake dati di Azure** e quindi fare clic su **Crea** nell'angolo inferiore sinistro della stessa e.

3. In e il **Nuovo archivio Lake** , fornire i valori, come illustrato nella schermata seguente:

    ![Creare un nuovo account Azure dati Lake archivio] (./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Creare un nuovo account Azure dati Lake")

    - **Abbonamento**. Selezionare l'abbonamento in cui si desidera creare un nuovo account di archivio di dati Lake.
    - **Gruppo di risorse**. Selezionare un gruppo di risorse esistente oppure fare clic su **Crea un gruppo di risorse** per crearne uno. Un gruppo di risorse è un contenitore che contiene le relative risorse per un'applicazione. Per ulteriori informazioni, vedere [Gruppi di risorse in Azure](azure-resource-manager/resource-group-overview.md#resource-groups).
    - **Posizione**: selezionare un percorso in cui si desidera creare l'account di archivio di dati Lake.

4. Selezionare **Aggiungi a Startboard** se si desidera che l'account di archivio di dati Lake per essere accessibili dalla Startboard.

5. Fare clic su **Crea**. Se si sceglie di aggiungere l'account per il startboard, viene visualizzata nuovamente la startboard ed è possibile visualizzare l'avanzamento del provisioning degli account per l'archivio Lake dati. Una volta che viene completato il provisioning account archivio Lake dati, e il conto viene visualizzata.

6. Espandere le **Nozioni di base** in giù per visualizzare che le informazioni relative all'account di archivio di dati Lake quali la risorsa raggruppare gli argomenti sono una parte, il percorso e così via. Fare clic sull'icona di **Avvio rapido** per visualizzare i collegamenti ad altre risorse relative all'archivio Lake dati.

    ![Account utente archivio Lake dati di Azure] (./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Account utente Lake di dati di Azure")

## <a name="createfolder"></a>Creare cartelle in account Azure dati Lake archivio

È possibile creare cartelle con l'account archivio Lake dati per gestire e archiviare i dati.

1. Aprire l'account di archivio di dati Lake appena creata. Dal riquadro di sinistra, fare clic su **Sfoglia**e quindi scegliere il nome dell'account in cui si desidera creare cartelle e il Lake archivio **Dati Lake Store**. Se è bloccato l'account per il startboard, fare clic su riquadro tale account.

2. In blade l'account archivio Lake dati, fare clic su **Esplora dati**.

    ![Creazione di cartelle nell'archivio dati Lake account] (./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Creazione di cartelle nell'archivio dati Lake account")

3. In blade l'account archivio Lake dati, fare clic su **Nuova cartella**, immettere un nome per la nuova cartella e quindi fare clic su **OK**.
    
    ![Creazione di cartelle nell'archivio dati Lake account] (./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Creazione di cartelle nell'archivio dati Lake account")
    
    La cartella appena creata sarà elencata in e il **Data Explorer** . È possibile creare cartelle nidificate fino a qualsiasi livello.

    ![Creazione di cartelle nell'account Lake dati] (./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Creazione di cartelle nell'account Lake dati")


## <a name="uploaddata"></a>Caricare dati account Azure dati Lake archivio

È possibile caricare i dati a un account Azure dati Lake archivio direttamente al livello radice o in una cartella che è stato creato all'interno dell'account. Nella schermata di sotto, seguire la procedura per caricare un file in una sottocartella da e il **Data Explorer** . In questa schermata, il file viene caricato in una sottocartella nella barra di navigazione (contrassegnati in un riquadro rosso).

Se si sta cercando alcuni dati di esempio caricare, è possibile ottenere la cartella **Ambulanza dati** dal [Repository fra Lake dei dati di Azure](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Caricare dati] (./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Caricare dati")


## <a name="properties"></a>Proprietà e azioni disponibili per i dati archiviati

Fare clic sul file appena aggiunto per aprire e il **proprietà** . Le proprietà associate al file e le azioni eseguite sul file sono disponibili in questo blade. È anche possibile copiare il percorso completo file nell'account Azure dati Lake archivio evidenziato nella casella rossa nella schermata seguente.

![Proprietà sui dati] (./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Proprietà sui dati")

* Fare clic su **Anteprima** per visualizzare un'anteprima del file direttamente dal browser. È possibile specificare il formato dell'anteprima anche. Fare clic su **Anteprima**, fare clic su **formato** e il **File di anteprima** e nella e **Formato di File di anteprima** selezionare le opzioni, ad esempio numero di righe da visualizzare, codifica da utilizzare, delimitatore da utilizzare e così via.

  ![Formato di file di anteprima] (./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato di file di anteprima")

* Fare clic su **Download** per scaricare il file nel computer in uso.

* Fare clic su **Rinomina file** per rinominare il file.

* Fare clic su **Elimina file** per eliminare il file.


## <a name="secure-your-data"></a>Protezione dei dati

È possibile proteggere i dati archiviati in account Azure dati Lake Store con Azure Active Directory e il controllo di accesso (ACL). Per istruzioni su come eseguire questa operazione, vedere [protezione dei dati di Azure dati Lake Store](data-lake-store-secure-data.md).


## <a name="delete-azure-data-lake-store-account"></a>Account Elimina Azure dati Lake archivio

Per eliminare un account Azure dati Lake Store da blade l'archivio Lake dati, fare clic su **Elimina**. Per confermare l'azione, verrà richiesto di immettere il nome dell'account che si desidera eliminare. Immettere il nome dell'account e quindi fare clic su **Elimina**.

![Elimina dati Lake account] (./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Elimina dati Lake account")


## <a name="next-steps"></a>Passaggi successivi

- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
- [Usare dati di Azure Lake Analitica con dati Lake archivio](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con dati Lake archivio](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Log diagnostici di Access per archivio Lake dati](data-lake-store-diagnostic-logs.md)
