
<properties
   pageTitle="Creare un cluster di servizio tessuti sicuro Gestione risorse di Azure | Microsoft Azure"
   description="In questo articolo viene descritto come configurare un cluster di servizio tessuti sicuro in Azure mediante Gestione risorse di Azure, archivio chiave Azure e Azure Active Directory (AAD) per l'autenticazione client."
   services="service-fabric"
   documentationCenter=".net"
   authors="chackdan"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="vturecek"/>

# <a name="create-a-service-fabric-cluster-in-azure-using-azure-resource-manager"></a>Creare un cluster di servizio tessuti in Azure Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md)
- [Portale di Azure](service-fabric-cluster-creation-via-portal.md)

Si tratta di una Guida dettagliata che illustra i passaggi della configurazione di un cluster di Azure servizio tessuti sicuro in Azure Gestione risorse di Azure. Questa guida viene illustrata la procedura seguente:

 - Configurare archivio chiave per la gestione delle chiavi per la sicurezza cluster e dell'applicazione.
 - Creare un cluster protetto in Azure Gestione risorse Azure.
 - Autenticazione degli utenti con Azure Active Directory (AAD) per la gestione del cluster.

Un cluster sicuro è un cluster che impedisce l'accesso non autorizzato alle operazioni di gestione, che include la distribuzione, aggiornamento ed eliminazione di applicazioni, servizi e i dati che contengono. Un cluster non protetto è una che chiunque può connettersi a in qualsiasi momento ed eseguire operazioni di gestione. Sebbene sia possibile creare un cluster non protetto, è **consigliabile per creare un cluster protetto**. Un cluster non protetta **non possono essere protetti in un secondo momento** , è necessario creare un nuovo cluster.

I concetti sono gli stessi per la creazione di cluster sicuro, se i cluster sono Linux cluster o cluster di Windows. Per ulteriori script informazioni e supporto per la creazione di cluster Linux sicuro, vedere [creazione cluster sicuro su Linux](#secure-linux-clusters)

## <a name="log-in-to-azure"></a>Accedere a Azure
Questa guida utilizza [Azure PowerShell][azure-powershell]. Quando si avvia una nuova sessione di PowerShell, accedere al proprio account Azure e selezionare l'abbonamento prima di eseguire comandi Azure.

Accedere al proprio account azure:

```powershell
Login-AzureRmAccount
```

Selezionare l'abbonamento:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-key-vault"></a>Configurare la chiave archivio

Questa sezione illustra la creazione di un archivio di chiave per un cluster di servizio tessuti in Azure e per le applicazioni di servizio tessuti. Per una guida completa di archivio di chiave, consultare la [Guida introduttiva di archivio di chiave][key-vault-get-started].

Servizio tessuti utilizza certificati x. 509 per proteggere un cluster e fornire funzionalità di sicurezza dell'applicazione. Archivio chiave Azure viene utilizzato per gestire i certificati per tessuti servizio cluster in Azure. Quando un cluster viene distribuito in Azure, il provider di risorse Azure responsabile della creazione dell'infrastruttura di servizio cluster recupera i certificati dall'archivio chiave e li installa in macchine virtuali di cluster.

Il diagramma seguente illustra la relazione tra chiave archivio, un cluster di tessuti di servizio e il provider di risorse Azure che utilizza certificati archiviati in archivio chiave per la creazione di un cluster:

![Installazione del certificato][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Il primo passaggio consiste nel creare un gruppo di risorse specificamente per chiave archivio. Applicazione pratica di archivio di chiave nel proprio gruppo di risorse è consigliabile. Questa sezione consente di rimuovere i gruppi di risorse elaborazione e di archiviazione, inclusi il gruppo di risorse contenente il cluster tessuti servizio senza perdere le chiavi e informazioni riservate. Gruppo di risorse contenente l'archivio di chiave deve essere nella stessa regione cluster in uso è.

```powershell

    New-AzureRmResourceGroup -Name mycluster-keyvault -Location 'West US'
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    
    ResourceGroupName : mycluster-keyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/mycluster-keyvault

```

### <a name="create-key-vault"></a>Creare un archivio chiave 

Creare un archivio di chiave nel nuovo gruppo di risorse. Il tasto archivio **deve essere abilitata per la distribuzione** per consentire il provider di risorse dell'infrastruttura di servizio ottenere i certificati da essa e installazione nei nodi del cluster:

```powershell

    New-AzureRmKeyVault -VaultName 'myvault' -ResourceGroupName 'mycluster-keyvault' -Location 'West US' -EnabledForDeployment
    
    
    Vault Name                       : myvault
    Resource Group Name              : mycluster-keyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault
    Vault URI                        : https://myvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all
    
    
    Tags                             :
```

Se si dispone di un archivio di chiave esistente, è possibile abilitare per la distribuzione tramite CLI Azure:

```cli
> azure login
> azure account set "your account"
> azure config mode arm 
> azure keyvault list
> azure keyvault set-policy --vault-name "your vault name" --enabled-for-deployment true
```

<a id="add-certificate-to-key-vault"></a>
## <a name="add-certificates-to-key-vault"></a>Aggiungere certificati all'archivio di chiave

I certificati utilizzati nel servizio tessuti per fornire l'autenticazione e la crittografia per proteggere diversi aspetti di un cluster e le applicazioni. Per ulteriori informazioni sull'utilizzo di certificati in tessuti servizio, vedere [scenari di protezione dell'infrastruttura di servizio cluster][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Certificato cluster e server (obbligatorio) 

Sono richieste per proteggere un cluster ed evitare accessi non autorizzati ad esso. Fornisce funzionalità di sicurezza cluster in due modi:
 
 - **Autenticazione cluster:** Comunicazioni a nodi per la federazione cluster l'autenticazione. Solo i nodi che è possano provare la propria identità con questo certificato possono aggiungere al cluster.
 - **Autenticazione server:** Autentica i punti finali Gestione cluster a un client di gestione, in modo che il client management sa che sta parlando al cluster reali. Questo certificato vengono forniti anche SSL per la gestione di HTTPS API e per servizio tessuti Explorer su HTTPS.

Per questi scopi, il certificato deve soddisfare i requisiti seguenti:

 - Il certificato deve contenere una chiave privata.
 - Per lo scambio, esportato in un file di scambio di informazioni personali (PFX), è necessario creare il certificato.
 - Nome del soggetto del certificato deve corrispondere dominio usato per accedere al servizio tessuti cluster. Questo matchng è necessario fornire SSL per il cluster endpoint gestione HTTPS e servizio tessuti Explorer. Non è possibile ottenere un certificato SSL da un'autorità di certificazione (CA) per la `.cloudapp.azure.com` dominio. È necessario acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da un'autorità di certificazione, nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per il cluster.

### <a name="application-certificates-optional"></a>Certificati delle applicazioni (facoltativi)

Qualsiasi numero di certificati aggiuntivi può essere installato in un cluster per motivi di sicurezza dell'applicazione. Prima di creare il cluster, considerare gli scenari di sicurezza di applicazioni che richiedono un certificato per essere installato nei nodi, ad esempio:

 - Crittografia e decrittografia dei valori di configurazione dell'applicazione
 - Crittografia dei dati tra i nodi durante la replica 

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formattazione dei certificati per l'utilizzo di provider di risorse Azure

File di chiave privati (PFX) possono essere aggiunti e utilizzati direttamente dall'archivio di chiave. Tuttavia, il provider di risorse Azure richiede chiavi verrà archiviato in un formato JSON speciale che include. pfx come base 64 codificare stringa e la password della chiave privata. Per adattare questi requisiti, le chiavi devono essere inserite in una stringa JSON e quindi archiviate con *informazioni riservate* nell'archivio di chiave.

Per semplificare il processo, un modulo di PowerShell è [disponibile in GitHub][service-fabric-rp-helpers]. Seguire questa procedura per usare il modulo:

  1. Scaricare l'intero contenuto di repo in una directory locale. 
  2. Importare il modulo nella finestra di PowerShell:

  ```powershell
  PS C:\Users\vturecek> Import-Module "C:\users\vturecek\Documents\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
  ```
     
Il `Invoke-AddCertToKeyVault` comando in questo modulo PowerShell formatta una chiave privata del certificato in una stringa JSON e caricarlo in archivio chiave automaticamente. Usarlo per aggiungere il certificato di cluster e i certificati di applicazione aggiuntivi alla chiave archivio. Ripetere questo passaggio per eventuali altri certificati per installare il cluster.

```powershell
 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName mycluster-keyvault -Location "West US" -VaultName myvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"
    
    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup mycluster-keyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing valut myvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to myvault in vault myvault
    
    
Name  : CertificateThumbprint
Value : <value>

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault

Name  : CertificateURL
Value : https://myvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

Le stringhe precedente sono tutti i prerequisiti di archivio di chiave per la configurazione di un modello di gestione risorse cluster tessuti servizio che consente di installare certificati per l'autenticazione di nodo, gestione endpoint sicurezza e l'autenticazione e le funzionalità di protezione di altre applicazioni che utilizzano i certificati x. 509. A questo punto, è necessario disporre ora le seguenti impostazioni in Azure:

 - Gruppo di risorse chiave archivio
   - Archivio di chiave
     - Certificato di autenticazione server cluster
     - Certificati delle applicazioni

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Configurare Azure Active Directory per l'autenticazione client

AAD consente alle organizzazioni (note come tenant) per gestire l'accesso utente alle applicazioni, suddiviso in applicazioni con un account di accesso dell'interfaccia utente basata sul web e un'esperienza client nativi. In questo documento si presuppone che è già stato creato un tenant. In caso contrario, leggere innanzitutto [come ottenere un tenant di Azure Active Directory][active-directory-howto-tenant].

Un cluster di servizio tessuti offre vari punti di ingresso alla funzionalità di gestione, inclusi basato sul web [Servizio tessuti Explorer] [ service-fabric-visualizing-your-cluster] e [Visual Studio][service-fabric-manage-application-in-visual-studio]. Di conseguenza, si creano due applicazioni AAD per controllare l'accesso al cluster, un'applicazione web e un'applicazione nativa.

Per semplificare alcune dei passaggi della procedura di configurazione AAD con un cluster di tessuti servizio, è stata creata una serie di script di Windows PowerShell.

>[AZURE.NOTE] È necessario eseguire questi passaggi *prima di* creare il cluster anche nei casi in cui gli script aspettarsi cluster nomi e i punti finali, utilizzare i valori pianificati, non quelli che è già stato creato.

1. [Scaricare gli script] [ sf-aad-ps-script-download] nel computer in uso.

2. Fare clic sul file zip, scegliere **proprietà**, quindi selezionare la casella di controllo **Sblocca** e applicare.

3. Estrarre il file zip.

4. Eseguire `SetupApplications.ps1`, fornendo la TenantId, il nome cluster e WebApplicationReplyUrl come parametri. Per esempio:

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    È possibile trovare il **TenantId** eseguendo il comando di PowerShell ' Get-AzureSubscription ' '. Verrà visualizzata **TenantId** per ogni abbonamento.

    **Nome cluster** è utilizzata come prefisso applicazioni AAD create dallo script. Non è necessario corrisponde al nome effettivo cluster esattamente come viene usata solo per rendere più semplice per poter eseguire il mapping di elementi AAD al cluster tessuti servizio che è in uso con.

    **WebApplicationReplyUrl** è l'endpoint predefinito che restituisce AAD agli utenti dopo aver completato la procedura di accesso. È necessario impostare all'endpoint del servizio tessuti Explorer per il cluster che per impostazione predefinita è:

    https://&lt;cluster_domain&gt;: 19080/Explorer

    Viene richiesto di eseguire l'accesso a un account con privilegi di amministratore per il tenant AAD. Una volta eseguita, lo script procede con la creazione di applicazioni web e native per rappresentare il cluster servizio tessuti. Se si osserva applicazioni del tenant nel [portale classica Azure][azure-classic-portal], verrà visualizzato due nuove voci:

    - *Nome cluster*\_Cluster
    - *Nome cluster*\_Client

    Stampa di script Json richiesto dal modello di gestione di risorse Azure quando si crea il cluster nella sezione successiva in modo mantenere PowerShell finestra aperta.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Creare un modello di servizio tessuti cluster Manager delle risorse

In questa sezione, l'output dei comandi PowerShell precedenti vengono utilizzati in un modello di gestione risorse cluster servizio tessuti.

Modelli di Manager delle risorse di esempio sono disponibili nella [raccolta modelli di avvio rapido Azure GitHub][azure-quickstart-templates]. Questi modelli utilizzabili come punto di partenza per il modello di grafico. 

### <a name="create-the-resource-manager-template"></a>Creare il modello di Manager delle risorse

Questa guida utilizza [cluster sicuro 5 nodo] [ service-fabric-secure-cluster-5-node-1-nodetype-wad] modello di esempio e i parametri del modello. Scaricare `azuredeploy.json` e `azuredeploy.parameters.json` al computer e aprire entrambi i file in un editor di testo.

### <a name="add-certificates"></a>Aggiungere i certificati

I certificati vengono aggiunti a un modello di gestione risorse cluster facendo riferimento archivio di chiave che contiene le chiavi certificato. È consigliabile che questi valori chiave archivio vengono inseriti in un file di parametri modello Manager delle risorse per mantenere la gestione di risorse riutilizzabile di file di modello e privo di valori specifici di una distribuzione.

#### <a name="add-all-certificates-to-the-vmss-osprofile"></a>Aggiungere tutti i certificati osProfile VMSS

Tutti i certificati che devono essere installata nel cluster devono essere configurato nella sezione osProfile della risorsa VMSS (Microsoft.Compute/virtualMachineScaleSets). Questo indica il provider di risorse per installare il certificato in macchine virtuali. Sono inclusi il certificato di cluster, nonché i certificati di sicurezza applicazione che si prevede di utilizzare per le applicazioni:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-service-fabric-cluster-certificate"></a>Configurare il certificato cluster tessuti servizio

Il certificato di autenticazione cluster inoltre configurare la risorsa servizio tessuti (Microsoft.ServiceFabric/clusters) e l'estensione del servizio tessuti per VMSS nella risorsa VMSS. In questo modo il provider di risorse dell'infrastruttura di servizio per configurarlo per l'utilizzo per l'autenticazione di raggruppamento e l'autenticazione server per gli endpoint gestione.

##### <a name="vmss-resource"></a>Risorsa VMSS:

```json
{
  "apiVersion": "2016-03-30",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="service-fabric-resource"></a>Risorsa servizio tessuti:

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="insert-aad-config"></a>Inserire AAD config

La configurazione di AAD creata in precedenza può essere inserita direttamente nel modello di Manager delle risorse, tuttavia è consigliabile estrarre i valori nei parametri prima di tutto in un file di parametri per mantenere il modello di gestione risorse riutilizzabile e privo di valori specifici per una distribuzione.

```json
{
  "apiVersion": "2016-03-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### < un "configurare ramo" ></a>i parametri del modello configurare Gestione risorse

Infine, utilizzare i valori di output nei comandi dell'archivio di chiave e PowerShell AAD per popolare il file di parametri:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```
A questo punto dovrebbe ora sono i seguenti:

 - Gruppo di risorse chiave archivio
    - Archivio di chiave
    - Certificato di autenticazione server cluster
    - Certificato di crittografia di dati
 - Tenant di Azure Active Directory 
    - Applicazione di AAD per la gestione basata sul web e servizio tessuti Explorer
    - Applicazione di AAD per la gestione del client nativo
    - Utenti con ruoli assegnati 
 - Modello di servizio tessuti cluster Manager delle risorse
    - Configurata tramite chiave archivio certificati
    - Azure Active Directory configurato 

Il diagramma seguente illustra in configurazione archivio chiave e AAD adatta nel modello di Manager delle risorse.

![Mappa dipendenza Manager delle risorse][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Creare il cluster

A questo punto si è pronti creare il cluster utilizzando [la distribuzione ARM][resource-group-template-deploy].

#### <a name="test-it"></a>Eseguire il test

Usare il comando PowerShell seguente per verificare il modello di gestione risorse con un file di parametri:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>Distribuirlo

Se il test di modello Gestione risorse passa, usare il seguente comando di PowerShell per distribuire il modello di gestione risorse con un file di parametri:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>
## <a name="assign-users-to-roles"></a>Assegnare utenti ai ruoli

Dopo aver creato le applicazioni per rappresentare il cluster, è necessario assegnare agli utenti per i ruoli supportati dal servizio tessuti: sola lettura e amministratore. È possibile eseguire questa operazione tramite il [portale classica Azure][azure-classic-portal].

1. Passare al tenant di e scegliere le applicazioni.
2. Scegliere l'applicazione web che ha un nome simile `myTestCluster_Cluster`.
3. Fare clic sulla scheda utenti.
4. Scegliere un utente di assegnare e fare clic sul pulsante **assegnare** nella parte inferiore dello schermo.

    ![Assegnare utenti ai pulsante ruoli][assign-users-to-roles-button]

5. Selezionare il ruolo da assegnare all'utente.

    ![Assegnare utenti ai ruoli][assign-users-to-roles-dialog]

>[AZURE.NOTE] Per ulteriori informazioni sui ruoli in tessuti servizio, vedere [controllo dell'accesso basato sui ruoli per i client di servizi tessuti](service-fabric-cluster-security-roles.md).

 <a name="secure-linux-cluster"></a> 
##  <a name="create-secure-clusters-on-linux"></a>Creare cluster sicuro su Linux

Per semplificare il processo, è disponibile uno script di supporto [di seguito](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Per l'uso di questo script di supporto, si presuppone che si dispone già di Azure CLI installato ed è nel percorso. Assicurarsi che lo script disponga delle autorizzazioni per l'esecuzione eseguendo `chmod +x cert_helper.py` dopo il download di esso. Il primo passaggio consiste nel accedere all'account di Azure usa CLI con la `azure login` comando. Dopo l'accesso all'account Azure, utilizzare il supporto con il certificato di firma e autorità di certificazione come i seguente comando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]

The -ifile parameter can take a .pfx or a .pem file as input, with the certificate type (pfx or pem, or ss if it is a self-signed cert).
The parameter -h prints out the help text.
```

Questo comando restituisce le seguenti tre stringhe come output: 

1. SourceVaultID è l'ID per il nuovo KeyVault ResourceGroup creato automaticamente. 

2. CertificateUrl per accedere al certificato.

3. CertificateThumbprint utilizzato per l'autenticazione.


Nell'esempio seguente viene illustrato come utilizzare il comando:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Eseguire il comando precedente vengono fornite le tre stringhe come indicato di seguito:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

 Nome del soggetto del certificato deve corrispondere dominio usato per accedere al servizio tessuti cluster. Questo è necessario fornire SSL per il cluster endpoint gestione HTTPS e servizio tessuti Explorer. Non è possibile ottenere un certificato SSL da un'autorità di certificazione (CA) per la `.cloudapp.azure.com` dominio. È necessario acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da un'autorità di certificazione nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per il cluster.

Queste sono le voci necessarie per creare un cluster di tessuti servizio sicura (senza AAD) come descritto in [Gestione risorse di configurare i parametri del modello](#configure-arm). È possibile connettersi al cluster protetto tramite istruzioni [all'accesso client a un cluster di autenticazione](service-fabric-connect-to-secure-cluster.md). Cluster di anteprima Linux non supportano l'autenticazione AAD. È possibile assegnare i ruoli di amministratore e client come descritto nella sezione [assegnazione di ruoli agli utenti](#assign-roles). Quando si specificano i ruoli di amministratore e client per un cluster di anteprima Linux, è necessario fornire identificazioni personali del certificato per l'autenticazione (anziché nome del soggetto, poiché non convalida della catena o revoca viene eseguita in questa versione di anteprima).


Se si desidera utilizzare un certificato autofirmato per il test, è possibile utilizzare lo stesso script per generare un certificato autofirmato e caricarlo in KeyVault, fornendo il contrassegno `ss` non indicarne il percorso del certificato e il nome di certificato. Ad esempio, vedere il seguente comando per la creazione e caricamento di un certificato autofirmato:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net" 
```

Questo comando restituisce le stesse tre stringhe SourceVault, CertificateUrl e CertificateThumbprint, che viene utilizzato per creare un cluster Linux sicuro, insieme nella posizione in cui è stato inserito il certificato autofirmato. È necessario il certificato autofirmato per connettersi al cluster.  È possibile connettersi al cluster protetto tramite istruzioni [all'accesso client a un cluster di autenticazione](service-fabric-connect-to-secure-cluster.md). Nome del soggetto del certificato deve corrispondere dominio usato per accedere al servizio tessuti cluster. Questo è necessario fornire SSL per il cluster endpoint gestione HTTPS e servizio tessuti Explorer. Non è possibile ottenere un certificato SSL da un'autorità di certificazione (CA) per la `.cloudapp.azure.com` dominio. È necessario acquistare un nome di dominio personalizzato per il cluster. Quando si richiede un certificato da un'autorità di certificazione nome del soggetto del certificato deve corrispondere al nome di dominio personalizzato utilizzato per il cluster.

È possibile compilare i parametri forniti dallo script di supporto nel portale di come descritto nella sezione [creare un cluster nel portale di Azure](service-fabric-cluster-creation-via-portal.md#create-cluster-portal).

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone di un cluster protetto con l'autenticazione di gestione che fornisca Azure Active Directory. Successivamente, [connettersi al cluster](service-fabric-connect-to-secure-cluster.md) e informazioni sulla [gestione delle informazioni riservate dell'applicazione](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Risolvere i problemi di configurazione di Azure Active Directory per l'autenticazione client

Se si verificano problemi durante la configurazione di Azure Active Directory per l'autenticazione client, esaminare suggestings seguenti per possibili soluzioni.

### <a name="service-fabric-explorer-prompts-for-selecting-certificate"></a>Servizio tessuti Explorer istruzioni per la selezione di certificato

#### <a name="problem"></a>Problema

Dopo l'accesso correttamente nella pagina di accesso AAD in Esplora tessuti servizio, nel browser restituisce alla home page, ma richiede una finestra di dialogo per la selezione di un certificato.

![Finestra di dialogo Seleziona certificato SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Motivo

L'utente non è stato assegnato un ruolo di applicazione cluster AAD. In questo modo AAD autenticazione non riesce cluster servizio tessuti. Servizio tessuti Explorer utilizza l'autenticazione del certificato.

#### <a name="solution"></a>Soluzione

Seguire le istruzioni di configurazione AAD e assegnare i ruoli utente. Inoltre, "Utente assegnazione necessari per APP ACCESS" è consigliabile attivato come `SetupApplications.ps1` indica.

### <a name="connect-with-powershell-fails-with-error-the-specified-credentials-are-invalid"></a>Connettersi con PowerShell ha esito negativo con errore: le credenziali specificate non sono valide

#### <a name="problem"></a>Problema

Quando usare PowerShell per connettersi a cluster utilizzando la modalità di sicurezza "AzureActiveDirectory", dopo l'accesso correttamente nella pagina di accesso AAD, la connessione viene interrotta con errore: le credenziali specificate non sono valide visualizzati.

#### <a name="solution"></a>Soluzione

Come sopra.

### <a name="service-fabric-explorer-signing-in-return-failure-aadsts50011"></a>Servizio tessuti Explorer firma in errore: AADSTS50011

#### <a name="problem"></a>Problema

Dopo l'accesso nella pagina servizio tessuti Explorer accesso AAD, pagina restituisce segno errore - AADSTS50011: l'indirizzo di risposta &lt;url&gt; non corrisponde agli indirizzi di risposta configurati per l'applicazione: &lt;guid&gt;. 

![Indirizzo di risposta SFX non corrispondono][sfx-reply-address-not-match]

#### <a name="reason"></a>Motivo

Applicazione di cluster(web) rappresenta tentativi di servizio tessuti Explorer per l'autenticazione AAD, come parte della richiesta fornisce l'URL di reindirizzamento restituito. Ma non è elencato nell'elenco 'URL di risposta' applicazione AAD.

#### <a name="solution"></a>Soluzione

Aggiungere url del servizio tessuti Explorer su "URL di risposta" nella scheda Configurazione dell'applicazione cluster(web) o sostituire un elemento nell'elenco. Scegliere Salva.

![Url di risposta dell'applicazione Web][web-application-reply-url]

### <a name="can-i-reuse-the-same-aad-tenant-for-multiple-clusters"></a>È possibile riutilizzare stesso tenant AAD per più cluster?

#### <a name="answer"></a>Risposta

Sì. Ma ricordarsi di aggiungere l'URL di servizio tessuti Esplora nell'applicazione di cluster(web) in caso contrario servizio tessuti Explorer non funzionano.

### <a name="why-do-i-still-need-server-certificate-while-aad-enabled"></a>Perché è comunque necessario certificato server mentre AAD abilitato?

#### <a name="answer"></a>Risposta

FabricClient e FabricGateway eseguire l'autenticazione reciproca. In caso di autenticazione AAD, AAD integrazione offre identità client al server e certificato server viene utilizzato per verificare l'identità del server. Per ulteriori informazioni sul funzionamento del certificato nel servizio tessuti, controllare [509 e tessuti di servizio][x509-certificates-and-service-fabric]

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype-wad]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype-wad/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png