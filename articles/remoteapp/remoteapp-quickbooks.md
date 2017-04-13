<properties 
    pageTitle="Distribuire QuickBooks in Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come condividere QuickBooks con RemoteApp di Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-do-you-deploy-quickbooks-in-azure-remoteapp"></a>Come distribuire QuickBooks in Azure RemoteApp?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Usare le informazioni seguenti per la condivisione di QuickBooks come un'app di Azure RemoteApp.


È possibile condividere QuickBooks 2015 Enterprise con Azure RemoteApp nella raccolta di una distribuzione ibrida o cloud. Il file della società deve trovarsi in una macchina virtuale che esegue server di database di QuickBooks che è distinto dal server Azure RemoteApp. Non memorizzare mai file della società nell'immagine RemoteApp di Azure - perdita di dati è previsto se si esegue questa operazione. Solo QuickBooks Enterprise supporta l'hosting il file di QuickBooks in una condivisione esterna con il server di database di QuickBooks accessibile tramite reti Windows standard.   

> [AZURE.IMPORTANT] Il server di database di QuickBooks in cui si trova il file della società deve trovarsi in una macchina virtuale separata all'interno di VNET stesso come insieme RemoteApp di Azure.  

## <a name="steps-to-deploy-quickbooks"></a>Procedura per la distribuzione di QuickBooks

1. Creare una macchina virtuale Azure e installare QuickBooks, server di database QuickBooks e copiare il file della società in una macchina virtuale Azure.  Assicurarsi di configurare correttamente le regole firewall.
2. Installare QuickBooks in un' [immagine personalizzata](remoteapp-imageoptions.md) e creare un [insieme di Azure RemoteApp](remoteapp-collections.md), cloud o ibrida, all'interno di VNET stessa esatta in cui si trova la macchina virtuale che ospita il server di database QuickBooks con i file della società. 
3.  [Pubblicare](remoteapp-publish.md) App di QuickBooks agli utenti
4.  Avviare il client di QuickBooks ospitato RemoteApp di Azure, passare tramite Windows standard per la macchina virtuale che ospita il server di database di QuickBooks e aprire il file della società. 

## <a name="documentation-references"></a>Documentazione riferimenti

- QuickBooks [configurazioni supportate](http://enterprisesuite.intuit.com/products/enterprise-solutions/technical/#top)
- [Opzioni di distribuzione](http://enterprisesuite.intuit.com/everythingenterprise/launchpad/new-user/) di QuickBooks

È anche possibile estrarre Ignite presentazione, [Nozioni fondamentali di Microsoft Azure RemoteApp gestione e amministrazione](https://channel9.msdn.com/Events/Ignite/2015/BRK3868) - invece 1:02:45 per ottenere la parte di QuickBooks.

## <a name="deployment-architecture"></a>Architettura di distribuzione

![QuickBooks + distribuzione RemoteApp Azure](./media/remoteapp-quickbooks/ra-quickbooks.png)