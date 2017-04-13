<properties
   pageTitle="Bilanciamento del carico più VIP per Azure | Microsoft Azure"
   description="Panoramica dei VIP più nel servizio di bilanciamento del carico Azure"
   services="load-balancer"
   documentationCenter="na"
   authors="chkuhtz"
   manager="narayan"
   editor=""
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/11/2016"
   ms.author="chkuhtz"
/>

# <a name="multiple-vips-for-azure-load-balancer"></a>Bilanciamento del carico più VIP per Azure

Azure bilanciamento del carico consente di caricare il bilanciamento dei servizi su più porte, più indirizzi IP o entrambe. È possibile utilizzare le definizioni di bilanciamento del carico pubblici e interni carica per caricare il bilanciamento dei flussi di un set di macchine virtuali.

Questo articolo descrive le nozioni fondamentali di questa possibilità di concetti importanti e vincoli. Se si intende solo esporre servizi su un indirizzo IP, è possibile trovare istruzioni semplificate per [pubbliche](load-balancer-get-started-internet-portal.md) o [interne](load-balancer-get-started-ilb-arm-portal.md) caricare le configurazioni di bilanciamento del carico. Aggiunta di più VIP è incrementale a una singola configurazione VIP. Con i concetti in questo articolo, è possibile espandere una configurazione semplificata in qualsiasi momento.

Quando si definisce un servizio di bilanciamento del carico di Azure, un front-end e una configurazione back-end sono connessi con le regole. Verifica dell'integrità a cui fa riferimento la regola viene utilizzata per determinare come nuovi flussi inviati a un nodo nel pool di back-end. Il front-end è definita da un virtuale IP (VIP), ovvero una tupla 3 costituita da un indirizzo IP (pubblico o interno), un protocollo di trasporto (UDP o TCP) e un numero di porta. Un DIP è un indirizzo IP in una scheda di rete virtuale Azure associato a una macchina virtuale nel pool di back-end.

Nella tabella seguente contiene alcune configurazioni front-end di esempio:

| VIP | Indirizzo IP | protocollo | porta |
|-----|------------|----------|------|
|1|65.52.0.1|TCP|80|
|2|65.52.0.1|TCP|_8080_|
|3|65.52.0.1|_UDP_|80|
|4|_65.52.0.2_|TCP|80|

Nella tabella sono quattro frontends diversi. Frontends #1 #2 e 3 # sono un singolo VIP con più regole. Viene utilizzato lo stesso indirizzo IP ma la porta o protocollo diverso per ogni front-end. 1 # Frontends e #4 sono un esempio di più VIP, in cui lo stesso frontend protocollo e porta sono riutilizzati tra più VIP.

Azure bilanciamento del carico offre flessibilità nella definizione delle regole di bilanciamento del carico. Una regola dichiara come porta nel front-end e un indirizzo mappato l'indirizzo di destinazione e la porta sul back-end. O meno porte back-end sono riutilizzate tra regole dipende dal tipo della regola. Ogni tipo di regola presenta requisiti specifici che possono influire sulla struttura di analisi e configurazione host. Esistono due tipi di regole:

1. La regola predefinita con riutilizzo non porta back-end
2. La regola IP mobile in cui vengono riutilizzate porte back-end

Azure bilanciamento del carico consente di combinare entrambi i tipi di regola in base alla configurazione di bilanciamento carico stesso. Bilanciamento del carico usarli contemporaneamente per una macchina virtuale specificata o qualsiasi combinazione, purché rispettare i vincoli della regola. Quale tipo di regola scelto dipende dal requisiti dell'applicazione e la complessità di supportare tale configurazione. È opportuno valutare quali tipi di regole sono ideali per lo scenario.

Vengono illustrati gli scenari seguenti ulteriormente iniziando il comportamento predefinito.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regola di tipo #1: nessuna riutilizzo porta back-end

![Figura MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In questo scenario VIP front-end possono essere configurate come indicato di seguito:

| VIP | Indirizzo IP | protocollo | porta |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

L'indirizzo DIP rappresenta la destinazione del flusso di in ingresso. Nel pool di back-end ogni macchina virtuale espone il servizio desiderato in una porta univoca su un DIP. Questo servizio è associato a front-end tramite una definizione di regola.

Sono definite due regole:

| Regola | Eseguire il mapping front-end | Al pool di back-end |
|------|--------------|-----------------|
| 1 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 | ![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

Le relazioni di mapping di bilanciamento del carico Azure è ora come indicato di seguito:

| Regola | Indirizzo IP VIP | protocollo | porta | Destinazione | porta |
|------|----------------|----------|------|-----|------|
|![regola](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|SONO indirizzo IP|80|
|![regola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|SONO indirizzo IP|81|

Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e la porta di destinazione. Modificando la porta di destinazione del flusso di più regole possono consegnare flussi DIP stesso su porte diverse.

Le ricerche integrità indirizzamento sempre a DIP di una macchina virtuale. È necessario che il sondaggio riflette lo stato della macchina virtuale.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regola di tipo #2: riutilizzo porta back-end tramite IP mobile

Bilanciamento del carico Azure flessibilità per riutilizzare la porta front-end tra più VIP indipendentemente dal tipo di regola utilizzata. Inoltre, alcuni scenari di applicazione preferiscono o richiedono la stessa porta che verrà utilizzato da più istanze dell'applicazione in una singola macchina virtuale nel pool di back-end. Esempi di riutilizzo porta includono cluster per disponibilità, dispositivi di rete ed esposti più endpoint TLS senza la riesecuzione della crittografia di rete.

Se si desidera riutilizzare la porta back-end tra più regole, è necessario abilitare IP mobile nella definizione della regola.

Mobile IP è una parte di quello che viene definito come diretto Server restituito DSR (). DSR è costituito da due parti: una topologia di flusso e un indirizzi IP di mappatura. Un livello di piattaforma, bilanciamento del carico Azure funziona sempre una topologia di flusso DSR indipendentemente dall'attivazione IP mobile o meno. Questo errore indica che la parte di un flusso di uscita è sempre correttamente riscrittura per trasferire direttamente sull'origine.

Con il tipo di regola predefinito Azure espone schema di mapping di indirizzi IP per semplificarne l'utilizzo di bilanciamento del carico tradizionale. Attivazione di indirizzi IP mobile cambia la combinazione di mapping di indirizzi IP per consentire per una maggiore flessibilità, come illustrato di seguito.

Il diagramma seguente illustra la configurazione:

![Figura MultiVIP](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Per questo scenario, ogni macchine Virtuali nel pool di back-end sono previsti tre interfacce di rete:

* DIP: una scheda NIC virtuale associata la macchina virtuale (risorsa NIC di Azure)
* VIP1: un'interfaccia loopback all'interno di sistema operativo configurata con l'indirizzo IP del VIP1 guest
* VIP2: un'interfaccia loopback all'interno di sistema operativo configurata con l'indirizzo IP del VIP2 guest

>[AZURE.IMPORTANT] Configurazione di interfacce logiche viene eseguita all'interno il sistema operativo guest. Questa configurazione non viene eseguita o gestita da Azure. Senza questa configurazione, le regole non funzionerà. Definizioni di verifica dell'integrità utilizzano DIP di macchina virtuale anziché VIP logici. Di conseguenza, il servizio deve fornire risposte verifica su una porta DIP che riflettono lo stato del servizio offerto della VIP logici.

Si supponga la stessa configurazione front-end come nello scenario precedente:

| VIP | Indirizzo IP | protocollo | porta |
|-----|------------|----------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|*65.52.0.2*|TCP|80|

Sono definite due regole:

| Regola | Eseguire il mapping front-end | Al pool di back-end |
|------|--------------|-----------------|
| 1 | ![regola](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) VIP1:80 (in VM1 e VM2) |
| 2 | ![regola](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 | ![back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) VIP2:80 (in VM1 e VM2) |

Nella tabella seguente mostra le relazioni di mapping di bilanciamento del carico:

| Regola | Indirizzo IP VIP | protocollo | porta | Destinazione | porta |
|------|----------------|----------|------|-------------|------|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1|65.52.0.1|TCP|80|diverso da quello di VIP (65.52.0.1)|diverso da quello di VIP (80)|
|![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2|65.52.0.2|TCP|80|diverso da quello di VIP (65.52.0.2)|diverso da quello di VIP (80)|

La destinazione del flusso di ingresso è l'indirizzo VIP nell'interfaccia loopback nella macchina virtuale. Ogni regola deve produrre un flusso con una combinazione univoca di indirizzo IP di destinazione e la porta di destinazione. Modificando l'indirizzo IP di destinazione del flusso di riutilizzo porta è possibile nella stessa macchina virtuale. Il servizio è esposto a bilanciamento del carico mediante l'associazione per l'indirizzo VIP indirizzo IP e porte dell'interfaccia di loopback rispettivi.

Si noti che, in questo esempio non cambia la porta di destinazione. Anche se si tratta di uno scenario di indirizzi IP mobile, bilanciamento del carico di Azure supporta anche la definizione di una regola di riscrittura porta di destinazione back-end e per creare diversi da porta di destinazione front-end.

Il tipo di regola IP mobile è la base dei diversi modelli di configurazione di bilanciamento carico. Un esempio che non è attualmente disponibile è la configurazione di [SQL AlwaysOn con più listener](../virtual-machines/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) . Nel tempo, dovranno documentare più di questi scenari.

## <a name="limitations"></a>Limitazioni

* Multipli VIP sono supportati solo con macchine virtuali IaaS.
* Con la regola IP mobile, l'applicazione è necessario utilizzare DIP per flussi in uscita. Se l'applicazione viene associato all'indirizzo VIP configurato sull'interfaccia loopback nel sistema operativo guest, quindi non è disponibile per il flusso in uscita di riscrittura SNAT e il flusso non riesce.
* Indirizzi IP pubblici hanno effetto sulla fatturazione. Per ulteriori informazioni, vedere [l'indirizzo IP prezzi](https://azure.microsoft.com/pricing/details/ip-addresses/)
* Limiti di sottoscrizione. Per ulteriori informazioni, vedere [limiti di servizio](../azure-subscription-service-limits.md#networking-limits) per informazioni dettagliate.
