<properties
   pageTitle="Usando il connettore SQL in App logica | Servizio di Microsoft Azure App"
   description="Come creare e configurare l'app connettore SQL o API e usarla in un'app di logica nel servizio App Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Introduzione a Microsoft SQL Connector e aggiungere logica all'applicazione in uso
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema. Per la versione di schema Azure SQL 2015-08-01-anteprima, fare clic su [API di SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Connettersi a un locale SQL Server o un Database di SQL Azure per creare e modificare le informazioni o i dati. Connettori utilizzabili nelle App logica per recuperare, processo, o inserire i dati come parte del flusso di lavoro"". Quando si utilizza il connettore di SQL nel flusso di lavoro, è possibile ottenere diversi scenari. Ad esempio, è possibile:

- Esporre una sezione di dati presenti nel database di SQL utilizzando un web o applicazione per dispositivi mobili.
- Inserire dati in una tabella di database SQL per l'archiviazione. Ad esempio, è possibile immettere record dipendente, aggiornare gli ordini di vendita e così via.
- Recuperare dati da SQL e usarla in un processo aziendale. Ad esempio, è possibile ottenere i record del cliente e i record dei clienti in SalesForce.

È possibile aggiungere il connettore SQL ai dati business flussi di lavoro e processo come parte del flusso di lavoro all'interno di un'App di logica. 

## <a name="triggers-and-actions"></a>Trigger e azioni
*I trigger* sono eventi che si verificano. Ad esempio, quando si aggiorna un ordine o quando un nuovo cliente verrà aggiunto. Il risultato del trigger è un' *azione* . Ad esempio, quando viene aggiornato un ordine, inviare un avviso per l'agente di vendita. In alternativa, quando viene aggiunto un nuovo cliente, inviare un messaggio di benvenuto al nuovo cliente.

Il connettore di SQL possono essere utilizzato come un trigger o un'azione in una logica app e supporta i dati nei formati XML e JSON. Per ogni tabella incluso nel pacchetto impostazioni (ulteriori informazioni più avanti in questo argomento), esiste un insieme di azioni JSON e un insieme di azioni XML.

Il connettore di SQL sono i seguenti trigger e le azioni disponibili:

Trigger | Azioni
--- | ---
Dati sondaggio | <ul><li>Inserire nella tabella</li><li>Aggiorna sommario</li><li>Selezionare dalla tabella</li><li>Eliminare dalla tabella</li><li>Chiamare Stored Procedure</li></ul>

## <a name="create-the-sql-connector"></a>Creare il connettore SQL

Un connettore, questo può essere creato all'interno di un'app logica o creato direttamente da Azure Marketplace. Per creare un connettore da Marketplace:  

1. Nella finestra startboard Azure, selezionare **Marketplace**.
2. Cercare "SQL connettore", selezionarla e selezionare **Crea**.
3. Immettere il nome, App piano di servizio e altre proprietà.
4. Immettere le impostazioni del pacchetto seguenti:

    Nome | Obbligatorio |  Descrizione
--- | --- | ---
Nome del server | Sì | Immettere il nome di SQL Server. Ad esempio, immettere *SQLserver.mydomain.com*o *SQL Server/SQL Express* .
Porta | No | Il valore predefinito è 1433.
Nome utente | Sì | Immettere un nome utente che può accedere a SQL Server. Se si connette a un Server SQL locale, immettere le credenziali di autenticazione di SQL Server.
Password | Sì | Immettere nome utente e password.
Nome del database | Sì | Immettere il database che si è connessi. Ad esempio, è possibile immettere *clienti* o *dbo/ordini*.
Locale | Sì | Il valore predefinito è False. Immettere False se la connessione a un database SQL Azure. Immettere True se la connessione a un SQL Server locale.
Stringa di connessione Bus di servizio | No | Se ci si connette a locale, immettere la stringa di connessione di inoltro Bus di servizio.<br/><br/>[Utilizzando la gestione di connessione ibrido](app-service-logic-hybrid-connection-manager.md)<br/>[Servizio Bus prezzi](https://azure.microsoft.com/pricing/details/service-bus/)
Nome del Server partner | No | Se il server primario non è disponibile, è possibile immettere un server partner come un server alternativo o backup.
Tabelle | No | Elenco di tabelle di database che possono essere aggiornate tramite il connettore. Ad esempio, immettere *OrdersTable* o *EmployeeTable*. Se non è stati immessi alcuna tabella, è possono utilizzare tutte le tabelle. Tabelle valide e/o Stored procedure è necessario utilizzare il connettore come un'azione.
Stored procedure | No | Immettere una stored procedure esistente che può essere chiamata tramite il connettore. Ad esempio, immettere *sp_IsEmployeeEligible* o *sp_CalculateOrderDiscount*. Tabelle valide e/o Stored procedure è necessario utilizzare il connettore come un'azione.
Query di dati disponibili | Per il supporto di trigger | Istruzione SQL per determinare se uno qualsiasi dei dati è disponibile per il polling di una tabella di database SQL Server. Questa operazione deve restituire un valore numerico che rappresenta il numero di righe di dati disponibili. Esempio: Selezionare Count table_name.
Query di dati sondaggio | Per il supporto di trigger | Istruzione SQL sondaggio la tabella di database SQL Server. È possibile immettere un numero qualsiasi di istruzioni SQL separati da punti e virgola. Questa istruzione è eseguita tramite transazioni e approvata solo quando i dati vengono archiviati in modo sicuro nell'app logica. Esempio: Selezionare *table_name; ELIMINARE dal table_name. <br/>**Note**<br/>È necessario fornire un'istruzione sondaggio che consente di evitare infinito mediante l'eliminazione, spostamento o l'aggiornamento dei dati selezionati per assicurarsi che non sono polling nuovamente da stessi dati.

5. Al termine, le impostazioni del pacchetto simile al seguente:  
![][1]  

6. Selezionare **Crea**. 


## <a name="use-the-connector-as-a-trigger"></a>Utilizzare il connettore come Trigger
Diamo un'occhiata un'app di logica semplice che esegue il polling dati da una tabella SQL, i dati vengono aggiunti in un'altra tabella e aggiorna i dati.

Per utilizzare il connettore SQL come trigger, immettere i valori **Dei dati disponibili Query** e **dati sondaggio** . **Dati disponibili Query** viene eseguita in base alla pianificazione immettere e determina se i dati è disponibile. Poiché questa query restituisce solo un numero scalare, può essere ottimizzata e ottimizzato per l'esecuzione di frequente.

**Query di dati sondaggio** viene eseguita solo quando la Query di dati disponibili indica la disponibilità dei dati. La presente informativa viene eseguita all'interno di una transazione che è stato eseguito il commit quando i dati estratti in modo permanente è archiviati il flusso di lavoro. È importante evitare all'infinito estrazione nuovamente gli stessi dati. Natura transazione di questa esecuzione è utilizzabile per eliminare o aggiornare i dati per verificare che non è raccolto la volta successiva che viene eseguita la query di dati.

> [AZURE.NOTE] Lo schema restituito da questa istruzione identifica le proprietà disponibili nel connettore. Tutte le colonne devono essere denominate.

#### <a name="data-available-query-example"></a>Esempio di dati della Query disponibili

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Esempio di dati della Query sondaggio

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Aggiungere il Trigger
1. Durante la creazione o modifica di un'app di logica, selezionare il connettore di SQL creata come trigger. Elenca i trigger disponibili: **Sondaggio dati JSON ()** e **Dati sondaggio (XML)**:  
![][5]

2. Selezionare il trigger **Sondaggio dati JSON ()** , immettere la frequenza e fare clic sul ✓:  
![][6]

3. Il trigger verrà visualizzato come configurato nell'app logica. Output del trigger vengono riportati e possono essere utilizzati come input in eventuali azioni successive:  
![][7]

## <a name="use-the-connector-as-an-action"></a>Utilizzare il connettore come un'azione
Con questo scenario app logica semplice che esegue il polling dati da una tabella SQL, i dati vengono aggiunti in un'altra tabella e aggiorna i dati.

Per utilizzare il connettore SQL come un'azione, immettere il nome di tabelle e/o Stored procedure è stata immessa una volta creato il connettore di SQL:

1. Dopo il trigger (o scegliere 'Esegui questa logica manualmente'), aggiungere il connettore SQL creato dalla raccolta. Selezionare una delle operazioni di inserimento, ad esempio *Inserire in TempEmployeeDetails (JSON)*:  
![][8]

2. Immettere i valori di input del record da inserire e fare clic sul ✓:  
![][9]

3. Dalla raccolta, selezionare il connettore SQL stessa che è stato creato. Come un'azione, selezionare l'azione Aggiorna nella stessa tabella, ad esempio *EmployeeDetails aggiornamento*:  
![][11]

4. Immettere i valori di input per l'operazione di aggiornamento e fare clic sul ✓:  
![][12]

È possibile verificare l'app logica mediante l'aggiunta di un nuovo record nella tabella che polling.

## <a name="what-you-can-and-cannot-do"></a>Operazioni eseguibili e non consentite

Query SQL | Supportati | Non è supportata
--- | --- | ---
Dove clausola | <ul><li>Operatori: E, o, =, <>, <, < =, >, > = e mi PIACE</li><li>Combinare più condizioni sub da '(' and')'</li><li>Stringhe letterali, Datetime (racchiuso tra virgolette singole), i numeri (può contenere solo caratteri numerici)</li><li>Dovrebbe essere strettamente in formato binario espressione come ((operand operator operand) e/o (operando operatore operando)) *</li></ul> | <ul><li>Operatori: Tra,</li><li>Tutte le funzioni predefinite come Add (), Now () max (), POWER() e così via</li><li>Ad esempio operatori matematici *, -, +, e così via</li><li>Concatenazioni utilizzando +.</li><li>Tutti i join</li><li>NULL e non Null</li><li>Tutti i numeri con caratteri non numerici, ad esempio i numeri esadecimali</li></ul>
Campi (in query di selezione) | <ul><li>Nomi di colonna valido separati da virgole. Nessun prefissi di nome tabella consentito (funziona connettore su una tabella alla volta).</li><li>Nomi possono escape con ' ["e"] "</li></ul> | <ul><li>Parole chiave come parte superiore, DISTINCT e così via</li><li>Alias, ad esempio via e numero civico, città + Zip come indirizzo</li><li>Tutte le funzioni, ad esempio Add (), Now () max (), POWER() e così via</li><li>Operatori matematici, ad esempio *, -, +, e così via</li><li>Concatenazioni utilizzando +</li></ul>

#### <a name="tips"></a>Suggerimenti

- Per le query avanzate, è preferibile creare una stored procedure e esecuzione con la procedura di execute archiviati API.
- Quando si utilizzano query interna, utilizzarli in una stored procedure.
- Per partecipare a più condizioni, è possibile utilizzare "AND" e operatori 'O'.

## <a name="hybrid-configuration-optional"></a>Configurazione ibrida (facoltativa)

> [AZURE.NOTE] Questo passaggio è necessario solo se si utilizza SQL Server locale all'interno del firewall.

Gestione configurazione ibrida App utilizza per connettersi al sistema locale. Se si connettore viene utilizzato un SQL Server locale, è necessario ibrido Connection Manager.

> [AZURE.NOTE] Se si desidera iniziare a utilizzare App logica Azure prima di iscriversi a un account Azure, passare a [Provare l'App logica](https://tryappservice.azure.com/?appservice=logic), in cui è possibile creare immediatamente un'app di logica starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

Vedere [utilizzando la gestione di connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere un flusso di lavoro di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

Visualizzare il riferimento all'API REST Swagger [connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare sicurezza di statistiche e controllo delle prestazioni per il connettore. Vedere [gestire e monitorare le app API e i connettori incorporati](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png
