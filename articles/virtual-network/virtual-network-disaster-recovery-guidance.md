<properties
    pageTitle="Operazioni da eseguire in caso di un'interruzione del servizio Azure impatto reti virtuali Azure | Microsoft Azure"
    description="Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure impatto reti virtuali Azure."
    services="virtual-network"
    documentationCenter=""
    authors="NarayanAnnamalai"
    manager="jefco"
    editor=""/>

<tags
    ms.service="virtual-network"
    ms.workload="virtual-network"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/16/2016"
    ms.author="narayan;aglick"/>

#<a name="virtual-network--business-continuity"></a>Virtuali – continuità aziendale

##<a name="overview"></a>Panoramica

Una rete virtuale (VNet) è una rappresentazione logica della rete nel cloud. Consente di definire il proprio spazio di indirizzi IP privato e segmento della rete in subnet. VNets funge da un limite di protezione per ospitare le risorse di elaborazione, ad esempio macchine virtuali di Azure e servizi Cloud (ruoli web/lavoro). Un VNet consente la comunicazione IP privata diretta tra le risorse ospitate al suo interno. Una rete virtuale possono essere collegata anche a una rete locale attraverso una delle opzioni di distribuzione ibrida, ad esempio un Gateway VPN o ExpressRoute.
 
Viene creata una VNet nell'ambito di un'area. È possibile creare VNets con stesso spazio di indirizzi in due aree geografiche diverse (ovvero Contattaci est e Contattaci ovest ma non è possibile collegarle reciproco direttamente). 

##<a name="business-continuity"></a>Continuità aziendale

Possono essere diversi modi che l'applicazione potrebbe essere interrotto. Una determinata regione potrebbe essere troncata completamente a causa di emergenza naturale o parziale emergenza a causa di un errore di più dispositivi/servizi. L'impatto sul servizio VNet è diverso in ognuna di queste situazioni.

**D: in che modo in caso di interruzione per un'intera regione? ad esempio, se è un'area completamente limita a causa di emergenza naturale? Cosa succede alle reti virtuali ospitati nell'area?**

R: alla rete virtuale e le risorse nell'area interessata rimane inaccessibile durante il periodo di interruzione del servizio.

![Diagramma reticolare virtuale semplice](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**D: cosa è possibile ricreare la stessa rete virtuale in un paese diverso?**

R: virtuali (VNet) sono abbastanza semplice risorsa. È possibile richiamare API di Azure per creare un VNet con lo stesso spazio indirizzo in un'area diversa. Per ricreare lo stesso ambiente che era presente nell'area interessata, è necessario effettuare chiamate API per la ridistribuzione servizi Cloud (ruoli web/lavoro) e macchine virtuali di cui era. È anche necessario aggiornarsi un Gateway VPN e connettersi alla rete locale se si dispone di connettività locale (come in una distribuzione ibrida).

Le istruzioni per la creazione di un VNet sono disponibili [qui](./virtual-networks-create-vnet-arm-pportal.md). 

**D: è possibile una replica di VNet in una determinata regione creare di nuovo in un'altra area anticipo?**

R: Sì, è possibile creare due VNets con gli stessi privato spazio di indirizzi IP e le risorse in due aree geografiche diverse anticipo. Se un cliente è stata hosting di servizi del VNet è connessa a internet, vengano Impossibile impostare Gestione il traffico a geografico instradare il traffico verso l'area che è attivo. Tuttavia, un cliente non riesce a connettersi due VNets con lo stesso spazio indirizzo per la rete locale poiché comporterebbe problemi di routing. Al momento di emergenza e perdita di VNet in un'area, un cliente in grado di altri VNet nell'area disponibile corrispondente spazio di indirizzi per la rete locale.

Le istruzioni per la creazione di un VNet sono disponibili [qui](./virtual-networks-create-vnet-arm-pportal.md).
