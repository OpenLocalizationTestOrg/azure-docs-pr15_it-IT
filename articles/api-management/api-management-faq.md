<properties
    pageTitle="API Azure gestione domande frequenti su | Microsoft Azure"
    description="Risposte alle domande comuni, modelli e procedure consigliate nella gestione dei API di Azure."
    services="api-management"
    documentationCenter=""
    authors="miaojiang"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="mijiang"/>

# <a name="azure-api-management-faqs"></a>Domande frequenti su gestione API Azure

Risposte alle domande comuni, modelli e procedure consigliate per la gestione di API di Azure.

## <a name="frequently-asked-questions"></a>Domande frequenti

-   [Come è possibile fare il team di Microsoft Azure API Gestione domande?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)
-   [Che cosa significa quando una caratteristica si trova nell'anteprima?](#what-does-it-mean-when-a-feature-is-in-preview)
-   [Come è possibile proteggere la connessione tra il gateway di gestione dell'API e i servizi di back-end?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
-   [Come copiare l'istanza del servizio di gestione delle API a una nuova istanza?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
-   [È possibile gestire le istanza di gestione delle API a livello di programmazione?](#can-i-manage-my-api-management-instance-programmatically)
-   [Come aggiunge un utente al gruppo Administrators?](#how-do-i-add-a-user-to-the-administrators-group)
-   [Perché è il criterio che si desidera aggiungere non è disponibile nell'editor Criteri?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
-   [Utilizzo di controllo delle versioni API in Gestione API](#how-do-i-use-api-versioning-in-api-management)
-   [Configurazione più ambienti in un'unica API](#how-do-i-set-up-multiple-environments-in-a-single-api)
-   [È possibile utilizzare SOAP con API Management?](#can-i-use-soap-with-api-management)
-   [È la costante di indirizzi IP di gateway API Gestione? È possibile usarli regole firewall.](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
-   [È possibile configurare un server di autorizzazione OAuth 2.0 con sicurezza ADFS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
-   [Il metodo di routing utilizza API gestione nelle distribuzioni a più aree geografiche?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
-   [È possibile usare un modello di gestione di risorse Azure per creare un'istanza di servizio di gestione delle API?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
-   [È possibile usare un certificato SSL autofirmato per un back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
-   [Perché si riceve un errore di autenticazione quando si tenta di duplicare un repository fra?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
-   [Gestione dell'API funziona con Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
-   [È possibile spostare un servizio di gestione delle API da una sottoscrizione a un altro?](#can-i-move-an-api-management-service-from-one-subscription-to-another)


### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Come è possibile fare il team di Microsoft Azure API Gestione domande?

È possibile contattarci tramite una di queste opzioni:

-   Pubblicare domande al [forum MSDN di gestione dell'API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
-   Inviare un messaggio di posta elettronica a <apimgmt@microsoft.com>.
-   Inviare una richiesta di funzionalità [Azure area feedback](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Che cosa significa quando una caratteristica si trova nell'anteprima?

Quando una caratteristica si trova nell'anteprima, significa che è stiamo attivamente alla ricerca di commenti e suggerimenti su come la funzione effettui il dell'utente. Una caratteristica di anteprima è completa, ma è possibile che è necessario modificare un'interruzione di risposta ai commenti. È consigliabile che non dipendono una caratteristica in anteprima nel proprio ambiente di produzione. Se si dispone di eventuali commenti e suggerimenti sulle funzionalità di anteprima, indicare tramite una delle opzioni di contatto in [come è pubblicare il team di Microsoft Azure API Gestione una domanda?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Come è possibile proteggere la connessione tra il gateway di gestione dell'API e i servizi di back-end?

Sono disponibili diverse opzioni per proteggere la connessione tra il gateway di gestione dell'API e i servizi di back-end. Si può:

-   Utilizzare l'autenticazione di base HTTP. Per ulteriori informazioni, vedere [configurare API impostazioni](api-management-howto-create-apis.md#configure-api-settings).
- Utilizzare l'autenticazione reciproca SSL come descritto in [come proteggere i servizi di back-end tramite autenticazione certificato client in Azure API Management](api-management-howto-mutual-certificates.md).
- Utilizzare whitelist IP del servizio di back-end. Se si dispone di un'istanza di gestione delle API di livello Standard o Premium, l'indirizzo IP del gateway rimane costante. È possibile impostare la proprietà consentite per consentire l'indirizzo IP. È possibile ottenere l'indirizzo IP dell'istanza di gestione delle API nel Dashboard nel portale di Azure.
- Connettere l'istanza di gestione API a una rete virtuale Azure. Per ulteriori informazioni, vedere [come configurare le connessioni VPN in Azure API Management](api-management-howto-setup-vpn.md).

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Come copiare l'istanza del servizio di gestione delle API a una nuova istanza?

Se si desidera copiare un'istanza di gestione delle API in una nuova istanza sono disponibili diverse opzioni. Si può:

-   Utilizzare il backup e ripristino funzione API gestione. Per ulteriori informazioni, vedere [come implementare mediante il backup di servizi di emergenza e ripristinare in Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
-   Creare una copia di backup e ripristino caratteristica tramite l' [API REST di gestione dell'API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Utilizzare l'API REST per salvare e ripristinare l'entità dall'istanza del servizio che si desidera.
-   Scaricare la configurazione del servizio mediante fra e quindi caricarlo in una nuova istanza. Per ulteriori informazioni, vedere [come salvare e configurare la configurazione del servizio di gestione delle API utilizzando fra](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>È possibile gestire le istanza di gestione delle API a livello di programmazione?

Sì, è possibile gestire gestione API a livello di programmazione tramite:

-   [Gestione dell'API API REST](https://msdn.microsoft.com/library/azure/dn776326.aspx).
-   La [raccolta di gestione ApiManagement servizio di Microsoft Azure SDK](http://aka.ms/apimsdk).
-   Cmdlet per [la distribuzione del servizio](https://msdn.microsoft.com/library/mt619282.aspx) e [gestione dei servizi](https://msdn.microsoft.com/library/mt613507.aspx) PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Come aggiunge un utente al gruppo Administrators?

Ecco come è possibile aggiungere un utente al gruppo Administrators:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al gruppo di risorse contenente l'istanza di gestione delle API che si desidera aggiornare.
3. Gestione delle API, assegnare il ruolo di **Collaboratore gestione Api** all'utente.

A questo punto collaboratori appena aggiunto possono utilizzare PowerShell Azure [cmdlet](https://msdn.microsoft.com/library/mt613507.aspx). Ecco come effettuare l'accesso come amministratore:

1. Utilizzare la `Login-AzureRmAccount` cmdlet per l'accesso.
2. Impostare il contesto per l'abbonamento che contiene il servizio usando `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Ottenere un URL single sign-on utilizzando `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Utilizzare l'URL per accedere al portale di amministrazione.


### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Perché è il criterio che si desidera aggiungere non è disponibile nell'editor Criteri?

Se il criterio che si desidera aggiungere viene visualizzato in grigio o Ombreggiato nell'editor criteri, assicurarsi di essere nell'ambito corretto per il criterio. Ogni informativa è progettato per l'uso in ambiti specifici e sezioni dei criteri. Per esaminare le sezioni dei criteri e gli ambiti per un criterio, nella sezione dei criteri per l'uso in [criteri di gestione delle API](https://msdn.microsoft.com/library/azure/dn894080.aspx).


### <a name="how-do-i-use-api-versioning-in-api-management"></a>Utilizzo di controllo delle versioni API in Gestione API

Si dispone di alcune opzioni per utilizzare il controllo delle versioni API in Gestione API:

-   Gestione delle API, è possibile configurare API per rappresentare le diverse versioni. Ad esempio, potrebbe essere due API diverse, MyAPIv1 e MyAPIv2. Uno sviluppatore può scegliere la versione che si desidera utilizzare.
-   È anche possibile configurare l'API con un URL del servizio che non include un segmento di versione, ad esempio https://my.api. Nel modello [Di riscrittura URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) di ogni operazione, configurare un segmento di versione. Ad esempio, è un'operazione con un [modello di URL](api-management-howto-add-operations.md#url-template) denominata /resource e un modello di [Riscrittura URL](api-management-howto-add-operations.md#rewrite-url-template) denominato /v1/Resource. È possibile modificare il valore di segmento versione separatamente per ogni operazione.
-   Se si preferisce mantenere un segmento di versione "predefinito" nell'URL del servizio dell'API, sull'attività selezionata, impostare un criterio che utilizza il [gruppo servizio back-end](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) criteri per modificare il percorso della richiesta di back-end.

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Configurazione più ambienti in un'unica API

Per configurare più ambienti, ad esempio, un ambiente di testing e un ambiente di produzione in un'unica API, sono disponibili due opzioni. Si può:

-   Host diverse API su stesso tenant.
-   Ospitare le stesse API nel tenant diverso.

### <a name="can-i-use-soap-with-api-management"></a>È possibile utilizzare SOAP con API Management?

È ora disponibile il supporto di [pass-through SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) . Gli amministratori possono importare WSDL del proprio servizio SOAP e Azure API Management creerà un front-end SOAP. Documentazione del portale per sviluppatori, console di prova, criteri e analitica è tutti disponibili per i servizi SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>È la costante di indirizzi IP di gateway API Gestione? È possibile usarli regole firewall.

Livelli Standard e Premium l'indirizzo IP pubblico (VIP) del tenant di gestione delle API è statico per la durata del tenant, con alcune eccezioni. Modifiche all'indirizzo IP nei seguenti casi:

-   Il servizio viene eliminato e quindi ricreare.
-   L'abbonamento a servizi è sospesa (ad esempio, per nonpayment) e quindi reimpostato.
-   Aggiungere o rimuovere virtuali Azure (è possibile utilizzare virtuali solo al livello Premium).

Per distribuzioni più aree, l'indirizzo internazionali cambia se l'area geografica è liberato e quindi reimpostato (è possibile utilizzare più aree distribuzione solo al livello Premium).

Tenant di livello Premium configurati per la distribuzione di più paesi viene assegnato un indirizzo IP pubblico per l'area geografica.

Nella pagina tenant nel portale di Azure, è possibile ottenere l'indirizzo IP (o gli indirizzi, in una distribuzione a più aree).

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>È possibile configurare un server di autorizzazione OAuth 2.0 con sicurezza ADFS?

Per informazioni su come configurare un server di autorizzazione OAuth 2.0 con protezione di Active Directory Federation Services (ADFS), vedere [Con ADFS in Gestione API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Il metodo di routing utilizza API gestione nelle distribuzioni a più aree geografiche?

Gestione dell'API utilizzato il [metodo di routing il traffico di prestazioni](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method) nelle distribuzioni a più aree geografiche. Il traffico in ingresso viene indirizzato al gateway API più vicino. Se una regione in linea, il traffico in ingresso viene automaticamente indirizzato al gateway più vicino successivo. Ulteriori informazioni sui metodi di routing in [gestore del traffico routing metodi](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>È possibile usare un modello di gestione di risorse Azure per creare un'istanza di servizio di gestione delle API?

Sì. Visualizzare i modelli di Guida introduttiva di [Servizio di gestione dell'API di Azure](http://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>È possibile usare un certificato SSL autofirmato per un back-end?

Sì. Ecco come usare un certificato autofirmato Secure Sockets Layer (SSL) per un back-end:

1. Creare un'entità [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) tramite Gestione API.
2. Impostare la proprietà **skipCertificateChainValidation** su **true**.
3. Se non si desidera consentire certificati autofirmati, eliminare l'entità di back-end o impostare la proprietà **skipCertificateChainValidation** su **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Perché si riceve un errore di autenticazione quando si tenta di duplicare un repository fra?

Se si utilizza Gestione credenziali fra o se si sta tentando di duplicare un repository fra utilizzando Visual Studio, che potrebbe verificarsi un problema noto con la finestra di dialogo credenziali Windows. Nella finestra di dialogo limiti di lunghezza della password a 127 caratteri e viene troncata password generati da Microsoft. Stiamo lavorando a ridurre la password. Per ora, utilizzare fra Bash da duplicare l'archivio di operazioni.

### <a name="does-api-management-work-with-azure-expressroute"></a>Gestione dell'API funziona con Azure ExpressRoute?

Sì. Gestione dell'API funziona con Azure ExpressRoute.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>È possibile spostare un servizio di gestione delle API da una sottoscrizione a un altro?

Sì. Per ulteriori informazioni, vedere [spostare le risorse per il nuovo gruppo di risorse o l'abbonamento](../resource-group-move-resources.md).
