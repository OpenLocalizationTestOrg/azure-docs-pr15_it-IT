<properties
   pageTitle="Autenticazione e l'autorizzazione con Power BI incorporata"
   description="Autenticazione e l'autorizzazione con Power BI incorporata"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Autenticazione e l'autorizzazione con Power BI incorporata

Il servizio Power BI incorporato utilizza **chiavi** e **I token di App** per l'autenticazione e autorizzazione, anziché l'autenticazione degli utenti finali espliciti. In questo modello, l'applicazione gestisce l'autenticazione e l'autorizzazione per gli utenti finali. Se necessario, l'app viene creato e invia i token App che indica il servizio per il rendering di report richiesto. Questa progettazione non richiede l'app da usare Azure Active Directory per l'autenticazione degli utenti e autorizzazioni, ma è comunque possibile.

## <a name="two-ways-to-authenticate"></a>Due modi per eseguire l'autenticazione

**Chiave** : È possibile utilizzare i tasti per tutte le chiamate all'API REST di Power BI incorporato. I tasti sono disponibili nel **portale di Azure** facendo clic su **tutte le impostazioni** e quindi **i tasti di scelta**. Considera sempre la chiave come se si trattasse di una password. Questi tasti dispone delle autorizzazioni per effettuare una chiamata su un insieme di un'area di lavoro specifica le API REST.

Per utilizzare una chiave di una chiamata resto, aggiungere l'intestazione di autorizzazione seguenti:            

    Authorization: AppKey {your key}

**Token di app** - App token vengono utilizzati per tutte le richieste di incorporamento. È progettati per essere eseguito sul lato client, in modo che è limitati a un singolo report ed è buona norma impostare un'ora di scadenza.

Token di App sono JWT (JSON Web Token) firmato da uno delle chiavi.

Il token app può contenere domande seguenti:

| Richiesta di rimborso      | Descrizione        |
|--------------|------------|
| **versione**      | La versione del token di app. 0.2.0 è la versione corrente.       |
| **AUD**      | Il destinatario del token. Per usare Power BI incorporata: "https://analysis.windows.net/powerbi/api".  |
| **ISS**      |  Stringa che indica l'applicazione che ha rilasciato il token.    |
| **tipo**     | Il tipo di token app che è stato creato. Corrente l'unico tipo supportato è **incorporare**.   |
| **WCN**      | Nome dell'area di lavoro insieme il token viene rilasciato per.  |
| **WID**      | ID dell'area di lavoro il token alla quale viene rilasciato per.  |
| **Elimina**      | Inviare una segnalazione che ID il token viene rilasciato per.     |
| **nome utente** (facoltativo) |  Utilizzata con RLS, si tratta di una stringa che consente di identificare l'utente quando l'applicazione di regole RLS. |
| **ruoli** (facoltativo)   |   Stringa contenente i ruoli per selezionare quando l'applicazione di regole di sicurezza a livello di riga. Se si passa a più di un ruolo, devono essere passati come matrice di stringa.    |
| **EXP** (facoltativo)    |   Indica il tempo in cui il token scadrà. Questi devono essere passati come Unix timestamp.   |
| **NBF** (facoltativo)    |   Indica il tempo in cui il token inizia da valido. Questi devono essere passati come Unix timestamp.   |

Un token di app esempio aspetto simile al seguente:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Quando decodificare, il messaggio si presenterà come segue:

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Ecco come funziona il flusso

1. Copiare i tasti di API per l'applicazione. È possibile ottenere le chiavi nel **Portale di Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Token indica una domanda e ha una scadenza.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Token viene effettuato l'accesso con un tasto di scelta API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Richieste degli utenti per visualizzare un report.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Token viene convalidata con un tasto di scelta API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI incorporata invia un report utente.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Dopo che un report Invia **Power BI incorporato** all'utente, l'utente può visualizzare il report nell'app personalizzate. Ad esempio, se sono stati importati [analisi vendite dati PBIX esempio](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l'app web di esempio da aspetto seguente:

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Vedere anche
- [Introduzione a Microsoft Power BI incorporata campione](power-bi-embedded-get-started-sample.md)
- [Scenari comuni di Microsoft Power BI incorporata](power-bi-embedded-scenarios.md)
- [Introduzione a Microsoft Power BI incorporata](power-bi-embedded-get-started.md)
