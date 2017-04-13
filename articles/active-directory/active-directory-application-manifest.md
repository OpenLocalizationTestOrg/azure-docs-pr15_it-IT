<properties
   pageTitle="Informazioni sulle manifesto dell'applicazione Azure Active Directory | Microsoft Azure"
   description="Descrizione dettagliata del manifesto dell'applicazione Azure Active Directory, che rappresenta la configurazione di identità dell'applicazione in un tenant di Azure Active Directory e viene utilizzato per facilitare OAuth esperienza consenso, autorizzazioni e altro ancora."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/11/2016"
   ms.author="dkershaw;bryanla"/>

# <a name="understanding-the-azure-active-directory-application-manifest"></a>Informazioni sulle manifesto dell'applicazione Azure Active Directory

Applicazioni che si integrano con Azure Active Directory (AD) devono essere registrate con un tenant di Azure Active Directory, fornire una configurazione persistente identità dell'applicazione. Questa configurazione viene consultata in fase di esecuzione, abilitazione di scenari che consentono di un'applicazione di affidare e negoziare autenticazione/autorizzazione tramite Azure Active Directory. Per ulteriori informazioni sul modello di applicazione di Azure Active Directory, vedere [aggiunta, aggiornamento e la rimozione di un'applicazione] [ ADD-UPD-RMV-APP] articolo.

## <a name="updating-an-applications-identity-configuration"></a>Aggiornamento della configurazione di identità dell'applicazione

In effetti disponibili più opzioni sono disponibili per aggiornare le proprietà sulla configurazione di identità dell'applicazione, che variano in funzionalità e gradi di difficoltà, incluse le operazioni seguenti:

- Il ** [Azure classica portal] [ AZURE-CLASSIC-PORTAL] interfaccia utente Web** consente di aggiornare le proprietà più comuni di un'applicazione. Questa è la modalità di soggetto a più veloce e almeno errore di aggiornamento delle proprietà dell'applicazione, ma non offre accesso completo a tutte le proprietà, ad esempio i due metodi.
- Per scenari più avanzati in cui è necessario aggiornare le proprietà che non vengono esposti nel portale di classica Azure, è possibile modificare il file **manifesto dell'applicazione**. Questo è lo stato attivo di questo articolo e viene spiegato in modo più dettagliato partire nella sezione successiva.
- È anche possibile **scrivere un'applicazione che utilizza l' [API grafico] [ GRAPH-API] ** per aggiornare l'applicazione che richiede il maggior numero di programmazione. È possibile un'opzione interessante se, se si sta scrivendo software per la gestione o necessario aggiornare le proprietà delle applicazioni a intervalli regolari in modo automatico.

## <a name="using-the-application-manifest-to-update-an-applications-identity-configuration"></a>Utilizzo di manifesto dell'applicazione per aggiornare la configurazione di identità dell'applicazione
Tramite il [portale classica Azure][AZURE-CLASSIC-PORTAL], configurazione di identità dell'applicazione, è possibile gestire tramite download e caricamento di un JSON file rappresentazione, ossia un manifesto dell'applicazione. Nessun effettivo del file è archiviato nella directory. Manifesto dell'applicazione è semplicemente un'operazione HTTP GET su entità applicazione API di Azure Active Directory grafico e il caricamento è un'operazione HTTP PATCH sull'entità dell'applicazione.

Di conseguenza, per comprendere il formato e le proprietà del manifesto dell'applicazione, è necessario fare riferimento all' API grafico [entità applicazione] [ APPLICATION-ENTITY] documentazione. Esempi di aggiornamenti che possono essere eseguiti anche se upload manifesto dell'applicazione:

- **Ambiti di autorizzazione Declare (oauth2Permissions)** esposto dall'API web. Vedere l'argomento "Esposizione delle API di Web da altre applicazioni" nelle [Applicazioni di integrazione con Azure Active Directory] [ INTEGRATING-APPLICATIONS-AAD] per informazioni sull'implementazione rappresentazione dell'utente utilizzando il oauth2Permissions delegata ambito di autorizzazione. Come detto in precedenza, le proprietà delle entità applicazione sono descritte in Graph API [entità e tipo complesso] [ APPLICATION-ENTITY] articolo di riferimento, inclusa la proprietà oauth2Permissions che rappresenta un insieme di tipo [OAuth2Permission][APPLICATION-ENTITY-OAUTH2-PERMISSION].
- **Ruoli di applicazione Declare (appRoles) esposti dall'applicazione**. Proprietà appRoles dell'entità applicazione è un insieme di tipo [AppRole][APPLICATION-ENTITY-APP-ROLE]. Vedere il [controllo dell'accesso nelle applicazioni cloud utilizzando Azure Active Directory basato sui ruoli] [ RBAC-CLOUD-APPS-AZUREAD] articolo per un esempio di implementazione.
- **Una dichiarazione noti client applicazioni (knownClientApplications)**, che consentono di collegare in modo logico il consenso delle applicazioni client specificato per la risorsa/web API.
- **Richiesta di Azure Active Directory di rilasciare il gruppo appartenenze richiedere** l'accesso all'utente (groupMembershipClaims).  Questa operazione può essere configurata per il rilascio di reclami sulle appartenenze ai ruoli di directory dell'utente. Vedere [le autorizzazioni in applicazioni Cloud mediante i gruppi di Active Directory] [ AAD-GROUPS-FOR-AUTHORIZATION] articolo per un esempio di implementazione.
- **Consenti all'applicazione di supportare OAuth 2.0 impliciti concedere** flussi (oauth2AllowImplicitFlow). Questo tipo di flusso grant viene utilizzato con pagine web JavaScript incorporate o singola pagina applicazioni (SPA). Per ulteriori informazioni su Concedi autorizzazioni impliciti, vedere [informazioni sui OAuth2 impliciti concedere del flusso di Azure Active Directory][IMPLICIT-GRANT].
- **Consente di utilizzare le X509 certificati come la chiave privata** (keyCredentials). [Creazione di applicazioni di servizio e daemon in Office 365] [ O365-SERVICE-DAEMON-APPS] e [Guida per gli sviluppatori per auth con Azure Manager delle risorse API] [ DEV-GUIDE-TO-AUTH-WITH-ARM] articoli che descrivono per esempi di implementazione.
- **Aggiungere un nuovo URI ID App** per l'applicazione (identifierURIs[]). App ID URI vengono utilizzati per identificare un'applicazione all'interno il tenant di Azure Active Directory (o tra più tenant di Azure Active Directory, per gli scenari multi-tenant quando qualificato tramite verificato dominio personalizzato). Vengono utilizzati durante la richiesta di autorizzazioni per un'applicazione di risorse o durante l'acquisizione di un token di accesso per un'applicazione di risorse. Quando si aggiorna questo elemento, lo stesso aggiornamento è una chiamata effettuato all'insieme di [] gli attributi SPN dell'identità di servizio corrispondente, si trova nel tenant iniziale dell'applicazione.

Manifesto dell'applicazione vengono forniti anche un'ottima soluzione per tenere traccia dello stato della registrazione dell'applicazione. Perché è disponibile in formato JSON, è possibile controllare la rappresentazione di file nel controllo origine, insieme al codice sorgente dell'applicazione.

## <a name="step-by-step-example"></a>Istruzioni per l'esempio di passaggio
Ora consente di eseguire i passaggi necessari per aggiornare la configurazione di identità dell'applicazione tramite il manifesto dell'applicazione. Verranno evidenziati uno degli esempi precedenti, che mostra come dichiarare un nuovo ambito di autorizzazione in un'applicazione di risorsa:

1. Passare al [portale classica Azure] [ AZURE-CLASSIC-PORTAL] ed effettuare l'accesso con un account che dispone di privilegi di amministratore o CO-servizio.

2. Dopo aver autenticato, scorrere verso il basso e selezionare l'estensione di Azure "Active Directory" nel riquadro di spostamento a sinistra (1) e fare clic su tenant di Azure Active Directory in cui l'applicazione è registrato (2).

    ![Selezionare il tenant di Azure Active Directory][SELECT-AZURE-AD-TENANT]

3. Se la pagina directory insorge, fare clic su "Applicazioni" (1) nella parte superiore della pagina per visualizzare un elenco di applicazioni registrate nel tenant. Individuare quindi l'applicazione che si desidera aggiornare nell'elenco e fare clic su di esso (2).

    ![Selezionare il tenant di Azure Active Directory][SELECT-AZURE-AD-APP]

4. Dopo aver selezionato pagina principale dell'applicazione, si noterà la caratteristica "Gestisci manifesto" nella parte inferiore della pagina (1). Se si fa clic su questo collegamento, verrà richiesto di scaricare o caricare il file manifesto JSON. Fare clic su "Download manifesto" (2). Questo sarà immediatamente seguito dalla finestra di dialogo Conferma download viene richiesto di confermare facendo clic su "Scaricare manifesto" (3), quindi aprire o salvare il file in locale (4).

    ![Gestire il manifesto, opzioni di download][MANAGE-MANIFEST-DOWNLOAD]

    ![Scaricare il manifesto][DOWNLOAD-MANIFEST]

5. In questo esempio è salvato il file in locale, che consente di aprire in un editor, apportare modifiche alla JSON e salvare nuovamente. Ecco è simile alla struttura JSON all'interno dell'editor di Visual Studio JSON. Nota che segue lo schema per l' [entità applicazione] [ APPLICATION-ENTITY] come indicato in precedenza:

    ![Aggiornare JSON manifesto][UPDATE-MANIFEST]

    Ad esempio, se che si desidera implementare/espongono un'autorizzazione nuova denominata "Employees.Read.All" sull'applicazione delle risorse (API), è sufficiente aggiungere un elemento di nuovo al secondo alla raccolta oauth2Permissions ie:

        "oauth2Permissions": [
        {
        "adminConsentDescription": "Allow the application to access MyWebApplication on behalf of the signed-in user.",
        "adminConsentDisplayName": "Access MyWebApplication",
        "id": "aade5b35-ea3e-481c-b38d-cba4c78682a0",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to access MyWebApplication on your behalf.",
        "userConsentDisplayName": "Access MyWebApplication",
        "value": "user_impersonation"
        },
        {
        "adminConsentDescription": "Allow the application to have read-only access to all Employee data.",
        "adminConsentDisplayName": "Read-only access to Employee records",
        "id": "2b351394-d7a7-4a84-841e-08a6a17e4cb8",
        "isEnabled": true,
        "type": "User",
        "userConsentDescription": "Allow the application to have read-only access to your Employee data.",
        "userConsentDisplayName": "Read-only access to your Employee records",
        "value": "Employees.Read.All"
        }
        ],

    La voce deve essere univoca e pertanto è necessario generare una nuova globalmente ID (GUID) per la `"id"` proprietà. In questo caso, poiché è specificato `"type": "User"`, questa autorizzazione possa acconsentita da qualsiasi account autenticati da Azure Active Directory del tenant in cui l'applicazione delle risorse/API registrata. Questo modo si concede il client applicazione l'autorizzazione ad accedervi per conto dell'account. Stringhe di nome descrizione e la visualizzazione vengono utilizzate durante consenso dell'utente e la visualizzazione nel portale di classica Azure.

6. Dopo aver ad aggiornare il manifesto, tornare alla pagina dell'applicazione Azure Active Directory nel portale di classica Azure e fare clic su di "Gestisci manifesto" nuovamente (1). Ma questa volta, selezionare l'opzione "Caricare manifesto" (2). Simile per il download, verrà visualizzato nuovamente con una seconda finestra di dialogo che richiede di relativa al percorso del file JSON. Fare clic su "Cerca file..." (3), quindi utilizzare la finestra di dialogo "Scegliere File per caricare" per selezionare il file JSON (4), quindi premere "Apri". Dopo la finestra di dialogo si risolve, selezionare la casella di controllo "OK" (5) e il manifesto verrà caricato.  

    ![Gestire il manifesto, caricare opzione][MANAGE-MANIFEST-UPLOAD]

    ![Caricare JSON manifesto][UPLOAD-MANIFEST]

    ![Caricare JSON manifesto - conferma][UPLOAD-MANIFEST-CONFIRM]

Dopo aver salvato il manifesto, è possibile assegnare un accesso di applicazione client per la nuova autorizzazione abbiamo aggiunto in precedenza. Questa volta è possibile utilizzare dell'interfaccia utente Web del portale classico Azure anziché la modifica manifesto dell'applicazione client:  

1. Prima di tutto accedere alla pagina "Configura" dell'applicazione client a cui si desidera aggiungere la nuova API di accesso e fare clic sul pulsante "Aggiungi applicazione".
2. Quindi verrà visualizzato con l'elenco delle applicazioni di risorsa registrata (API) nel tenant. Fare clic sul segno più / + simbolo accanto al nome dell'applicazione delle risorse per selezionarla.  
3. Fare clic sul segno di spunta nell'angolo inferiore destro.
4. Quando si torna nella sezione "Aggiungi applicazione" della pagina di configurazione del cliente, verrà visualizzata la nuova applicazione di risorsa nell'elenco. Se si passa il mouse sopra la sezione "Delegato autorizzazioni" a destra della riga, si verrà visualizzato un elenco a discesa visualizzato. Fare clic sull'elenco, quindi selezionare la nuova autorizzazione per aggiungerlo all'elenco di richiesta del client di autorizzazioni. Nota: la nuova autorizzazione verrà archiviata nella configurazione di identità dell'applicazione client, nella proprietà dell'insieme "requiredResourceAccess".

![Autorizzazioni per le altre applicazioni][PERMS-TO-OTHER-APPS]

![Autorizzazioni per le altre applicazioni][PERMS-SELECT-APP]

![Autorizzazioni per le altre applicazioni][PERMS-SELECT-PERMS]

Questo è tutto. Ora le applicazioni verranno eseguite con la nuova configurazione di identità.

## <a name="next-steps"></a>Passaggi successivi
- Per ulteriori informazioni sulla relazione tra gli oggetti dell'applicazione e principale del servizio di un'applicazione, vedere [applicazione e servizio principale in Azure Active Directory][AAD-APP-OBJECTS].
- Vedere il [glossario per sviluppatori di Azure Active Directory] [ AAD-DEVELOPER-GLOSSARY] per le definizioni di alcuni dei concetti di sviluppo di Azure Active Directory (AD) principali.

Utilizzare la sezione commenti DISQUS riportata di seguito per inviare commenti e suggerimenti degli perfezionare e definire il contenuto.

<!--Image references-->
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[PERMS-SELECT-APP]: ./media/active-directory-application-manifest/portal-perms-select-app.png
[PERMS-SELECT-PERMS]: ./media/active-directory-application-manifest/portal-perms-select-perms.png
[PERMS-TO-OTHER-APPS]: ./media/active-directory-application-manifest/portal-perms-to-other-apps.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-APP-OBJECTS]: active-directory-application-objects.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]: active-directory-dev-understanding-oauth2-implicit-grant.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

