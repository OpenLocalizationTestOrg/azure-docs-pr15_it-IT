<properties
   pageTitle="Come contrassegnare una macchina virtuale Linux | Microsoft Azure"
   description="Informazioni sull'aggiunta di una macchina virtuale Linux creata in Azure utilizzando il modello di distribuzione di Manager delle risorse."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Come contrassegnare una macchina virtuale Linux in Azure

In questo articolo vengono illustrati i diversi modi per contrassegnare una macchina virtuale Linux in Azure attraverso il modello di distribuzione di Manager delle risorse. Tag sono coppie di parole chiave/valore definite dall'utente che è possibile posizionare direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 15 tag per ogni risorsa e gruppo di risorse. Tag potrebbe inserito in una risorsa al momento della creazione o aggiunta a una risorsa esistente. Si noti, tag sono supportati per le risorse create tramite solo il modello di distribuzione di Manager delle risorse.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Tagging in combinazione con CLI Azure

Per iniziare, [installare e configurare CLI Azure](../xplat-cli-azure-resource-manager.md) e verificare che siano in modalità di gestione risorse (`azure config mode arm`).

È possibile visualizzare tutte le proprietà per una determinata macchina virtuale, l'inclusione dei tag, questo comando:

        azure vm show -g MyResourceGroup -n MyTestVM

Per aggiungere un nuovo tag macchine Virtuali tramite CLI Azure, è possibile utilizzare il `azure vm set` comando insieme tag parametro **-l**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Per rimuovere tutti i contrassegni, è possibile utilizzare il parametro **– T** il `azure vm set` comando.

        azure vm set – g MyResourceGroup –n MyTestVM -T


Ora che è stato applicato tag alle risorse CLI Azure e il portale, diamo un'occhiata ai dettagli per visualizzare i contrassegni nel portale di fatturazione.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sull'aggiunta di risorse di Azure, vedere [Panoramica di gestione risorse Azure][] e [Tramite i contrassegni per organizzare le risorse di Azure][].
* Per come tag consentono di gestire l'uso delle risorse Azure, vedere [informazioni sulla fattura Azure][] e [ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Panoramica di gestione risorse Azure]: ../azure-resource-manager/resource-group-overview.md
[Uso dei contrassegni per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Informazioni sulla fattura Azure]: ../billing/billing-understand-your-bill.md
[Ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md
