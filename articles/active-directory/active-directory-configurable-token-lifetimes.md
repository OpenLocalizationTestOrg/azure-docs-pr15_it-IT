<properties
   pageTitle="Durata Token configurabile di Azure Active Directory | Microsoft Azure"
   description="Questa funzionalità è utilizzata da amministratori e sviluppatori per specificare la durata di token emessi Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/06/2016"
   ms.author="billmath"/>


# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Durata Token configurabile di Azure Active Directory (pubblico anteprima)

>[AZURE.NOTE]
>Questa funzionalità è attualmente in anteprima pubblica.  Devono essere preparati a ripristinare o rimuovere le modifiche.  Si sta aprendo questa caratteristica per tutti gli utenti a provare durante l'anteprima di pubblico, tuttavia, alcuni aspetti potrebbero richiedere un [abbonamento di Azure Active Directory Premium](active-directory-get-started-premium.md) dopo generalmente disponibile.


## <a name="introduction"></a>Introduzione
Questa funzionalità è utilizzata da amministratori e sviluppatori per specificare la durata di token emessi Azure Active Directory. Durata token può essere configurata per tutte le App in un tenant, per un'applicazione multi-tenant o per un'entità servizio specifici in un tenant.

In Azure Active Directory, un oggetto Criteri rappresenta un set di regole rispettate le singole applicazioni o tutte le applicazioni in un tenant.  Ogni tipo di criterio presenta una struttura univoca con un insieme di proprietà che vengono quindi applicati a oggetti a cui sono state assegnate.

È possibile impostare un criterio come predefinito per un tenant. Questo criterio avrà quindi effetto su tutte le applicazioni che si trovano all'interno del tenant, purché non sottoposto a override da un criterio con priorità alta. Criteri possono anche essere assegnati alle applicazioni specifiche. L'ordine di priorità varia in base al tipo di criteri.

Durata possono essere configurati per i token di aggiornamento, token di accesso, token di sessione e token ID.


### <a name="access-tokens"></a>Token di accesso
Token di accesso viene utilizzato da un client per accedere a una risorsa protetta. Token di accesso utilizzabili solo per una specifica combinazione di utenti, client e delle risorse. Token di accesso non è possibile revocare e sono validi finché il periodo di validità. Un attore dannoso che è possibile token di accesso è possibile usarlo per misura della durata.  Modifica della durata token di accesso è un compromesso tra le prestazioni del sistema e aumentare la quantità di tempo che il client mantiene l'accesso dopo aver disabilitato l'account dell'utente.  Le prestazioni di sistema migliorato è possibile ridurre il numero di volte in cui che è necessario acquisire un token di accesso aggiornato un client. 

### <a name="refresh-tokens"></a>Aggiornare i token
Quando un client acquisisce un token di accesso per accedere a una risorsa protetta, riceve un token di aggiornamento e un token di accesso. Il token di aggiornamento viene utilizzato per ottenere accesso e aggiornare nuovo coppie token quando scade token di accesso corrente. Aggiornare i token sono associati a combinazioni di client e utente. È possibile revocato e la validità sia selezionata ogni volta che vengono utilizzati.

È importante distinguere tra i client di informazioni riservati e pubblici. Client riservate sono applicazioni che sono in grado di archiviare in modo sicuro una password di client, che consente loro di provare che le richieste di provenienza l'applicazione client e non un attore dannoso. Questi flussi sono più sicuro, le durate predefinite di aggiornamento i token emessi a questi flussi più alta e non possono essere modificati con criteri.

A causa di limitazioni dell'ambiente che le applicazioni vengono eseguite in, client pubblico non è possibile archiviare in modo sicuro una password di client. Per impedire che i token di aggiornamento da client pubblico precedenti a un periodo specificato come ottenere una nuova coppia token di accesso e aggiornare (aggiornare Token tempo inattivo Max), è possono impostare criteri sulle risorse.  Inoltre, i criteri possono essere utilizzati per impostare un periodo di tempo oltre il quale l'aggiornamento token non sono più accettate (aggiornare Token Max Age).  Regolazione della durata token di aggiornamento consente di controllare quando e con quale frequenza l'utente è necessario e reimmettere le credenziali anziché viene automaticamente nuovamente autenticato quando si usa un'applicazione client pubblico.


### <a name="id-tokens"></a>Token ID
ID token vengono passati a siti web e client nativi e contengono informazioni del profilo relative a un utente. Token ID è associato a una combinazione specifica di client e utente. Token ID sono considerate valide fino alla scadenza.  In genere, un'applicazione web che corrisponde a un utente di durata della sessione nell'applicazione per la durata del token ID generati per l'utente.  Modifica della durata di token ID consente di controllare con quale frequenza l'applicazione web scadere della sessione dell'applicazione e richiedere all'utente di essere autenticati nuovamente con Azure Active Directory (automaticamente o in modo interattivo).

### <a name="single-sign-on-session-token"></a>Token di sessione Single sign-on
Quando un utente viene autenticato con Azure Active Directory, viene stabilita una sessione con l'utente del browser e Azure Active Directory.  Il Single Sign-On sessione Token, sotto forma di un cookie rappresenta questa sessione. È importante tenere presente che il token di sessione Single Sign-On non è associato a un'applicazione client/risorsa specifico. Può essere revocato token di sessione Single Sign-on e la validità sia selezionata ogni volta che vengono utilizzati.

Esistono due tipi di token di sessione Single Sign-on. Token di sessione permanenti vengono archiviati come i cookie permanenti tramite il browser e il token di sessione non permanente vengono memorizzati come cookie (questi vengono eliminati quando il browser viene chiuso).

Token di sessione persistente non hanno una durata di 24 ore mentre i token persistenti avere una durata di 180 giorni. Ogni volta che viene utilizzato il token di sessione Single Sign-on all'interno del periodo di validità, il periodo di validità viene esteso un altro 24 ore o 180 giorni. Se la sessione di Single Sign-on token non viene usata all'interno del periodo di validità, viene considerato scaduto e non è più essere accettato dall'utente. 

Criteri possono essere utilizzati per impostare un periodo di tempo dopo la prima sessione è stato rilasciato token oltre il quale i token di sessione non sono più accettate (sessione Token Max Age).  Modifica della durata token di sessione consente di controllare quando e con quale frequenza l'utente è necessario reimmettere le credenziali anziché l'autenticazione automaticamente quando si usa un'applicazione web.

### <a name="token-lifetime-policy-properties"></a>Proprietà dei criteri di durata
Criteri di durata sono un tipo di oggetto criteri che contiene le regole di durata.  Le proprietà dei criteri vengono utilizzate per controllare durata token specificata.  Se non è impostato alcun criterio, il sistema applica il valore di durata predefinito.


### <a name="configurable-token-lifetime-properties"></a>Proprietà configurabili durata
Proprietà|Stringa di proprietà dei criteri|Viene applicata a|Impostazione predefinita|Valore minimo|Massimo|
----- | ----- | ----- |----- | ----- | ----- |
Durata Token di accesso|  AccessTokenLifetime|Token di accesso, token ID, i token SAML2|1 ora|10 minuti|1 giorno|
L'aggiornamento di inattività massimo Token|    MaxInactiveTime |Aggiornare i token |14 giorni|10 minuti|    90 giorni|
Età Max Token fattore singolo aggiornamento|    MaxAgeSingleFactor  |Aggiornare i token (per tutti gli utenti) |90 giorni|10 minuti |Fino a revocato *|
Età Max Token l'aggiornamento a più fattori| MaxAgeMultiFactor|  Aggiornare i token (per tutti gli utenti)| 90 giorni|10 minuti|Fino a revocato *|
Fattore di singola sessione Token Max età |MaxAgeSessionSingleFactor * *    |Token di sessione (permanente e non permanente)| Fino a revocato   |10 minuti |Fino a revocato *|
Età Max Token sessione a più fattori| MaxAgeSessionMultiFactor * * *|    Token di sessione (permanente e non permanente)| Fino a revocato|  10 minuti| Fino a revocato *



- * 365 giorni è la lunghezza massima esplicita che può essere impostata per gli attributi.
- * * Se non è supportata MaxAgeSessionSingleFactor questo valore ha il valore di MaxAgeSingleFactor. Se nessuno dei parametri è impostata, la proprietà assume il valore predefinito (fino a revocato).
- Se non è impostata MaxAgeSessionMultiFactor questo valore prende il valore di MaxAgeMultiFactor. Se nessuno dei parametri è impostata, la proprietà assume il valore predefinito (fino a revocato).

### <a name="exceptions"></a>Eccezioni
Proprietà|Viene applicata a|Impostazione predefinita|
----- | ----- | ----- |
Aggiornare i Token Max inattività (utenti federati con le informazioni di revoca insufficiente)|Aggiornare i token (rilasciato per gli utenti federati con le informazioni di revoca insufficiente)|12 ore|
L'aggiornamento di inattività massimo Token (client riservate)| Aggiornare i token (rilasciato per i client riservate)|90 giorni|
Aggiornare i token Max Age (rilasciato per i client riservate) |   Aggiornare i token (rilasciato per i client riservate) |Fino a revocato

### <a name="priority-and-evaluation-of-policies"></a>Priorità e valutazione dei criteri

Criteri di durata token possono essere creati e assegnati alle applicazioni specifiche, tenant e identità di servizio. Ciò significa che è possibile che più criteri da applicare a un'applicazione specifica. I criteri di durata Token che ha effetto segue queste regole:


- Se per il capitale di servizio in modo esplicito è assegnato un criterio, verranno applicata. 
- Se per il capitale di servizio in modo esplicito è assegnato alcun criterio, verrà applicato un criterio assegnato esplicitamente nel tenant di padre del servizio principale. 
- Se per il servizio principale o al tenant descritta in modo esplicito è assegnato alcun criterio, il criterio assegnato all'applicazione verrà applicato. 
- Se non è stato assegnato alcun criterio principale del servizio, tenant o sull'oggetto applicazione, i valori predefiniti verranno applicati (vedere la tabella precedente).

Per ulteriori informazioni sulla relazione tra gli oggetti dell'applicazione e servizio principale in Azure Active Directory, vedere [applicazione e oggetti principale del servizio di Azure Active Directory](active-directory-application-objects.md).

Validità del token viene valutata al momento in cui che viene utilizzato. Il criterio con la massima priorità sull'applicazione che si accede ha effetto.


>[AZURE.NOTE]
>Esempio
>
>L'utente desidera accedere 2 nelle applicazioni web A e B. 
>
>
>- Entrambe le applicazioni sono nello stesso tenant padre. 
>- Criteri di durata token 1 con una sessione di Token Max Age di 8 ore viene impostato come predefinito del tenant padre.
>- Applicazione Web A un'applicazione web normali e non è collegato a tutti i criteri. 
>- Applicazione Web B viene utilizzato per i processi riservati e il relativo principale del servizio è collegato ai criteri di durata 2 con Token Max Age una sessione di 30 minuti.
>
>12:00 PM viene aperta una nuova sessione del browser e tenta di accedere applicazione web risposte l'utente viene reindirizzato alla Azure Active Directory e viene chiesto di accesso. In questa procedura si elimina un cookie con un token di sessione nel browser. L'utente viene reindirizzato indietro per un oggetto di applicazione con un token di ID che consente di accedere all'applicazione web.
>
>12:15 PM, l'utente tenta di accedere applicazione web B. Il browser reindirizza Azure Active Directory che consente di rilevare cookie della sessione. Principale del servizio Web applicazione B è collegato a un criterio di 1, ma è anche parte del tenant padre con il criterio predefinito 2. 2 ha effetto poiché criteri collegati alle identità di servizio hanno priorità più alta criteri predefiniti per il tenant. Il token di sessione è stato pubblicato originariamente negli ultimi 30 minuti affinché sia considerato valido. L'utente viene reindirizzato all'applicazione web B dotato di un ID concedere l'accesso.
>
>1:00 PM l'utente tenta di spostarsi A. applicazione web L'utente viene reindirizzato alla Azure Active Directory. Applicazione Web non è collegato a tutti i criteri, ma poiché si tratta di un tenant con il criterio predefinito 1, questo criterio ha effetto. La sessione cookie viene rilevato che è stata pubblicata originariamente all'interno delle ultime 8 ore e l'utente viene reindirizzata automaticamente all'applicazione web risposte con un nuovo token ID senza la necessità di eseguire l'autenticazione.
>
>L'utente immediatamente tenta di accedere applicazione web B. L'utente viene reindirizzato alla Azure Active Directory. Come prima, 2 ha effetto. Mentre il token è stato rilasciato più di 30 minuti, viene quindi richiesto di immettere nuovamente le proprie credenziali e una nuova sessione e token ID vengono emessi. L'utente può accedere quindi applicazione web B.

## <a name="configurable-policy-properties-in-depth"></a>Proprietà criteri configurabili: in modo completo

### <a name="access-token-lifetime"></a>Durata token di accesso

**Stringa:** AccessTokenLifetime

**Viene applicata a:** Token di accesso, token ID

**Riepilogo:** Questo criterio controlla quanto tempo token ID per la risorsa e accesso sono considerate valide. Ridurre la durata di token di accesso consente di ridurre i rischi di un token di ID utilizzato da un attore dannoso per un periodo di tempo (come non può essere revocati) o di access, ma influisce negativamente sulle prestazioni durante i token devono essere sostituite più spesso.

### <a name="refresh-token-max-inactive-time"></a>Aggiornare i token inattività massimo

**Stringa:** MaxInactiveTime

**Viene applicata a:** Aggiornare i token

**Riepilogo:** Questo criterio controlla come precedente può essere un token di aggiornamento prima di un client non è più usarla per recuperare una nuova coppia di token di accesso e aggiornare quando si tenta di accedere a questa risorsa. Poiché un nuovo token di aggiornamento è in genere restituito che viene utilizzato un token di aggiornamento, il client non deve aver raggiunto a tutte le risorse utilizzando il token di aggiornamento corrente per il periodo di tempo prima che questo criterio da impedire l'accesso specificato. 

Questo criterio impone gli utenti che non è stato attivi su un client di effettuare nuovamente l'autenticazione per recuperare un nuovo token di aggiornamento. 

È importante tenere presente che l'aggiornamento Token inattivo tempo massimo deve essere impostato su un valore inferiore fattore singolo Token Max Age e a più fattori aggiornare Token Max Age.

### <a name="single-factor-refresh-token-max-age"></a>Età max token fattore singolo aggiornamento

**Stringa:** MaxAgeSingleFactor

**Viene applicata a:** Aggiornare i token

**Riepilogo:** Questo criterio consente il periodo di tempo un utente può continuare a usare i token di aggiornamento per ottenere accesso e aggiornare nuovo coppie token dopo l'ultima volta che tali autenticati correttamente con un solo ad un fattore. Dopo che un utente viene autenticato e riceve un nuovo token di aggiornamento, sarà in grado di utilizzare il flusso di token di aggiornamento (purché non sia stato revocato token di aggiornamento corrente e non viene utilizzato per più tempo inattivo) per il periodo di tempo specificato. A questo punto, gli utenti dovrà nuovamente l'autenticazione per ricevere un nuovo token di aggiornamento. 

Ridurre la durata massima del modo, gli utenti autenticati più spesso. Poiché autenticazione a fattore singolo viene considerato meno sicuro un'autenticazione a più fattori, è consigliabile che questo criterio è impostato su un valore uguale o minore rispetto a più fattori aggiornare Token Max età criteri.

### <a name="multi-factor-refresh-token-max-age"></a>Età max token l'aggiornamento a più fattori

**Stringa:** MaxAgeMultiFactor

**Viene applicata a:** Aggiornare i token

**Riepilogo:** Questo criterio consente il periodo di tempo un utente può continuare a usare i token di aggiornamento per ottenere accesso e aggiornare nuovo coppie token dopo l'ultima volta che tali autenticati correttamente a più fattori. Dopo che un utente viene autenticato e riceve un nuovo token di aggiornamento, sarà in grado di utilizzare il flusso di token di aggiornamento (purché non sia stato revocato token di aggiornamento corrente e non viene utilizzato per più tempo inattivo) per il periodo di tempo specificato. A questo punto, gli utenti dovrà nuovamente l'autenticazione per ricevere un nuovo token di aggiornamento. 

Ridurre la durata massima del modo, gli utenti autenticati più spesso. Poiché autenticazione a fattore singolo viene considerato meno sicuro un'autenticazione a più fattori, è consigliabile che questo criterio è impostato su un valore maggiore o uguale rispetto ai fattore singolo aggiornare Token Max età criteri.

### <a name="single-factor-session-token-max-age"></a>Età max token fattore singola sessione

**Stringa:** MaxAgeSessionSingleFactor

**Viene applicata a:** Token di sessione (permanente e non permanente)

**Riepilogo:** Questo criterio consente il periodo di tempo un utente può continuare a usare i token di sessione per ottenere nuovi token ID e la sessione dopo l'ultima volta autenticati, in modo corretto con un solo ad un fattore. Dopo che un utente viene autenticato e riceve un token di nuova sessione, sarà da utilizzare il flusso di token di sessione (purché il token di sessione corrente non è stato revocato o scaduto) per il periodo di tempo specificato. A questo punto, gli utenti dovrà nuovamente l'autenticazione per ricevere un token di nuova sessione. 

Ridurre la durata massima del modo, gli utenti autenticati più spesso. Poiché autenticazione a fattore singolo viene considerato meno sicuro un'autenticazione a più fattori, è consigliabile che questo criterio è impostato su un valore uguale o minore rispetto a più fattori sessione Token Max età criteri.

### <a name="multi-factor-session-token-max-age"></a>Età max token sessione a più fattori

**Stringa:** MaxAgeSessionMultiFactor

**Viene applicata a:** Token di sessione (permanente e non permanente)

**Riepilogo:** Questo criterio consente il periodo di tempo un utente può continuare a usare i token di sessione per ottenere nuovi token ID e la sessione dopo l'ultima volta autenticati, in modo corretto a più fattori. Dopo che un utente viene autenticato e riceve un token di nuova sessione, sarà da utilizzare il flusso di token di sessione (purché il token di sessione corrente non è stato revocato o scaduto) per il periodo di tempo specificato. A questo punto, gli utenti dovrà nuovamente l'autenticazione per ricevere un token di nuova sessione. 

Ridurre la durata massima del modo, gli utenti autenticati più spesso. Poiché autenticazione a fattore singolo viene considerato meno sicuro un'autenticazione a più fattori, è consigliabile che questo criterio è impostato su un valore maggiore o uguale rispetto ai criteri fattore singola sessione Token Max età.

## <a name="sample-token-lifetime-policies"></a>Esempi di criteri di durata

La possibilità di creare e gestire token durata per App, identità di servizio e il tenant complessivo espone tutti i tipi di nuovi scenari possibili in Azure Active Directory.  Vogliamo dettagliatamente alcuni scenari comuni di criteri che consentono di stabilire nuove regole per:


- Durata token
- Max token inattivo volte
- Età Max token

Verrà esaminato alcuni scenari tra cui:


- Gestione dei criteri predefiniti del Tenant
- Creazione di un criterio per l'accesso Web
- Creazione di un criterio per nativo App si chiama un API Web
- Gestione di un criterio Avanzate 

### <a name="prerequisites"></a>Prerequisiti
In scenari di esempio è verrà essere creazione, aggiornamento, il collegamento e l'eliminazione criteri App, identità di servizio e il tenant complessivo.  Se ha familiarità con Azure Active Directory, estrazione, [in questo articolo](active-directory-howto-tenant.md) consentono di iniziare prima di procedere con questi esempi.  


1. Per iniziare, scaricare l' ultima [Anteprima Cmdlet PowerShell di Azure Active Directory](https://www.powershellgallery.com/packages/AzureADPreview). 
2.  Dopo avere creato i cmdlet di PowerShell di Azure Active Directory, eseguire il comando Connetti per accedere all'account di amministratore di Azure Active Directory. È necessario eseguire questa operazione ogni volta che si avvia una nuova sessione.
        
        Connect-AzureAD -Confirm

3.  Eseguire il comando seguente per visualizzare tutti i criteri creati nel tenant.  Questo comando deve essere utilizzato dopo la maggior parte delle operazioni negli scenari seguenti.  Inoltre consente di ottenere l' **ID dell'oggetto** dei criteri di. 
        
        Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Esempio: Gestione dei criteri predefiniti del tenant

In questo esempio, verrà creato un criterio che consente agli utenti di accedere meno frequentemente attraverso l'intero tenant. 

A questo scopo è creare un criterio di durata per singolo fattore aggiornare token che vengano applicati al tenant. Questo criterio verrà applicato a tutte le applicazioni nel tenant e ogni servizio principale che non dispone già di un criterio impostato un. 

1.  **Creare un criterio di durata.** 

Impostare Token aggiornare fattore singolo significato "fino a revocato" che non scada fino a revocare l'accesso.  La definizione di criteri riportata di seguito è cosa che verrà creata:
        
        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Eseguire il seguente comando per creare questo criterio. 

        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
        
Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando seguente.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **aggiornare i criteri**

Si decide che il primo criterio non è abbastanza strict come il servizio richiede e aver deciso i token aggiornare il fattore singolo scadenza di 2 giorni. Eseguire il comando seguente. 
        
    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")
        
&nbsp;&nbsp;3. **fatto!** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Esempio: Creazione di un criterio per l'accesso web

In questo esempio, verrà creato un criterio che richiederà agli utenti di eseguire l'autenticazione più spesso in un'applicazione Web. Questo criterio verrà impostata la durata di token/Id di accesso e Max Age di un Token di sessione a più fattori principale del servizio dell'applicazione web.

1.  **Creare un criterio di durata.**

Questo criterio per l'accesso Web imposta la durata Token/Id di accesso e Max fattore singola sessione Token Age a 2 ore.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando seguente.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **assegnare i criteri per le principali di servizio.**

Verranno collegare il nuovo criterio con un'identità di servizio.  È necessario anche un modo per accedere **ObjectId** del servizio principale. È possibile eseguire query su [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o passare al nostro [Strumento Explorer grafico](https://graphexplorer.cloudapp.net/) e accedere all'account Azure Active Directory per visualizzare l'identità del tutti i tenant servizio. 

Dopo avere inserito **ObjectId**, eseguire il comando seguente.
        
    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3. **fatto!** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Esempio: Creazione di un criterio per nativo App si chiama un API Web

>[AZURE.NOTE]
>Il collegamento di criteri alle applicazioni è disattivato.  Stiamo lavorando su come abilitare questa breve.  Questa pagina verrà aggiornata non appena la caratteristica è disponibile.

In questo esempio abbiamo creerà un criterio che gli utenti dovranno eseguire l'autenticazione di minore e verrà allungare la quantità di tempo che può essere disattivate senza dover eseguire nuovamente l'autenticazione. Il criterio verrà applicato alle API Web, in questo modo quando l'applicazione nativa richiede come risorsa che verrà applicato questo criterio.

1.  **Creare un criterio di durata.** 

Questo comando creerà un criterio strict per un'API Web. 
        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy
         
Per visualizzare i nuovi criteri e ottenere il relativo ObjectID, eseguire il comando seguente.

    Get-AzureADPolicy

&nbsp;&nbsp;2. **assegnare i criteri per l'API Web**.

Verranno collegare il nuovo criterio con un'applicazione.  È necessario anche un modo per accedere **ObjectId** dell'applicazione. Il modo migliore per trovare dell'app **ObjectId** consiste nell'utilizzare il [Portale di Azure](https://portal.azure.com/). 

Dopo avere inserito **ObjectId**, eseguire il comando seguente.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **fatto!** 

### <a name="sample-managing-an-advanced-policy"></a>Esempio: Gestione di un criterio Avanzate 

In questo esempio, verrà creato alcuni criteri per dimostrare come funziona il sistema di priorità e come è possibile gestire più criteri applicati a diversi oggetti. Otterranno approfondire la priorità dei criteri descritto in precedenza e consente anche di gestire più complesse. 

1.  **Creazione di un criterio di durata**

Finora molto semplice. Abbiamo creato un criterio predefinito tenant che imposta la durata fattore singolo aggiornare Token a 30 giorni. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Per visualizzare i nuovi criteri e scoprire come è ObjectID, eseguire il comando seguente.
 
    Get-AzureADPolicy

&nbsp;&nbsp;2. **assegnare i criteri per un'identità di servizio**

A questo punto sono presenti un criterio all'intero tenant.  Supponiamo che si desidera mantenere il criterio di 30 giorni per un'identità di servizio specifico, ma modificare il criterio predefinito tenant per il limite massimo di "fino a revocato". 

Prima di tutto, verranno collegare il nuovo criterio con il nostro principale del servizio.  È necessario anche un modo per accedere **ObjectId** del servizio principale. È possibile eseguire query su [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) o passare al nostro [Strumento Explorer grafico](https://graphexplorer.cloudapp.net/) e accedere all'account Azure Active Directory per visualizzare l'identità del tutti i tenant servizio. 

Dopo avere inserito **ObjectId**, eseguire il comando seguente.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **impostare il flag IsTenantDefault false usando il comando seguente**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4. **creare un nuovo criterio predefinito Tenant**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5. **fatto!** 

È ora disponibile il criterio originale collegato il capitale di servizio e il nuovo criterio impostato come il criterio predefinito tenant.  È importante tenere presente che i criteri applicati alle identità di servizio hanno la priorità sui criteri predefiniti per il tenant. 


## <a name="cmdlet-reference"></a>Cmdlet di riferimento

### <a name="manage-policies"></a>Gestire i criteri
I cmdlet seguenti possono essere usati per gestire i criteri.</br></br>



#### <a name="new-azureadpolicy"></a>Nuovo AzureADPolicy
Crea un nuovo criterio.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-Definizione| Matrice di stringified JSON che contiene tutte le regole del criterio.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-DisplayName|Stringa del nome del criterio|MyTokenPolicy - DisplayName
-IsTenantDefault|Se true imposta il criterio come criterio predefinito del tenant, se è false non ha alcun effetto|-IsTenantDefault $true
-Tipo|Il tipo di criteri per la durata token sempre utilizzare "TokenLifetimePolicy"|-TokenLifetimePolicy tipo
-AlternativeIdentifier [facoltativo]|Imposta un id alternativo per il criterio.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy         
Ottiene tutti i criteri di AzureAD o i criteri specificati 
        
    Get-AzureADPolicy 

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId [facoltativo]|Id del criterio che si desidera ottenere l'oggetto. |-ObjectId &lt;ObjectID dei criteri&gt; 
</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject         
Ottiene tutte le App e identità di servizio collegata a un criterio
        
    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|Id del criterio che si desidera ottenere l'oggetto.|-ObjectId &lt;ObjectID dei criteri&gt;
</br></br>

#### <a name="set-azureadpolicy"></a>Set AzureADPolicy
Aggiorna un criterio esistente
        
    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 
 
Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|Id del criterio che si desidera ottenere l'oggetto.|-ObjectId &lt;ObjectID dei criteri&gt;
-DisplayName|Stringa del nome del criterio|MyTokenPolicy - DisplayName
-Definizione [facoltativo]|Matrice di stringified JSON che contiene tutte le regole del criterio.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-IsTenantDefault [facoltativo]|Se true imposta il criterio come criterio predefinito del tenant, se è false non ha alcun effetto|-IsTenantDefault $true
-Tipo [facoltativo]|Il tipo di criteri per la durata token sempre utilizzare "TokenLifetimePolicy"|-TokenLifetimePolicy tipo
-AlternativeIdentifier [facoltativo]|Imposta un id alternativo per il criterio.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="remove-azureadpolicy"></a>Rimuovi AzureADPolicy         
Elimina i criteri specificati

     Remove-AzureADPolicy -ObjectId <object id of policy>

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|Id del criterio che si desidera ottenere l'oggetto.|-ObjectId &lt;ObjectID dei criteri&gt;
</br></br>

### <a name="application-policies"></a>Criteri di applicazioni
Utilizzare i cmdlet seguenti per i criteri di applicazione.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Aggiungere AzureADApplicationPolicy         
I collegamenti ai criteri specificati per un'applicazione

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
-RefObjectId|L'oggetto Id dei criteri. |-RefObjectId &lt;ObjectID dei criteri&gt;
</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy        
Ottiene il criterio assegnato a un'applicazione

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Rimuovi AzureADApplicationPolicy        
Rimuove un criterio da un'applicazione

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
-PolicyId| ObjectId dei criteri.|-PolicyId &lt;ObjectID dei criteri&gt;
</br></br>

### <a name="service-principal-policies"></a>Criteri principali del servizio
Utilizzare i cmdlet seguenti per i criteri dell'entità servizio.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Aggiungere AzureADServicePrincipalPolicy         
I collegamenti ai criteri specificati per un'identità di servizio

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
-RefObjectId|L'oggetto Id dei criteri. |-RefObjectId &lt;ObjectID dei criteri&gt;
</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy        
Ottiene qualsiasi criteri collegate a capitale servizio specificato

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>
 
Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Rimuovi AzureADServicePrincipalPolicy         
Rimuove i criteri dal servizio specificato principale

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>
 
Parametri|Descrizione|Esempio|
-----| ----- |-----|
-ObjectId|L'oggetto Id dell'applicazione.|-ObjectId &lt;ObjectID dell'applicazione&gt; 
-PolicyId| ObjectId dei criteri.|-PolicyId &lt;ObjectID dei criteri&gt;
