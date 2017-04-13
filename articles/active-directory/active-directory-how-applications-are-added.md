<properties
   pageTitle="Come le applicazioni vengono aggiunti all'Azure Active Directory."
   description="Questo articolo descrive come applicazioni vengono aggiunti a un'istanza di Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="shoatman"
   manager="kbrint"
   editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="02/09/2016"
      ms.author="shoatman"/>

# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Come e perché le applicazioni vengono aggiunti all'Azure Active Directory

Una delle operazioni inizialmente sconcertante quando si visualizza un elenco di applicazioni nell'istanza di Azure Active Directory è comprensione provenienza le applicazioni e perché sono sono disponibili.  In questo articolo fornisce una panoramica di alto livello delle modalità sono rappresentate nella directory e forniscono contesto utili per comprendere come un'applicazione è stato acquistato sia nella directory delle applicazioni.

## <a name="what-services-does-azure-ad-provide-to-applications"></a>A quali servizi forniscono alle applicazioni di Azure Active Directory?

Le applicazioni vengono aggiunti all'Azure Active Directory per utilizzare al meglio uno o più servizi forniti.  Questi servizi includono:

* Le autorizzazioni e l'autenticazione di app
* Autorizzazione e autenticazione dell'utente
* Singolo sign-on (SSO) utilizzando la federazione o password
* Il provisioning dell'utente e la sincronizzazione
* Controllo dell'accesso basato sui ruoli Utilizzare la directory per definire i ruoli di applicazione per svolgere ruoli in base a controlli di autorizzazione in un'app.
* servizi di autorizzazione oAuth (utilizzati da Office 365 e altre applicazioni di Microsoft per autorizzare l'accesso alle API/risorse.)
* La pubblicazione delle applicazioni e proxy. Pubblicare un'app da una rete privata a internet

## <a name="how-are-applications-represented-in-the-directory"></a>Come vengono rappresentate applicazioni nella directory?

Le applicazioni sono rappresentate in Azure Active Directory utilizzando 2 oggetti: oggetto applicazione e un oggetto principale del servizio.  È disponibile un oggetto di applicazione, registrato "casa" / "proprietario" o "pubblicazione" directory e uno o più servizi oggetti principali che rappresenta l'applicazione in ogni directory in cui viene applicato.  

L'oggetto applicazione descrive l'app di Azure Active Directory (servizio multi-tenant) e potrebbe includere una delle operazioni seguenti: (*Nota*: questa non è un elenco esaustivo.)

* Nome, il Logo e Publisher
* Informazioni riservate (chiavi simmetriche e/o asimmetriche utilizzate per eseguire l'autenticazione dell'app)
* Dipendenze API (oAuth)
* API/risorse/ambiti pubblicati (oAuth)
* Ruoli App (RBAC)
* Single Sign-on metadati e configurazione (SSO)
* Provisioning dei metadati e la configurazione dell'utente
* Configurazione e proxy dei metadati

Principale del servizio è un record dell'applicazione in ogni directory, in cui l'applicazione agisce inclusi home directory.  Capitale servizio:

* Fa riferimento a un oggetto applicazione mediante la proprietà id app
* Utente locale di record e le assegnazioni di ruolo app di gruppo
* Record concedere autorizzazioni a utenti e amministratori all'app
    * Ad esempio: autorizzazioni per l'app accedere a un messaggio di posta elettronica determinati utenti
* Criteri locali record inclusi i criteri di accesso condizionato
* Record locale alternative impostazioni locali per un'app
    * Regole di trasformazione delle attestazioni
    * Mapping degli attributi (il provisioning dell'utente)
    * Ruoli app specifica del tenant (se l'app supporta ruoli personalizzati)
    * Nome/Logo

### <a name="a-diagram-of-application-objects-and-service-principals-across-directories"></a>Un diagramma di oggetti dell'applicazione e identità di servizio in elenchi in linea

![Diagramma che illustra come applicazione oggetti e servizio identità esistente con le istanze di Azure Active Directory.][apps_service_principals_directory]

Come si può vedere dal diagramma precedente.  Microsoft gestisce due directory internamente (a sinistra) viene utilizzato per la pubblicazione di applicazioni.

* Uno per Microsoft Apps (directory di servizi Microsoft)
* Uno per App di terze parti 3 ° pre-integrate (directory App raccolta)

Autori/fornitori di applicazioni che si integrano con Azure Active Directory devono disporre di una directory di pubblicazione.  (Alcune Directory SAAS).

Applicazioni che si aggiungono personalmente includono:

* App che sono state sviluppate (integrato con AAD)
* App connesso per single sign-on
* App è stato pubblicato utilizzando il proxy di applicazione di Azure Active Directory.

### <a name="a-couple-of-notes-and-exceptions"></a>Un paio di note ed eccezioni

* Non tutte le identità di servizio scegliere tornare a oggetti dell'applicazione.  Vero? Quando Azure Active Directory è stato creato originariamente i servizi forniti alle applicazioni sono state molto più limitati e servizio era sufficiente per la creazione di un'identità di app.  Il servizio originale dell'entità non è più vicino nella forma all'account del servizio Windows Server Active Directory.  Per questo motivo 's ancora possibile creare identità di servizio con Azure Active Directory PowerShell senza prima creare un oggetto applicazione.  L'API di grafico richiede un oggetto app prima di creare un servizio principale.
* Non tutte le informazioni descritte in precedenza è attualmente esposta a livello di programmazione.  Di seguito è disponibili solo nell'interfaccia utente:
    * Regole di trasformazione delle attestazioni
    * Mapping degli attributi (il provisioning dell'utente)
* Per altre informazioni dettagliate sul principale del servizio e gli oggetti applicazione fare riferimento alla documentazione del riferimento all'API REST di Azure Active Directory grafico.  *Suggerimento*: la documentazione dell'API di Azure Active Directory grafico riguarda più simile a un riferimento allo schema di Azure Active Directory che non è attualmente disponibile.  
    * [Applicazione](https://msdn.microsoft.com/library/azure/dn151677.aspx)
    * [Principale del servizio](https://msdn.microsoft.com/library/azure/dn194452.aspx)


## <a name="how-are-apps-added-to-my-azure-ad-instance"></a>Come vengono aggiunti App a personale istanza di Azure Active Directory
Esistono diversi modi che un'app può essere aggiunto al Azure Active Directory:

* Aggiungere un'app dalla [Raccolta App di Azure Active Directory](https://azure.microsoft.com/updates/azure-active-directory-over-1000-apps/)
* Accesso verso l'alto o in un 3o App di terze parti integrata con Azure Active Directory (ad esempio: [Smartsheet](https://app.smartsheet.com/b/home) o [DocuSign](https://www.docusign.net/member/MemberLogin.aspx))
    * Durante l'iscrizione verso l'alto o in utenti viene richiesto di assegnare autorizzazioni per l'app per accedere il suo profilo e le altre autorizzazioni.  La prima persona a dare il consenso causa un'entità servizio che rappresenta l'app da aggiungere alla directory.
* Eseguire l'accesso verso l'alto o in Microsoft online services come [Office 365](http://products.office.com/)
    * Quando si sottoscrive a Office 365 o iniziare una versione di valutazione vengono create uno o più identità di servizio nella directory che rappresenta i vari servizi che consentono di eseguire tutte le funzionalità associata a Office 365.
    * Alcuni servizi di Office 365 come SharePoint creare identità di servizio su base in corso consente la comunicazione sicura tra componenti inclusi i flussi di lavoro.
* Aggiungere un'app si sviluppano soluzioni in vedere portale di gestione Azure: https://msdn.microsoft.com/library/azure/dn132599.aspx
* Aggiungere un'app si sviluppano soluzioni utilizzando Visual Studio vedere:
    * [Metodi di autenticazione ASP.Net](http://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions)
    * [Servizi connessi](http://blogs.msdn.com/b/visualstudio/archive/2014/11/19/connecting-to-cloud-services.aspx)
* Aggiungere un'app da utilizzare per utilizzare il [Proxy di applicazione Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Connettere un'app per single sign-on con SAML o Password Single Sign-on
* Molti altri contenente varie esperienze di sviluppo di Azure e/in Esplora risorse API esperienze indipendentemente centri per sviluppatori

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Chi è autorizzato ad aggiungere alle applicazioni di personale istanza di Azure Active Directory?

Solo gli amministratori globali possono:

* Aggiungere App dalla raccolta app Azure Active Directory (pre-3 ° festa App integrate)
* Pubblicare un'applicazione tramite il Proxy di applicazione Azure Active Directory

Tutti gli utenti nella directory dispongano dei diritti per aggiungere applicazioni che lo sviluppo e discrezione su quali applicazioni sono Condividi/concedere l'accesso ai dati dell'organizzazione.  *È necessario ricordare di accesso dell'utente freccia su/di un'app e la concessione di autorizzazioni potrebbe causare un'entità servizio da creare.*

Questo potrebbe inizialmente audio relative, ma mantenere le operazioni seguenti in considerazione:

* App sono in grado di utilizzare Windows Server Active Directory per l'autenticazione utente per molti anni senza della registrato/registrate nella directory dell'applicazione.  A questo punto l'organizzazione verrà migliorata la visibilità per esattamente quante App utilizza la directory e possibili per.
* Non è più necessaria amministrazione basate su processo di pubblicazione/registrazione app.  Con Active Directory Federation Services è probabile che un amministratore ha aggiungere un'app come componente di applicazione per conto di sviluppatori.  Gli sviluppatori possono ora Self-Service.
* Gli utenti l'accesso in/tutte le app mediante gli account dell'organizzazione per scopi aziendali sono un'ottima soluzione.  Se successivamente lasciano l'organizzazione perdono l'accesso al proprio account nell'applicazione che erano in uso.
* Con un record di dati che è stato condiviso con cui applicazione è un'ottima soluzione.  Dati sono più trasportabili mai e verificano cancella traccia di chi ha condiviso i dati con le applicazioni può essere utile.
* App che usano Azure Active Directory per oAuth decidere esattamente quali autorizzazioni che gli utenti possono concedere alle applicazioni e che le autorizzazioni richiedono un amministratore per accettare.  Devono essere inseriti senza che indica che solo gli amministratori possono consenso ambiti più ampi e autorizzazioni più significative.
* Gli utenti aggiungendo e consentire l'App accedere ai dati sono controllati gli eventi in modo che è possibile visualizzare i report di controllo all'interno del portale di gestione di Azure per determinare come è stata aggiunta un'app alla directory.

**Nota:** *Ha operato la stessa Microsoft tramite le impostazioni predefinite per numero di mesi a questo punto.*

Con tutto ciò ha è possibile impedire agli utenti nella directory di aggiunta di applicazioni e da esercitare discrezione su quali informazioni condividere con applicazioni modificando la configurazione di Directory nel portale di gestione di Azure.  Le seguenti operazioni è possono accedere all'interno del portale di gestione di Azure nella scheda "Configura" della Directory.

![Schermata dell'interfaccia utente per la configurazione delle impostazioni di app integrate][app_settings]


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su come aggiungere alle applicazioni di Azure Active Directory e su come configurare i servizi per le applicazioni.

* Gli sviluppatori: [informazioni su come integrare un'applicazione con AAD](https://msdn.microsoft.com/library/azure/dn151122.aspx)
* Gli sviluppatori: [revisione esempio codice per App integrato con Azure Active Directory in Github](https://github.com/AzureADSamples)
* Gli sviluppatori e per i professionisti IT: [Rivedere la documentazione API REST per l'API di grafico Azure Active Directory](https://msdn.microsoft.com/library/azure/hh974478.aspx)
* I professionisti IT: [informazioni su come usare le applicazioni già integrate di Azure Active Directory dalla raccolta App](https://msdn.microsoft.com/library/azure/dn308590.aspx)
* I professionisti IT: [informazioni su esercitazioni pratiche per la configurazione specifica App pre-integrate](https://msdn.microsoft.com/library/azure/dn893637.aspx)
* I professionisti IT: [informazioni su come pubblicare un'applicazione tramite il Proxy di applicazione Azure Active Directory](https://msdn.microsoft.com/library/azure/dn768219.aspx)

## <a name="see-also"></a>Vedere anche

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)

<!--Image references-->
[apps_service_principals_directory]:media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
[app_settings]:media/active-directory-how-applications-are-added/IntegratedAppSettings.jpg
