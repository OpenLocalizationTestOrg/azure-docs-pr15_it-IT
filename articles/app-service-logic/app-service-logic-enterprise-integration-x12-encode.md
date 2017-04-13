<properties 
    pageTitle="Informazioni sulle Enterprise Integration Pack codificare X12 messaggio Connctor | Servizio di Microsoft Azure App | Microsoft Azure" 
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

# <a name="get-started-with-encode-x12-message"></a>Guida introduttiva a codifica X12 messaggio

Convalida EDI e proprietà specifiche del partner, converte i messaggi con codifica XML in set di transazioni EDI dell'interscambio e le richieste di una conferma tecnici e/o funzionali

## <a name="create-the-connection"></a>Creare la connessione

### <a name="prerequisites"></a>Prerequisiti

* Un account Azure; è possibile creare un [account gratuito](https://azure.microsoft.com/free)

* Per utilizzare codifica x12 messaggio connettore è necessario un Account di integrazione. Visualizzare i dettagli su come creare un [Account di integrazione](./app-service-logic-enterprise-integration-create-integration-account.md), [partner](./app-service-logic-enterprise-integration-partners.md) e [X12 contratto](./app-service-logic-enterprise-integration-x12.md)

### <a name="connect-to-encode-x12-message-using-the-following-steps"></a>Connettersi a messaggio codifica X12 procedendo come segue:

1. [Creare un'App logica](./app-service-logic-create-a-logic-app.md) viene fornito un esempio

2. Il connettore non dispone di tutti i trigger. Usare altri trigger per avviare l'applicazione di logica, ad esempio un trigger di richiesta.  Nella finestra di progettazione logica App, aggiungere un trigger e aggiungere un'azione.  Selezionare Mostra Microsoft API gestite nell'elenco a discesa dell'elenco e quindi immettere "x12" nella casella di ricerca.  Selezionare uno dei due X12-codificare X12 messaggio in base al nome del contratto o X12-codificare al messaggio X 12 dalle identità.  

    ![eseguire una ricerca x12](./media/app-service-logic-enterprise-integration-x12connector/x12decodeimage1.png) 

3. Se è stata creata in precedenza tutte le connessioni all'integrazione di Account, verrà richiesto per i dettagli della connessione

    ![connessione di account di integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage1.png) 


4. Immettere i dettagli di integrazione Account.  Proprietà con un asterisco sono necessari

  	| Proprietà | Dettagli |
  	| -------- | ------- |
  	| Nome della connessione * | Immettere un nome per la connessione |
  	| Integrazione Account * | Immettere il nome dell'Account di integrazione. Assicurarsi che l'Account di integrazione e la logica app siano nella stessa posizione Azure |

    Una volta completato, dettagli relativi alla connessione un aspetto simile al seguente

    ![connessione di account integrazione creato](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage2.png) 


5. Selezionare **Crea**

6. Si noti che la connessione è stata creata.

    ![Dettagli connessione conto integrazione](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage3.png) 

#### <a name="x12---encode-x12-message-by-agreement-name"></a>X12-codificare X12 messaggio in base al nome del contratto

7. Selezionare X12 contratto dal messaggio dell'elenco a discesa e xml da codificare.

    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage4.png) 

#### <a name="x12---encode-x12-message-by-identities"></a>X12-codificare X12 messaggio dalle identità

7.  Specificare l'identificatore del mittente, qualificatore mittente, destinatario identificatore e qualificatore ricevitore in base alla configurazione di X12 contratto.  Selezionare messaggio xml da codificare

    ![specificare i campi obbligatori](./media/app-service-logic-enterprise-integration-x12connector/x12encodeimage5.png) 

## <a name="x12-encode-does-following"></a>X12 codificare sono gli effetti seguenti:

* Risoluzione di contratto confrontando le proprietà di contesto mittente e del destinatario.
* Serializza l'interscambio EDI la conversione di messaggi con codifica XML in set di transazioni EDI dell'interscambio.
* Si applica segmenti di intestazione e finale set transazione
* Genera un numero di controllo di interscambio, un numero di controllo di gruppo e un numero di controllo imposta transazione per ogni interscambio in uscita
* Sostituisce i separatori nei dati payload
* Convalida EDI e proprietà specifiche del partner
    * Convalida dello schema set di transazioni elementi dei dati rispetto al messaggio Schema
    * Convalida EDI sugli elementi di dati di set di transazioni.
    * Convalida estesa eseguita sugli elementi di dati di set di transazioni
* Richiede una conferma tecnici e/o funzionali (se configurati).
    * Una conferma tecnico genera in seguito alla convalida di intestazione. Riconoscimento tecnico segnala lo stato di elaborazione di un'intestazione di interscambio e finale dal destinatario indirizzo
    * Una conferma funzionale genera in seguito alla convalida corpo. Riconoscimento funziona segnala ogni errore durante l'elaborazione documento ricevuto

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni sull'organizzazione Integration Pack] (./app-service-logic-enterprise-integration-overview.md "Informazioni sui Service Pack integrazione Enterprise") 

