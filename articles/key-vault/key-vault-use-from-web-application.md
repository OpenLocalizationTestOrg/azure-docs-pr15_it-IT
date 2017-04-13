<properties
    pageTitle="Utilizzare Azure archivio chiave da un'applicazione Web | Microsoft Azure"
    description="Utilizzare questa esercitazione per imparare a utilizzare Azure chiave archivio da un'applicazione web."
    services="key-vault"
    documentationCenter=""
    authors="adhurwit"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-azure-key-vault-from-a-web-application"></a>Utilizzare Azure archivio chiave da un'applicazione Web #

## <a name="introduction"></a>Introduzione  
Utilizzare questa esercitazione per imparare a utilizzare Azure chiave archivio da un'applicazione web in Azure. Illustra il processo di accesso a un segreto da un archivio di chiave Azure in modo che può essere utilizzato nell'applicazione web.

**Durata stimata per completare:** 15 minuti


Per informazioni generali su Azure chiave archivio, vedere [Novità Azure chiave archivio?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- URI su un segreto in un archivio di chiave Azure
- Un ID Client e un segreto Client per un'applicazione web registrato con Azure Active Directory non ha accesso all'archivio di chiave
- Un'applicazione web. Abbiamo con la procedura per un'applicazione Asp.net distribuita in Azure come un'App Web.

> [AZURE.NOTE]  È importante che sia stata completata la procedura indicata in [Guida introduttiva di Azure chiave archivio](key-vault-get-started.md) per questa esercitazione in modo da avere URI un segreto e ID Client e segreto del Client per un'applicazione web.

L'applicazione web che dovranno accedere archivio chiave è quello che viene registrata Azure Active Directory ed è stato concesso l'accesso per l'archivio di chiave. Se il caso non, tornare ai registri un'applicazione in questa esercitazione per iniziare e ripetere i passaggi elencati.

In questa esercitazione è progettata per gli sviluppatori web riconoscibili nozioni fondamentali sulla creazione di applicazioni web in Azure. Per ulteriori informazioni su Azure Web Apps, vedere [Panoramica sul Web Apps](../app-service-web/app-service-web-overview.md).



## <a id="packages"></a>Aggiungere pacchetti Nuget ##
Sono disponibili due pacchetti che deve avere installato l'applicazione web.

- Active Directory Authentication Library - contiene i metodi per interagire con Azure Active Directory e gestione delle identità utente
- Raccolta di archivio di Azure chiave - contiene metodi per interagire con Azure chiave archivio


Entrambi i pacchetti può essere installati mediante la Console di gestione di pacchetto con il comando pacchetto di installazione.

    // this is currently the latest stable version of ADAL
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202

    Install-Package Microsoft.Azure.KeyVault


## <a id="webconfig"></a>Modificare config ##
Esistono tre impostazioni applicazione che è necessario aggiungere per config come indicato di seguito.

    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />


Se non si intende pubblicare l'applicazione come un'App Web di Azure, è necessario aggiungere i valori effettivi ClientId segreto del Client e segreto URI a config. In caso contrario lasciare questi valori fittizi perché verranno aggiunti i valori effettivi nel portale di Azure per un livello di sicurezza aggiuntivo.


## <a id="gettoken"></a>Aggiungere metodo per ottenere Token di accesso ##
Per utilizzare l'API di archivio di chiave è necessario un token di accesso. Il Client di archivio di chiave gestisce le chiamate all'API di archivio di chiave, ma è necessario fornire con una funzione che ottiene il token di accesso.  

Di seguito è il codice per ottenere un accesso token da Azure Active Directory. Questo codice è possibile passare in un punto qualsiasi dell'applicazione. Desidera aggiungere una classe utilità o EncryptionHelper.  

    //add these using statements
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System.Threading.Tasks;
    using System.Web.Configuration;

    //this is an optional property to hold the secret after it is retrieved
    public static string EncryptSecret { get; set; }

    //the method that will be provided to the KeyVaultClient
    public static async Task<string> GetToken(string authority, string resource, string scope)
    {
        var authContext = new AuthenticationContext(authority);
        ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                    WebConfigurationManager.AppSettings["ClientSecret"]);
        AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

        if (result == null)
            throw new InvalidOperationException("Failed to obtain the JWT token");

        return result.AccessToken;
    }

> [AZURE.NOTE] 
> Utilizzo di un ID Client e segreto del Client è il modo più semplice per eseguire l'autenticazione di un'applicazione di Azure Active Directory. E usarlo in un'applicazione web consente una separazione dei compiti e un maggiore controllo la gestione delle chiavi. Ma basarsi su inserimento segreto del Client nelle impostazioni di configurazione che per alcuni possono essere come rischiose come installare il segreto che si desidera proteggere nelle impostazioni di configurazione. Per informazioni su come utilizzare un ID Client e certificato anziché ID Client e segreto del Client per autenticare l'applicazione di Azure Active Directory, vedere di seguito.



## <a id="appstart"></a>Recuperare il segreto avviare l'applicazione ##
A questo punto è necessario codice per chiamare l'API di archivio di chiave e recuperare il segreto. Il codice riportato di seguito possono essere inseriti in un punto qualsiasi, purché chiamarlo prima che sia necessario utilizzarlo. È stata inserita questo codice in caso di avvio dell'applicazione in asax in modo che viene eseguito una volta nella schermata start e rende il segreto disponibile per l'applicazione.

    //add these using statements
    using Microsoft.Azure.KeyVault;
    using System.Web.Configuration;

    // I put my GetToken method in a Utils class. Change for wherever you placed your method.
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

    var sec = kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]).Result.Value;

    //I put a variable in a Utils class to hold the secret for general  application use.
    Utils.EncryptSecret = sec;



## <a id="portalsettings"></a>Aggiungere le impostazioni dell'App nel portale di Azure (facoltativo) ##
Se si dispone di un'App Web di Azure è ora possibile aggiungere i valori effettivi per AppSettings nel portale di Azure. In questo modo, i valori effettivi non saranno dello stesso file ma protetto tramite il portale nel punto in cui si dispone di funzionalità di controllo di accesso separato. Questi valori verranno sostituiti i valori che è stata immessa la config. Assicurarsi che i nomi siano lo stesso.

![Impostazioni dell'applicazione visualizzate nel portale di Azure][1]


## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Eseguire l'autenticazione con un certificato anziché un segreto Client
Un altro modo per eseguire l'autenticazione di un'applicazione di Azure Active Directory è utilizzando un ID Client e un certificato anziché un ID Client e il segreto del Client. Ecco la procedura per usare un certificato in un'App Web di Azure:

1. Recupero o creazione di un certificato
2. Associare il certificato a un'applicazione di Azure Active Directory
3. Aggiungere codice all'App Web di utilizzare il certificato
4. Aggiungere un certificato a un'applicazione Web


**Recupero o creazione di un certificato** Ai fini è consentono di rendere un certificato di prova. Ecco un paio di comandi che è possibile usare in un Prompt dei comandi di sviluppo per creare un certificato. Spostarsi nel punto in cui si desidera i file di certificato da creare.

    makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2015 -e 07/31/2016 -r
    pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123

Prendere nota della data di fine e la password per il PFX (in questo esempio: 07/31/2016 e test123). Saranno necessari riportata di seguito.

Per ulteriori informazioni sulla creazione di un certificato di prova, vedere [procedura: creare il certificato di prova personalizzato](https://msdn.microsoft.com/library/ff699202.aspx)


**Associare il certificato con un'applicazione di Azure Active Directory** Dopo aver creato un certificato, è necessario associare a un'applicazione di Azure Active Directory. Ma il portale di gestione di Azure non supporta questa immediatamente. È necessario usare Powershell. Di seguito sono i comandi che è necessario eseguire:

    $x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2

    PS C:\> $x509.Import("C:\data\KVWebApp.cer")

    PS C:\> $credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())

    PS C:\> $now = [System.DateTime]::Now

    # this is where the end date from the cert above is used
    PS C:\> $yearfromnow = [System.DateTime]::Parse("2016-07-31")

    PS C:\> $adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -KeyValue $credValue -KeyType "AsymmetricX509Cert" -KeyUsage "Verify" -StartDate $now -EndDate $yearfromnow

    PS C:\> $sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId

    PS C:\> Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName $sp.ServicePrincipalName -PermissionsToSecrets all -ResourceGroupName 'contosorg'

    # get the thumbprint to use in your app settings
    PS C:\>$x509.Thumbprint

Dopo avere eseguito questi comandi, è possibile visualizzare l'applicazione in Azure Active Directory. Se non viene visualizzato l'applicazione prima di tutto, cercare "Applicazioni appartiene alla società" anziché "Applicazioni azienda utilizza".

Per ulteriori informazioni sugli oggetti ServicePrincipal e gli oggetti applicazione di Azure Active Directory, vedere [gli oggetti dell'applicazione e principale del servizio](../active-directory/active-directory-application-objects.md)



**Aggiungere codice all'App Web di utilizzare il certificato** Verranno aggiunti codice all'App Web accesso il certificato e usarlo per l'autenticazione.

Prima di tutto è codice per accedere il certificato.

    public static class CertificateHelper
    {
        public static X509Certificate2 FindCertificateByThumbprint(string findValue)
        {
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            try
            {
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                    findValue, false); // Don't validate certs, since the test root isn't installed.
                if (col == null || col.Count == 0)
                    return null;
                return col[0];
            }
            finally
            {
                store.Close();
            }
        }
    }


Si noti che StoreLocation CurrentUser anziché LocalMachine. E che si sta fornendo "false" per il metodo di ricerca poiché si sta utilizzando un certificato di prova.


È quindi codice che utilizza la CertificateHelper e crea un ClientAssertionCertificate necessaria per l'autenticazione.

    public static ClientAssertionCertificate AssertionCert { get; set; }

    public static void GetCert()
    {
        var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
        AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
    }


Ecco il nuovo codice per ottenere il token di accesso. Sostituisce il metodo GetToken precedente. È stata concessa è un nome diverso per comodità.

    public static async Task<string> GetAccessToken(string authority, string resource, string scope)
    {
        var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
        var result = await context.AcquireTokenAsync(resource, AssertionCert);
        return result.AccessToken;
    }

È stata inserita tutto il codice nella classe di utilità dei progetti Web App per semplificarne l'utilizzo.

L'ultima modifica di codice è nel metodo Application_Start. Prima di tutto è necessario chiamare il metodo GetCert() per caricare il ClientAssertionCertificate. E quindi si cambia il metodo che viene fornito quando si crea un nuovo KeyVaultClient. Si noti che questa impostazione sostituisce il codice utilizzato in precedenza in precedenza.

    Utils.GetCert();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));


**Aggiungere un certificato da un'applicazione Web tramite il portale di Azure** Aggiunta di un certificato a un'applicazione Web è un processo in due semplici passaggi. Prima di tutto, accedere al portale di Azure e passare a un'applicazione Web. Della pala di impostazioni per un'App Web, fare clic sulla voce per "domini personalizzati e SSL". In e che si apre il potranno caricare il certificato creato in precedenza, KVWebApp.pfx, che è importante ricordare la password per il pfx.

![Aggiunta di un certificato per un'App Web nel portale di Azure][2]


L'ultimo elemento che si vuole semplicemente consiste nell'aggiungere un'impostazione dell'applicazione all'applicazione Web che contiene il sito Web nome\_carico\_certificati e il valore *. In questo modo che tutti i certificati vengono caricati. Se si desidera caricare solo i certificati caricati, è possibile immettere un elenco delimitato da virgole di loro identificazioni personali.

Per ulteriori informazioni sull'aggiunta di un certificato a un'App Web, vedere [Uso dei certificati nelle applicazioni di siti Web di Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)


**Aggiungere un certificato all'archivio di chiave come segreto** Invece di caricare il certificato direttamente al servizio Web App, è possibile archiviare nell'archivio di chiave come segreto e distribuire da tale posizione. Si tratta di un processo in due passaggi che è delineato da un post del blog [La distribuzione di Azure Web App certificato tramite chiave archivio](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)



## <a id="next"></a>Passaggi successivi ##


Per la programmazione di riferimenti, vedere [Azure chiave archivio c# Client API informazioni di riferimento](https://msdn.microsoft.com/library/azure/dn903628.aspx).


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
