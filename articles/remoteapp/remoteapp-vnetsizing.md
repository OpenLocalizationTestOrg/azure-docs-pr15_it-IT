
<properties
    pageTitle="Informazioni per una VNET in Azure RemoteApp di ridimensionamento | Microsoft Azure"
    description="Informazioni sui requisiti di indirizzi IP per l'esecuzione con un VNET RemoteApp di Azure"
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



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informazioni di ridimensionamento per VNET in RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Quando si usa RemoteApp di Azure con una rete virtuale (VNET), RemoteApp utilizza indirizzi IP all'interno della subnet. In base a proporzioni del servizio RemoteApp, è necessario assicurarsi che subnet disponga di indirizzi IP sufficiente per macchine virtuali RemoteApp. Mentre questa Guida di ridimensionamento non è perfetta specificato come RemoteApp creata e dinamicamente ruota verso il basso macchine virtuali all'interno di una raccolta, esso consente di valutare l'intervallo di subnet. Questa operazione è particolarmente importante come dopo un servizio RemoteApp viene posizionato in un VNET, è possibile aumentare la dimensione subnet senza rimuovere RemoteApp.

Per ogni raccolta RemoteApp che si desidera eseguire la capacità massima, è necessario disporre 100 gli indirizzi IP disponibili. Ad esempio, se si dispone di un insieme di RemoteApp nel piano del Standard e si desidera che il numero massimo di 500 utenti, è 100 indirizzi IP per la raccolta. Analogamente, è necessario 100 indirizzi IP per una raccolta di RemoteApp nel piano di base contenente 800 utenti. Se si prevede che gli utenti possano meno (inferiore al massimo), è possibile ridurre gli indirizzi IP necessari per ogni raccolta. Requisiti di dimensione minima subnet sono 30 indirizzi IP (/ 27).

Le informazioni seguenti per assicurarsi che il VNET sia correttamente configurato e funzionante:

- [Eseguire la migrazione da un VNET personale a un VNET Azure](remoteapp-migratevnet.md)
- [Convalidare VNET Azure da utilizzare con RemoteApp di Azure](remoteapp-vnet.md)
