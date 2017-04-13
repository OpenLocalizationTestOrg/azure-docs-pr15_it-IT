<properties
   pageTitle="Usando il connettore Informix nel servizio di Microsoft Azure App | Microsoft Azure"
   description="Come utilizzare il connettore Informix con azioni e trigger app logica"
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

# <a name="informix-connector"></a>Connettore Informix
>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Connettore Microsoft per Informix è un'app di API per la connessione di applicazioni di servizio App Azure alle risorse archiviate in un database IBM Informix. Connettore include un Client Microsoft per connettersi al computer server Informix remoti tramite una connessione di rete TCP/IP, incluse le connessioni di Azure ibrido ai server di Informix locale utilizzando l'inoltro di Azure servizio Bus. Connettore supporta le seguenti operazioni di database:

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


## <a name="create-the-informix-connector"></a>Creare il connettore Informix
È possibile definire un connettore all'interno di un'app logica o Azure Marketplace, come nell'esempio seguente:  

1. Nella finestra startboard Azure, selezionare **Marketplace**.
2. In **tutti gli elementi** e il, digitare **informix** nella casella **Cerca tutti gli elementi** e quindi fare clic su INVIO.
3. Nella ricerca tutti gli elementi dei risultati riquadro, selezionare **il connettore Informix**.
4. In e di descrizione per il connettore Informix, selezionare **Crea**.
5. In e pacchetto il connettore di Informix, immettere il nome (ad esempio "InformixConnectorNewOrders"), App piano di servizio e altre proprietà.
6. Selezionare **le impostazioni del pacchetto**e immettere le seguenti impostazioni del pacchetto.

    Nome | Obbligatorio |  Descrizione
--- | --- | ---
ConnectionString | Sì | Stringa di connessione Informix Client (ad esempio, "indirizzo di rete = nomeserver; Porta di rete = 9089; ID utente = username; Password = password; catalogo iniziale = nwind; Schema predefinito = informix ").
Tabelle | Sì | Elenco di valori separati da virgola della tabella, quindi fare clic su Visualizza e alias i nomi delle richieste per le operazioni di OData e per generare la documentazione swagger con esempi (ad esempio "NEWORDERS").
Procedure | Sì | Elenco di valori separati da virgola di nomi di procedure e funzione (ad esempio "SPORDERID").
Locale | No | Distribuire locali con Azure servizio Bus inoltro.
ServiceBusConnectionString | No | Stringa di connessione di Azure servizio Bus inoltro.
PollToCheckData | No | Selezionare Conteggio istruzione da utilizzare con un trigger di app logica (ad esempio "Seleziona conteggio (\*) da NEWORDERS dove DATASPEDIZIONE IS NULL").
PollToReadData | No | Istruzione SELECT da utilizzare con un trigger di app logica (ad esempio "selezionare \* da NEWORDERS dove DATASPEDIZIONE è NULL FOR UPDATE").
PollToAlterData | No | Aggiorna bibliografia o istruzione DELETE da utilizzare con un trigger di app logica (ad esempio "DATASPEDIZIONE impostare NEWORDERS di aggiornamento = corrente data in cui corrente di &lt;cursore&gt;").

7. Fare clic su **OK**e quindi scegliere **Crea**.
8. Al termine, le impostazioni del pacchetto simile al seguente:  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>App logica con azione connettore Informix per aggiungere i dati ##
È possibile definire un'azione app logica per aggiungere dati a una tabella di Informix mediante un API Inserisci o Post all'operazione di entità OData. Ad esempio, è possibile inserire un nuovo record di ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT rispetto a una tabella definita con una colonna di identità restituisce il valore di identità o le righe interessate all'app logica (selezionare ORDID dalla tabella finale (valori di inserimento in NEWORDERS (IDCLIENTE, destinatario, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

> [AZURE.TIP] Connessione Informix "*Post per EntitySet*" restituirà il valore della colonna identità e "*Inserisci API*" restituirà righe interessate

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "NewOrdersInformix"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **ricorrenza**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore Informix**, espandere l'elenco di operazioni per selezionare **inserire NEWORDER**.
7. Espandere l'elenco di parametri per immettere i valori seguenti:  

    Nome | Valore
--- | --- 
IDCLIENTE | 10042
IDCORRIERE | 10000
DESTINATARIO | Archivio Kountry K lenta 
SHIPADDR | Terrazza orchestra 12
CITTÀDESTINATARIO | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Selezionare il **segno di spunta** per salvare le impostazioni di azione e **salvare**.
9. Le impostazioni avrà un aspetto come indicato di seguito:  
![][3]  
10. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente). 
11. Selezionare l' elemento **azione** **informixconnectorneworders**e il **logica app eseguire** .
12. In e **l'azione app logica** , selezionare il **Collegamento di input**. Connettore Informix utilizza input per l'elaborazione di un'istruzione INSERT parametri.
13. In e **l'azione app logica** , selezionare il **Collegamento di output**. Input avrà un aspetto come indicato di seguito:  
![][4]

#### <a name="what-you-need-to-know"></a>Informazioni utili

- Connettore tronca i nomi delle tabelle di Informix quando che formano i nomi delle azioni app logica. Ad esempio, l'operazione **inserire NEWORDERS** viene troncato per **inserire NEWORDER**.
- Dopo aver salvato l'app logica **trigger e azioni**, app logica elabora l'operazione. Può essere un ritardo di un numero di secondi (ad esempio, 3 a 5 secondi) prima della logica app elabora l'operazione. Facoltativamente, è possibile fare clic su **Esegui** per effettuare l'operazione.
- Connettore Informix definisce i membri di EntitySet con attributi, ad esempio se il membro corrisponde a una colonna di Informix con un valore predefinito o generato colonne (ad esempio identità). Logica app Visualizza un asterisco rosso accanto al nome di ID membro EntitySet, per indicare le colonne di Informix che richiedono valori. Non si deve immettere un valore per il membro ORDID, che corrisponde alla colonna identità Informix. È possibile immettere valori per gli altri membri facoltativi (elementi, ORDDATE, REQDATE, IDCORRIERE, trasporto, SHIPCTRY), che corrispondono a colonne Informix con valori predefiniti. 
- Il connettore Informix restituisce all'app logica la risposta sul Post per EntitySet che include i valori per le colonne di identità, derivata dal SQLDARD DRDA (dati di risposta Area dati di SQL) in istruzione SQL INSERT preparata. Server di Informix non restituire i valori inseriti per le colonne con valori predefiniti.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>App logica con azione connettore Informix per aggiungere i dati in blocco ##
È possibile definire un'azione app logica per aggiungere dati a una tabella di Informix mediante un'operazione di inserimento di massa API. Ad esempio, è possibile inserire due nuovi record ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT utilizzando una matrice di valori di riga su una tabella definita con una colonna di identità restituire le righe interessate all'app logica (selezionare ORDID dalla tabella finale (valori di inserimento in NEWORDERS (IDCLIENTE, destinatario, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "NewOrdersBulkInformix"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **ricorrenza**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore Informix**, espandere l'elenco di operazioni per selezionare **In blocco inserire nel nuovo**.
7. Immettere il valore di **righe** sotto forma di matrice. Ad esempio, copiare e incollare la stringa seguente:  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
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

- Connettore tronca i nomi delle tabelle di Informix quando che formano i nomi delle azioni app logica. Ad esempio, l'operazione **In blocco inserire NEWORDERS** viene troncato **in blocco**inserire in nuovo.
- Database Informix potrebbe essere tra maiuscole e minuscole ai nomi di tabelle e colonne. I nomi di colonna di inserimento di massa operazione in forma di matrice, ad esempio, potrebbero essere necessario specificare in minuscolo ("IDCliente") anziché lettere maiuscole ("IDCLIENTE").
- Omettendo identità colonne (ad esempio ORDID), le colonne nullable (ad esempio DATASPEDIZIONE) e le colonne con valori predefiniti (ad esempio ORDDATE, REQDATE, IDCORRIERE, trasporto, SHIPCTRY), database Informix genera i valori.
- Se si specifica "oggi" e "domani", il connettore Informix genera "Data corrente" e "Data corrente più un giorno" funzioni (ad esempio REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>App logica con Informix trigger connettore da leggere, modificare o eliminare dati ##
È possibile definire un trigger di app logica per sondaggio e leggere i dati da una tabella di Informix mediante un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovo ordine record dei clienti, restituisce i record per l'app logica. Le impostazioni di connessione di Informix pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | <no value specified>


Inoltre, è possibile definire un trigger di app logica per sondaggio, leggere e modificare i dati in una tabella di Informix mediante un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovi record ordine cliente, aggiornare i valori della riga, restituendo i record selezionati (prima dell'aggiornamento) all'app logica. Le impostazioni di connessione di Informix pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | AGGIORNAMENTO NEWORDERS SET DATASPEDIZIONE = data corrente in cui corrente della &lt;cursore&gt;


Inoltre, è possibile definire un trigger di app logica per sondaggio, leggere e rimuovere i dati da una tabella di Informix utilizzando un'operazione composta API sondaggio dati. Ad esempio, è possibile leggere uno o più nuovi record ordine cliente, eliminare le righe, restituendo i record selezionati (prima dell'eliminazione) all'app logica. Le impostazioni di connessione di Informix pacchetto/app dovrebbero essere come segue:

    Impostazione di App | Valore
--- | --- | ---
PollToCheckData | Seleziona conteggio (\*) da NEWORDERS in DATASPEDIZIONE è NULL
PollToReadData | Selezionare \* da NEWORDERS in DATASPEDIZIONE è NULL per l'aggiornamento
PollToAlterData | Elimina NEWORDERS in effetti corrente &lt;cursore&gt;

In questo esempio app logica verrà sondaggio, leggere, aggiornare e leggere nuovamente i dati nella tabella Informix.

1. Nella finestra startboard Azure, selezionare **+** (segno più), **Web + Mobile**e quindi **app logica**.
2. Immettere il nome (ad esempio "ShipOrdersInformix"), App piano di servizio e altre proprietà e quindi selezionare **Crea**.
3. In startboard Azure, selezionare la logica app che appena creato, le **Impostazioni**e quindi **trigger e azioni**.
4. In e il trigger e le azioni selezionare **creare da zero** all'interno dell'app logica modelli.
5. Nel riquadro App API selezionare **connettore Informix**, impostare una frequenza e intervallo e quindi **segno di spunta**.
6. Nel pannello API App selezionare **connettore Informix**, espandere l'elenco di operazioni e scegliere **Selezionare da NEWORDERS**.
7. Selezionare il **segno di spunta** per salvare le impostazioni di azione e **salvare**. Le impostazioni avrà un aspetto come indicato di seguito:  
![][10]
8. Fare clic su per chiudere e il **trigger e le azioni** e quindi fare clic su per chiudere e **l'Impostazioni** .
9. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente).
10. Selezionare **l'azione** e il **logica app eseguire** .
11. Selezionare il **Collegamento di output**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>App logica con azione connettore Informix per rimuovere i dati ##
È possibile definire un'azione app logica per rimuovere i dati da una tabella di Informix mediante un API Elimina o Post all'operazione di entità OData. Ad esempio, è possibile inserire un nuovo record di ordine cliente, mediante l'elaborazione di un'istruzione SQL INSERT rispetto a una tabella definita con una colonna di identità restituisce il valore di identità o le righe interessate all'app logica (selezionare ORDID dalla tabella finale (valori di inserimento in NEWORDERS (IDCLIENTE, destinatario, SHIPADDR, CITTÀDESTINATARIO, SHIPREG, SHIPZIP) (?,?,?,?,?,?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Creare app logica usando Informix connector per rimuovere i dati ##
È possibile creare una nuova app logica da Azure Marketplace e quindi utilizzare il connettore Informix come un'azione per rimuovere gli ordini dei clienti. Ad esempio, è possibile utilizzare l'operazione di eliminazione Informix condizionale connettore per l'elaborazione di un'istruzione SQL DELETE (Elimina da NEWORDERS in ORDID > = 10000).

1. Nel menu hub della scheda Azure **iniziare** , fare clic su **+** (segno più), fare clic su **Web + Mobile**e quindi fare clic su **app logica**. 
2. In e il **logica creare app** , digitare un **nome**, ad esempio **RemoveOrdersInformix**.
3. Selezionare o definire i valori per le altre impostazioni (piano di servizio, gruppo di risorse).
4. Le impostazioni avrà un aspetto come indicato di seguito. Fare clic su **Crea**:  
![][12]
5. In e **l'Impostazioni** , fare clic su **trigger e azioni**.
6. In e il **trigger e azioni** , nell'elenco delle **app logica modelli** , fare clic su **Crea da zero**.
7. In e il **trigger e azioni** , nel riquadro **App API** all'interno del gruppo di risorse, fare clic su **ricorrenza**.
8. Nella finestra di progettazione logica app, fare clic sull'elemento **ricorrenza** , impostare una **frequenza** e **intervallo**, ad esempio i **giorni** e **1**, quindi scegliere il **segno di spunta** per salvare le impostazioni di elementi di ricorrenza.
9. In e il **trigger e azioni** , nel riquadro **App API** all'interno del gruppo di risorse, fare clic su **connettore Informix**.
10. Nella finestra di progettazione logica app, fare clic su azione **Informix connettore** , fare clic sui puntini di sospensione (**.**) per espandere l'elenco di operazioni e quindi fare clic su **Elimina condizionale da N**.
11. Azione di connettore Informix, digitare **ordid grandi 10000** per un' **espressione che identifica un sottoinsieme di voci**.
12. Fare clic sul **segno di spunta** per salvare le impostazioni di azione e quindi fare clic su **Salva**. Le impostazioni avrà un aspetto come indicato di seguito:  
![][13]
13. Fare clic su per chiudere e il **trigger e le azioni** e quindi fare clic su per chiudere e **l'Impostazioni** .
14. Nell'elenco **tutto viene eseguito** in **operazioni**, selezionare l'elemento nell'elenco prima (esecuzione più recente).
15. Selezionare **l'azione** e il **logica app eseguire** .
16. Selezionare il **Collegamento di output**e **l'azione app logica** . L'output avrà un aspetto come indicato di seguito:  
![][14]

**Nota:** Progettazione di applicazioni logica tronca i nomi delle tabelle. Ad esempio l'operazione **condizionale eliminare da NEWORDERS** viene troncato **condizionale**eliminare da N.


> [AZURE.TIP] Utilizzare le seguenti istruzioni SQL per creare la tabella di esempio e stored procedure. 

È possibile creare la tabella di esempio NEWORDERS utilizzando le seguenti istruzioni DDL SQL Informix:
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


È possibile creare la procedura SPORDERID archiviati esempio utilizzando l'istruzione Informix DDL seguente:
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configurazione ibrida (facoltativo)

> [AZURE.NOTE] Questo passaggio è necessario solo se si utilizza DB2 connettore locale all'interno del firewall.

Gestione configurazione ibrida App utilizza per connettersi al sistema locale. Se il connettore utilizza un locale IBM DB2 Server per Windows, la gestione di connessione ibrida è obbligatorio.

Vedere [utilizzando la gestione di connessione ibrida](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Eseguire operazioni più con il connettore
Una volta creato il connettore, è possibile aggiungere un flusso di lavoro di business un'app di logica. Vedere [quali sono le applicazioni di logica?](app-service-logic-what-are-logic-apps.md).

Creare App API utilizzando le API REST. Vedere [i connettori e API App riferimento](http://go.microsoft.com/fwlink/p/?LinkId=529766).

È anche possibile esaminare sicurezza di statistiche e controllo delle prestazioni per il connettore. Vedere [gestire e monitorare le app API e i connettori incorporati](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png


