<properties 
   pageTitle="Risolvere i problemi di processi di Azure dati Lake Analitica nel portale di Azure | Azure" 
   description="Informazioni su come usare il portale di Azure per la risoluzione dei processi dati Lake Analitica. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>Risolvere i problemi di processi di Azure dati Lake Analitica nel portale di Azure

Informazioni su come usare il portale di Azure per la risoluzione dei processi dati Lake Analitica.

In questa esercitazione si configura un problema di file di origine mancanti e usare il portale di Azure per risolvere il problema.

**Prerequisiti**

Prima di iniziare questa esercitazione, è necessario disporre le operazioni seguenti:

- **Processo di lavoro knowledge base di dati Lake Analitica**. Vedere [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md).
- **Account A dati Lake Analitica**. Vedere [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md#create-adl-analytics-account).
- **Copiare i dati di esempio per l'account di archivio Lake dati predefinito**.  Vedere [preparare i dati di origine](data-lake-analytics-get-started-portal.md#prepare-source-data)

##<a name="submit-a-data-lake-analytics-job"></a>Inviare un processo dati Lake Analitica

A questo punto si creerà un processo U SQL con un nome di file di origine non validi.  

**Per inviare il processo**

1. Dal portale di Azure, fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic sul riquadro con il nome dell'account dati Lake Analitica.  È stata bloccata qui quando è stato creato l'account.
Se l'account non viene bloccata sono, vedere [apertura di un account Analitica dal portale](data-lake-analytics-manage-use-portal.md#access-adla-account).
3. Fare clic su **Nuovo processo** dal menu superiore.
4. Immettere un nome di processo e lo script U SQL seguente:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv1"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/output/SearchLog-from-adls.csv"
        USING Outputters.Csv();

    File di origine definito lo script è **/Samples/Data/SearchLog.tsv1**, in cui deve essere **/Samples/Data/SearchLog.tsv**.
     
5. Fare clic su **Invia processo** nella parte superiore. Si apre un nuovo riquadro dei dettagli del processo. Sulla barra del titolo, che mostra lo stato del processo. Bastano pochi minuti. È possibile fare clic su **Aggiorna** per ottenere lo stato aggiornato.
6. Attendere finché non viene modificato lo stato del processo a **non riuscita**.  Se il processo non è **riuscita**, è perché non è riuscita rimuovere la cartella /Samples sul. Vedere la sezione **Prerequisiti** all'inizio dell'esercitazione.

È importante sapere - perché richiede così tanto tempo per un processo di piccole dimensioni.  È necessario ricordare che dati Lake Analitica è progettato per l'elaborazione di dati.  Si riflette durante l'elaborazione di grandi quantità di dati tramite il sistema distribuito.

Di seguito presuppongono che è inviato il processo e chiudere il portale.  Nella sezione successiva si imparerà a risolvere i problemi del processo.


## <a name="troubleshoot-the-job"></a>Risoluzione dei problemi del processo

Nella sezione precedente, l'invio di un processo e il processo non è riuscita.  

**Per visualizzare tutti i processi**

1. Dal portale di Azure, fare clic su **Microsoft Azure** nell'angolo superiore sinistro.
2. Fare clic sul riquadro con il nome dell'account dati Lake Analitica.  Viene visualizzato un riepilogo sul riquadro **La gestione del processo** .

    ![Gestione dei processi Analitica Lake di dati di Azure](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)
    
    Il processo di gestione fornisce una panoramica dello stato del processo. Si noti che c'è un processo non riuscito.
   
3. Fare clic sul riquadro di **Gestione del processo** per visualizzare i processi. Sono classificati i processi in **esecuzione**, **in coda**e **finito**. Risulta il processo non riuscito nella sezione **finito** . Deve essere primo nell'elenco. Quando sono presenti molte processi, è possibile fare clic su **filtro** per ottenere assistenza per individuare processi.

    ![Azure dati Lake Analitica filtrare processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. Fare clic su processo non riuscito dall'elenco per aprire i dettagli in una nuova pala:

    ![Azure dati Lake Analitica non è riuscita processo](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)
    
    Si noti il pulsante **Nuovo invio** . Dopo aver risolto il problema, è possibile inviare nuovamente il processo.

5. Fare clic su parte evidenziata dalla schermata precedente per aprire i dettagli dell'errore.  Viene visualizzato è simile a:

    ![Azure dati Lake Analitica non è riuscita i dettagli dei processi](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    Indica che la cartella di origine non viene trovata.
    
6. Fare clic su **Duplica Script**.
7. Aggiornare il percorso **FROM** le operazioni seguenti:

    "/ Samples/Data/SearchLog.tsv"

8. Fare clic su **Invia processo**.


##<a name="see-also"></a>Vedere anche

- [Panoramica di Analitica Lake di dati di Azure](data-lake-analytics-overview.md)
- [Guida introduttiva di Azure dati Lake Analitica tramite PowerShell Azure](data-lake-analytics-get-started-powershell.md)
- [Guida introduttiva a Azure dati Lake Analitica e U-SQL utilizzando Visual Studio](data-lake-analytics-u-sql-get-started.md)
- [Gestire Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-manage-use-portal.md)





