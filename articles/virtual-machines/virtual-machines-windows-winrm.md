<properties
    pageTitle="La configurazione dell'accesso WinRM per macchine virtuali in Gestione risorse di Azure | Microsoft Azure"
    description="Per configurare l'accesso WinRM per l'utilizzo con una macchina virtuale Manager delle risorse di Azure"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/16/2016"
    ms.author="singhkay"/>

# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Impostazione dell'accesso WinRM per macchine virtuali in Gestione risorse di Azure

## <a name="winrm-in-azure-service-management-vs-azure-resource-manager"></a>WinRM nella gestione dei servizi di Azure vs Manager delle risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica

* Per una panoramica di gestione risorse di Azure, vedere l' [articolo](../azure-resource-manager/resource-group-overview.md)
* Per le differenze tra la gestione dei servizi Azure e gestione di risorse di Azure, vedere l' [articolo](../resource-manager-deployment-model.md)

La differenza fondamentale per l'impostazione della configurazione di WinRM tra i due stack è come il certificato viene installato nella macchina virtuale. Nell'elenco Gestione risorse di Azure, i certificati vengono creati come risorse gestite dal Provider di risorse chiave archivio. Di conseguenza, l'utente deve fornire il proprio certificato e caricarlo in un archivio di chiave prima di utilizzare in una macchina virtuale.

Ecco i passaggi da eseguire per configurare una macchina virtuale con la connettività WinRM

1. Creare un archivio di chiave
2. Creare un certificato autofirmato
3. Caricare il certificato autofirmato in archivio chiave
4. Ottenere l'URL per il certificato autofirmato nell'archivio di chiave
5. L'URL di certificati fare riferimento durante la creazione di una macchina virtuale

## <a name="step-1-create-a-key-vault"></a>Passaggio 1: Creare un archivio di chiave

È possibile usare il seguente comando per creare l'archivio di chiave

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Passaggio 2: Creare un certificato autofirmato
È possibile creare un certificato autofirmato questo script di PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Passaggio 3: Caricare il certificato autofirmato per l'archivio di chiave

Prima di caricare il certificato nell'archivio di chiave creato nel passaggio 1, è necessario convertiti in un formato comprensibile il provider di risorse Microsoft.Compute. Di seguito PowerShell script consente di effettuare questa operazione

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Passaggio 4: Ottenere l'URL per il certificato autofirmato nell'archivio di chiave

Il provider di risorse Microsoft.Compute è necessario un URL per il segreto all'interno di archivio di chiave durante il provisioning di macchina virtuale. In questo modo il provider di risorse Microsoft.Compute di scaricare il segreto e creare il certificato equivalente nella macchina virtuale.

>[AZURE.NOTE]L'URL del segreto deve includere anche la versione. Un URL di esempio è simile alla seguente https://contosovault.vault.azure.net:443/informazioni riservate/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### <a name="templates"></a>Modelli

È possibile ottenere il collegamento all'URL nel modello utilizzando il seguente codice

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell

È possibile ottenere questo URL utilizzando il seguente comando PowerShell

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Passaggio 5: Riferimento l'URL di certificati durante la creazione di una macchina virtuale

#### <a name="azure-resource-manager-templates"></a>Modelli di gestione risorse di Azure

Durante la creazione di una macchina virtuale di modelli, il certificato viene fatto riferimento nella sezione informazioni riservate e la sezione winRM come indicato di seguito:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Un modello di esempio per le risposte sopra, visitare qui [201--winrm-keyvault-finestre SV](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Codice sorgente per questo modello può trovarsi in [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell

    $vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Passaggio 6: Connessione per la macchina virtuale
Per potersi connettere a macchine Virtuali è necessario assicurarsi che il computer è configurato per la gestione remota WinRM. Avviare PowerShell come amministratore ed eseguire il seguente comando per verificare che sta configurare.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Potrebbe essere necessario verificare che il servizio è in esecuzione se le risposte sopra non funziona. È possibile eseguire tale utilizzando`Get-Service WinRM`

Al termine dell'installazione, è possibile connettersi al macchine Virtuali utilizzando il seguente comando

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate