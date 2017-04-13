<properties 
    pageTitle="Configurare le applicazioni web nel servizio di App Azure" 
    description="Come configurare un'applicazione web in servizi di App di Windows Azure" 
    services="app-service\web" 
    documentationCenter="" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="configure-web-apps-in-azure-app-service"></a>Configurare le applicazioni web nel servizio di App Azure #

In questo argomento viene illustrato come configurare un'applicazione web tramite il [Portale di Azure].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="application-settings"></a>Impostazioni dell'applicazione

1. Nel [Portale di Azure], aprire e il per l'applicazione web.
2. Fare clic su **tutte le impostazioni**.
3. Fare clic su **Impostazioni applicazione**.

![Impostazioni dell'applicazione][configure01]

E **l'Impostazioni applicazione** è raggruppate in categorie diverse impostazioni.

### <a name="general-settings"></a>Impostazioni generali

**Framework versioni**. Se l'app viene utilizzata una Framework, impostare queste opzioni: 

- **.NET Framework**: impostare la versione di .NET framework. 
- **PHP**: impostare la versione PHP o **OFF** per disabilitare PHP. 
- **Linguaggio**: selezionare la versione di linguaggio o **OFF** per disabilitare Java. Usare l'opzione **Web contenitore** per scegliere tra versioni Tomcat e alla banchina.
- **Python**: selezionare la versione di Python o **OFF** per disabilitare Python.

Per motivi tecnici, l'attivazione di linguaggio per l'app disattiva le opzioni di .NET, PHP e Python.

<a name="platform"></a>
**Piattaforma**. Seleziona se l'applicazione web viene eseguita in un ambiente a 32 o 64 bit. L'ambiente a 64 bit richiede la modalità Basic o Standard. Liberare e modalità Shared sempre eseguite in un ambiente a 32 bit.

**Sockets web**. Impostare **via** per abilitare il protocollo WebSocket; ad esempio, se si usa un'applicazione web [ASP.NET SignalR] o [socket.io].

<a name="alwayson"></a>
**Sempre attiva**. Per impostazione predefinita, App web vengono scaricati qualora siano inattivi per un periodo di tempo. In questo modo il sistema di conservare le risorse. In modalità Basic o Standard, è possibile abilitare **Sempre attiva** mantenere l'app caricato sempre questa impostazione. Se l'app viene eseguita processi web continua, è necessario attivare **Sempre**o processi web potrebbero non funzionare correttamente.

**Gestite Pipeline versione**. Imposta la [modalità pipeline]di IIS. Lasciare il valore impostato su integrata (impostazione predefinita), a meno che non si dispone di un'app legacy che richiede una versione precedente di IIS.

**Scambio di automatico**. Se si abilita scambio automatico per un intervallo di aperto di distribuzione, servizio App automaticamente scambiare app web nell'ambiente di produzione quando si inserisce un aggiornamento per tale intervallo aperto. Per ulteriori informazioni, vedere [distribuire a bande orarie per web apps in Azure App servizio di gestione temporanea] (web-siti-gestita-publishing.md).

### <a name="debugging"></a>Il debug

**Il debug remoto**. Attiva il debug remoto. Quando attivata, è possibile utilizzare verrà remote in Visual Studio a cui connettersi direttamente da un'app web. Il debug remoto rimarrà attivato per 48 ore. 

### <a name="app-settings"></a>Impostazioni dell'App

In questa sezione contiene coppie nome/valore che web app verrà caricato nella schermata start. 

- Per le applicazioni .NET, queste impostazioni vengono inserite nella configurazione di .NET `AppSettings` in fase di esecuzione, si esegue l'override impostazioni esistenti. 

- Le applicazioni PHP, Python, Java e nodo possono accedere a queste impostazioni come variabili in fase di esecuzione. Per ogni impostazione app vengono create due variabili di ambiente; uno con il nome specificato per l'immissione di impostazioni di app e un altro con un prefisso APPSETTING_. Contengono lo stesso valore.

### <a name="connection-strings"></a>Stringhe di connessione

Stringhe di connessione per le risorse collegate. 

Per le applicazioni .NET, tali stringhe di connessione vengono inseriti nella configurazione di .NET `connectionStrings` impostazioni in fase di esecuzione, si esegue l'override voci esistenti nel punto in cui la chiave è uguale al nome del database collegati. 

Per le applicazioni PHP, Python, Java e nodo, queste impostazioni sarà disponibile come variabili in fase di esecuzione con il tipo di connessione preceduta. I prefissi variabile di ambiente sono i seguenti: 

- SQL Server:`SQLCONNSTR_`
- MySQL:`MYSQLCONNSTR_`
- Database SQL:`SQLAZURECONNSTR_`
- Personalizzato:`CUSTOMCONNSTR_`

Ad esempio, se sono stata denominata una stringa di connessione MySql `connectionstring1`, sarà possibile accedervi tramite la variabile di ambiente `MYSQLCONNSTR_connectionString1`.

### <a name="default-documents"></a>Documenti predefiniti

Il documento predefinito è la pagina web che viene visualizzato l'URL radice di un sito Web.  Viene utilizzato il primo file corrispondente nell'elenco. 

App Web potrebbe utilizzare i moduli che la distribuzione in base a URL, anziché utilizzo in contenuto statico, nel qual caso come tale non documento predefinito.    

### <a name="handler-mappings"></a>Mapping gestori

Utilizzare quest ' area per aggiungere processori di script personalizzati per gestire le richieste di estensioni di file specifici. 

- **Estensione**. Estensione di file da gestire, ad esempio *. PHP o handler.fcgi. 
- **Script processore percorso**. Percorso assoluto del processore di script. Le richieste di file che corrispondono l'estensione di file verranno elaborate dal processore di script. Utilizzare il percorso `D:\home\site\wwwroot` per fare riferimento alla directory radice dell'applicazione.
- **Argomenti aggiuntivi**. Argomenti della riga di comando facoltativi per il processore di script 


### <a name="virtual-applications-and-directories"></a>Directory e applicazioni virtuale 
 
Per configurare le applicazioni virtuali e directory, specificare ogni directory virtuale e il relativo percorso fisico corrispondente relativo alla radice di sito Web. Facoltativamente, è possibile selezionare la casella di controllo **dell'applicazione** per contrassegnare una directory di un'applicazione.


## <a name="enabling-diagnostic-logs"></a>Abilitazione dei registri diagnostici

Per abilitare log diagnostici:

1. In blade per un'app web, fare clic su **tutte le impostazioni**.
2. Fare clic su **registri diagnostici**. 

Opzioni per la scrittura dei registri diagnostici da un'applicazione web che supporta la registrazione: 

- **Applicazione di registrazione**. Scrive registri applicazioni nel file System. Registrazione dura per un periodo di 12 ore. 

**Livello**. Quando si abilita la registrazione delle applicazioni, questa opzione specifica la quantità di informazioni che verranno registrato (errore, avviso, informazioni o dettagliato).

**Registrazione server web**. Registri vengono salvati nel formato di file di registro esteso W3C. 

**Messaggi di errore dettagliati**. Messaggi di errore di dettagliata consente di salvare file con estensione htm. I file vengono salvati in /LogFiles/DetailedErrors. 

**Analisi richiesta non riuscita**. Log non è possibile le richieste di file XML. I file vengono salvati in /LogFiles/W3SVC*xxx*, dove xxx è un identificatore univoco. Questa cartella contiene un file XSL e uno o più file XML. Assicurarsi di scaricare il file XSL, perché offre funzionalità per la formattazione e il contenuto dei file XML di filtro.

Per visualizzare i file di log, è necessario creare le credenziali FTP, come indicato di seguito:

1. In blade per un'app web, fare clic su **tutte le impostazioni**.
2. Fare clic su **credenziali di distribuzione**.
3. Immettere un nome utente e la password.
4. Fare clic su **Salva**.

![Impostare le credenziali di distribuzione][configure03]

Il nome utente FTP completo è "app\username" dove *app* è il nome dell'applicazione web. Il nome utente viene elencato in e il app web, in **Essentials**.  

![Credenziali di distribuzione FTP][configure02]

## <a name="other-configuration-tasks"></a>Altre attività di configurazione

### <a name="ssl"></a>SSL 

In modalità Basic o Standard, è possibile caricare i certificati SSL per un dominio personalizzato. Per ulteriori informazioni, vedere [abilitare HTTPS per un'app web]. 

Per visualizzare i certificati caricati, fare clic su **Tutte le impostazioni** > **domini personalizzati e SSL**.

### <a name="domain-names"></a>Nomi di dominio

Aggiungere i nomi di dominio personalizzato per un'app web. Per ulteriori informazioni, vedere [configurare un nome di dominio personalizzato per un'app web nel servizio App Azure].

Per visualizzare i nomi di dominio, fare clic su **Tutte le impostazioni** > **domini personalizzati e SSL**.

### <a name="deployments"></a>Distribuzioni

- Configurare la distribuzione continua. Vedere [Utilizzando fra per distribuire Web Apps in Azure App servizio](./web-sites-deploy.md).
- Bande orarie di distribuzione. Vedere [distribuire in ambienti di gestione temporanea per Web Apps nel servizio App Azure].


Per visualizzare gli intervalli di distribuzione, fare clic su **Tutte le impostazioni** > **bande orarie di distribuzione**.

### <a name="monitoring"></a>Monitoraggio

In modalità Basic o Standard, è possibile verificare la disponibilità di endpoint HTTP o HTTPS, da fino a tre posizioni distribuito geografico. Test di monitoraggio non riesce se il codice di risposta HTTP non è un errore (4xx o 5xx) o la risposta richiede più di 30 secondi. Un endpoint è considerato disponibile se il test monitoraggio hanno esito positivo da tutti i percorsi specificati. 

Per ulteriori informazioni, vedere [come: monitorare lo stato dell'endpoint web].

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare], in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare un nome di dominio personalizzato nel servizio di App Azure]
- [Abilita HTTPS per un'app nel servizio App Azure]
- [Ridimensionare un'app web nel servizio App Azure]
- [Monitoraggio delle nozioni di base per Web Apps nel servizio App Azure]

<!-- URL List -->

[SignalR ASP.NET]: http://www.asp.net/signalr
[Portale di Azure]: https://portal.azure.com/
[Configurare un nome di dominio personalizzato nel servizio di App Azure]: ./web-sites-custom-domain-name.md
[Distribuire in ambienti di gestione temporanea per Web Apps nel servizio App Azure]: ./web-sites-staged-publishing.md
[Abilita HTTPS per un'app nel servizio App Azure]: ./web-sites-configure-ssl-certificate.md
[Procedura: monitorare lo stato dell'endpoint web]: http://go.microsoft.com/fwLink/?LinkID=279906
[Monitoraggio delle nozioni di base per Web Apps nel servizio App Azure]: ./web-sites-monitor.md
[modalità pipeline]: http://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Ridimensionare un'app web nel servizio App Azure]: ./web-sites-scale.md
[Socket.IO]: ./web-sites-nodejs-chat-app-socketio.md
[Provare il servizio di App]: http://go.microsoft.com/fwlink/?LinkId=523751

<!-- IMG List -->

[configure01]: ./media/web-sites-configure/configure01.png
[configure02]: ./media/web-sites-configure/configure02.png
[configure03]: ./media/web-sites-configure/configure03.png
