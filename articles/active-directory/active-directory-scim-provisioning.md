<properties
    pageTitle="Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory alle applicazioni | Microsoft Azure"
    description="Azure Active Directory può automaticamente effettuato il provisioning degli utenti e gruppi in qualsiasi applicazione o identità archivio che applicazioni da un servizio Web con l'interfaccia definita nella specifica del protocollo SCIM"
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="using-scim-to-enable-automatic-provisioning-of-users-and-groups-from-azure-active-directory-to-applications"></a>Utilizzo di SCIM per consentire il provisioning automatico degli utenti e gruppi da Azure Active Directory per applicazioni

##<a name="overview"></a>Panoramica

Azure Active Directory automaticamente può effettuare il provisioning degli utenti e gruppi in qualsiasi applicazione o identità archivio che applicazioni da un servizio Web con l'interfaccia definito nella [specifica di protocollo SCIM 2.0](https://tools.ietf.org/html/draft-ietf-scim-api-19). Azure Active Directory possono inviare richieste per creare, modificare ed eliminare assegnato agli utenti e gruppi per il servizio Web, in grado di convertire quindi le richieste in operazioni all'archivio di identità di destinazione. 

![][1]
*Figura: Il Provisioning di Azure Active Directory a un archivio di identità tramite un servizio Web*

Questa funzionalità utilizzabile in combinazione con la funzionalità di "[porta applicazione](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)" in Azure Active Directory per abilitare il single sign-on e provisioning per applicazioni che forniscono o applicazioni da un servizio web SCIM automatica dell'utente.

Esistono due casi di utilizzo per SCIM di Azure Active Directory:

* **Il provisioning di utenti e gruppi alle applicazioni che supportano SCIM** - applicazioni che supportano 2.0 SCIM e utilizzare i token del titolare OAuth per l'autenticazione funzionerà con Azure Active Directory della finestra.

* **Creare soluzione provisioning per le applicazioni che supportano altri il provisioning in base all'API** - per le applicazioni non SCIM, è possibile creare un endpoint SCIM per tradurre testi tra Azure AD endpoint SCIM e dalle API l'applicazione supporta per il provisioning dell'utente.  Per facilitare lo sviluppo di un endpoint SCIM, sono disponibili librerie CLI insieme a esempi di codice che illustrano come fornire un endpoint SCIM e convertire i messaggi SCIM.  

##<a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Il provisioning di utenti e gruppi alle applicazioni che supportano SCIM

Azure Active Directory possono essere configurate automaticamente il provisioning degli assegnate utenti e gruppi per le applicazioni di implementano un [sistema di gestione delle identità domini 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) Web service e accettano i token del titolare OAuth per l'autenticazione. All'interno della specifica SCIM 2.0 applicazioni devono soddisfare i requisiti:

* Supporta la creazione di utenti e/o gruppi, in base alla sezione 3.3 del protocollo SCIM.  

* Supporta la modifica di utenti e/o gruppi con le richieste di patch indicato nella sezione 3.5.2 del protocollo SCIM.  

* Supporta il recupero di una risorsa nota indicato nella sezione 3.4.1 del protocollo SCIM.  

*  Supporta la ricerca di utenti e/o gruppi, in base alla sezione 3.4.2 del protocollo SCIM.  Per impostazione predefinita, viene chiesto da externalId utenti e gruppi vengono eseguite query da displayName.  

* Supporta la ricerca utente in base all'ID e da Gestione indicato nella sezione 3.4.2 del protocollo SCIM.  

* Supporta la ricerca di gruppi di ID e membro indicato nella sezione 3.4.2 del protocollo SCIM.  

* Accetta i token del titolare OAuth di autorizzazione in base alla sezione 2.1 del protocollo SCIM.

È necessario contattare il provider di applicazione o documentazione del provider di applicazione per le istruzioni di compatibilità con questi requisiti.
 
###<a name="getting-started"></a>Guida introduttiva

Le applicazioni che supportano il profilo SCIM descritto in precedenza possono essere connesso a Azure Active Directory mediante la caratteristica di app "personalizzato" nella raccolta di applicazione di Azure Active Directory. Una volta connessa, Azure AD un processo di sincronizzazione viene eseguita ogni 5 minuti nel punto in cui endpoint SCIM dell'applicazione per assegnato agli utenti e gruppi, una query e crea o li modifica in base ai dettagli di un'assegnazione.

**Per collegare un'applicazione che supporta SCIM:**

1.  In un web browser, avviare il portale di gestione Azure in https://manage.windowsazure.com.
2.  Passare alla **Active Directory > Directory > [della Directory] > applicazioni**e selezionare **Aggiungi > Aggiungi un'applicazione dalla raccolta**.
3.  Selezionare la scheda **personalizzata** sulla sinistra, immettere un nome per l'applicazione e fare clic sull'icona di segno di spunta per creare un oggetto di app.

![][2]

4.  Nella schermata, selezionare il pulsante **del provisioning degli account Configura** secondo.
5.  Nel campo **Di Provisioning Endpoint URL** immettere l'URL dell'endpoint SCIM dell'applicazione.
6.  Se l'endpoint SCIM richiede un token del titolare OAuth da un emittente ad eccezione di Azure Active Directory, copiare il token del titolare OAuth necessari nel campo **Autenticazione Token (facoltativo)** . È in questo campo viene lasciato vuoto, quindi Azure Active Directory includerà un token del titolare OAuth emesso da Azure Active Directory con ogni richiesta. App che usano Azure Active Directory come un provider di servizi idenity convalidare questo Azure AD-token emessi.
7.  Fare clic su **Avanti**e fare clic sul pulsante **Avvia Test** per provare a connettersi all'endpoint SCIM Azure Active Directory. Se i tentativi hanno esito negativo, verranno visualizzate le informazioni di diagnostica.  
8.  Se il tentativo di connessione su esito positivo applicazione quindi fare clic su **Avanti** nella rimanenti schermate e fare clic su **completa** per chiudere la finestra di dialogo.
9.  Nella schermata, selezionare il terzo pulsante **Assegnare account** . Nella sezione utenti e gruppi risultante, assegnare gli utenti o gruppi a che cui eseguire il provisioning per l'applicazione.
10. Dopo che gli utenti e gruppi vengono assegnati, fare clic sulla scheda **Configura** nella parte superiore dello schermo.
11. In **Provisioning degli Account**, verificare che lo stato è impostato su in. 
12. In **Strumenti**, fare clic su **Riavvia del provisioning degli account** per avviare il processo di provisioning.

Si noti che tempo prima che il processo di provisioning inizierà a inviare richieste per l'endpoint SCIM venga 5-10 minuti.  Viene fornito un riepilogo dei tentativi di connessione nella scheda Dashboard dell'applicazione e un report di provisioning attività e gli errori di provisioning possono essere scaricati dal scheda report della directory.

##<a name="building-your-own-provisioning-solution-for-any-application"></a>Creazione di Provisioning soluzione per tutte le applicazioni personalizzate

Tramite la creazione di un servizio web SCIM che si interfaccia con Azure Active Directory, è possibile attivare il provisioning di utente single sign-on e automatico per qualsiasi applicazione che fornisce un utente resto o SOAP API di provisioning.

Ecco come funziona:

1.  Azure Active Directory fornisce una libreria di infrastruttura lingua comuni denominata [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Gli sviluppatori e integratori possono utilizzare questa raccolta per creare e distribuire un endpoint del servizio web basate su SCIM in grado di connettersi Azure Active Directory archivio dell'applicazione qualsiasi di identità.
2.  Mapping sono implementati nel servizio web per mappare lo schema utente standard per l'utente schema e i protocolli richiesti dall'applicazione.
3.  L'URL endpoint registrato in Azure Active Directory come parte di un'applicazione personalizzata nella raccolta dell'applicazione.
4.  Gli utenti e gruppi vengono assegnati a questa applicazione in Azure Active Directory. Durante l'assegnazione, vengono inseriti in una coda per consentirne la sincronizzazione all'applicazione di destinazione. Il processo di sincronizzazione gestisce la coda viene eseguita ogni 5 minuti.

###<a name="code-samples"></a>Esempi di codice

Per semplificare il processo, un set di [esempi di codice](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) vengono forniti da creare un endpoint del servizio web SCIM e dimostrare il provisioning automatico. Un campione è di un provider che mantiene un file con le righe di valori delimitati da virgole che rappresentano gli utenti e gruppi.  Il secondo è di un provider che opera il servizio di gestione di accesso e identità di servizi Web di Amazon.  

**Prerequisiti**

* Visual Studio 2013 o versione successiva
* [Azure SDK per .NET](https://azure.microsoft.com/downloads/)
* Computer Windows che supporta ASP.NET framework 4.5 da utilizzare come endpoint SCIM. Il computer deve essere accessibile dal cloud
* [Una sottoscrizione Azure con una versione di valutazione o licenza di Azure Active Directory Premium](https://azure.microsoft.com/services/active-directory/)
* Nell'esempio di Amazon AWS richiede librerie di [AWS Toolkit per Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Visualizzare il file Leggimi incluso nell'esempio per ulteriori informazioni

###<a name="getting-started"></a>Guida introduttiva

Il modo più semplice per implementare un endpoint SCIM che può accettare richieste di provisioning da Azure Active Directory consiste nel creare e distribuire il codice di esempio che genera i provisioning agli utenti di un file con valori delimitati da virgole (CSV).

**Per creare un endpoint SCIM esempio:**

1.  Scaricare il pacchetto di esempio di codice in [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2.  Decomprimere il pacchetto e inserirla nel computer Windows in una posizione, ad esempio C:\AzureAD-BYOA-Provisioning-Samples\.
3.  In questa cartella, avviare la soluzione FileProvisioningAgent in Visual Studio.
4.  Selezionare **Strumenti > Gestione di pacchetti libreria > Console di gestione pacchetti**ed eseguire i seguenti comandi per il progetto FileProvisioningAgent risolvere i riferimenti soluzione:

    Pacchetto di installazione pacchetto di installazione Microsoft.SystemForCrossDomainIdentityManagement pacchetto di installazione Microsoft.IdentityModel.Clients.ActiveDirectory pacchetto di installazione Microsoft.Owin.Diagnostics Microsoft.Owin.Host.SystemWeb

5.  Compilare il progetto FileProvisioningAgent.
6.  Avviare l'applicazione al prompt dei comandi di Windows (come un amministratore) e usare il comando **cd** per modificare la directory alla cartella **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** .
7.  Eseguire il comando seguente, sostituendo < indirizzo ip > con il nome di dominio o indirizzi IP del computer Windows.

    FileAgnt.exe http://<ip-address>:9000 TargetFile.csv

8.  Nelle finestre in **impostazioni di Windows > Impostazioni di Internet e di rete**, selezionare la **Windows Firewall > Impostazioni avanzate**e creare una **Regola in entrata** che consente l'accesso in ingresso alla porta 9000.
9.  Se il computer Windows è dietro un router, è necessario essere configurato per eseguire la conversione di accesso di rete tra il porta 9000 esposto a internet e la porta 9000 sul computer Windows o al router. Questa operazione è necessaria Azure Active Directory per poter accedere a questo punto finale nel cloud.


**Per registrare l'endpoint SCIM di esempio in Azure Active Directory:**

1.  In un web browser, avviare il portale di gestione Azure in https://manage.windowsazure.com.
2.  Passare alla **Active Directory > Directory > [della Directory] > applicazioni**e selezionare **Aggiungi > Aggiungi un'applicazione dalla raccolta**.
3.  Selezionare la scheda **personalizzata** sulla sinistra, immettere un nome, ad esempio "SCIM Test App" e fare clic sull'icona di segno di spunta per creare un oggetto di app. Si noti che l'oggetto applicazione creato intende rappresentano l'applicazione di destinazione, è necessario il provisioning di, l'implementazione di single sign-on per e non solo l'endpoint SCIM.

![][2]

4.  Nella schermata, selezionare il pulsante **del provisioning degli account Configura** secondo.
5.  Nella finestra di dialogo immettere l'URL e la porta dell'endpoint SCIM esposto a internet. Questa operazione può essere ad esempio http://testmachine.contoso.com:9000 o http://<ip-address>:9000/, dove < indirizzo ip > è internet esposto IP indirizzo.  
6.  Fare clic su **Avanti**e fare clic sul pulsante **Avvia Test** per provare a connettersi all'endpoint SCIM Azure Active Directory. Se i tentativi hanno esito negativo, verranno visualizzate le informazioni di diagnostica.  
7.  Se il tentativo di connettersi al servizio Web funziona correttamente, quindi fare clic su **Avanti** nelle schermate rimanenti e fare clic su **completa** per chiudere la finestra di dialogo.
8.  Nella schermata, selezionare il terzo pulsante **Assegnare account** . Nella sezione utenti e gruppi risultante, assegnare gli utenti o gruppi a che cui eseguire il provisioning per l'applicazione.
9.  Dopo che gli utenti e gruppi vengono assegnati, fare clic sulla scheda **Configura** nella parte superiore dello schermo.
10. In **Provisioning degli Account**, verificare che lo stato è impostato su in. 
11. In **Strumenti**, fare clic su **Riavvia del provisioning degli account** per avviare il processo di provisioning.

Si noti che tempo prima che il processo di provisioning inizierà a inviare richieste per l'endpoint SCIM venga 5-10 minuti.  Viene fornito un riepilogo dei tentativi di connessione nella scheda Dashboard dell'applicazione e un report di provisioning attività e gli errori di provisioning possono essere scaricati dal scheda report della directory.

Il passaggio finale per verificare l'esempio consiste nell'aprire il file TargetFile.csv nella cartella \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug sul proprio computer Windows. Una volta eseguito il processo di provisioning, il file Mostra i dettagli di tutti assegnate e viene completato il provisioning degli utenti e gruppi.

###<a name="development-libraries"></a>Librerie di sviluppo

Per sviluppare il proprio servizio Web che è conforme alle specifiche SCIM, è prima di tutto acquisire familiarità con le raccolte seguenti fornite da Microsoft per accelerare il processo di sviluppo: 

**1:**  Librerie Language Infrastructure comuni sono disponibili per l'utilizzo con lingue in base a tale infrastruttura, ad esempio c#.  Una di queste raccolte [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/)dichiara un'interfaccia Microsoft.SystemForCrossDomainIdentityManagement.IProvider, illustrata nella figura seguente.  Uno sviluppatore utilizzando le relative raccolte da implementare l'interfaccia con una classe che può essere definita, in generale, come un provider di servizi.  Le raccolte per abilitare lo sviluppatore distribuire facilmente un servizio Web conforme alle specifiche SCIM, uno ospitato all'interno di Internet Information Services o qualsiasi assembly CLI eseguibile.  Le richieste di servizio Web verranno convertite in chiamate a metodi del provider, che sarebbero programmati dello sviluppatore operare su un archivio di identità.    

![][3]

**2:** [Gestori di route Express](http://expressjs.com/guide/routing.html) sono disponibili per l'analisi di oggetti di richiesta Node che rappresenta le chiamate (come definito nella specifica SCIM), apportate a un servizio Web Node.     

###<a name="building-a-custom-scim-endpoint"></a>Creazione di un Endpoint SCIM personalizzato

Le raccolte descritte in precedenza, gli sviluppatori che utilizzano tali librerie consentono di inserire i servizi in qualsiasi assembly CLI eseguibile o in Internet Information Services.  Ecco il codice di esempio per l'hosting di un servizio all'interno di un assembly eseguibile, in corrispondenza dell'indirizzo http://localhost:9000/objectUri: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  
    
    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

È importante tenere presente che questo servizio deve disporre di un HTTP indirizzo e il server autenticazione certificato di cui l'autorità di certificazione è una delle operazioni seguenti: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Certificato di autenticazione server può essere associato a una porta su un host di Windows tramite l'utilità di shell di rete, come illustrato di seguito: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  
 
In questo caso, il valore specificato per l'argomento certhash è l'identificazione personale del certificato, mentre il valore specificato per l'argomento appid è un identificatore univoco globale non autorizzato.  

Per ospitare il servizio all'interno di Internet Information Services, uno sviluppatore da creare un assembly libreria di codice CLI con una classe denominata avvio dello spazio dei nomi predefinito dell'assembly.  Ecco un esempio della classe in: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

###<a name="handling-endpoint-authentication"></a>Gestione Endpoint autenticazione

Le richieste di Azure Active Directory includono un token del titolare OAuth 2.0.   Qualsiasi servizio che riceve la richiesta l'autenticazione autorità come Azure Active Directory per conto di previsto tenant di Azure Active Directory, per accedere al servizio Web di grafico di Azure Active Directory.  Nel token di autorità è identificato da un'attestazione iss, ad esempio, "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  In questo esempio, l'indirizzo di base del valore attestazione https://sts.windows.net, identifica Azure Active Directory come autorità, mentre il segmento indirizzo relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, è un identificatore univoco del tenant di Azure Active Directory per conto del quale è stato rilasciato il token.  Se il token è stato rilasciato per accedere al servizio Web grafico di Azure Active Directory, l'identificatore di tale servizio 00000002-0000-0000-c000-000000000000, deve essere il valore della domanda aud del token.  

Gli sviluppatori che utilizzano le librerie CLI fornite da Microsoft per la creazione di un servizio SCIM possano autenticare le richieste di Azure Active Directory utilizzando il pacchetto Microsoft.Owin.Security.ActiveDirectory procedendo come segue: 

**1:**  In un provider di implementare la proprietà Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior facendo sì che restituiscono un metodo da chiamare ogni volta che viene avviato il servizio: 

    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }

**2:**  A tale metodo per qualsiasi richiesta di qualsiasi endpoint del servizio autenticato reca un token emesso Azure Active Directory per conto di un tenant specificato, per accedere al servizio Web di grafico di Azure Active Directory, aggiungere il codice seguente: 

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }

##<a name="user-and-group-schema"></a>Utente e Schema di gruppo

Due tipi di risorse ai servizi Web SCIM può effettuare il provisioning di Azure Active Directory.  Questi tipi di risorse sono gli utenti e gruppi.  

Risorse utente sono identificate dall'identificatore schema, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, inclusa nella specifica protocollo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  Il mapping predefinito degli attributi di utenti in Azure Active Directory per gli attributi delle risorse urn: ietf:params:scim:schemas:extension:enterprise:2.0:User viene specificato nella tabella 1, sotto.  

Gruppo risorse identificate da identificatore schema http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabella 2, sotto mostra il mapping predefinito degli attributi di gruppi di Azure Active Directory per gli attributi delle risorse http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

###<a name="table-1-default-user-attribute-mapping"></a>Tabella 1: Predefinito mapping degli attributi utente

| Utente di Azure Active Directory | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| ------------- | ------------- |
| IsSoftDeleted | Attiva |
| displayName | displayName |
| Fax TelephoneNumber | valore phoneNumbers [tipo eq "fax"] |
| givenName | name.givenName |
| jobTitle | titolo |
| posta elettronica | valore di messaggi di posta elettronica [tipo eq "ufficio"] |
| mailNickname | externalId |
| Manager | Manager |
| dispositivi mobili | valore phoneNumbers [tipo eq "mobile"] |
| objectId | ID |
| CAP | .postalCode indirizzi [tipo eq "ufficio"] |
| Indirizzi proxy | messaggi di posta elettronica [Digitare eq "altro"]. Valore |
| OfficeName di recapito fisici | indirizzi [Digitare eq "altro"]. Formattato |
| streetAddress | .streetAddress indirizzi [tipo eq "ufficio"] |
| Cognome | name.familyName |
| Numero di telefono | valore phoneNumbers [tipo eq "ufficio"] |
| utente PrincipalName | nome utente |


###<a name="table-2-default-group-attribute-mapping"></a>Tabella 2: Predefinito mapping degli attributi di gruppo

| Gruppo di Azure Active Directory | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| ------------- | ------------- |
| displayName | externalId |
| posta elettronica | valore di messaggi di posta elettronica [tipo eq "ufficio"] |
| mailNickname | displayName |
| membri | membri |
| objectId | ID |
| proxyAddresses | messaggi di posta elettronica [Digitare eq "altro"]. Valore |


##<a name="user-provisioning-and-de-provisioning"></a>Il Provisioning dell'utente e deselezionare il Provisioning

Nella figura seguente archiviare i messaggi di Azure Active Directory, invierà a un servizio SCIM per gestire il ciclo di vita di un utente in un'altra identità.  Il diagramma mostra anche come un servizio SCIM implementato tramite le librerie CLI fornite da Microsoft per la creazione di tali servizi sarà tradurre le richieste di chiamate per i metodi di un provider.  

![][4]
*Figura: Il provisioning dell'utente e deselezionare il provisioning di sequenza*

**1:**  Azure Active Directory richiederà il servizio per un utente con un valore dell'attributo externalId corrisponde al valore dell'attributo mailNickname di un utente di Azure Active Directory.  La query verrà espresso come una richiesta di Hypertext Transfer Protocol come questo, in cui jyoung è riportato un esempio di mailNickname di un utente di Azure Active Directory: 

    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...

Se il servizio è stato creato utilizzando le librerie CLI fornite da Microsoft per l'implementazione di servizi SCIM, la richiesta verrà convertita in una chiamata al metodo Query del provider del servizio.  Ecco la firma di tale metodo: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);

Ecco la definizione dell'interfaccia Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }
    
    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }

Nel caso dell'esempio precedente di una query per un utente con un valore specificato per l'attributo externalId, i valori degli argomenti passati al metodo Query sarà come indicato di seguito: 

* parametri. AlternateFilters.Count: 1
* parametri. AlternateFilters.ElementAt(0). AttributePath: "externalId"
* parametri. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
* parametri. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
* correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. ID richiesta"] 

**2:**  Se la risposta a una query per il servizio per un utente con un valore dell'attributo externalId corrisponde al valore dell'attributo mailNickname di un utente di Azure Active Directory non restituisce tutti gli utenti, Azure Active Directory richiederà che il servizio di effettuare il provisioning di un utente corrispondente a quello di Azure Active Directory.  Ecco un esempio di tale richiesta: 

    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}

Le raccolte di CLI fornite da Microsoft per l'implementazione di servizi SCIM da tradurre la richiesta in una chiamata al metodo Create del provider del servizio.  Il metodo Create con la firma: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);

In caso di una richiesta di effettuare il provisioning di un utente, il valore dell'argomento risorsa sarà un'istanza di Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser classe, definita nella raccolta Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se la richiesta di provisioning l'utente ha avuto esito positivo, l'implementazione del metodo deve restituire un'istanza dell'il Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser classe, con il valore della proprietà identificatore impostare l'identificatore univoco dell'utente appena effettuato il provisioning.  

**3:**  Per aggiornare un utente che sono presenti in un archivio di identità applicazioni da un SCIM, Azure Active Directory procederà richiedendo lo stato corrente di tale utente dal servizio con una richiesta analogo al seguente: 

    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...

In un servizio costruito librerie CLI fornite da Microsoft per l'implementazione di servizi SCIM, la richiesta verrà convertita in una chiamata per il metodo di recupero del provider del servizio.  Ecco la firma del metodo recuperare: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);
    
    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }

Nel caso dell'esempio precedente di una richiesta per recuperare lo stato corrente di un utente, i valori delle proprietà dell'oggetto specificato come valore dell'argomento parametri saranno come indicato di seguito: 

* Identificatore: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

**4:**  Se non è un attributo di riferimento da aggiornare, quindi Azure Active Directory esegue una query nel servizio per determinare se il valore corrente dell'attributo riferimento nell'archivio di identità applicazioni dal servizio già utilizzerà il valore dell'attributo di Azure Active Directory.  Nel caso di utenti, l'unico attributo di cui verrà eseguite query il valore corrente in questo modo è l'attributo manager.  Ecco un esempio di una richiesta per determinare se l'attributo di gestione di un oggetto utente particolare con un determinato valore: 

    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...

Il valore del parametro di query attributi id, significa che se è presente un oggetto utente che soddisfa il valore di expression specificato come valore del parametro di query filtro, quindi il servizio è previsto per rispondere con una risorsa urn: ietf:params:scim:schemas:core:2.0:User o urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, incluso solo il valore dell'attributo id della risorsa.  Naturalmente, il valore dell'attributo id noto al richiedente, ovvero è incluso il valore del parametro di query di filtro. lo scopo di richiedere è effettivamente per richiedere una rappresentazione minima di una risorsa che soddisfano l'espressione di filtro come indicazione della se tale oggetto esiste.   

Se il servizio è stato creato utilizzando le librerie CLI fornite da Microsoft per l'implementazione di servizi SCIM, la richiesta verrà convertita in una chiamata al metodo Query del provider del servizio.  Il valore di proprietà dell'oggetto specificato come valore dell'argomento parametri saranno come indicato di seguito: 

* parametri. AlternateFilters.Count: 2
* parametri. AlternateFilters.ElementAt(x). AttributePath: "id"
* parametri. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
* parametri. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parametri. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parametri. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* parametri. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
* parametri. RequestedAttributePaths.ElementAt(0): "id"
* parametri. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

In questo caso, il valore dell'indice x può essere 0 e il valore di y indice può essere 1, o il valore di x può essere 1 e il valore di y può essere 0, a seconda dell'ordine delle espressioni del parametro di query filtro.   

**5:**  Ecco un esempio di una richiesta di Azure Active Directory a un servizio SCIM per aggiornare un utente: 

    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}

Le raccolte di CLI Microsoft per l'implementazione di servizi SCIM da tradurre la richiesta in una chiamata al metodo di aggiornamento del provider del servizio.  Ecco la firma di tale metodo: 

    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }
    
    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }
      
      public string Value
      { get; set; }
    }



Nel caso dell'esempio precedente di una richiesta di aggiornamento di un utente, l'oggetto specificato come valore dell'argomento patch avrà questi valori: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest come PatchRequest2). Operations.Count: 1
* (PatchRequest come PatchRequest2). Operations.ElementAt(0). Invece: OperationName.Add
* (PatchRequest come PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest come PatchRequest2). Operations.ElementAt(0). Value.Count: 1
* (PatchRequest come PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Riferimento: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest come PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valore: 2819c223-7f76-453a-919d-413861904646

**6:**  Per disattivare il provisioning di un utente da un archivio di identità applicazioni da un servizio SCIM, Azure Active Directory invierà una richiesta analogo al seguente: 

    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    
Se il servizio è stato creato utilizzando le librerie CLI fornite da Microsoft per l'implementazione di servizi SCIM, la richiesta verrà convertita in una chiamata al metodo all'eliminazione del provider del servizio.   Tale metodo presenta la firma: 

    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
 
L'oggetto specificato come valore dell'argomento resourceIdentifier sarà disponibili i valori delle proprietà nel caso dell'esempio precedente di una richiesta per disattivare il provisioning di un utente: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

##<a name="group-provisioning-and-de-provisioning"></a>Il Provisioning di gruppo e deselezionare il Provisioning

Nella figura seguente archiviare i messaggi di Azure Active Directory, invierà a un servizio SCIM per gestire il ciclo di vita di un gruppo in un'altra identità.  I messaggi non corrispondere a messaggi relativi a utenti in tre modi: 

* Lo schema di una risorsa di gruppo non verrà identificato come http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Richieste di recupero gruppi indica se l'attributo membri consiste nel esclusi da tutte le risorse fornite in risposta alla richiesta.  
* Le richieste per determinare se un attributo di riferimento ha un determinato valore verranno richieste sull'attributo membri.  

![][5]
*Figura: Il provisioning di gruppo e deselezionare il provisioning di sequenza*

##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Automatizzare utente Provisioning/Deprovisioning alle App SaaS](active-directory-saas-app-provisioning.md)
- [Personalizzazione dei mapping degli attributi per il Provisioning dell'utente](active-directory-saas-customizing-attribute-mappings.md)
- [Scrittura di espressioni per mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Definizione dell'ambito filtri per il Provisioning dell'utente](active-directory-saas-scoping-filters.md)
- [Account il Provisioning di notifiche](active-directory-saas-account-provisioning-notifications.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)


    
<!--Image references-->
[1]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
