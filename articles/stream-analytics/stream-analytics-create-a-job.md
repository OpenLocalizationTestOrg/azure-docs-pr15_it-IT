<properties 
    pageTitle="Creazione di un processo di elaborazione analitica dati per flusso Analitica | Microsoft Azure" 
    description="Creare un processo di elaborazione analitica dei dati per flusso Analitica | segmento di percorso di apprendimento."
    keywords="elaborazione analitica dei dati"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/> 

# <a name="how-to-create-a-data-analytics-processing-job-for-stream-analytics"></a>Creazione di un processo di elaborazione analitica dei dati per flusso Analitica

La risorsa principale in Azure flusso Analitica è un processo Analitica flusso.  È costituita da una o più origini dati di input, una query di esprimere la trasformazione dei dati e una o più destinazioni di output che vengono salvati i risultati. Insieme questi consentono di eseguire analitica dati elaborazione per lo streaming scenari di dati.

Per iniziare a usare flusso Analitica, iniziare creando un nuovo processo flusso Analitica.  Si noti che questa azione non è alcun implicazioni fatturazione fino a quando non viene avviato il processo.

1.  Accedere a online di [Azure portale classico](http://manage.windowsazure.com) o il [portale di Azure](https://portal.azure.com/).
2.  Nel portale: **Fare clic su Nuovo**, quindi fare clic su **Servizi di dati** o **Dati Analitica** a seconda del portale e quindi fare clic su **Azure flusso Analitica** o **Analitica flusso** e quindi **Creare rapido**.

    ![Elaborazione analitica dati procedura guidata](./media/stream-analytics-create-a-job/1-stream-analytics-create-a-job.png)  

    ![Creare processi di elaborazione analitica di dati](./media/stream-analytics-create-a-job/4-stream-analytics-create-a-job.png)  

3.  Specificare la configurazione desiderata per il processo di flusso Analitica.
    - Nella casella **Nome processo** immettere un nome per identificare il processo di flusso Analitica. Quando il **Nome del processo** viene convalidata, viene visualizzato un segno di spunta verde nella casella Nome processo. Il **Nome del processo** può contenere solo caratteri alfanumerici e '-' caratteri e deve essere compreso tra 3 e 63 caratteri.
    - Utilizzare **l'area geografica** nel portale di Azure o **posizione** nel portale di Azure per specificare l'area geografica in cui si vuole eseguire il processo.
    - Se tramite il portale di Azure, selezionare o creare un account di archiviazione da utilizzare come **Account di archiviazione monitoraggio internazionali**. Questo account di archiviazione viene utilizzato per archiviare i dati di monitoraggio di tutti i processi di flusso Analitica in esecuzione in quest'area.
    - Se tramite il portale di Azure, specificare un **Gruppo di risorse** di nuovo o esistente per contenere le relative risorse per l'applicazione.

4.  Dopo aver configurate le nuove opzioni di processo flusso Analitica, fare clic su **Crea flusso Analitica processo**. È possibile richiedere alcuni minuti per il processo di flusso Analitica da creare. Per controllare lo stato, è possibile monitorare l'avanzamento nell'hub notifiche.

    ![Hub di notfications processo di elaborazione di dati analitica](./media/stream-analytics-create-a-job/2-stream-analytics-create-a-job.png)  

    ![Processo di creazione di Azure analitica dati portale l'elaborazione di processo](./media/stream-analytics-create-a-job/5-stream-analytics-create-a-job.png)  

5.  Verrà visualizzato il nuovo processo con lo stato **creato**. Si noti che viene disabilitato dal pulsante **Start** . È necessario configurare il processo input, query e l'output prima di iniziare il processo.

    ![Processo di processo di elaborazione analitica dati sullo stato](./media/stream-analytics-create-a-job/3-stream-analytics-create-a-job.png)  

    ![Azure analitica dati portale l'elaborazione di stato del processo di processo](./media/stream-analytics-create-a-job/6-stream-analytics-create-a-job.png)  

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
