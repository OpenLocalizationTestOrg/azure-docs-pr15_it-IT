<properties
    pageTitle="Dashboard di Power BI in un flusso Analitica | Microsoft Azure"
    description="Utilizzare un dashboard di Power BI di trasmissione in tempo reale per raccogliere funzionalità di business intelligence e analizzare i dati di volumi elevati da un processo di flusso Analitica."
    keywords="dashboard analitica, dashboard in tempo reale"
    services="stream-analytics"
    documentationCenter=""
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

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Flusso Analitica e Power BI: un dashboard in tempo reale analitica per il flusso di dati

Azure Analitica flusso consente di sfruttare uno l'iniziale strumenti di business intelligence, Microsoft Power BI. Informazioni su come usare Azure flusso Analitica per analizzare i volumi elevati, flusso di dati e ottenere informazioni in un dashboard di analitica Power BI in tempo reale.

Utilizzare [Microsoft Power BI](https://powerbi.com/) per creare rapidamente un dashboard live. [Guardare un video che illustra lo scenario](https://www.youtube.com/watch?v=SGUpT-a99MA).

In questo articolo viene illustrato come creare i proprio strumenti di business intelligence aziendali personalizzate tramite Power BI come un output per i processi di Azure flusso Analitica e utilizzare un dashboard in tempo reale.

## <a name="prerequisites"></a>Prerequisiti di

* Account di Microsoft Azure
* Input per il processo di flusso Analitica utilizzare dati flussi da. Flusso Analitica accetta input dallo spazio di archiviazione Azure evento hub o Blob Azure.  
* Account aziendale o dell'istituto di istruzione per Power BI

## <a name="create-azure-stream-analytics-job"></a>Creare il processo di Azure flusso Analitica

Dal [Portale classica di Azure](https://manage.windowsazure.com), fare clic su **Nuovo, ODS, flusso Analitica, creazione rapida**.

Specificare i valori seguenti, quindi fare clic su **Crea Analitica flusso del processo**:

* **Nome del processo** : immettere un nome di processo. Ad esempio **DeviceTemperatures**.
* **Area** - selezionare l'area geografica in cui si desidera il processo che si trova. È consigliabile inserire il processo e l'hub di evento nella stessa regione per garantire prestazioni ottimali ed evitare sostenere i costi di trasferimento dati tra le aree.
* **Account di archiviazione** - scegliere l'Account di archiviazione che si desidera utilizzare per archiviare i dati di monitoraggio per tutti i processi di flusso Analitica in esecuzione all'interno di quest'area. Viene offerta la possibilità di scegliere un Account di archiviazione esistente o crearne uno nuovo.

Fare clic su **Flusso Analitica** nel riquadro a sinistra per visualizzare un elenco di processi flusso Analitica.

![1][graphic1]

> [AZURE.TIP] Verrà visualizzato il nuovo processo con lo stato **Non avviata**. Si noti che viene disabilitato dal pulsante **Start** nella parte inferiore della pagina. È previsto come è necessario configurare il processo input, output, query, e così via prima di iniziare il processo.

## <a name="specify-job-input"></a>Specificare l'input di processo

Per questa esercitazione si suppone che si utilizza Hub evento come input con la serializzazione JSON e la codifica UTF-8.

* Scegliere il nome del processo.
* Fare clic su **input** nella parte superiore della pagina e quindi fare clic su **Aggiungi Input**. Finestra di dialogo visualizzata consentirà numerosi passaggi per configurare l'input.
*   Selezionare **flusso di dati**e quindi fare clic sul pulsante a destro.
*   Selezionare **Hub evento**e quindi scegliere il pulsante destro.
*   Digitare o selezionare i valori seguenti nella terza pagina:
  * **Alias Input** - immettere un nome descrittivo per il processo di input. Si noti che si userà il nome della query in un secondo momento.
  * **Hub evento** - se Hub evento creato nella stessa sottoscrizione processo flusso Analitica, selezionare lo spazio dei nomi di hub evento.
*   Se l'hub di evento in una sottoscrizione diversa, selezionare **Usa evento Hub da un'altra sottoscrizione** e immettere manualmente le informazioni per ** **Servizio Bus Namespace**evento Hub Name**, **Nome del criterio Hub evento**, **Chiave dei criteri di evento Hub**e **Conteggio partizione Hub eventi**.

> [AZURE.NOTE]  In questo esempio viene utilizzato il numero predefinito di partizioni, 16.

* **Nome dell'evento Hub** - selezionare il nome dell'evento Hub Azure si dispone.
* **Nome del criterio Hub evento** - selezionare il criterio di evento Hub per l'Hub di evento in uso. Assicurarsi che questo criterio è gestire le autorizzazioni.
*   **Gruppo Consumer Hub di eventi** : È possibile lasciare vuoto questo campo oppure specificare un gruppo di consumer si dispone di un centro di raccolta eventi. Si noti che ogni gruppo consumer di un Hub di evento può avere solo 5 lettori alla volta. E quindi decidere di conseguenza il gruppo consumer appropriata per il lavoro. Se si omette il campo, utilizzerà il gruppo di consumer predefinito.

*   Scegliere il pulsante destro.
*   Specificare i valori seguenti:
  * **Evento serializzatore formato** - JSON
  * **Codifica** : UTF8
*   Fare clic sul pulsante di controllo per aggiungere questa origine e verificare che flusso Analitica è possibile connettersi all'hub di evento.

## <a name="add-power-bi-output"></a>Aggiungere l'output di Power BI

1.  Fare clic su **Output** nella parte superiore della pagina e quindi fare clic su **Aggiungi Output**. Verrà visualizzata che Power BI è elencata come un'opzione di output.

    ![immagine 2][graphic2]  

2.  Selezionare **Power BI** e quindi fare clic sul pulsante a destro.
3.  Verrà visualizzata una schermata simile alla seguente:

    ![graphic3][graphic3]  

4.  In questo passaggio, fornire un account aziendale o dell'istituto di istruzione per l'output di processo flusso Analitica. Se si dispone già di account di Power BI, selezionare **Autorizzare ora**. In caso contrario, scegliere **Iscriviti ora**. [Ecco un buon blog attraverso i dettagli di iscrizione a Power BI](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Successivamente si verrà visualizzata una schermata simile alla seguente:

    ![graphic4][graphic4]  

Fornire i valori come indicato di seguito:

* **Alias di output** : È possibile inserire qualsiasi alias di output di facile per è possibile fare riferimento. Questo alias di output è particolarmente utile se si dispone di più output per il lavoro. In questo caso, è necessario consultare l'output della query. Ad esempio, utilizzare il valore di alias di output = "OutPbi".
* **Nome del set di dati** - specificare un nome di set di dati che si desidera disporre di output di Power BI. Ad esempio, utilizzare "pbidemo".
*   **Nome tabella** : specificare un nome di tabella sotto il set di dati di output di Power BI. Supponiamo che si chiama "pbidemo". Attualmente, l'output di Power BI processi flusso Analitica può avere una tabella in un set di dati.
*   **Area di lavoro** , selezionare un'area di lavoro nel tenant di Power BI in cui verrà creato il set di dati.

>   [AZURE.NOTE] Non in modo esplicito creare questo set di dati e tabella nell'account di Power BI. Verrà creati automaticamente quando si avvia il processo di flusso Analitica e si avvia il processo output pompaggio in Power BI. Se la query di processo non restituisce risultati, il set di dati e la tabella non verrà creati.

*   Fare clic su **OK**, **Connessione di prova** e ora si genera l'output configurazione del è stata completata.

>   [AZURE.WARNING] Tenere inoltre presente che se Power BI dispone già di un set di dati e una tabella con lo stesso nome di quella indicata in questa posizione lavorativa flusso Analitica, verranno sovrascritti i dati esistenti.


## <a name="write-query"></a>Scrivere query

Fare clic sulla scheda **Query** del lavoro. Scrivere la query, che l'output di cui si desidera di Power BI. La, ad esempio, potrebbe essere simile, ad esempio la query SQL seguente:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Avviare il processo. Verificare che l'hub di evento riceve eventi e la query genera i risultati previsti. Se la query restituisce 0 righe, tabelle e set di dati di Power BI vengono non create automaticamente.

## <a name="create-the-dashboard-in-power-bi"></a>Creare dashboard in Power BI

Passare a [Powerbi.com](https://powerbi.com) e accedere con l'account aziendale o dell'istituto di istruzione. Se la query di processo flusso Analitica restituisce risultati, verrà visualizzato che il set di dati è già stato creato:

![graphic5][graphic5]

Per la creazione di dashboard, passare all'opzione dashboard e creare un nuovo Dashboard.

![graphic6][graphic6]

In questo esempio abbiamo verrà etichetta è "Demo Dashboard".

Fare clic su set di dati creati dal processo del flusso Analitica (pbidemo nell'esempio corrente). Per creare un grafico nella parte superiore di questo set di dati, sarà accedere a una pagina. Di seguito è ma un esempio di report che è possibile creare:

Selezionare Σ temp e l'ora di campi. Verranno automaticamente accedono al valore e l'asse del grafico:

![graphic7][graphic7]

Con questo valore, si riceverà automaticamente un grafico come indicato di seguito:

![graphic8][graphic8]

Nella sezione valore fare clic sull'elenco a discesa verso il basso temp e selezionare **Media** per la temperatura e del grafico, fare clic su **visualizzazione** e selezionare **grafico a linee**:

![graphic9][graphic9]

A questo punto, verrà visualizzato un grafico a linee di Media nel tempo.  Usa l'opzione Aggiungi come indicato di seguito, è possibile aggiungere questo al dashboard creato in precedenza:

![graphic10][graphic10]

Quando si visualizza il dashboard con questo report bloccato, verrà visualizzata l'aggiornamento di report in tempo reale. Provare a modificare i dati negli eventi – temp raccoglitore o qualcosa di simile a quella e si vedrà succede in tempo reale che riportati nel dashboard.

Nota che questa esercitazione è stato illustrato come creare ma un tipo di grafico per un set di dati. Power BI è possibile crearne altri strumenti di business intelligence del cliente per l'organizzazione. Per un altro esempio di un dashboard di Power BI, guardare il video [Introduzione a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Per ulteriori informazioni sulla configurazione di un output di Power BI e utilizzare gruppi di Power BI, consultare la [sezione di Power BI](stream-analytics-define-outputs.md#power-bi) di [output Understanding flusso Analitica](stream-analytics-define-outputs.md "produce comprensione flusso Analitica"). Un'altra risorsa utile per ulteriori informazioni sulla creazione di dashboard con Power BI è [dashboard in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitazioni e le procedure consigliate

Power BI impiega vincoli concorrenza e la velocità effettiva come descritto di seguito: [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Prezzi di Power BI")

A causa di quelli Power BI recapitata più naturale nei casi in cui Azure flusso Analitica svolge una riduzione di caricamento dati significativi.
È consigliabile utilizzare TumblingWindow o HoppingWindow per garantire che push dei dati sarebbero al massimo 1 push/secondo e che la query recapitata all'interno i requisiti di velocità – è possibile utilizzare l'equazione seguente per calcolare il valore per assegnare la finestra in secondi:

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Ad esempio: se si dispone di 1.000 dispositivi invio dei dati di ogni secondo, ci si trova nello SKU Power BI Pro che supporta 1.000.000 righe/ora e si desidera ottenere dati Media per dispositivo in Power BI è possibile eseguire almeno un push ogni 4 secondi per dispositivo (come illustrato di seguito):  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Che indica che è necessario modificare la query originale per:

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Aggiornamento di visualizzazione di ottenere informazioni

Domande frequenti sono "Perché non dashboard di aggiornamento automatico, ottenere informazioni?".

A tale scopo, di ottenere informazioni utilizzare domande e risposte e porre una domanda, ad esempio "Valore massimo da temp dove Timestamp oggi è" e aggiungere tale riquadro per il dashboard.

### <a name="renew-authorization"></a>Rinnovo di autorizzazione

È necessario ripetere l'autenticazione account Power BI se la password del proprio stato modificato dopo il lavoro è stato creato o dell'ultima autenticazione. Se l'autenticazione a più fattori (MFA) è configurato nel tenant di Azure Active Directory (AAD) sarà anche necessario rinnovare l'autorizzazione di Power BI ogni 2 settimane. Un indicatore di questo problema è alcun output di processo e un "autenticazione utente errore" nei log operazione:

![graphic12][graphic12]

Analogamente, se un processo tenta di avviare mentre il token è scaduto, si verifica un errore e avvio del processo avrà esito negativo. L'errore sarà simile a come il seguente:

![Errore di convalida di ottenere informazioni](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Per risolvere il problema, interrompere il lavoro in esecuzione e passare all'output di Power BI.  Fare clic sul collegamento "Rinnovare autorizzazione" e riavviare il processo durante l'ultimo arrestato per evitare perdite di dati.

![Ottenere informazioni rinnovo di convalida](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Dopo l'autorizzazione viene aggiornata con Power BI verrà visualizzato un avviso di colore verde nell'area di autorizzazione:

![Ottenere informazioni rinnovo di convalida](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png
