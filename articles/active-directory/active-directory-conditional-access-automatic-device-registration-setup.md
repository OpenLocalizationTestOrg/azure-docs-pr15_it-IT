<properties
    pageTitle="Configurare la registrazione automatica dei dispositivi di dominio di Windows con Azure Active Directory | Microsoft Azure"
    description="Configurare i dispositivi di Windows dominio per registrare automaticamente e automaticamente con Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="markvi"/>



# <a name="set-up-automatic-registration-of-windows-domain-joined-devices-with-azure-active-directory"></a>Configurare la registrazione automatica di Windows dominio dispositivi con Azure Active Directory

Per utilizzare [access di Azure Active Directory basate su dispositivo condizionale](active-directory-conditional-access.md), è necessario che sia registrati i computer di dominio di Windows con Azure Active Directory (Azure Active Directory). In questo articolo per informazioni è necessario eseguire per configurare la registrazione dei dispositivi di dominio di Windows con Azure Active Directory nell'organizzazione.

Utilizzo di access condizionale in Azure Active Directory offre questi vantaggi:

-   Esperienza avanzata single sign-on (SSO) nelle applicazioni di Azure Active Directory mediante account aziendale o dell'istituto di istruzione
-   Enterprise roaming delle impostazioni tra i dispositivi
-   Accesso a Windows Store per le aziende
-   Autenticazione più efficace e pratico accesso con Windows Hello

> [AZURE.NOTE] L'aggiornamento di Windows 10 novembre offre alcune delle esperienze utente avanzata in Azure Active Directory, ma l'aggiornamento di Windows 10 anniversario supporta accesso condizionale basato sul dispositivo. Per ulteriori informazioni sull'accesso condizionale, vedere [accesso condizionale basato su dispositivo Azure Active Directory](active-directory-conditional-access.md). Per ulteriori informazioni sui dispositivi Windows 10 in un'area di lavoro e come si registra un dispositivo con Windows 10 con Azure Active Directory, vedere [Windows 10 per le aziende: utilizzare i dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md).

È possibile registrare alcune versioni precedenti di Windows, incluse queste versioni:

-   Windows 8.1
-   Windows 7

Se si usa un computer Windows Server come desktop, è possibile registrare queste piattaforme:

- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2


## <a name="prerequisites"></a>Prerequisiti

Il requisito principale per la registrazione automatica dei dispositivi dominio tramite Azure Active Directory è installata una versione aggiornata di Azure Active Directory la connessione (Azure AD Connect).

In base alla distribuzione Azure AD Connect e se è stata utilizzata un'installazione express o personalizzata o un aggiornamento sul posto, i prerequisiti seguenti potrebbero essere stati configurati automaticamente:

-   **Punto di connessione in Active Directory locale**. Per l'individuazione di informazioni tenant di Azure Active Directory da computer che eseguire la registrazione per Azure Active Directory.

-   **Rilascio di active Directory Federation Services (ADFS) trasformare le regole**. Per l'autenticazione del computer nella registrazione (applicabile alle configurazioni federati).

Se alcuni dispositivi le organizzazioni non sono dispositivi Windows 10 dominio, assicurarsi di eseguire le operazioni seguenti:

- Impostare un criterio di Azure Active Directory, in modo che gli utenti possono eseguire la registrazione di dispositivi
- Impostare integrata l'autenticazione di Windows integrata (Windows) come un'alternativa valida per l'autenticazione a più fattori in ADFS



## <a name="set-a-service-connection-point-for-discovery-of-the-azure-ad-tenant"></a>Impostare un punto di connessione del servizio per l'individuazione del tenant Azure Active Directory

Un oggetto punto di connessione di servizio deve esistere nella configurazione per la denominazione partizione di contesto della foresta del dominio in cui vengono aggiunti computer. Il punto di connessione contiene informazioni individuazione tenant di Azure Active Directory in cui eseguire la registrazione computer. Una configurazione di Active Directory più insiemi di strutture, il punto di connessione che siano presenti in tutti gli insiemi di strutture contenenti dominio computer.

Impostare il punto di connessione in questi percorsi in Active Directory:

    CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Configuration Naming Context]

> [AZURE.NOTE] Per un insieme di strutture di Active Directory nome di dominio *example.com*, contesto di denominazione della configurazione è CN = Configurazione, Cc = esempio, Cc = com.

È possibile cercare i valori oggetto e individuazione del tenant Azure Active Directory utilizzando il seguente script di Windows PowerShell. (Sostituire contesto della configurazione nell'esempio con il contesto di configurazione).

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=example,DC=com";

    $scp.Keywords;

Il `$scp.Keywords` output vengono visualizzate le informazioni di tenant di Azure Active Directory:

    azureADName:microsoft.com

    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se il punto di connessione non esiste, crearla eseguendo il seguente script di PowerShell nel server Azure AD Connect:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


> [AZURE.NOTE]Quando si esegue `$aadAdminCred = Get-Credential`, utilizzare il formato *user@example.com* per il nome utente nella finestra di dialogo **Get-Credential** .  
> Quando si esegue il cmdlet inizializzazione ADSyncDomainJoinedComputerSync, sostituire [*nome dell'account connettore*] con l'account di dominio che viene utilizzata per l'account Active Directory connector.  
> Il cmdlet utilizza il modulo di Active Directory PowerShell, che si basa sui servizi Web di Active Directory in un controller di dominio. Servizi Web di Active Directory è supportato in controller di dominio in Windows Server 2008 R2 e versioni successive. Controller di dominio in Windows Server 2008 o versioni precedenti, utilizzare l'API DirectoryServices tramite PowerShell per creare il punto di connessione e quindi assegnare i valori di parole chiave.

## <a name="create-ad-fs-rules-for-instant-device-registration-in-federated-organizations"></a>Creare regole di ADFS per la registrazione periferica immediata nelle organizzazioni federati

In un Azure federati configurazione di Active Directory, dispositivi si basano sul ADFS (o sul server federativo locale) per eseguire l'autenticazione di Azure Active Directory. Quindi vengono registrare Azure Active Directory dispositivo registrazione servizio (servizio di registrazione di Azure Active Directory dispositivo).

> [AZURE.NOTE] In una configurazione non federato hash delle password utente vengono sincronizzate in Azure Active Directory e computer Windows 10 e Windows Server 2016 dominio per autenticare il servizio di registrazione di Azure Active Directory dispositivo. Un utente viene autenticato tramite le credenziali che l'utente inserisce gli account computer locale, e inoltro di Azure Active Directory tramite Azure AD Connect. Per non Windows 10 e Windows Server 2016 computer una configurazione non federato, sono disponibili opzioni per l'impostazione di un'autorità di certificazione basate su dispositivo all'interno dell'organizzazione. Per ulteriori informazioni, vedere i pacchetti di scaricare Windows Installer sezione per i computer non Windows 10 in questo articolo.

Per i computer Windows 10 e Windows Server 2016, Azure AD Connect associa l'oggetto di dispositivo in Azure Active Directory con l'oggetto di account computer locale. Domande seguenti è necessario che durante l'autenticazione per il servizio di registrazione di Azure Active Directory dispositivo completare la registrazione e creare l'oggetto dispositivo:

- http://schemas.microsoft.com/ws/2012/01/AccountType contiene il valore di dispositivo, che identifica autenticatore principale come computer dominio.
- http://schemas.microsoft.com/Identity/claims/onpremobjectguid contiene il valore dell'attributo **objectGUID** dell'account del computer locale.
- http://schemas.microsoft.com/ws/2008/06/Identity/claims/primarysid contiene primario SID protezione () del computer, che corrisponde al valore dell'attributo **objectSid** dell'account del computer locale.
- http://schemas.microsoft.com/ws/2008/06/Identity/claims/issuerid contiene il valore utilizzato per considerare attendibile il token emesso da ADFS o dal locale sicurezza servizio token di Azure Active Directory. È importante in una configurazione di Active Directory più insiemi di strutture. In questa configurazione computer potrebbero essere aggiunto a un insieme di strutture diverso da quello che si connette a Azure Active Directory in AD FS o servizio token di sicurezza locale. Per AD FS, usare il valore di*nome di dominio*; http://< >/adfs/services/protezione /, dove <*nome di dominio*> è il nome di dominio convalidati in Azure Active Directory.

Per creare le regole manualmente, in ADFS, usare il seguente script di PowerShell in una sessione di cui è connesso al server. Sostituire la prima riga con il nome di dominio dell'organizzazione convalidati in Azure Active Directory.

> [AZURE.NOTE] Se non si usa ADFS del server federativo locale, seguire le istruzioni del fornitore per creare le regole che le richieste di problema.

    $validatedDomain = "example.com"      # Replace example.com with your organization's validated domain name in Azure AD

    $rule1 = '@RuleName = "Issue object GUID"

        c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

        c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

        => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain-joined computers"

      c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

> [AZURE.NOTE] Utilizzare le prime tre regole se l'ambiente locale è una singola foresta. Se il computer si trovano in una foresta diversa da quella la sincronizzazione con Azure Active Directory o se si usa nomi alternativi a quelle nella configurazione di sincronizzazione, è necessario includere anche altre regole.

    $rule3 = '@RuleName = "Pass through primary SID"

      c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]

      => issue(claim = c2);'

    $rule4 = '@RuleName = "Issue AccountType with the value User when it’s not a computer account"

      NOT EXISTS([Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"])

      => add(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "User");'

    $rule5 = '@RuleName = "Capture UPN when AccountType is User and issue the IssuerID"

      c1:[Type == "http://schemas.xmlsoap.org/claims/UPN"] &&

      c2:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "User"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c1.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));'

    $rule6 = '@RuleName = "Update issuer for DJ computer auth"

      c1:[Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", Value == "DJ"]

      => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = "http://'+$validatedDomain+'/adfs/services/trust/");'

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5 + $rule6

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

> [AZURE.NOTE] Computer Windows 10 e Windows Server 2016 dominio autenticazione tramite integrata di Windows a un endpoint WS-Trust attivo che è ospitato da ADFS. Assicurarsi che l'endpoint sia attivo. Se si utilizza il Proxy di applicazione Web, assicurarsi che l'endpoint viene pubblicato attraverso il proxy. L'endpoint è adfs/services/protezione/13/windowstransport. Per verificare che sia attiva nella console di ADFS accedere al **servizio** > **i punti finali**. Se non si usa ADFS del server federativo locale, seguire le istruzioni del fornitore per assicurarsi che il punto finale corrispondente è attivo.



## <a name="set-up-ad-fs-for-authentication-of-device-registration"></a>Configurare ADFS per l'autenticazione di registrazione periferica

Assicurarsi che integrata di Windows sia impostato come un'alternativa valida per l'autenticazione a più fattori per la registrazione di dispositivo in ADFS. A questo scopo è necessario che un rilascio trasformare regola che attraversano il metodo di autenticazione.

1. Nella console di ADFS, passare a **ADFS** > **Relazioni di Trust** > **Attendibilità componente**.

2. Pulsante destro del mouse sull'oggetto di protezione parte componente piattaforma identità di Microsoft Office 365 e quindi selezionare **Modifica regole attestazione**.

3.  Nella scheda **Regole del rilascio di trasformazione** , selezionare **Aggiungi regola**.

4.  Nell'elenco modello **Richiedi regola** , selezionare **Invia tramite una regola personalizzata reclami**.

5.  Selezionare **Avanti**.

6.  Nella casella **Nome regola attestazione** immettere **Regola Richiedi metodo di autenticazione**.

7.  Nella casella **Richiedi regola** immettere questo comando:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]
    => issue(claim = c);`.

8. Nel server federativo, immettere il comando di PowerShell:

    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

<*RPObjectName*> è il nome di oggetto componente di terze parti per l'oggetto di protezione parte componente Azure Active Directory. L'oggetto è in genere denominato *Piattaforma identità di Microsoft Office 365*.



## <a name="deployment-and-rollout"></a>Della distribuzione

Quando il computer di dominio soddisfa i prerequisiti, sono pronti per eseguire la registrazione con Azure Active Directory.

Il computer Windows Update anniversario 10 e Windows Server 2016 dominio registra automaticamente con Azure Active Directory la volta successiva che si riavvia il dispositivo o quando un utente accede a Windows. Nuovo computer connessi al dominio registrato con Azure Active Directory quando il dispositivo si riavvia dopo l'operazione di join di dominio.

> [AZURE.NOTE] Computer Windows 10 dominio registra automaticamente con Azure Active Directory solo se è impostata l'oggetto Criteri di gruppo di distribuzione.

È possibile utilizzare un oggetto Criteri di gruppo per controllare la distribuzione di registrazione automatica di Windows 10 e Windows Server 2016 dominio computer. Per distribuire il registrazione automatica del computer di dominio - Windows 10, è possibile distribuire un pacchetto di Windows Installer nel computer che si seleziona.

> [AZURE.NOTE] I criteri di gruppo per il controllo di implementazione attiva anche registrazione di Windows 8.1 dominio computer. È possibile utilizzare il criterio per la registrazione dei computer Windows 8.1 dominio. In alternativa, se si dispone di una combinazione di versioni di Windows, comprese le versioni di Windows 7 o Windows Server, è possibile registrare dei - Windows 10 e Windows Server 2016 computer utilizzando un pacchetto di Windows Installer.

### <a name="create-a-group-policy-object-to-control-the-rollout-of-automatic-registration"></a>Creare un oggetto Criteri di gruppo per controllare la distribuzione di registrazione automatica

Per controllare la distribuzione di registrazione automatica del dominio computer con Azure Active Directory, è possibile distribuire i criteri di gruppo **registrare dominio computer come dispositivi** per i computer che si desidera registrare. Ad esempio, è possibile distribuire il criterio a un'unità organizzativa o a un gruppo di sicurezza.

Per impostare il criterio, eseguire le operazioni seguenti:

1. Aprire Server Manager e quindi fare clic su **Strumenti** > **Gestione criteri di gruppo**.

2. Passare al nodo del dominio che corrisponde al dominio in cui si desidera attivare la registrazione automatica dei computer Windows 10 o Windows Server 2016.

3. Pulsante destro del mouse **Dei criteri di gruppo**e quindi selezionare **Nuovo**.

4. Immettere un nome per l'oggetto Criteri di gruppo. Ad esempio, *La registrazione automatica di Azure Active Directory*. Fare clic su **OK**.

4. Fare di nuovo oggetto Criteri di gruppo e quindi selezionare **Modifica**.

5. Passare a **Configurazione Computer** > **criteri** > **modelli amministrativi** > **componenti di Windows** > **registrazione periferica**. Pulsante destro del mouse **Registro dominio computer come dispositivi**e quindi selezionare **Modifica**.

    > [AZURE.NOTE] Questo modello di criteri di gruppo è stato rinominato da versioni precedenti di console di gestione dei criteri di gruppo. Se si utilizza una versione precedente della console, passare alla **Configurazione Computer** > **criteri** > **Modelli amministrativi** > **Componenti di Windows** > **Partecipa alla rete aziendale** > **automaticamente computer di client partecipa alla rete aziendale**.

6. Selezionare **attivato**e quindi selezionare **Applica**.

7. Fare clic su **OK**.

8. Creare un collegamento oggetto Criteri di gruppo in una posizione di propria scelta. Ad esempio, è possibile collegare a una specifica unità organizzativa. È anche possibile collegarlo a un gruppo di sicurezza specifiche del computer in cui registrare automaticamente con Azure Active Directory. Per impostare il criterio per tutti dominio Windows 10 e Windows Server 2016 computer dell'organizzazione, l'oggetto Criteri di gruppo collegato al dominio.

### <a name="download-windows-installer-packages-for-non-windows-10-computers"></a>Scaricare i pacchetti di Windows Installer da computer a Windows 10  

Per registrare il computer di dominio che eseguono Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2, è possibile scaricare e installare questi file di pacchetto (MSI) di Windows Installer:

- [x64](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x64.msi)
- [x86](http://download.microsoft.com/download/C/A/7/CA79FAE2-8C18-4A8C-A4C0-5854E449ADB8/Workplace_x86.msi)

Distribuire il pacchetto usando un sistema di distribuzione di software come System Center Configuration Manager. Il pacchetto supporta le opzioni di installazione interattiva standard con il parametro *non interattiva* . System Center Configuration Manager 2016 offre vantaggi aggiuntivi rispetto alle versioni precedenti, ad esempio la possibilità di tenere traccia delle registrazioni completate. Per ulteriori informazioni, vedere [System Center 2016](https://www.microsoft.com/en-us/cloud-platform/system-center).

Il programma di installazione crea un'attività programmata nel sistema che viene eseguito nel contesto dell'utente. L'attività viene attivata quando l'utente accede a Windows. L'attività consente di registrare automaticamente il dispositivo con Azure Active Directory con le credenziali utente dopo l'autenticazione tramite integrata di Windows. Per visualizzare l'attività pianificata, passare a **Microsoft** > **Partecipare luogo di lavoro**e quindi passare alla raccolta di utilità di pianificazione.



## <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionale di Azure Active Directory](active-directory-conditional-access.md)
