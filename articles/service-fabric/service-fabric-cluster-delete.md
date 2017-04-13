<properties
   pageTitle="Eliminare un cluster di Azure e le relative risorse | Microsoft Azure"
   description="Informazioni su come eliminare completamente un'infrastruttura di servizio cluster può eliminare il gruppo di risorse che include il cluster o eliminando in modo selettivo risorse."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Eliminare un cluster di servizio tessuti Azure e le risorse utilizzate

Un cluster di servizio tessuti è costituito da molte altre risorse Azure oltre la risorsa stessa. Per eliminare completamente un cluster di servizio tessuti pertanto è necessario eliminare tutte le risorse che è costituito.
Sono disponibili due opzioni: uno eliminare il gruppo di risorse che il cluster (che elimina la risorsa e le eventuali altre risorse nel gruppo di risorse) o specificamente eliminare la risorsa ed è associata risorse (ma non ad altre risorse nel gruppo di risorse).

>[AZURE.NOTE] Raggruppamento delle risorse **non** si elimina tutte le altre risorse che composta il cluster servizio tessuti.

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Eliminare l'intero gruppo di risorse (RG) che cluster tessuti servizio

Questo è il modo più semplice per garantire eliminare tutte le risorse associate con i cluster, inclusi il gruppo di risorse. È possibile eliminare il gruppo di risorse tramite PowerShell o tramite il portale di Azure. Se il gruppo di risorse è risorse che non correlate al cluster tessuti servizio, è possibile eliminare risorse specifiche.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Eliminare il gruppo di risorse tramite PowerShell Azure

È anche possibile eliminare il gruppo di risorse eseguendo il seguente cmdlet di PowerShell Azure. Verificare che l'opzione Azure PowerShell 1.0 o versione successiva è installato nel computer in uso. Se non è già in precedenza, seguire i passaggi descritti in [come installare e configurare Azure PowerShell.](../powershell-install-configure.md)

Aprire una finestra di PowerShell ed eseguire i cmdlet PS seguenti:

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Verrà visualizzato un messaggio per confermare l'eliminazione, se non si utilizza il *-forza* opzione. Nella finestra di conferma la RG e tutte le risorse che contiene vengono eliminate.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Eliminare un gruppo di risorse nel portale di Azure  

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al cluster tessuti servizio che si desidera eliminare.
3. Fare clic sul nome del gruppo di risorse della pagina di essentials cluster.
4. Verrà visualizzata la pagina **Delle risorse gruppo Essentials** .
5. Fare clic su **Elimina**.
6. Seguire le istruzioni nella pagina per completare l'eliminazione del gruppo di risorse.

![Elimina gruppo di risorse][ResourceGroupDelete]


## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Eliminare la risorsa e le risorse utilizzate, ma non ad altre risorse nel gruppo di risorse

Se il gruppo di risorse contiene solo le risorse correlate al cluster tessuti servizio che si desidera eliminare, quindi è facile eliminare l'intero gruppo di risorse. Se si desidera eliminare in modo selettivo le risorse uno alla volta nel gruppo di risorse, attenersi alla seguente procedura.

Se è stato distribuito il cluster tramite il portale o utilizzando uno dei modelli di gestione risorse tessuti servizio dalla raccolta modelli, tutte le risorse utilizzate cluster vengono contrassegnate con due tag seguenti. È possibile usarli decidere quali risorse che si desidera eliminare.

***Tag #1:*** Chiave = nome cluster, valore = 'nome del cluster'

***Tag #2:*** Chiave = resourceName, valore = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Eliminazione di risorse specifiche nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al cluster tessuti servizio che si desidera eliminare.
3. Passare a **tutte le impostazioni** e il nozioni di base.
4. Fare clic su **tag** in **Gestione risorse** e l'impostazioni.
5. Fare clic su uno dei **tag** e il tag per ottenere un elenco di tutte le risorse con tale tag.

    ![Tag di risorse][ResourceTags]

6. Dopo avere inserito l'elenco delle risorse con tag, fare clic su ciascuna delle risorse ed eliminarli.

    ![Risorse con tag][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Eliminare le risorse usando PowerShell Azure

È possibile eliminare le risorse uno alla volta, eseguire i cmdlet di PowerShell Azure seguenti. Verificare che l'opzione Azure PowerShell 1.0 o versione successiva è installato nel computer in uso. Se non è già in precedenza, seguire i passaggi descritti in [come installare e configurare Azure PowerShell.](../powershell-install-configure.md)

Aprire una finestra di PowerShell ed eseguire i cmdlet PS seguenti:

```powershell
Login-AzureRmAccount
```
Per ognuna delle risorse da eliminare, eseguire le operazioni seguenti:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Per eliminare la risorsa, eseguire le operazioni seguenti:

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Passaggi successivi
Leggere le operazioni seguenti per vengono inoltre fornite informazioni sull'aggiornamento di un cluster e partizioni servizi:

- [Informazioni sugli aggiornamenti cluster](service-fabric-cluster-upgrade.md)
- [Informazioni sulle partizioni servizi informazioni sullo stato per scala massima](service-fabric-concepts-partitioning.md)


<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG
