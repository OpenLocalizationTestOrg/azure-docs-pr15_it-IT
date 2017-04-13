<properties
   pageTitle="Selezionare immagini Linux VM con CLI Azure | Microsoft Azure"
   description="Informazioni su come determinare la pubblicazione, offerta e SKU per le immagini quando si crea una macchina virtuale Linux con il modello di distribuzione di Manager delle risorse."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="select-linux-vm-images-with-the-azure-cli"></a>Selezionare immagini Linux VM con CLI Azure

In questo argomento viene descritto come trovare gli autori, offerte, SKU e le versioni per ogni posizione in cui è possibile distribuire. Per assegnare un esempio, alcune immagini Linux VM usati di frequente sono:

**Tabella di uso comune Linux immagini**


| PublisherName                        | Offerta                                 | SKU                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| Red Hat                           | RHEL                                       | 7.2                              |
| credativ                         | Debian                                     | 8                                | 
| SUSE                             | openSUSE                                   | 13.2                             |
| SUSE                             | SLES                                       | SP1 12                           |
| OpenLogic                        | CentOS                                     | 7.1                              |
| Canonici                        | UbuntuServer                               | 14.04.4-LTS                      |
| CoreOS                           | CoreOS                                     | Stabile                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]
