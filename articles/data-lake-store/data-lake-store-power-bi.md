<properties
   pageTitle="Analizzare i dati nell'archivio Lake dati usando Power BI | Microsoft Azure"
   description="Usare Power BI per analizzare i dati archiviati nell'archivio Lake dati di Azure"
   services="data-lake-store" 
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analizzare i dati nell'archivio Lake dati usando Power BI

In questo articolo si imparerà a usare Power BI Desktop per analizzare e visualizzare i dati archiviati nell'archivio Lake dati di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Azure un abbonamento**. Vedere [ottenere Azure versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Account azure dati Lake Store**. Seguire le istruzioni nella [Guida introduttiva di Azure dati Lake archivio tramite il portale di Azure](data-lake-store-get-started-portal.md). In questo articolo si presuppone che ha creato un account di archivio di dati Lake, denominato **mybidatalakestore**e caricare un file di dati di esempio (**Drivers.txt**). Il file di esempio è disponibile per il download da [Azure dati Lake fra Repository](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).

- **Power BI Desktop**. È possibile scaricare [dall'Area Download Microsoft](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 


## <a name="create-a-report-in-power-bi-desktop"></a>Creare un report in Power BI Desktop

1. Avviare Power BI Desktop del computer.

2. Dalla barra multifunzione **Home** , fare clic su **Recupera dati**e quindi fare clic su altro. Nella finestra di dialogo **Carica dati** fare clic su **Azure**, fare clic su **Archivio Lake dati di Azure**e quindi fare clic su **Connetti**.

    ![Connettersi all'archivio dati Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account.png "Connettersi all'archivio dati Lake")

3. Se viene visualizzato una finestra di dialogo informazioni sul connettore in fase di sviluppo, scegliere di continuare.

4. Nella finestra di dialogo **Archivio Lake dati di Microsoft Azure** specificare l'URL per l'account di archivio Lake dati e quindi fare clic su **OK**.

    ![URL per l'archivio Lake dati] (./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "URL per l'archivio Lake dati")

5. Nella finestra di dialogo successiva, fare clic su **Accedi** per accedere a account archivio Lake dati. Si verrà reindirizzati alla pagina di accesso dell'organizzazione. Seguire le istruzioni per accedere con l'account.

    ![Accedere all'archivio dati Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Accedere all'archivio dati Lake")

6. Dopo aver completato l'accesso, fare clic su **Connetti**.

    ![Connettersi all'archivio dati Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Connettersi all'archivio dati Lake")

7. Nella finestra di dialogo successiva verrà visualizzato il file caricato al proprio account archivio Lake dati. Verificare le informazioni e quindi fare clic su **Carica**.

    ![Carica dati dall'archivio dati Lake] (./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Carica dati dall'archivio dati Lake")

8. Dopo i dati sono stati caricati in Power BI, verranno visualizzati i campi seguenti nella scheda **campi** .

    ![Campi importati] (./media/data-lake-store-power-bi/imported-fields.png "Campi importati")

    Tuttavia, per visualizzare e analizzare i dati, è preferibile i dati siano disponibili per i campi seguenti

    ![Campi desiderato] (./media/data-lake-store-power-bi/desired-fields.png "Campi desiderato")

    Nei passaggi successivi Microsoft aggiornerà la query per convertire i dati importati nel formato desiderato.

9. Dalla barra multifunzione **Home** , fare clic su **Modifica query**.

    ![Modificare le query] (./media/data-lake-store-power-bi/edit-queries.png "Modificare le query")

10. Nell'Editor di Query nella colonna del **contenuto** , fare clic su **binario**.

    ![Modificare le query] (./media/data-lake-store-power-bi/convert-query1.png "Modificare le query")

11. Verrà visualizzata un'icona file, che rappresenta il file **Drivers.txt** caricato. Pulsante destro del mouse sul file e fare clic su **CSV**.  

    ![Modificare le query] (./media/data-lake-store-power-bi/convert-query2.png "Modificare le query")

12. Verrà visualizzato un output, come illustrato di seguito. Sono ora disponibili in un formato che è possibile utilizzare per creare visualizzazioni dei dati.

    ![Modificare le query] (./media/data-lake-store-power-bi/convert-query3.png "Modificare le query")

13. Dalla barra multifunzione **Home** , fare clic su **Applica e Chiudi**e quindi fare clic su **Chiudi e applicare**.

    ![Modificare le query] (./media/data-lake-store-power-bi/load-edited-query.png "Modificare le query")

14. Dopo la query viene aggiornata, la scheda **campi** illustra i nuovi campi disponibili per la visualizzazione.

    ![Campi di aggiornamento] (./media/data-lake-store-power-bi/updated-query-fields.png "Campi di aggiornamento")

15. Creazione automatica di un grafico a torta per rappresentare i driver di ogni città per un paese. A tale scopo, effettuare le seguenti selezioni.

    1. Nella scheda visualizzazioni fare clic sul simbolo per un grafico a torta.

        ![Creare grafici a torta] (./media/data-lake-store-power-bi/create-pie-chart.png "Creare grafici a torta")

    2. Le colonne che verranno utilizzare sono **colonna 4** (nome della città) e **colonna 7** (nome del paese). Trascinare queste colonne dalla scheda **campi** alla scheda **visualizzazioni** come illustrato di seguito.

        ![Creare visualizzazioni] (./media/data-lake-store-power-bi/create-visualizations.png "Creare visualizzazioni")

    3. Grafico a torta sarà ora simile simile a quello illustrato di seguito.

        ![Grafico a torta] (./media/data-lake-store-power-bi/pie-chart.png "Creare visualizzazioni")

16. Se si seleziona un paese specifico dai filtri a livello di pagina, è ora possibile visualizzare il numero di driver di ogni città del paese selezionato. Ad esempio, nella scheda **visualizzazioni** in **filtri a livello di pagina**, selezionare **Brasile**.

    ![Selezionare un paese] (./media/data-lake-store-power-bi/select-country.png "Selezionare un paese")

17. Grafico a torta viene aggiornato automaticamente per visualizzare i driver in città del Brasile.

    ![Driver in un paese] (./media/data-lake-store-power-bi/driver-per-country.png "Driver per paese")

18. Dal menu **File** fare clic su **Salva** per salvare la visualizzazione in un file di Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Pubblicare report di servizio Power BI

Dopo aver creato le visualizzazioni in Power BI Desktop, è possibile condividere con altri utenti pubblicandolo in servizio Power BI. Per istruzioni su come eseguire questa operazione, vedere [pubblicazione da Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Vedere anche

* [Analizzare i dati in archivio Lake Analitica Lake dati](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
