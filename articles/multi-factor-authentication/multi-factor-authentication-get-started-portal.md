<properties 
    pageTitle="Distribuzione portale utente per il Server di autenticazione a più fattori Azure"
    description="Questa è la pagina di autenticazione a più fattori Azure che descrive come iniziare a utilizzare MFA Azure e il portale di utente."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="deploying-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Distribuzione portale utente per il Server di autenticazione a più fattori Azure

Portale utente consente all'amministratore installare e configurare il portale di Azure a più fattori autenticazione utente. Il portale utente è un sito web IIS che consente agli utenti di registrarsi autenticazione a più fattori Azure e mantenere il proprio account. Un utente può modificare il numero di telefono, modificare il proprio PIN o ignorare l'autenticazione a più fattori Azure durante il loro segno successivo in.

Gli utenti verranno accedere al portale utente usando il normale nome utente e la password e verrà completare una chiamata di autenticazione a più fattori Azure o rispondere a domande di sicurezza per completare l'autenticazione di loro. Se è consentita la registrazione utente, un utente configurerà il numero di telefono e un PIN la prima volta che accedono al portale di utente.

Utente portale amministratori possono configurare e concessa l'autorizzazione per aggiungere nuovi utenti e aggiornare gli attuali utenti.

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-portal/install.png)</center>

## <a name="deploying-the-user-portal-on-the-same-server-as-the-azure-multi-factor-authentication-server"></a>Distribuire il portale utente nello stesso server il Server di autenticazione a più fattori Azure

I prerequisiti seguenti sono necessari per installare il portale di utenti nello stesso server il Server di autenticazione a più fattori Azure:

- IIS deve essere installato inclusi asp.net e compatibilità di base metatag IIS 6 (per IIS 7 o versioni successive)
- Ha effettuato l'accesso utente deve disporre dei diritti di amministratore per il computer e il dominio, se applicabile.  Ciò avviene perché l'account deve disporre delle autorizzazioni per creare gruppi di sicurezza di Active Directory.

### <a name="to-deploy-the-user-portal-for-the-azure-multi-factor-authentication-server"></a>Distribuire il portale utente per il Server di autenticazione a più fattori Azure

1. All'interno del Server di autenticazione a più fattori Azure: fare clic sull'icona di User Portal nel menu a sinistra, fare clic sul pulsante Installa User Portal.
1. Fare clic su Avanti.
1. Fare clic su Avanti.
1. Se il computer fa parte di un dominio e la configurazione di Active Directory per rendere sicure le comunicazioni tra il portale di utente e il servizio di autenticazione a più fattori Azure è incompleta, verrà visualizzato il passaggio di Active Directory. Fare clic sul pulsante Avanti per completare automaticamente questa configurazione.
1. Fare clic su Avanti.
1. Fare clic su Avanti.
1. Fare clic su Chiudi.
1. Aprire un web browser da qualsiasi computer e passare all'URL in cui è stato installato User Portal (ad esempio https://www.publicwebsite.com/MultiFactorAuth). Assicurarsi che non vengano visualizzati avvisi certificato o errori.

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-portal/portal.png)</center>

## <a name="deploying-the-azure-multi-factor-authentication-server-user-portal-on-a-separate-server"></a>Distribuzione di Azure autenticazione a più fattori Server User Portal in un Server diverso

Per usare l'App di autenticazione a più fattori di Azure, di seguito sono necessari in modo che l'app è possibile comunicare correttamente con utente portale:

Per requisiti hardware e software, vedere requisiti Hardware e Software:

- È necessario utilizzare versione 6.0 o versione successiva del Server di autenticazione a più fattori Azure.
- Portale utente deve essere installata su un server web esposto a Internet in esecuzione Microsoft® Internet Information Services (IIS) 6. x, IIS 7. x o versioni successive.
- Quando si utilizza IIS 6. x, assicurarsi che ASP.NET v 2.0.50727 è installata, registrata e impostato su consentito.
- Servizi ruolo necessari quando si utilizza IIS 7. x o versioni successive includono ASP.NET e compatibilità Metabase IIS 6.
- Portale utente deve essere protetto con un certificato SSL.
- Azure a più fattori autenticazione Web Service SDK deve essere installato in IIS 6. x, IIS 7. x o versioni successive su server installati nel Server di autenticazione a più fattori Azure.
- Azure a più fattori autenticazione Web Service SDK devono essere protetti con un certificato SSL.
- Portale utente deve essere in grado di connettersi a SDK servizio Web di autenticazione a più fattori Azure su SSL.
- Portale utente deve essere autenticati Azure a più fattori autenticazione Web Service SDK utilizzando le credenziali dell'account di servizio che fa parte di un gruppo di sicurezza denominato "PhoneFactor amministratori". L'account di servizio e gruppo esistenti in Active Directory se il Server di autenticazione a più fattori Azure è in esecuzione su un server di dominio. L'account di servizio e gruppo disponibili sul Server di autenticazione a più fattori Azure se non è stato aggiunto a un dominio.

L'installazione il portale di utente in un server diverso da Server di autenticazione a più fattori Azure richiede tre passaggi seguenti:

1. Installare il servizio web SDK
2. Installare il portale di utente
3. Configurare le impostazioni del portale utente nel Server di autenticazione a più fattori Azure


### <a name="install-the-web-service-sdk"></a>Installare il servizio web SDK

Se Azure a più fattori autenticazione Web Service SDK non è già installato sul Server di autenticazione a più fattori Azure, passare al server e aprire il Server di autenticazione a più fattori Azure. Fare clic sull'icona di Web Service SDK, fare clic su Installa Web Service SDK... pulsante e seguire le istruzioni visualizzate. Il servizio Web SDK devono essere protetti con un certificato SSL. Un certificato autofirmato è sufficiente a tale scopo, ma presenta da importare nell'archivio "Radice autorità di certificazione attendibili" dell'account del Computer locale nel server web User Portal in modo che esso verrà considerato attendibile il certificato quando si avvia la connessione SSL.

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-portal/sdk.png)</center>

### <a name="install-the-user-portal"></a>Installare il portale di utente

Prima di installare il portale di utente in un server diverso, tenere presente quanto segue:

- Può essere utile aprire un web browser sul server web esposto a Internet e passare all'URL del servizio Web SDK digitati config. Se il browser può accedere al servizio web correttamente, viene richiesto delle credenziali. Immettere il nome utente e la password che sono state immesse in config esattamente come viene visualizzata nel file. Assicurarsi che non vengano visualizzati avvisi certificato o errori.
- Se si trova un proxy inverso o un firewall che precede il server web User Portal e completato scaricando SSL, è possibile modificare config User Portal e aggiungere la seguente chiave per la <appSettings> sezione in modo che il portale utente può usare http invece https. <add key="SSL_REQUIRED" value="false"/>

#### <a name="to-install-the-user-portal"></a>Per installare il portale di utente

1. Aprire Esplora risorse nel Server di autenticazione a più fattori Azure server e passare alla cartella in cui è installato il Server di autenticazione a più fattori Azure (ad esempio, c:\Programmi Files\Multi fattore autenticazione Server). Scegliere la versione a 32 o 64 bit del file di installazione MultiFactorAuthenticationUserPortalSetup appropriata per il server installati nel portale di utente. Copiare il file di installazione per il server di accesso a Internet.
2. In server web esposto a Internet, è necessario eseguire il file di installazione con diritti di amministratore. Il modo più semplice per eseguire questa operazione consiste nell'aprire un prompt dei comandi come amministratore e passare al percorso in cui è stato copiato il file di installazione.
3. Eseguire il file di installazione MultiFactorAuthenticationUserPortalSetup64, se lo si desidera modificare il nome del sito e Directory virtuale.
4. Dopo aver completato l'installazione del portale di utente, selezionare C:\inetpub\wwwroot\MultiFactorAuth (o directory appropriata in base al nome di directory virtuale) e modificare il file config.
5. Individuare la chiave USE_WEB_SERVICE_SDK e modificare il valore da false in true. Individuare le chiavi WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e impostare i valori per il nome utente e la password dell'account del servizio che fa parte di protezione PhoneFactor Admins (vedere la sezione requisiti). Assicurarsi di immettere il nome utente e Password tra le virgolette semplici alla fine della riga, (valore = "" / >). Si consiglia di utilizzare un nome completo (ad esempio DOMINIO\nome utente o computer ome utente)
6. Individuare l'impostazione pfup_pfwssdk_PfWsSdk e modificare il valore di "http://localhost:4898/PfWsSdk.asmx" l'URL del servizio Web SDK che è in esecuzione sul Server di autenticazione a più fattori Azure (ad esempio https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Poiché si utilizza SSL per la connessione, è necessario fare riferimento Web Service SDK dal nome del server e non l'indirizzo IP poiché il certificato SSL venga rilasciato per il nome del server e l'URL utilizzato deve corrispondere al nome del certificato. Se il nome del server non viene risolto in un indirizzo IP dal server esposto a Internet, aggiungere una voce al file hosts del server per mappare il nome del Server di autenticazione a più fattori Azure l'indirizzo IP. Salvare il file config dopo che sono state apportate modifiche.
7. Se il sito Web che portale utente è stato installato in (ad esempio sito Web predefinito) non è già stata binded con un certificato di firma al pubblico, installare il certificato nel server in caso contrario già installato, aprire Gestione IIS e associare il certificato al sito Web.
8. Aprire un web browser da qualsiasi computer e passare all'URL in cui è stato installato User Portal (ad esempio https://www.publicwebsite.com/MultiFactorAuth). Assicurarsi che non vengano visualizzati avvisi certificato o errori.



## <a name="configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni del portale utente nel Server di autenticazione a più fattori Azure
Ora che il portale è installato, è necessario configurare il Server di autenticazione a più fattori Azure per lavorare con il portale.

Server di autenticazione a più fattori Azure offre diverse opzioni per il portale di utente.  La tabella seguente contiene un elenco di queste opzioni e una spiegazione dei vengono utilizzati per.

Impostazioni del portale utente|Descrizione|
:------------- | :------------- |
URL del portale utente| Consente di immettere l'URL del ospita il portale.
Autenticazione principale| Consente di specificare il tipo di autenticazione da utilizzare quando si accede al portale.  Autenticazione di Windows, raggio o LDAP.
Consentire agli utenti di effettuare l'accesso|Consente agli utenti di immettere un nome utente e la password nella pagina di accesso per il portale di utente.  Se non è selezionata, le caselle verranno disattivate.
Consentire la registrazione utente|Consente di registrare l'autenticazione a più fattori accettando loro una schermata in cui viene richiesto per ulteriori informazioni, ad esempio il numero di telefono.  Chiedi conferma per telefono backup consente di specificare un numero di telefono secondario.  Richiedi di terze parti token GIURAMENTO consente di specificare un token di GIURAMENTO parte 3 °.
Consentire agli utenti di avviare Bypass temporaneo| In questo modo agli utenti di avviare un tantum bypass.  Se un set di utente è l'operazione richiederà influenzano la volta successiva che l'utente accede.  Richiedi bypass secondi consente all'utente di una casella in modo che possono modificare il valore predefinito di 300 secondi.  In caso contrario, bypass monouso è valida solo per 300 secondi.
Consentire agli utenti di selezionare metodo| Consente di specificare il metodo di contatto principale.  Può trattarsi di telefonata, messaggio di testo, app per dispositivi mobili o token GIURAMENTO.
Consentire agli utenti di selezionare la lingua|  Consente all'utente di modificare la lingua in cui viene utilizzata per la telefonata, messaggio di testo, app per dispositivi mobili o token GIURAMENTO.
Consentire agli utenti di attivare app per dispositivi mobili| Consente agli utenti generare il codice di attivazione per completare il processo di attivazione di app per dispositivi mobili utilizzata con il server.  È anche possibile impostare il numero dei dispositivi che questo attivato.  Compreso tra 1 e 10.
Domande sulla protezione di utilizzo di fallback|Consente di utilizzare domande sulla protezione nel caso in cui si verifica un errore di autenticazione a più fattori.  È possibile specificare il numero di domande di sicurezza che è necessario rispondere correttamente.
Consentire agli utenti di associare token GIURAMENTO di terze parti| Consente di specificare un token di GIURAMENTO di terze parti.
Utilizzare GIURAMENTO token di fallback|Consente l'uso di un token GIURAMENTO nel caso in cui l'autenticazione a più fattori non viene completata.  È inoltre possibile specificare il timeout di sessione in minuti.
Attivare la registrazione|Consente l'accesso al portale di utente.  I file di log si trovano in: c:\Programmi\Microsoft Files\Multi fattore autenticazione Server\Logs..

La maggior parte di queste impostazioni sono visibili agli utenti dopo l'abilitazione e i segni di utente nel portale utente.

![Impostazioni del portale utente](./media/multi-factor-authentication-get-started-portal/portalsettings.png)



### <a name="to-configure-the-user-portal-settings-in-the-azure-multi-factor-authentication-server"></a>Per configurare le impostazioni del portale utente nel Server di autenticazione a più fattori Azure




1. Nel Server di autenticazione a più fattori Azure, fare clic sull'icona User Portal. Nella scheda Impostazioni, immettere l'URL per il portale di utente nella casella di testo URL portale utente. Questo URL verrà inserito in messaggi di posta elettronica inviati agli utenti quando vengono importati nel Server di autenticazione a più fattori Azure se è stata attivata la funzionalità di posta elettronica.
2. Scegliere le impostazioni che si desidera utilizzare nel portale di utente. Ad esempio, se gli utenti sono consentiti per controllare i metodi di autenticazione, verificare che Consenti agli utenti di selezionare metodo sia selezionata e i metodi possibile scegliere tra.
3. Fare clic sul collegamento nell'angolo superiore destro per comprendere le impostazioni di visualizzazione della Guida.

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-portal/config.png)</center>


## <a name="administrators-tab"></a>Scheda gli amministratori
Questa scheda è sufficiente consente di aggiungere gli utenti hanno privilegi di amministratore.  Quando si aggiunge un amministratore, è possibile ottimizzare le autorizzazioni che si ricevono.  In questo modo, può essere solo concedere le autorizzazioni necessarie per l'amministratore.  È sufficiente fare clic sul pulsante Aggiungi e quindi selezionare e utente e le relative autorizzazioni e quindi fare clic su Aggiungi.

![Amministratori del portale utente](./media/multi-factor-authentication-get-started-portal/admin.png)


## <a name="security-questions"></a>Domande sulla protezione
Questa scheda consente di specificare le domande di sicurezza che gli utenti dovranno fornire risposte a se le domande di protezione usare per l'opzione fallback sia selezionata.  Azure a più fattori Authenticaton Server viene fornito con domande predefiniti che è possibile usare.  È anche possibile modificare l'ordine o aggiungere le proprie domande.  Quando si aggiungono le proprie domande, è possibile specificare la lingua da utilizzare per tali domanda venga visualizzata anche.

![Domande sulla protezione del portale di utente](./media/multi-factor-authentication-get-started-portal/secquestion.png)


## <a name="passed-sessions"></a>Sessioni passate

## <a name="saml"></a>SAML
Consente di installare il portale di utente per accettare sulle attestazioni da un provider di identità utilizzando SAML.  È possibile specificare il timeout di sessione, specificare il certificato di verifica e disconnessione URL di reindirizzamento.

![SAML](./media/multi-factor-authentication-get-started-portal/saml.png)

## <a name="trusted-ips"></a>IP di attendibile
Questa scheda consente di specificare singoli indirizzi IP o intervalli di indirizzi IP che possono essere aggiunti in modo che se un utente è l'accesso da uno di questi indirizzi IP, viene ignorato autenticazione a più fattori.

![Portale utente attendibili IP](./media/multi-factor-authentication-get-started-portal/trusted.png)

## <a name="self-service-user-enrollment"></a>Registrazione utente modalità Self-Service
Se si desidera che gli utenti di effettuare l'accesso e la registrazione è necessario selezionare Consenti agli utenti l'accesso in e consentire le opzioni di registrazione utente. Tenere presente che l'esperienza di accesso dell'utente vengono influenzata dalle impostazioni selezionate.

Ad esempio quando un utente accede al portale di utente e si fa clic sul pulsante Accedi, quindi derivano alla pagina di configurazione di Azure a più fattori autenticazione utente.  A seconda di come è stato configurato l'autenticazione a più fattori di Azure, l'utente potrebbe essere possibile selezionare il metodo di autenticazione.  

Se selezionare il metodo di autenticazione chiamate vocali o sono stato preconfigurati a utilizzare tale metodo, la pagina verrà chiesto di immettere il numero di telefono principale ed estensione se applicabile.  Sono inoltre potrà immettere un numero di telefono alternativo.  

![Portale utente attendibili IP](./media/multi-factor-authentication-get-started-portal/backupphone.png)

Se l'utente è necessario usare un PIN quando l'autenticazione, la pagina verrà anche visualizzato un messaggio di immettere un PIN.  Dopo aver immesso i propri numeri di telefono e un PIN (se applicabile), l'utente fa clic la chiamata Me ora al pulsante autentica.  Autenticazione a più fattori Azure eseguirà l'autenticazione di una chiamata telefonica al numero di telefono principale dell'utente.  È necessario rispondere alla chiamata telefonica e immettere il proprio PIN (se applicabile) e premere n per passare alla sezione successiva del processo di registrazione automatica.   

Se si seleziona il metodo di autenticazione di testo SMS o è stato preconfigurato a utilizzare tale metodo, la pagina richiederà all'utente per il numero di telefono cellulare.  Se l'utente è necessario usare un PIN quando l'autenticazione, la pagina verrà anche visualizzato un messaggio di immettere un PIN.  Dopo aver immesso il numero di telefono e un PIN (se applicabile), l'utente seleziona il testo Me ora al pulsante autentica.  Autenticazione a più fattori Azure eseguirà l'autenticazione di SMS sul cellulare dell'utente.  L'utente deve ricevere SMS che contiene un passcode di ora uno (strumento) e rispondere al messaggio con tale strumento plus il proprio PIN se applicabile) per passare alla sezione successiva del processo di registrazione automatica.

![Portale utente SMS](./media/multi-factor-authentication-get-started-portal/text.png)   

Se si seleziona il metodo di autenticazione app per dispositivi mobili o è stato preconfigurato a utilizzare tale metodo, la pagina verrà chiesto di installare l'app di autenticazione a più fattori Azure sul dispositivo e generare il codice di attivazione.  Dopo aver installato l'app di autenticazione a più fattori Azure, l'utente fa clic sul pulsante genera codice di attivazione.    

>[AZURE.NOTE]Per usare l'app di autenticazione a più fattori Azure, l'utente deve attivare le notifiche push per il dispositivo.

La pagina verrà visualizzato un codice di attivazione e un URL insieme a un'immagine di codice a barre.  Se l'utente è necessario usare un PIN quando l'autenticazione, la pagina verrà anche visualizzato un messaggio di immettere un PIN.  L'utente immette il codice di attivazione e l'URL nell'app autenticazione a più fattori Azure o utilizza il codice a barre per eseguire la scansione del codice a barre e fare clic sul pulsante attiva.    

Una volta completata l'attivazione, l'utente fa clic sul pulsante autenticazione questo punto.  Autenticazione a più fattori Azure eseguirà l'autenticazione a app per dispositivi mobili dell'utente.  L'utente deve immettere il proprio PIN (se applicabile) e fare clic sul pulsante autentica in app per dispositivi mobili per passare alla sezione successiva del processo di registrazione automatica.  


Se gli amministratori hanno configurato il Server di autenticazione a più fattori Azure per la raccolta di domande e risposte sicurezza, l'utente verrà visualizzata la pagina domande di sicurezza.  È necessario selezionare quattro sicurezza domande e fornire risposte alle proprie domande selezionati.    

![Domande sulla protezione del portale di utente](./media/multi-factor-authentication-get-started-portal/secq.png)  

Registrazione automatica l'utente è stata completata e l'utente è stato effettuato l'accesso al portale di utente.  Gli utenti possono accedere di nuovo in al portale di utente in qualsiasi momento in futuro per cambiare i numeri di telefono, pin, i metodi di autenticazione e domande di sicurezza, se consentito dai relativi amministratori.
