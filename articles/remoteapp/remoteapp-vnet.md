
<properties
    pageTitle="Convalidare VNET Azure da utilizzare con Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come assicurarsi che il VNET Azure sia pronto per l'utilizzo con RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Convalidare VNET Azure da utilizzare con RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Prima di utilizzare un VNET Azure con RemoteApp di Azure, è consigliabile convalidare la VNET. Consente di evitare problemi di connettività.

Per convalidare il VNET Azure, eseguire le operazioni seguenti:

1. Creare una macchina virtuale Azure all'interno della subnet di VNET Azure da usare con RemoteApp di Azure.

2. Connettersi a quella macchina virtuale utilizzando l'opzione **Connetti** nel portale di gestione.
3. Aggiungere la macchina virtuale allo stesso dominio che si desidera utilizzare con RemoteApp di Azure. Se si sta creando un insieme di ibrido che si connette alla rete locale, aggiungere la macchina virtuale al proprio dominio locale.

In caso di esito negativo, VNET Azure è pronto per l'uso con RemoteApp.

Per ulteriori informazioni sul flusso di lavoro Raccolta-to-end ibride, vedere gli articoli seguenti:

- [Come pianificare la rete virtuale RemoteApp di Azure](remoteapp-planvnet.md)
- [Creare una raccolta ibrido](remoteapp-create-hybrid-deployment.md)
- [Distribuire Azure RemoteApp insieme alla rete virtuale Azure (con supporto per ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)
