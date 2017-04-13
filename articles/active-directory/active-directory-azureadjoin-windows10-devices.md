<properties
    pageTitle="Con dispositivi Windows 10 dell'area di lavoro | Microsoft Azure"
    description="Fornisce uno snapshot della funzionalità per gli utenti e IT, confrontare diversi modi un dispositivo può essere completato il provisioning e utilizzato in un'organizzazione con Windows 10."
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
    ms.date="10/17/2016"
    ms.author="femila"/>

# <a name="using-windows-10-devices-in-your-workplace"></a>Uso di dispositivi Windows 10 in luogo di lavoro

Si applica a: PC con Windows 10

Windows 10 sono disponibili tre modelli per le organizzazioni che consentono agli utenti di accedere alle risorse di lavoro in modo semplice e sicuro.

- **Partecipare a Azure Active Directory** (Azure Active Directory Join), per i colleghi che accedono alle risorse, ad esempio Office 365 principalmente nel cloud. Azure Active Directory Join è una soluzione self-service provisioning dell'utente che è le novità di Windows 10.
- **Aggiunta a un dominio**, per le organizzazioni che hanno gli investimenti in locale App e risorse. Aggiunta a un dominio offre un'esperienza in Windows 10 quando connessi a Azure Active Directory.
- **Una nuova esperienza BYOD semplificata**per gli utenti per aggiungere un account aziendale o dell'istituto di istruzione per Windows e facilmente accedere alle risorse di dispositivi personali.

Nella tabella seguente vengono presentate uno snapshot della funzionalità di supporto per gli utenti e amministratori IT, confrontare diversi modi un dispositivo può essere completato il provisioning e utilizzato in un'organizzazione con Windows 10:

|                                                                                                                                                                 | Aggiunta a un dominio     | Azure Active Directory Join | Dispositivo personale |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------|---------------|-----------------|
| Windows dispositivo effettuare l'accesso per gli account aziendale o dell'istituto di istruzione.                                                                                                                      | Sì             | Sì           | No              |
| Utente single sign-on (SSO) per le applicazioni di Office 365 e Azure Active Directory. Single Sign-on è la possibilità di eseguire l'accesso una sola volta accedere alle risorse dell'organizzazione. | Sì             | Sì           | Sì             |
| Utente Single Sign-on alle App Kerberos/NTLM.                                                                                                                                  | Sì             | Limitato       | Sì, tramite VPN         |
| Autorizzazione sicuro e pratico accesso per gli account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello.                                                                   | Sì             | Sì           | Sì             |
| Accesso alla versione enterprise di Windows Store con un account aziendale o dell'istituto di istruzione (non un account Microsoft).                                                                                    | Sì             | Sì           | Sì             |
| Enterprise conforme roaming delle impostazioni utente da dispositivi con un account aziendale o dell'istituto di istruzione.                                                                                 | Sì             | Sì           | Sì             |
| La possibilità di limitare l'accesso alle App dell'organizzazione a dispositivi che sono conformi ai criteri di dispositivo dell'organizzazione.                                                      | Sì             | Sì           | Sì             |
| Utente modalità self-service il provisioning dei dispositivi per "lavoro da qualsiasi posizione".                                                                                                | No              | Sì           | Sì             |
| Possibilità di gestire i dispositivi.                                                                                                                                       | Sì, tramite criteri di gruppo/SCCM | Sì           | Sì             |

## <a name="use-work-owned-devices-with-azure-ad-join-and-domain-join-in-windows-10"></a>Partecipare a usare proprietà lavoro dispositivi con Azure Active Directory Join e il dominio in Windows 10

Windows 10 sono disponibili due modi per i dispositivi di lavoro di proprietà accedere alle risorse lavoro:

- Azure Active Directory Join
- Aggiunta a un dominio

 Entrambe sono valide opzioni in base alle esigenze dell'organizzazione e requisiti. In alcuni casi, le organizzazioni possono usufruire attivazione entrambi i metodi di distribuzione.

## <a name="when-to-use-azure-active-directory-join"></a>Quando utilizzare partecipare Azure Active Directory

Azure Active Directory Join è una nuova modalità self-service il provisioning di ambiente in Windows 10.  È riservata agli worker che accedono alle risorse lavoro, ad esempio Office 365 principalmente nel cloud. È un modo semplice per configurare i computer, Tablet e telefoni cellulari per le aziende. I dispositivi sono gestiti tramite Gestione di dispositivi mobili, utilizzando i controlli coerenti piattaforme di Windows.

**Usare Azure Active Directory partecipare per i motivi seguenti**:

- Si desidera attivare la modalità self-service il provisioning dei dispositivi per collaboratori in viaggio.
- Offrire agli utenti con i dispositivi mobili proprietà lavoro come Tablet e telefoni.
- Si desidera gestire un gruppo di utenti in Azure Active Directory anziché in Active Directory, ad esempio stagionali, imprenditori o gli studenti.
- Si desidera fornire funzionalità di unione per collaboratori in remoto uffici con infrastruttura locale limitato.
- Non si dispone di un server di Active Directory locale.

In alcune organizzazioni utilizzeranno Azure Active Directory partecipare come il modo migliore per distribuire proprietà lavoro dispositivi, in particolare durante la migrazione la maggior parte o tutte le risorse nel cloud. Organizzazioni ibrido con Active Directory e Azure Active Directory anche possono scegliere di distribuire un metodo o in un altro in base all'utente o al reparto IT.

Zone dell'istituto di istruzione e università, ad esempio, potrebbe di gestire personale in Active Directory e gli studenti di Azure Active Directory. Alcune aziende potrebbero essere necessario gestire uffici remoti o reparti vendite in Azure Active Directory. Azure Active Directory Join e i metodi di join dominio utilizzabili all'interno di un'organizzazione ibrida. Azure Active Directory Join può essere un accessorio ideale per aggiunta a un dominio per la distribuzione di dispositivi in un ambiente di lavoro.

**Se l'accesso alle risorse aziendali più comune consiste nell'utilizzare nel cloud, l'organizzazione potrebbe sfrutteranno i vantaggi aggiuntivi se**:

- È possibile rimuovere le dipendenze infrastruttura delle identità locali.
- È possibile semplificare il modello di distribuzione di dispositivi, introduzione all'esterno di soluzioni consentendo configurazione self-service.
- È possibile utilizzare Gestione di dispositivi mobili per gestire tutti i dispositivi tra piattaforme diverse.

Per ulteriori informazioni sui Join di Azure Active Directory, vedere [funzionalità cloud estensione ai dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="when-to-use-domain-join-or-keep-using-it"></a>Quando utilizzare join dominio (o Mantieni usarlo)

Per gli ultimi anni 15, molte organizzazioni hanno implementato aggiunta a un dominio per la connessione di dispositivi di lavoro. Consente agli utenti di accedere ai dispositivi con il proprio lavoro Active Directory o dell'istituto di istruzione account. Aggiunta a un dominio consente inoltre IT a livello centrale e completamente gestire i dispositivi. Organizzazioni in genere si basano sui metodi di acquisizione immagini di provisioning dei dispositivi e spesso essere gestiti tramite System Center Configuration Manager (SCCM) o criteri di gruppo (criteri di gruppo).

**Parte dell'azienda devono essere utilizzate join dominio (o Mantieni usarlo) per i motivi seguenti**:

- È necessario App Win32 distribuito in questi dispositivi che utilizzano NTLM/Kerberos.
- È necessario dei criteri di gruppo o SCCM/DCM gestire i dispositivi.
- Si desidera continuare a utilizzare le soluzioni di acquisizione immagini per configurare i dispositivi per i dipendenti.

**Aggiunta a un dominio in Windows 10 offre anche i seguenti vantaggi quando connessi a Azure Active Directory**:

- Autenticazione associate ai dispositivi e pratico accesso per gli account aziendale o dell'istituto di istruzione con Microsoft Passport e Windows Hello.
- Accesso alla rete aziendale di Windows Store per i dispositivi che utilizzano aziendale o dell'istituto di istruzione account (obbligatorio account di Microsoft).
- Enterprise conforme roaming delle impostazioni utente da dispositivi che utilizzano account aziendale o dell'istituto di istruzione (obbligatorio account di Microsoft).
- La possibilità di limitare l'accesso alle App dell'organizzazione a dispositivi che sono conformi ai criteri di dispositivo dell'organizzazione.

Per ulteriori informazioni sui Join di Azure Active Directory, vedere [funzionalità cloud estensione ai dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-overview.md).

## <a name="enable-joining-of-personally-owned-devices-for-work-or-school"></a>Abilitare l'aggiunta dei dispositivi personalmente di proprietà per l'ufficio o dell'istituto di istruzione

Per supportare BYOD all'interno dell'organizzazione, all'utente di Windows 10 offre la possibilità di aggiungere un account aziendale o dell'istituto di istruzione per i computer, tablet o telefono. Dopo che l'utente ha aggiunto un account aziendale o dell'istituto di istruzione, il dispositivo è registrato con Azure Active Directory e facoltativamente registrato nel sistema di gestione di dispositivi mobili che l'organizzazione ha configurato. Nella directory verrà visualizzati questi dispositivi "Registrata" e 'Aggiungono di Azure Active Directory'. Administraors IT possono applicare criteri diversi in base a queste informazioni, fornire un tocco più chiaro su privati dispositivi più proprietà lavoro dispositivi se lo si desidera.

Gli utenti possono aggiungere un lavoro o dell'istituto di istruzione in modo molto account per il dispositivo personale. È possibile eseguire questa quando si accede a un'applicazione di lavoro per la prima volta o possono farlo manualmente tramite il menu impostazioni. Questo account costituiranno Single Sign-on alle risorse dell'organizzazione.

Per ulteriori informazioni su Azure Active Directory Join, vedere [Connetti dominio ai dispositivi di Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md).

## <a name="enable-domain-join-or-azure-ad-join"></a>Abilitare aggiunta al dominio o un Join di Azure Active Directory

Tutti i metodi descritti in precedenza (aggiunta al dominio, Join di Azure Active Directory e aggiungere aziendale o dell'istituto di istruzione account) presenti punti di ingresso dell'esperienza utente di Windows 10. Tuttavia, tutti richiedono un amministratore IT attivare la funzionalità dell'infrastruttura prima l'esperienza di funzionamento.

## <a name="requirements-for-deploying-azure-ad-join"></a>Requisiti per la distribuzione di Azure Active Directory partecipare

Per la distribuzione di Azure Active Directory partecipare di qualsiasi insieme di utenti è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory.
- Una sottoscrizione di Azure Active Directory Premium, ad esempio un dispositivo mobile gestione registrazione automatica, se si richiede più funzionalità.
- Gestione di dispositivi mobili, ad esempio, un abbonamento a Microsoft Intune, gestione di dispositivi mobili per Office 365 o i fornitori di gestione di dispositivi mobili partner che si integrano con Azure Active Directory. (Vedere la [sezione Domande frequenti](#frequently-asked-questions) verso la fine di questo articolo per ulteriori informazioni).

Se le strutture ibride, consiglia distribuire Azure AD Connect per estendere Azure Active directory locale.

## <a name="requirements-for-using-domain-join-with-azure-ad"></a>Requisiti per l'utilizzo dominio partecipare con Azure Active Directory

Aggiunta a un dominio continua a funzionare come sempre da. Tuttavia, per ottenere i vantaggi di Azure Active Directory è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory.
- Distribuzione di Azure AD Connect per estendere la directory locale di Azure Active Directory.
- Criteri che consente ai dispositivi dominio abbia accesso condizionale a Azure Active Directory.
- Criteri che consente l'accesso ai dispositivi "dominio" Se si desidera limitare l'accesso per alcuni dispositivi.
- System Center Configuration Manager versione 1509 per Technical Preview, per attivare le regole per la richiesta di dispositivi compatibili. (Vedere la documentazione di TechNet e inserire post di blog).

Per ulteriori informazioni sull'aggiunta a un dominio in Windows 10, vedere [esperienze Connetti dominio ai dispositivi di Azure Active Directory per Windows 10](active-directory-azureadjoin-devices-group-policy.md)

## <a name="requirements-for-using-byod-and-add-a-work-or-school-account"></a>Requisiti per l'uso BYOD e "Aggiungi un account aziendale o dell'istituto di istruzione"

Per abilitare "portare il proprio dispositivo" (BYOD) con un account aziendale o dell'istituto di istruzione, è necessario le operazioni seguenti:

- Una sottoscrizione di Azure Active Directory.
- Una sottoscrizione di Azure Active Directory Premium, ad esempio un dispositivo mobile gestione registrazione automatica, se si richiede più funzionalità.

## <a name="requirements-for-using-microsoft-passport"></a>Requisiti per l'utilizzo di Microsoft Passport

Per abilitare Microsoft Passport, sarà necessario le operazioni seguenti:

- Una pubblica chiave infrastruttura () per il supporto per l'autenticazione basata sul certificato che viene utilizzato Microsoft Passport.
- Un abbonamento Intune per il supporto per l'autenticazione basata sul certificato che utilizza Microsoft Passport per partecipare a Azure Active Directory e gli account aziendale o dell'istituto di istruzione.
- System Center Configuration Manager versione 1509 per Technical Preview (vedere la documentazione di TechNet e inserire post di blog) per il supporto di autenticazione basata su certificati che utilizza Microsoft Passport per l'aggiunta al dominio.
- Criteri per l'attivazione di Microsoft Passport nell'organizzazione.

In alternativa all'utilizzo di un'infrastruttura a chiave pubblica, è possibile abilitare basata su Microsoft Passport eseguendo le operazioni seguenti:

- Distribuire Windows Server 2016 "Produzione anteprima 1" controller di dominio (senza la necessità di livelli di dominio o struttura; diversi controller di dominio per rendere disponibile la ridondanza che ogni sito di Active Directory è sufficiente).
- Impostare criteri per abilitare Microsoft Passport nell'organizzazione.

Per ulteriori informazioni su Microsoft Passport e Windows Hello in Windows 10, vedere < link-to-MS-Passport-and-Windows-Hello-document >.

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="which-partner-mobile-device-management-products-integrate-with-azure-ad"></a>Quali prodotti di gestione di dispositivi mobili partner si integrano con Azure Active Directory?

I seguenti prodotti fornitore integrano con Azure Active Directory per l'accesso condizionale in Windows 10 e unificata registrazione:

- AirWatch da VMware
- Citrix Xenmobile
- Gestione di dispositivi mobili Lightspeed
- Gestione di dispositivi mobili di SOTI locale
- MobileIron

### <a name="what-about-workplace-join-in-windows-10"></a>Informazioni sulle luogo di lavoro partecipare in Windows 10?
Join luogo di lavoro è stato usato in Windows 8.1 per abilitare BYOD. In Windows 10, BYOD viene abilitata tramite "Aggiungere un aziendale o dell'istituto di istruzione account", come illustrato in precedenza in questo documento. Per le organizzazioni che non si integrano la gestione di dispositivi mobili con Azure Active Directory, gli utenti possono registrare il dispositivo in Gestione manualmente mediante **Impostazioni** > **account** > **accesso di lavoro**.


### <a name="can-users-connect-their-microsoft-account-to-their-domain-account-in-windows-10"></a>Gli utenti connettersi l'account Microsoft personale all'account di dominio in Windows 10?
Non in Windows 10. In Windows 8.1, gli utenti dei dispositivi dominio Impossibile "connettersi" il proprio account Microsoft (ad esempio, Hotmail, Live, Outlook, Xbox, ecc.) all'account di dominio per attivare alcune esperienze come Single Sign-on per i servizi di Live, utilizzo di Windows Store e roaming delle impostazioni utente tra i dispositivi. In Windows 10, l'account di Microsoft "connettersi" funzionalità è stata ritirata. L'utente può aggiungere uno o più account di Microsoft come altri account per consentire di Single Sign-on di servizi consumer, ad esempio Windows Store. Questa operazione viene eseguita nelle **Impostazioni** > **account** > **l'account**.

Gli utenti che esegue l'aggiornamento da Windows 8.1 dominio dispositivi e chi ha il proprio account Microsoft connesso, avrà automaticamente il proprio account Microsoft connesso aggiunto all'elenco di altri account che usino.


## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
