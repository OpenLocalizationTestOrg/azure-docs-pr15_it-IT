<properties
    pageTitle="Proteggere un'app nel servizio App Azure"
    description="Informazioni su come proteggere un web app, back-end app per dispositivi mobili o app API in Azure App servizio."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Proteggere un'app nel servizio App Azure

In questo articolo consente di introduzione sulla protezione del web app, back-end app per dispositivi mobili o app API in Azure App servizio. 

Sicurezza in Azure App servizio ha due livelli: 

- **Protezione dell'infrastruttura e la piattaforma** - si ritiene attendibile Azure per trasferire i servizi è necessario eseguire operazioni in modo sicuro nel cloud.
- **Protezione delle applicazioni** , è necessario progettare app in modo sicuro. Sono inclusi la modalità di integrazione con Azure Active Directory, come si gestiscono i certificati, e come si è certi che possono sicuro parlare diversi servizi. 

#### <a name="infrastructure-and-platform-security"></a>Protezione dell'infrastruttura e la piattaforma
Poiché servizio App mantiene le macchine virtuali Azure, lo spazio di archiviazione, le connessioni di rete, Framework web, gestione e caratteristiche di integrazione e molto altro ancora è attivamente protetta e avanzata attraversa conformità violenta e verifica la presenza su base continua per assicurarsi che:

- Le applicazioni di servizio di App sono isolate da entrambi Internet e dalle risorse Azure dei clienti.
- Comunicazione di informazioni riservate (ad esempio stringhe di connessione) tra l'applicazione di servizio di App e altre risorse Azure (ad esempio SQL Database) in un gruppo di risorse rimane all'interno di Azure e non attraversano i limiti di rete. Informazioni riservate sempre crittografati.
- Tutte le comunicazioni tra l'applicazione di servizio di App e risorse esterne, ad esempio Gestione PowerShell, interfaccia riga di comando, Azure SDK, API REST e connessioni ibrida, in modo corretto vengono crittografati.
- rischio di 24 ore gestione protegge risorse del servizio di App da malware, distribuito negazione del servizio (DDoS) uomo-in-diretti (MITM) e altri rischi. 

Per ulteriori informazioni sulla protezione dell'infrastruttura e la piattaforma in Azure, vedere [Centro protezione di Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Protezione delle applicazioni

Mentre è responsabile per la protezione dell'infrastruttura e piattaforma eseguita l'applicazione su Azure, è necessario proteggere l'applicazione stessa. In altre parole, è necessario sviluppare, distribuire e gestire il codice dell'applicazione e i contenuti in modo sicuro. In caso contrario, il codice dell'applicazione o contenuto può essere ancora esposto a rischi quali:

- Inserimento SQL
- Controllo della sessione
- Gli script tra siti
- Livello di applicazione MITM
- Livello di applicazione DDoS

Una descrizione completa di considerazioni sulla protezione per applicazioni basate sul web è lo scopo di questo documento. Come punto di partenza per un'ulteriore sulla protezione dell'applicazione, vedere l' [Apri Web applicazione protezione progetto (OWASP)](https://www.owasp.org/index.php/Main_Page), in particolare i [primi 10 progetto.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), in cui sono elencati i principali corrente difetti di protezione l'applicazione web critiche 10, come determinato dai membri del OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Eseguire penetrazione l'App

Uno dei modi più semplici per ancora iniziata con il test per vulnerabilità sull'applicazione di servizio App consiste nell'usare l' [integrazione con sicurezza Stagnola](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) per eseguire analisi dell'App vulnerabilità un solo clic. È possibile visualizzare i risultati in un report di facile comprensione e informazioni su come risolvere ogni vulnerabilità con istruzioni dettagliate.

Se si preferisce eseguire test di penetrazione o si desidera utilizzare un'altra famiglia di prodotti uno scanner o il provider, è necessario seguire il [processo di approvazione test di penetrazione Azure](https://security-forms.azure.com/penetration-testing/terms) e ottenere l'approvazione precedente per eseguire il test di penetrazione desiderato.

##<a name="https"></a>Proteggere la comunicazione con i clienti

Se si usa il ** \*. azurewebsites.net** nome di dominio creato per l'applicazione di servizio di App, è possibile utilizzare immediatamente HTTPS, come un certificato SSL è disponibile per tutti ** \*. azurewebsites.net** i nomi di dominio. Se il sito utilizza un [nome di dominio personalizzato](web-sites-custom-domain-name.md), è possibile caricare un certificato SSL [Abilita HTTPS](web-sites-configure-ssl-certificate.md) per il dominio personalizzato.

Abilitazione di [HTTPS](https://en.wikipedia.org/wiki/HTTPS) consente di proteggere attacchi MITM le comunicazioni tra l'app e i relativi utenti.

## <a name="secure-data-tier"></a>Livello di protezione dei dati

Servizio di App si integra altamente con Database SQL, in modo che tutte le stringhe di connessione crittografate tutti i livelli e solo decrittografare in macchine Virtuali che l'app viene eseguita *e* solo quando l'app viene eseguita. Inoltre, Database SQL Azure include molte caratteristiche di protezione per proteggere i dati delle applicazioni da minacce informatica, tra cui [la crittografia al resto](https://msdn.microsoft.com/library/dn948096.aspx), [Sempre crittografati](https://msdn.microsoft.com/library/mt163865.aspx), [Masking dati dinamici](../sql-database/sql-database-dynamic-data-masking-get-started.md)e [Individuazione](../sql-database/sql-database-threat-detection-get-started.md). Se si dispone di dati riservati o i requisiti di conformità, vedere [la protezione del Database di SQL](../sql-database/sql-database-security.md) per ulteriori informazioni su come proteggere i dati.

Se si usa un provider di database di terze parti, ad esempio ClearDB, è consigliabile consultare la documentazione del provider direttamente nelle procedure consigliate.  

##<a name="develop"></a>Proteggere sviluppo e la distribuzione

### <a name="publishing-profiles-and-publish-settings"></a>Pubblicazione dei profili e le impostazioni di pubblicazione

Quando si sviluppano applicazioni, eseguire le attività di gestione o automatizzare le attività utilizzando le utilità, ad esempio **Visual Studio**, **Matrice Web**, **PowerShell Azure** o l' **interfaccia di riga di comando Azure Azure CLI ()**, è possibile usare un file di *impostazioni di pubblicazione* o un *profilo di pubblicazione*. Due tipi di file autenticarsi Azure e devono essere protetti per impedire l'accesso non autorizzato.

* Contiene un file di **impostazioni di pubblicazione**

    * L'ID di abbonamento Azure

    * Certificato di gestione che consente di eseguire operazioni di gestione per l'abbonamento *senza dover specificare un nome dell'account o una password*.

* Contiene un file di **profilo di pubblicazione**

    * Informazioni per la pubblicazione per l'app

Se si usa un'utilità che utilizza un file di impostazioni di pubblicazione o un file di profilo pubblica, importare il file contenente le impostazioni di pubblicazione o profilo nell'utilità e quindi **eliminare** il file. Se è necessario mantenere il file da condividere con altri utenti lavorano al progetto, ad esempio, archiviarlo in un percorso sicuro, ad esempio una directory *crittografata* con autorizzazioni limitate.

Inoltre, assicurarsi che le credenziali importate vengono protetti. Ad esempio **PowerShell di Azure** e **Azure della riga di comando interfaccia di Azure** memorizzare informazioni importate nella **home directory** (*~* */users/yourusername* sistemi Windows e sistemi OS X o Linux.) Per una maggiore protezione, si consiglia di **crittografare** questi percorsi utilizzando gli strumenti di crittografia disponibili per il sistema operativo.

### <a name="configuration-settings-and-connection-strings"></a>Impostazioni di configurazione e le stringhe di connessione
È consigliabile archiviare le stringhe di connessione, le credenziali di autenticazione e altre informazioni riservate nei file di configurazione. Purtroppo, questi file possono essere esposti nel sito Web o selezionati in un repository pubblico esposizione queste informazioni. Una semplice ricerca nella [GitHub](https://github.com), ad esempio, scoprire numerosi file di configurazione con informazioni riservate esposti negli archivi pubblici.

La procedura consigliata è mantenere queste informazioni dal file di configurazione dell'applicazione. Il servizio di App consente di memorizzare informazioni sulla configurazione come parte dell'ambiente di runtime come **le impostazioni dell'app** e **stringhe di connessione**. I valori vengono esposti all'applicazione in fase di esecuzione tramite *le variabili di ambiente* per la maggior parte dei linguaggi di programmazione. Per le applicazioni .NET, tali valori vengono inseriti nella configurazione di .NET in fase di esecuzione. Oltre a queste situazioni, queste impostazioni di configurazione rimarrà crittografate a meno che non si visualizza o si configurarli tramite il [Portale di Azure](https://portal.azure.com) e utilità, ad esempio PowerShell o CLI Azure. 

Memorizzazione delle informazioni di configurazione del servizio di App consente all'amministratore dell'applicazione di bloccare le informazioni riservate per le applicazioni di produzione. Gli sviluppatori possono utilizzare un gruppo distinto di impostazioni di configurazione per lo sviluppo di app e le impostazioni possono essere sostituite automaticamente da impostazioni configurate nel servizio di App. Anche gli sviluppatori devono fonti di informazioni riservate configurati per l'applicazione di produzione. Per ulteriori informazioni su come configurare le impostazioni dell'app e stringhe di connessione nel servizio di App, vedere [configurazione web apps](web-sites-configure.md).

### <a name="ftps"></a>INVIA

Servizio di App Azure fornisce l'accesso sicuro FTP nel file System per l'app tramite **FTPS**. Questa sezione consente di accedere in modo sicuro il codice dell'applicazione sul web app e diagnostica registri. Si consiglia di utilizzare sempre FTPS anziché FTP. 

Il collegamento FTPS per l'app sono disponibili con la procedura seguente:

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Selezionare **Esplora tutto**.
3. Selezionare **I servizi di App**e **individuare** il.
4. Selezionare l'app desiderata e il **App servizi** .
5. Blade dell'app, selezionare **tutte le impostazioni**.
6. Scegliere **proprietà**e **l'Impostazioni** .
7. Vengono forniti i collegamenti FTP e FTPS su e **l'Impostazioni** . 

Per ulteriori informazioni sul FTPS, vedere [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla protezione della piattaforma Azure, informazioni sulla segnalazione di un **incidente o abusi**o per informare Microsoft che si intende eseguire **test di penetrazione** del sito, vedere la sezione sicurezza del [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

Per ulteriori informazioni sui file **config** o **applicationHost** nelle applicazioni di servizio di App, vedere [Opzioni di configurazione sbloccato nel servizio di Azure App web App](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Per informazioni sulle informazioni di registrazione per le applicazioni di servizio di App, che può essere utile per rilevare attacchi, vedere [attivare la registrazione diagnostica](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità

* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
