<properties
   pageTitle="Creare un contenitore dei servizi cloud con PowerShell | Microsoft Azure"
   description="In questo articolo viene spiegato come creare un contenitore dei servizi cloud con PowerShell. Il contenitore ospita ruoli web e lavoro."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utilizzare un comando di PowerShell Azure per creare un contenitore di servizio cloud vuota
In questo articolo viene spiegato come creare rapidamente un contenitore di servizi Cloud uso dei cmdlet di PowerShell Azure. Eseguire la procedura seguente:

1. Installare il cmdlet di Microsoft Azure PowerShell dalla pagina [download Azure PowerShell](http://aka.ms/webpi-azps) .
2. Aprire il prompt dei comandi di PowerShell.
3. Usare [Aggiungi AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) effettuare l'accesso.

    > [AZURE.NOTE] Per ulteriori istruzioni sull'installazione il cmdlet di PowerShell Azure e la connessione al proprio abbonamento Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

4. Utilizzare il cmdlet **New-AzureService** per creare un contenitore di servizio cloud di Azure vuoto.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Seguire questo esempio per richiamare il cmdlet:
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Per ulteriori informazioni sulla creazione di servizio cloud Azure, eseguire:
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Passaggi successivi

 * Per gestire la distribuzione di servizio cloud, fare riferimento ai comandi [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Rimuovi AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)e [AzureService Set](https://msdn.microsoft.com/library/azure/dn495242.aspx) . È inoltre possibile fare riferimento come [configurare i servizi cloud](cloud-services-how-to-configure.md) per ulteriori informazioni.

 * Per pubblicare il progetto di servizio cloud in Azure, vedere l'esempio di codice **PublishCloudService.ps1** dalla [consegna continua per servizio cloud di Azure](cloud-services-dotnet-continuous-delivery.md).
