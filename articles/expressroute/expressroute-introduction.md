<properties 
   pageTitle="Introduzione a ExpressRoute | Microsoft Azure"
   description="Questa pagina offre una panoramica del servizio ExpressRoute, tra cui funzionamento di una connessione ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/10/2016"
   ms.author="cherylmc"/>

# <a name="expressroute-technical-overview"></a>Panoramica tecnica ExpressRoute

Microsoft Azure ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata dedicata mediante un provider di integrazione applicativa. Con ExpressRoute, è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure, Office 365 e CRM Online. Connettività può essere da una qualsiasi a qualsiasi rete (IP VPN), una rete Ethernet punto o una connessione tra virtuale tramite un provider di integrazione applicativa in una struttura di posizione condivisa. Connessioni ExpressRoute va tramite Internet. In questo modo connessioni ExpressRoute per l'offerta più affidabilità, velocità, latenza inferiore e protezione più elevata rispetto alle connessioni tipiche tramite Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Vantaggi principali includono:**

- Livello 3 connettività di rete locale e nel Cloud Microsoft tramite un provider di integrazione applicativa. Connettività può essere da una qualsiasi a qualsiasi rete (IPVPN), una connessione punto a punto Ethernet o tramite una connessione tra virtuale tramite un exchange Ethernet.
- Connettività ai servizi cloud Microsoft attraverso tutte le aree nell'area geopolitiche.
- Connettività a Microsoft services tra tutte le aree con il componente aggiuntivo premium ExpressRoute globale.
- Dinamico routing tra la rete e Microsoft su protocolli standard (BGP).
- Ridondanza incorporata in ogni posizione peering per maggiore affidabilità.
- Connessione dei tempi di attività [contratto di servizio](https://azure.microsoft.com/support/legal/sla/).
- QoS e il supporto per più classi del servizio per applicazioni speciali, ad esempio Skype per le aziende.

Vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) per altri dettagli.

## <a name="howtoconnect"></a>Come è possibile collegare la rete a Microsoft utilizzando ExpressRoute?

È possibile creare una connessione tra la rete locale e nel cloud Microsoft in tre modi diversi:

### <a name="co-located-at-a-cloud-exchange"></a>Presenti in un exchange cloud

Se sono situati in una struttura con un exchange cloud, è possibile ordinare le connessioni tra virtuale nel cloud Microsoft tramite exchange Ethernet del provider di posizione condivisa. Provider di posizione condivisa può offrire cross di livello 2 per le connessioni o Layer 3 gestito tra connessioni tra l'infrastruttura la funzione di co-ubicazione e nel cloud Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Connessioni Ethernet punto a punto 

È possibile connettersi i Data Center/uffici locali al cloud Microsoft tramite collegamenti Ethernet punto. Provider Ethernet punto può offrire connessioni di livello 2 o gestito Layer 3 connessioni tra il sito e il cloud Microsoft.

### <a name="any-to-any-ipvpn-networks"></a>Qualsiasi per le reti (IPVPN)

È possibile integrare il WAN con il cloud Microsoft. Provider IPVPN (in genere MPLS VPN) offrono qualsiasi a qualsiasi connessione tra nelle sedi e Data Center. Microsoft cloud può essere interconnessi per la rete WAN per conferirgli esattamente come qualsiasi altro filiale. Provider di servizi WAN offrono in genere gestita connettività di livello 3. Caratteristiche e funzionalità ExpressRoute sono tutti identici per tutti i modelli di integrazione applicativa sopra. 

Provider di integrazione applicativa può offrire uno o più modelli di integrazione applicativa. È possibile lavorare con il provider di integrazione applicativa per selezionare il modello più adatto alle proprie necessità.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## <a name="expressroute-features"></a>Caratteristiche ExpressRoute

ExpressRoute supporta le caratteristiche seguenti: 

### <a name="layer-3-connectivity"></a>Connettività di livello 3

Microsoft utilizza dinamico routing protocollo standard (BGP) per scambiare indirizza tra la rete locale, le istanze di Azure e Microsoft indirizzi pubblici.  Stabilire più sessioni BGP alla rete aziendale per i profili di traffico diversi. Sono disponibili ulteriori informazioni vedere l'articolo [ExpressRoute circuito e il routing di domini](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Ridondanza

Ogni circuito ExpressRoute è costituito da due connessioni a due router bordo Microsoft Enterprise (MSEEs) dal provider di integrazione applicativa / il bordo di rete. Microsoft richiederà doppio connessione BGP dal provider di integrazione applicativa / il lato: uno per ogni MSEE. È possibile scegliere di non distribuire dispositivi ridondanti / Ethernet circuiti l'estremità. Tuttavia, i provider di integrazione applicativa utilizzano dispositivi ridondanti per garantire che le connessioni vengono passate a Microsoft in modo ridondante. La configurazione di connettività di livello 3 ridondante è un requisito per il [contratto di servizio](https://azure.microsoft.com/support/legal/sla/) valido. 

### <a name="connectivity-to-microsoft-cloud-services"></a>Connettività ai servizi cloud Microsoft

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Connessioni ExpressRoute consentono di accedere ai servizi seguenti:

- Servizi di Microsoft Azure
- Servizi di Microsoft Office 365
- Servizi Microsoft CRM Online 
 
È possibile visitare la pagina delle [Domande ExpressRoute](expressroute-faqs.md) per un elenco dettagliato dei servizi supportati su ExpressRoute.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connettività a tutte le aree in un'area geopolitiche

È possibile connettersi a Microsoft in uno dei [percorsi peering](expressroute-locations.md) nostro e ha accesso a tutte le aree all'interno dell'area geopolitiche. 

Ad esempio, se si è connessi a Microsoft Amsterdam tramite ExpressRoute, si avrà accesso a tutti i servizi cloud Microsoft ospitato in Nord Europa e occidentale. Fare riferimento all'articolo [peering posizioni e partner ExpressRoute](expressroute-locations.md) per una panoramica delle aree geopolitiche, aree di cloud Microsoft associate e posizioni peering ExpressRoute corrispondenti.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Connettività globale con il componente aggiuntivo premium ExpressRoute

È possibile abilitare la caratteristica di componente aggiuntivo premium ExpressRoute estendere la connettività oltre i confini geopolitici. Ad esempio, se si è connessi a Microsoft Amsterdam tramite ExpressRoute, si avrà accesso a tutti i servizi cloud Microsoft ospitato in tutti i paesi in tutto il mondo (nuvole nazionali sono esclusi). È possibile accedere a servizi distribuiti in Sud America o Australia allo stesso modo è accedere alle aree ovest Europa e Nord.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecosistema partner connettività RTF

ExpressRoute dispone di un insieme di provider di integrazione applicativa e partner SI offre un costantemente crescente. È possibile fare riferimento all'articolo [posizioni e dei gestori di ExpressRoute](expressroute-locations.md) per informazioni più recenti.

### <a name="connectivity-to-national-clouds"></a>Connettività a nuvole nazionali

Microsoft opera ambienti cloud isolato per aree geopolitiche speciali e segmenti di clienti. Fare riferimento alla pagina [percorsi e dei gestori di ExpressRoute](expressroute-locations.md) per un elenco delle aree nazionali e dei gestori.

### <a name="supported-bandwidth-options"></a>Opzioni della larghezza di banda supportati

È possibile acquistare circuiti ExpressRoute per un'ampia gamma di larghezze di banda. L'elenco di larghezze di banda supportati è indicata di seguito. Assicurarsi di contattare il provider di integrazione applicativa per determinare l'elenco di larghezze di banda supportati che forniscono.

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 GB/s
- 2 GB/s
- 5 GB/s
- 10 GB/s

### <a name="dynamic-scaling-of-bandwidth"></a>Adattamento dinamico della larghezza di banda

Avere la possibilità di aumentare la larghezza di banda circuito ExpressRoute (in base basata migliore) senza dover chiudere le connessioni. 

### <a name="flexible-billing-models"></a>Modelli di fatturazione flessibili

È possibile selezionare un modello di fatturazione più adatto alle proprie necessità. Scegliere tra i modelli di fatturazione elencati di seguito. Fare riferimento alla pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md) per altri dettagli. 

- **Dati illimitato**. Circuito ExpressRoute viene addebitato in base alla tariffa mensile e tutti i trasferimenti di dati in ingresso e in uscita viene aggiunto a titolo gratuito. 
- **Dati a consumo**. Circuito ExpressRoute viene addebitato in base alla tariffa mensile. Tutti i trasferimenti di dati in ingresso sono gratuito. Trasferimento di dati in uscita verrà addebitato per GB trasferimento dei dati. Trasferimento di dati variano in base l'area geografica.
- **Componente aggiuntivo premium ExpressRoute**. Premio ExpressRoute è un componente aggiuntivo su circuito ExpressRoute. Il componente aggiuntivo premium ExpressRoute fornisce le funzionalità seguenti: 
    - Limiti di maggiore la distribuzione di Azure pubbliche e Azure privato peering da 4.000 indirizza 10.000 cicli di.
    - Connettività globale per i servizi. Un circuito ExpressRoute creato in un'area (esclusi nuvole nazionali) avrà accesso alle risorse in un'altra regione nel mondo. Ad esempio, una rete virtuale creata in Europa occidentale accessibili tramite un circuito ExpressRoute effettuato il provisioning in silicio picchi.
    - Numero maggiore di collegamenti VNet per circuito ExpressRoute da 10 a un limite superiore, a seconda della larghezza di banda del circuito.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle connessioni ExpressRoute e domini di routing. Vedere [ExpressRoute circuiti e domini di routing](expressroute-circuit-peerings.md).
- Trovare un provider di servizi. Vedere [ExpressRoute partner e posizioni peering](expressroute-locations.md).
- Verificare che siano soddisfatti tutti i prerequisiti. Vedere la sezione [Prerequisiti ExpressRoute](expressroute-prerequisites.md).
- Fare riferimento ai requisiti per il [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configurare la connessione ExpressRoute.
    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurare la distribuzione](expressroute-howto-routing-classic.md)
    - [Creare un collegamento a un circuito ExpressRoute un VNet](expressroute-howto-linkvnet-classic.md)
