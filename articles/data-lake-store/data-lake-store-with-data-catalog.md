<properties
   pageTitle="Registrare i dati dall'archivio dati Lake nel catalogo dati di Azure | Microsoft Azure"
   description="Registrare i dati dall'archivio dati Lake nel catalogo dati di Azure"
   services="data-lake-store,data-catalog" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Registrare i dati dall'archivio dati Lake nel catalogo dati di Azure

In questo articolo si imparerà a integrare Azure dati Lake Store con il catalogo dati di Azure per rendere il all'interno dell'organizzazione dei dati mediante l'integrazione con catalogo dati. Per ulteriori informazioni su catalogo dati, vedere [Catalogo dati di Azure](../data-catalog/data-catalog-what-is-data-catalog.md). Per comprendere gli scenari in cui è possibile utilizzare il catalogo dati, vedere [scenari comuni di catalogo dati di Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Attivare l'abbonamento Azure** per dati Lake archivio Public Preview. Vedere [le istruzioni](data-lake-store-get-started-portal.md#signup).

- **Account azure dati Lake Store**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md). Per questa esercitazione, possiamo creare un account di archivio di dati Lake denominato **datacatalogstore**. 

    Dopo aver creato l'account, caricare un set di dati di esempio. Per questa esercitazione, possiamo caricare tutti i file con estensione csv nella cartella **AmbulanceData** [Repository fra Lake dei dati di Azure](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). È possibile usare vari client, ad esempio [Esplora archivi Azure](http://storageexplorer.com/), per caricare dati in un contenitore di blob.

- **Catalogo dati di azure**. L'organizzazione deve dispone già di un catalogo di dati di Azure creati per l'organizzazione. Per ogni organizzazione è consentito solo un catalogo.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registro dati Lake Store come origine per il catalogo dati

>[AZURE.VIDEO adcwithadl] 

1. Passare a `https://azure.microsoft.com/services/data-catalog`e fare clic su **per iniziare**.

2. Accedere al portale di catalogo dati di Azure e fare clic su **Pubblica dati**.

    ![Registrare un'origine dati] (./media/data-lake-store-with-data-catalog/register-data-source.png "Registrare un'origine dati")

3. Nella pagina successiva, fare clic su **Avvia applicazione**. Questa operazione verrà scaricate file manifesto dell'applicazione nel computer in uso. Fare doppio clic sul file manifesto per avviare l'applicazione.

4. Nella pagina di benvenuto, fare clic su **Accedi**e immettere le credenziali.

    ![Schermata iniziale] (./media/data-lake-store-with-data-catalog/welcome.screen.png "Schermata iniziale")

5. Nella schermata selezionare un'origine dati, selezionare **Lake di dati di Azure**e quindi fare clic su **Avanti**.

    ![Selezionare l'origine dati] (./media/data-lake-store-with-data-catalog/select-source.png "Selezionare l'origine dati")

6. Nella pagina successiva, specificare il nome dell'account archivio Lake dati che si desidera registrare nel catalogo dei dati. Lasciare le altre opzioni come predefinito e quindi fare clic su **Connetti**.

    ![Connettersi all'origine dati] (./media/data-lake-store-with-data-catalog/connect-to-source.png "Connettersi all'origine dati")

7. Nella pagina successiva dividono in segmenti seguenti.

    un. La casella **Gerarchia Server** rappresenta la struttura delle cartelle archivio dati Lake account. **$Root** rappresenta la radice di account di archivio di dati Lake e **AmbulanceData** rappresenta la cartella creata nella radice del conto dati Lake Store.

    b. La casella di **oggetti disponibili** sono elencati i file e cartelle nella cartella **AmbulanceData** .

    c. **Oggetti registrati nella finestra di** sono elencati i file e cartelle che si desidera registrare nel catalogo dati di Azure.

    ![Struttura di dati di visualizzazione] (./media/data-lake-store-with-data-catalog/view-data-structure.png "Struttura di dati di visualizzazione")

8. Per questa esercitazione, è necessario registrare tutti i file nella directory. Per, fare clic sul pulsante (![spostare oggetti](./media/data-lake-store-with-data-catalog/move-objects.png "spostare oggetti")) per spostare tutti i file nella casella **oggetti da registrare** . 

    Poiché i dati verranno registrati in un catalogo di dati a livello di organizzazione, è un approccio consigliata per aggiungere alcuni metadati che è possibile utilizzare in un secondo momento per trovare rapidamente i dati. Ad esempio, è possibile aggiungere un indirizzo di posta elettronica per il proprietario di dati (ad esempio uno chi sta caricando i dati) o aggiungere un tag per identificare i dati. Nella schermata seguente mostra un tag aggiunte ai dati.

    ![Struttura di dati di visualizzazione] (./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Struttura di dati di visualizzazione")

    Fare clic su **Registra**.

8. Nella schermata seguente indica che i dati sia registrati correttamente nel catalogo dati.

    ![Registrazione completata] (./media/data-lake-store-with-data-catalog/registration-complete.png "Struttura di dati di visualizzazione")

9. Fare clic su **Visualizzazione portale** per tornare al portale di catalogo dati e verificare che sia possibile accedere a questo punto dati registrato dal portale di. Per cercare i dati, è possibile utilizzare il tag utilizzato durante la registrazione dei dati.

    ![Dati di ricerche nel catalogo] (./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Dati di ricerche nel catalogo")

10. È ora possibile eseguire operazioni come l'aggiunta di annotazioni e la documentazione per i dati. Per ulteriori informazioni, vedere i collegamenti seguenti.
    * [Aggiungere note alle origini dati nel catalogo dei dati](../data-catalog/data-catalog-how-to-annotate.md)
    * [Origini dati di documenti nel catalogo dei dati](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Vedere anche

* [Aggiungere note alle origini dati nel catalogo dei dati](../data-catalog/data-catalog-how-to-annotate.md)
* [Origini dati di documenti nel catalogo dei dati](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrare dati Lake Store con altri servizi di Azure](data-lake-store-integrate-with-other-services.md)
