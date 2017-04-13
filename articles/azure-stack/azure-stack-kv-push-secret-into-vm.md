<properties
    pageTitle="Distribuire una macchina virtuale con un certificato utilizzando Azure Stack chiave archivio | Microsoft Azure"
    description="Informazioni su come distribuire una macchina virtuale e inserisce un certificato dall'archivio chiave dello Stack di Azure"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="create-vms-and-include-certificates-retrieved-from-key-vault"></a>Creare macchine virtuali e includere i certificati recuperati dall'archivio di chiave

In pila di Azure, macchine virtuali vengono distribuite tramite Gestione risorse di Azure ed è ora possibile memorizzare i certificati in Azure Stack chiave archivio. Quindi Stack di Azure (provider di risorse Microsoft.Compute un specifico) consente di inserire loro nelle macchine virtuali quando vengono distribuite macchine virtuali. Certificati possono essere utilizzati in molti scenari, tra cui SSL, sulla crittografia e autenticazione basata su certificato.

Con questo metodo, è possibile mantenere il certificato attendibili. È ora non presente nell'immagine macchine Virtuali o nei file di configurazione dell'applicazione o un altro percorso non sicuro. Impostando il criterio di accesso appropriato per l'archivio di chiave, è possibile controllare chi Ottiene l'accesso al certificato. Un altro vantaggio è che è possibile gestire tutti i certificati di un'unica posizione Azure Stack chiave archivio.

Ecco una breve panoramica del processo:

-   È necessario un certificato in formato pfx.

-   Creare un archivio di chiave (utilizzando un modello o il seguente script di esempio).

-   Assicurarsi che è stata attivata l'opzione EnabledForDeployment.

-   Caricare il certificato come segreto.

## <a name="deploying-vms"></a>Distribuzione di macchine virtuali

Questo script di esempio crea un archivio di chiave e quindi memorizza un certificato archiviato nel file. pfx in una directory locale per l'archivio chiave come segreto.

    $vaultName = "contosovault"
    $resourceGroup = "contosovaultrg"
    $location = "local"
    $secretName = "servicecert"
    $fileName = "keyvault.pfx"
    $certPassword = "abcd1234"

    # =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    $fileContentBytes = get-content \$fileName -Encoding Byte

    $fileContentEncoded =
    [System.Convert\]::ToBase64String(\$fileContentBytes)
    $jsonObject = @"
    {
    "data": "\$filecontentencoded",
    "dataType" :"pfx",
    "password": "\$certPassword"
    }
    @$jsonObjectBytes = [System.Text.Encoding\]::UTF8.GetBytes(\$jsonObject)
    $jsonEncoded = \[System.Convert\]::ToBase64String(\$jsonObjectBytes)
    Switch-AzureMode -Name AzureResourceManager
    New-AzureResourceGroup -Name \$resourceGroup -Location \$location
    New-AzureKeyVault -VaultName \$vaultName -ResourceGroupName
    $resourceGroup -Location \$location -sku standard -EnabledForDeployment
    $secret = ConvertTo-SecureString -String \$jsonEncoded -AsPlainText -Force
    Set-AzureKeyVaultSecret -VaultName \$vaultName -Name \$secretName -SecretValue \$secret

La prima parte dello script legge il file. pfx e archivia come un JSON oggetto con la base 64 contenuto di file con codifica. L'oggetto JSON sarà anche con codificata base 64.

Successivamente, viene creato un nuovo gruppo di risorse e quindi crea un archivio di chiave. Si noti l'ultimo parametro per il comando nuovo AzureKeyVault '-EnabledForDeployment', che consente l'accesso a Azure (specificamente per il provider di risorse Microsoft.Compute) per leggere informazioni riservate dalla chiave archivio per le distribuzioni.

L'ultimo comando Archivia semplicemente l'oggetto JSON base 64 codificato nell'archivio di chiave come segreto.

Di seguito è riportato un output dello script precedente:

    VERBOSE:  – Created resource group 'contosovaultrg' in
    location
    'eastus'
    ResourceGroupName : contosovaultrg
    Location          : eastus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                        Actions NotActions
                        ======= ==========
                        \*
    ResourceId        :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56/re
    sourceGroups/contosovaultrg
    VaultUri             : https://contosovault.vault.azure.net
    TenantId             : xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
    TenantName           : xxxxxxxx
    Sku                  : standard
    EnabledForDeployment : True
    AccessPolicies       : {xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47}
    AccessPoliciesText   :
                           Tenant ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-2d7cd011db47
                           Object ID              :
                           xxxxxxxx-xxxx-xxxx-xxxx-b092cebf0c80
                           Application ID         :
                           Display Name           : Derick Developer  (derick@contoso.com)
                           Permissions to Keys    : get, create, delete,
                           list, update, import, backup, restore
                           Permissions to Secrets : all
    OriginalVault        : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId           :
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-dd149b4aeb56                 
    /resourceGroups/contosovaultrg/providers/Microsoft.KeyV
                           ault/vaults/contosovault
    VaultName            : contosovault
    ResourceGroupName    : contosovaultrg
    Location             : eastus
    Tags                 : {}
    TagsTable            :
    SecretValue     : System.Security.SecureString
    SecretValueText :
    ew0KImRhdGEiOiAiTUlJSkN3SUJBekNDQ01jR0NTcUdTSWIzRFFFSEFh
    Q0NDTGdFZ2dpME1JSUlzRENDQmdnR0NTcUdTSWIzRFFFSEFhQ0NCZmtF           
    Z2dYMU1JSUY4VENDQmUwR0N5cUdTSWIzRFFFTUNnRUNvSUlFL2pDQ0JQ
    &lt;&lt;&lt; Output truncated… &gt;&gt;&gt;
    Attributes      :
    Microsoft.Azure.Commands.KeyVault.Models.SecretAttributes
    VaultName       : contosovault
    Name            : servicecert
    Version         : e3391a126b65414f93f6f9806743a1f7
    Id              :
    https://contosovault.vault.azure.net:443/secrets/servicecert
                      /e3391a126b65414f93f6f9806743a1f7

A questo punto si è pronti distribuire un modello di macchina virtuale. Nota l'URI del segreto dall'output (come mostrato nell'output precedente in verde).

È necessario un modello in seguito. I parametri di interesse speciale (oltre ai parametri macchina virtuale comune) sono il nome di archivio, il gruppo di risorse archivio e URI segreta. Naturalmente, è possibile scaricarlo da GitHub e modificare in base alle esigenze.

Quando questa macchina virtuale viene distribuita, Azure inserisce il certificato in macchina virtuale.
In Windows, i certificati nel file. pfx vengono aggiunti con la chiave privata No. Il certificato viene aggiunto al percorso certificato LocalMachine, con l'archivio certificati immessi dall'utente. Su Linux, il file di certificato viene inserito nella directory /var/lib/waagent, con il nome del file &lt;UppercaseThumbprint&gt;CRT per il X509 file di certificato e &lt;UppercaseThumbprint&gt;.prv per la chiave privata.
Entrambi i file sono .pem formattato.

L'applicazione in genere consente di trovare il certificato utilizzando l'identificazione personale e non richiede la modifica.

## <a name="retiring-certificates"></a>Eliminazione dei certificati


Nella sezione precedente è stato illustrato come inserire un nuovo certificato nelle macchine virtuali esistenti. Ma il certificato precedente è ancora in macchina virtuale e non può essere rimossi. Per una maggiore protezione, è possibile modificare l'attributo per vecchio segreto su 'Disattivato', in modo che anche se un modello precedente tenta di creare una macchina virtuale con questa versione precedente del certificato, verrà. Ecco come si configura una versione specifica segreta da disattivare:

    Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0

## <a name="conclusion"></a>Conclusioni


Con il nuovo metodo, il certificato è possibile mantenere separato da immagine macchine Virtuali o payload dell'applicazione. Pertanto abbiamo eliminato un punto di esposizione.

Il certificato può anche essere rinnovato e caricato in archivio chiave senza dover ricostruire l'immagine di macchine Virtuali o il pacchetto di distribuzione dell'applicazione. L'applicazione deve ancora essere fornito con il nuovo URI per la nuova versione di certificato attraverso.

Separando il certificato da macchina virtuale o payload dell'applicazione, sono state ridotte a questo punto il numero del personale che avranno accesso diretto al certificato.

Come un altro vantaggio, è ora disponibile una posizione centralizzata nell'archivio di chiave per gestire tutti i certificati, incluse tutte le versioni distribuite nel tempo.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire una macchina virtuale con una password di archivio di chiave](azure-stack-kv-deploy-vm-with-secret.md)

[Consentire a un'applicazione di accedere chiave archivio](azure-stack-kv-sample-app.md)