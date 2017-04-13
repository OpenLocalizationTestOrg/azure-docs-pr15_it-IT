<properties
   pageTitle="Distribuzione di dispositivi di rete in disponibilità | Microsoft Azure"
   description="Informazioni su come distribuire rete dispositivi di rete in disponibilità."
   services=""
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="telmos"/>

# <a name="deploying-virtual-appliances-in-high-availability"></a>Distribuzione di dispositivi di rete di disponibilità

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Questo articolo illustra un insieme di procedure consigliate per la distribuzione di rete dispositivi di rete (NVAs) in modo disponibilità. Prima di continuare, assicurarsi di sapere come [route definite dall'utente (UDR)] [ udr-overview] e [bilanciamento del carico] [ lb-overview] funzionano in Azure.

È possibile utilizzare diversi NVAs disponibile in Azure marketplace per estendere la funzionalità di Azure nello stesso modo che si usa accessori nel data center locale. La figura seguente mostra una distribuzione di esempio di un [singolo NVA] [ nva-scenario] come un firewall semplice. 

![[0]][0]

La distribuzione precedente funziona, introduce un singolo punto di errore. Se il dispositivo virtuale non riesce, il traffico non scorre. Per risolvere il problema, è necessario utilizzare più NVAs. Tuttavia, che richiede anche altre impostazioni e le risorse da utilizzare in base ai propri requisiti.

È possibile utilizzare una delle seguenti soluzioni per distribuire un ambiente NVA altamente disponibile.

|Soluzione|Vantaggi|Considerazioni|
|---|---|---|
|Ingresso con livello 7 dispositivi di rete|Tutti i nodi sono attivi|Richiede un NVA che può terminare connessioni e utilizzare SNAT<br/>Richiede un gruppo distinto di NVAs per il traffico provenienti da Internet e da Azure<br/>Può essere utilizzato solo per il traffico originari all'esterno di Azure|
|Uscita ingresso con livello 7 dispositivi di rete|Tutti i nodi sono attivi<br/>Grado di gestire il traffico generato in Azure |Richiede un NVA che può terminare connessioni e utilizzare SNAT<br/>Richiede un gruppo distinto di NVAs per il traffico provenienti da Internet e da Azure|
|Opzione PIP UDR|Singolo set di NVAs per tutto il traffico<br/>In grado di gestire tutto il traffico (nessun limite alle regole di porta)|Attiva passiva<br/>Richiede un processo di failover|

## <a name="ingress-with-layer-7-virtual-appliances"></a>Ingresso con livello 7 dispositivi di rete
È possibile usare un set di NVAs dietro a un servizio di bilanciamento del carico Azure per fornire la connettività a carichi di lavoro di Azure dietro un piccolo gruppo di porte sul lato server (ad esempio HTTP e HTTPS). Nella figura seguente evidenzia come fornire disponibilità in questo scenario livello NVA.

![[1]][1]

In questo scenario, il dispositivo di rete virtuale utilizzato deve terminare tutte le connessioni e li passa alla subnet carico di lavoro. Il carico di lavoro macchine () rispondono al NVA che ha ricevuto una richiesta da e flussi di traffico senza problemi. 

## <a name="ingress-egress-with-layer-7-virtual-appliances"></a>Uscita ingresso con livello 7 dispositivi di rete
È possibile estendere l'architettura precedente e aggiungere un'altra coppia di NVAs per gestire il traffico provenienti da Azure da gestire con NVAs, come illustrato nella figura seguente:

![[2]][2]

In questo scenario, tutto il traffico originari Azure viene inviato a un interno bilanciamento del carico che distribuisce carico tra un set di NVAs diverso. Questi NVAs indirizzare il traffico a Internet utilizzando i singoli indirizzi IP pubblici. 

## <a name="pip-udr-switch"></a>Opzione PIP UDR
È possibile evitare la creazione di più stack NVA tramite due NVAs in modalità attiva passiva. In questo scenario, è possibile passare l'indirizzo IP pubblico (PIP) e indirizza definite dall'utente (UDRs) quando si interrompe il nodo attivo.  

![[3]][3]

Questo scenario è simile a questo scenario NVA. L'unica differenza è che il PIP e UDRs deve essere modificata per passare il traffico tra il NVAs. Queste modifiche possono essere eseguite manualmente o è anche possibile automatizzarle. Per automatizzare il processo, è possibile distribuire un'applicazione di entrambi NVAs che verifica la presenza di stato di integrità del nodo attivo. Una volta nodo attivo verso il basso, l'applicazione può modificare il PIP e UDRs da collegare al nodo passivo.

Una possibile implementazione di questa soluzione consiste nell'usare una [ZooKeeper] [ zookeeper] daemon su NVAs per gestire scelta coordinatore (decidere il nodo attivo). Dopo aver scelto di un carattere di riempimento, chiama all'API REST Azure per rimuovere il nodo PIP e collegarlo al coordinatore. È necessario modificare anche UDRs in modo che puntino all'indirizzo IP privato del carattere di riempimento nuovo.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sull'implementazione di una rete Perimetrale tra Azure e la data center locale] [ dmz-on-prem] utilizzando NVAs livello 7.
- Informazioni [sull'implementazione di una rete Perimetrale tra Azure e Internet] [ dmz-internet] utilizzando NVAs livello 7.

<!-- links -->
[udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[lb-overview]: ../load-balancer/load-balancer-overview.md
[zookeeper]: https://zookeeper.apache.org/
[nva-scenario]: ../virtual-network/virtual-network-scenario-udr-gw-nva.md
[dmz-on-prem]: guidance-iaas-ra-secure-vnet-hybrid.md
[dmz-internet]: guidance-iaas-ra-secure-vnet-dmz.md

<!-- images -->
[0]: ./media/guidance-nva-ha/single-nva.png "Architettura NVA singola"
[1]: ./media/guidance-nva-ha/l7-ingress.png "Ingresso livello 7"
[2]: ./media/guidance-nva-ha/l7-ingress-egress.png "Livello 7 ingresso e in uscita"
[3]: ./media/guidance-nva-ha/active-passive.png "Attivo passivo"