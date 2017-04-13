<properties
    pageTitle="Protezione dei privilegi di accesso in Azure Active Directory | Microsoft Azure"
    description="Un argomento che illustra gli approcci per la protezione dei privilegi di accesso in Azure, Azure Active Directory e Microsoft Online Services."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="mwahl"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="kgremban"/>


# <a name="securing-privileged-access-in-azure-ad"></a>Protezione dei privilegi di accesso in Azure Active Directory

Protezione dei privilegi di accesso è critico primo passaggio per proteggere i beni aziendali in un'organizzazione moderna. Account con privilegi sono quelli che amministrare e gestire sistemi IT. Gli utenti malintenzionati informatica destinazione questi account per accedere ai dati e sistemi di un'organizzazione. Per proteggere i privilegi di accesso, è necessario isolare gli account e i sistemi da rischiare vengano esposti a un utente non autorizzato.

Altri utenti iniziano a privilegi per accedere a servizi cloud. Può trattarsi di amministratori globali di Office 365, gli amministratori di abbonamento Azure e gli utenti che hanno accesso amministrativo in macchine virtuali o sulle applicazioni SaaS.

Si consiglia di che seguire questa Guida di orientamento [Protezione dell'accesso ai privilegi](https://technet.microsoft.com/library/mt631194.aspx).

Se si utilizza Azure Active Directory per gestire l'accesso a Azure, Office 365, o altri servizi e applicazioni, questi principi applicano agli account utente sono gestiti e autenticati da Active Directory o di Azure Active Directory. Nelle sezioni seguenti vengono forniscono ulteriori informazioni sulle funzionalità di Azure Active Directory per supportare l'accesso con privilegi di protezione.

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori

Per aumentare la protezione dell'amministratore autenticazione, è necessario che l'autenticazione a più fattori (MFA) prima di concedere privilegi. MFA è un metodo di verifica dell'identità dell'utente che richiede l'uso di più solo un nome utente e la password. Fornisce un secondo livello di sicurezza per gli accessi utente e le transazioni.

Azure autenticazione a più fattori consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Offre autenticazione tramite un intervallo delle opzioni di verifica facile inclusi chiamate telefoniche, messaggi di testo, le notifiche di app per dispositivi mobili, o codice di verifica e 3 ° token GIURAMENTO di terze parti.

Per una panoramica del funzionamento di autenticazione a più fattori Azure vedere il video seguente.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]

Per ulteriori informazioni, vedere [MFA per Office 365 e MFA per Azure](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/).

## <a name="time-bound-privileges"></a>Limite di tempo diverso privilegi

Potrebbero risultare alcune organizzazioni che hanno Troppi utenti ruoli privilegi elevati. Un utente potrebbe essere state aggiunte al ruolo per una particolare attività, ad esempio per accedere a un servizio, ma non sono stati utilizzati i privilegi di frequente in un secondo momento.

Per ridurre il tempo di esposizione dei privilegi e aumentare la visibilità in uso, limitare gli utenti a solo in modo semplice i privilegi in tempo (Just) solo quando è necessario eseguire un'attività. Per Azure Active Directory e Microsoft Online Services, è possibile utilizzare [la gestione delle identità privilegi di Azure Active Directory (PIM)](http://aka.ms/AzurePIM).


![Dashboard PIM][2]


## <a name="attack-detection"></a>Rilevamento degli attacchi

[Protezione di Azure Active Directory dell'identità](../active-directory-identityprotection.md) fornisce una visualizzazione consolidata di eventi di rischio e potenziali vulnerabilità che interessano identità dell'organizzazione. In base a eventi di rischio, la protezione dell'identità calcola un livello di rischio utente per ogni utente, che consente di configurare i criteri basati sui rischi per proteggere automaticamente l'identità dell'organizzazione. Questi criteri, insieme altri controlli di accesso condizionato forniti da Azure Active Directory e questa funzionalità, possono essere automaticamente impedire che l'utente oppure suggerimenti che includono la reimpostazione della password e l'applicazione di autenticazione a più fattori.

![Protezione dell'identità di Azure Active Directory][3]

## <a name="conditional-access"></a>Accesso condizionato

Controllo di accesso condizionato, Azure Active Directory controlla le condizioni specifiche scelto per l'autenticazione di un utente, prima di consentire l'accesso a un'applicazione. Quando sono soddisfatte le condizioni, l'utente viene autenticato e autorizzato ad accedere all'applicazione.


![Impostazione delle regole di accesso condizionato con MFA][4]


## <a name="related-articles"></a>Articoli correlati

- Abilitare [l'autenticazione a più fattori Azure](../../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)
- Attivare [la gestione delle identità privilegi di Azure Active Directory](../active-directory-privileged-identity-management-configure.md)
- Abilitare [la protezione dell'identità di Azure Active Directory](../active-directory-identityprotection.md)
- Abilitare [i controlli di accesso condizionato](../active-directory-conditional-access.md)


Per ulteriori informazioni sulla creazione di una Guida di orientamento alla sicurezza completa, vedere la sezione "responsabilità per il cliente e Guida di orientamento" del documento di [Microsoft Cloud Security per i progettisti dell'organizzazione](http://aka.ms/securecustomer) . Per ulteriori informazioni su svolgano servizi Microsoft per assistenza con uno di questi argomenti, contattare il rappresentante Microsoft o visitare la [pagina soluzioni alla sicurezza informatica](https://www.microsoft.com/microsoftservices/campaigns/cybersecurity-protection.aspx).

<!--Image references-->
[1]: ../media/active-directory-privileged-identity-management-configure/Search_PIM.png
[2]: ../media/active-directory-privileged-identity-management-configure/PIM_Dash.png
[3]: ../media/active-directory-identityprotection/29.png
[4]: ../media/active-directory-conditional-access/conditionalaccess-saas-apps.png
