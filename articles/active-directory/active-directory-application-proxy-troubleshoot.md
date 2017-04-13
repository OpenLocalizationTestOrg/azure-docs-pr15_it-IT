<properties
    pageTitle="Risolvere i problemi di Proxy applicazione | Microsoft Azure"
    description="Viene illustrato come risolvere gli errori di Azure Active Directory applicazione Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="kgremban"/>



# <a name="troubleshoot-application-proxy"></a>Risolvere i problemi di Proxy di applicazione

Se si verificano errori di accesso a un'applicazione pubblicata o nelle applicazioni di pubblicazione, verificare le opzioni seguenti per visualizzare il Proxy di applicazione di Microsoft Azure Active Directory funzioni correttamente:

- Aprire la console di Windows Services e verificare che sia attivato il servizio **Microsoft AAD applicazione Proxy Connector** e in esecuzione. È anche consigliabile esaminare la pagina di proprietà Proxy di applicazione del servizio, come illustrato nell'immagine seguente:  
  ![Schermata della finestra proprietà di connettore Proxy di applicazione di Microsoft AAD](./media/active-directory-application-proxy-troubleshoot/connectorproperties.png)

- Aprire il Visualizzatore eventi e cercare gli eventi di connettore Proxy dell'applicazione in **registri applicazioni e servizi** > **Microsoft** > **AadApplicationProxy** > **connettore** > **amministratore**.
- Se necessario, sono disponibili registri più dettagliati attivando analitica debug log e attivare il Registro di sessione connettore Proxy dell'applicazione.

## <a name="the-page-is-not-rendered-correctly"></a>La pagina non viene eseguita correttamente

Se non viene visualizzato un messaggio di errore specifico, potrebbe essere ancora problemi con l'applicazione di rendering o funziona in modo non corretto. Questo può verificarsi se è stato pubblicato il percorso di articolo, ma l'applicazione richiede il contenuto presente all'esterno di tale percorso.

Ad esempio, se si pubblica https://yourapp/app percorso ma l'applicazione chiama immagini in https://yourapp/media, sia non è possibile eseguirne il rendering. Verificare che si pubblica l'applicazione utilizzando il percorso di livello più alto che è necessario includere tutto il contenuto pertinente. In questo esempio, potrebbe essere http://yourapp/.

Se si cambia il percorso per includere il contenuto di cui si fa riferimento, ma è comunque necessario che gli utenti su un collegamento più approfondito nel percorso, vedere il blog [impostando il collegamento appropriato per le applicazioni Proxy applicazioni nel Pannello di accesso di Azure Active Directory e avvio delle app di Office 365](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="general-errors"></a>Errori generali

Errore | Descrizione | Risoluzione
--- | --- | ---
Impossibile accedere a questa applicazione aziendale. Non è autorizzato per accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi che assegnare all'utente con accesso a questa applicazione. | Potrebbe non assegnate all'utente dell'applicazione. | Passare alla scheda **applicazione** e nella sezione **utenti e gruppi**, assegnare l'utente o gruppo di utenti a questa applicazione.
Impossibile accedere a questa applicazione aziendale. Non è autorizzato per accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi che l'utente ha una licenza per Azure Active Directory Premium o di base. | Gli utenti che venga visualizzato questo errore quando si tenta di accedere all'app che è stato pubblicato se non sono state assegnate in modo esplicito con una licenza Premium/Basic dall'amministratore del server di sottoscrizione. | Passare alla scheda Active Directory **licenze** del sottoscrittore e assicurarsi che l'utente o gruppo di utenti sia assegnato una Premium o una licenza di base.


## <a name="connector-troubleshooting"></a>Risoluzione dei problemi di connessione
Se la registrazione non riesce durante l'installazione guidata di connettore, esistono due modi per visualizzare il motivo dell'errore. Controllare il registro eventi in **applicazioni e servizi Logs\Microsoft\AadApplicationProxy\Connector\Admin**o eseguire il seguente comando di Windows PowerShell.

    Get-EventLog application –source “Microsoft AAD Application Proxy Connector” –EntryType “Error” –Newest 1

| Errore | Descrizione | Risoluzione |
| --- | --- | --- |
| Registrazione di connettore non riuscita: verificare che è stata attivata Proxy di applicazione nel portale di gestione di Azure e immesso correttamente il nome utente di Active Directory e la password. Errore: 'uno o più errori." | Finestra di registrazione è chiuso senza eseguire l'accesso a Azure Active Directory. | Eseguire la procedura guidata connettore nuovamente e registrare il connettore. |
| Registrazione di connettore non riuscita: verificare che è stata attivata Proxy di applicazione nel portale di gestione di Azure e immesso correttamente il nome utente di Active Directory e la password. Errore: "AADSTS50001: risorse `https://proxy.cloudwebappproxy.net /registerapp` viene disabilitato." | Proxy di applicazione viene disabilitato.  | Verificare che si attiva Proxy di applicazione nel portale di classica Azure prima di tentare di registrare il connettore. Per ulteriori informazioni su come abilitare Proxy di applicazione, vedere [abilitare Proxy applicazione Servizi](active-directory-application-proxy-enable.md). |
| Registrazione di connettore non riuscita: verificare che è stata attivata Proxy di applicazione nel portale di gestione di Azure e immesso correttamente il nome utente di Active Directory e la password. Errore: 'uno o più errori." | Se la finestra di registrazione viene aperto e quindi si chiude immediatamente senza consentire agli utenti di accedere, si otterrà probabilmente questo errore. Questo errore si verifica quando si verifica un errore di rete nel sistema. | Assicurarsi che sia possibile connettersi da un browser a un sito Web pubblico e che le porte siano aperte come specificato nei [Prerequisiti Proxy dell'applicazione](active-directory-application-proxy-enable.md). |
| Registrazione di connettore non riuscita: verificare che il computer sia connesso a Internet. Errore: "Nessun endpoint in ascolto su `https://connector.msappproxy.net :9090/register/RegisterConnector` che può accettare il messaggio. Questo è spesso causato da un indirizzo non corretti o azione SOAP. Se presente, per informazioni dettagliate, vedere InnerException,. " | Se si ad accedere con il nome utente di Azure Active Directory e la password, ma questo errore, è possibile che tutte le porte sopra 8081 vengono bloccate. | Assicurarsi che le porte necessarie siano aperte. Per ulteriori informazioni, vedere [Prerequisiti Proxy dell'applicazione](active-directory-application-proxy-enable.md). |
| Cancella ' ultimo viene visualizzato nella finestra di registrazione. Non è possibile procedere – solo per chiudere la finestra. | È stata immessa la password o il nome utente errato. | Riprova. |
| Registrazione di connettore non riuscita: verificare che è stata attivata Proxy di applicazione nel portale di gestione di Azure e immesso correttamente il nome utente di Active Directory e la password. Errore: "AADSTS50059: nessuna informazione che identifica tenant trovato in una richiesta di o implicita tramite le credenziali fornite alla ricerca principio servizio URI non è riuscita. | Si sta tentando di accedere usando un Account Microsoft e non un dominio che fa parte di ID organizzazione della directory di cui che si sta tentando di accedere. | Assicurarsi che l'amministratore fa parte dello stesso nome di dominio al dominio tenant, ad esempio, se il dominio di Azure Active Directory è contoso.com, l'amministratore deve essere admin@contoso.com. |
| Non è possibile recuperare i criteri di esecuzione per l'esecuzione di script di PowerShell. | Se il codice Product Key non funziona, verificare che il criterio di esecuzione di PowerShell non sia disattivato. | Aprire l'Editor criteri di gruppo. Passare a **Configurazione Computer** > **Modelli amministrativi** > **Componenti di Windows** > **Windows PowerShell** e fare doppio clic su **Attiva esecuzione di Script**. Impostare su **Non configurato** o **attivato**. Se impostato su **attivato**, assicurarsi che in Opzioni, il criterio di esecuzione è impostato su uno **script locale Consenti e remote con segno** o per **consentire a tutti gli script**. |
| Connettore non è possibile scaricare la configurazione. | Certificato client del connettore, che viene utilizzato per l'autenticazione, scaduto. Può verificarsi se si dispone di Connector installato dietro un proxy. In questo caso, il connettore non è possibile accedere a Internet e non saranno in grado di fornire applicazioni agli utenti remoti. | Rinnovare protezione manualmente utilizzando il `Register-AppProxyConnector` cmdlet di Windows PowerShell. Se il connettore dietro un proxy, è necessario concedere l'accesso a Internet per gli account di connettore "servizi di rete" e "sistema locale". Può essere eseguita concedendo loro l'accesso al Proxy o mediante l'impostazione di ignorare il proxy. |
| Registrazione di connettore non riuscita: accertarsi di essere un amministratore globale in Active Directory per registrare il connettore. Errore: "la richiesta di registrazione negata." | L'alias che si sta tentando di accedere con non è un amministratore in questo dominio. Il connettore viene sempre installato per la directory che possiede il dominio dell'utente. | Assicurarsi che l'amministratore si sta tentando di accedere disponga delle autorizzazioni globale nel tenant di Azure Active Directory.|


## <a name="kerberos-errors"></a>Errori di Kerberos

| Errore | Descrizione | Risoluzione |
| --- | --- | --- |
| Non è possibile recuperare i criteri di esecuzione per l'esecuzione di script di PowerShell. | Se il codice Product Key non funziona, verificare che il criterio di esecuzione di PowerShell non sia disattivato. | Aprire l'Editor criteri di gruppo. Passare a **Configurazione Computer** > **Modelli amministrativi** > **Componenti di Windows** > **Windows PowerShell** e fare doppio clic su **Attiva esecuzione di Script**. Impostare su **Non configurato** o **attivato**. Se impostato su **attivato**, assicurarsi che in Opzioni, il criterio di esecuzione è impostato su uno **script locale Consenti e remote con segno** o per **consentire a tutti gli script**. |
| 12008 - azure Active Directory ha superato il numero massimo di tentativi di autenticazione Kerberos consentiti per il server di back-end. | Questo evento può indicare una configurazione non corretta tra Azure Active Directory e il server di back-end applicazione o un problema di configurazione di data e l'ora su entrambi i computer. | Il server di back-end rifiutato ticket Kerberos creati da Azure Active Directory. Verificare che Azure AD e il server di back-end applicazioni siano configurati correttamente. Assicurarsi che la configurazione di data e l'ora in Azure Active Directory e il server di applicazioni back-end vengono sincronizzati. |
| 13016 - azure Active Directory non è possibile recuperare un ticket Kerberos per conto dell'utente poiché non esiste alcun UPN nel token di bordo o in cookie access. | Si è verificato un problema con la configurazione di servizio token di sicurezza. | Correggere la configurazione di attestazione UPN nel servizio token di sicurezza. |
| 13019 - azure Active Directory non è possibile recuperare un ticket Kerberos per conto dell'utente a causa dell'errore API generale. | Questo evento può indicare una configurazione non corretta tra Azure Active Directory e server controller di dominio o un problema di configurazione di data e l'ora su entrambi i computer. | Controller di dominio rifiutato ticket Kerberos creati da Azure Active Directory. Verificare che Azure AD e il server di back-end dell'applicazione non sono configurati correttamente, in particolare la configurazione di SPN. Verificare che sia di Azure AD dominio allo stesso dominio controller di dominio da verificare che il controller di dominio stabilisce trust con Azure Active Directory. Assicurarsi che la configurazione di data e l'ora in Azure Active Directory e vengono sincronizzati controller di dominio. |
| 13020 - azure Active Directory non è possibile recuperare un ticket Kerberos per conto dell'utente perché il server di back-end SPN non è definito. | Questo evento può indicare una configurazione non corretta tra Azure Active Directory e server controller di dominio o un problema di configurazione di data e l'ora su entrambi i computer. | Controller di dominio rifiutato ticket Kerberos creati da Azure Active Directory. Verificare che Azure AD e il server di back-end dell'applicazione non sono configurati correttamente, in particolare la configurazione di SPN. Verificare che sia di Azure AD dominio allo stesso dominio controller di dominio da verificare che il controller di dominio stabilisce trust con Azure Active Directory. Assicurarsi che la configurazione di data e l'ora in Azure Active Directory e vengono sincronizzati controller di dominio. |
| 13022 - azure Active Directory non è possibile autenticare l'utente perché il server di back-end risponde a tentativi di autenticazione Kerberos con un errore HTTP 401. | Questo evento può indicare una configurazione non corretta tra Azure Active Directory e il server di back-end applicazione o un problema di configurazione di data e l'ora su entrambi i computer. | Il server di back-end rifiutato ticket Kerberos creati da Azure Active Directory. Verificare che Azure AD e il server di back-end applicazioni siano configurati correttamente. Assicurarsi che la configurazione di data e l'ora in Azure Active Directory e il server di applicazioni back-end vengono sincronizzati. |
| Il sito Web non è possibile visualizzare la pagina. | L'utente che venga visualizzato questo errore quando si tenta di accedere all'app che è stato pubblicato se l'applicazione è un'applicazione integrata di Windows. L'entità servizio definito per questa applicazione non sia corretto. | Per le app integrata di Windows: verificare che l'entità servizio configurato per l'applicazione sia corretto. |
| Il sito Web non è possibile visualizzare la pagina. | L'utente che venga visualizzato questo errore quando si tenta di accedere all'app che è stato pubblicato se l'applicazione è un'applicazione di OWA. Questo errore può essere dovuto da una delle operazioni seguenti: <br> -L'entità servizio definito per questa applicazione non è corretto. <br> -L'utente che ha tentato di accedere all'applicazione utilizza un account Microsoft piuttosto che l'account aziendale appropriato per l'accesso o l'utente è un utente guest. <br> -L'utente che ha tentato di accedere all'applicazione non è corretto definito per questa applicazione sul lato locale. | La procedura per ridurre a seconda dei casi: <br> -Verificare che l'entità servizio configurato per l'applicazione sia corretto. <br> -Assicurarsi che l'utente accede con loro account aziendali che corrisponde al dominio dell'applicazione pubblicato. Guest e i relativi utenti Account Microsoft non è possibile accedere a applicazioni integrata di Windows. <br> -Verificare che l'opzione che l'utente dispone delle autorizzazioni appropriate come definito per questa applicazione back-end nel computer locale. |
| Impossibile accedere a questa applicazione aziendale. Non è autorizzato per accedere a questa applicazione. Autorizzazione non riuscita. Assicurarsi che assegnare all'utente con accesso a questa applicazione. | Gli utenti che venga visualizzato questo errore quando si tenta di accedere all'app che è stato pubblicato se utilizzano account di Microsoft anziché il proprio account aziendale di accedere. Questo errore possono verificarsi anche da utenti guest. | Guest e gli utenti di Account Microsoft non è possibile accedere a applicazioni integrata di Windows. Verificare che l'utente accede con loro account aziendali che corrisponde al dominio dell'applicazione pubblicato. |
| Non è possibile accedere immediatamente questa applicazione aziendale. Provare più tardi... Il connettore timeout. | Gli utenti che venga visualizzato questo errore quando si tenta di accedere all'app che è stato pubblicato se non sono definiti in modo corretto per l'applicazione sul lato locale. | Assicurarsi che gli utenti abbiano le autorizzazioni appropriate, come definito per questa applicazione back-end nel computer locale. |


## <a name="see-also"></a>Vedere anche

- [Abilitare Proxy di applicazione per Azure Active Directory](active-directory-application-proxy-enable.md)
- [Pubblicare applicazioni con Proxy dell'applicazione](active-directory-application-proxy-publish.md)
- [Attivare single sign-on](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)


<!--Image references-->
[1]: ./media/active-directory-application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
