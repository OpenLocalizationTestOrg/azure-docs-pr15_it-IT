<Properties
    pageTitle="Accesso condizionale di Azure Active Directory | Microsoft Azure"  
    description="Utilizzare il controllo dell'accesso condizionale di Azure Active Directory per cercare specifiche condizioni per l'autenticazione per l'accesso alle applicazioni."  
    services="active-directory"
    keywords="accesso condizionato per App, l'accesso condizionale con Azure Active Directory, l'accesso sicuro a risorse aziendali, criteri di accesso condizionale"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/21/2016"
    ms.author="markvi"/>


# <a name="conditional-access-in-azure-active-directory"></a>Accesso condizionato di Azure Active Directory   

Le funzionalità di controllo di accesso condizionato Azure Active Directory (Azure Active Directory) offrono semplici modi per proteggere le risorse nel cloud e locale. Criteri di accesso condizionale come autenticazione a più fattori consente di proteggere il rischio di furto e le credenziali phished. Altri criteri di accesso condizionale consente di proteggere i dati dell'organizzazione. Ad esempio, oltre a richiedere credenziali, potrebbe essere un criterio specifico solo dispositivi che partecipano un sistema di gestione di dispositivi mobili come Microsoft Intune può accedere ai servizi riservati dell'organizzazione.


## <a name="prerequisites"></a>Prerequisiti

Accesso condizionale di Azure Active Directory è una funzionalità di [Azure Active Directory Premium](http://www.microsoft.com/identity). Ogni utente che accede a un'applicazione che contiene i criteri di accesso condizionale applicati deve avere una licenza di Azure Active Directory Premium. Sono disponibili ulteriori informazioni sui requisiti di licenza nel [rapporto di utenti senza licenza](https://aka.ms/utc5ix).


## <a name="how-is-conditional-access-control-enforced"></a>Come viene attivato il controllo dell'accesso condizionale?  

Controllo di accesso condizionato in posizione, Azure Active Directory verifica la presenza per la specifica le condizioni impostate per un utente di accedere a un'applicazione. Dopo che sono soddisfatti i requisiti di accesso, l'utente viene autenticato e può accedere all'applicazione.  

![Panoramica di accesso condizionale](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condizioni

Di seguito sono indicate le condizioni che è possibile includere in un criterio di accesso condizionale:

- **L'appartenenza ai gruppi**. Controllare l'accesso dell'utente in base all'appartenenza a un gruppo.

- **Posizione**. Utilizzare la posizione dell'utente per l'autenticazione a più fattori trigger e usare i controlli blocchi quando un utente non è presente in una rete attendibile.

- **Piattaforma del dispositivo**. Utilizzare la piattaforma del dispositivo, ad esempio iOS, Android, Windows Mobile o Windows, la condizione per l'applicazione dei criteri.

- **Dispositivo abilitato**. Lo stato del dispositivo, abilitazione o disabilitazione, viene convalidata durante la valutazione dei criteri di dispositivo. Se si disabilita un dispositivo perso o furto nella directory, è possibile non soddisfano più requisiti dei criteri.

- **Rischio di accesso e l'utente**. È possibile utilizzare [la protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md) per i criteri di accesso condizionato rischio. Criteri di accesso condizionato dei rischi sono utili per fornire la protezione di avanzamento dell'organizzazione in base agli eventi di rischio e attività di accesso insolita.


## <a name="controls"></a>Controlli

Si tratta di controlli che è possibile utilizzare per applicare un criterio di accesso condizionale:

- **Autenticazione a più fattori**. È possibile richiedere l'autenticazione mediante l'autenticazione a più fattori. È possibile utilizzare l'autenticazione a più fattori tramite autenticazione a più fattori Azure o tramite un provider di autenticazione a più fattori locale, combinato con Active Directory Federation Services (ADFS). Mediante l'autenticazione a più fattori consente di proteggere risorse dall'accesso autorizzato da un utente non autorizzato che potrebbe avere ottenuto l'accesso alle credenziali di un utente valido.

- **Blocco**. È possibile applicare condizioni come percorso utente per bloccare l'accesso degli utenti. Ad esempio, è possibile bloccare l'accesso quando un utente non è presente in una rete attendibile.

- **Dispositivi compatibili**. È possibile impostare i criteri di accesso condizionale a livello dispositivo. È possibile impostare un criterio in modo che solo i computer che appartengono al dominio o dispositivi mobili che registrati in un'applicazione di gestione di dispositivi mobili, possono accedere alle risorse dell'organizzazione. Ad esempio, è possibile utilizzare Intune per verificare la compatibilità di dispositivo e quindi segnalarlo Azure Active Directory per l'applicazione quando l'utente tenta di accedere a un'applicazione. Per istruzioni dettagliate su come utilizzare Intune per proteggere i dati e le applicazioni, vedere [dati con Microsoft Intune e le applicazioni di protezione](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). È anche possibile usare Intune per applicare la protezione dei dati per i dispositivi di perdita o furto. Per ulteriori informazioni, vedere [proteggere i dati con cancellazione completa o selettivo utilizzando Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Applicazioni

È possibile applicare un criterio di accesso condizionale a livello di applicazione. Impostare i livelli di accesso per applicazioni e servizi nel cloud o locale. Il criterio viene applicato direttamente al sito Web o al servizio. Il criterio viene applicato per accedere al browser e per le applicazioni di accedere al servizio.


## <a name="device-based-conditional-access"></a>Accesso condizionale basato sul dispositivo

È possibile limitare l'accesso alle applicazioni da dispositivi registrate con Azure Active Directory e che soddisfano specifiche condizioni. Accesso condizionale basato su dispositivo impedisce agli utenti che tentano di accedere alle risorse da risorse dell'organizzazione:

- Dispositivi sconosciuti o non gestiti.
- Dispositivi che non soddisfano i criteri di sicurezza per l'organizzazione impostato.

È possibile impostare criteri in base a tali requisiti:

- **Dispositivi di dominio**. Impostare un criterio per limitare l'accesso ai dispositivi che fanno parte di un dominio di Active Directory locale e anche registrate con Azure Active Directory. Questo criterio viene applicato a Windows desktop, notebook e Tablet enterprise.
Per ulteriori informazioni su come configurare la registrazione automatica del dominio dispositivi con Azure Active Directory, vedere [configurare la registrazione automatica di Windows dominio dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

- **Dispositivi compatibili**. Impostare un criterio per limitare l'accesso ai dispositivi contrassegnati **conforme** nella directory di sistema di gestione. Questo criterio assicura che solo i dispositivi che soddisfano i criteri di sicurezza, ad esempio applica la crittografia dei file in un dispositivo sono autorizzati ad accedere. È possibile utilizzare il criterio per limitare l'accesso da dispositivi seguenti:

    - **Dispositivi di dominio di Windows**. Gestite da System Center Configuration Manager (ramo corrente) una configurazione ibrida.
    - **Windows 10 Mobile ufficio o dispositivi personali**. Gestiti dall'Intune o da un sistema di gestione di dispositivi supportati terze parti.
    - **dispositivi iOS e Android**. Gestita da Intune.


Gli utenti che accedono alle applicazioni che sono protetti da un dispositivo basato su, i criteri Autorità di certificazione necessario accedere all'applicazione da un dispositivo che soddisfi i requisiti del criterio. Accesso negato se si è tentato in un dispositivo che non soddisfa i requisiti.

Per informazioni su come configurare un criterio di autorità di certificazione basate su dispositivo in Azure Active Directory, vedere [impostare i criteri di accesso condizionale basato su dispositivo Applications connessi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Risorse

Vedere i seguenti articoli per ulteriori informazioni sull'impostazione accesso condizionato per l'organizzazione e categorie di risorse.


### <a name="multi-factor-authentication-and-location-policies"></a>Criteri di autenticazione e la posizione a più fattori

- [Introduzione a access condizionale alle App Azure Active Directory connessi in base a gruppo, luogo e i criteri di autenticazione a più fattori](active-directory-conditional-access-azuread-connected-apps.md)
- [Applicazioni supportate](active-directory-conditional-access-supported-apps.md)


### <a name="device-based-conditional-access"></a>Accesso condizionale basato sul dispositivo

- [Impostare criteri di accesso condizionale basato su dispositivo per il controllo dell'accesso alle applicazioni connessi Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
- [Configurare la registrazione automatica di Windows dominio dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
- [Risoluzione dei problemi per problemi di accesso di Azure Active Directory](active-directory-conditional-access-device-remediation.md)
- [Proteggere i dati su dispositivi di perdita o furto utilizzando Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### <a name="protect-resources-based-on-sign-in-risk"></a>Proteggere le risorse in base a rischio di accesso

-   [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)

### <a name="next-steps"></a>Passaggi successivi

- [Accesso condizionato domande frequenti](active-directory-conditional-faqs.md)
- [Guida di riferimento tecnico](active-directory-conditional-access-technical-reference.md)
