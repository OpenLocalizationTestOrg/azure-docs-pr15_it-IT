<properties
    pageTitle="Monitorare lo spazio di archiviazione e le richieste di DocumentDB | Microsoft Azure"
    description="Informazioni su come eseguire il monitoraggio account DocumentDB per metriche di prestazioni, ad esempio gli errori di server e le richieste e metriche di utilizzo, ad esempio il consumo di spazio di archiviazione."
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="monitor-documentdb-requests-usage-and-storage"></a>Monitorare le richieste DocumentDB, utilizzo e lo spazio di archiviazione

È possibile monitorare gli account Azure DocumentDB nel [portale di Azure](https://portal.azure.com/). Per ogni account DocumentDB entrambi metriche di prestazioni, ad esempio le richieste e gli errori del server e metriche di utilizzo, ad esempio il consumo di spazio di archiviazione, sono disponibili.

È possibile esaminare metriche e il conto o su e il nuovo formato.

## <a name="view-performance-metrics-on-the-metrics-blade"></a>Visualizzazione di prestazioni relativi alla e metriche

1. In una nuova finestra, aprire il [portale di Azure](https://portal.azure.com/), fare clic su **Altri servizi**, fare clic su **DocumentDB (NoSQL)**e fare clic sul nome dell'account DocumentDB per il quale si desidera visualizzare dati sulle prestazioni.
2. Nel menu delle risorse, fare clic su **metriche**.

Apre e il metriche ed è possibile selezionare la raccolta per verificare. È possibile esaminare le metriche di disponibilità, le richieste, velocità e lo spazio di archiviazione e confrontarli con contratti di servizio DocumentDB.

## <a name="view-performance-metrics-on-the-account-blade"></a>Visualizzazione di prestazioni relativi alla e conto
1.  In una nuova finestra, aprire il [portale di Azure](https://portal.azure.com/), fare clic su **Altri servizi**, fare clic su **DocumentDB (NoSQL)**e fare clic sul nome dell'account DocumentDB per il quale si desidera visualizzare dati sulle prestazioni.

2.  Per impostazione predefinita, l'obiettivo di **monitoraggio** Visualizza i riquadri seguenti:
    *   Totale richieste per il giorno corrente.
    *   Spazio di archiviazione utilizzato.

    Se la tabella consente di visualizzare **dati non disponibili** e si ritiene che ci siano dati nel database, vedere la sezione [risoluzione dei problemi](#troubleshooting) .

    ![Schermata dell'obiettivo monitoraggio che mostra le richieste e l'utilizzo di spazio di archiviazione](./media/documentdb-monitor-accounts/documentdb-total-requests-and-usage.png)


3.  Fare clic sul **richieste** o **lo spazio di archiviazione** riquadro aprirà blade **unità di misura metriche** dettagliate.
4.  E **l'unità di misura metriche** Mostra i dettagli sul metriche è stato selezionato.  Nella parte superiore e il è un grafico di richieste nel grafico ogni ora e sotto di esso, tabella contenente i valori di aggregazione per le richieste di accelerata e totale.  E il metrico Mostra anche l'elenco di avvisi che sono state definite, filtrata in base ai metriche che vengono visualizzati nella e metrico corrente (in questo modo, se si dispone di un numero di avvisi, verranno visualizzati solo quelle pertinenti presentate di seguito).   

    ![Schermata della stessa e metriche che include limitato richieste](./media/documentdb-monitor-accounts/documentdb-metric-blade.png)


## <a name="customize-performance-metric-views-in-the-portal"></a>Personalizzare le visualizzazioni metriche di prestazioni nel portale

1.  Per personalizzare le metriche che vengono visualizzati in un grafico specifico, fare clic sul grafico per aprirlo in e **l'unità di misura metriche** e quindi fare clic su **Modifica grafico**.  
    ![Schermata dei controlli blade metriche con grafico Modifica evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.  In e il **Grafico di modifica** sono disponibili opzioni per modificare le metriche che vengono visualizzati nel grafico, come l'intervallo di tempo.  
    ![Schermata della stessa e grafico Modifica](./media/documentdb-monitor-accounts/madocdb4.png)

3.  Per modificare le metriche visualizzate nella parte, è sufficiente selezionare o deselezionare le metriche di prestazioni disponibili e quindi fare clic su **OK** nella parte inferiore della stessa e.  
4.  Per modificare l'intervallo di tempo, scegliere un intervallo diverso (ad esempio, **personalizzati**) e quindi fare clic su **OK** nella parte inferiore della stessa e.  

    ![Schermata della parte intervallo di tempo della stessa e modificare grafico che mostra come immettere un intervallo di tempo personalizzato](./media/documentdb-monitor-accounts/madocdb5.png)


## <a name="create-side-by-side-charts-in-the-portal"></a>Creare grafici side-by-side nel portale
Il portale di Azure consente di creare grafici metrici side-by-side.  

1.  Prima di tutto, fare doppio clic sul grafico che si desidera copiare e selezionare **Personalizza**.

    ![Schermata del grafico Totale richieste con l'opzione Personalizza evidenziato](./media/documentdb-monitor-accounts/madocdb6.png)

2.  Fare clic su **Copia** dal menu per copiare la parte e quindi fare clic su **completato la personalizzazione**.

    ![Schermata con il grafico Totale richieste con la copia e completato la personalizzazione opzioni evidenziate](./media/documentdb-monitor-accounts/madocdb7.png)  


Si può ora indicazione questa parte qualsiasi altra parte metrica, personalizzare l'intervallo di criteri di misurazione e l'ora visualizzata nella parte.  In questo modo è possibile visualizzare due diverse metriche grafico side-by-side nello stesso momento.  
    ![Schermata del grafico Totale richieste e le richieste di totale nuovo oltre all'ora grafico](./media/documentdb-monitor-accounts/madocdb8.png)  

## <a name="set-up-alerts-in-the-portal"></a>Impostare avvisi nel portale
1.  Nel [portale di Azure](https://portal.azure.com/), fare clic su **Altri servizi**, fare clic su **DocumentDB (NoSQL)**e quindi fare clic sul nome dell'account DocumentDB per il quale si desidera configurare avvisi metriche di prestazioni.

2.  Nel menu delle risorse, fare clic su **Regole di avviso** per aprire e il regole di avviso.  
    ![Schermata dell'avviso di regole parte selezionata](./media/documentdb-monitor-accounts/madocdb10.5.png)

3.  In e il **regole di avviso** , fare clic su **Aggiungi avviso**.  
    ![Schermata della stessa e le regole di avviso, con il pulsante Aggiungi avviso evidenziato](./media/documentdb-monitor-accounts/madocdb11.png)

4.  Nella finestra e **aggiungere una regola di avviso** specificare:
    *   Il nome della regola di avviso che si sta impostando.
    *   Descrizione della nuova regola di avviso.
    *   Metrica per la regola di avviso.
    *   La condizione, soglia e periodo che determinano quando l'avviso attiva. Ad esempio, un server Errore numero maggiore di 5 su 15 minuti.
    *   Se l'amministratore del servizio e coadministrators vengono inviati tramite posta elettronica quando viene attivata l'avviso.
    *   Indirizzi di posta elettronica aggiuntivi per le notifiche di avviso.  
    ![Schermata di Aggiungi blade una regola di avviso](./media/documentdb-monitor-accounts/madocdb12.png)

## <a name="monitor-documentdb-programatically"></a>Eseguire il monitoraggio a livello di programmazione DocumentDB
Metriche livello account disponibili nel portale, ad esempio richieste di utilizzo e totale di spazio di archiviazione account, non sono disponibili in pratici APIs DocumentDB. Tuttavia, è possibile recuperare dati di utilizzo a livello di raccolta utilizzando APIs DocumentDB. Per recuperare i dati a livello di raccolta, eseguire le operazioni seguenti:

- Per utilizzare l'API REST, [eseguire una GET nella raccolta](https://msdn.microsoft.com/library/mt489073.aspx). Vengono restituite le informazioni di quota e l'utilizzo della raccolta intestazioni di ms di x-quota di risorse e Uso risorse di ms x nella risposta.
- Per utilizzare .NET SDK, utilizzare il metodo [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , che restituisce un [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) che contiene un numero di proprietà di utilizzo, ad esempio **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**e altro ancora.

Per accedere a statistiche aggiuntive, utilizzare [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Tramite la chiamata, è possono recuperare le definizioni di metriche disponibili:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Query per recuperare singoli metriche utilizzare nel formato seguente:

    https://management.azure.com/subscriptions/{SubecriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Per ulteriori informazioni, vedere [Il recupero delle risorse metriche tramite l'API REST di Azure Monitor](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/). Si noti che è stata rinominata "Azure Inights" "Azure Monitor".  Questo post di blog si intende il nome precedente.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se i riquadri monitoraggio visualizzare il messaggio **Nessun dato disponibile** e hai apportate richieste o aggiungere dati al database, è possibile modificare il riquadro per riflettere l'utilizzo di recente.

### <a name="edit-a-tile-to-refresh-current-data"></a>Modificare un riquadro per aggiornare i dati correnti
1.  Per personalizzare le metriche che vengono visualizzati in una parte specifica, fare clic sul grafico per aprire e **l'unità di misura metriche** e quindi fare clic su **Modifica grafico**.  
    ![Schermata dei controlli blade metriche con grafico Modifica evidenziato](./media/documentdb-monitor-accounts/madocdb3.png)

2.  Scegliere e il **Grafico di modifica** , nella sezione **Intervallo di tempo** , fare clic su **oltre all'ora**e quindi fare clic su **OK**.  
    ![Schermata della stessa e grafico Modifica con passata ora selezionata](./media/documentdb-monitor-accounts/documentdb-no-available-data-past-hour.png)


3.  Nel riquadro a questo punto è necessario aggiornare con i dati correnti e l'uso.  
    ![Schermata della aggiornato richieste oltre all'ora riquadro totali](./media/documentdb-monitor-accounts/documentdb-no-available-data-fixed.png)

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla capacità DocumentDB, vedere [gestire DocumentDB capacità](documentdb-manage.md).
