<properties
   pageTitle="Creare dell'entità servizio Azure con PowerShell | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare Azure PowerShell per creare un'applicazione di Active Directory e principale del servizio e concedere l'accesso alle risorse tramite il controllo dell'accesso basato sui ruoli. Viene illustrato come eseguire l'autenticazione dell'applicazione con una password o certificato."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/12/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-powershell-to-create-a-service-principal-to-access-resources"></a>Usare PowerShell Azure per creare un'entità servizio per accedere alle risorse

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)

Quando si dispone di un'applicazione o script che è necessario accedere alle risorse, non è probabile che si desidera eseguire il processo con le proprie credenziali. Si potrebbe avere autorizzazioni diverse desiderata per l'applicazione e non si desidera un'applicazione di continuare a usare le credenziali se le responsabilità cambiano. Se, tuttavia, si crea un'identità dell'applicazione che include le credenziali di autenticazione e assegnazioni di ruolo. Ogni volta che viene eseguita l'app, esegue l'autenticazione con le credenziali. In questo argomento viene illustrato come utilizzare [Azure PowerShell](powershell-install-configure.md) per impostare tutte le informazioni che necessarie per un'applicazione per l'esecuzione con le proprie credenziali e identità.

Con PowerShell, sono disponibili due opzioni per l'autenticazione dell'applicazione di Active Directory:

 - password
 - certificato

In questo argomento viene illustrato come utilizzare entrambe le opzioni in PowerShell. Se si prevede di accedere a Azure da un framework di programmazione (ad esempio Python, trascrizione o Node), autenticazione tramite password potrebbe essere la soluzione migliore. Prima di decidere se usare una password o certificato, vedere la sezione [applicazioni di esempio](#sample-applications) per alcuni esempi di autenticazione Framework diversi.

## <a name="active-directory-concepts"></a>Concetti relativi ad Active Directory

In questo articolo si creano due oggetti - l'applicazione di Active Directory (AD) e il servizio principale. L'applicazione di Active Directory è la rappresentazione globale dell'applicazione. La presentazione contiene le credenziali (un id di applicazione e una password o certificato). Il servizio principale è la rappresentazione dell'applicazione in Active Directory locale. Contiene l'assegnazione di ruolo. In questo argomento è incentrata su un'applicazione singola tenant nel punto in cui l'applicazione deve essere eseguita solo l'organizzazione. Applicazioni single-tenant viene generalmente utilizzata per le applicazioni line-of-business eseguite all'interno dell'organizzazione. In un'applicazione singola tenant, si dispone di un'applicazione di Active Directory e capitale di un servizio.

Per comprendere - perché è necessario entrambi gli oggetti? Questo approccio ha più senso quando è prendere in considerazione le applicazioni multi-tenant. È in genere utilizzare applicazioni multi-tenant Applications (SaaS) software come servizio in cui l'applicazione viene eseguita in molti diversi abbonamenti. Per le applicazioni multi-tenant, si dispone di un'applicazione di Active Directory e più identità di servizio (uno in ogni Active Directory che consente l'accesso all'app). Per configurare un'applicazione multi-tenant, vedere [la Guida per gli sviluppatori di autorizzazione con l'API di gestione risorse Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per completare questo argomento, è necessario disporre di autorizzazioni sufficienti in Azure Active Directory e l'abbonamento Azure. In particolare, è necessario essere possibile creare un'app in Active Directory e assegnare un ruolo principale del servizio. 

In Active Directory, l'account deve essere un amministratore (ad esempio, **L'amministratore globale** o **Amministratore utenti**). Se l'account è assegnato il ruolo di **utente** , è necessario che un amministratore elevare le autorizzazioni.

Nell'abbonamento, è necessario disporre l'account `Microsoft.Authorization/*/Write` accesso, viene concesso tramite il ruolo di [proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore di accesso utente](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se l'account è assegnato il ruolo di **Collaboratore** , verrà visualizzato un messaggio di errore quando si tenta di assegnare un ruolo principale del servizio. Nuovo amministratore abbonamento conceda l'accesso sufficienti.

A questo punto, passare a una sezione per l'autenticazione [password](#create-service-principal-with-password) o [certificato](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Creare servizio dell'entità con password

In questa sezione, attenersi alla procedura per:

- creare l'applicazione di Active Directory con una password
- creare principale del servizio
- assegnare il ruolo di lettore a principale del servizio

Per eseguire rapidamente questa procedura, vedere i cmdlet seguenti tre. 

     $app = New-AzureRmADApplication -DisplayName "{app-name}" -HomePage "https://{your-domain}/{app-name}" -IdentifierUris "https://{your-domain}/{app-name}" -Password "{your-password}"
     New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
     New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Questo articolo verranno illustrate le operazioni con attenzione per assicurarsi che comprendere il processo.

1. Accedere al proprio account.

        Add-AzureRmAccount

1. Creare una nuova applicazione di Active Directory fornendo un nome visualizzato, l'URI che descrive l'applicazione, URI che identificano l'applicazione e la password per l'identità dell'applicazione.

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org/exampleapp" -IdentifierUris "https://www.contoso.org/exampleapp" -Password "<Your_Password>"

     Per le applicazioni single-tenant, gli URI non vengono convalidati.
     
     Se l'account non dispone delle [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica "Authentication_Unauthorized" o "sottoscrizione non trovata nel contesto".

1. Esaminare il nuovo oggetto applicazione. 

        $app
        
     Tenere presente la proprietà **ID applicazione** , è necessario per la creazione di identità di servizio, le assegnazioni di ruolo e durante l'acquisizione di token di accesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}

2. Creare un'entità servizio per l'applicazione passando l'id dell'applicazione di Active Directory applicazione.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

3. Concedere le autorizzazioni dell'entità servizio per l'abbonamento. In questo esempio, aggiungere il capitale servizio il ruolo di **lettore, che concede l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione** . Per altri ruoli, vedere [RBAC: ruoli incorporati](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName** , fornire l' **ID applicazione** utilizzato per la creazione dell'applicazione. 

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se l'account non dispone delle autorizzazioni sufficienti per assegnare un ruolo, viene visualizzato un messaggio di errore. Il messaggio indica l'account **non dispone di autorizzazioni per eseguire l'operazione 'Microsoft.Authorization/roleAssignments/write' ambito ' / abbonamenti / {guid}'**. 

Questo è tutto! L'applicazione di Active Directory e principale del servizio siano configurati. La sezione seguente viene illustrato come accedere con le credenziali tramite PowerShell. Se si desidera utilizzare le credenziali dell'applicazione di codice, è possibile passare alle [applicazioni di esempio](#sample-applications). 

### <a name="provide-credentials-through-powershell"></a>Le credenziali tramite PowerShell

A questo punto, è necessario eseguire l'accesso come l'applicazione di eseguire operazioni.

1. Creare un oggetto **PSCredential** che contiene le credenziali eseguendo il comando **Get-Credential** . È necessario **ID applicazione** prima di eseguire questo comando dunque, verificare di avere disponibili per incollare.

        $creds = Get-Credential

2. Verrà richiesto di immettere le credenziali. Per il nome utente, utilizzare l' **ID applicazione** utilizzato per la creazione dell'applicazione. Per la password, utilizzare quello specificato quando si crea l'account.

     ![Immettere le credenziali](./media/resource-group-authenticate-service-principal/arm-get-credential.png)

2. Ogni volta che si accede come entità di servizio, è necessario fornire l'id di tenant della directory per l'app di Active Directory. Un tenant è un'istanza di Active Directory. Se si dispone di una sottoscrizione, è possibile usare:

        $tenant = (Get-AzureRmSubscription).TenantId
    
     Se si dispone di più di una sottoscrizione, specificare l'abbonamento in cui si trova in Active Directory. Per ulteriori informazioni, vedere [come Azure sottoscrizioni associate Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

        $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

4. Eseguire l'accesso come principale del servizio specificando che l'account è un'entità di servizio e fornendo l'oggetto credenziali. 

        Add-AzureRmAccount -Credential $creds -ServicePrincipal -TenantId $tenant
        
     A questo punto è autenticato come il servizio principale per l'applicazione di Active Directory è stato creato.

### <a name="save-access-token-to-simplify-log-in"></a>Salvare token di accesso per semplificare l'accesso

Per evitare di fornire le credenziali dell'entità servizio ogni volta che è necessario effettuare l'accesso, è possibile salvare il token di accesso.

1. Per utilizzare il token di accesso corrente in una sessione successiva, salvare il profilo.

        Save-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
     Aprire il profilo ed esaminarne il contenuto. Si noti che include un token di accesso. 
        
2. Anziché manualmente effettuare l'accesso, è sufficiente caricare il profilo.

        Select-AzureRmProfile -Path c:\Users\exampleuser\profile\exampleSP.json
        
> [AZURE.NOTE] Il token di accesso scade, usando un profilo salvato funziona solo per purché il token è valido.
        
## <a name="create-service-principal-with-certificate"></a>Creare servizio dell'entità con certificato

In questa sezione, attenersi alla procedura per:

- creare un certificato autofirmato
- creare l'applicazione di Active Directory con il certificato
- creare principale del servizio
- assegnare il ruolo di lettore a principale del servizio

Per eseguire rapidamente queste operazioni con Azure PowerShell 2.0 in Windows 10 o Windows Server 2016 Technical Preview, vedere i cmdlet seguenti. 

    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore
    New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
    New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

Questo articolo verranno illustrate le operazioni con attenzione per assicurarsi che comprendere il processo. In questo articolo viene illustrato anche eseguire le attività quando si utilizzano versioni precedenti di Azure PowerShell o sistemi operativi.

### <a name="create-the-self-signed-certificate"></a>Creare il certificato autofirmato

La versione di PowerShell disponibili con Windows 10 e Windows Server 2016 Technical Preview include un cmdlet **New-SelfSignedCertificate** aggiornati per la generazione di un certificato autofirmato. Nei sistemi operativi precedenti, i cmdlet New-SelfSignedCertificate ma non offre i parametri necessari per questo argomento. Se, tuttavia, è necessario importare un modulo per generare il certificato. In questo argomento viene entrambi gli approcci per generare il certificato in base al sistema operativo, che si dispone. 

- Se si usa **Windows 10 o Windows Server 2016 Technical Preview**, eseguire il seguente comando per creare un certificato autofirmato: 

        $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=exampleapp" -KeySpec KeyExchange
       
- Se si **dispone di Windows 10 o Windows Server 2016 Technical Preview**, è necessario scaricare [autofirmato generatore certificato](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) da Microsoft Script Center. Estrarre il contenuto e importare il cmdlet che è necessario.
     
        # Only run if you could not use New-SelfSignedCertificate
        Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
    
     Generare il certificato.
    
        $cert = New-SelfSignedCertificateEx -Subject "CN=exampleapp" -KeySpec "Exchange" -FriendlyName "exampleapp"

Si dispone del certificato e si può procedere con la creazione di un'applicazione di Active Directory.

### <a name="create-the-active-directory-app-and-service-principal"></a>Creare il servizio e Active Directory app principale

1. Recuperare il valore della chiave del certificato.

        $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

2. Accedere al proprio account Azure.

        Add-AzureRmAccount

3. Creare una nuova applicazione di Active Directory fornendo un nome visualizzato, l'URI che descrive l'applicazione, URI che identificano l'applicazione e la password per l'identità dell'applicazione.

     Se si dispone di Azure PowerShell 2.0 usare (agosto 2016 o versioni successive), il seguente cmdlet:

        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Se si dispone di Azure PowerShell 1.0, utilizzare il cmdlet seguente:
    
        $app = New-AzureRmADApplication -DisplayName "exampleapp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.contoso.org/example" -KeyValue $keyValue -KeyType AsymmetricX509Cert  -EndDate $cert.NotAfter -StartDate $cert.NotBefore      
    
    Per le applicazioni single-tenant, gli URI non vengono convalidati.
    
    Se l'account non dispone delle [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica "Authentication_Unauthorized" o "sottoscrizione non trovata nel contesto".
        
    Esaminare il nuovo oggetto applicazione. 

        $app

    Si noti la proprietà **ID applicazione** , è necessario per la creazione di identità di servizio, le assegnazioni di ruolo e durante l'acquisizione di token di accesso.

        DisplayName             : exampleapp
        ObjectId                : c95e67a3-403c-40ac-9377-115fa48f8f39
        IdentifierUris          : {https://www.contoso.org/example}
        HomePage                : https://www.contoso.org
        Type                    : Application
        ApplicationId           : 8bc80782-a916-47c8-a47e-4d76ed755275
        AvailableToOtherTenants : False
        AppPermissions          : 
        ReplyUrls               : {}


5. Creare un'entità servizio per l'applicazione passando l'id dell'applicazione di Active Directory applicazione.

        New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId

6. Concedere le autorizzazioni dell'entità servizio per l'abbonamento. In questo esempio, aggiungere il capitale servizio il ruolo di **lettore, che concede l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione** . Per altri ruoli, vedere [RBAC: ruoli incorporati](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName** , fornire l' **ID applicazione** utilizzato per la creazione dell'applicazione.

        New-AzureRmRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $app.ApplicationId.Guid

    Se l'account non dispone delle autorizzazioni sufficienti per assegnare un ruolo, viene visualizzato un messaggio di errore. Il messaggio indica l'account **non dispone di autorizzazioni per eseguire l'operazione 'Microsoft.Authorization/roleAssignments/write' ambito ' / abbonamenti / {guid}'**.

Questo è tutto! L'applicazione di Active Directory e principale del servizio siano configurati. La sezione seguente viene illustrato come accedere con certificato tramite PowerShell.

### <a name="provide-certificate-through-automated-powershell-script"></a>Fornire certificato tramite script di PowerShell automatico

Ogni volta che si accede come entità di servizio, è necessario fornire l'id di tenant della directory per l'app di Active Directory. Un tenant è un'istanza di Active Directory. Se si dispone di una sottoscrizione, è possibile usare:

    $tenant = (Get-AzureRmSubscription).TenantId
    
Se si dispone di più di una sottoscrizione, specificare l'abbonamento in cui si trova in Active Directory. Per ulteriori informazioni, vedere [amministrazione directory Azure Active Directory](./active-directory/active-directory-administer.md).

    $tenant = (Get-AzureRmSubscription -SubscriptionName "Contoso Default").TenantId

Per eseguire l'autenticazione in uno script, specificare l'account è un servizio principale e fornire identificazione personale del certificato, l'id dell'applicazione e id tenant. Per automatizzare uno script, è possibile archiviare questi valori come variabili e recuperarli durante l'esecuzione oppure è possibile includerli dello script.

    Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint $cert.Thumbprint -ApplicationId $app.ApplicationId -TenantId $tenant

A questo punto è autenticato come il servizio principale per l'applicazione di Active Directory è stato creato.

## <a name="sample-applications"></a>Applicazioni di esempio

Le applicazioni di esempio seguente viene illustrato come eseguire l'accesso come principale del servizio.

**.NET**

- [Distribuire un SSH abilitato macchine Virtuali con un modello con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gestire le risorse Azure e gruppi di risorse con .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Guida introduttiva di risorse - distribuire utilizzo dei modelli di gestione risorse Azure - Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Guida introduttiva di risorse - Gestione gruppo risorse - Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Distribuire un SSH abilitato macchine Virtuali con un modello di Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestione risorse Azure e gruppi di risorse con Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node**

- [Distribuire un SSH abilitato macchine Virtuali con un modello di Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gestire le risorse Azure e gruppi di risorse con Node](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Trascrizione**

- [Distribuire un SSH abilitato macchine Virtuali con un modello di trascrizione](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestione risorse Azure e gruppi di risorse con trascrizione](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)

## <a name="next-steps"></a>Passaggi successivi
  
- Per informazioni dettagliate sull'integrazione di un'applicazione in Azure per la gestione delle risorse, vedere [la Guida per gli sviluppatori di autorizzazione con l'API di gestione risorse Azure](resource-manager-api-authentication.md).
- Per ulteriori informazioni su applicazioni e identità di servizio, vedere [gli oggetti dell'applicazione e principale del servizio](./active-directory/active-directory-application-objects.md). 
- Per ulteriori informazioni sull'autenticazione Active Directory, vedere [Scenari di autenticazione per Azure Active Directory](./active-directory/active-directory-authentication-scenarios.md).



