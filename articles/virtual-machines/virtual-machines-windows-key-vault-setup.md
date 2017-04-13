<properties
    pageTitle="Configurare la chiave archivio per macchine virtuali in Gestione risorse di Azure | Microsoft Azure"
    description="Come configurare l'archivio di chiave per l'utilizzo con una macchina virtuale Manager delle risorse di Azure."
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
    ms.date="05/31/2016"
    ms.author="singhkay"/>

# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Configurare la chiave archivio per macchine virtuali in Gestione risorse di Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica

In pila Manager delle risorse di Azure, informazioni riservate/certificati vengono creati come risorse forniti dal provider di risorse dell'archivio di chiave. Per altre informazioni sull'archivio di chiave, vedere [Novità Azure chiave archivio?](../key-vault/key-vault-whatis.md)

>[AZURE.NOTE] 
>
>1. Affinché archivio chiave da utilizzare con macchine virtuali di Manager delle risorse di Azure, è necessario impostare la proprietà **EnabledForDeployment** chiave archivio su true. È possibile eseguire questa operazione in vari client.
>
>2. È necessario creare sottoscrizione e la posizione della macchina virtuale stesso archivio di chiave.

## <a name="use-powershell-to-set-up-key-vault"></a>Usare PowerShell per configurare l'archivio di chiave
Per creare un archivio di chiave tramite PowerShell, vedere [Guida introduttiva di Azure chiave archivio](../key-vault/key-vault-get-started.md#vault).

Per nuovi archivi di chiave, è possibile utilizzare questo cmdlet di PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Per gli archivi di chiave esistenti, è possibile utilizzare questo cmdlet di PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Aiutaci a CLI per configurare l'archivio di chiave
Per creare un archivio di chiave utilizzando l'interfaccia della riga di comando (CLI), vedere [gestire archivi chiave usa CLI](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault).

Per CLI, è necessario creare un archivio chiave prima di assegnare i criteri di distribuzione. È possibile eseguire questa operazione utilizzando il comando seguente:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Usare i modelli per configurare la chiave archivio
Mentre si usa un modello, è necessario impostare il `enabledForDeployment` la proprietà `true` per la risorsa chiave archivio.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Per altre opzioni che è possibile configurare quando si crea un archivio di chiave con i modelli, vedere [creare un archivio di chiave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
