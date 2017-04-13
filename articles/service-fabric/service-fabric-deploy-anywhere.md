<properties
   pageTitle="Creare cluster di Azure servizio tessuti in Windows Server e Linux | Microsoft Azure"
   description="Cluster tessuti servizio eseguire in Windows Server e Linux, vale a dire sarà possibile distribuire e applicazioni di servizio tessuti host in un punto qualsiasi è possibile eseguire Windows Server o Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="chackdan"/>

# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>Creare tessuti servizio cluster in Windows Server o Linux

Tessuti servizio Azure consente la creazione di tessuti servizio cluster su qualsiasi macchine virtuali o computer che eseguono Windows Server o Linux. Significa che è possibile distribuire ed eseguire le applicazioni di servizio tessuti in qualsiasi ambiente in cui si dispone di un insieme di computer Windows Server o Linux interconnessi, che può essere locale, Microsoft Azure o con un provider del cloud.

##<a name="create-service-fabric-clusters-on-azure"></a>Creare tessuti servizio cluster su Azure

Creazione di un cluster in Azure viene eseguita tramite un modello di risorse o il portale di Azure. Per ulteriori informazioni, vedere [creare un cluster di servizio tessuti usando un modello di Manager delle risorse](service-fabric-cluster-creation-via-arm.md) o [creare un cluster di servizio tessuti dal portale di Azure](service-fabric-cluster-creation-via-portal.md) .

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Sistemi operativi supportati per i cluster in Azure

In grado di creare cluster in macchine virtuali su questi sistemi operativi:

* Windows Server 2012 R2
* Windows Server 2016 (dopo viene annunciato come generalmente disponibile)
* 16.04 Ubuntu Linux (in pubblica versione di anteprima) 


##<a name="create-service-fabric-standalone-clusters-on-premise-or-with-any-cloud-provider"></a>Creare tessuti servizio autonomo cluster locale o con un provider del cloud

Servizio tessuti fornisce un pacchetto di installazione per poter creare autonoma tessuti servizio cluster locale o su un provider del cloud

Per ulteriori informazioni sull'impostazione di cluster di tessuti servizio autonomo in Windows Server, leggere [la creazione di cluster tessuti servizio per Windows Server](service-fabric-cluster-creation-for-windows-server.md)

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>Qualsiasi distribuzioni cloud e distribuzioni locali
Il processo di creazione di un cluster di servizio tessuti locale è simile al processo di creazione di un cluster in qualsiasi cloud di propria scelta con una serie di macchine virtuali. La procedura iniziale per effettuare il provisioning di macchine virtuali dipendono dal provider cloud o ambiente locale che si sta utilizzando. Dopo avere inserito un set di macchine virtuali con la connettività di rete abilitata tra di esse, quindi la procedura per configurare il pacchetto di servizio tessuti, modificare le impostazioni di cluster, la creazione di cluster ed eseguire gli script di gestione sono identici. In questo modo che la conoscenza ed esperienza di gestione e la gestione dei servizi tessuti cluster è trasferibile quando si sceglie di nuovi ambienti di hosting di destinazione.

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>Vantaggi della creazione di cluster tessuti servizio autonomo
* Sono gratuite scegliere un provider del cloud per ospitare il cluster.
* Le applicazioni di servizio tessuti, una volta sviluppate, possono essere eseguite in più ambienti hosting con minime su Nessuna modifica.
* Conoscere la creazione di applicazioni di servizio tessuti viene mantenuta da un ambiente di hosting in un altro.
* Esperienza di esecuzione e gestione dei servizi tessuti cluster esegue su da un ambiente a altro.
* Cliente ampia portata è illimitata ambiente vincoli di hosting.
* Un livello aggiuntivo di affidabilità e protezione contro interruzioni diffusione esiste perché è possibile spostare i servizi su in un altro ambiente di distribuzione se dispone di un provider di dati al centro o cloud blackout.

## <a name="supported-operating-systems-for-standalone-clusters"></a>Sistemi operativi supportati per i cluster autonomo
In grado di creare cluster in macchine virtuali o un computer che eseguono questi sistemi operativi:

* Windows Server 2012 R2
* Windows Server 2016 (dopo viene annunciato come generalmente disponibile)
* Linux (disponibile a breve)

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>Vantaggi di servizio tessuti rispetto ai cluster su Azure autonoma che tessuti servizio cluster creato in locale

L'esecuzione di tessuti servizio cluster su Azure offre vantaggi rispetto locale in opzione, anche se non si dispone di esigenze specifiche per cui è in esecuzione il cluster, quindi è consigliabile eseguirli in Azure. In Azure, sono disponibili l'integrazione con altri servizi, semplificando così operazioni e gestione del cluster più semplice e affidabile e caratteristiche di Azure.

* **Portal azure:** Portale Azure consente di creare e gestire cluster facilmente.

* **Gestione risorse di azure:** Utilizzo di gestione di risorse di Azure consente di gestire semplice di tutte le risorse utilizzate dal cluster come unità e semplifica la verifica di costi e fatturazione.
* **Servizio tessuti Cluster come risorsa Azure** Un cluster di servizio tessuti è una risorsa ARM, in modo che è possibile modellare come altre risorse ARM in Azure.
* **Integrazione con infrastruttura di Azure** Servizio tessuti interagisce con l'infrastruttura di Azure sottostante per OS, rete e altri aggiornamenti migliorare la disponibilità e l'affidabilità delle applicazioni.  
* **Diagnostica:** In Azure, sono disponibili l'integrazione con diagnostica Windows Azure e Log Analitica.
* **Il ridimensionamento automatico:** Per i cluster in Azure, sono disponibili funzionalità di adattamento automatico incorporata a causa di set di scala macchina virtuale. In locale e in altri ambienti cloud, è necessario creare il proprio funzionalità o una scala manualmente utilizzando le API esposti tessuti servizio per il ridimensionamento cluster il ridimensionamento automatico.

## <a name="next-steps"></a>Passaggi successivi
Creare un cluster in macchine virtuali o un computer che eseguono Windows Server: [creazione di cluster tessuti servizio per Windows Server](service-fabric-cluster-creation-for-windows-server.md)

Creare un cluster in macchine virtuali o un computer che eseguono Linux: [Servizio tessuti su Linux](service-fabric-linux-overview.md)
