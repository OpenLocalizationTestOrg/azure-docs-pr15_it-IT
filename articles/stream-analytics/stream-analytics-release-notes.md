<properties 
    pageTitle="Note sulla versione flusso Analitica | Microsoft Azure" 
    description="Note sulla versione Analitica flusso" 
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

#<a name="stream-analytics-release-notes"></a>Note sulla versione Analitica flusso

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Note sulla versione 15/04/2016 di flusso Analitica ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Disponibilità generale per output di Power BI  | [Power BI output](stream-analytics-power-bi-dashboard.md) sono in genere disponibili. È stata rimossa la scadenza di autorizzazione 90 giorni per Power BI. Per ulteriori informazioni sugli scenari in cui deve essere rinnovato autorizzazione vedere la sezione [rinnovare autorizzazione](stream-analytics-power-bi-dashboard.md#Renew-authorization) di creazione di un dashboard di Power BI.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Note della versione di flusso Analitica 03/03/2016 ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Nuovi elementi del linguaggio di Query Analitica flusso  | SAQL è ora disponibile [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "Pagina MSDN GetType"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "Pagina MSDN TRY_CAST") e [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN pagina").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 10/12/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Aggiornamento di versione API REST | La versione API REST è stata aggiornata a 2015-10-01. Sono disponibili informazioni dettagliate su MSDN [Riferimento all'API REST di flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx) e [l'integrazione di apprendimento in flusso Analitica](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Integrazione di apprendimento Machine Azure | Con questa versione include il supporto per Azure apprendimento funzioni definite dall'utente. Vedere l' [esercitazione](stream-analytics-machine-learning-integration-tutorial.md) per ulteriori informazioni, nonché [annuncio blog generale](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Note sulla versione 11/12/2015 di flusso Analitica ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Nuovo comportamento della selezione | Fare clic su nel flusso Analitica è stata estesa per consentire * come una funzione di accesso di un record annidato. Per ulteriori informazioni, consultare [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Tipi di dati complessi").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 22/10/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Funzionalità del linguaggio di query aggiuntive | Flusso Analitica è espanso il linguaggio di query facilmente individuabili perché includono le seguenti caratteristiche: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [ACCEDI](https://msdn.microsoft.com/library/azure/mt605290.aspx), [QUADRATA](https://msdn.microsoft.com/library/azure/mt605288.aspx)e [RADQ](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Rimuovere le limitazioni di aggregazione  | In questa versione rimuove la limitazione di 15 aggregazioni in una query. Non è ora disponibile nessun limite al numero di aggregazioni per ogni query.
Caratteristica di gruppo da System.Timestamp aggiunta | La funzione [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) consente ora per window_type o [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
OFFSET aggiunto per lo scambio e Hopping windows | Per impostazione predefinita, [lo scambio](https://msdn.microsoft.com/library/azure/dn835055.aspx) e [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows allineati rispetto al tempo uguali a zero (1/1/0001 12:00:00 AM UTC). Il nuovo parametro (facoltativo) 'offsetsize' consente di specificare un offset personalizzato (o allineamento).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 29/09/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo | Descrizione
---|---
Anteprima pubblica di Azure IoT famiglia di prodotti | Flusso Analitica è incluso nel riquadro di anteprima pubblico della famiglia di prodotti IoT Azure.
Integrazione di portale Azure | Oltre a "Continua" presenza nel portale di gestione di Azure, flusso Analitica è ora integrato nel [Portale di Azure](https://azure.microsoft.com/overview/preview-portal/). Nota che Analitica flusso funzionalità nel portale di Preview è attualmente disponibile un sottoinsieme delle funzionalità offerte nel portale di gestione di Azure, senza il supporto per la verifica del browser query, Power BI output configurazione e la selezione o la creazione di nuovo input e risorse di output negli abbonamenti a cui a che si dispone dell'accesso.
Supporto per l'output DocumentDB | Processi Analitica flusso eseguire a questo punto l'output in [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Supporto per l'input IoT Hub | Processi Analitica flusso ora in grado di acquisire dati da hub IoT.
TIMESTAMP, per gli eventi eterogenei | Quando un unico flusso di dati contiene più tipi di evento verificano timestamp in campi diversi, è ora possibile usare [TIMESTAMP,](http://msdn.microsoft.com/library/mt573293.aspx) con le espressioni per specificare i campi di data e ora diverso per ogni caso.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 09/10/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo|Descrizione
---|---
Supporto per i gruppi di ottenere informazioni|Per abilitare la condivisione dei dati con altri utenti di Power BI, processi flusso Analitica ora possono scrivere ai [gruppi di ottenere informazioni](stream-analytics-define-outputs.md#power-bi) all'interno di account Power BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 20/08/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo|Descrizione
---|---
Funzione ultima aggiunta |La funzione [ultima](http://msdn.microsoft.com/library/mt421186.aspx) è ora disponibile in processi flusso Analitica, che consente di recuperare l'evento più recente in un flusso di eventi all'interno di un determinato intervallo di tempo.
Nuove funzioni di matrice|Funzioni di matrice [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) e [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sono ora disponibili.
Nuove funzioni di Record|Le funzioni di registrazione [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) e [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sono ora disponibili.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 30/07/2015 ##

In questa versione sono i seguenti aggiornamenti.

Titolo|Descrizione
---|---
Org Id di Power BI disaccoppiato da Azure Id|Questa caratteristica consente [l'output di Power BI](stream-analytics-power-bi-dashboard.md) per i processi ASA in qualsiasi tipo di account Azure (Live Id o Org Id). È inoltre disponibile un Id organizzazione per l'account Azure e utilizzare una diversa per l'autorizzazione di output di Power BI.
Supporto per l'output di servizio Bus code|[Servizio Bus code](stream-analytics-define-outputs.md#service-bus-queues) output sono ora disponibili in processi flusso Analitica.
Supporto per l'output di servizio Bus argomenti|Output di [Servizio Bus argomenti](stream-analytics-define-outputs.md#service-bus-topics) sono ora disponibili in processi flusso Analitica.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 09/07/2015 ##

In questa versione sono i seguenti aggiornamenti.


Titolo|Descrizione
---|---
Personalizza Blob partizione di Output|Output di spazio di archiviazione BLOB consente ora un'opzione per specificare la frequenza di tale scritti BLOB e la struttura e il formato della struttura di output dati percorso cartella. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 05/03/2015 ##

In questa versione sono i seguenti aggiornamenti.


Titolo|Descrizione
---|---
Aumento del valore massimo ordine tolleranza finestra|Le dimensioni massime della finestra di tolleranza ordine sono ora 59:59 (mm.ss)
Formato di Output JSON: Riga o in una matrice|A questo punto è un'opzione durante la creazione di archiviazione Blob o un evento Hub per generare l'output come una matrice di oggetti JSON o separando oggetti JSON con una nuova riga. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Note della versione di flusso Analitica 16/04/2015 ##


Titolo|Descrizione
---|---
Ritardi nella configurazione dell'account di archiviazione Azure|Quando si crea un processo Analitica flusso in un'area per la prima volta, verrà richiesto di creare un nuovo account di archiviazione o specificare un account esistente per Monitoraggio flusso Analitica in quell'area. A causa di latenza per la configurazione di monitoraggio, creazione di un altro processo flusso Analitica nella stessa regione entro 30 minuti verrà richiesta la specifica di un account di archiviazione secondo anziché con quello recente configurato nell'elenco a discesa monitoraggio Account di archiviazione. Per evitare la creazione di un account di archiviazione non necessarie, attendere 30 minuti dopo la creazione di un processo in un'area per la prima volta prima di provisioning ulteriori processi in tale area.
Aggiornamento del processo|Al momento Analitica flusso non supporta le modifiche live per la definizione o la configurazione di un processo in esecuzione. Per modificare l'input, output, query, scala o configurazione di un processo in esecuzione, è innanzitutto necessario interrompere il processo.
Tipi di dati dedotti dall'origine di input|Se non viene utilizzata un'istruzione CREATE TABLE, il tipo di input dipende dal formato di input, ad esempio tutti i campi da CSV stringa. I campi devono essere convertito in modo esplicito nel tipo giusto tramite la funzione CAST per evitare errori di tipo non corrispondente.
Campi mancanti restituiti come valori null|Riferimento a un campo che non sono presente nell'origine di input verrà generato l'evento di output di valori null.
CON le istruzioni precedere istruzioni SELECT|Nella query, le istruzioni SELECT devono seguire sottoquery definite con le istruzioni.
Problema di memoria insufficiente|Riavviare flussi processi Analitica con una tolleranza di grandi dimensioni per gli eventi in ordine e/o query complesse mantenendo che una grande quantità di stato può causare il processo per esaurirsi di memoria in un processo. Le operazioni di inizio e fine saranno visibili nei log operazione del processo. Per evitare questo comportamento, adattarsi la query su più partizioni. Nelle versioni future, questa limitazione verrà risolto in termini di prestazioni sui processi interessati anziché il riavvio di loro.
Input di grandi dimensioni blob senza timestamp payload può causare problemi di memoria insufficiente|Utilizzo di file di grandi dimensioni dallo spazio di archiviazione Blob potrebbe causare processi flusso Analitica subisce un arresto anomalo se tramite TIMESTAMP, non viene specificato in un campo di data e ora. Per evitare questo problema, mantenere ogni blob di dimensioni inferiori a 10MB di dimensione.
Limitazione volume di eventi del Database di SQL|Quando si usano Database SQL come destinazione di output, volumi elevati di dati di output possono causare il processo di flusso Analitica timeout. Per risolvere il problema, ridurre il volume di output con aggregazioni o gli operatori di filtro o scegliere invece archiviazione Blob Azure o evento hub come destinazione di output.
Set di dati di ottenere informazioni può contenere solo una tabella|Ottenere informazioni non supporta più di una tabella in un determinato set di dati.

## <a name="get-help"></a>È possibile ottenere assistenza
Per ulteriori informazioni, provare al [forum di Azure flusso Analitica](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione al flusso Azure Analitica](stream-analytics-introduction.md)
- [Introduzione all'utilizzo di Azure flusso Analitica](stream-analytics-get-started.md)
- [Scala Azure flusso Analitica processi](stream-analytics-scale-jobs.md)
- [Guida di riferimento Query flusso Azure Analitica](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Riferimento all'API REST di Azure flusso Analitica gestione](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 
