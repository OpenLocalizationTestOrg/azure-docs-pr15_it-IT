<properties 
    pageTitle="Per la delega di sottoscrizione di registrazione e prodotto utente" 
    description="Informazioni su come delegato di sottoscrizione di registrazione e prodotto utente a una terza parte in Azure API Management." 
    services="api-management" 
    documentationCenter="" 
    authors="antonba" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="antonba"/>

# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Per la delega di sottoscrizione di registrazione e prodotto utente

Delega consente di utilizzare il sito Web esistente per la gestione di sviluppo accesso-in/sign il e la sottoscrizione a prodotti anziché utilizzare la funzionalità incorporata nel portale per sviluppatori. In questo modo il sito Web per proprietari i dati dell'utente ed eseguire la convalida dei passaggi in modo personalizzato.

## <a name="delegate-signin-up"> </a>Sviluppo di delega accesso e abbonamento

Per delegare sviluppo accesso e per l'abbonamento al sito Web esistente sarà necessario creare un endpoint di delega speciali nel sito che funge da punto di ingresso per qualsiasi domanda avviata da portale per sviluppatori di gestione delle API.

Il flusso di lavoro finale sarà come indicato di seguito:

1. Sviluppo clic sul collegamento di accesso o iscrizione al portale per sviluppatori di gestione delle API
2. Browser viene reindirizzato all'endpoint delega
3. Delega endpoint in Reindirizza a o presenta dell'interfaccia utente che richiede all'utente di accesso o iscrizione
4. Successo, l'utente viene reindirizzato alla pagina di portale per sviluppatori API Management in avviati da


Per iniziare, di seguito prima gestione API di configurazione per instradare le richieste di tramite l'endpoint di delega. Nel portale di publisher API gestione, fare clic su **sicurezza** e quindi fare clic sulla scheda **delega** . Fare clic sulla casella di controllo per attivare 'Delegato accesso & iscrizione'.

![Pagina delega][api-management-delegation-signin-up]

* Decidere quali l'URL dell'endpoint delega speciali verrà e immettere nel campo **URL endpoint delega** . 

* All'interno del campo **chiave di autenticazione delega** immettere un segreto che verrà usato per calcolare una firma fornita per la verifica per assicurarsi che la richiesta sia effettivamente provenienti da Azure API Management. È possibile fare clic sul pulsante **Genera** per API Managemnet in modo casuale generare una chiave dell'utente.

A questo punto è necessario creare l' **endpoint di delega**. Deve eseguire numerose azioni:

1. Ricezione di una richiesta nel formato seguente:

    > *sale e http://www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl= {URL della pagina di origine} = {stringa} & firma = {stringa}*

    Parametri di query per il caso accessohttp:/ / abbonamento:
    - **operazione**: consente di identificare il tipo di delega richiederlo - può essere solo **accesso** in questo caso
    - **returnUrl**: l'URL della pagina in cui l'utente fa clic su un collegamento di accesso o iscrizione
    - **salt**: una stringa salt speciale utilizzata per il calcolo un hash di sicurezza
    - **firma**: un hash protezione calcolato da utilizzare per il confronto con il proprio calcolato hash

2. Verificare che la richiesta provenga da Azure API Management (facoltativo, ma consigliata per la protezione)

    * Calcolare un hash SHA512 HMAC di una stringa in base ai parametri query **returnUrl** e **salt** ([codice riportato di seguito]):
        > HMAC (**salt** "\n" + **returnUrl**)
         
    * Confrontare hash sopra calcolato il valore del parametro di query **firma** . Se i due valori corrispondono, passare alla sezione successiva, in caso contrario rifiutare la richiesta.

2. Verificare che si riceve una richiesta di accesso-in/accesso-up: il parametro di query **operazione** verrà impostato su "**accesso**".

3. Presentare all'utente dell'interfaccia utente di accesso o iscrizione

4. Se l'utente è l'accesso remoto è necessario creare un account corrispondente all'API gestione. [Creare un utente] con l'API REST di gestione dell'API. In tal caso, assicurarsi di impostare l'ID utente allo stesso che si trova nell'archivio utente o un ID che è possibile tenere traccia delle.

5. Quando l'utente viene autenticato correttamente:

    * [richiedere un token di single sign-on (SSO)] tramite l'API REST di gestione delle API

    * aggiungere un parametro di query returnUrl all'URL Single Sign-on ricevuto dalla chiamata API precedente:
        > ad esempio https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 

    * reindirizzare l'utente all'URL prodotto precedente

Oltre l'operazione di **accesso** , è inoltre possibile eseguire la gestione degli account, seguire la procedura precedente e usando una delle operazioni seguenti.

-   **Cambia password**
-   **ChangeProfile**
-   **CloseAccount**

È necessario passare i parametri di query seguenti per le operazioni di gestione di account.

-   **operazione**: consente di identificare il tipo di richiesta di delega è (Cambia password, ChangeProfile o CloseAccount)
-   **ID utente**: l'id utente dell'account da gestire
-   **salt**: una stringa salt speciale utilizzata per il calcolo un hash di sicurezza
-   **firma**: un hash protezione calcolato da utilizzare per il confronto con il proprio calcolato hash

## <a name="delegate-product-subscription"> </a>Delega dell'abbonamento prodotto

Delega dell'abbonamento con codice product è molto simile alla delega dell'utente accessohttp:/ /-up. Il flusso di lavoro finale dovrebbero essere come indicato di seguito:

1. Sviluppatore seleziona un prodotto nel portale per sviluppatori di gestione API e fa clic sul pulsante Sottoscrivi
2. Browser viene reindirizzato all'endpoint delega
3. Endpoint delega esegue operazioni di sottoscrizione di prodotti richiesta - ciò dipende dall'utente e può comportare il reindirizzamento a un'altra pagina per richiedere informazioni sulla fatturazione, domande ulteriori o semplicemente memorizzare le informazioni e che non richiedono un'azione dell'utente


Per abilitare la funzionalità, nella pagina **Delegation** fare clic su **sottoscrizione prodotto delegato**.

Assicurarsi quindi che l'endpoint di delega esegue le operazioni seguenti:


1. Ricezione di una richiesta nel formato seguente:

    > *http://www.yourwebsite.com/apimdelegation?operation= {operazione} & productId = {prodotto sottoscrivere} & ID utente = {utente richiesta} & salt = {stringa} & firma = {stringa}*

    Parametri per il caso di sottoscrizione di prodotto della query:
    - **operazione**: consente di identificare il tipo di richiesta di delega. Per abbonamento prodotto richieste le opzioni valide sono:
        - "La sottoscrizione": una richiesta per la sottoscrizione a un determinato prodotto con l'utente specificato ID (vedere sopra)
        - "Annullare la sottoscrizione": una richiesta per annullare la sottoscrizione di un utente da un prodotto
        - "Rinnovo": un facciano richiesta per rinnovare l'abbonamento (ad esempio, che potrebbe essere in scadenza)
    - **productId**: l'ID del prodotto richiesto dall'utente per la sottoscrizione
    - **ID utente**: l'ID dell'utente per il quale viene effettuata la richiesta
    - **salt**: una stringa salt speciale utilizzata per il calcolo un hash di sicurezza
    - **firma**: un hash protezione calcolato da utilizzare per il confronto con il proprio calcolato hash


2. Verificare che la richiesta provenga da Azure API Management (facoltativo, ma consigliata per la protezione)

    * Calcolare un SHA512 HMAC di una stringa in base ai parametri di query **productId**, **ID utente** e **salt** :
        > HMAC (**salt** "\n" **productId** + "\n" + **ID utente**)
         
    * Confrontare hash sopra calcolato il valore del parametro di query **firma** . Se i due valori corrispondono, passare alla sezione successiva, in caso contrario rifiutare la richiesta.
    
3. Qualsiasi elaborazione prodotto abbonamento in base al tipo di operazione richiesti in **operazione** - ad esempio, di fatturazione, ulteriori domande e così via.

4. Nella sottoscrizione correttamente l'utente per il prodotto sul lato "i", Sottoscrivi l'utente per il prodotto di gestione delle API chiamando [API REST per abbonamento prodotto].

## <a name="delegate-example-code"></a> Codice di esempio ##

Questi esempi di codice viene illustrato come richiedere la *chiave di convalida delega*, che è impostato nella schermata di delega del portale di publisher per creare un HMAC che verrà utilizzato per convalidare la firma, provare la validità dei returnUrl passato. Lo stesso codice funziona per productId e ID utente di modifica di lieve entità.

**Il codice c# per generare hash di returnUrl**

    using System.Security.Cryptography;

    string key = "delegation validation key";
    string returnUrl = "returnUrl query parameter";
    string salt = "salt query parameter";
    string signature;
    using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
    {
        signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
        // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
        // compare signature to sig query parameter
    }


**Codice NodeJS per generare hash di returnUrl**

    var crypto = require('crypto');
    
    var key = 'delegation validation key'; 
    var returnUrl = 'returnUrl query parameter';
    var salt = 'salt query parameter';
    
    var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
    var digest = hmac.update(salt + '\n' + returnUrl).digest();
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
    
    var signature = digest.toString('base64');

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla delega, vedere il video seguente.

> [AZURE.VIDEO delegating-user-authentication-and-product-subscription-to-a-3rd-party-site]

[Delegating developer sign-in and sign-up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[richiedere un token di single sign-on (SSO)]: http://go.microsoft.com/fwlink/?LinkId=507409
[creare un utente]: http://go.microsoft.com/fwlink/?LinkId=507655#CreateUser
[chiamata all'API REST dell'abbonamento con codice product]: http://go.microsoft.com/fwlink/?LinkId=507655#SSO
[Next steps]: #next-steps
[esempio di codice fornita di seguito]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 