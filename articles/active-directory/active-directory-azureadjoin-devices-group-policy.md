<properties
    pageTitle="Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze | Microsoft Azure"
    description="Spiega come gli amministratori possono configurare i criteri di gruppo per consentire ai dispositivi di dominio per la rete aziendale."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="connect-domain-joined-devices-to-azure-ad-for-windows-10-experiences"></a>Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze

Aggiunta a un dominio è che organizzazioni modalità tradizionale connessi dispositivi per il lavoro per gli ultimi anni 15 e altro ancora. Viene attivata agli utenti di accedere ai dispositivi tramite il loro lavoro Windows Server Active Directory (Active Directory) o dell'istituto di istruzione account e consentito IT per gestire i dispositivi. Organizzazioni in genere si basano sui metodi di acquisizione immagini di provisioning dei dispositivi agli utenti e in genere utilizzano System Center Configuration Manager (SCCM) o criteri di gruppo per gestirli.

Dopo la connessione di dispositivi di Azure Active Directory (Azure Active Directory), aggiunta a un dominio in Windows 10 fornirà i seguenti vantaggi:

- Singolo sign-on (SSO) alle risorse di Azure Active Directory ovunque
- Accesso alla rete aziendale di Windows Store utilizzando l'account aziendale o dell'istituto di istruzione (obbligatorio account di Microsoft)
- Enterprise conforme roaming delle impostazioni utente tra i dispositivi usando l'account aziendale o dell'istituto di istruzione (obbligatorio account di Microsoft)
- Autenticazione e pratico accesso per gli account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello
- Possibilità di limita l'accesso ai dispositivi conformi alle impostazioni di criteri di gruppo dispositivo organizzativa

## <a name="prerequisites"></a>Prerequisiti

Aggiunta a un dominio continua a essere utile. Tuttavia, per ottenere i vantaggi di Azure Active Directory di Single Sign-on, il roaming delle impostazioni di lavoro o dell'istituto di istruzione account e accesso a Windows Store con un account aziendale o dell'istituto di istruzione, sarà necessario le operazioni seguenti:

- Sottoscrizione di Azure Active Directory
- Azure AD Connect per estendere la directory locale di Azure Active Directory
- Criteri impostati a cui connettersi dominio dispositivi Azure Active Directory
- Generazione di Windows 10 (build 10551 o versioni successive) per i dispositivi

Per abilitare Microsoft Passport per il lavoro e Windows Hello, è necessario anche le operazioni seguenti:

- Infrastruttura chiave pubblica () per il rilascio di certificati utente.
- System Center Configuration Manager versione 1509 per Technical Preview. Per ulteriori informazioni, vedere [Microsoft System Center configurazione Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) e [Blog del Team di System Center Configuration Manager](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx). È necessario per distribuire certificati utente basati sulle chiavi Microsoft Passport.

In alternativa al requisito di distribuzione infrastruttura a chiave pubblica, è possibile eseguire le operazioni seguenti:

- Alcuni controller di dominio con Windows Server 2016 servizi di dominio.

Per abilitare l'accesso condizionale, è possibile creare impostazioni di criteri di gruppo che consentono l'accesso a dominio dispositivi con alcun distribuzioni aggiuntive. Per gestire il controllo dell'accesso basato sulla conformità del dispositivo, è necessario quanto segue:

- System Center Configuration Manager versione 1509 per Technical Preview per gli scenari di Passport

## <a name="deployment-instructions"></a>Istruzioni di distribuzione



### <a name="step-1-deploy-azure-active-directory-connect"></a>Passaggio 1: Distribuzione di Azure Active Directory connettersi

Azure AD Connect consentirà di effettuare il provisioning computer locale come oggetti dispositivo nel cloud. Per distribuire Azure AD Connect, fare riferimento a "Installazione Azure AD Connect" nell'articolo [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md#install-azure-ad-connect).

 - Se è stata eseguita un' [installazione personalizzata per Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md) (non l'installazione rapida), quindi seguire la procedura **creare una connessione al servizio scegliere di Active Directory locale**, più avanti in questo passaggio.
 - Se si dispone di una configurazione federata con Azure Active Directory prima di installare Azure AD Connect (ad esempio, se è stato distribuito Active Directory Federation Services (ADFS) prima), seguire la procedura di **configurazione ADFS Richiedi regole** , più avanti in questo passaggio.

#### <a name="create-a-service-connection-point-in-on-premises-active-directory"></a>Creare un punto di connessione di servizio in Active Directory locale

Dominio dispositivi utilizzerà il punto di connessione per individuare informazioni tenant di Azure Active Directory al momento della registrazione automatica con il servizio di registrazione del dispositivo Azure.

Sul server Azure AD Connect, eseguire i comandi di PowerShell seguenti:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;


Quando si esegue il cmdlet $aadAdminCred = Get-Credential, utilizzare il formato *user@example.com* per il nome utente delle credenziali che viene immesso quando viene visualizzata la finestra popup Get-Credential.

Durante l'esecuzione di cmdlet inizializzazione ADSyncDomainJoinedComputerSync, sostituire [*nome dell'account connettore*] con l'account di dominio che viene utilizzato come account di connettore di Active Directory.

#### <a name="configure-ad-fs-claim-rules"></a>Configurare le regole attestazione di ADFS
Configurare le regole di Richiedi ADFS Abilita registrazione istantanea di un computer con il servizio di registrazione di Azure dispositivo consentendo computer per l'autenticazione tramite Kerberos/NTLM tramite ADFS. Se non viene eseguito computer otterrà di Azure Active Directory in modo ritardato (soggetto a volte di sincronizzazione di Azure AD Connect).

>[AZURE.NOTE]
Se non si dispone di ADFS come il federazione server locale, seguire le istruzioni del fornitore per creare le regole di richiesta di rimborso.

Nel server ADFS (o in una sessione connesso al server ADFS), eseguire i comandi di PowerShell seguenti:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed
     |   for DomainJoin++. The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules

    $rule1 = '@RuleName = "Issue object GUID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'

    $rule2 = '@RuleName = "Issue account type for domain joined computers"
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'

    $rule3 = '@RuleName = "Pass through primary SID"
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
          => issue(claim = c2);'

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString

>[AZURE.NOTE]
Computer Windows 10 verranno autenticati mediante l'autenticazione integrata di Windows a un endpoint WS-Trust active ospitato da ADFS. Assicurarsi che sia abilitato l'endpoint. Se si utilizza il Proxy di autenticazione Web, assicurarsi che l'endpoint viene pubblicato attraverso il proxy. È possibile eseguire questa operazione selezionando adfs/services/protezione/13/windowstransport. Dovrebbe essere visualizzata come attivati nella console di ADFS in **servizio** > **i punti finali**.


### <a name="step-2-configure-automatic-device-registration-via-group-policy-in-active-directory"></a>Passaggio 2: Configurare la registrazione di dispositivo automatico tramite criteri di gruppo in Active Directory.

È possibile usare i criteri di gruppo in Active Directory per configurare i dispositivi Windows 10 dominio per registrare automaticamente con Azure Active Directory.

> [AZURE.NOTE]
> Per vedere più recente istruzioni su come configurare la registrazione di dispositivo automatico, [come configurare la registrazione automatica del dominio di Windows aggiungono dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
>
> Questo modello di criteri di gruppo è stato rinominato in Windows 10. Se si esegue lo strumento di criteri di gruppo da un computer Windows 10, il criterio verrà visualizzata come: <br>
> **Registrare i computer di dominio come dispositivi**<br>
> Il criterio viene visualizzato in uno dei seguenti percorsi:<br>
> ***Registrazione di componenti/dispositivo configurazione/criteri/amministrativi modelli/Windows del computer***


## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
