<properties
    pageTitle="Servizi PaaS e gli strumenti per Stack di Azure | Microsoft Azure"
    description="Informazioni su come iniziare a utilizzare i servizi PaaS in pila Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="tools-for-azure-stack"></a>Strumenti per Stack di Azure

È possibile scaricare gli strumenti descritti di seguito. Se si vuole ricevere una notifica degli strumenti e nuovi servizi, eseguire #AzureStack su Twitter.

## <a name="template-tools"></a>Strumenti di modello

### <a name="azure-stack-github-templates"></a>Azure Stack Github modelli
Esplorare l'insieme crescente di [Azure Stack GitHub modelli](https://github.com/Azure/AzureStack-QuickStart-Templates). Come [Azure](https://github.com/Azure/azure-quickstart-templates), questi modelli di gestione di risorse Azure "Guida introduttiva" puntano a iniziare a usare blocchi di base ed esempi, pronti per distribuire in ambiente di Microsoft Azure Stack Technical Preview prova di concetto. Sono disponibili prima degli esempi di carico di lavoro di terze parti per [Active Directory-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha), [sql-2014-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha), [sharepoint-2013-non-ha](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha), nonché molte 101 modelli semplici vuole [101-semplici-windows-macchina virtuale](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm).


### <a name="marketplace-item-packaging-tool-and-sample"></a>Esempio e strumento elemento marketplace
[Scaricare e usare lo strumento](http://www.aka.ms/azurestackmarketplaceitem) per creare elementi marketplace per i modelli personalizzati aggiungere al marketplace dello Stack di Azure. Sono disponibili istruzioni su come creare un elemento marketplace e renderlo disponibile per il tenant [nell'elemento creare Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="developer-tools"></a>Strumenti per sviluppatori


### <a name="use-visual-studio-and-azure-stack-tp2-on-the-mas-con01-virtual-machine"></a>Utilizzo di Visual Studio e Azure Stack TP2 la macchina virtuale MAS CON01
Se si desidera utilizzare Visual Studio nella console di macchine Virtuali per lavorare con i modelli di Stack di Azure, è necessario installare le versioni corrette gli strumenti necessari. Utilizzare la procedura seguente per installare le versioni supportate per TP2.

1. Utilizzare connessione Desktop remoto agli utenti di accedere al computer virtuale MAS CON01 con le credenziali azurestack\azurestackadmin.
2. Installare e aprire Installazione guidata piattaforma Web.
3. Individuare e installare **Visual Studio Community 2015 con Microsoft Azure SDK - 2.9.5**.
4. Uso di **Aggiungi/Rimuovi programmi**, disinstallare **Microsoft Azure PowerShell (settembre)** che viene installato come parte di SDK.
5. Aprire il programma di installazione di piattaforma Web.
6. Individuare e installare **Microsoft Azure PowerShell - Azure Stack Technical Preview 2**. 
7. Riavviare il computer virtuale MAS CON01.
8. Utilizzare connessione Desktop remoto agli utenti di accedere al computer virtuale MAS CON01 con le credenziali azurestack\azurestackadmin.
9. Aprire Visual Studio e verificare che è possibile connettersi all'ambiente dello Stack di Azure, ottenere modelli e così via. 

### <a name="azure-powershell-sdk"></a>Azure SDK PowerShell
PowerShell Azure è un modulo che fornisce i cmdlet per la gestione di Azure e Azure Stack con Windows PowerShell. È possibile utilizzare i cmdlet per creare, testare e distribuire e gestire soluzioni e i servizi recapitati mediante la piattaforma Azure Stack.
[Azure PowerShell SDK scaricare](http://aka.ms/azStackPsh) e [installare PowerShell](azure-stack-connect-powershell.md).

### <a name="azure-cross-platform-command-line-interfaces"></a>Azure cross interfacce della riga di comando piattaforma
Installare rapidamente l'interfaccia della riga di comando Azure (Azure CLI) per l'utilizzo di un insieme di comandi basate su shell Apri origine per la creazione e gestione delle risorse in Microsoft Azure pila.

[Scaricare Windows CLI](http://aka.ms/azstack-windows-cli)

[Scaricare Mac CLI](http://aka.ms/azstack-linux-cli)

[Scaricare CLI Linux](http://aka.ms/azstack-mac-cli)

>[AZURE.NOTE]
>
> + Se si è in un computer Mac o Linux, è inoltre possibile ottenere CLI tramite il comando`npm install -g azure-cli@0.9.11`</br>
> + Se si ricevono errori di convalida certificato, eseguire il comando`set NODE_TLS_REJECT_UNAUTHORIZED=0`
