<properties 
    pageTitle="Informazioni sulle Enterprise Integration Pack decodificare X12 messaggio Connctor | Servizio di Microsoft Azure App | Microsoft Azure" 
    description="Informazioni su come usare i partner con le app Enterprise Integration Pack e logica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="padmavc" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="padmavc"/>

# <a name="get-started-with-decode-x12-message"></a>Guida introduttiva a Decode X12 messaggio

Convalida EDI e proprietà specifiche del partner, vengono generate documento XML per ogni set di transazione e conferma per transazione elaborata.

## <a name="create-the-connection"></a>Creare la connessione

### <a name="prerequisites"></a>Prerequisiti

* Un account Azure; è possibile creare un [account gratuito](https://azure.microsoft.com/free)

* Per utilizzare Decode X12 messaggio connettore è necessario un Account di integrazione. Visualizzare i dettagli su come creare un [Account di integrazione](./app-service-logic-enterprise-integration-create-integration-account.md), [partner](./app-service-logic-enterprise-integration-partners.md) e [X12 contratto](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-decode-x12-message-using-the-following-steps"></a>Connettersi a messaggio Decode X12 procedendo come segue:

1. [Creare un'App logica](./app-service-logic-create-a-logic-app.md) viene fornito un esempio

2. Il connettore non dispone di tutti i trigger. Usare altri trigger per avviare l'applicazione di logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione logica App, aggiungere un trigger e aggiungere un'azione.  Selezionare Mostra Microsoft API gestite nell'elenco a discesa dell'elenco e quindi immettere "x12" nella casella di ricerca.  Selezionare X12 – decodificare X12 messaggio

    ![eseguire una ricerca x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png)  

3. Se è stata creata in precedenza tutte le connessioni all'integrazione di Account, verrà richiesto per i dettagli della connessione

    ![connessione di account di integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage4.png)    

4. Immettere i dettagli di integrazione Account.  Proprietà con un asterisco sono necessari

  	| Proprietà | Dettagli |
  	| -------- | ------- |
  	| Nome della connessione * | Immettere un nome per la connessione |
  	| Integrazione Account * | Immettere il nome dell'Account di integrazione. Assicurarsi che l'Account di integrazione e la logica app siano nella stessa posizione Azure |

    Una volta completato, dettagli relativi alla connessione un aspetto simile al seguente
    
    ![connessione di account integrazione creato](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage5.png) 

5. Selezionare **Crea**
    
6. Si noti che la connessione è stata creata.

    ![Dettagli connessione conto integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage6.png) 

7. Selezionare X12 piatta messaggio file decodificare

    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage7.png) 

## <a name="x12-decode-does-following"></a>Decodificare X12 funziona seguendo

* Convalida della busta contro trading contratto per i partner
* Generazione di un documento XML per il set di ogni transazione.
* Convalida EDI e proprietà specifiche del partner
    * Convalida strutturale EDI e la convalida degli schemi "Extended"
    * Convalida della struttura della busta interscambio.
    * Convalida dello schema della busta lo schema di controllo.
    * Convalida dello schema set di transazioni elementi dei dati in base allo schema messaggio.
    * Convalida EDI eseguita sugli elementi di dati di set di transazioni 
* Verifica che i numeri di controllo imposta interscambio, i gruppi e transazione non siano duplicati
    * Controlla il numero di controllo di interscambio con svincoli ricevuti in precedenza.
    * Controlla il numero di controllo di gruppo con altri numeri di controllo del gruppo di interscambio.
    * Verifica che la transazione imposta il numero di controllo con altri numeri di controllo transazione set di tale gruppo.
* Converte l'intero interscambio XML 
    * Dividi interscambio come transazione set - sospendere set transazioni errore: analizza ogni transazione impostare un interscambio in un documento XML separato. Se uno o più transazioni imposta l'interscambio non vengono convalidati, X12 Decode sospende solo i set di transazioni.
    * Dividi interscambio come transazione set - sospendere interscambio errore: analizza ogni transazione impostare un interscambio in un documento XML separato.  Se uno o più transazioni imposta l'interscambio non vengono convalidati, X12 Decode sospende l'intero interscambio.
    * Mantenere interscambio - sospendere set transazioni errore: crea un documento XML per l'intero interscambio in batch. X12 Decode sospende solo i set di transazioni non convalidati, pur continuando per elaborare tutti gli altri transazione imposta
    * Mantenere interscambio - sospendere interscambio errore: crea un documento XML per l'intero interscambio in batch. Se uno o più transazioni imposta l'interscambio non vengono convalidati, X12 Decode sospende l'intero interscambio 
* Genera una conferma tecnici e/o funzionali (se configurati).
    * Una conferma tecnico genera in seguito alla convalida di intestazione. Riconoscimento tecnico segnala lo stato di elaborazione di un'intestazione di interscambio e finale dal destinatario indirizzo.
    * Una conferma funzionale genera in seguito alla convalida corpo. Riconoscimento funziona segnala ogni errore durante l'elaborazione documento ricevuto

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'organizzazione Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Informazioni sui Service Pack integrazione Enterprise") 


