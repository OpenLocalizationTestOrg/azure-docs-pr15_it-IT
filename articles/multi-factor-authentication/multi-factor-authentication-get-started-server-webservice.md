<properties 
    pageTitle="Guida introduttiva MFA Mobile App Web del Server"
    description="L'App di autenticazione a più fattori Azure offre la possibilità di autenticazione fuori banda aggiuntiva.  Consente al server MFA per usare le notifiche push per gli utenti."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="getting-started-the-mfa-server-mobile-app-web-service"></a>Guida introduttiva MFA Mobile App Web del Server

L'App di autenticazione a più fattori Azure offre la possibilità di autenticazione fuori banda aggiuntiva. Anziché inserire una telefonata automatica o SMS all'utente durante l'accesso, autenticazione a più fattori Azure inserisce una notifica all'App di autenticazione a più fattori Azure nello smartphone o un tablet dell'utente. L'utente digita semplicemente "Autentica" (o immette il PIN e toccare "Autentica") nell'app agli utenti di accedere.

Per usare l'App di autenticazione a più fattori di Azure, di seguito sono necessari in modo che l'app è possibile comunicare correttamente con il servizio Web App Mobile:

- Vedere requisiti Hardware e Software per requisiti hardware e software
- È necessario utilizzare versione 6.0 o versione successiva del Server di autenticazione a più fattori Azure
- Servizio Web App mobile deve essere installato su un server web esposto a Internet in esecuzione Microsoft® Internet Information Services (IIS) IIS 7. x o versioni successive.  Per ulteriori informazioni su IIS vedere [IIS.NET](http://www.iis.net/).
- Assicurarsi che v 4.0.30319 ASP.NET è installata, registrata e impostato su consentito
- Servizi ruolo obbligatori includono ASP.NET e compatibilità Metabase IIS 6
- Servizio Web App mobile deve essere accessibile tramite un URL pubblico
- Servizio Web App mobile devono essere protetti con un certificato SSL.
- Azure a più fattori autenticazione Web Service SDK deve essere installato in IIS 7. x o versioni successive nel server che il Server di autenticazione a più fattori Azure
- Azure a più fattori autenticazione Web Service SDK devono essere protetti con un certificato SSL.
- Servizio Web App mobile deve essere in grado di connettersi a SDK servizio Web di autenticazione a più fattori Azure su SSL
- Servizio Web App mobile deve essere autenticato Azure a più fattori autenticazione Web Service SDK utilizzando le credenziali dell'account di servizio che fa parte di un gruppo di sicurezza denominato "PhoneFactor amministratori". L'account di servizio e gruppo esistenti in Active Directory se il Server di autenticazione a più fattori Azure è in esecuzione su un server di dominio. L'account di servizio e gruppo disponibili sul Server di autenticazione a più fattori Azure se non è stato aggiunto a un dominio.


L'installazione il portale di utente in un server diverso da Server di autenticazione a più fattori Azure richiede tre passaggi seguenti:

1. Installare il servizio web SDK
2. Installare il servizio web app per dispositivi mobili
3. Configurare le impostazioni di app per dispositivi mobili nel Server di autenticazione a più fattori Azure
4. Attivare l'App di autenticazione a più fattori Azure per gli utenti finali

## <a name="install-the-web-service-sdk"></a>Installare il servizio web SDK

Se Azure a più fattori autenticazione Web Service SDK non è già installato sul Server di autenticazione a più fattori Azure, passare al server e aprire il Server di autenticazione a più fattori Azure. Fare clic sull'icona di Web Service SDK, fare clic su Installa Web Service SDK... pulsante e seguire le istruzioni visualizzate. Il servizio Web SDK devono essere protetti con un certificato SSL. Un certificato autofirmato è sufficiente a tale scopo, ma presenta da importare nell'archivio "Radice autorità di certificazione attendibili" dell'account del Computer locale nel server web User Portal in modo che esso verrà considerato attendibile il certificato quando si avvia la connessione SSL.

<center>![Programma di installazione](./media/multi-factor-authentication-get-started-server-webservice/sdk.png)</center>

## <a name="install-the-mobile-app-web-service"></a>Installare il servizio web app per dispositivi mobili
Prima di installare il servizio web app per dispositivi mobili, tenere presente quanto segue:

- Se il portale di Azure a più fattori autenticazione utente è già installato nel server esposto a Internet, il nome utente, password e l'URL per il servizio Web SDK possono essere copiati da Web. config del portale utente.
- Può essere utile aprire un web browser sul server web esposto a Internet e passare all'URL del servizio Web SDK digitati config. Se il browser può accedere al servizio web correttamente, viene richiesto delle credenziali. Immettere il nome utente e la password che sono state immesse in config esattamente come viene visualizzata nel file. Assicurarsi che non vengano visualizzati avvisi certificato o errori.
- Se si trova un proxy inverso o un firewall che precede il server web servizio Web App Mobile e si esegue SSL scaricando, è possibile modificare il file config servizio Web App Mobile e aggiungere la seguente chiave per la <appSettings> sezione in modo che il servizio Web App Mobile è possibile usare http anziché https. Tuttavia SSL è comunque necessario dall'App per dispositivi mobili al proxy di firewall/reverse. <add key="SSL_REQUIRED" value="false"/>

### <a name="to-install-the-mobile-app-web-service"></a>Per installare il servizio web app per dispositivi mobili

<ol>
<li>Aprire Esplora risorse sul Server di autenticazione a più fattori Azure e passare alla cartella in cui è installato il Server di autenticazione a più fattori Azure (ad esempio, c:\Programmi Files\Azure autenticazione a più fattori). Scegliere la versione a 32 o 64 bit del file di installazione AuthenticationPhoneAppWebServiceSetup a più fattori Azure specificare quello appropriato per il server installati nel servizio Web App Mobile. Copiare il file di installazione per il server di accesso a Internet.</li>

<li>In server web esposto a Internet, è necessario eseguire il file di installazione con diritti di amministratore. Il modo più semplice per eseguire questa operazione consiste nell'aprire un prompt dei comandi come amministratore e passare al percorso in cui è stato copiato il file di installazione.</li>  

<li>Eseguire il file di installazione a più fattori AuthenticationMobileAppWebServiceSetup, se lo si desidera modificare il sito e passare alla directory virtuale un nome breve, ad esempio "PA". Dal momento che gli utenti devono immettere l'URL del servizio Web App Mobile nel dispositivo mobile durante l'attivazione, si consiglia un nome breve directory virtuale.</li>

<li>Dopo aver completato l'installazione di AuthenticationMobileAppWebServiceSetup di a più fattori Azure, passare al C:\inetpub\wwwroot\PA (o directory appropriata in base al nome di directory virtuale) e modificare il file config.</li>  

<li>Individuare le chiavi WEB_SERVICE_SDK_AUTHENTICATION_USERNAME e WEB_SERVICE_SDK_AUTHENTICATION_PASSWORD e impostare i valori per il nome utente e la password dell'account del servizio che fa parte di protezione PhoneFactor Admins (vedere la sezione requisiti). È possibile lo stesso account utilizzato come l'identità del portale di Azure a più fattori autenticazione utente se che è stato installato in precedenza. Assicurarsi di immettere il nome utente e Password tra le virgolette semplici alla fine della riga, (valore = "" / >). È consigliabile utilizzare un nome completo (ad esempio DOMINIO\nome utente o computer ome utente).</li>  

<li>Individuare l'impostazione di App Web Service_pfwssdk_PfWsSdk pfMobile e modificare il valore di "http://localhost:4898/PfWsSdk.asmx" l'URL del servizio Web SDK che è in esecuzione sul Server di autenticazione a più fattori Azure (ad esempio https://computer1.domain.local/MultiFactorAuthWebServiceSdk/PfWsSdk.asmx). Poiché si utilizza SSL per la connessione, è necessario fare riferimento Web Service SDK dal nome del server e non l'indirizzo IP poiché il certificato SSL venga rilasciato per il nome del server e l'URL utilizzato deve corrispondere al nome del certificato. Se il nome del server non viene risolto in un indirizzo IP dal server esposto a Internet, aggiungere una voce al file hosts del server per mappare il nome del Server di autenticazione a più fattori Azure l'indirizzo IP. Salvare il file config dopo che sono state apportate modifiche.</li>  

<li>Se il sito Web che il servizio Web App Mobile è stato installato in (ad esempio sito Web predefinito) non è già stata binded con un certificato di firma al pubblico, installare il certificato nel server in caso contrario già installato, aprire Gestione IIS e associare il certificato al sito Web.</li>  

<li>Aprire un web browser da qualsiasi computer e passare all'URL in cui è stato installato il servizio Web App Mobile (ad esempio https://www.publicwebsite.com/PA). Assicurarsi che non vengano visualizzati avvisi certificato o errori.</li>

### <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurare le impostazioni di app per dispositivi mobili nel Server di autenticazione a più fattori Azure
Ora che è installato il servizio web app per dispositivi mobili, è necessario configurare il Server di autenticazione a più fattori Azure per lavorare con il portale.

#### <a name="to-configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Per configurare le impostazioni di app per dispositivi mobili nel Server di autenticazione a più fattori Azure

1. Nel Server di autenticazione a più fattori Azure, fare clic sull'icona User Portal. Se gli utenti possono controllare i metodi di autenticazione, nella scheda Impostazioni, in Consenti agli utenti di selezionare metodo, selezionare App Mobile. Senza questa funzionalità è attivata, gli utenti finali sarà richiesto di contattare l'Help Desk per completare l'attivazione per l'App Mobile.
2. Selezionare Consenti agli utenti per attivare la casella di App Mobile.
3. Selezionare la casella Consenti registrazione utente.
4. Fare clic sull'icona App Mobile.
5. Immettere l'URL con la directory virtuale che è stata creata durante l'installazione di AuthenticationMobileAppWebServiceSetup a più fattori Azure viene utilizzato. Il nome di un Account può essere immesse nell'apposita casella. Verrà visualizzato il nome della società nell'applicazione di dispositivi mobili. Se omesso, verrà visualizzato il nome del Provider di autenticazione a più fattori creato nel portale di gestione di Azure.



<center>![Programma di installazione](./media/multi-factor-authentication-get-started-server-webservice/mobile.png)</center>
