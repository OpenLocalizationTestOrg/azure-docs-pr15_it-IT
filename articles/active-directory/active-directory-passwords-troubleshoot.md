<properties
    pageTitle="Risoluzione dei problemi: Gestione di Azure Active Directory Password | Microsoft Azure"
    description="Risoluzione dei problemi comuni i passaggi per la gestione di Azure Active Directory Password, tra cui Ripristina, modifica, writeback, registrazione, e le informazioni da includere durante la ricerca per assistenza."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="asteen"/>

# <a name="how-to-troubleshoot-password-management"></a>Come risolvere i problemi di gestione delle Password

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Se si sono verificati problemi con la gestione delle Password, siamo qui utili. La maggior parte dei problemi che possono verificarsi possano essere risolti con pochi semplici suggerimenti sulla risoluzione dei problemi che è possibile leggere sulle sotto a risolvere i problemi di distribuzione:

* [**Informazioni da includere quando è necessaria assistenza**](#information-to-include-when-you-need-help)
* [**Problemi relativi alla configurazione di gestione delle Password nel portale di gestione di Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problemi con i report di gestione Password nel portale di gestione di Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problemi con la Password reimpostare portale di registrazione**](#troubleshoot-the-password-reset-registration-portal)
* [**Problemi con la Password reimpostare portale**](#troubleshoot-the-password-reset-portal)
* [**Problemi con Writeback di Password**](#troubleshoot-password-writeback)
  - [Codici di errore di password Writeback registro eventi](#password-writeback-event-log-error-codes)
  - [Problemi con le connessioni Writeback di Password](#troubleshoot-password-writeback-connectivity)

Se si è provate diverse soluzioni di seguito alla risoluzione dei problemi e funzionano ancora problemi, è possibile pubblicare una domanda nel [forum di Azure Active Directory](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) o contattare il supporto tecnico ed è necessario esaminare il problema come è possibile.

## <a name="information-to-include-when-you-need-help"></a>Informazioni da includere quando è necessaria assistenza

Se non è possibile risolvere il problema con le indicazioni seguenti, è possibile contattare i personale del supporto tecnico. Quando si contattarla, è consigliabile includere le seguenti informazioni:

 - **Descrizione generale dell'errore** : il messaggio di errore ha l'utente vedere?  Se si è non verificato nessun messaggio di errore, viene descritto il comportamento imprevisto notato, in modo dettagliato.
 - **Pagina** – quali pagina presenti quando si è visto l'errore (includere l'URL)?
 - **Data / ora / fuso orario** : che cos'è stata preciso data e ora è visualizzato l'errore (includere il fuso orario)?
 - **Codice di supporto tecnico** : qual è il codice di supporto generato quando l'utente visto l'errore (per trovare e riprodurre l'errore, quindi fare clic sul collegamento supportano codice nella parte inferiore dello schermo e inviare del supporto tecnico GUID risultante).
   - Se si è in una pagina senza un codice di supporto nella parte inferiore, premere F12 e cercare SID e CID e inviare i due risultati del supporto tecnico.

    ![][001]

 - **ID utente** : qual è l'ID dell'utente che ha visto (ad esempio, l'erroreuser@contoso.com)?
 - **Informazioni sull'utente** : è stata l'utente federato, hash password sincronizzato, solo cloud?  È stato all'utente di avere una licenza di Premium AAD o AAD base assegnata?
 - **Registro eventi applicazioni** : se si utilizza il Writeback di Password e l'errore è all'interno dell'infrastruttura locale, vedere zip backup del registro eventi dell'applicazione dal server Azure AD Connect e inviare insieme la richiesta.

Incluse queste informazioni consente di risolvere il problema al più presto.


## <a name="troubleshoot-password-reset-configuration-in-the-azure-management-portal"></a>Risolvere i problemi di configurazione di reimpostare la password nel portale di gestione di Azure
Se si verifica un errore durante la reimpostazione della password di configurazione, potrebbe essere possibile risolvere il problema seguendo la procedura di risoluzione dei problemi seguente:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Errore di maiuscole/minuscole</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Determinare l'errore visualizzare un utente?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è visualizzato la sezione <strong>Criteri di reimpostazione Password utente </strong>nella scheda <strong>Configura</strong> nel portale di gestione di Azure</p>
            </td>
            <td>
              <p>La sezione <strong>Criteri di reimpostazione Password l'utente </strong>non è visibile nella scheda <strong>Configura</strong> nel portale di gestione di Azure.</p>
            </td>
            <td>
              <p>Questo può verificarsi se non si dispone di una licenza Premium AAD o AAD base assegnata all'interfaccia di amministrazione eseguire questa operazione. </p>
              <p>Per risolvere il problema, assegnare una licenza Premium AAD o AAD base per l'account dell'amministratore in questione, passare alla scheda <strong>licenze</strong> e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è visualizzato una delle opzioni di configurazione nella sezione <strong>Criteri di reimpostazione Password utente</strong> quello descritto nella documentazione.</p>
            </td>
            <td>
              <p>La sezione <strong>Criteri di reimpostazione Password l'utente </strong>è visibile, ma l'unico flag di sotto di esso sia il contrassegno <strong>Gli utenti abilitati per la reimpostazione della Password</strong> .</p>
            </td>
            <td>
              <p>Il resto dell'interfaccia utente verrà visualizzato quando si passa il flag <strong>Gli utenti abilitati per la reimpostazione della Password</strong> per <strong>Sì.</strong></p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è visualizzato un'opzione di configurazione specifico.</p>
            </td>
            <td>
              <p>Ad esempio, non visualizzato l'opzione <strong>numero di giorni prima che un utente deve confermare ai contatti</strong> quando scorre la sezione <strong>Criteri di reimpostazione Password utente</strong> (o altri esempi dello stesso problema).</p>
            </td>
            <td>
              <p>Numero di elementi dell'interfaccia utente è nascoste finché sono necessari. Provare ad attivare tutte le opzioni nella pagina se si desidera visualizzare.</p>
              <p>Per ulteriori informazioni su tutti i controlli disponibili, vedere <a href="active-directory-passwords-customize.md#password-management-behavior">il comportamento di gestione delle Password</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L'opzione di configurazione <strong>Scrivere password nella distribuzione locale</strong> non è presente.</p>
            </td>
            <td>
              <p>L'opzione <strong>Scrivere password nella distribuzione locale</strong> non è visibile nella scheda <strong>Configura</strong> nel portale di gestione di Azure</p>
            </td>
            <td>
              <p>Questa opzione è visibile solo se è stato scaricato Azure AD Connect e configurato Writeback di Password. Quando è stato fatto, tale opzione verrà visualizzata e consente di abilitare o disabilitare writeback dal cloud.</p>
              <p>Per ulteriori informazioni su come eseguire questa operazione, vedere <a href="active-directory-passwords-getting-started.md#step-2-enable-password-writeback-in-azure-ad-connect">Abilitare Writeback di Password in Azure AD Connect</a> .</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-management-reports-in-the-azure-management-portal"></a>Risolvere i problemi di report di gestione delle password nel portale di gestione di Azure
Se si verifica un errore quando si utilizzano i report di gestione delle password, potrebbe essere possibile risolvere il problema seguendo la procedura di risoluzione dei problemi seguente:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Errore di maiuscole/minuscole</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Determinare l'errore visualizzare un utente?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile vedere i report di gestione password</p>
            </td>
            <td>
              <p>I report di <strong>reimpostazione della Password attività</strong> e <strong>attività di registrazione la reimpostazione della Password</strong> non sono visibili nei report del <strong>Log di attività</strong> nella scheda <strong>report</strong> .</p>
            </td>
            <td>
              <p>Questo può verificarsi se non si dispone di una licenza Premium AAD o AAD base assegnata all'interfaccia di amministrazione eseguire questa operazione. </p>
              <p>Per risolvere il problema, assegnare una licenza Premium AAD o AAD base per l'account dell'amministratore in questione, passare alla scheda <strong>licenze</strong> e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le registrazioni utente mostrano più volte</p>
            </td>
            <td>
              <p>Quando un utente registra posta elettronica alternativo, tramite cellulare e domande sulla protezione, ognuna di esse visualizzati come righe separate anziché una sola riga.</p>
            </td>
            <td>
              <p>Attualmente, quando si registra un utente, è Impossibile presuppongono che verranno registrati tutti gli elementi presenti nella pagina di registrazione. Di conseguenza, è attualmente accedere ogni dato singoli registrato come un evento separato.</p>
              <p>Se si desidera aggregare i dati, è possibile scaricare il report e aprire i dati come una tabella pivot in excel per una maggiore flessibilità.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Risoluzione dei problemi del portale di registrazione di reimpostazione password
Se si verifica un errore durante la registrazione di un utente per la reimpostazione della password, potrebbe essere possibile risolvere il problema seguendo la procedura di risoluzione dei problemi seguente:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Errore di maiuscole/minuscole</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Determinare l'errore visualizzare un utente?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory non è abilitato per la reimpostazione della password</p>
            </td>
            <td>
              <p>L'amministratore ha attivato non è possibile utilizzare questa caratteristica.</p>
            </td>
            <td>
              <p>Passare il flag <strong>Gli utenti abilitati per la reimpostazione della Password</strong> su <strong>Sì</strong> e premere <strong>Salva</strong> nella scheda Configurazione directory portale di gestione Azure. È necessario disporre di un premio di Azure Active Directory o base licenza assegnata all'interfaccia di amministrazione eseguire questa operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente non è assegnata una licenza Premium AAD o AAD base</p>
            </td>
            <td>
              <p>L'amministratore ha attivato non è possibile utilizzare questa caratteristica.</p>
            </td>
            <td>
              <p>Assegnare una licenza di Azure Active Directory Premium o di Azure Active Directory base per l'utente nella scheda <strong>licenze</strong> nel portale di gestione di Azure. È necessario disporre di un premio di Azure Active Directory o base licenza assegnata all'interfaccia di amministrazione eseguire questa operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore durante l'elaborazione richiesta</p>
            </td>
            <td>
              <p>Utente viene visualizzato un messaggio di errore indicante:</p>
              <p>

              </p>
              <p>Errore durante l'elaborazione richiesta </p>
              <p>Quando si tenta di reimpostare una password.</p>
            </td>
            <td>
              <p>Questo errore può essere dovuto a molti problemi, ma in genere questo errore è causato da uno dei due un servizio interruzione del problema di configurazione o che non può essere risolto. </p>
              <p>Se viene visualizzato questo messaggio di errore e impatto l'azienda, contattare il supporto e ti aiuterà ASAP. Visualizzare le <a href="#information-to-include-when-you-need-help">informazioni da includere quando è necessaria assistenza</a> per vedere che cos'è necessario fornire al supporto tecnico per facilitare una risoluzione.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-the-password-reset-portal"></a>Risoluzione dei problemi del portale di reimpostazione password
Se si verifica un errore per la reimpostazione password per un utente, potrebbe essere possibile risolvere il problema seguendo la procedura di risoluzione dei problemi seguente:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Errore di maiuscole/minuscole</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Determinare l'errore visualizzare un utente?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory non è abilitato per la reimpostazione della password</p>
            </td>
            <td>
              <p>L'account non è abilitato per la reimpostazione della password</p>
              <p>Ci scusiamo, ma l'amministratore non ha configurato l'account da utilizzare con questo servizio. </p>
              <p>

              </p>
              <p>Se si desidera, possiamo contattare un amministratore dell'organizzazione per reimpostare la password dell'utente.</p>
            </td>
            <td>
              <p>Passare il flag <strong>Gli utenti abilitati per la reimpostazione della Password</strong> su <strong>Sì</strong> e premere <strong>Salva</strong> nella scheda Configurazione directory portale di gestione Azure. È necessario disporre di un premio di Azure Active Directory o base licenza assegnata all'interfaccia di amministrazione eseguire questa operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente non è assegnata una licenza Premium AAD o AAD base</p>
            </td>
            <td>
              <p>Mentre è Impossibile reimpostare le password degli account di amministratore non automaticamente, possiamo contattare amministratore dell'organizzazione per svolgere il lavoro.</p>
            </td>
            <td>
              <p>Assegnare una licenza di Azure Active Directory Premium o di Azure Active Directory base per l'utente nella scheda <strong>licenze</strong> nel portale di gestione di Azure. È necessario disporre di un premio di Azure Active Directory o base licenza assegnata all'interfaccia di amministrazione eseguire questa operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory è abilitata per la reimpostazione della password, ma l'utente ha mancanti o non corretto ha le informazioni di autenticazione</p>
            </td>
            <td>
              <p>L'account non è abilitato per la reimpostazione della password</p>
              <p>Ci scusiamo, ma l'amministratore non ha configurato l'account da utilizzare con questo servizio. </p>
              <p>

              </p>
              <p>Se si desidera, possiamo contattare un amministratore dell'organizzazione per reimpostare la password dell'utente.</p>
            </td>
            <td>
              <p>Assicurarsi che l'utente è nel formato corretto dati relativi ai contatti sul file nella directory prima di procedere. Per informazioni su come configurare le informazioni di autenticazione nella directory in modo che gli utenti non viene visualizzato questo errore, vedere <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quali dati vengono utilizzati per la reimpostazione della password</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory è abilitata per la reimpostazione della password, ma un utente ha solo una parte di dati relativi ai contatti nel file quando il criterio è impostato per richiedere due passaggi di verifica</p>
            </td>
            <td>
              <p>L'account non è abilitato per la reimpostazione della password</p>
              <p>Ci scusiamo, ma l'amministratore non ha configurato l'account da utilizzare con questo servizio. </p>
              <p>

              </p>
              <p>Se si desidera, possiamo contattare un amministratore dell'organizzazione per reimpostare la password dell'utente.</p>
            </td>
            <td>
              <p>Prima di procedere, assicurarsi che l'utente abbia almeno due metodi contatti configurati correttamente (ad esempio, cellulare e telefono ufficio). Per informazioni su come configurare le informazioni di autenticazione nella directory in modo che gli utenti non viene visualizzato questo errore, vedere <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quali dati vengono utilizzati per la reimpostazione della password</a> .</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Directory è abilitata per la reimpostazione della password e utente sia configurato correttamente ma utenti non potranno essere contattati </p>
            </td>
            <td>
              <p>E poi?  Si è verificato un errore imprevisto durante contattare l'utente.</p>
            </td>
            <td>
              <p>Il risultato di un errore di servizio temporaneo può trattarsi o non configurati correttamente dati dei contatti che non è possibile rilevare correttamente. Se l'utente deve attendere 10 secondi, provare nuovamente e viene visualizzato "contattare l'amministratore" collegamento. Fare nuovamente clic provare nuovamente invierà la chiamata, mentre facendo clic su "contattare l'amministratore" verrà inviato un messaggio di posta elettronica modulo per utente, password o gli amministratori globali (in tale ordine di precedenza) richiedere una password Reimposta da eseguire per tale account utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente riceve mai la reimpostazione della password SMS o una telefonata</p>
            </td>
            <td>
              <p>Utente fa clic su "testo me" o "chiama" e quindi non riceve mai nulla.</p>
            </td>
            <td>
              <p>Questo potrebbe essere il risultato di un numero di telefono corretto utente nella directory. Verificare che sia il numero di telefono nel formato "+ xxxyyyzzzzXeeee ccc". Per ulteriori informazioni sulla formattazione telefono numeri per l'utilizzo con la reimpostazione della password vedere <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quali dati vengono utilizzati per la reimpostazione della password</a>.</p>
              <p>Se si richiede un'estensione per essere inviate all'utente in questione, si noti che la reimpostazione della password non supporta le estensioni, anche se si specifica una nella directory (che vengono eliminati prima la chiamata viene inviata). Provare a utilizzare un numero, senza un'estensione o integrare l'estensione il numero di telefono PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente non riceve posta elettronica di reimpostazione password</p>
            </td>
            <td>
              <p>Utente fa clic su "email" e quindi non riceve mai nulla.</p>
            </td>
            <td>
              <p>La causa più comune per risolvere il problema è che il messaggio è stato rifiutato da un filtro della posta indesiderata. Controllare la posta indesiderata, la cartella posta indesiderata o eliminate per la posta elettronica.</p>
              <p>Inoltre assicurarsi che si desidera archiviare il messaggio giusto per il messaggio … molte persone sono molto simili indirizzi di posta elettronica e finiscano controllo della posta in arrivo problema per il messaggio. Se nessuna di queste opzioni funzionano, è inoltre possibile che l'indirizzo di posta elettronica nella directory non è corretto, verificare che l'indirizzo di posta elettronica sia quello giusto e riprovare. Per ulteriori informazioni sulla formattazione di posta elettronica indirizzi per l'utilizzo con la reimpostazione della password vedere <a href="active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset">quali dati vengono utilizzati per la reimpostazione della password</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>È impostato un criterio di reimpostazione password, ma quando un account di amministratore utilizza la reimpostazione della password, non viene applicato il criterio</p>
            </td>
            <td>
              <p>Account di amministratore reimpostare le password, vedere le stesse opzioni abilitate per la reimpostazione della password, posta elettronica e telefono cellulare, indipendentemente da quale criterio è impostato nella sezione <strong>Criteri di reimpostazione Password utente</strong> della scheda <strong>Configura</strong> .</p>
            </td>
            <td>
              <p>Le opzioni configurate nella sezione <strong>Criteri di reimpostazione Password utente</strong> della scheda <strong>Configura</strong> vengono applicate solo agli utenti finali dell'organizzazione.</p>
              <p>Gestione è affidata a Microsoft e controlli reimpostazione della password amministratore dei criteri per garantire il massimo livello di sicurezza</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente impedito tentativo troppe volte in un giorno reimpostazione della password</p>
            </td>
            <td>
              <p>Utente viene visualizzato un errore indicante:</p>
              <p>

              </p>
              <p>Utilizzare un'altra opzione.</p>
              <p>Si è tentato di verificare l'account troppe volte nell'ultimo 1 ore. Per motivi di sicurezza, sarà necessario attendere 24 ore prima che è possibile provare nuovamente. </p>
              <p>Se si desidera, possiamo contattare un amministratore dell'organizzazione per reimpostare la password dell'utente.</p>
            </td>
            <td>
              <p>È implementare un meccanismo di limitazione automatico per impedire agli utenti di tentare di reimpostare le password troppe volte in un breve periodo di tempo. Questo problema si verifica quando:</p>
              <ol class="ordered">
                <li>
Utente tenta di convalidare un numero di telefono 5 ore in un'ora.<br\><br\></li>
                <li>
Utente tenta di utilizzare la verifica di domande sicurezza 5 volte in un'ora.<br\><br\></li>
                <li>
Utente tenta di reimpostare una password per lo stesso account utente 5 volte in un'ora.<br\><br\></li>
              </ol>
              <p>Per risolvere il problema, chiedere all'utente di attendere 24 ore dopo l'ultimo tentativo e l'utente sarà in grado di reimpostare la propria password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Utente viene visualizzato un messaggio di errore durante la convalida il numero di telefono</p>
            </td>
            <td>
              <p>Quando si tenta di verificare un telefono da utilizzare come un metodo di autenticazione, venga visualizzato un errore indicante:</p>
              <p>

              </p>
              <p>Numero di telefono corretto specificato.</p>
            </td>
            <td>
              <p>Questo errore si verifica quando il numero di telefono immesso non corrisponde al numero di telefono nel file.</p>
              <p>Verificare che l'utente immette il numero di telefono completo, incluso il codice paese e area, quando si tenta di usare il metodo telefonica per la reimpostazione della password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore durante l'elaborazione richiesta</p>
            </td>
            <td>
              <p>Utente viene visualizzato un messaggio di errore indicante:</p>
              <p>

              </p>
              <p>Errore durante l'elaborazione richiesta </p>
              <p>Quando si tenta di reimpostare una password.</p>
            </td>
            <td>
              <p>Questo errore può essere dovuto a molti problemi, ma in genere questo errore è causato da uno dei due un servizio interruzione del problema di configurazione o che non può essere risolto. </p>
              <p>Se viene visualizzato questo messaggio di errore e impatto l'azienda, contattare il supporto e ti aiuterà ASAP. Visualizzare le <a href="#information-to-include-when-you-need-help">informazioni da includere quando è necessaria assistenza</a> per vedere che cos'è necessario fornire al supporto tecnico per facilitare una risoluzione.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback"></a>Risolvere i problemi di Writeback di Password
Se si verifica un errore per l'attivazione, disabilitare o tramite Password Writeback, potrebbe essere possibile risolvere il problema seguendo la procedura di risoluzione dei problemi seguente:

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Errore di maiuscole/minuscole</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Determinare l'errore visualizzare un utente?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Soluzione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errori di avvio e onboarding generale</p>
            </td>
            <td>
              <p>Servizio di reimpostazione password non viene avviato in locale con errore 6800 nel registro eventi applicazioni del computer Azure AD Connect.</p>
              <p>

              </p>
              <p>Dopo la valutazione federato o hash della password utenti sincronizzati non è possibile reimpostare le password.</p>
            </td>
            <td>
              <p>Quando è abilitato la Password Writeback, il motore di sincronizzazione chiama la libreria di writeback per eseguire la configurazione (onboarding) tramite telefono con il servizio di onboarding cloud. Eventuali errori rilevati durante la valutazione o durante l'avvio endpoint WCF per il Writeback di Password genererà errori nel registro eventi nel registro eventi del computer Azure AD Connect.</p>
              <p>Durante il riavvio del servizio di sincronizzazione Active Directory, se è stato configurato writeback, endpoint WCF verrà avviata. Tuttavia, se l'avvio dell'endpoint non riesce, verrà semplicemente accedere evento 6800 e consentire l'avvio del servizio di sincronizzazione. La presenza di questo evento indica che Writeback Password endpoint non è stata avviata la. Registro eventi dettagli dell'evento (6800) insieme a voci del registro eventi generano PasswordResetService componente indicano perché l'endpoint Impossibile avviare. Esaminare questi errori registro eventi e provare a riavviare Azure AD Connect se non è ancora lavorando Writeback di Password. Se il problema persiste, provare a disattivare e riattivare Writeback di Password.</p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Quando si tenta di reimpostare una password o sbloccare un account con writeback di password abilitato, l'operazione non riesce. Inoltre, viene visualizzato un evento in calendario il registro eventi di Azure AD Connect contenente: "motore di sincronizzazione restituito un errore hr = 800700CE, messaggio = il nome del file o estensione è troppo lungo" dopo l'operazione di sblocco.
                            </p>
            </td>
            <td>
              <p>Questo può verificarsi se ha effettuato l'aggiornamento da versioni precedenti di Azure AD Connect o DirSync. L'aggiornamento a versioni precedenti di Azure AD Connect imposta una password 254 caratteri per l'account Azure agente di gestione di Active Directory (versioni più recenti verranno impostata una password di lunghezza 127 caratteri). Ad esempio le password lunghe funziona per le operazioni di Active Directory Connector importazione / esportazione ma non è supportati dall'operazione di Sblocca.
                            </p>
            </td>
            <td>
              <p>[Trovare l'account Active Directory](active-directory-aadconnect-accounts-permissions.md#active-directory-account) per Azure AD Connect e reimpostare la password contenere non più di 127 caratteri. Aprire quindi **Il servizio di sincronizzazione** dal menu Start. Passare ai **connettori** e trovare **Active Directory Connector**. Selezionare l'immagine e fare clic su **proprietà**. Passare alla pagina di **credenziali** e immettere la nuova password. Fare clic su **OK** per chiudere la pagina.
                            </p>
            </td>
          </tr>
                    <tr>
            <td>
              <p>Errore durante la configurazione di writeback durante l'installazione di Azure AD Connect.</p>
            </td>
            <td>
              <p>L'ultimo passaggio del processo di installazione di Azure AD Connect, viene visualizzato un errore che indica che la Password Writeback non è stato configurato.</p>
              <p>

              </p>
              <p>Registro eventi dell'applicazione di connessione di Azure Active Directory contiene l'errore 32009 con il testo "Errore durante la lettura auth token".</p>
            </td>
            <td>
              <p>Questo errore si verifica in due casi seguenti:</p>
              <ul>
                <li class="unordered">
È stata specificata una password per l'account di amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect non corretta.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Si è tentato di utilizzare un utente federato per l'account di amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Per correggere questo errore, verificare che non si usa un account federato per l'amministratore globale specificato all'inizio del processo di installazione di Azure AD Connect e che la password specificata sia corretta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore durante la configurazione di writeback durante l'installazione di Azure AD Connect.</p>
            </td>
            <td>
              <p>Il registro eventi di Azure AD Connect computer contiene errore 32002 generate il PasswordResetService.</p>
              <p>

              </p>
              <p>Legge l'errore: "errore si connette alla ServiceBus, il provider di token è in grado di fornire un token di sicurezza..."</p>
              <p>

              </p>
            </td>
            <td>
              <p>La causa dell'errore è che il servizio di reimpostazione password in esecuzione nell'ambiente locale non sia possibile connettersi all'endpoint del servizio bus nel cloud. Questo errore è normalmente in genere causato da una regola del firewall il blocco di una connessione in uscita a un determinato indirizzo web o porta.</p>
              <p>

              </p>
              <p>Assicurarsi che il firewall consente connessioni in uscita per le operazioni seguenti:</p>
              <ul>
                <li class="unordered">
Tutto il traffico su TCP 443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Connessioni in uscita a <br\><br\></li>
              </ul>
              <p>

              </p>
              <p>Dopo aver aggiornato le regole, riavviare il computer di Azure AD Connect e la Password Writeback deve iniziare a lavorare nuovamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Password Writeback endpoint locale non raggiungibile</p>
            </td>
            <td>
              <p>Dopo avere utilizzato per alcuni ora federato o hash password utenti sincronizzati non è possibile reimpostare le password.</p>
            </td>
            <td>
              <p>In alcuni casi, il servizio di Password Writeback potrebbe non riuscire avviare nuovamente dopo l'avvio di Azure AD Connect nuovamente. In questi casi, controllare innanzitutto se sembra essere abilitato in-Importo Amm.to Writeback di Password Questa operazione può essere eseguita utilizzando la procedura guidata di Azure AD Connect o powershell (vedere oggetti sezione sopra). Se viene visualizzata la caratteristica da attivare, provare ad abilitare o disabilitare la caratteristica di nuovo tramite l'interfaccia utente o PowerShell. Vedere "passaggio 2: abilitare Writeback di Password nel computer la sincronizzazione della Directory &amp; configurare regole firewall" <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">come abilitare/disabilitare le Password Writeback</a> per ulteriori informazioni su come eseguire questa operazione.</p>
              <p>

              </p>
              <p>Se il problema persiste, provare una disinstallazione completa e reinstallare Azure AD Connect.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errori relativi alle autorizzazioni</p>
            </td>
            <td>
              <p>Federato o password hash sincronizzati utenti che tentano di reimpostare loro vedere password un messaggio di errore dopo aver inviato la password che indica che si è verificato un problema di assistenza.</p>
              <p>

              </p>
              <p>Inoltre, durante le operazioni di reimpostazione password, potrebbe essere visualizzato che un messaggio di errore relativo a agente di gestione negato ai registri eventi locale attivato.</p>
            </td>
            <td>
              <p>Se vengono visualizzati questi errori nel log eventi, verificare che l'account di vendite di Active Directory (che è stata specificata nella creazione guidata al momento della configurazione) disponga delle autorizzazioni necessarie per il Writeback di Password.</p>
              <p>

              </p>
              <p>Si noti che una volta questa autorizzazione può richiedere fino a 1 ora per le autorizzazioni per l'ambito tramite sdprop attività in background sul controller di dominio. </p>
              <p>Per reimpostare per usare la password, l'autorizzazione deve apporre su descrittore dell'oggetto utente il cui reimpostarla. Fino a questa autorizzazione viene visualizzata nell'oggetto utente, la reimpostazione della password continuerà a non riuscire con accesso negato.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Errore durante la configurazione di Password Writeback dalla configurazione guidata Azure AD Connect </p>
            </td>
            <td>
              <p>Errore "Impossibile individuare vendite" nella creazione guidata durante l'attivazione/disattivazione Writeback di Password</p>
            </td>
            <td>
              <p>C'è un errore noto nella versione finale di Azure AD Connect che manifesti nelle situazioni seguenti:</p>
              <ol class="ordered">
                <li>
Configurare Azure AD Connect per tenant abc.com (dominio verificato) con credenziali. Il risultato è connettore AAD con nome "abc.com-AAD" da creare.<br\><br\></li>
                <li>
È quindi modificare le credenziali AAD per il connettore (con sincronizzazione precedente dell'interfaccia utente) per (si noti stesso tenant ma nome di dominio diverso). <br\><br\></li>
                <li>
A questo punto si prova ad attivare/disattivare Writeback di Password. Verrà costruire il nome del connettore utilizzando le credenziali, come "abc.onmicrosoft.com-AAD" e passare al cmdlet Writeback di Password. Questo non riuscirà perché nessun connettore creato con questo nome.<br\><br\></li>
              </ol>
              <p>È stato risolto in nostro build più recente. Se si dispone di una build precedenti, una soluzione alternativa consiste nell'utilizzare il cmdlet di powershell per attivare o disattivare la caratteristica. Vedere "passaggio 2: abilitare Writeback di Password nel computer la sincronizzazione della Directory &amp; configurare regole firewall" <a href="active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords">come abilitare/disabilitare le Password Writeback</a> per ulteriori informazioni su come eseguire questa operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Non è possibile reimpostare la password per gli utenti in gruppi speciali, ad esempio Domain Admins / e così via amministratori dell'organizzazione.</p>
            </td>
            <td>
              <p>Federato o password hash sincronizzati gli utenti che fanno parte di gruppi protetti e reimpostare loro vedere password di un messaggio di errore dopo averlo inviato la password che indica che si è verificato un problema di assistenza.</p>
            </td>
            <td>
              <p>Gli utenti con privilegi di Active Directory sono protetti tramite AdminSDHolder. Vedere <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> per altri dettagli. </p>
              <p>

              </p>
              <p>Ciò significa descrittori di protezione in tali oggetti periodicamente selezionati in modo che corrispondano AdminSDHolder specificato in un e vengono reimpostati se sono diversi. Le autorizzazioni aggiuntive necessari per il Writeback di Password pertanto non ambito a tali utenti. Questo può comportare Writeback di Password non funziona per tali utenti. È di conseguenza, non supportano la gestione delle password per gli utenti all'interno di questi gruppi perché si interrompe il modello di protezione di Active Directory.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reimposta operazioni avrà esito negativo e oggetto non è stato trovato</p>
            </td>
            <td>
              <p>Federato o password hash sincronizzati utenti che tentano di reimpostare loro vedere password un messaggio di errore dopo aver inviato la password che indica che si è verificato un problema di assistenza.</p>
              <p>

              </p>
              <p>Inoltre, durante le operazioni di reimpostazione password, potrebbe venire visualizzato un messaggio di errore ai registri eventi dal servizio di Azure AD Connect che indica un messaggio di errore "Impossibile trovare l'oggetto".</p>
            </td>
            <td>
              <p>Questo errore indica in genere che il motore di sincronizzazione non riesce a trovare l'oggetto utente lo spazio di connettore AAD o MV collegate o oggetto spazio connettore Active Directory. </p>
              <p>

              </p>
              <p>Per risolvere il problema, verificare che l'utente è effettivamente sincronizzato da locale a AAD tramite l'istanza corrente di Azure AD Connect e controllare lo stato degli oggetti negli spazi connettore e MV. Verificare che l'oggetto Active Directory è connettore all'oggetto MV tramite la regola "Microsoft.InfromADUserAccountEnabled.xxx".</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Reimposta operazioni avrà esito negativo e più corrispondenze trovato se</p>
            </td>
            <td>
              <p>Federato o password hash sincronizzati utenti che tentano di reimpostare loro vedere password un messaggio di errore dopo aver inviato la password che indica che si è verificato un problema di assistenza.</p>
              <p>

              </p>
              <p>Inoltre, durante le operazioni di reimpostazione password, venga visualizzato un messaggio di errore ai registri eventi dal servizio di Azure AD Connect che indica un errore di "Maches più trovato".</p>
            </td>
            <td>
              <p>Indica che il motore di sincronizzazione rilevato che l'oggetto MV è connesso a più oggetti Active Directory tramite "Microsoft.InfromADUserAccountEnabled.xxx". Questo errore indica che l'utente ha un account attivato più di un insieme di strutture. </p>
              <p>

              </p>
              <p>Questo scenario attualmente non è supportato per il Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Operazioni con password non riuscire con un errore di configurazione.</p>
            </td>
            <td>
              <p>Operazioni con password non riuscire con un errore di configurazione. Registro eventi dell'applicazione contiene Azure AD Connect errore 6329 con testo: 0x8023061f (operazione non è riuscita perché la sincronizzazione delle password non è attivata per questo agente di gestione).</p>
            </td>
            <td>
              <p>Questo si verifica se la configurazione di Azure AD Connect viene modificata per aggiungere&nbsp;una nuova foresta di Active Directory (o da rimuovere e aggiungere di nuovo un insieme di strutture esistente) <strong>dopo</strong> la funzione Writeback di Password è già stata attivata. Operazioni con password per gli utenti di tali insiemi appena aggiunti avrà esito negativo. Per risolvere il problema, disattivare e riattivare la caratteristica Writeback Password dopo aver completate le modifiche di configurazione della foresta.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Riscrittura password essere stati opportunamente reimpostati da works utenti correttamente ma writeback password modificato da un utente o Ripristina per un utente da un amministratore ha esito negativo.</p>
            </td>
            <td>
              <p>Quando si tenta di reimpostare una password per conto di un utente dal portale di gestione di Azure, viene visualizzato un messaggio con l'indicazione: "il servizio di reimpostazione password in esecuzione nell'ambiente locale non supporta gli amministratori reimpostazione della password utente. Eseguire l'aggiornamento alla versione più recente di Azure AD Connect per risolvere il problema."</p>
            </td>
            <td>
              <p>Quando la versione del motore di sincronizzazione non supporta l'operazione di Writeback Password specifica che è stato utilizzato. Versioni di Azure AD Connect più tardi 1.0.0419.0911 supporta tutte le operazioni di gestione delle password, ad esempio password Reimposta writeback writeback di modificare la password e writeback di reimpostazione password avviato dall'amministratore dal portale di gestione di Azure.&nbsp; Le versioni DirSync in un secondo momento rispetto 1.0.6862 supportano writeback di reimpostazione password solo. Per risolvere il problema, è consigliabile installare la versione più recente di Azure AD Connect o connettersi Azure Active Directory (per ulteriori informazioni, vedere <a href="active-directory-aadconnect">Strumenti di integrazione della Directory</a>) per risolvere il problema e per ottenere il massimo da Writeback di Password all'interno dell'organizzazione.</p>
            </td>
          </tr>
        </tbody></table>


## <a name="password-writeback-event-log-error-codes"></a>Codici di errore di password Writeback registro eventi
Procedura consigliata per la risoluzione dei problemi con la Password Writeback consiste nel verificare che nel registro eventi applicazioni nel computer in uso Azure AD Connect. Il registro eventi conterrà gli eventi da due origini di interesse per il Writeback di Password. L'origine di PasswordResetService per descrivere le operazioni e problemi relativi all'operazione di Writeback di Password. L'origine di sincronizzazione Active Directory per descrivere le operazioni e problemi relativi all'impostazione delle password, nell'ambiente Active Directory.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Codice</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Assegnare un nome / messaggio</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Origine</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Descrizione</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619: "una restrizione impedisce la password viene modificato in quella corrente specificato."</p>
            </td>
            <td>
              <p>Sincronizzazione Active Directory</p>
            </td>
            <td>
              <p>Si verifica quando il servizio di Password Writeback tenta di impostare una password nella directory locale che non soddisfa la validità della password, cronologia, complessità o requisiti di filtri del dominio.</p>
              <ul>
                <li class="unordered">
Se si ha la validità minima e la password all'interno di tale intervallo di tempo modifichi, non sarà possibile modificare nuovamente la password fino a raggiungere l'età specificata in un dominio. Scopo di verifica, impostare l'età minima su 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di requisiti della cronologia delle password abilitati, quindi è necessario selezionare una password che non è stata utilizzata nell'ora dell'ultimo N, dove N è l'impostazione della cronologia delle password. Se si seleziona una password è stata utilizzata l'ultima tempi N, si verrà visualizzato un errore in questo caso. Scopo di verifica, cronologia deve essere impostata su 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di requisiti di complessità delle password, tutti gli elementi verranno applicati quando si tenta di modificare o reimpostare una password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di filtri password abilitati e si sceglie una password che non soddisfa i criteri di filtri, quindi la reimpostazione o modificare l'operazione non riuscirà.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>RISORSE UMANE 8023042</p>
            </td>
            <td>
              <p>Motore di sincronizzazione restituito un errore hr = 80230402, messaggio = un tentativo di ottenere un oggetto non è riuscito perché sono presenti voci duplicate con stesso ancoraggio</p>
            </td>
            <td>
              <p>Sincronizzazione Active Directory</p>
            </td>
            <td>
              <p>Si verifica quando è abilitato l'id utente stesso in più domini.  Ad esempio, se la sincronizzazione di insiemi di Account/risorsa e avere lo stesso id utente presente ed è attivato in ogni, questo errore può verificarsi.  </p>
              <p>Questo errore può verificarsi anche se si utilizza un attributo di ancoraggio non univoco (ad esempio UPN o l'alias) e due utenti condividono allo stesso attributo ancoraggio.</p>
              <p>Per risolvere il problema, assicurarsi di non avere utenti singoli utenti all'interno dei domini e si utilizza un attributo ancoraggio univoco per ogni utente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale rilevata la reimpostazione della password richiesta per un federati o sincronizzati hash password utente provenienti da nel cloud. Questo evento è che il primo evento di ogni password Reimposta l'operazione di writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente selezionato una nuova password durante l'operazione di reimpostazione password, è stabilito che questa password soddisfi i requisiti di password aziendale e la password è stato scritto tornare all'ambiente di Active Directory locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente selezionato una password e la password ricevuti correttamente per l'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente di Active Directory locale, si è verificato un errore. Questo problema può verificarsi per diverse ragioni:</p>
              <ul>
                <li class="unordered">
La password dell'utente non soddisfano l'età, cronologia, complessità o filtrare i requisiti per il dominio. Provare una password completamente nuova per risolvere questo problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
L'account di servizio MA non ha le autorizzazioni appropriate per impostare la nuova password dell'account utente in questione.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Account dell'utente non è un gruppo protetto, ad esempio gli amministratori di dominio o dell'organizzazione, che impedisce le operazioni di impostazione di password.<br\><br\></li>
              </ul>
              <p>Vedere <a href="#troubleshoot-password-writeback">Risoluzione dei problemi Writeback Password</a> per informazioni sulle quali altri situtions possono più causare l'errore seguente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento si verifica se si abilita Writeback di Password con Azure AD Connect e indica che iniziato onboarding organizzazione al servizio web Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica il processo di onboarding stato completato e che Writeback di Password per sia pronto per l'utilizzo.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale ha rilevato una richiesta di modifica la password per una federati o sincronizzati hash password utente provenienti da nel cloud. Questo evento è il primo in ciascuna delle operazioni writeback Cambia password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente selezionato una nuova password durante un'operazione di modifica della password, è stabilito che questa password soddisfi i requisiti di password aziendale e la password è stato scritto tornare all'ambiente di Active Directory locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che un utente selezionato una password e la password ricevuti correttamente per l'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente di Active Directory locale, si è verificato un errore. Questo problema può verificarsi per diverse ragioni:</p>
              <ul>
                <li class="unordered">
La password dell'utente non soddisfano l'età, cronologia, complessità o filtrare i requisiti per il dominio. Provare una password completamente nuova per risolvere questo problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
L'account di servizio MA non ha le autorizzazioni appropriate per impostare la nuova password dell'account utente in questione.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Account dell'utente non è un gruppo protetto, ad esempio gli amministratori di dominio o dell'organizzazione, che impedisce le operazioni di impostazione di password.<br\><br\></li>
              </ul>
              <p>Vedere <a href="#troubleshoot-password-writeback">Risoluzione dei problemi Writeback Password</a> per informazioni sulle quali altri situazioni possono più causare l'errore seguente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Il servizio locale ha rilevato un reimpostazione della password richiesta per un federati o sincronizzati hash password utente provenienti da parte dell'amministratore per conto di un utente. Questo evento è il primo in ogni operazione di writeback di reimpostazione password generate dall'amministratore.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L'amministratore selezionata una nuova password durante un'operazione di reimpostazione password generate dall'amministratore, è stabilito che questa password soddisfi i requisiti di password aziendale e la password è stato scritto tornare all'ambiente di Active Directory locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L'amministratore selezionata una password per conto di un utente e la password ricevuti correttamente per l'ambiente locale, ma quando si è tentato di impostare la password nell'ambiente di Active Directory locale, si è verificato un errore. Questo problema può verificarsi per diverse ragioni:</p>
              <ul>
                <li class="unordered">
La password dell'utente non soddisfano l'età, cronologia, complessità o filtrare i requisiti per il dominio. Provare una password completamente nuova per risolvere questo problema.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
L'account di servizio MA non ha le autorizzazioni appropriate per impostare la nuova password dell'account utente in questione.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Account dell'utente non è un gruppo protetto, ad esempio gli amministratori di dominio o dell'organizzazione, che impedisce le operazioni di impostazione di password.<br\><br\></li>
              </ul>
              <p>Vedere <a href="#troubleshoot-password-writeback">Risoluzione dei problemi Writeback Password</a> per informazioni sulle quali altri situtions possono più causare l'errore seguente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento si verifica se si disattiva Writeback di Password con Azure AD Connect e indica che è stato avviato offboarding organizzazione al servizio web Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica il processo di offboarding stato completato e che la funzionalità di Writeback di Password è stata disattivata.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il processo di offboarding non è riuscito. Può trattarsi a causa di un errore di autorizzazioni dell'account di amministratore cloud o locale specificato durante la configurazione, o perché si sta tentando di usare un amministratore globale cloud federati quando la disattivazione del Password. Per risolvere il problema, verificare l'amministrazione autorizzazioni e che non si usa uno federato account durante la configurazione di funzionalità Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio di Password Writeback avviato correttamente ed è pronto per accettare le richieste di gestione password dal cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio Writeback di Password è stata interrotta e che tutte le richieste di gestione password dal cloud non verranno eseguita correttamente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è recuperato un token di autorizzazione per l'amministratore globale specificato durante l'installazione di Azure AD Connect per avviare il processo di offboarding o onboarding.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che è stato creato la chiave di crittografia password utilizzati per crittografare le password dal cloud per essere inviate per l'ambiente locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica un errore sconosciuto durante un'operazione di gestione password. Esaminare il testo dell'eccezione nell'evento per altri dettagli. Se si sono verificati problemi, provare a disattivare e riattivare Writeback di Password. Se questa operazione, includere una copia del log eventi con l'id di verifica specificato persona interna per il supporto tecnico.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica si è verificato un errore di connessione per la password cloud Reimposta servizio e in genere si verifica quando il servizio locale non è riuscito a connettersi al servizio web di reimpostazione password. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica si è verificato un errore di connessione per l'istanza del tenant del servizio bus. È possibile che si bloccano le connessioni in uscita, nell'ambiente locale. Controllare il firewall per assicurarsi di consentire le connessioni sulla TCP 443 e <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e riprovare. Se si verificano ancora problemi, provare a disattivare e riattivare Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il passaggio di input all'API di servizio web non è valido. Ripetere l'operazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore decrittografare la password ricevuti dal cloud. Può trattarsi a causa di mancata corrispondenza decrittografia chiave tra il servizio cloud e l'ambiente locale. Per risolvere questo problema, disattivare e riattivare Password Writeback nel proprio ambiente locale.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante la valutazione, informazioni specifiche tenant viene salvato in un file di configurazione dell'ambiente locale. Questo evento indica verificato un errore durante il salvataggio del file o che quando è stato avviato il servizio è stato un errore la lettura del file. Per risolvere il problema, provare nuovamente l'attivazione e disattivazione Writeback di Password per forzare una riscrittura di file di configurazione. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: questo evento non sono presente in Azure AD Connect, compilato solo all'inizio di DirSync supportata writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Durante la valutazione, si invia dati dal cloud alla password locale reimpostare servizio. Tali dati viene quindi scritto in un file in memoria prima dell'invio al servizio di sincronizzazione per memorizzare le informazioni in modo sicuro su disco. Questo evento indica un problema con la scrittura o aggiornare i dati in memoria. Per risolvere il problema, provare nuovamente l'attivazione e disattivazione Writeback di Password per forzare una riscrittura della configurazione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che una risposta non valida abbiamo ricevuto dal servizio web di reimpostazione password. Per risolvere il problema, provare nuovamente l'attivazione e disattivazione Writeback di Password.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che Impossibile individuare un'autorizzazione token per l'account di amministratore globale specificato durante l'installazione di Azure AD Connect. Questo errore può essere dovuto a un nome utente non valida o la password specificata per l'account di amministratore globale o federata perché l'account di amministratore globale specificato. Per risolvere il problema, eseguire nuovamente la configurazione con il nome utente corretto e la password e assicurarsi che l'amministratore è di tipo gestito (basata solo su cloud o la sincronizzazione delle password) account.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica si è verificato un errore durante la generazione la password chiave di crittografia o decrittografia di una password che arrivano a partire dal servizio cloud. Questo messaggio di errore che indica un problema con l'ambiente. Esaminare i dettagli del log eventi per altre informazioni e risolvere il problema. È possibile inoltre provare a disattivare e riattivare il servizio di Password Writeback per risolvere il problema.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale non può comunicare correttamente con il servizio web di reimpostare la password per avviare il processo di valutazione. È possibile a causa di una regola del firewall o durante un token di autenticazione per il tenant. Per risolvere il problema, verificare che non bloccano le connessioni in uscita TCP 443 e TCP 9350 9354 o <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e che l'account dell'amministratore AAD in uso per incorporata non federato. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: questo evento non sono presente in Azure AD Connect, compilato solo all'inizio di DirSync supportata writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che il servizio locale non può comunicare correttamente con il servizio web di reimpostare la password per avviare il processo di offboarding. È possibile a causa di una regola del firewall o durante un token di autorizzazione per il tenant. Per risolvere il problema, verificare che non bloccano le connessioni in uscita tramite 443 o a <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>e che l'account dell'amministratore AAD si usa per offboard non è federato. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che era per riconnettersi a istanza bus del servizio del tenant. In condizioni normali, questo non deve essere un problema, ma se viene visualizzato l'evento in molti casi, è consigliabile controllare la connessione di rete per bus di servizio, soprattutto se si tratta di una connessione di larghezza di banda ridotta o latenza elevata.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Per controllare lo stato del servizio Writeback di Password, verrà inviata dati heartbeat per le password reimpostare servizio web ogni 5 minuti. Questo evento indica che si è verificato un errore durante l'invio di queste informazioni integrità al servizio web cloud. Queste informazioni integrità non includono un OII o personali di dati ed sono puramente heartbeat e statistiche del servizio di base in modo da poter fornire informazioni sullo stato del servizio nel cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è verificato un errore sconosciuto restituito da Active Directory, controllare il registro eventi server Azure AD Connect per gli eventi dall'origine sincronizzazione Active Directory per ulteriori informazioni sull'errore simile al seguente.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'utente che sta tentando di reimpostare o modificare una password non è stato trovato nella directory locale. Questo problema può verificarsi quando l'utente è stato eliminato in locale ma non nel cloud, o se si verifica un problema di sincronizzazione. Controllare i registri di sincronizzazione, come l'ultima sincronizzazione alcuni dettagli per ulteriori informazioni esecuzione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quando la reimpostazione della password o richiesta di modifica è stata creata dal cloud, ancoraggio cloud specificato durante il processo di configurazione di Azure AD Connect vengono utilizzati per determinare come collegare la richiesta di un utente nel proprio ambiente locale. Questo evento indica che sono stati rilevati due utenti nella directory locale con lo stesso attributo ancoraggio cloud. Controllare i registri di sincronizzazione, come l'ultima sincronizzazione alcuni dettagli per ulteriori informazioni esecuzione.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'account di servizio agente di gestione che non dispone delle autorizzazioni appropriate per l'account in questione per impostare una nuova password. Assicurarsi che l'account di vendite foresta dell'utente disponga delle autorizzazioni di password Reimposta e impostare su tutti gli oggetti della foresta.  Per ulteriori informazioni su come eseguire questa operazione, vedere <a href="active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions">passaggio 4: configurare le autorizzazioni appropriate di Active Directory</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che si è tentato di reimpostare o modificare una password per un account che è stata disabilitata in locale. Attivare l'account e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Evento indica che si è tentato di reimpostare o modificare una password per un account che è stato bloccato in locale. Blocchi possono verificarsi quando un utente ha cercato una modifica o reimpostare operazione password troppe volte in un breve periodo. Sbloccare l'account e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica che l'utente specificato una password corrente errata durante l'esecuzione di una password operazione di modifica. Specificare la password corrente corretta e riprovare.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Si verifica quando il servizio di Password Writeback tenta di impostare una password nella directory locale che non soddisfa la validità della password, cronologia, complessità o requisiti di filtri del dominio.</p>
              <ul>
                <li class="unordered">
Se si ha la validità minima e la password all'interno di tale intervallo di tempo modifichi, non sarà possibile modificare nuovamente la password fino a raggiungere l'età specificata in un dominio. Scopo di verifica, impostare l'età minima su 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di requisiti della cronologia delle password abilitati, quindi è necessario selezionare una password che non è stata utilizzata nell'ora dell'ultimo N, dove N è l'impostazione della cronologia delle password. Se si seleziona una password è stata utilizzata l'ultima tempi N, si verrà visualizzato un errore in questo caso. Scopo di verifica, cronologia deve essere impostata su 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di requisiti di complessità delle password, tutti gli elementi verranno applicati quando si tenta di modificare o reimpostare una password.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
Se si dispone di filtri password abilitati e si sceglie una password che non soddisfa i criteri di filtri, quindi la reimpostazione o modificare l'operazione non riuscirà.<br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Questo evento indica durante la scrittura di un problema eseguire il backup di una password alla directory locale a causa di un problema di configurazione con Active Directory. Selezionare registro eventi di applicazioni del computer Azure AD Connect per i messaggi dal servizio di sincronizzazione Active Directory per ulteriori informazioni sul tipo di errore. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: questo evento non sono presente in Azure AD Connect, compilato solo all'inizio di DirSync supportata writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: questo evento non sono presente in Azure AD Connect, compilato solo all'inizio di DirSync supportata writeback.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>OBSOLETO: questo evento non sono presente in Azure AD Connect, compilato solo all'inizio di DirSync supportata writeback.</p>
            </td>
          </tr>
        </tbody></table>

## <a name="troubleshoot-password-writeback-connectivity"></a>Risolvere i problemi di connettività Writeback di Password

Se si verificano interruzioni del servizio con il componente Password Writeback di Azure AD Connect, ecco alcune azioni rapide che è possibile eseguire per risolvere questo problema:

 - [Riavvio Azure AD connettere il servizio di sincronizzazione](#restart-the-azure-AD-Connect-sync-service)
 - [Disattivare e riattivare la caratteristica Writeback di Password](#disable-and-re-enable-the-password-writeback-feature)
 - [Installare l'ultima versione di Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Risolvere i problemi di Writeback di Password](#troubleshoot-password-writeback)

In generale, è consigliabile eseguire le operazioni seguenti nell'ordine precedente per recuperare il servizio in modo più rapido.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Riavvio Azure AD connettere il servizio di sincronizzazione
Il riavvio di Azure Active Directory connettersi servizio di sincronizzazione può aiutare a risolvere i problemi di connettività o altri problemi temporanei con il servizio.

 1. Un amministratore, fare clic su **Start** del server che esegue **Azure AD Connect**.
 2. Digitare **"Services. msc"** nella casella di ricerca e premere **INVIO**.
 3. Cercare la voce di **Microsoft Azure AD Connect** .
 4. Pulsante destro del mouse sulla voce del servizio, fare clic su **Riavvia**e attendere il completamento dell'operazione.

    ![][002]

La procedura seguente verranno ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni possibile che si verifichino.  Se il riavvio del servizio di sincronizzazione non consente di risolvere il problema, è consigliabile che si tenta di disattivare e riattivare la caratteristica di Password Writeback come un passaggio successivo.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Disattivare e riattivare la caratteristica Writeback di Password
Disattivare e riattivare la caratteristica Writeback Password consentono di risolvere i problemi di connettività.

 1. Un amministratore, aprire la **Configurazione guidata di Azure AD Connect**.
 2. Nella finestra di dialogo **Connetti a Azure Active Directory** , immettere le **credenziali di amministratore globale di Azure Active Directory**
 3. Nella finestra di dialogo **connettersi a Active Directory** , immettere le **credenziali di amministratore dei servizi di dominio Active Directory**.
 4. Nella finestra di dialogo **identifica gli utenti** , fare clic sul pulsante **Avanti** .
 5. Nella finestra di dialogo **caratteristiche facoltative** , deselezionare la casella di controllo **writeback Password** .

    ![][003]

 6. Fare clic su **Avanti** attraverso le rimanenti pagine della finestra di dialogo senza apportare alcuna modifica finché non si raggiunge la pagina **pronto per la configurazione** .
 7. Assicurarsi che la pagina Configura Mostra l' **opzione writeback Password come disabilitato** e quindi fare clic sul pulsante **Configura** verde per salvare le modifiche.
 8. Nella finestra di dialogo **Installazione completata** , deselezionare l'opzione **Sincronizza** e quindi fare clic su **Fine** per chiudere la procedura guidata.
 9. Aprire nuovamente la **Configurazione guidata di Azure AD Connect**.
 10.    **Ripetere i passaggi da 2 a 8**, tranne assicurarsi di **Selezionare l'opzione writeback Password** le **caratteristiche facoltative** schermata per riattivare il servizio.

    ![][004]

La procedura seguente verranno ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni possibile che si verifichino.

Se disattivare e riattivare la caratteristica Writeback di Password non viene risolto il problema, è consigliabile che si tenta di installare nuovamente Azure AD Connect come un passaggio successivo.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installare l'ultima versione di Azure AD Connect
Reinstallare il pacchetto di Azure AD Connect risolvere eventuali problemi di configurazione che possono influenzare la possibilità di una connessione ai servizi cloud o per gestire le password nel proprio ambiente di Active Directory locale.
È consigliabile eseguire questo passaggio solo dopo aver tentato i primi due passaggi descritti sopra.

 1. Scaricare la versione più recente di Azure AD Connect [qui](active-directory-aadconnect.md#install-azure-ad-connect).
 2. Dal momento che sia stato già installato Azure AD Connect, è necessario solo eseguire un aggiornamento sul posto per aggiornare l'installazione di Azure AD Connect alla versione più recente.
 3. Eseguire il pacchetto scaricato e seguire le le istruzioni per aggiornare il computer di Azure AD Connect.  Nessun manuali passaggi aggiuntivi necessari a meno che non è stato personalizzato fuori delle regole di sincronizzazione casella, nel qual caso è necessario **eseguire il backup questi prima di procedere con l'aggiornamento e manualmente nuovamente distribuirle al termine**.

La procedura seguente verranno ristabilire la connessione con il servizio cloud e risolvere eventuali interruzioni possibile che si verifichino.

Se l'installazione la versione più recente del server Azure AD Connect non consente di risolvere il problema, è consigliabile provare nuovamente l'attivazione e disattivazione Writeback di Password come ultimo passaggio dopo aver installato l'ultima sincronizzazione QFE.

Se non viene risolto il problema, è consigliabile eseguire un'occhiata a [Risolvere i problemi di Password Writeback](#troubleshoot-password-writeback) e la [password di Azure Active Directory domande frequenti su gestione](active-directory-passwords-faq.md) per verificare se il problema potrebbe essere discusso sono.


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Collegamenti a password reimpostare documentazione
Di seguito sono disponibili collegamenti a tutte le pagine della documentazione di reimpostazione della Password di Azure Active Directory:

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [**Come funziona**](active-directory-passwords-how-it-works.md) - informazioni sui diversi componenti del servizio e cosa sei ognuno di essi
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - informazioni su come consentire agli utenti di reimpostare e cambiare le password cloud o locale
* [**Personalizza**](active-directory-passwords-customize.md) - informazioni su come personalizzare l'aspetto e l'aspetto e comportamento del servizio in base alle esigenze dell'organizzazione
* [**Procedure consigliate**](active-directory-passwords-best-practices.md) - informazioni su come distribuire rapidamente e gestire in modo efficiente password all'interno dell'organizzazione
* [**Ottenere informazioni approfondite**](active-directory-passwords-get-insights.md) - informazioni sulla funzionalità di creazione di report integrata
* [**Domande frequenti**](active-directory-passwords-faq.md) - risposte alle domande frequenti
* [**Altre informazioni**](active-directory-passwords-learn-more.md) - profondità passare direttamente ai dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"
