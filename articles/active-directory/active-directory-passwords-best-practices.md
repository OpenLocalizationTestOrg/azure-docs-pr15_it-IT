<properties
    pageTitle="Procedure consigliate: Gestione di Azure Active Directory Password | Microsoft Azure"
    description="Procedure consigliate per la distribuzione e l'utilizzo, la documentazione per l'utente finale di esempio e guide di formazione per la gestione delle Password di Azure Active Directory."
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
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="deploying-password-management-and-training-users-to-use-it"></a>Distribuzione di gestione delle Password e formazione degli utenti per utilizzarla

> [AZURE.IMPORTANT] **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).

Dopo la reimpostazione della password l'attivazione, il passaggio successivo che è necessario eseguire è necessario ottenere gli utenti che utilizzano il servizio all'interno dell'organizzazione. A questo scopo è necessario assicurarsi che gli utenti siano configurati per utilizzare il servizio in modo corretto e anche che gli utenti abbiano la formazione devono essere eseguita correttamente in Gestione la propria password. In questo articolo spiega all'utente i concetti seguenti:

* [**Come ottenere gli utenti configurati per la gestione delle Password**](#how-to-get-users-configured-for-password-reset)
  * [Caratteristiche di un account configurato per la reimpostazione della password](#what-makes-an-account-configured)
  * [Metodi disponibili per popolare i dati di autenticazione se stessi](#ways-to-populate-authentication-data)
* [**I metodi migliori per distribuire all'organizzazione di reimpostazione della password**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Comunicazioni di posta elettronica basata su posta elettronica implementazione + campione](#email-based-rollout)
  * [Creare il proprio portale di gestione password personalizzata per gli utenti](#creating-your-own-password-portal)
  * [Come utilizzare la registrazione imposta come per forzare la registrazione in Accedi](#using-enforced-registration)
  * [Come caricare dati di autenticazione per gli account utente](#uploading-data-yourself)
* [**Utente di esempio e materiale di supporto (presto disponibile)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>Come ottenere gli utenti configurato per la reimpostazione della password
In questa sezione vengono descritti all'utente diversi metodi per il quale è possibile assicurarsi che tutti gli utenti dell'organizzazione possono usare modalità self-service la reimpostazione della password in modo efficace nel caso in cui si dimentica la password.

### <a name="what-makes-an-account-configured"></a>Caratteristiche di un account configurato
Prima di un utente può usare la reimpostazione della password, **tutte** le condizioni seguenti devono essere soddisfatte:

1.  La reimpostazione della password deve essere abilitata nella directory.  Informazioni su come abilitare la reimpostazione leggendo [consentono agli utenti di reimpostare la password di Active Directory Azure](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) o [consentire agli utenti di reimpostare o modificare la password di Active Directory](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) della password
2.  L'utente deve essere concesso in licenza.
 - Per gli utenti cloud, l'utente deve avere **una licenza di Office 365 a pagamento**o un **AAD base** o **AAD Premium licenza** assegnata.
 - Per locale gli utenti (federato o hash sincronizzato), l'utente **deve avere una licenza di AAD Premium assegnata**.
3.  L'utente deve avere il **numero minimo di dati di autenticazione definiti** in base ai criteri di reimpostare la password corrente.
 - Dati di autenticazione si considera definito se il campo corrispondente nella directory contiene dati corretti.
 - Un numero minimo di dati di autenticazione è definito come in **almeno una** delle opzioni di autenticazione abilitato se è stato configurato un criterio di una porta o presso **almeno due** delle opzioni di autenticazione abilitato se è stato configurato un criterio di due porta.
4.  Se l'utente stia usando un account locale, quindi [Writeback Password](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) deve essere attivata e attivato

### <a name="ways-to-populate-authentication-data"></a>Modi per popolare i dati di autenticazione
Sono disponibili diverse opzioni su come specificare i dati per gli utenti dell'organizzazione da utilizzare per la reimpostazione della password.

- Modificare gli utenti nel [Portale di gestione Azure](https://manage.windowsazure.com) o il [Portale di amministrazione di Office 365](https://portal.microsoftonline.com)
- Utilizzare sincronizzazione di Azure Active Directory per sincronizzare le proprietà utente in Active Directory Azure da un dominio di Active Directory locale
- Utilizzare Windows PowerShell per modificare le proprietà utente eseguendo [la procedura descritta di seguito](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Consentire agli utenti di registrare i propri dati guidando loro al portale di registrazione [all'http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
- Richiedere agli utenti di eseguire la registrazione per la reimpostazione quando effettuano l'accesso al proprio account Azure Active Directory mediante l'impostazione della password di [**Richiedi agli utenti di registrarsi durante l'accesso?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) opzione di configurazione su **Sì**.

Gli utenti non necessitano registrarsi reimpostazione della password per il sistema per l'uso.  Ad esempio, se si dispone mobile esistente o i numeri di telefono di office nella directory locale, è possibile sincronizzarli in Azure Active Directory e li verrà usata per la reimpostazione automatica della password.

È anche possibile leggere altre informazioni su [come i dati vengono utilizzati da password reimpostare](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) e [come è possibile inserire campi di autenticazione singoli con PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>Che cos'è il modo migliore per l'implementazione di reimpostazione della password per gli utenti?
Di seguito sono i passaggi di distribuzione generale per la reimpostazione della password:

1.  Abilitare la reimpostazione nella directory passando alla scheda **Configura** nel [Portale di gestione Azure](https://manage.windowsazure.com) e selezionando **Sì** per l'opzione **Gli utenti abilitati per la reimpostazione della Password** della password.
2.  Assegnare licenze a ogni utente a cui si desidera offrire di reimpostazione della password di facendo clic sulla scheda **licenze** nel [Portale di gestione Azure](https://manage.windowsazure.com).
3.  Se lo si desidera limitare a un gruppo di utenti cui è possibile distribuire la caratteristica lentamente nel tempo impostando l'interruttore **Limitare l'accesso per la reimpostazione della Password** su **Sì** e selezionando un gruppo di sicurezza abilitato per la reimpostazione della password (nota questi utenti devono avere tutte le licenze assegnate) reimpostazione della password.
4.  Chiedere agli utenti di utilizzare la reimpostazione tramite uno dei due l'invio di un messaggio di posta elettronica per indicare loro di eseguire la registrazione, della password per consentire applicate registrazione nella barra di accesso, oppure caricare i dati di autenticazione appropriata per tali utenti tramite DirSync, PowerShell o il [Portale di gestione Azure](https://manage.windowsazure.com).  Ulteriori informazioni su questo vengono fornite di seguito.
5.  Nel tempo, esaminare gli utenti la registrazione, passare alla scheda report e la visualizzazione del report di [**Attività di registrazione di reimpostare la Password**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) .
6.  Dopo aver registrato un buon numero di utenti, guardarli di usare la reimpostazione, passare alla scheda report e la visualizzazione del report di [**Attività di reimpostare la Password**](active-directory-passwords-get-insights.md#view-password-reset-activity) della password.

Esistono diversi modi per informare gli utenti che essi possono eseguire la registrazione per e utilizzare la reimpostazione dell'organizzazione della password.  Sono riportate di seguito.

### <a name="email-based-rollout"></a>Implementazione basata su posta elettronica
Ad esempio l'approccio più semplice per informare gli utenti per eseguire la registrazione per o utilizzare password reimpostare è inviando un messaggio di posta elettronica per indicare loro di farlo.  Di seguito è un modello che è possibile utilizzare per eseguire questa operazione.  È possibile sostituire i colori / logo con quelle di propria scelta per personalizzarlo in base alle proprie esigenze.

  ![][001]

È possibile [scaricare il modello di messaggio di posta elettronica](http://1drv.ms/1xWFtQM).

### <a name="creating-your-own-password-portal"></a>Creazione di portale password
Una strategia che funziona anche per la distribuzione di funzionalità di gestione password grandi clienti consiste nel creare un singolo "portale password" che gli utenti possono usare per gestire tutte le operazioni relative alla password in un'unica posizione.  

Molti clienti più grande scegliere per creare una radice voce DNS, ad esempio https://passwords.contoso.com con collegamenti per la password di Azure Active Directory reimpostare portale, reimpostazione della password portale di registrazione e la password di modificare pagine.  In questo modo, nelle comunicazioni di posta elettronica o i volantini che si inviano, è possibile includere un unico, elenchi, URL che è possibile utilizzare al meglio se hanno un secondo per iniziare a utilizzare il servizio.

Per cominciare qui, è stata creata una pagina semplice che utilizza l'ultima versione risponde dell'interfaccia utente struttura modelli e lavorerà su tutti i browser e dispositivi mobili.

  ![][007]

È possibile [scaricare il modello di sito Web](https://github.com/kenhoff/password-reset-page).  È consigliabile personalizzare il logo e i colori alle esigenze dell'organizzazione.

### <a name="using-enforced-registration"></a>Utilizzo di registrazione imposta
Se si vuole agli utenti di eseguire la registrazione per se stessi reimpostazione della password, è possibile forzare loro di eseguire la registrazione al momento dell'accesso al pannello di accesso in [http://myapps.microsoft.com](http://myapps.microsoft.com).  È possibile abilitare questa opzione dalla scheda **Configura** della directory, attivare l'opzione **Richiedi agli utenti di registro quando si accede al pannello di accesso** .  

È anche possibile definire o meno verrà chiesto di registrare nuovamente dopo un periodo di tempo configurabile modificando l'opzione **numero di giorni prima che gli utenti devono confermare ai contatti** da un valore diverso da zero. Per ulteriori informazioni, vedere [Personalizzazione di comportamento di gestione Password degli utenti](active-directory-passwords-customize.md#password-management-behavior) .

  ![][002]

Dopo aver attivato questa opzione quando gli utenti accedere a pannello di accesso, verrà visualizzato un popup che li informa che l'amministratore ha richiesto per verificare le informazioni di contatto. È possibile usarlo per reimpostare la password se perdono mai l'accesso al proprio account.

  ![][003]

Fare clic su **Verifica adesso** li porta per il **portale di registrazione la reimpostazione della password** in [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) e richiede loro di eseguire la registrazione.  Registrazione tramite questo metodo può essere chiusa facendo clic sul pulsante **Annulla** o chiudere la finestra, ma gli utenti sono promemoria ogni volta che eseguire l'accesso se che non viene registrato.

  ![][004]

### <a name="uploading-data-yourself"></a>Caricamento di dati manualmente
Se si desidera caricare dati di autenticazione se stessi, gli utenti devono non eseguire la registrazione per la reimpostazione prima di poter reimpostare le password della password.  Come gli utenti hanno i dati di autenticazione definiti il proprio account che soddisfa la password reimpostare criteri di definiti, quindi tali utenti saranno in grado di reimpostare la propria password.

Per informazioni su quali proprietà è possibile impostare tramite AAD connettersi o Windows PowerShell, vedere [quali dati vengono utilizzati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

È possibile caricare i dati di autenticazione tramite il [Portale di gestione Azure](https://manage.windowsazure.com) seguendo la procedura seguente:

1.  Passare alla directory dell' **estensione di Active Directory** nel [Portale di gestione Azure](https://manage.windowsazure.com).
2.  Fare clic sulla scheda **utenti** .
3.  Selezionare l'utente che si è interessati dall'elenco.
4.  Nella prima scheda, si noterà **Posta elettronica alternativo**, che può essere utilizzato come proprietà per abilitare la reimpostazione della password.

    ![][005]

5.  Fare clic sulla scheda **Informazioni sul lavoro** .
6.  In questa pagina, si troverà **Telefono dell'ufficio**, **cellulare**, **Telefono di autenticazione**e **Posta elettronica di autenticazione**.  Queste proprietà possono anche essere impostate per consentire all'utente di reimpostare la password.

    ![][006]

Vedere [quali dati vengono utilizzati per la reimpostazione della password](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) per informazioni sull'utilizzo di ognuna di queste proprietà possa essere.

Vedere [come accedere a password Reimposta i dati per gli utenti da PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) per vedere come è possibile leggere e impostare i dati con PowerShell.

## <a name="sample-training-materials"></a>Materiale di esempio
Stiamo lavorando a materiale di esempio che è possibile utilizzare per l'organizzazione IT e gli utenti per velocizzare rapidamente su come distribuire e utilizzare la reimpostazione della password.  Visita!


<br/>
<br/>
<br/>

## <a name="links-to-password-reset-documentation"></a>Collegamenti a password reimpostare documentazione
Di seguito sono disponibili collegamenti a tutte le pagine della documentazione di reimpostazione della Password di Azure Active Directory:

* **Sei qui perché si verificano problemi di accesso?** In caso affermativo, [Ecco come è possibile cambiarli e reimpostare la propria password](active-directory-passwords-update-your-own-password.md).
* [**Come funziona**](active-directory-passwords-how-it-works.md) - informazioni sui diversi componenti del servizio e cosa sei ognuno di essi
* [**Guida introduttiva**](active-directory-passwords-getting-started.md) - informazioni su come consentire agli utenti di reimpostare e cambiare le password cloud o locale
* [**Personalizza**](active-directory-passwords-customize.md) - informazioni su come personalizzare l'aspetto e l'aspetto e comportamento del servizio in base alle esigenze dell'organizzazione
* [**Ottenere informazioni approfondite**](active-directory-passwords-get-insights.md) - informazioni sulla funzionalità di creazione di report integrata
* [**Domande frequenti**](active-directory-passwords-faq.md) - risposte alle domande frequenti
* [**Risoluzione dei problemi**](active-directory-passwords-troubleshoot.md) - informazioni su come risolvere rapidamente i problemi con il servizio
* [**Altre informazioni**](active-directory-passwords-learn-more.md) - profondità passare direttamente ai dettagli tecnici del funzionamento del servizio



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"
