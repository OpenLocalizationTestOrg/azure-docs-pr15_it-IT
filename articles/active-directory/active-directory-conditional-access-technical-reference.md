
<properties
    pageTitle="Documentazione tecnica di Azure Active Directory Access condizionale | Microsoft Azure"
    description="Controllo di accesso condizionato, Azure Active Directory controlla le condizioni specifiche scelto per l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Quando sono soddisfatte le condizioni, l'utente viene autenticato e autorizzato ad accedere all'applicazione."
    services="active-directory"
    documentationCenter=""
    authors="MarkusVi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="10/20/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-conditional-access-technical-reference"></a>Documentazione tecnica di Azure Active Directory Access condizionale

## <a name="services-enabled-with-conditional-access"></a>Servizi attivati con accesso condizionale
Regole di accesso condizionale sono supportate in vari tipi di applicazioni di Azure Active Directory. L'elenco include:

- Applicazioni federati dalla raccolta applicazione Azure Active Directory
- Applicazioni di Single Sign-on password dalla raccolta applicazione Azure Active Directory
- Applicazioni registrate con il Proxy di applicazione Azure
- Riga sviluppo di applicazioni aziendali e multi-tenant registrate con Azure Active Directory
- Visual Studio Online
- App Remote Azure
-   Dynamics CRM
- Yammer di Microsoft Office 365
- Microsoft Office 365 Exchange Online
- Microsoft Office 365 SharePoint Online (include OneDrive for Business)


## <a name="enable-access-rules"></a>Abilitare le regole di accesso

Ogni regola può essere abilitata o disabilitata in una per ogni applicazione basi. Quando le regole possono **via** sono verrà attivate e applicate per gli utenti che accedono all'applicazione. Quando sono **OFF** che non verranno utilizzate e non influisce l'esperienza di accesso agli utenti.

## <a name="applying-rules-to-specific-users"></a>Applicazione di regole a utenti specifici
Le regole possono essere applicate a specifici gruppi di utenti in base al gruppo di sicurezza mediante l'impostazione **Applica a**. **Applica a** è possibile impostare per **Tutti gli utenti** o **gruppi**. Quando impostata per **Tutti gli utenti** verranno applicate le regole per tutti gli utenti con l'accesso all'applicazione. L'opzione **gruppi** consente di protezione specifici e i gruppi di distribuzione da selezionare regole verranno applicate solo per i gruppi.

Quando si distribuisce una regola, è comune per applicare un limitato insieme di utenti che appartengono a un gruppo pilota. Una volta completata la regola può essere applicata a **Tutti gli utenti**. In questo modo la regola dovrà essere applicata per tutti gli utenti nell'organizzazione.

Gruppi di selezionare anche possono essere sottratti criteri utilizzando l'opzione **eccetto** . Saranno esclusi anche tutti i membri dei gruppi, anche se vengono visualizzati in un gruppo incluso.

## <a name="at-work-networks"></a>"In ufficio" reti


Le regole di accesso condizionale che usano una rete "in ufficio", si basano su attendibili intervalli di indirizzi IP che sono stati configurati in Azure Active Directory, oppure utilizzare del credito "all'interno di corpnet" da ADFS. Queste regole includono:

- Richiede l'autenticazione a più fattori quando non al lavoro
- Bloccare l'accesso quando non al lavoro

Opzioni per si specifica "in ufficio" reti

1. Configurare gli intervalli di indirizzi IP attendibili nella [pagina di configurazione di autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication-whats-next.md). Criterio di accesso condizionale utilizzerà intervalli configurati a ogni richiesta di autenticazione e rilascio token da valutare le regole. 
2. Utilizzo di all'interno di configurare corpnet richiedere, questa opzione può essere utilizzata con directory federata, tramite ADFS. [Altre informazioni all'interno delle attestazioni coronet](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).
3. Configurare gli intervalli di indirizzi IP pubblici. Nella scheda configurazione per la directory, è possibile impostare gli indirizzi IP pubblici. Accesso condizionato utilizzerà questi come 'LIN' indirizzi IP, in questo modo intervalli aggiuntivi da configurare, oltre il limite di indirizzi IP 50 che viene applicato dalla pagina di impostazione MFA.



## <a name="rules-based-on-application-sensitivity"></a>Regole basate su riservatezza applicazione

Le regole vengono configurate per ogni applicazione consentendo ai servizi di valore elevato da proteggere senza influire sull'accesso ad altri servizi. Le regole di accesso condizionale possono essere configurate nella scheda **configurazione** dell'applicazione. 

Regole attualmente offerte:

- **Richiede l'autenticazione a più fattori**
 - Tutti gli utenti che il criterio viene applicato a saranno necessari per l'autenticazione tramite autenticazione a più fattori almeno una volta.
 
- **Richiede l'autenticazione a più fattori quando non al lavoro**
 - Se il criterio viene applicato, tutti gli utenti saranno necessari per avere eseguito l'autenticazione a più fattori almeno una volta se sono accedere al servizio da una posizione remota non lavorativo. Se si muova da un ufficio in posizione remota, vengono verrà richiesto di eseguire l'autenticazione per accedere al servizio.
 
- **Bloccare l'accesso quando non al lavoro** 
 - Quando gli utenti spostano dal lavoro a una posizione remota, verrà bloccate se è applicato il criterio "Bloccare l'accesso quando non al lavoro".  Vengono nuovamente potrà accedere quando si trova in un luogo di lavoro.


## <a name="related-topics"></a>Argomenti correlati

- [Protezione dell'accesso a Office 365 e altre App connessi a Azure Active Directory](active-directory-conditional-access.md)
- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
