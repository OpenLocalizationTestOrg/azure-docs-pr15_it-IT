<properties
   pageTitle="Utilizzo di immagini di Windows client per gli scenari di sviluppo/test | Microsoft Azure"
   description="Come usare i vantaggi dell'abbonamento Visual Studio per la distribuzione di Windows 7/8/10 in Azure per gli scenari di sviluppo e di testing"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>Usando il client di Windows in Azure per gli scenari di sviluppo/test

È possibile utilizzare Windows 7, Windows 8 o Windows 10 in Azure per gli scenari di sviluppo/test purché che si disponga di un abbonamento a Visual Studio (in precedenza MSDN) appropriato. In questo articolo vengono illustrati i requisiti di idoneità per client di Windows in esecuzione in Azure e l'utilizzo di immagini raccolta Azure.


## <a name="subscription-eligibility"></a>Idoneità abbonamento
Gli abbonati a Visual Studio attivi (persone che hanno acquistato una licenza di abbonamento Visual Studio) possono utilizzare il client di Windows per lo sviluppo e scopo di verifica. Client Windows può essere usata su hardware e Azure macchine virtuali in esecuzione in un tipo di abbonamento Azure. Client Windows potrebbero non essere distribuito in usato in Azure per l'utilizzo di produzione normale o utilizzati dagli utenti che non sono gli abbonati a Visual Studio attivi.

Le tue abbiamo apportato alcune immagini di Windows 10 disponibili nella raccolta di Azure all'interno di [sviluppo e di testing idoneo offre](#eligible-offers). Inoltre, gli abbonati a Visual Studio all'interno di qualsiasi tipo di offerta possono [adeguatamente preparare e creare](virtual-machines-windows-prepare-for-upload-vhd-image.md) un 64 bit di Windows 7, Windows 8 o Windows 10 immagine e quindi [caricare in Azure](virtual-machines-windows-upload-image.md). L'uso rimane limitato al dev/test per gli abbonati a Visual Studio attivi.


## <a name="eligible-offers"></a>Offerte ammesse
Nella tabella seguente viene offerta ID sono idonei per la distribuzione di Windows 10 attraverso la raccolta di Azure. Le immagini di Windows 10 sono visibili solo per le offerte seguenti. Gli abbonati a Visual Studio che serve per eseguire il client di Windows in un tipo di offerta diversa è necessario [prepararsi adeguatamente](virtual-machines-windows-prepare-for-upload-vhd-image.md) e creare un'immagine di Windows 7, Windows 8 o Windows 10 e a 64 bit [quindi caricare in Azure](virtual-machines-windows-upload-image.md).

| Nome offerta | Numero di offerta | Immagini disponibili client |
|:-----------|:------------:|:-----------------------:|
| [Uso prepagato. Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Abbonati a Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Abbonati a Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Abbonati a Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Studio Premium con MSDN (vantaggio)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Abbonati a Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Abbonati a Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Sviluppo dell'organizzazione e di testing](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Verificare l'abbonamento Azure
Se non si conosce l'ID di offerta, è possibile ottenere tramite il portale di Azure o il portale di Account.

L'ID di offerta di abbonamento è indicato nella e 'Abbonamenti' all'interno del portale Azure:

![ID dettagli dell'offerta dal portale di Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

È inoltre possibile visualizzare l'ID di offerta dalla [scheda 'Sottoscrizioni'](http://account.windowsazure.com/Subscriptions) del portale di Account Azure:

![ID dettagli dell'offerta dal portale di Account Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Passaggi successivi
È ora possibile distribuire le macchine virtuali con [PowerShell](virtual-machines-windows-ps-create.md), [modelli di Manager delle risorse](virtual-machines-windows-ps-template.md)o [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
