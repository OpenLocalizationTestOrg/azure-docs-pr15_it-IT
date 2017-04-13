<properties
    pageTitle="Aggiungere il connettore di Database SQL Azure nelle applicazioni logica | Microsoft Azure"
    description="Panoramica della connessione di Database SQL Azure con i parametri di API REST"
    services=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="anneta"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="10/18/2016"
   ms.author="mandia"/>


# <a name="get-started-with-the-azure-sql-database-connector"></a>Iniziare a utilizzare il connettore di Database SQL Azure
Il connettore di Database SQL Azure consente di creare flussi di lavoro per l'organizzazione di gestire i dati delle tabelle. 

Database SQL, è:

- Creare il flusso di lavoro mediante l'aggiunta di un nuovo cliente a un database di clienti o aggiornamento di un ordine in un database di ordini.
- Utilizzare azioni per ottenere una riga di dati, inserire una nuova riga e di eliminare. Ad esempio, quando si crea un record in Dynamics CRM Online (trigger), inserire una riga in un Database di SQL Azure (un'azione). 

In questo argomento viene illustrato come utilizzare il connettore di Database SQL in un'app di logica e anche elencate le azioni.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla disponibilità generale App logica (GA). 

Per ulteriori informazioni su come logica App, vedere [quali sono le applicazioni di logica](../app-service-logic/app-service-logic-what-are-logic-apps.md) e [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-azure-sql-database"></a>Connettersi al Database SQL Azure

Prima che l'app logica poter accedere a qualsiasi servizio, è prima di tutto creare una *connessione* al servizio. Una connessione fornisce la connettività tra un'app di logica e un altro servizio. Ad esempio, per connettersi al Database SQL, è prima di tutto creare una *connessione*al Database SQL. Per creare una connessione, immettere le credenziali che generalmente vengono utilizzati per accedere al servizio che si è connessi. Quindi, nel Database di SQL, immettere le credenziali di Database SQL per creare la connessione. 

#### <a name="create-the-connection"></a>Creare la connessione

>[AZURE.INCLUDE [Create the connection to SQL Azure](../../includes/connectors-create-api-sqlazure.md)]

## <a name="use-a-trigger"></a>Utilizzare un trigger

Il connettore non dispone di tutti i trigger. Usare altri trigger per avviare l'applicazione di logica, ad esempio un trigger di ricorrenza, un trigger HTTP Webhook, trigger disponibili con altri connettori e altro ancora. [Creare un'app logica](../app-service-logic/app-service-logic-create-a-logic-app.md) viene fornito un esempio.

## <a name="use-an-action"></a>Utilizzare un'azione
    
Un'azione è un'operazione effettuata dal flusso di lavoro definita in un'app di logica. [Altre informazioni sulle operazioni](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

1. Selezionare il segno di addizione. Viene visualizzato selezionare diverse opzioni: **aggiungere un'azione**, **aggiungere una condizione**o una delle **altre** opzioni.

    ![](./media/connectors-create-api-sqlazure/add-action.png)

2. Scegliere **Aggiungi un'azione**.

3. Nella casella di testo digitare "sql" per ottenere un elenco di tutte le azioni disponibili.

    ![](./media/connectors-create-api-sqlazure/sql-1.png) 

4. In questo esempio, scegliere **SQL Server - ottenere riga**. Se esiste già una connessione, quindi selezionare il **nome della tabella** dall'elenco a discesa e immettere l' **ID di riga** per restituire.

    ![](./media/connectors-create-api-sqlazure/sample-table.png)

    Se viene chiesto di immettere le informazioni di connessione, quindi immettere i dettagli per creare la connessione. [Creare la connessione](connectors-create-api-sqlazure.md#create-the-connection) in questo argomento sono descritte le proprietà. 

    > [AZURE.NOTE] In questo esempio viene restituita una riga da una tabella. Per visualizzare i dati in questa riga, aggiungere un'altra azione che viene creato un file utilizzando i campi della tabella. Ad esempio aggiungere un'azione OneDrive che utilizza i campi nome e cognome per creare un nuovo file nell'account di archiviazione cloud. 

5. **Salvare** le modifiche (superiore sinistro della barra degli strumenti). L'app logica verrà salvata e può essere attivata automaticamente.


## <a name="technical-details"></a>Dettagli tecnici

## <a name="sql-database-actions"></a>Azioni di Database SQL
Un'azione è un'operazione effettuata dal flusso di lavoro definita in un'app di logica. Il connettore di Database SQL include le azioni seguenti. 

|Azione|Descrizione|
|--- | ---|
|[ExecuteProcedure](connectors-create-api-sqlazure.md#execute-stored-procedure)|Esegue una stored procedure in SQL|
|[GetRow](connectors-create-api-sqlazure.md#get-row)|Recupera una singola riga da una tabella SQL|
|[GetRows](connectors-create-api-sqlazure.md#get-rows)|Recupera righe da una tabella SQL|
|[InsertRow](connectors-create-api-sqlazure.md#insert-row)|Inserisce una nuova riga in una tabella SQL|
|[Eliminariga](connectors-create-api-sqlazure.md#delete-row)|Elimina una riga da una tabella SQL|
|[GetTables](connectors-create-api-sqlazure.md#get-tables)|Recupera tabelle da un database SQL|
|[UpdateRow](connectors-create-api-sqlazure.md#update-row)|Aggiorna una riga esistente in una tabella SQL|

### <a name="action-details"></a>Dettagli di azione

In questa sezione, vedere i dettagli specifici di ogni azione, inclusi eventuali proprietà input obbligatorio o facoltativo e qualsiasi output corrispondente associato al connettore.


#### <a name="execute-stored-procedure"></a>Eseguire stored procedure
Esegue una stored procedure in SQL.  

| Nome della proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|procedura * | Nome della procedura | Il nome della stored procedure da eseguire |
|parametri * | Parametri di input | I parametri sono dinamici e si basa su stored procedure che scelto. <br/><br/> Ad esempio, se si usa il database di esempio Adventure Works, scegliere la procedura *ufnGetCustomerInformation* archiviato. Viene visualizzato il parametro di input **ID cliente** . Immettere "6" o una delle altre cliente ID. |

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output
ProcedureResult: Esegue risultato dell'esecuzione di stored procedure

| Nome della proprietà | Tipo di dati | Descrizione |
|---|---|---|
|OutputParameters|oggetto|Valori dei parametri di output |
|Codice restituito|numero intero|Codice restituito di una routine |
|Gruppi di risultati|oggetto| Set di risultati|


#### <a name="get-row"></a>Ottenere la riga 
Recupera una singola riga da una tabella SQL.  

| Nome della proprietà| Nome visualizzato |Descrizione|
| ---|---|---|
|tabella * | Nome della tabella |Nome della tabella SQL|
|ID * | Id riga |Identificatore univoco della riga da recuperare|

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output
Elemento

| Nome della proprietà | Tipo di dati |
|---|---|
|ItemInternalId|stringa|


#### <a name="get-rows"></a>Ottenere righe 
Recupera le righe da una tabella SQL.  

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|tabella *|Nome della tabella|Nome della tabella SQL|
|$skip|Ignora conteggio|Numero di voci per ignorare (impostazione predefinita = 0)|
|$top|Numero massimo Get|Numero massimo di voci per recuperare (impostazione predefinita = 256)|
|$filter|Query del filtro|Una query di filtro ODATA per limitare il numero di voci|
|$orderby|Order By|Una query di orderBy ODATA per specificare l'ordine delle voci|

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output
ItemsList

| Nome della proprietà | Tipo di dati |
|---|---|
|valore|in forma di matrice|


#### <a name="insert-row"></a>Inserisci riga 
Inserisce una nuova riga in una tabella SQL.  

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|tabella *|Nome della tabella|Nome della tabella SQL|
|elemento *|Riga|Riga per inserire la tabella specificata in SQL|

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output
Elemento

| Nome della proprietà | Tipo di dati |
|---|---|
|ItemInternalId|stringa|


#### <a name="delete-row"></a>Elimina riga 
Elimina una riga da una tabella SQL.  

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|tabella *|Nome della tabella|Nome della tabella SQL|
|ID *|Id riga|Identificatore univoco della riga da eliminare|

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output
Nessuno.

#### <a name="get-tables"></a>Inserire tabelle 
Recupera tabelle da un database SQL.  

Non sono presenti parametri per la chiamata. 

##### <a name="output-details"></a>Dettagli output 
TablesList

| Nome della proprietà | Tipo di dati |
|---|---|
|valore|in forma di matrice|

#### <a name="update-row"></a>Riga di aggiornamento 
Aggiorna una riga esistente in una tabella SQL.  

|Nome della proprietà| Nome visualizzato|Descrizione|
| ---|---|---|
|tabella *|Nome della tabella|Nome della tabella SQL|
|ID *|Id riga|Identificatore univoco della riga da aggiornare|
|elemento *|Riga|Riga con valori aggiornati|

Un asterisco (*) indica che la proprietà è obbligatoria.

##### <a name="output-details"></a>Dettagli output  
Elemento

| Nome della proprietà | Tipo di dati |
|---|---|
|ItemInternalId|stringa|


### <a name="http-responses"></a>Risposte HTTP

Quando si effettua chiamate a diverse operazioni, è possibile ottenere alcune risposte. Nella tabella seguente vengono descritte le risposte e le relative descrizioni:  

|Nome|Descrizione|
|---|---|
|200|Ok|
|202|Accettato dall'utente|
|400|Richiesta non valida|
|401|Non autorizzato|
|403|Accesso negato|
|404|Non trovato|
|500|Errore interno del Server. Errore sconosciuto|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="next-steps"></a>Passaggi successivi

[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md). Esplorare gli altri connettori disponibili nelle App logica il nostro [elenco API](apis-list.md).
