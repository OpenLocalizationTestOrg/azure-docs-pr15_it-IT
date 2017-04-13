<properties
   pageTitle="Come contrassegnare una macchina virtuale | Microsoft Azure"
   description="Informazioni sull'aggiunta di una macchina virtuale di Windows creata in Azure utilizzando il modello di distribuzione di Manager delle risorse"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Come tag una macchina virtuale di Windows Azure


In questo articolo vengono illustrati i diversi modi per contrassegnare una macchina virtuale di Windows in Azure attraverso il modello di distribuzione di Manager delle risorse. Tag sono coppie di parole chiave/valore definite dall'utente che è possibile posizionare direttamente in una risorsa o un gruppo di risorse. Azure supporta attualmente fino a 15 tag per ogni risorsa e gruppo di risorse. Tag potrebbe inserito in una risorsa al momento della creazione o aggiunta a una risorsa esistente. Si noti che i contrassegni sono supportati per le risorse create tramite il modello di distribuzione Manager delle risorse solo. Se si desidera contrassegnare una macchina virtuale Linux, vedere [come contrassegnare una macchina virtuale Linux in Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagging in combinazione con PowerShell

Per creare, aggiungere ed eliminare categorie tramite PowerShell, è prima necessario configurare l' [ambiente di PowerShell Gestione risorse Azure][]. Dopo aver completato la configurazione, è possibile inserire tag sulle risorse di elaborazione, di rete e di archiviazione, al momento della creazione o dopo aver creata la risorsa tramite PowerShell. In questo articolo verrà concentrarsi su visualizzare o modificare categorie inseriti in macchine virtuali.

Prima di tutto, passare a una macchina virtuale tramite il `Get-AzureRmVM` cmdlet.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Se la macchina virtuale contiene già tag, verranno visualizzate tutti i tag per la risorsa:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Se si desidera aggiungere contrassegni tramite PowerShell, è possibile utilizzare il `Set-AzureRmResource` comando. Nota Quando si aggiornano i contrassegni tramite PowerShell, tag vengono aggiornati nel suo insieme. Pertanto se si sta aggiungendo un tag a una risorsa che già contiene tag, sarà necessario includere tutti i tag che si desidera collocare sulla risorsa. Di seguito è illustrato un esempio di come aggiungere altri tag a una risorsa tramite PowerShell Cmdlets.

Questo cmdlet prima imposta tutti i tag archiviati *MyTestVM* variabile *$tags* utilizzando il `Get-AzureRmResource` e `Tags` proprietà.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Il secondo comando Visualizza i contrassegni per la variabile specificato.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

Il terzo comando aggiunge un tag aggiuntivo per la variabile *$tags* . Si noti l'utilizzo della **+=** per aggiungere la nuova coppia chiave/valore all'elenco *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

Il quarto comando imposta tutti i contrassegni definiti nella variabile *$tags* alla risorsa specificata. In questo caso è MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Il comando quinto vengono visualizzati tutti i contrassegni sulla risorsa. Come si può notare, i *percorso* è ora definito come un tag con *percorso* come valore.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Per ulteriori informazioni sull'uso dei tag tramite PowerShell, vedere [Cmdlet per la risorsa Azure][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Passaggi successivi

* Per ulteriori informazioni sull'aggiunta di risorse di Azure, vedere [Panoramica di gestione risorse Azure][] e [Tramite i contrassegni per organizzare le risorse di Azure][].
* Per come tag consentono di gestire l'uso delle risorse Azure, vedere [informazioni sulla fattura Azure][] e [ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure][].

[Ambiente di PowerShell Gestione risorse Azure]: ../powershell-azure-resource-manager.md
[Cmdlet per la risorsa Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Panoramica di gestione risorse Azure]: ../azure-resource-manager/resource-group-overview.md
[Uso dei contrassegni per organizzare le risorse di Azure]: ../resource-group-using-tags.md
[Informazioni sulla fattura Azure]: ../billing/billing-understand-your-bill.md
[Ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure]: ../billing-usage-rate-card-overview.md
