<properties
   pageTitle="Requisiti di routing per ExpressRoute | Microsoft Azure"
   description="Questa pagina fornisce informazioni dettagliate sui requisiti per la configurazione e gestione del routing per circuiti ExpressRoute."
   documentationCenter="na"
   services="expressroute"
   authors="osamazia"
   manager="ganesr"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="osamazia"/>


# <a name="expressroute-routing-requirements"></a>Requisiti di routing ExpressRoute  

Per connettersi ai servizi cloud Microsoft tramite ExpressRoute, è necessario configurare e gestire il routing. Alcuni provider di integrazione applicativa offrono impostazione e gestione del routing come servizio gestito. Contattare il provider di integrazione applicativa per vedere se offrono questo servizio. In caso contrario, è necessario rispettare i requisiti seguenti. 

Fare riferimento all'articolo [circuiti e domini di routing](expressroute-circuit-peerings.md) per una descrizione delle sessioni di routing che devono essere configurato per facilitare la connettività.

>[AZURE.NOTE] Microsoft non supporta i protocolli di ridondanza router (ad esempio, HSRP, VRRP) per le configurazioni disponibilità. Abbiamo si basano su una coppia di ridondanti di sessioni BGP per peering disponibilità elevata.

## <a name="ip-addresses-used-for-peerings"></a>Indirizzi IP utilizzati per peerings

È necessario prenotare alcuni blocchi di indirizzi IP per configurare il routing tra la rete e router bordo (MSEEs) di Microsoft Enterprise. In questa sezione fornisce un elenco di requisiti e vengono descritte le regole relative alle come questi indirizzi IP devono essere acquisiti e utilizzati.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Indirizzi IP utilizzati per peering privato Azure

È possibile utilizzare gli indirizzi IP privati o indirizzi IP per configurare il peerings. L'intervallo di indirizzi usato per la configurazione indirizza deve non si sovrappongono con intervalli di indirizzi usati per creare le reti virtuali in Azure. 

 - È necessario prenotare una /29 subnet o due /30 subnet per le interfacce di routing.
 - Subnet utilizzate per il routing può essere indirizzi IP privati o indirizzi IP.
 - Le subnet non devono essere in conflitto con intervallo riservato dal cliente per l'utilizzo in Microsoft cloud.
 - Se un /29 subnet viene utilizzato, verranno suddivisi in due /30 subnet. 
     - Prima /30 subnet verrà utilizzato per il collegamento principale e il secondo/subnet 30 verranno usate per il collegamento secondario.
     - Per ogni il /30 subnet, è necessario utilizzare il primo indirizzo IP del /30 subnet sul router. Microsoft utilizzerà il secondo indirizzo IP del /30 subnet per impostare una sessione BGP.
     - È necessario impostare entrambe le sessioni BGP per la [disponibilità contratto di servizio](https://azure.microsoft.com/support/legal/sla/) valido.  

#### <a name="example-for-private-peering"></a>Esempio di peering privato

Se si sceglie di utilizzare a.b.c.d/29 per impostare il peering, verranno suddivisi in due /30 subnet. Nell'esempio seguente, esamineremo utilizzo subnet a.b.c.d/29. 

a.b.c.d/29 verranno dividere a.b.c.d/30 e a.b.c.d+4/30 e trasmessi a Microsoft tramite l'API di provisioning. Si userà a.b.c.d+1 IP VRF per PE principale e Microsoft utilizzerà a.b.c.d+2 come IP VRF per MSEE principale. Si userà a.b.c.d+5 IP VRF per PE secondario e Microsoft utilizzerà a.b.c.d+6 come IP VRF MSEE secondario.

Valutare la possibilità di un caso in cui si seleziona 192.168.100.128/29 configurare peering privato. 192.168.100.128/29 include gli indirizzi da 192.168.100.128 a 192.168.100.135, tra cui:

- 192.168.100.128/30 vengono assegnate al link1, con provider mediante 192.168.100.129 e Microsoft utilizzando 192.168.100.130.
- 192.168.100.132/30 vengono assegnate al link2, con provider mediante 192.168.100.133 e Microsoft utilizzando 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Indirizzi IP utilizzati per pubblico Azure e peering Microsoft

È necessario utilizzare indirizzi IP che si è proprietari per la configurazione sessioni BGP. Microsoft deve essere in grado di verificare la proprietà di indirizzi IP tramite Routing registri Internet e registri di Routing Internet. 

- È necessario utilizzare univoco/subnet 29 o subnet due /30 configurare BGP peering per ogni peering per ExpressRoute circuito (se si usano più monitor). 
- Se un /29 subnet viene utilizzato, verranno suddivisi in due /30 subnet. 
    - Prima /30 subnet verrà utilizzato per il collegamento principale e il secondo/subnet 30 verranno usate per il collegamento secondario.
    - Per ogni il /30 subnet, è necessario utilizzare il primo indirizzo IP del /30 subnet sul router. Microsoft utilizzerà il secondo indirizzo IP del /30 subnet per impostare una sessione BGP.
    - È necessario impostare entrambe le sessioni BGP per la [disponibilità contratto di servizio](https://azure.microsoft.com/support/legal/sla/) valido.

## <a name="public-ip-address-requirement"></a>Requisito di indirizzo IP pubblico 

### <a name="private-peering"></a>Peering privato 

È possibile utilizzare gli indirizzi IPv4 di pubblico o privati per peering privato. Sono disponibili isolamento-to-end del traffico in modo sovrapposti degli indirizzi con altri utenti non è possibili in caso di peering privato. Questi indirizzi non vengono inseriti a Internet. 

### <a name="public-peering"></a>Peering pubblico

Il percorso di peering pubblico Azure consente di connettersi a tutti i servizi ospitati in Azure su indirizzi IP pubblici. Ad esempio servizi elencati in [Domande frequenti su ExpessRoute](expressroute-faqs.md) e uno o più servizi ospitati da indipendenti in Microsoft Azure. Connettività a servizi di Microsoft Azure su peering pubblico sempre viene avviata dalla rete alla rete Microsoft. È necessario utilizzare indirizzi IP pubblico per il traffico di rete Microsoft.

### <a name="microsoft-peering"></a>Microsoft Peering

Il percorso di peering Microsoft consente di connettersi ai servizi cloud Microsoft che non sono supportati aiutano Azure pubblico peering. L'elenco dei servizi include servizi di Office 365, ad esempio Exchange Online, SharePoint Online, Skype for Business e CRM Online. Microsoft supporta la connettività bidirezionale in di Microsoft peering. Il traffico destinato ai servizi cloud Microsoft necessario utilizzare indirizzi IPv4 pubblici validi prima di accedere alla rete Microsoft.

Assicurarsi che l'indirizzo IP e come numero registrati per l'utente in uno dei registri elencati di seguito.

- [ARIN](https://www.arin.net/)
- [APNIC](https://www.apnic.net/)
- [AFRINIC](https://www.afrinic.net/)
- [LACNIC](http://www.lacnic.net/)
- [RIPENCC](https://www.ripe.net/)
- [RADB](http://www.radb.net/)
- [ALTDB](http://altdb.net/)

>[AZURE.IMPORTANT] Indirizzi IP pubblici annunciati a Microsoft tramite ExpressRoute non devono essere annunciati a Internet. Questo potrebbe causare il danneggiamento connettività ad altri servizi Microsoft. Tuttavia, possono essere oggetto di indirizzi IP pubblico utilizzati dal server nella rete di comunicare con i punti finali di Office 365 all'interno di Microsoft tramite ExpressRoute. 

## <a name="dynamic-route-exchange"></a>Exchange route dinamico

Exchange routing sarà il protocollo eBGP. Sessioni EBGP stabilite tra il MSEEs e il router. L'autenticazione delle sessioni BGP non è un requisito. Se necessario, è possibile configurare un hash MD5. Per informazioni sulla configurazione delle sessioni BGP, vedere il [Configura il routing](expressroute-howto-routing-classic.md) e [circuito il provisioning di flussi di lavoro e stati circuito](expressroute-workflows.md) .

## <a name="autonomous-system-numbers"></a>Numeri di sistema autonomi

Microsoft utilizzerà 12076 AS per Azure pubblico, Azure privato e Microsoft peering. È stato riservato ASN da 65515 a 65520 per uso interno. 16 e 32 bit come numeri sono supportati.

Non esistono requisiti intorno a dati trasferimento simmetria. I percorsi di inoltro e restituiti possono attraversano coppie di router diverso. Indirizza identiche deve annunciato da uno dei due lati tra più coppie di circuito appartenenti è. Metriche non è necessario essere identici.

## <a name="route-aggregation-and-prefix-limits"></a>Instradare limiti di aggregazione e prefisso

È supportato fino a 4000 prefissi annunciati a Microsoft tramite Azure peering privato. È possibile aumentare fino a 10.000 prefissi se il componente aggiuntivo premium ExpressRoute è abilitato. Abbiamo accetta massimo di 200 prefissi per sessione BGP per Azure pubblico e Microsoft peering. 

La sessione BGP verrà rimosse se il numero di prefissi supera il limite. Abbiamo Accetta route predefinite sui solo il collegamento di peering privato. Provider di filtrare route predefinita e gli indirizzi IP privati (RFC 1918) da Azure pubblico e percorsi peering Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Transito routing e opzioni internazionali tra routing

ExpressRoute non può essere configurato come router transito. È necessario si basano sul provider di integrazione applicativa per i servizi di routing transito.

## <a name="advertising-default-routes"></a>Route predefinite relative alla pubblicità

Route predefinite sono consentite solo per le sessioni di Azure peering private. In questo caso, si indirizza tutto il traffico dalle reti virtuale associate alla rete. Annuncio route predefinite in privato peering genereranno percorso internet da Azure bloccate. Non è necessario utilizzare gli aziendale per indirizzare il traffico da e a internet per servizi ospitati in Azure. 

 Per abilitare la connettività per altri servizi di infrastruttura e i servizi di Azure, è necessario apportare che uno degli elementi seguenti sia presente:

 - Azure peering pubblico è abilitato per instradare il traffico verso endpoint pubblico
 - Utilizzare il routing definite dall'utente per consentire la connessione a internet per ogni subnet che richiedono connettività Internet.
 
>[AZURE.NOTE] La pubblicità route predefinite verrà interrotte Windows e altri attivazione delle licenze macchine Virtuali. Seguire le istruzioni [di seguito](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) per risolvere il problema.

## <a name="support-for-bgp-communities-preview"></a>Supporto per le community BGP (Preview)


In questa sezione viene fornita una panoramica di come verranno utilizzate europee BGP con ExpressRoute. Microsoft segnala indirizza pubblico e percorsi peering Microsoft con route contrassegnate con i valori della community appropriato. Spiegazione per farlo e i dettagli sui valori di community sono descritti di seguito. Microsoft, tuttavia, non rispetta i valori della community tag indirizza annunciato a Microsoft.

Se ci si connette a Microsoft tramite ExpressRoute in una qualsiasi peering posizione all'interno di un'area geopolitiche, si avrà accesso a tutti i servizi cloud Microsoft attraverso tutte le aree all'interno del limite geopolitiche. 

Ad esempio, se si è connessi a Microsoft Amsterdam tramite ExpressRoute, si avrà accesso a tutti i servizi cloud Microsoft ospitato in Europa Nord America ed Europa occidentale. 

Fare riferimento alla pagina [percorsi peering e partner ExpressRoute](expressroute-locations.md) per un elenco dettagliato delle aree geografiche geopolitiche, alle aree di Azure associate ed ExpressRoute corrispondente peering posizioni.

È possibile acquistare più circuito ExpressRoute per geopolitici regione. Disporre di più connessioni offre vantaggi significativi sulla disponibilità a causa di ridondanza geografico. Nei casi in cui si dispone di più circuiti ExpressRoute, viene visualizzato lo stesso insieme di prefissi annunciato e su peering pubblico Microsoft peering percorsi. Di conseguenza, che sarà necessario più percorsi di rete in Microsoft. Questa operazione può causare decisioni di routing ottimale essere eseguite all'interno della rete. Di conseguenza, potrebbero verificarsi esperienze connettività ottimale per diversi servizi. 

Microsoft verrà tag prefissi annunciati tramite peering pubblico e Microsoft peering con i valori di community BGP appropriati che indica l'area geografica i prefissi sono ospitati. Non è possibile utilizzare i valori della community per prendere decisioni di routing appropriate per l'offerta [il routing ottimale per i clienti](expressroute-optimize-routing.md).

| **Area geopolitiche** | **Area di Microsoft Azure** | **Valore di community BGP** |
|---|---|---|
| **Nord America** |    |  |
|    | Stati Uniti orientali | 12076:51004 |
|    | Stati Uniti orientali 2 | 12076:51005 |
|    | Usa ovest | 12076:51006 |
|    | Usa ovest 2 | 12076:51026 |
|    | Stati Uniti centro ovest | 12076:51027 |
|    | America del Nord centrale USA | 12076:51007 |
|    | Sud centrale USA | 12076:51008 |
|    | Centrale USA | 12076:51009 |
|    | Canada centrale | 12076:51020 |
|    | Canada orientale | 12076:51021 |
| **Sud America** |  |  |
|    | Brasile sud | 12076:51014 |
| **Europa** |    |  |
|    | Europa Nord America | 12076:51003 |
|    | Europa occidentale | 12076:51002 |
| **Area Asia Pacifico** |    |   |
|    | Asia orientale | 12076:51010 |
|    | Area Asia sudorientale | 12076:51011 |
| **Giappone** |     |   |
|    | Giappone est | 12076:51012 |
|    | Giappone ovest | 12076:51013 |
| **Australia** |    |   | 
|    | Australia orientale | 12076:51015 |
|    | Australia sudorientale | 12076:51016 |
| **India** |    |   |
|    | Sud India | 12076:51019 |
|    | India ovest | 12076:51018 |
|    | India centrale | 12076:51017 |

Tutte le route annunciate da Microsoft verranno contrassegnate con il valore di community appropriato. 

>[AZURE.IMPORTANT] Prefissi globali verranno contrassegnati con un valore di community appropriato e verranno annunciati solo quando componente aggiuntivo premium ExpressRoute è attivato.


Oltre a quanto sopra menzionato, Microsoft verrà anche tag prefissi basati sul servizio a cui appartengono. Si applica solo per i Microsoft peering. Nella tabella seguente fornisce un mapping del servizio valore community BGP.

| **Servizio** | **Valore di community BGP** |
|---|---|
| **Exchange** | 12076:5010 |
| **SharePoint** | 12076:5020 |
| **Skype per le aziende** | 12076:5030 |
| **CRM Online** | 12076:5040 |
| **Altri servizi di Office 365** | 12076:5100 |

>[AZURE.NOTE] Microsoft non rispetta i valori di community BGP impostati nel indirizza annunciato a Microsoft.

## <a name="next-steps"></a>Passaggi successivi

- Configurare la connessione ExpressRoute.

    - [Creare un circuito ExpressRoute per il modello di distribuzione classica](expressroute-howto-circuit-classic.md) o [creare e modificare un circuito ExpressRoute Gestione risorse di Azure](expressroute-howto-circuit-arm.md)
    - [Configura il routing per il modello di distribuzione classica](expressroute-howto-routing-classic.md) o [Configura il routing per il modello di distribuzione di Manager delle risorse](expressroute-howto-routing-arm.md)
    - [Collegamento un VNet classico per un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md) o [VNet un Manager delle risorse per un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)


