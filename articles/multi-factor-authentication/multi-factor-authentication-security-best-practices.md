<properties 
    pageTitle="Le procedure consigliate per l'uso di Azure MFA"
    description="Questo documento vengono fornite procedure consigliate sull'utilizzo di Azure MFA con un account di Azure"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Le procedure consigliate per l'uso di autenticazione a più fattori Azure con un account di Azure Active Directory

Quando si tratta di ottimizzazione del processo di autenticazione, autenticazione a più fattori è la scelta migliore per la maggior parte delle organizzazioni. Autenticazione a più fattori Azure (MFA) consente alle aziende di soddisfare i requisiti di sicurezza e conformità garantendo un'esperienza di accesso semplice per gli utenti. In questo articolo vengono descritte le procedure consigliate che è necessario considerare durante la pianificazione per l'adozione di Azure MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Procedure consigliate per l'autenticazione a più fattori Azure nel cloud
Per specificare tutti gli utenti con l'autenticazione a più fattori e possono avvalersi delle funzionalità estese che offre l'autenticazione a più fattori di Azure, sarà necessario abilitare l'autenticazione a più fattori Azure su tutti gli utenti.  Questa operazione viene eseguita mediante una delle operazioni seguenti:

- Azure Active Directory Premium o la famiglia di mobilità Enterprise
- Provider di autenticazione a più fattori

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure Active Directory Premium/Enterprise mobilità Suite

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Il primo passaggio consigliato per adottare MFA Azure nel cloud con Azure Active Directory Premium o Enterprise mobilità Suite è assegnare licenze agli utenti.  Autenticazione a più fattori Azure fa parte di queste famiglie di prodotti e come tali l'organizzazione non richiede alcuna aggiuntive per estendere la funzionalità di autenticazione a più fattori a tutti gli utenti.

Quando è consigliabile impostazione autenticazione a più fattori seguenti:

- Non è necessario creare un Provider di autenticazione a più fattori.  Azure Active Directory Premium ed Enterprise mobilità Suite viene fornito con l'autenticazione a più fattori Azure.  Se si crea un Provider di autenticazione è possibile ottenere double fatturate.
- Azure AD Connect è solo un requisito se si sta sincronizzando l'ambiente di Active Directory locale con una directory di Azure Active Directory. Se si utilizza solo una directory di Azure Active Directory che non è sincronizzata con un'istanza locale di Active Directory, non è necessaria Azure AD Connect.


### <a name="multi-factor-auth-provider"></a>Provider di autenticazione a più fattori

![Provider di autenticazione a più fattori](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se non si dispone Azure Active Directory Premium o Enterprise mobilità Suite il primo passaggio consigliato per adozione MFA Azure nel cloud consiste nel creare un Provider di autenticazione MFA. Anche se è disponibile per impostazione predefinita per gli amministratori globali che dispongono di Azure Active Directory, quando si distribuisce MFA per l'organizzazione che è necessario per estendere la funzionalità di autenticazione a più fattori di tutti gli utenti che è necessario un Provider di autenticazione a più fattori MFA.
Quando si seleziona il Provider di autenticazione, è necessario selezionare una directory e tenere presente quanto segue:

- Non è necessaria una directory di Azure Active Directory per creare un Provider di autenticazione a più fattori.
- È necessario associare il Provider di autenticazione a più fattori una directory di Azure Active Directory se si desidera estendere l'autenticazione a più fattori per tutti gli utenti e/o desidera che gli amministratori globali per poter usufruire delle caratteristiche, ad esempio il portale di gestione, messaggio di saluto personalizzato e report.
- DirSync o AAD Sync sono solo un requisito se si sta sincronizzando l'ambiente di Active Directory locale con una directory di Azure Active Directory. Se si utilizza solo una directory di Azure Active Directory che non è sincronizzata con un'istanza locale di Active Directory, non è necessaria DirSync o AAD Sync.
- Se si dispone di Azure Active Directory Premium o Enterprise mobilità Suite, non è necessario creare un provider di autenticazione a più fattori. È necessario assegnare una licenza per un utente e quindi è possibile iniziare attivare MFA per gli utenti.
- Assicurarsi di selezionare il modello di utilizzo appropriata per l'azienda (per auth o per l'utente abilitato), dopo aver selezionato il modello di utilizzo non è possibile modificarla.

### <a name="user-account"></a>Account utente
Quando si abilita MFA per gli utenti, gli account utente possono essere in uno dei tre stati di base: disabilitato, attivate o stabilite.
Attenersi alle linee guida seguenti per assicurarsi che si usa l'opzione più appropriata per la distribuzione:

- Quando lo stato dell'utente è impostato su disattivato, tale utente non utilizza l'autenticazione a più fattori. Questo stato predefinito.
- Quando lo stato dell'utente è impostato su abilitato, significa che l'utente è abilitata ma non è stata completata la procedura di registrazione. Verrà richiesto di completare il processo per l'accesso successivo. Questa impostazione non influisce su browser non app. Tutte le app continueranno a funzionare fino al completamento del processo di registrazione.
- Quando lo stato dell'utente è impostato su imposto, significa che l'utente può o non ha effettuato la registrazione. Se è stata completata la procedura di registrazione in uso l'autenticazione a più fattori. In caso contrario, l'utente verrà richiesto per completare il processo di registrazione al successivo accesso. Questa impostazione influisce sui browser non app. Queste applicazioni non funzioneranno fino a quando non vengono creati e utilizzati password dell'app.
- Utilizzare il modello di notifica utente disponibili nell'articolo della [Guida introduttiva a autenticazione a più fattori Azure nel cloud](multi-factor-authentication-get-started-cloud.md) per inviare un messaggio di posta elettronica agli utenti relativa adozione MFA.

### <a name="supportability"></a>Supporto

Poiché la maggior parte degli utenti sono abituati all'uso delle password solo per l'autenticazione, è importante che la propria azienda di portare la conoscenza a tutti gli utenti su questo processo. Questa conoscenza riduce la probabilità che gli utenti verranno chiamare il supporto tecnico per problemi correlati a MFA.
Tuttavia, esistono alcuni scenari in cui è necessario disattivare temporaneamente MFA. Utilizzare le linee guida seguenti per imparare a gestire questi scenari:

- Verificare che il personale di supporto tecnico qualificato quadratino scenari in cui un telefono o app per dispositivi mobili non riceve una notifica o una chiamata telefonica e per questo motivo l'utente non è in grado di accedere. È possibile abilitare un'opzione di esclusione monouso consentire all'utente di eseguire l'autenticazione una sola volta "ignorando" autenticazione a più fattori. Il bypass è temporaneo e scade dopo il numero di secondi specificato.
- Se necessario, è possibile sfruttare le funzionalità IP attendibili in Azure MFA. Questa caratteristica consente agli amministratori di un tenant gestito o federato la possibilità di ignorare l'autenticazione a più fattori per gli utenti di accedere da intranet locale della società. Le caratteristiche sono disponibili per i tenant di Azure Active Directory che dispongono delle licenze di Azure Active Directory Premium, Enterprise mobilità Suite o autenticazione a più fattori Azure.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Procedure consigliate per l'autenticazione a più fattori Azure locale
Se la propria azienda ha deciso di propria infrastruttura per abilitare MFA, sarà necessario distribuire un Server di autenticazione a più fattori Azure locale. I componenti MFA Server vengono visualizzati nella figura seguente:

![Provider di autenticazione a più fattori](./media/multi-factor-authentication-security-best-practices/server.png)
*non è installato per impostazione predefinita ** installato ma non attivata per impostazione predefinita


Azure Server di autenticazione a più fattori può essere utilizzato per proteggere le risorse cloud e locali accessibili dagli account Azure Active Directory.  È tuttavia possibile solo utilizzando la federazione.  Che è necessario ADFS e di avere è federato con il tenant di Azure Active Directory.
Quando è consigliabile configurazione Server di autenticazione a più fattori seguenti:

- Se ci si trova protezione di Azure Active Directory risorse con Active Directory Federation Services viene eseguito il 1 ° fattore di autenticazione locale tramite ADFS e il fattore di 2 ° verrà eseguite in locale per rispettare la richiesta.
- Non è un requisito necessario installare il Server di autenticazione a più fattori Azure nel server federativo ADFS, ma la scheda di autenticazione a più fattori per AD FS deve essere installata in un Windows Server 2012 R2 esecuzione ADFS. È possibile installare il server in un altro computer, purché si tratta di una versione supportata e installare la scheda ADFS separatamente nel server federativo ADFS. Vedere la procedura riportata di seguito per istruzioni sull'installazione scheda separatamente.
- Autenticazione a più fattori AD FS scheda Installazione guidata crea un gruppo di sicurezza denominato PhoneFactor Admins di Active Directory e quindi aggiunge l'account di servizio ADFS del servizio federativo a questo gruppo. È consigliabile verificare sul controller di dominio che il gruppo PhoneFactor Admins viene effettivamente creato e che AD FS account del servizio è un membro del gruppo. Se necessario, aggiungere l'account di servizio ADFS manualmente al gruppo PhoneFactor Admins sul controller di dominio.

### <a name="user-portal"></a>Portale utente
Il portale viene eseguito in un sito web di Internet Information Server (IIS), che consente di Self-Service e fornisce un set completo di funzionalità di amministrazione utente. Per configurare questo componente, utilizzare le linee guida seguenti:

- È necessario IIS 6 o versione successiva
- ASP.NET v2.0.507207 è necessario installare e registrare
- In una rete perimetrale, è possibile distribuire questo server.



### <a name="app-passwords"></a>Password dell'App
Se l'organizzazione è federato con Single Sign-on Azure Active Directory e si intende utilizzare MFA Azure, quindi tenere presente quanto segue quando si utilizzano password app (ricordare che si applica solo a federato (SSO) viene utilizzato):

- La Password di App verificata da Azure Active Directory e pertanto Ignora federazione. Federazione viene utilizzata solo quando si imposta Password per l'App.
- Per federato (SSO) le password degli utenti verranno archiviate nel campo id dell'organizzazione. Se l'utente lascia l'azienda, flusso per id dell'organizzazione tramite DirSync in tempo reale quelle informazioni. Disabilita/eliminazione dell'account potrebbe richiedere fino a 3 ore per sincronizzare, ritardare disabilita / l'eliminazione della Password per l'App in Azure Active Directory.
- Impostazioni di controllo dell'accesso Client locale non vengono rispettate da App Password
- L'autenticazione non locale registrazione / funzionalità di controllo è disponibile per la Password di App
- Ulteriori per l'istruzione degli utenti finali è necessario per il client di Microsoft Lync 2013.
- Alcuni aspetti architettonica avanzate possono richiedere utilizzando una combinazione di nome utente dell'organizzazione e app password quando si usa l'autenticazione a più fattori con client, a seconda di dove l'autenticazione. Per i client autenticare un'infrastruttura locale, utilizzare un nome utente dell'organizzazione e la password. Per i client autenticare Azure Active Directory, utilizzare la password di app.
- Per impostazione predefinita, gli utenti non è possibile creare password dell'app, se è necessario che o se è necessario consentire agli utenti di creare password per l'app in alcuni casi, assicurarsi che sia selezionata l'opzione Consenti agli utenti di creare password dell'app per accedere a applicazioni non browser.

## <a name="additional-considerations"></a>Altre considerazioni
Utilizzare l'elenco seguente per comprendere alcune considerazioni e procedure consigliate per ogni componente che verrà distribuito in locale:

Metodo|Descrizione
:------------- | :------------- |
[Active Directory Federation Services](multi-factor-authentication-get-started-adfs.md)|Informazioni sulle impostazioni di autenticazione a più fattori Azure con ADFS.
[RAGGIO Authenticaton](multi-factor-authentication-get-started-server-radius.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con raggio.
[Autenticazione IIS](multi-factor-authentication-get-started-server-iis.md)|Informazioni sull'installazione e configurazione del Server di MFA Azure con IIS.
[Authenticaton di Windows](multi-factor-authentication-get-started-server-windows.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con l'autenticazione di Windows.
[Autenticazione LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informazioni sull'installazione e configurazione del Server di MFA Azure con autenticazione LDAP.
[Remote Desktop Gateway e Server di autenticazione a più fattori Azure utilizzando raggio](multi-factor-authentication-get-started-server-rdg.md)|  Informazioni sull'installazione e configurazione del Server di MFA Azure con Gateway Desktop remoto utilizzando RADIUS.
[Eseguire la sincronizzazione con Active Directory di Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informazioni sull'installazione e configurazione della sincronizzazione tra Active Directory e il Server di MFA Azure.
[La distribuzione del servizio di autenticazione a più fattori Azure Server Web App per dispositivi mobili](multi-factor-authentication-get-started-server-webservice.md)|Informazioni sull'installazione e configurazione del servizio web di server Azure MFA.
[Configurazione avanzata VPN con Azure autenticazione a più fattori](multi-factor-authentication-advanced-vpn-configurations.md)|Informazioni sulla configurazione di dispositivi ASA Cisco, Citrix Netscaler e Juniper/Pulse sicura VPN tramite LDAP o RADIUS.


## <a name="additional-resources"></a>Risorse aggiuntive
Mentre in questo articolo evidenzia le procedure consigliate per MFA Azure, sono disponibili altre risorse che è possibile anche utilizzare durante la pianificazione della distribuzione MFA. Nell'elenco seguente include alcuni articoli chiavi che possono risultare utile durante il processo:

- [Report in Azure autenticazione a più fattori](multi-factor-authentication-manage-reports.md)
- [Esperienza di configurazione per l'autenticazione a più fattori di Azure](multi-factor-authentication-end-user-first-time.md)
- [Domande frequenti su Azure autenticazione a più fattori](multi-factor-authentication-faq.md)
