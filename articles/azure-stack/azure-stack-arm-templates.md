<properties
    pageTitle="Utilizzare i modelli di gestione di risorse Azure in pila Azure (sviluppatori tenant) | Microsoft Azure"
    description="Informazioni su come usare i modelli di Manager delle risorse Azure in pila Azure per distribuire ed effettuare il provisioning di tutte le risorse per l'applicazione in un'operazione su una singola e coordinata."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Utilizzare i modelli di gestione di risorse Azure in pila di Azure

Modelli di gestione risorse Azure distribuire ed effettuare il provisioning di tutte le risorse per l'applicazione in un'operazione su una singola e coordinata. Definire le risorse per l'applicazione e come in cui verrà distribuito.  È anche possibile ridistribuire modelli per apportare modifiche alle risorse nel gruppo di risorse.

Questi modelli possono essere distribuiti con il portale di Microsoft Azure Stack, PowerShell, la riga di comando e Visual Studio.

[AZURE.VIDEO microsoft-azure-stack-tp1--foundational-skills-1-deploying-json-templates]

I modelli seguenti sono disponibili in [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-non-high-availability"></a>Distribuzione di SharePoint (non elevata disponibilità)

Utilizzare l'estensione DSC PowerShell per creare una farm di SharePoint 2013 che include le operazioni seguenti:

-   Una rete virtuale

-   Tre account di archiviazione

-   Due servizi di bilanciamento del carico esterno

-   Una macchina virtuale configurato come controller di dominio per una nuova foresta con un unico dominio

-   Una macchina virtuale configurato come un server SQL Server 2014 autonomo

-   Una macchina virtuale configurato come una farm di SharePoint 2013 un computer

## <a name="deploy-ad-non-high-availability"></a>Distribuire Active Directory (non elevata disponibilità)

Utilizzare l'estensione DSC PowerShell per creare un server di controller di dominio Active Directory che include le operazioni seguenti:

-   Una rete virtuale

-   Un account di archiviazione

-   Un servizio di bilanciamento del carico esterno

-   Una macchina virtuale configurato come controller di dominio per una nuova foresta con un unico dominio

## <a name="deploy-adsql-non-high-availability"></a>Distribuire Active Directory/SQL (non elevata disponibilità)

Utilizzare l'estensione DSC PowerShell per creare un server autonomo di SQL Server 2014 che include le operazioni seguenti:

-   Una rete virtuale

-   Due account di archiviazione

-   Un servizio di bilanciamento del carico esterno

-   Una macchina virtuale configurato come controller di dominio per una nuova foresta con un unico dominio

-   Una macchina virtuale configurato come un server SQL Server 2014 autonomo

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Utilizzare l'estensione DSC PowerShell per configurare una macchina virtuale esistente locale configurazione Manager (MCM) e registrare a un Server di estrarre DSC di Azure automazione Account.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Creare una macchina virtuale da un'immagine utente

Creare una macchina virtuale da un'immagine utente personalizzate. Questo modello distribuisce inoltre una rete virtuale (con DNS), indirizzo IP pubblico e un'interfaccia di rete.

## <a name="simple-vm"></a>Macchine Virtuali semplice

Distribuire una semplice macchina virtuale di Windows che include una rete virtuale (con DNS), indirizzo IP pubblico e un'interfaccia di rete.

## <a name="cancel-a-running-template-deployment"></a>Annullamento di una distribuzione di modelli in esecuzione

Per annullare una distribuzione di modelli in esecuzione, utilizzare la `Stop-AzureRmResourceGroupDeployment` cmdlet di PowerShell.


## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con il portale](azure-stack-deploy-template-portal.md)

[Panoramica di gestione risorse Azure](../azure-resource-manager/resource-group-overview.md)

