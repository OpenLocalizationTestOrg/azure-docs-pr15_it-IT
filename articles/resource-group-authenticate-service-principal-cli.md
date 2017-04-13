<properties
   pageTitle="Crea servizio dell'entità con Azure CLI | Microsoft Azure"
   description="In questo articolo viene descritto come utilizzare Azure CLI per creare un'applicazione di Active Directory e principale del servizio e concedere l'accesso alle risorse tramite il controllo dell'accesso basato sui ruoli. Viene illustrato come eseguire l'autenticazione dell'applicazione con una password o certificato."
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
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Consente di creare un'entità servizio per accedere alle risorse CLI Azure

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [CLI Azure](resource-group-authenticate-service-principal-cli.md)
- [Portale](resource-group-create-service-principal-portal.md)


Quando si dispone di un'applicazione o script che è necessario accedere alle risorse, non è probabile che si desidera eseguire il processo con le proprie credenziali. Si potrebbe avere autorizzazioni diverse desiderata per l'applicazione e non si desidera un'applicazione di continuare a usare le credenziali se le responsabilità cambiano. Se, tuttavia, si crea un'identità dell'applicazione che include le credenziali di autenticazione e assegnazioni di ruolo. Ogni volta che viene eseguita l'app, esegue l'autenticazione con le credenziali. In questo argomento viene illustrato come utilizzare [CLI Azure per Mac, Linux e Windows](xplat-cli-install.md) per configurare un'applicazione venga eseguita con le proprie credenziali e identità.

Con CLI Azure, sono disponibili due opzioni per l'autenticazione dell'applicazione di Active Directory:

 - password
 - certificato

In questo argomento viene illustrato come utilizzare entrambe le opzioni in Azure CLI. Se si prevede di accedere a Azure da un framework di programmazione (ad esempio Python, trascrizione o Node), autenticazione tramite password potrebbe essere la soluzione migliore. Prima di decidere se usare una password o certificato, vedere la sezione [applicazioni di esempio](#sample-applications) per alcuni esempi di autenticazione Framework diversi.

## <a name="active-directory-concepts"></a>Concetti relativi ad Active Directory

In questo articolo si creano due oggetti - l'applicazione di Active Directory (AD) e il servizio principale. L'applicazione di Active Directory è la rappresentazione globale dell'applicazione. La presentazione contiene le credenziali (un id di applicazione e una password o certificato). Il servizio principale è la rappresentazione dell'applicazione in Active Directory locale. Contiene l'assegnazione di ruolo. In questo argomento è incentrata su un'applicazione singola tenant nel punto in cui l'applicazione deve essere eseguita solo l'organizzazione. Applicazioni single-tenant viene generalmente utilizzata per le applicazioni line-of-business eseguite all'interno dell'organizzazione. In un'applicazione singola tenant, si dispone di un'applicazione di Active Directory e capitale di un servizio.

Per comprendere - perché è necessario entrambi gli oggetti? Questo approccio ha più senso quando è prendere in considerazione le applicazioni multi-tenant. È in genere utilizzare applicazioni multi-tenant Applications (SaaS) software come servizio in cui l'applicazione viene eseguita in molti diversi abbonamenti. Per le applicazioni multi-tenant, si dispone di un'applicazione di Active Directory e più identità di servizio (uno in ogni Active Directory che consente l'accesso all'app). Per configurare un'applicazione multi-tenant, vedere [la Guida per gli sviluppatori di autorizzazione con l'API di gestione risorse Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorizzazioni necessarie

Per completare questo argomento, è necessario disporre di autorizzazioni sufficienti in Azure Active Directory e l'abbonamento Azure. In particolare, è necessario essere possibile creare un'app in Active Directory e assegnare un ruolo principale del servizio. 

In Active Directory, l'account deve essere un amministratore (ad esempio, **L'amministratore globale** o **Amministratore utenti**). Se l'account è assegnato il ruolo di **utente** , è necessario che un amministratore elevare le autorizzazioni.

Nell'abbonamento, è necessario disporre l'account `Microsoft.Authorization/*/Write` accesso, viene concesso tramite il ruolo di [proprietario](./active-directory/role-based-access-built-in-roles.md#owner) o [Amministratore di accesso utente](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Se l'account è assegnato il ruolo di **Collaboratore** , verrà visualizzato un messaggio di errore quando si tenta di assegnare un ruolo principale del servizio. Nuovo amministratore abbonamento conceda l'accesso sufficienti.

A questo punto, passare a una sezione per l'autenticazione [password](#create-service-principal-with-password) o [certificato](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Creare servizio dell'entità con password

In questa sezione, eseguire la procedura per creare l'applicazione di Active Directory con una password e assegnare il ruolo di lettore al servizio principale.

Questo articolo verranno illustrate le operazioni seguenti.

1. Accedere al proprio account.

        azure login

1. Sono disponibili due opzioni per la creazione dell'applicazione di Active Directory. È possibile creare l'applicazione di Active Directory e il servizio principale in un unico passaggio o crearli separatamente. Crearle in un unico passaggio se non è necessario specificare una home page e identificatore URI per l'app. Crearli separatamente se è necessario impostare questi valori per un'app web. In questo passaggio vengono visualizzate entrambe le opzioni.

     - Per creare l'applicazione di Active Directory e servizio principale in un unico passaggio, specificare il nome di app e una password, come illustrato nel comando seguente:
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Per creare l'applicazione di Active Directory separatamente, fornire il nome dell'app, una home page URI, identificatore URI e una password, come illustrato nel comando seguente:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         Il comando precedente restituisce un valore AppId. Per creare un'entità servizio, specificare il valore come un parametro per il comando seguente:
     
            azure ad sp create -a <AppId>
     
     Se l'account non dispone delle [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica "Authentication_Unauthorized" o "sottoscrizione non trovata nel contesto".
    
     Per entrambe le opzioni, viene restituita la nuova identità di servizio. L' **ID dell'oggetto** è necessario per la concessione di autorizzazioni. Guid elencato con i **Nomi dell'entità servizio** è necessaria per l'accesso. Questo guid è lo stesso valore di id app. Nelle applicazioni di esempio, questo valore viene definito l' **ID Client**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Concedere le autorizzazioni dell'entità servizio per l'abbonamento. In questo esempio, aggiungere il capitale servizio il ruolo di **lettore, che concede l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione** . Per altri ruoli, vedere [RBAC: ruoli incorporati](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName** fornire **ObjectId** utilizzato per la creazione dell'applicazione. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Se l'account non dispone delle autorizzazioni sufficienti per assegnare un ruolo, viene visualizzato un messaggio di errore. Il messaggio indica l'account **non dispone di autorizzazioni per eseguire l'operazione 'Microsoft.Authorization/roleAssignments/write' ambito ' / abbonamenti / {guid}'**. 

Questo è tutto! L'applicazione di Active Directory e principale del servizio siano configurati. La sezione seguente viene illustrato come accedere con le credenziali tramite CLI Azure. Se si desidera utilizzare le credenziali dell'applicazione di codice, non è necessaria continuare con questo argomento. È possibile passare alle [applicazioni di esempio](#sample-applications) per visualizzare esempi di accesso con l'id dell'applicazione e la password. 

### <a name="provide-credentials-through-azure-cli"></a>Le credenziali tramite CLI Azure

A questo punto, è necessario eseguire l'accesso come l'applicazione di eseguire operazioni.

1. Ogni volta che si accede come entità di servizio, è necessario fornire l'id di tenant della directory per l'app di Active Directory. Un tenant è un'istanza di Active Directory. Per recuperare l'id tenant per l'abbonamento attualmente autenticato, usare:

        azure account show

     Che restituisce:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se è necessario ottenere l'id tenant dell'abbonamento a un altro, usare il comando seguente:

        azure account show -s {subscription-id}

2. Se è necessario recuperare l'id client da utilizzare per l'accesso, usare:

        azure ad sp show -c exampleapp --json

     Il valore da utilizzare per l'accesso è il guid elencato i nomi dell'entità.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Eseguire l'accesso come principale del servizio.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Viene chiesto di immettere la password. Specificare la password specificata durante la creazione dell'applicazione di Active Directory.

        info:    Executing command login
        Password: ********

A questo punto è autenticato come principale del servizio per l'identità di servizio che è stato creato.

## <a name="create-service-principal-with-certificate"></a>Creare servizio dell'entità con certificato

In questa sezione, attenersi alla procedura per:

- creare un certificato autofirmato
- creare l'applicazione di Active Directory con il certificato e principale del servizio
- assegnare il ruolo di lettore a principale del servizio

Per completare questa procedura, è necessario disporre [OpenSSL](http://www.openssl.org/) installato.

1. Creare un certificato autofirmato.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combinare le chiavi pubbliche e privatehttp.

        cat privkey.pem cert.pem > examplecert.pem

3. Aprire il file **examplecert.pem** e cercare la sequenza di caratteri tra **---inizio certificato---** e **---TERMINARE certificato---**lunga. Copiare i dati del certificato. Passare questi dati come parametro quando la creazione del servizio principale.

1. Accedere al proprio account.

        azure login

1. Sono disponibili due opzioni per la creazione dell'applicazione di Active Directory. È possibile creare l'applicazione di Active Directory e il servizio principale in un unico passaggio o crearli separatamente. Create in un unico passaggio se non è necessario specificare una home page e identificatore URI per l'app. Crearli separatamente se è necessario impostare questi valori per un'app web. In questo passaggio vengono visualizzate entrambe le opzioni.

     - Per creare l'applicazione di Active Directory e servizio principale in un unico passaggio, specificare il nome dell'app e i dati del certificato, come illustrato nel comando seguente:
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Per creare l'applicazione di Active Directory separatamente, fornire il nome dell'app, una home page URI, identificatore URI e i dati del certificato, come illustrato nel comando seguente:
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         Il comando precedente restituisce un valore AppId. Per creare un'entità servizio, specificare il valore come un parametro per il comando seguente:
     
            azure ad sp create -a <AppId>
  
     Se l'account non dispone delle [autorizzazioni necessarie](#required-permissions) in Active Directory, viene visualizzato un messaggio di errore che indica "Authentication_Unauthorized" o "sottoscrizione non trovata nel contesto".
    
     Per entrambe le opzioni, viene restituita la nuova identità di servizio. L'Id di oggetto è necessario per la concessione di autorizzazioni. Guid elencato con i **Nomi dell'entità servizio** è necessaria per l'accesso. Questo guid è lo stesso valore di id app. Nelle applicazioni di esempio, questo valore viene definito l' **ID Client**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Concedere le autorizzazioni dell'entità servizio per l'abbonamento. In questo esempio, aggiungere il capitale servizio il ruolo di **lettore, che concede l'autorizzazione per la lettura di tutte le risorse nella sottoscrizione** . Per altri ruoli, vedere [RBAC: ruoli incorporati](./active-directory/role-based-access-built-in-roles.md). Per il parametro **ServicePrincipalName** fornire **ObjectId** utilizzato per la creazione dell'applicazione. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Se l'account non dispone delle autorizzazioni sufficienti per assegnare un ruolo, viene visualizzato un messaggio di errore. Il messaggio indica l'account **non dispone di autorizzazioni per eseguire l'operazione 'Microsoft.Authorization/roleAssignments/write' ambito ' / abbonamenti / {guid}'**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fornire certificato tramite script Azure CLI automatico

A questo punto, è necessario eseguire l'accesso come l'applicazione di eseguire operazioni.

1. Ogni volta che si accede come entità di servizio, è necessario fornire l'id di tenant della directory per l'app di Active Directory. Un tenant è un'istanza di Active Directory. Per recuperare l'id tenant per l'abbonamento attualmente autenticato, usare:

        azure account show

     Che restituisce:

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Se è necessario ottenere l'id tenant dell'abbonamento a un altro, usare il comando seguente:

        azure account show -s {subscription-id}

1. Per recuperare l'identificazione personale certificato e rimuovere i caratteri non necessari, usare:

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Che restituisce un valore simile a:

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Se è necessario recuperare l'id client da utilizzare per l'accesso, usare:

        azure ad sp show -c exampleapp

     Il valore da utilizzare per l'accesso è il guid elencato i nomi dell'entità.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Eseguire l'accesso come principale del servizio.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

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
- Per ottenere ulteriori informazioni sull'utilizzo dei certificati e Azure CLI, vedere [l'autenticazione basata su certificato con Azure servizio identità dalla riga di comando Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 
