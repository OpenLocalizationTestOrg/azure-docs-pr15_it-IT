<properties 
   pageTitle="Comprensione delle prestazioni Query Database SQL Azure" 
   description="Monitoraggio delle prestazioni di query identifica maggior parte delle query di utilizzo della CPU per un Database di SQL Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Comprensione delle prestazioni Query Database SQL Azure


Gestione e ottimizzazione delle prestazioni dei database relazionali è un'operazione difficile che richiede competenze importanti e investimento di tempo. Comprendere le prestazioni di query consente di ridurre le attività di risoluzione dei problemi di prestazioni del database fornendo i seguenti:

- Approfondita il consumo di risorse (DTU) il database. 
- Principali query dal conteggio di CPU/Durata/esecuzione, potenzialmente può essere ottimizzata per migliorare le prestazioni.
- La possibilità di drill-down dei dettagli di una query, visualizzare il testo e la cronologia di utilizzo delle risorse. 
- Annotazioni che mostrano le azioni eseguite da [Preparazione di Database di SQL Azure](sql-database-advisor.md) l'ottimizzazione delle prestazioni  



## <a name="prerequisites"></a>Prerequisiti

- Comprendere le prestazioni di query è disponibile solo con V12 di Database SQL Azure.
- Comprendere le prestazioni di query è necessario che sia attivo il database di [Archivio di Query](https://msdn.microsoft.com/library/dn817826.aspx) . Se non è in esecuzione Query Store, il portale viene richiesto di attivare il componente aggiuntivo.

 
## <a name="permissions"></a>Autorizzazioni

Per utilizzare comprendere le prestazioni di Query sono necessari le autorizzazioni di [controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-configure.md) seguenti: 

- Per visualizzare le query di tempo superiore delle risorse e i grafici sono necessarie le autorizzazioni di **lettore**, **proprietario**, **collaboratori**, **SQL DB collaboratori**o **Collaboratori di SQL Server** . 
- Per visualizzare il testo della query sono necessarie le autorizzazioni di **proprietario**, **collaboratori**, **SQL DB collaboratori**o **Collaboratori di SQL Server** .



## <a name="using-query-performance-insight"></a>Utilizzo di comprendere le prestazioni di Query

Comprendere le prestazioni di query è facile da usare:

- Aprire [il portale di Azure](https://portal.azure.com/) e trovare database che si desidera esaminare. 
  - Dal menu a sinistra, in supporto e risoluzione dei problemi, selezionare "Comprendere le prestazioni di Query".
- Nella prima scheda, esaminare l'elenco dei principali query utilizzo delle risorse.
- Selezionare una singola query per visualizzarne i dettagli.
- Aprire [Preparazione di Database di SQL Azure](sql-database-advisor.md) e verificare se eventuali raccomandazioni sono disponibili.
- Utilizzare i dispositivi di scorrimento zoom icone per modificare l'intervallo osservato.

    ![dashboard prestazioni](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Sono riportate alcune ore di dati di deve acquisire dall'archivio di Query per Database di SQL fornire informazioni dettagliate sulle prestazioni di query. Se il database non ha alcuna attività o archivio Query non è attivo durante un determinato periodo di tempo, i tipi di grafici sarà vuoti quando si visualizza il periodo di tempo. Se non è in esecuzione, è possibile abilitare archivio di Query in qualsiasi momento.   



## <a name="review-top-cpu-consuming-queries"></a>Rivedere CPU superiore utilizzo di query

Nel [portale di](http://portal.azure.com) eseguire le operazioni seguenti:

1. Passare a un database SQL e fare clic su **tutte le impostazioni** > **supporto + risoluzione dei problemi** > **comprendere le prestazioni di Query**. 

    ![Comprendere le prestazioni di query][1]

    Apertura della visualizzazione principali query e le query di utilizzo della CPU superiore sono elencate.

1. Fare clic su intorno al grafico per informazioni dettagliate.<br>Nella riga superiore visualizzato complessiva DTU % per il database, mentre le barre mostrano % di CPU utilizzati dalle query selezionata durante l'intervallo selezionato (ad esempio, se è selezionata **l'ultima settimana** ogni barra rappresenta un giorno).

    ![principali query][2]

    Griglia inferiore rappresenta aggregate informazioni per le query visibile.

  - ID di query: identificatore univoco della query all'interno del database.
  - CPU per query durante l'intervallo di presenza (dipende dalla funzione di aggregazione).
  - Durata per query (dipende dalla funzione di aggregazione).
  - Numero totale di esecuzioni per una particolare query.

    Selezionare o deselezionare le singole query per includere o escludere del grafico con le caselle di controllo.

1. Se i dati più aggiornati, fare clic sul pulsante **Aggiorna** .
1. È possibile utilizzare i dispositivi di scorrimento e zoom pulsanti per modificare l'intervallo osservazioni e provare a utilizzare picchi:  ![impostazioni](./media/sql-database-query-performance/zoom.png)
1. Facoltativamente, se si desidera una visualizzazione diversa, è possibile selezionare la scheda **personalizzata** e impostare:
  
  - Unità di misura metriche (CPU, durata, numero di esecuzione)
  - Intervallo di tempo (ultime 24 ore, ultima settimana, scorso mese). 
  - Numero di query.
  - Funzione di aggregazione.

    ![Impostazioni](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Visualizzazione dei dettagli singola query

Per visualizzare i dettagli della query:

1. Fare clic su una query nell'elenco dei principali query.

    ![Dettagli](./media/sql-database-query-performance/details.png)

1. Apertura della visualizzazione dettagli e il conteggio di consumo/Durata/esecuzione query CPU viene suddivisa nel tempo.
1. Fare clic su intorno al grafico per informazioni dettagliate.
  - Grafico superiore Mostra linee con complessiva database DTU % e le barre sono % di CPU consumata dalla query selezionata.
  - Secondo grafico Mostra durata totale per la query selezionata.
  - Grafico inferiore mostra il numero totale di esecuzioni per la query selezionata.
    
    ![dettagli della query][3]

1. Facoltativamente, è possibile utilizzare i dispositivi di scorrimento, pulsanti di zoom o fare clic su **Impostazioni** per personalizzare la modalità di visualizzazione di dati della query o per selezionare un periodo di tempo diverso.

## <a name="review-top-queries-per-duration"></a>Revisione principali query per la durata

Aggiornamento recenti di analisi delle prestazioni di Query, abbiamo introdotto due nuove metriche che consentono di identificare i potenziali problemi relativi: conteggio durata e l'esecuzione.<br>

Query con esecuzione prolungata hanno maggiori possibilità il blocco di risorse più lungo, impedire ad altri utenti e limitare scalabilità. Sono inoltre i migliori candidati per l'ottimizzazione.<br>

Per individuare le query lunga:

1. Aprire scheda **personalizzati** in comprendere le prestazioni di Query per database selezionate
1. Modificare il formato per **durata**
1. Selezionare il numero di query e osservazioni intervallo
1. Selezionare una funzione di aggregazione
  - **Somma** sommare tutti i tempi di esecuzione query durante l'intervallo di osservazioni intera.
  - **Max** consente di individuare le query il tempo di esecuzione è stata massimo intervallo osservazioni intera.
  - **AVG** trova tempo medio di esecuzione di tutte le esecuzioni di query e visualizzare i primi fuori questi medie. 

    ![durata query][4]

## <a name="review-top-queries-per-execution-count"></a>Revisione principali query per il conteggio di esecuzione

Numero elevato di esecuzioni potrebbe non essere influisce negativamente sulla stesso database e l'uso di risorse può essere bassa ma generali dell'applicazione è possibile che venga visualizzato lenta.

In alcuni casi, che potrebbero rimandare esecuzioni molto elevato per ingrandire le dimensioni di round trip di rete. Round trip in modo significativo delle prestazioni. Sono soggetti a latenza di rete e alla latenza server figlio. 

Ad esempio, molti siti Web basati sui dati in modo rilevante accedere al database per ogni richiesta utente. Durante la connessione pool consente, il traffico di rete maggiore e carico nel server di database di elaborazione possa influire negativamente sulle prestazioni.  Consigli di carattere generale sono mantenere round trip al minimo.

Per identificare spesso eseguire query query ("semplici"):

1. Aprire scheda **personalizzati** in comprendere le prestazioni di Query per database selezionate
1. Modificare il formato per essere **conteggio di esecuzione**
1. Selezionare il numero di query e osservazioni intervallo

    ![conteggio di esecuzione query][5]

## <a name="understanding-performance-tuning-annotations"></a>Informazioni sulle annotazioni ottimizzazione delle prestazioni 

Durante l'esplorazione il carico di lavoro di comprendere le prestazioni di Query, è possibile notare le icone con linea verticale nella parte superiore del grafico.<br>

Queste icone sono annotazioni. si tratta di prestazioni che interessano le azioni eseguite da [Preparazione di Database di SQL Azure](sql-database-advisor.md). Da questa annotazione, viene visualizzato l'azione informazioni di base:

![annotazioni di query][6]

Se si vuole altre fonti di informazioni o applicare recommendation advisor, fare clic sull'icona. Si aprirà i dettagli dell'azione. Se si tratta di un suggerimento attivo è possibile applicare immediatamente tramite comando.

![Dettagli annotazione della query][7]

### <a name="multiple-annotations"></a>Più annotazioni. ###

È possibile che a causa di livello di zoom, annotazioni che sono vicini verranno visualizzato compresso in una sola. Ciò viene rappresentato dall'icona speciale, facendo clic su esso verrà aperto nuova pala in elenco raggruppati annotazioni verrà visualizzata.
Correlare query e le azioni di ottimizzazione delle prestazioni può aiutare a comprendere meglio il carico di lavoro. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Ottimizzare la configurazione dell'archivio di Query per comprendere le prestazioni di Query

Durante l'uso di analisi delle prestazioni di Query, è possibile riscontrare i messaggi di archivio di Query seguenti:

- "Store query non è configurato correttamente nel database corrente. Fare clic qui per altre informazioni."
- "Store query non è configurato correttamente nel database corrente. Fare clic qui per modificare le impostazioni." 

Questi messaggi in genere visualizzati quando archivio Query non è in grado di raccogliere nuovi dati. 

Primo caso si verifica quando Query archivio è in stato di sola lettura e i parametri vengono impostati in modo ottimale. È possibile risolvere il problema aumento delle dimensioni dell'archivio di Query o deselezionando archivio di Query.

![pulsante qds][8]

Secondo caso si verifica quando l'archivio di Query è disattivata o non è stati impostati in modo ottimale i parametri. <br>È possibile modificare i criteri di conservazione e l'acquisizione e abilitare l'archivio di Query eseguendo comandi riportati di seguito o direttamente dal portale:

![pulsante qds][9]

### <a name="recommended-retention-and-capture-policy"></a>Criteri di conservazione e l'acquisizione consigliati

Esistono due tipi di criteri di conservazione:

- Dimensioni in base a: se impostato su automatico verrà eliminare i dati automaticamente quando viene raggiunta accanto alla dimensione massima.
- In base a ora, per impostazione predefinita che è verrà impostato su 30 giorni, pertanto, se Store Query verrà eseguita fuori area, Elimina le informazioni di query precedenti al 30 giorni

Acquisire criterio può essere impostato su:

- **Tutti** : acquisisce tutte le query.
- **Auto** -query poco frequenti e le query con durata compilazione ed esecuzione significativa vengono ignorate. Limiti per la durata di esecuzione di conteggio, compilazione e runtime internamente dipendono. Questo è l'opzione predefinita.
- **Nessuno** : archivio di Query viene interrotta l'acquisizione nuove query, ma comunque raccolti Stat runtime per le query già acquisite.
    
È consigliabile l'impostazione di tutti i criteri su automatico e Pulisci criteri a 30 giorni:

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Aumentare le dimensioni dell'archivio di Query. Questo è possibile eseguire la connessione a un database ed emettere le seguenti query:

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Applicazione di queste impostazioni non hanno un limite renderà archivio Query raccolta nuove query, ma se si preferisce non attendere è possibile cancellare archivio di Query. 
> [AZURE.NOTE] Esecuzione della query seguenti eliminerà tutte le informazioni correnti nell'archivio di Query. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Riepilogo

Comprendere le prestazioni di query consente di comprendere l'impatto del carico di lavoro query e come si riferisce a consumo risorsa database. Con questa caratteristica verrà informazioni nella parte superiore di utilizzo di query e semplificarne l'identificazione quelli per la risoluzione prima che diventino un problema.




## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come migliorare le prestazioni del database SQL, scegliere [consigli](sql-database-advisor.md) e il **Comprendere le prestazioni di Query** .

![Advisor prestazioni](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png

