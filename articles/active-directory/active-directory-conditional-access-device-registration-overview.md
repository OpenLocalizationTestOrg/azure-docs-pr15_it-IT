<properties
    pageTitle="Panoramica di Azure Active Directory dispositivo registrazione | Microsoft Azure"
    description="è la base per gli scenari di accesso condizionale basato sul dispositivo. Quando un dispositivo è registrato, registrazione di Azure Active Directory dispositivo provisioning del dispositivo con un'identità che viene utilizzato per eseguire l'autenticazione il dispositivo quando l'utente accede."
    services="active-directory"
    keywords="registrazione di dispositivo, Attiva registrazione periferica, registrazione periferica e MDM"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="Markvi"/>

# <a name="get-started-with-azure-active-directory-device-registration"></a>Guida introduttiva di Azure Active Directory dispositivo registrazione

Azure registrazione di Active Directory dispositivo è la base per gli scenari di accesso condizionale basato sul dispositivo. Quando un dispositivo è registrato, registrazione di Azure Active Directory dispositivo fornisce il dispositivo con un'identità che viene utilizzato per eseguire l'autenticazione il dispositivo quando l'utente accede. Il dispositivo autenticato e gli attributi del dispositivo, quindi utilizzabile per applicare i criteri di accesso condizionato per le applicazioni presenti nel cloud e locale.

In combinazione con una soluzione di management(MDM) dispositivo mobile, ad esempio Microsoft Intune, gli attributi di dispositivo in Azure Active Directory vengono aggiornati con informazioni aggiuntive sul dispositivo. In questo modo è possibile creare regole di accesso condizionato che consentono l'accesso da dispositivi per soddisfare agli standard di sicurezza e conformità. Per ulteriori informazioni sulla registrazione dispositivi in Microsoft Intune, vedere [dispositivi di registrazione per la gestione in Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune).

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Scenari abilitati dal registrazione Azure Active Directory dispositivo

Azure registrazione di Active Directory dispositivo include il supporto per iOS, Android e Windows dispositivi. I singoli scenari che utilizzano la registrazione di Azure Active Directory dispositivo potrebbero essere più specifici requisiti e piattaforma di supporto. Gli scenari seguenti sono i seguenti:

- **Condizionale accedere alle applicazioni che sono ospitati in locale**: È possibile utilizzare i dispositivi registrati con i criteri di accesso per le applicazioni che configurate per l'utilizzo di ADFS con Windows Server 2012 R2. Per ulteriori informazioni sull'impostazione di accesso condizionato per locale, vedere [impostazione dell'accesso condizionale locali con Azure Active Directory dispositivo registrazione](active-directory-conditional-access-on-premises-setup.md).

- **Accesso condizionato per le applicazioni di Office 365 con Microsoft Intune** : gli amministratori IT possono effettuare il provisioning di criteri di dispositivo accesso condizionato per proteggere risorse aziendali, mentre nello stesso momento consentendo agli information worker nei dispositivi conformi di accedere ai servizi. Per ulteriori informazioni, vedere [Condizionale i criteri di dispositivo di accesso per i servizi di Office 365](active-directory-conditional-access-device-policies.md).

##<a name="setting-up-azure-active-directory-device-registration"></a>Impostazione di registrazione di Azure Active Directory dispositivo

È necessario attivare la registrazione di Azure Active Directory dispositivo nel portale di Azure, in modo che i dispositivi mobili di individuare il servizio riconoscibili noto record DNS. È necessario configurare la propria azienda DNS in modo che i dispositivi Windows 10, Windows 8.1, Windows 7, Android e iOS consentono di individuare e utilizzare il servizio.
Consente di visualizzare e abilitare/disabilitare dispositivi registrati tramite il portale di amministrazione di Azure Active Directory.

>[AZURE.NOTE]
 Per vedere più recente istruzioni su come configurare la registrazione di dispositivo automatico, [come configurare la registrazione automatica del dominio di Windows aggiungono dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

### <a name="enable-azure-active-directory-device-registration-service"></a>Attivare il servizio di registrazione dispositivi di Azure Active Directory

1. Accedere al portale di Microsoft Azure come amministratore.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Nella scheda **Directory** selezionare directory.
4. Selezionare la scheda **Configura** .
5. Scorrere fino alla sezione denominata **dispositivi**.
6. Selezionare **tutti** **gli utenti possono dispositivi partecipa alla rete aziendale**.
7. Selezionare il numero massimo di dispositivi che si desidera autorizzare per ogni utente.

>[AZURE.NOTE]
>La registrazione con Microsoft Intune o gestione di dispositivi mobili per Office 365 è necessario partecipare a luogo di lavoro. Se è stato configurato uno di questi servizi, tutto sia selezionata e il pulsante NONE è disattivato.

Per impostazione predefinita, l'autenticazione a due fattori non è abilitato per il servizio. Tuttavia, è consigliabile autenticazione a due fattori durante la registrazione di un dispositivo.

- Prima che richiede l'autenticazione a due fattori per questo servizio, è necessario configurare un provider di autenticazione a due fattori di Azure Active Directory e configurare gli account utente per l'autenticazione a più fattori, vedere [Aggiunta di autenticazione a più fattori di Azure Active Directory](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)

- Se si utilizza ADFS con Windows Server 2012 R2, è necessario configurare un modulo di autenticazione a due fattori in ADFS, vedere [Utilizzo di autenticazione a più fattori con Active Directory Federation Services](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Configurare Individuazione di registrazione di Azure Active Directory dispositivo
I dispositivi Windows 7 e Windows 8.1 scopriranno il servizio di registrazione di dispositivo combinando il nome dell'account utente con un nome di server registrazione periferica noto.

È necessario creare un record CNAME DNS che il record associato al servizio di registrazione di Azure Active Directory dispositivo di puntamento. Il record CNAME è necessario utilizzare enterpriseregistration prefisso noto seguito dal suffisso UPN utilizzato da account utente dell'organizzazione. Se l'organizzazione Usa più suffissi UPN, è necessario creare più record CNAME in DNS.

Ad esempio, se è utilizzare un suffisso UPN due organizzazione denominata @contoso.com e @region.contoso.com, è necessario creare i record DNS seguenti.

| Voce                                     | Tipo  | Indirizzo                            |
|-------------------------------------------|-------|------------------------------------|
| enterpriseregistration.contoso.com        | RECORD CNAME | enterpriseregistration.Windows.NET |
| enterpriseregistration.Region.contoso.com | RECORD CNAME | enterpriseregistration.Windows.NET |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Visualizzare e gestire gli oggetti dispositivo Azure Active Directory
1. Dal portale di amministrazione di Azure, è possibile visualizzare, bloccare e sbloccare dispositivi. Un dispositivo è bloccato non avrà accesso alle applicazioni configurato per consentire solo i dispositivi registrati.
2. Accedere al portale di Microsoft Azure come amministratore.
3. Nel riquadro sinistro selezionare **Active Directory**.
4. Selezionare la directory.
5. Selezionare la scheda **utenti** . Selezionare un utente per visualizzare i dispositivi
6. Selezionare la scheda **dispositivi** .
7. Selezionare **Dispositivi registrati** dal menu a discesa.
8. È possibile visualizzare, bloccare o sbloccare il dispositivo registrati gli utenti.

## <a name="additional-topics"></a>Argomenti aggiuntivi

È possibile registrare i dispositivi Windows 7 e Windows 8.1 dominio con la registrazione di Azure Active Directory dispositivo. Gli argomenti seguenti sono disponibili ulteriori informazioni sui prerequisiti e i passaggi necessari per configurare la registrazione di dispositivo in dispositivi Windows 7 e Windows 8.1.

- [Registrazione automatica dispositivo con Azure Active Directory per Windows dominio dispositivi](active-directory-conditional-access-automatic-device-registration.md)
- [Configurare la registrazione di dispositivo automatico per i dispositivi di dominio di Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)
- [Configurare la registrazione di dispositivo automatico per i dispositivi di dominio Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registrazione automatica dispositivo con i dispositivi di dominio di Azure Active Directory per Windows 10](active-directory-azureadjoin-devices-group-policy.md)
