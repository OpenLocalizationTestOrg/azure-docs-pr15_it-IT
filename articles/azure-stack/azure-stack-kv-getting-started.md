<properties
    pageTitle="Guida introduttiva di Azure Stack chiave archivio | Microsoft Azure"
    description="Introduzione all'utilizzo di Azure Stack chiave archivio"
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
    ms.date="10/18/2016"
    ms.author="ricardom"/>


# <a name="getting-started-with-key-vault"></a>Guida introduttiva di archivio di chiave

In questa sezione vengono descritti i passaggi per creare un archivio, gestire chiavi e informazioni riservate nonché di autorizzare utenti o applicazioni per richiamare le operazioni di archivio in pila Azure. I passaggi seguenti presuppongono esiste una sottoscrizione tenant e il servizio KeyVault registrato all'interno di tale sottoscrizione. Tutti i comandi di esempio si basano sui cmdlet KeyVault disponibili come parte di Azure PowerShell SDK.

## <a name="enabling-the-tenant-subscription-for-vault-operations"></a>Attivazione della sottoscrizione tenant per le operazioni di archivio 

È possibile eseguire operazioni su qualsiasi archivio, è necessario assicurarsi che l'abbonamento è abilitata per la gestione dell'archivio. È possibile confermare che mediante il comando di PowerShell seguente:

    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -AutoSize

L'output del comando precedente segnalerà "Registrata" per lo stato "Registrazione" di ogni riga.

    ProviderNamespace RegistrationState ResourceTypes Locations
    Microsoft.KeyVault Registered {operations} {local}
    Microsoft.KeyVault Registered {vaults} {local}
    Microsoft.KeyVault Registered {vaults/secrets} {local}
    

 Se non è che le maiuscole/minuscole, è necessario chiamare il seguente comando per registrare il servizio KeyVault all'interno di abbonamento:

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault

E la riportata di seguito è riportato l'output del comando:
    
    ProviderNamespace : Microsoft.KeyVault
    RegistrationState : Registered
    ResourceTypes : {operations, vaults, vaults/secrets}
    Locations : {local}


>[AZURE.NOTE] Se viene visualizzato l'errore: "*l'abbonamento non è registrato con Azure chiave archivio*" quando si richiama KeyVault cmdlet, confermare è stato attivato il provider di risorse KeyVault per istruzioni precedenti.


## <a name="creating-a-hardened-container-a-vault-in-azure-stack-to-store-and-manage-cryptographic-keys-and-secrets"></a>Creazione di un contenitore di protezione avanzato (un archivio) in pila Azure per archiviare e gestire le informazioni riservate e chiavi di crittografia

Per creare un archivio, un tenant prima di tutto necessario creare un gruppo di risorse. I comandi di PowerShell seguenti creano un gruppo di risorse, quindi un archivio di tale gruppo di risorse. Nell'esempio include anche l'output tipico da tale cmdlet.

### <a name="creating-a-resource-group"></a>Creazione di un gruppo di risorse:

    New-AzureRmResourceGroup -Name vaultrg010 -Location local -Verbose -Force

Output:

    VERBOSE: Performing the operation "Replacing resource group ..." on target "".
    VERBOSE: 12:52:51 PM - Created resource group 'vaultrg010' in location 'local'
    ResourceGroupName : vaultrg010
    Location : local
    ProvisioningState : Succeeded
    Tags :
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010
    

### <a name="creating-a-vault"></a>Creazione di un archivio:


    New-AzureRmKeyVault -VaultName vault010 -ResourceGroupName vaultrg010 -Location local -Verbose

Output:

    VaultUri : https://vault010.vault.azurestack.local
    TenantId : 5454420b-2e38-4b9e-8b56-1712d321cf33
    TenantName : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Sku : Standard
    EnabledForDeployment : False
    EnabledForTemplateDeployment : False
    EnabledForDiskEncryption : False
    AccessPolicies : {5454420b-2e38-4b9e-8b56-1712d321cf33}
    AccessPoliciesText :
    Tenant ID : 5454420b-2e38-4b9e-8b56-1712d321cf33
    Object ID : ca342e90-f6aa-435b-a11c-dfe5ef0bfeeb
    Application ID :
    Display Name : Tenant Admin (tenantadmin1@msazurestack.onmicrosoft.com)
    Permissions to Keys : get, create, delete, list, update, import, backup, restore
    Permissions to Secrets : all
    OriginalVault : Microsoft.Azure.Management.KeyVault.Vault
    ResourceId : /subscriptions/fa881715-3802-42cc-a54e-a06adf61584d/resourceGroups/vaultrg010/providers/Microsoft.KeyVault/vaults/vault010
    VaultName : vault010
    ResourceGroupName : vaultrg010
    Location : local
    Tags : {}
    TagsTable :
    
L'output di questo cmdlet Visualizza le proprietà dell'archivio di chiave appena creata. Le due proprietà più importanti sono:

-   **Nome archivio**: nell'esempio seguente viene **vault010**. Utilizzare questo nome per altri cmdlet chiave archivio.

-   **URI archivio**: nell'esempio seguente viene https://vault010.vault.azurestack.local. Le applicazioni che utilizzano l'archivio tramite l'API REST necessario utilizzare questo URI.

L'account Azure è autorizzato a eseguire le operazioni su questo tasto archivio. Nessun altro ancora, è.


## <a name="operating-on-keys-and-secrets"></a>Operazioni su chiavi e informazioni riservate

Dopo aver creato un archivio, seguire la seguente procedura per creare, gestire chiavi e informazioni riservate:

### <a name="creating-a-key"></a>Creazione di una chiave

Per creare un tasto, usare **Aggiungi AzureKeyVaultKey** per esempio riportato di seguito. Dopo la Creazione chiave ha esito positivo, il cmdlet fornirà i dettagli chiave appena creati.

    Add-AzureKeyVaultKey -VaultName \$vaultName -Name\$keyVaultKeyName -Verbose -Destination Software

Di seguito è l'output del cmdlet *AzureKeyVaultKey Aggiungi* :

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff
    
È ora possibile fare riferimento questo tasto che è stato creato o caricamento di archivio di chiave Azure, utilizzando il relativo URI. Utilizzare **i tasti/https://vault010.vault.azurestack.local:443/keyVaultKeyName001** per ottenere sempre la versione corrente; Utilizzare **https://vault010.vault.azurestack.local:443/tasti/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff** per ottenere la versione specifica.

### <a name="retrieving-a-key"></a>Recupero di una chiave

Utilizzare **Get-AzureKeyVaultKey** per recuperare una chiave e i relativi dettagli per l'esempio seguente:

    Get-AzureKeyVaultKey -VaultName vault010 -Name keyVaultKeyName001

Di seguito è l'output di Get-AzureKeyVaultKey

    Attributes : Microsoft.Azure.Commands.KeyVault.Models.KeyAttributes
    Key : {"kid":"https://vault010.vault.azurestack.local/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff","kty":"RSA","key\_ops":\["encrypt"
    ,"decrypt","sign","verify","wrapKey","unwrapKey"\],"n":"nDkcBQCyyLnXtbwFMnXOWzPDqWKiyjf0G3QTxvuN\_MansEn9X-91q4\_WFmRBCd5zWBqz671iuZO\_D4r0P25
    Fe2lAq\_3T1gATVNGR7LTEU9W5h8AoY10bmt4e0y66Jn2vUV-UTCz4\_vtKSKoiuNXHFR\_tGZ-6YX-frqKIiC8pbE4Qvz1x-c7E-eM\_Cpu87koL95n-Hl3wQRQRPXEPRR6gcHR5E74D1
    gLEFCWKySTo4nXtLoeBMNK5QYEBZIAS61ACbR4czjHn6ty-tZeVTc7hyK\_UO2EbJovQIAhyayfq018uNtCBzjjkqJKnY34kviVCPoTQqOdpHa0FHrloe5FeIw","e":"AQAB"}
    VaultName : vault010
    Name : keyVaultKeyName001
    Version : 86062b02b10342688f3b0b3713e343ff
    Id : https://vault010.vault.azurestack.local:443/keys/keyVaultKeyName001/86062b02b10342688f3b0b3713e343ff

### <a name="setting-a-secret"></a>Impostazione di un segreto

    $secretvalue = ConvertTo-SecureString 'User@123' -AsPlainText -Force
    Set-AzureKeyVaultSecret -Name MySecret-VaultName vault010 -SecretValue $secretvalue
    
Output

    Vault Name : vault010
    Name : MySecret
    Version : 65a387f2ed4a416180e852b970846f5b
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret/65a387f2ed4a416180e852b970846f5b
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags : 

### <a name="retrieving-a-secret"></a>Recupero di un segreto

    Get-AzureKeyVaultSecret -VaultName vault010

Output

    Vault Name : vault010
    Name : MySecret
    Version :
    Id : https://vault010.vault.azurestack.local:443/secrets/MySecret
    Enabled : True
    Expires :
    Not Before :
    Created : 8/17/2016 10:49:52 PM
    Updated : 8/17/2016 10:49:52 PM
    Content Type :
    Tags :

A questo punto, l'archivio chiave e chiave o segreto è pronto per l'utilizzo di applicazioni.
È necessario autorizzare applicazioni usarle.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizzare l'applicazione per utilizzare il tasto o segreto 

Per autorizzare l'applicazione per accedere alla chiave o segreta nell'archivio di, utilizzare il cmdlet Set -**AzureRmKeyVaultAccessPolicy** .

Se, ad esempio il nome di archivio è *ContosoKeyVault* e l'applicazione che si desidera autorizzare dispone di un *ID Client* di *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*e si vuole autorizzare l'applicazione decrittografare ed eseguire l'accesso con i tasti dell'archivio, eseguire le operazioni seguenti:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se si vuole autorizzare la stessa applicazione di leggere informazioni riservate nell'archivio, eseguire le operazioni seguenti:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get


## <a name="next-steps"></a>Passaggi successivi
[Distribuire una macchina virtuale con una password di archivio di chiave](azure-stack-kv-deploy-vm-with-secret.md)

[Distribuire una macchina virtuale con un certificato chiave archivio](azure-stack-kv-push-secret-into-vm.md)