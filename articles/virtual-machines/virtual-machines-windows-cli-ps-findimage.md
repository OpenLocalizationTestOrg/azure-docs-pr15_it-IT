<properties
   pageTitle="Individuare e selezionare le immagini di macchine Virtuali di Windows | Microsoft Azure"
   description="Informazioni su come determinare la pubblicazione, offerta e SKU per le immagini quando si crea una macchina virtuale di Windows con il modello di distribuzione di Manager delle risorse."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Individuare e selezionare immagini macchina virtuale di Windows Azure con PowerShell o CLI

In questo argomento viene descritto come trovare gli autori di immagine macchine Virtuali, offerte, SKU e le versioni per ogni posizione in cui è possibile distribuire. Per assegnare un esempio, alcune immagini macchine Virtuali di Windows usati di frequente sono:

## <a name="table-of-commonly-used-windows-images"></a>Tabella simboli di utilizzo delle immagini di Windows


| PublisherName                        | Offerta                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Enterprise-ottimizzata-per-DW      |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Enterprise-ottimizzata-per-OLTP    |
| MicrosoftWindowsServer           | Windows Server                              | Data Center di R2 2012                  |
| MicrosoftWindowsServer           | Windows Server                              | Data Center 2012               |
| MicrosoftWindowsServer           | Windows Server                              | 2008 R2 SP1 |
| MicrosoftWindowsServer           | Windows Server                              | Anteprima tecnica di Server Windows |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
