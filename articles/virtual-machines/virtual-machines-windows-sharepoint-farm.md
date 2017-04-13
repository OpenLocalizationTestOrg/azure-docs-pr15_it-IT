<properties
    pageTitle="Creare server farm di SharePoint | Microsoft Azure"
    description="Creare rapidamente una nuova farm di SharePoint 2013 o 2016 di SharePoint in Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2016"
    ms.author="josephd"/>

# <a name="create-sharepoint-server-farms"></a>Creare server farm di SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello classico.

## <a name="sharepoint-2013-farms"></a>Farm di SharePoint 2013

Con Microsoft Azure marketplace portale, è possibile creare rapidamente preconfigurata farm di SharePoint Server 2013. Questo può risparmiare molto tempo quando è necessaria una farm di SharePoint di base o disponibilità per un ambiente di sviluppo/test o se si sta valutando SharePoint Server 2013 come una soluzione di collaborazione per l'organizzazione.

> [AZURE.NOTE] L'elemento **Server Farm di SharePoint** in Azure Marketplace del portale di Azure è stata rimossa. È stata sostituita con gli elementi **Non-HA Farm di SharePoint 2013** e **SharePoint 2013 HA Farm** .

La farm di SharePoint di base è costituito da tre macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

È possibile utilizzare questa configurazione farm per un'installazione semplificata per lo sviluppo di app di SharePoint o la valutazione prima di SharePoint 2013.

Per creare la farm di SharePoint (tre server) base:

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Fare clic su **Distribuisci**.
3. Nel riquadro **Non-HA Farm di SharePoint 2013** , fare clic su **Crea**.
4. Specificare le impostazioni dei passaggi del riquadro **Creare SharePoint 2013 non HA Farm** e quindi fare clic su **Crea**.

La farm di SharePoint di disponibilità è costituito da nove macchine virtuali in questa configurazione.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

È possibile utilizzare questa configurazione farm per testare carichi client superiori, disponibilità del sito di SharePoint esterno e disponibilità AlwaysOn di SQL Server per una farm di SharePoint. È anche possibile utilizzare questa configurazione per lo sviluppo di app di SharePoint in un ambiente di disponibilità.

Per creare la farm di SharePoint di disponibilità (nove server):

1. Fare clic [qui](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Fare clic su **Distribuisci**.
3. Nel riquadro **Della Farm di SharePoint 2013 elevata** , fare clic su **Crea**.
4. Specificare le impostazioni dei passaggi sette del riquadro **Creare 2013 HA Farm di SharePoint** e quindi fare clic su **Crea**.

> [AZURE.NOTE] È possibile creare il **Non-HA Farm di SharePoint 2013** o **Farm di SharePoint 2013 HA** con una versione di valutazione gratuita di Azure.

Portale di Azure crea entrambe queste farm in una rete virtuale basata solo su cloud con una presenza sul web esposto a Internet. Non esiste nessuna connessione di rete VPN o ExpressRoute da sito alla rete dell'organizzazione.

> [AZURE.NOTE] Quando si crea la base o farm di SharePoint disponibilità tramite il portale di Azure, non è possibile specificare un gruppo di risorse esistenti. Per risolvere questa limitazione, creare tali farm con PowerShell Azure. Per ulteriori informazioni, vedere [creazione di SharePoint 2013 dev/test farm con PowerShell Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).

## <a name="sharepoint-2016-farms"></a>Farm di SharePoint 2016

Vedere [questo articolo](https://technet.microsoft.com/library/mt723354.aspx) per le istruzioni per creare la farm di SharePoint Server 2016 server singolo seguente.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gestire la farm di SharePoint

È possibile amministrare il server di queste farm tramite connessioni Remote Desktop. Per ulteriori informazioni, vedere [accedere al computer virtuale](virtual-machines-windows-hero-tutorial.md#log-on-to-the-virtual-machine).

Dal sito di SharePoint di amministrazione centrale, è possibile configurare i siti personali, applicazioni di SharePoint e altre funzionalità. Per ulteriori informazioni, vedere [Configurare SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Passaggi successivi

- Alla scoperta di [SharePoint configurazioni](https://technet.microsoft.com/library/dn635309.aspx) aggiuntive in servizi di infrastruttura Azure.
