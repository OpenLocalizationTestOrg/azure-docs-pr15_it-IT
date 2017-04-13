<properties
   pageTitle="Creazione di modelli con le estensioni macchine Virtuali di Windows | Microsoft Azure"
   description="Informazioni sulla creazione di modelli di gestione di risorse Azure con le estensioni per macchine virtuali di Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# <a name="authoring-azure-resource-manager-templates-with-windows-vm-extensions"></a>Creazione di modelli di gestione di risorse Azure con estensioni macchine Virtuali di Windows

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Da Azure PowerShell, eseguire il seguente cmdlet di PowerShell Azure:

      Get-AzureVMAvailableExtension


Questo cmdlet restituisce il nome dell'autore, estensione e la versione come indicato di seguito:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Queste tre proprietà mappare "publisher", "tipo" e "typeHandlerVersion" rispettivamente nel frammento di modello precedente.

>[AZURE.NOTE]È sempre consigliabile per utilizzare l'ultima versione di estensione per ottenere le funzionalità più aggiornate.

## <a name="identifying-the-schema-for-the-extension-configuration-parameters"></a>Identificare lo schema per i parametri di configurazione di estensione

Il passaggio successivo con la creazione di un modello di estensione è identificare il formato per fornire parametri di configurazione. Ogni estensione supporta un insieme di parametri.

Per esaminare le configurazioni di esempio per le estensioni di Windows, vedere [esempi di estensioni Windows](virtual-machines-windows-extensions-configuration-samples.md).


Fare riferimento al seguente per ottenere un modello completo con le estensioni macchine Virtuali.

[Estensione di Script personalizzati in un Windows macchine Virtuali](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Dopo la creazione del modello, è possibile distribuirlo tramite PowerShell Azure.
