<properties
   pageTitle="Usando il connettore DB2 nel servizio di Microsoft Azure App | Microsoft Azure"
   description="Come utilizzare il connettore DB2 con azioni e trigger app logica"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="db2-connector"></a>Connettore DB2
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Connettore Microsoft per DB2 è un'app di API per la connessione di applicazioni di servizio App Azure alle risorse archiviate in un database IBM DB2. Connettore include un Client Microsoft per connettersi al computer server DB2 remoti tramite una connessione di rete TCP/IP, compresi ibrida Azure connessioni ai server DB2 locale utilizzando l'inoltro di Azure servizio Bus connettore supporta le seguenti operazioni di database:

- Selezionare le righe di lettura utilizzando
- Sondaggio per leggere righe mediante selezionare numero seguito da selezionare
- Aggiungere una riga o su più righe (massa) con l'istruzione INSERT
- Modificare una riga o su più righe (massa) con UPDATE
- Rimuovere una riga o su più righe (massa) tramite CANC
- Lettura per modificare le righe mediante cursore selezionare seguita da aggiornamento posizione corrente del cursore
- Lettura per rimuovere le righe con cursore selezionare seguita da aggiornamento posizione corrente del cursore
- Eseguire una routine con i parametri di input e di output, restituisce un valore, gruppo di risultati, tramite chiamata
- Comandi personalizzati e composite operazioni con, selezionare Inserisci, Aggiorna, Elimina

## <a name="triggers-and-actions"></a>Trigger e azioni
Connettore supporta le azioni trigger app logica seguenti:

Trigger | Azioni
--- | ---
<ul><li>Dati sondaggio</li></ul> | <ul><li>Inserimento di massa</li><li>Inserisci</li><li>Aggiornamento in blocco</li><li>Aggiornamento</li><li>Chiamata</li><li>Eliminazione in blocco</li><li>Elimina</li><li>Selezionare</li><li>Aggiornamento condizionale</li><li>Pubblica in EntitySet</li><li>Elimina condizionale</li><li>Selezionare singola entità</li><li>Elimina</li><li>Upsert a EntitySet</li><li>Comandi personalizzati</li><li>Operazioni composte</li></ul>


## <a name="create-the-db2-connector"></a>Creare il connettore DB2
È possibile definire un connettore all'interno di un'app logica o Azure Marketplace, come nell'esempio seguente:  

1. Nella finestra startboard Azure, selezionare **Marketplace**.
2. In **tutti gli elementi** e il, digitare **db2** nella casella **Cerca tutti gli elementi** e quindi fare clic su INVIO.
3. Nella ricerca tutti gli elementi dei risultati riquadro, selezionare **il connettore DB2**.
4. In e di descrizione per il connettore DB2, selezionare **Crea**.
5. In e pacchetto il connettore di DB2, immettere il nome (ad esempio "Db2ConnectorNewOrders"), App piano di servizio e altre proprietà.
6. Selezionare **le impostazioni del pacchetto**e immettere le impostazioni del pacchetto seguenti:  

    Nome | Obbligatorio |  Descrizione
--- | --- | ---
ConnectionString | Sì | Stringa di connessione Client DB2 (ad esempio, "indirizzo di rete = nomeserver; Porta di rete = 50000; ID utente = username; Password = password; catalogo iniziale = campione. Creare un pacchetto insieme = NWIND; predefinito Schema = NWIND ").
Tabelle | Sì | Elenco di valori separati da virgola della tabella, quindi fare clic su Visualizza e alias i nomi delle richieste per le operazioni di OData e per generare la documentazione swagger con esempi (ad esempio "*NEWORDERS*").
Procedure | Sì | Elenco di valori separati da virgola di nomi di procedure e funzione (ad esempio "SPORDERID").
Locale | No | Distribuire locali con Azure servizio Bus inoltro.
ServiceBusConnectionString | No | Stringa di connessione di Azure servizio Bus inoltro.
PollToCheckData | No | Selezionare Conteggio istruzione da utilizzare con un trigger di app logica (ad esempio "Seleziona conteggio (\*) da NEWORDERS dove DATASPEDIZIONE IS NULL").
PollToReadData | No | Istruzione SELECT da utilizzare con un trigger di app logica (ad esempio "selezionare \* da NEWORDERS dove DATASPEDIZIONE è NULL FOR UPDATE").
PollToAlterData | No | Aggiorna bibliografia o istruzione DELETE da utilizzare con un trigger di app logica (ad esempio "DATASPEDIZIONE impostare NEWORDERS di aggiornamento = corrente data in cui corrente di &lt;cursore&gt;").

7. Fare clic su **OK**e quindi scegliere **Crea**.
8. Al termine, le impostazioni del pacchetto simile al seguente:  
![][1]


## <a name="logic-app-with-db2-connector-action-to-add-data"></a>App logica con azione connettore DB2 per aggiungere i dati ##
È possibile definire un'azione app logica per aggiungere dati a una tabella di DB2 utilizzando API Insert o Post all'operazione di entità OData. Ad esempio, è possibile inserire un nuovo record di ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT rispetto a una tabella definita con una colonna di identità restituisce il valore di identità o le righe interessate all'app logica (selezionare ORDID dalla tabella finale (inserire in NWIND. VALORI NEWORDERS (IDCLIENTE, DESTINATARIO, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

> [AZURE.TIP] Connessione DB2 "*Post per EntitySet*" restituirà il valore della colonna identità e "*Inserisci API*" restituirà righe interessate

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "NewOrdersDb2"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **ricorrenza**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore DB2**, espandere l'elenco di operazioni per selezionare **inserire NEWORDER**.
7. Espandere l'elenco di parametri per immettere i valori seguenti:  

    Nome | Valore
--- | --- 
IDCLIENTE | 10042
DESTINATARIO | Archivio Kountry K lenta 
SHIPADDR | Terrazza orchestra 12
CITTÀDESTINATARIO | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Selezionare il **segno di spunta** per salvare le impostazioni di azione e **salvare**.
9. Le impostazioni avrà un aspetto come indicato di seguito:  
![][3]

10. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente). 
11. Selezionare l' elemento **azione** **db2connectorneworders**e il **logica app eseguire** .
12. In e **l'azione app logica** , selezionare il **Collegamento di input**. Connettore DB2 utilizza input per l'elaborazione di un'istruzione INSERT parametri.
13. In e **l'azione app logica** , selezionare il **Collegamento di output**. Input avrà un aspetto come indicato di seguito:  
![][4]

#### <a name="what-you-need-to-know"></a>Informazioni utili

- Connettore tronca i nomi delle tabelle di DB2 quando che formano i nomi delle azioni app logica. Ad esempio, l'operazione **inserire NEWORDERS** viene troncato per **inserire NEWORDER**.
- Dopo aver salvato l'app logica **trigger e azioni**, app logica elabora l'operazione. Può essere un ritardo di un numero di secondi (ad esempio, 3 a 5 secondi) prima della logica app elabora l'operazione. Facoltativamente, è possibile fare clic su **Esegui** per effettuare l'operazione.
- Connettore DB2 definisce i membri di EntitySet con attributi, ad esempio se il membro corrisponde a una colonna di DB2 con un valore predefinito o generato colonne (ad esempio identità). Logica app Visualizza un asterisco rosso accanto al nome di ID membro EntitySet, per indicare le colonne DB2 che richiedono valori. Non si deve immettere un valore per il membro ORDID, che corrisponde alla colonna identità DB2. È possibile immettere valori per gli altri membri facoltativi (elementi, ORDDATE, REQDATE, IDCORRIERE, trasporto, SHIPCTRY), che corrispondono a colonne DB2 con valori predefiniti. 
- Il connettore DB2 restituisce all'app logica la risposta sul Post per EntitySet che include i valori per le colonne di identità, derivata dal SQLDARD DRDA (dati di risposta Area dati di SQL) in istruzione SQL INSERT preparata. Server DB2 non restituire i valori inseriti per le colonne con valori predefiniti.  


## <a name="logic-app-with-db2-connector-action-to-add-bulk-data"></a>App logica con azione connettore DB2 per aggiungere i dati in blocco ##
È possibile definire un'azione app logica per aggiungere dati a una tabella di DB2 mediante un'operazione di inserimento di massa API. Ad esempio, è possibile inserire due nuovi record ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT utilizzando una matrice di valori di riga su una tabella definita con una colonna di identità restituire le righe interessate all'app logica (selezionare ORDID dalla tabella finale (inserire in NWIND. VALORI NEWORDERS (IDCLIENTE, DESTINATARIO, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "NewOrdersBulkDb2"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **ricorrenza**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore DB2**, espandere l'elenco di operazioni per selezionare **In blocco inserire nel nuovo**.
7. Immettere il valore di **righe** sotto forma di matrice. Ad esempio, copiare e incollare la stringa seguente:

    ```
    [{"CUSTID":10081,"SHIPNAME":"Trail's Head Gourmet Provisioners","SHIPADDR":"722 DaVinci Blvd.","SHIPCITY":"Kirkland","SHIPREG":"WA","SHIPZIP":"98034"},{"CUSTID":10088,"SHIPNAME":"White Clover Markets","SHIPADDR":"305 14th Ave. S. Suite 3B","SHIPCITY":"Seattle","SHIPREG":"WA","SHIPZIP":"98128","SHIPCTRY":"USA"}]
    ```

8. Selezionare il **segno di spunta** per salvare le impostazioni di azione e **salvare**. Le impostazioni avrà un aspetto come indicato di seguito:  
![][6]

9. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente).
10. Selezionare **l'azione** e il **logica app eseguire** .
11. Selezionare il **Collegamento di input**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
[][7]
12. Selezionare il **Collegamento di output**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
![][8]

#### <a name="what-you-need-to-know"></a>Informazioni utili

- Connettore tronca i nomi delle tabelle di DB2 quando che formano i nomi delle azioni app logica. Ad esempio, l'operazione **In blocco inserire NEWORDERS** viene troncato **in blocco**inserire in nuovo.
- Omettendo identità colonne (ad esempio ORDID), le colonne nullable (ad esempio DATASPEDIZIONE) e le colonne con valori predefiniti (ad esempio ORDDATE, REQDATE, IDCORRIERE, trasporto, SHIPCTRY), database DB2 genera i valori.
- Se si specifica "oggi" e "domani", il connettore DB2 genera "Data corrente" e "Data corrente più un giorno" funzioni (ad esempio REQDATE). 


## <a name="logic-app-with-db2-connector-trigger-to-read-alter-or-delete-data"></a>App logica con trigger connettore DB2 di leggere, modificare o eliminare dati ##
È possibile definire un trigger di app logica per sondaggio e leggere i dati da una tabella di DB2 tramite un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovo ordine record dei clienti, restituisce i record per l'app logica. Le impostazioni di connessione DB2 pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | <no value specified>


Inoltre, è possibile definire un trigger di app logica per sondaggio, leggere e modificare i dati in una tabella di DB2 usando un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovi record ordine cliente, aggiornare i valori della riga, restituendo i record selezionati (prima dell'aggiornamento) all'app logica. Le impostazioni di connessione DB2 pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | AGGIORNAMENTO NEWORDERS SET DATASPEDIZIONE = data corrente in cui corrente della &lt;cursore&gt;


Inoltre, è possibile definire un trigger di app logica per sondaggio, leggere e rimuovere i dati da una tabella di DB2 tramite un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovi record ordine cliente, eliminare le righe, restituendo i record selezionati (prima dell'eliminazione) all'app logica. Le impostazioni di connessione DB2 pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | Elimina NEWORDERS in effetti corrente &lt;cursore&gt;

In questo esempio app logica verrà sondaggio, leggere, aggiornare e leggere nuovamente i dati nella tabella DB2.

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "ShipOrdersDb2"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **connettore DB2**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore DB2**, espandere l'elenco di operazioni e scegliere **Selezionare da NEWORDERS**.
7. Selezionare il **segno di spunta** per salvare le impostazioni di azione e **salvare**. Le impostazioni avrà un aspetto come indicato di seguito:  
![][10]  
8. Fare clic su per chiudere e il **trigger e le azioni** e quindi fare clic su per chiudere e **l'Impostazioni** .
9. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente).
10. Selezionare **l'azione** e il **logica app eseguire** .
11. Selezionare il **Collegamento di output**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
![][11]


## <a name="logic-app-with-db2-connector-action-to-remove-data"></a>App logica con azione connettore DB2 per rimuovere i dati ##
È possibile definire un'azione app logica per rimuovere i dati da una tabella di DB2 mediante un API Elimina o Post all'operazione di entità OData. Ad esempio, è possibile inserire un nuovo record di ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT rispetto a una tabella definita con una colonna di identità restituisce il valore di identità o le righe interessate all'app logica (selezionare ORDID dalla tabella finale (inserire in NWIND. VALORI NEWORDERS (IDCLIENTE, DESTINATARIO, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

## <a name="create-logic-app-using-db2-connector-to-remove-data"></a>Creare app logica usando DB2 connector per rimuovere i dati ##
È possibile creare una nuova app logica da Azure Marketplace e quindi utilizzare il connettore DB2 come un'azione per rimuovere gli ordini dei clienti. Ad esempio, è possibile utilizzare l'operazione di eliminazione DB2 condizionale connettore per l'elaborazione di un'istruzione SQL DELETE (Elimina da NEWORDERS in ORDID > = 10000).

1. Nel menu hub della scheda Azure **iniziare** , fare clic su **+** (segno più), fare clic su **Web + Mobile**e quindi fare clic su **app logica**. 
2. In e il **logica creare app** , digitare un **nome**, ad esempio **RemoveOrdersDb2**.
3. Selezionare o definire i valori per le altre impostazioni (piano di servizio, gruppo di risorse).
4. Le impostazioni avrà un aspetto come indicato di seguito. Fare clic su **Crea**:  
![][12]  
5. In e **l'Impostazioni** , fare clic su **trigger e azioni**.
6. In e il **trigger e azioni** , nell'elenco delle **app logica modelli** , fare clic su **Crea da zero**.
7. In e il **trigger e azioni** , nel riquadro **App API** all'interno del gruppo di risorse, fare clic su **ricorrenza**.
8. Nella finestra di progettazione logica app, fare clic sull'elemento **ricorrenza** , impostare una **frequenza** e **intervallo**, ad esempio i **giorni** e **1**, quindi scegliere il **segno di spunta** per salvare le impostazioni di elementi di ricorrenza.
9. In e il **trigger e azioni** , nel riquadro **App API** all'interno del gruppo di risorse, fare clic su **connettore DB2**.
10. Nella finestra di progettazione logica app, fare clic su azione **connettore DB2** , fare clic sui puntini di sospensione (**.**) per espandere l'elenco di operazioni e quindi fare clic su **Elimina condizionale da N**.
11. Azione di connettore DB2, digitare **ORDID grandi 10000** per un' **espressione che identifica un sottoinsieme di voci**.
12. Fare clic sul **segno di spunta** per salvare le impostazioni di azione e quindi fare clic su **Salva**. Le impostazioni avrà un aspetto come indicato di seguito:  
![][13]  
13. Fare clic su per chiudere e il **trigger e le azioni** e quindi fare clic su per chiudere e **l'Impostazioni** .
14. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente).
15. Selezionare **l'azione** e il **logica app eseguire** .
16. Selezionare il **Collegamento di output**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
![][14]

**Nota:** Progettazione di applicazioni logica tronca i nomi delle tabelle. Ad esempio, l'operazione **condizionale eliminare da NEWORDERS** viene troncato **condizionale**eliminare da N.


> [AZURE.TIP] Utilizzare le seguenti istruzioni SQL per creare la tabella di esempio e stored procedure. 

È possibile creare la tabella di esempio NEWORDERS utilizzando le seguenti istruzioni DDL SQL DB2:
 
    CREATE TABLE ORDERS (  
        ORDID INT NOT NULL GENERATED BY DEFAULT AS IDENTITY (START WITH 10000, INCREMENT BY 1) ,  
        CUSTID INT NOT NULL ,  
        EMPID INT NOT NULL DEFAULT 10000 ,  
        ORDDATE DATE NOT NULL DEFAULT CURRENT DATE ,  
        REQDATE DATE DEFAULT CURRENT DATE ,  
        SHIPDATE DATE ,  
        SHIPID INT NOT NULL DEFAULT 10000,  
        FREIGHT DECIMAL (9,2) NOT NULL DEFAULT 0.00 ,  
        SHIPNAME CHAR (40) NOT NULL ,  
        SHIPADDR CHAR (60) NOT NULL ,  
        SHIPCITY CHAR (20) NOT NULL ,  
        SHIPREG CHAR (15) NOT NULL ,  
        SHIPZIP CHAR (10) NOT NULL ,  
        SHIPCTRY CHAR (15) NOT NULL DEFAULT 'USA' ,  
        PRIMARY KEY(ORDID)  
        )  
 
    CREATE UNIQUE INDEX XORDID ON ORDERS (ORDID ASC)  



È possibile creare la procedura SPOERID archiviati esempio utilizzando l'istruzione DDL DB2 seguente:
 
    CREATE OR REPLACE PROCEDURE NWIND.SPORDERID (IN ORDERID VARCHAR(128))  
        DYNAMIC RESULT SETS 1  
    P1: BEGIN  
        DECLARE CURSOR1 CURSOR WITH RETURN FOR  
            SELECT * FROM NWIND.NEWORDERS  
                WHERE ORDID = ORDERID;  
        OPEN CURSOR1;  
    END P1  
    ') 


## <a name="hybrid-configuration-optional"></a>Configurazione ibrida (facoltativa)

> [AZURE.NOTE] Questo passaggio è necessario solo se si utilizza DB2 connettore locale all'interno del firewall.

Gestione configurazione ibrida App utilizza per connettersi al sistema locale. Se il connettore utilizza un locale IBM DB2 Server per Windows, la gestione di connessione ibrida è obbligatorio.

Vedere [utilizzando la gestione di connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere un flusso di lavoro di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

Creare App API utilizzando le API REST. Vedere [i connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare sicurezza di statistiche e controllo delle prestazioni per il connettore. Vedere [gestire e monitorare le app API e i connettori incorporati](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-db2/ApiApp_Db2Connector_Create.png
[2]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Create.png
[3]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_TriggersActions.png
[4]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersDb2_Outputs.png
[5]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Create.png
[6]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_TriggersActions.png
[7]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Inputs.png
[8]: ./media/app-service-logic-connector-db2/LogicApp_NewOrdersBulkDb2_Outputs.png
[9]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Create.png
[10]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_TriggersActions.png
[11]: ./media/app-service-logic-connector-db2/LogicApp_UpdateOrdersDb2_Outputs.png
[12]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Create.png
[13]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_TriggersActions.png
[14]: ./media/app-service-logic-connector-db2/LogicApp_RemoveOrdersDb2_Outputs.png

