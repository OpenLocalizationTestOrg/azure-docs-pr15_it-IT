<properties
    pageTitle="Azure AD Connect: Utente accedere | Microsoft Azure"
    description="Azure AD Connect accesso dell'utente per le impostazioni personalizzate."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure Active Directory la connessione di accesso dell'utente opzioni

Azure AD Connect consente agli utenti di accedere al cloud e locale risorse usando le stesse password. In questo articolo vengono illustrati i concetti fondamentali per ogni modello di identità consente di selezionare l'identità che si desidera utilizzare per l'accesso a Azure Active Directory.

Se si ha già familiarità con il modello di identità di Azure Active Directory e si desidera acquisire familiarità con un metodo specifico, fare clic di sotto sul argomento appropriato.

* [Sincronizzazione delle password](#password-synchronization)
* [Single Sign-on federato (con ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Scelta di un metodo di accesso utente
Per la maggior parte delle organizzazioni che si vuole semplicemente per consentire utente effettua l'accesso a Office 365, applicazioni SaaS e altri Azure AD in base a risorse, è consigliabile l'opzione di sincronizzazione delle Password predefinita.
In alcune organizzazioni, tuttavia, avere particolari motivi per l'uso di un segno federato opzione, ad esempio ADFS.  Sono inclusi:

- L'organizzazione ha già ADFS o un 3o provider federazione distribuito di terze parti
- Criteri di sicurezza impedisce la sincronizzazione hash delle password nel cloud
- È necessario che gli utenti riscontrare perfetta Single Sign-On (senza password aggiuntiva viene visualizzato un prompt) quando accesso alle risorse cloud da dominio aggiunto computer della rete aziendale
- Si richiede alcune funzionalità specifiche sono previsti ADFS
    - Autenticazione a più fattori locale con un provider di terze parti o sulle smart card (per informazioni sui provider di terze parti MFA per AD FS in Windows Server 2012 R2)
    - Funzionalità di integrazione di Active Directory, ad esempio il blocco degli account contorni o criteri di ore di lavoro e password Active Directory
    - Condizionale accesso per locali e cloud risorse usando registrazione periferica, join di Azure Active Directory o criteri di MDM Intune

### <a name="password-synchronization"></a>Sincronizzazione delle password
Con la sincronizzazione delle password, hash delle password degli utenti sincronizzati da Active Directory locale di Azure Active Directory.  Quando la password vengono modificata o reimpostare in locale, le nuove password vengono sincronizzate immediatamente con Azure Active Directory in modo che gli utenti possono sempre utilizzare la stessa password per le risorse cloud uguali locale.  Le password non sono mai inviate a Azure Active Directory né archiviate in Azure Active Directory in testo non crittografato.
La sincronizzazione delle password è possibile usare insieme password writeback abilitare self la reimpostazione della password di servizio in Azure Active Directory.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Ulteriori informazioni sulla sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Federazione con un ADFS nuovo o esistente nella farm di Windows Server 2012 R2
Con federati sign-on, i utenti possano accedere ai servizi di Azure Active Directory in base a con la propria password locale e, nella rete aziendale, senza dover immettere nuovamente la password.  La federazione con ADFS consente di distribuire un nuovo o specificare un ADFS esistente in Windows Server 2012 R2 farm.  Se si sceglie di specificare una farm esistente, Azure AD Connect configurerà il trust tra le farm e Azure Active Directory, in modo che gli utenti possano accedere.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Per distribuire la federazione con ADFS in Windows Server 2012 R2, è necessario quanto segue

Se si distribuisce una nuova farm:

- Server di Windows Server 2012 R2 per il server federativo.
- Server di Windows Server 2012 R2 per il Proxy di applicazione Web.
- file pfx con un certificato SSL per il nome di servizio federazione previsto, ad esempio fs.contoso.com.

Se si desidera distribuire una nuova farm o usando una farm esistente:

- Credenziali di amministratore locale nei server federativi.
- Credenziali di amministratore locale in un gruppo di lavoro (non di dominio) Server in cui si desidera distribuire il ruolo di Proxy di applicazione Web.
- Il computer in cui si esegue la procedura guidata deve essere in grado di connettersi a tutti gli altri computer in cui si vuole installare ADFS o Proxy di applicazione Web tramite Gestione remota Windows.

[Configurazione di Single Sign-On con ADFS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Accedere tramite una versione precedente di ADFS o una terza parte soluzione

Se è già stato configurato accesso cloud tramite una versione precedente di ADFS (ad esempio ADFS) 2.0 o il provider di una federazione di terze parti, è possibile scegliere di ignorare accesso dell'utente configurazione tramite Azure AD Connect.  In questo modo sarà che consente di ottenere l'ultima sincronizzazione e altre funzionalità di Azure AD Connect mentre si utilizza la soluzione esistente per accedere in.

[Elenco di compatibilità Azure Active Directory federation di terze parti](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Accesso degli utenti e il nome dell'entità utente (UPN)

### <a name="understanding-user-principal-name"></a>Nome dell'entità utente Understanding

In Active Directory, il suffisso UPN predefinito è il nome DNS del dominio in cui creare account utente. Nella maggior parte dei casi, questo è il nome di dominio registrato come dominio dell'organizzazione su Internet. Tuttavia, è possibile aggiungere ulteriori suffissi UPN mediante e trust di Active Directory Domains.

L'UPN dell'utente è il formato username@domai. Per un server active directory, ad esempio, contoso.com utente John potrebbe essere UPN john@contoso.com. L'UPN dell'utente si basa su RFC822. Anche se UPN e la posta elettronica condividono lo stesso formato, il valore di UPN per un utente può o non può essere uguale a indirizzo di posta elettronica dell'utente.

### <a name="user-principal-name-in-azure-ad"></a>Nome dell'entità utente in Active Directory Azure

Azure AD Connect guidata verrà utilizzare l'attributo userPrincipalName o consentono di specificare l'attributo (nell'installazione personalizzata) per essere utilizzato dal locale come il nome dell'entità utente in Active Directory Azure. Si tratta del valore che verrà utilizzato per l'accesso a Azure Active Directory. Se il valore dell'attributo del nome dell'entità utente non corrisponde a un dominio verificato in Azure Active Directory, Azure Active Directory verrà sostituirlo con un valore predefinito. onmicrosoft.com valore.

Ogni directory di Azure Active Directory viene fornito con un nome di dominio predefinito in contoso.onmicrosoft.com la maschera che consente di iniziare a utilizzare Azure o altri servizi Microsoft. È possibile migliorare e semplificare l'esperienza di accesso utilizzando domini personalizzati. Per informazioni sul dominio personalizzato i nomi di Azure Active Directory e su come verificare il dominio, leggere [aggiungere il nome di dominio personalizzato per Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure configurazione di Active Directory

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure Active Directory accesso configurazione con Azure AD Connect
Azure esperienza di Active Directory è dipendente che sia possibile in modo che corrispondano il suffisso del nome dell'entità utente di un utente che viene sincronizzato con uno dei domini personalizzati verificati nella directory di Azure Active Directory di Azure Active Directory. Azure AD Connect viene fornita assistenza durante la configurazione delle impostazioni di Azure Active Directory, accesso in modo che accesso esperienza utente nel cloud è simile all'ambiente locale. Azure AD Connect sono elencati i suffissi UPN definiti per i domini e tenta di far corrispondere con un dominio personalizzato in Azure Active Directory e consente di con l'azione appropriata che saranno necessarie.
La pagina di accesso di Azure Active Directory Elenca gli suffissi UPN definiti per il servizio Active Directory locale e viene visualizzato lo stato su ogni suffisso corrispondente. Possono essere uno dei valori di stato di seguito:

| Stato | Descrizione | Sono necessari interventi |
|:----|:----|:----|
|Verificato| Azure AD Connect trovare che una corrispondenza di dominio verificato in Azure Active Directory. Tutti gli utenti per questo dominio possono accesso aggiuntivo seguendo le proprie credenziali locale| È richiesta alcuna azione |
|Non verificati| Azure AD Connect Impossibile trovare un dominio personalizzato corrispondente in Azure Active Directory, ma non è verificato. Suffisso UPN degli utenti del dominio verrà modificato per impostazione predefinita. onmicrosoft.com suffisso dopo la sincronizzazione se dominio non è stata verificata. | Verificare il dominio personalizzato in Azure Active Directory. [Ulteriori informazioni](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Non è stato aggiunto| Azure AD Connect non è riuscito a trovare un dominio personalizzato corrispondente al suffisso UPN. Suffisso UPN degli utenti da questo dominio verrà modificato per impostazione predefinita. onmicrosoft.com se non è stato aggiunto il dominio e verifeid in Azure.| Aggiunta e la verifica di un dominio personalizzato corrispondente al suffisso UPN [Approfondimenti](./active-directory-add-domain.md)|

Pagina di accesso AD Azure Elenca suffix(s) UPN definite per il servizio Active Directory locale e il dominio personalizzato corrispondente Azure Active Directory con lo stato di verifica corrente. Nell'installazione personalizzata, è ora possibile selezionare l'attributo per nome dell'entità utente la pagina di **accesso Azure Active Directory** .

![Pagina di accesso AD Azure](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

È possibile fare clic sul pulsante Aggiorna per recuperare nuovamente lo stato più recente di domini personalizzati da Azure Active Directory.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Selezione attributo per nome dell'entità utente in Active Directory Azure

UserPrincipalName - userPrincipalName attributo è l'attributo che verrà utilizzato dagli utenti quando vengono accesso aggiuntivo per Azure Active Directory e in Office 365. I domini usati, noto anche come-suffisso UPN, devono essere verificati in Azure Active Directory prima che gli utenti sono sincronizzati. Si consiglia di mantenere predefinito attributo userPrincipalName. Se l'attributo è non routing e non può essere verificato quindi è possibile selezionare un altro attributo, ad esempio posta elettronica, come l'attributo che l'ID di accesso. Tale valore è noto come ID alternativo. Il valore dell'attributo ID alternativo deve seguire lo standard RFC822. Un ID alternativo può essere utilizzato con password Single Sign-On (SSO) e la federazione Single Sign-on della soluzione di accesso.

>[AZURE.NOTE] Con un ID alternativo non è compatibile con tutti i carichi di Office 365. Per ulteriori informazioni, vedere [Configurazione ID di accesso alternativo](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Stati di dominio personalizzato diverso ed effetto sull'esperienza di accesso Azure
È importante conoscere la relazione tra gli stati di dominio personalizzato nella directory di Azure Active Directory e suffissi UPN definita locale. Automatica verranno illustrate le possibili Azure accesso esperienze diverse quando si configurano sycnhronization con Azure Active Directory connessione.

Per le informazioni seguenti, si supponga che sono interessati con il contoso.com suffisso UPN utilizzata nella directory locale come parte di UPN, ad esempio user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Impostazioni rapide / la sincronizzazione delle Password
| Stato         | Effetto sull'esperienza utente accessohttp Azure |
|:-------------:|:----------------------------------------|
| Non è stato aggiunto | In questo caso non dominio personalizzato per contoso.com è stato aggiunto nella directory di Azure Active Directory. Utenti che hanno UPN locale con suffisso @contoso.com, non saranno in grado di utilizzare il relativo UPN locale di accesso a Azure. Se, tuttavia hanno utilizzare un nuovo UPN fornito da Azure Active Directory aggiungendo il suffisso per directory di Azure Active Directory predefinito. Ad esempio, se si siano eseguendo la sincronizzazione agli utenti di Azure Active directory azurecontoso.onmicrosoft.com quindi l'utente locale user@contoso.com sarà assegnato un UPN diuser@azurecontoso.onmicrosoft.com|
| Non verificati | In questo caso abbiamo contoso.com un dominio personalizzato aggiunto nella directory di Azure Active Directory, ma non è ancora verificato. Se procedere con la sincronizzazione di utenti senza verificare il dominio, quindi gli utenti riceveranno un nuovo UPN da Azure Active Directory esattamente come accade in uno scenario 'Non aggiunto'.|
| Verificato | In questo caso è necessario un dominio personalizzato contoso.com già aggiunto e verificata in Azure Active Directory per il suffisso UPN. Gli utenti potranno usare il nome dell'entità utente locale, ad esempio user@contoso.com, effettuare l'accesso a Azure dopo sono sincronizzati Azure Active Directory|

###### <a name="ad-fs-federation"></a>Federazione ADFS
Non è possibile creare una federazione con il valore predefinito. sul dominio onmicrosoft.com in Azure Active Directory o un dominio personalizzato non verificato in Azure Active Directory. Quando si esegue la procedura guidata Azure AD Connect, se si seleziona un dominio non verificato per creare una federazione con quindi Azure AD Connect verrà chiesto con i record necessari da creare nel punto in cui è ospitato il DNS per il dominio. Per ulteriori informazioni vedere [di seguito](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se è selezionata l'opzione accesso utente come "La federazione con ADFS", è necessario disporre un dominio personalizzato per continuare con la creazione di una federazione in Azure Active Directory. Per la discussione, ciò significa che bisogna sufficiente contoso.com un dominio personalizzato aggiunto nella directory di Azure Active Directory.

| Stato         | Effetto sull'esperienza utente accessohttp Azure |
|:-------------:|:----------------------------------------|
| Non è stato aggiunto | In questo caso Azure AD Connect Impossibile trovare un dominio personalizzato corrispondente per il contoso.com suffisso UPN nella directory di Azure Active Directory. È necessario aggiungere un dominio personalizzato contoso.com, se è necessario effettuare l'accesso tramite ADFS con il relativo UPN locale come utenti user@contoso.com.|
| Non verificati | In questo caso Azure AD Connect visualizzerà appropriati informazioni dettagliate su come è possibile verificare il dominio in una fase successiva|
| Verificato | In questo caso è possibile procedere con la configurazione senza ulteriori operazioni|  

## <a name="changing-user-sign-in-method"></a>Modifica metodo di accesso dell'utente

È possibile modificare il metodo di accesso dell'utente da federazione per la sincronizzazione delle Password utilizzando disponibile attività in Azure AD Connect dopo la configurazione iniziale di Azure AD Connect usando la procedura guidata. Eseguire nuovamente la procedura guidata Azure AD Connect e verrà visualizzato con un elenco di attività che è possibile eseguire. Selezionare **Modifica utente Accedi -** dall'elenco di attività. 

![Modificare account utente"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Nella pagina successiva, verrà chiesto di specificare le credenziali di Azure Active Directory.

![Connettersi a Azure Active Directory](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Nella pagina **account utente** , selezionare **La sincronizzazione delle Password**. Verranno modificate nella directory da federato a uno gestito.

![Connettersi a Azure Active Directory](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Se si apportano solo un parametro temporaneo per la sincronizzazione delle password, quindi selezionare **non convertire gli account utente**. Non controllare l'opzione, verrà visualizzata la conversione di ogni utente federato e può richiedere alcune ore.
  
## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).

Per ulteriori informazioni su [Azure AD Connect: progettare concetti](active-directory-aadconnect-design-concepts.md)


