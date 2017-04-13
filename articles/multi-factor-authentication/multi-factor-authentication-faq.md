<properties
    pageTitle="Domande frequenti su Azure autenticazione a più fattori"
    description="Fornisce un elenco di domande e risposte relativi all'autenticazione a più fattori Azure. Autenticazione a più fattori è un metodo di verifica identità dell'utente che richiede più di un nome utente e password. Fornisce un ulteriore livello di sicurezza per l'accesso degli utenti e le transazioni."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Domande frequenti su Azure autenticazione a più fattori


In questo argomento fornisce le risposte alle domande frequenti sull'autenticazione a più fattori Azure e mediante il servizio di autenticazione a più fattori, tra cui domande su modello di fatturazione e facilità di utilizzo.

## <a name="general"></a>Generale

**D: come Server di autenticazione a più fattori Azure gestire dati utente?**

Con il Server di autenticazione a più fattori, dati utente vengono archiviati solo sui server locale. Dati utente persistente non vengono archiviati nel cloud. Quando l'utente esegue la verifica in due passaggi, Server di autenticazione a più fattori invia dati al servizio cloud di autenticazione a più fattori Azure per l'autenticazione. La comunicazione tra Server di autenticazione a più fattori e il servizio cloud di autenticazione a più fattori utilizza Secure Sockets Layer (SSL) o protezione TLS (Transport Layer) tramite la porta 443 in uscita.

Quando le richieste di autenticazione vengono inviate al servizio cloud, raccolti per l'autenticazione e l'uso di report. Di seguito sono riportati i campi di dati inclusi nei registri di verifica in due passaggi:

- **ID univoco** (uno dei due nomi o locale a più fattori autenticazione Server ID utente)
- **Nome e cognome** (facoltativo)
- **Indirizzo di posta elettronica** (facoltativo)
- **Numero di telefono** (quando si utilizza una chiamata vocale o l'autenticazione di SMS)
- **Dispositivo Token** (se si utilizza l'autenticazione di app per dispositivi mobili)
- **Modalità di autenticazione**
- **Risultato dell'autenticazione**
- **Nome del Server di autenticazione a più fattori**
- **Autenticazione a più fattori Server IP**
- **Client IP** (se disponibile)

I campi facoltativi possono essere configurati nel Server di autenticazione a più fattori.

Il risultato di verifica (esito positivo o rifiuto) e il motivo se è stato negato, archiviati con i dati di autenticazione ed è disponibile nei rapporti di autenticazione e l'uso.


## <a name="billing"></a>Fatturazione

La maggior parte delle fatturazione possono essere risposte alle domande facendo riferimento alla [pagina prezzi autenticazione a più fattori](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**D: organizzazione addebitate per le chiamate telefoniche o messaggi di testo utilizzato per eseguire l'autenticazione gli utenti?**

Organizzazioni non vengono addebitate per il testo o singoli telefonate inseriti i messaggi inviati agli utenti tramite autenticazione a più fattori Azure. I proprietari di telefono potrebbero essere addebitati per le chiamate telefoniche SMS che ricevono, in base al servizio di numero di telefono personale.

**D: sono gli effetti le spese modello fatturazione per utente in base al numero di utenti che sono configurati per utilizzare l'autenticazione a più fattori o il numero di utenti a cui effettuare le verifiche?**

Fatturazione è in base al numero di utenti configurato per l'autenticazione a più fattori.

**D: come funziona la fatturazione di autenticazione a più fattori?**

Quando si utilizza il "per utente" o "per l'autenticazione" MFA del modello, Azure è una risorsa in base a consumo. Le spese vengono addebitate alla sottoscrizione di Azure dell'organizzazione come macchine virtuali di siti Web, e così via.

Quando si usa il modello di licenza, licenze di autenticazione a più fattori Azure sono acquistate e quindi assegnate agli utenti, come per Office 365 e altri prodotti di sottoscrizione.

**D: è presente una versione gratuita di autenticazione a più fattori Azure per gli amministratori?**

In alcuni casi, Sì. Autenticazione a più fattori per gli amministratori di Azure offre un sottoinsieme di funzionalità di Azure MFA senza costi. Questa offerta si applica ai membri del gruppo di amministratori globali di Azure nelle istanze di Azure Active Directory che non sono collegate a un provider di autenticazione a più fattori Azure basato su consumo. Utilizzo di un provider di autenticazione a più fattori Aggiorna tutti gli amministratori e utenti nella directory sono configurati per utilizzare l'autenticazione a più fattori per la versione completa di autenticazione a più fattori Azure.

**D: è presente una versione gratuita di autenticazione a più fattori Azure per gli utenti di Office 365?**

In alcuni casi, Sì. Autenticazione a più fattori per Office 365 offre un sottoinsieme di funzionalità di Azure MFA senza costi. Questa offerta è valida per gli utenti che hanno una licenza di Office 365 assegnata, quando un provider di autenticazione a più fattori Azure basato su consumo non è stato collegato all'istanza corrispondente di Azure Active Directory. Mediante il provider di autenticazione a più fattori Aggiorna tutti gli amministratori e utenti nella directory sono configurati per utilizzare l'autenticazione a più fattori per la versione completa di autenticazione a più fattori Azure.

**D: è possibile organizzazione passare tra per utente per l'autenticazione a consumo fatturazione modelli e in qualsiasi momento?**

L'organizzazione sceglie un modello di fatturazione per la creazione di una risorsa. Non è possibile modificare un modello di fatturazione dopo la risorsa viene eseguito il provisioning. È tuttavia possibile, creare un'altra risorsa di autenticazione a più fattori per sostituire l'originale. Impostazioni utente e le opzioni di configurazione non è possibile trasferite alla nuova risorsa.

**D: è possibile organizzazione passare tra la fatturazione consumo e il modello di licenza in qualsiasi momento?**

Quando le licenze vengono aggiunti a una directory che dispone già di un provider di autenticazione a più fattori Azure per utente, in base a consumo fatturazione è diminuisce il numero di licenze di proprietà. Se tutti gli utenti configurati per l'autenticazione a più fattori dispongano di licenze assegnate, l'amministratore può eliminare il provider di autenticazione a più fattori Azure.

Non è possibile combinare fatturazione consumo autenticazione a un modello di licenza. Quando un provider di autenticazione a più fattori per autenticazione è collegato a una directory, l'organizzazione viene effettuata per tutte le richieste verifica di autenticazione a più fattori, indipendentemente da eventuali licenze di proprietà.

**D: la propria organizzazione è necessario utilizzare e sincronizzare le identità per utilizzare l'autenticazione a più fattori Azure?**

Se un'organizzazione Usa un modello di fatturazione in base a consumo, Azure Active Directory non sono necessarie. Collegare un provider di autenticazione a più fattori una directory è facoltativo. Se l'organizzazione non è collegato a una directory, è possibile distribuire Server di autenticazione a più fattori Azure o l'autenticazione a più fattori di Azure SDK locale.

Azure Active Directory è necessaria per il modello di licenza poiché licenze vengono aggiunti alla directory quando è possibile acquistare e assegnarli agli utenti nella directory.


## <a name="usability"></a>Facilità di utilizzo

**D: cosa serve un utente? se non ricevono una risposta sul telefono o se il telefono non è disponibile per l'utente**

Se l'utente ha configurato un telefono backup, che deve riprovare e selezionare il telefono quando richiesto nella pagina di accesso. Se l'utente non dispone di un altro metodo configurato, amministratore dell'organizzazione può aggiornare il numero assegnato al telefono principale dell'utente.


**D: cosa serve l'amministratore? se un utente amministratore per un account che l'utente non potrà più accedervi dei contatti**

L'amministratore può ripristinare l'account utente chiedendo loro di eseguire nuovamente il processo di registrazione. Ulteriori informazioni sulla [gestione di utenti e impostazioni dei dispositivi con autenticazione a più fattori Azure nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: cosa serve un amministratore? se il telefono dell'utente che utilizza le password app perdito o furto**

L'amministratore può eliminare le password app dell'utente per impedire l'accesso non autorizzato. Quando l'utente dispone di un dispositivo di sostituzione, l'utente possa ricreare le password. Ulteriori informazioni sulla [gestione di utenti e impostazioni dei dispositivi con autenticazione a più fattori Azure nel cloud](multi-factor-authentication-manage-users-and-devices.md).

**D: cosa fare se l'utente non è possibile accedere alle App non browser?**

Un utente che ha configurato per l'utilizzo di autenticazione a più fattori richiede una password di app per accedere a alcune App diverso dal browser. Un utente deve deselezionare informazioni di accesso (Elimina), riavviare l'app e accedere con la propria password di app e nome utente.

È possibile ottenere ulteriori informazioni sulla creazione di password dell'app e altri [agevolare la password dell'app](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticazione moderno per i client di Office 2013
>
> I client di Office 2013 (inclusi Outlook) supportano nuovi protocolli di autenticazione. È possibile configurare Office 2013 per supportare l'autenticazione a più fattori. Dopo aver configurato Office 2013, le password app non sono necessari per i client di Office 2013. Per ulteriori informazioni, vedere [annuncio anteprima pubblica di Office 2013 autenticazione moderno](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**D: cosa serve un utente? se l'utente non riceve un messaggio di testo oppure se l'utente risponde a un messaggio di testo bidirezionale ma la verifica timeout**

Inviare messaggi di testo e ricezione di risposte nel SMS bidirezionale non riesce perché non controllabile fattori che potrebbero interferire con l'affidabilità del servizio. Che includono il paese di destinazione, l'operatore di telefonia mobile e il segnale.

Gli utenti che hanno difficoltà in modo affidabile la ricezione di SMS devono selezionare il metodo di app o una telefonata mobile invece. App per dispositivi mobili possono ricevere notifiche sia tramite cellulare e connessioni di rete Wi-Fi. Inoltre, app per dispositivi mobili può generare i codici di verifica anche quando il dispositivo non ha alcun segnale tutto. L'app Microsoft Authenticator è disponibile per [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se è necessario utilizzare SMS, è consigliabile utilizzare SMS unidirezionale anziché bidirezionale SMS laddove possibile. SMS unidirezionale è più affidabile e impedisce agli utenti di costi di SMS globale di rispondere a un messaggio di testo che è stato inviato da un altro paese.


**D: è possibile utilizzare i token hardware con Server di autenticazione a più fattori Azure?**

Se si utilizza Server di autenticazione a più fattori Azure, è possibile importare i token di terze parti aperto l'autenticazione (GIURAMENTO) basate sul tempo monouso password (TOTP) e quindi utilizzarli per la verifica in due passaggi.

È possibile utilizzare i token ActiveIdentity i token GIURAMENTO TOTP se si inserisce il file di chiave segreto in un file CSV e importare al Server di autenticazione a più fattori Azure. È possibile utilizzare i token GIURAMENTO con Active Directory Federation Services (ADFS), RADIUS Remote Authentication Dial-In User Service () quando il client possibile elaborare le risposte richiesta di accesso e l'autenticazione basata su moduli di Internet Information Server (IIS).

È possibile importare i token GIURAMENTO TOTP di terze parti con i formati seguenti:  
- Contenitore chiave simmetrico portatile (PSKC)  
- CSV se il file contiene un numero seriale, una chiave segreta in formato Base 32 e un intervallo di tempo  

**D: è possibile utilizzare Server di autenticazione a più fattori Azure per proteggere i servizi Terminal?**

Sì, ma se si utilizza Windows Server 2012 R2 o versioni successive solo tramite Remote Desktop Gateway (desktop).

Modifiche relative alla sicurezza in Windows Server 2012 R2 sono cambiati il modo in cui si connette Server di autenticazione a più fattori Azure per il pacchetto di protezione autorità sicurezza locale () in Windows Server 2012 e versioni precedenti. Per le versioni di servizi Terminal in Windows Server 2012 o versioni precedenti, è possibile [proteggere un'applicazione con l'autenticazione di Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se si utilizza Windows Server 2012 R2, è necessario Gateway Desktop remoto.

**D: perché da un utente ricevere una chiamata di autenticazione a più fattori da un chiamante anonimo dopo avere impostato l'ID chiamante?**

Quando si trovano le chiamate di autenticazione a più fattori tramite la rete telefonica pubblica, talvolta passano attraverso un vettore che non supporta l'ID chiamante. Per questi motivi, l'ID chiamante non è garantito, anche se il sistema di autenticazione a più fattori invia sempre.


## <a name="errors"></a>Errori

**D: azioni da intraprendere se è visualizzato un messaggio di errore "richiesta di autenticazione non è per un account attivato" quando si utilizzano le notifiche di app per dispositivi mobili?**

Indicare loro di eseguire questa procedura per rimuovere il proprio account dall'app per dispositivi mobili e quindi aggiungerlo di nuovo:

1. Passare al [proprio profilo portale Azure](https://account.activedirectory.windowsazure.com/profile/) e accedere con l'account aziendale.
2. Selezionare **verifica rafforzare la sicurezza**.
4. Rimuovere l'account esistente dall'app per dispositivi mobili.
5. Fare clic su **Configura**e quindi seguire le istruzioni per riconfigurare app per dispositivi mobili.


**D: azioni da intraprendere se è visualizzato un messaggio di errore 0x800434D4L durante l'accesso a un'applicazione non browser?**

Attualmente, un utente può usare verifica rafforzare la sicurezza solo con le applicazioni e servizi che l'utente può accedere tramite un browser. Le applicazioni non browser (denominate anche *applicazioni rich client*) che vengono installate in un computer locale, ad esempio Windows PowerShell, non funziona con un account che richiedono la verifica rafforzare la sicurezza. In questo caso, l'utente può vedere l'applicazione generato un errore 0x800434D4L.

Una possibile soluzione per questo utente distinta account per correlati Amministrazione e operazioni non amministratore. In un secondo momento, è possibile collegare le cassette postali tra l'account di amministratore e l'account non amministratore in modo che è possibile accedere a Outlook utilizzando l'account non amministratore. Per ulteriori informazioni, informazioni su come [consentire a un amministratore per aprire e visualizzare il contenuto della cassetta postale dell'utente](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Passaggi successivi

Se qui non rispondere alla domanda, lasciarlo nei commenti nella parte inferiore della pagina. In alternativa, ecco alcune opzioni aggiuntive per ottenere assistenza:


**D: come è possibile ottenere assistenza con l'autenticazione a più fattori Azure?**

- Cercare il [supporto della Microsoft Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) per le soluzioni ai problemi tecnici comuni.

- Cercare e individuare domande e risposte dalla community tecniche o pubblicare domande nei [forum di Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Se si ha un legacy PhoneFactor e domande o serve aiuto per reimpostare una password, utilizzare il collegamento [la reimpostazione della password](mailto:phonefactorsupport@microsoft.com) per aprire un caso di supporto.

- Contattare il supporto tecnico tramite [il supporto di Server di autenticazione a più fattori Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando si contatta Contattaci, può essere utile se è possibile includere le informazioni relative al problema possibili. È possibile fornire informazioni includono la pagina in cui è visualizzato l'errore, il codice di errore, l'ID di sessione specifici e l'ID dell'utente che ha visualizzato l'errore.
