<properties
   pageTitle="Prerequisiti per l'adozione di ExpressRoute | Microsoft Azure"
   description="Questa pagina fornisce un elenco dei requisiti che venga rispettata prima che è possibile ordinare un circuito Azure ExpressRoute."
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


# <a name="expressroute-prerequisites--checklist"></a>Elenco di controllo e i prerequisiti ExpressRoute  

Per connettersi ai servizi cloud Microsoft tramite ExpressRoute, è necessario verificare che siano soddisfatti i requisiti seguenti elencati nelle sezioni seguenti.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Account Azure

- Un account di Microsoft Azure valido e attivo. Ciò è necessario installare il circuito ExpressRoute. Circuiti ExpressRoute sono risorse all'interno di Azure abbonamenti. Una sottoscrizione Azure è un requisito anche se la connettività è limitata ai servizi cloud Microsoft non Azure, ad esempio servizi di Office 365 e CRM online.
- Un abbonamento attivo a Office 365 (se con servizi di Office 365). Vedere la sezione [requisiti specifici di Office 365](#office-365-specific-requirements) di questo articolo per ulteriori informazioni.

## <a name="connectivity-provider"></a>Provider di integrazione applicativa
- È possibile collaborare con un [partner di integrazione applicativa ExpressRoute](expressroute-locations.md#partners) per la connessione al cloud Microsoft. È possibile configurare una connessione tra la rete locale e Microsoft in [tre modi](expressroute-introduction.md#howtoconnect). 
- Se il provider non è un partner di integrazione applicativa ExpressRoute, è comunque possibile connettersi al cloud Microsoft tramite un [provider di exchange cloud](expressroute-locations.md#nonpartners).

## <a name="network-requirements"></a>Requisiti di rete
- **Connettività ridondante**: non è necessario ridondanza in connettività fisica tra il mittente e il proprio provider. Microsoft richiede ridondanti sessioni BGP da impostare tra router Microsoft e i router peering, anche quando si utilizza solo [una connessione fisica a un exchange cloud](expressroute-faqs.md#onep2plink). 
- **Routing**: a seconda di come si connette a Microsoft Cloud, si o dal provider è necessario configurare e gestire le sessioni BGP per [domini di routing](expressroute-circuit-peerings.md). Alcuni provider di connettività Ethernet o il provider di exchange cloud può offrire gestione BGP come un servizio a valore aggiunto.
- **NAT**: Microsoft accetta solo gli indirizzi IP pubblici tramite Microsoft peering. Se si utilizza indirizzi IP privati della rete locale, è o la necessità di provider di traduzione di indirizzi IP privati per l'indirizzo IP pubblico gli indirizzi [utilizzando NAT](expressroute-nat.md).
- **QoS**: Skype per Business ha diversi servizi (ad esempio la voce, video e testo) che richiedono distingue gestione QoS. Si e il proprio provider di seguire i [requisiti di qualità del](expressroute-qos.md).
- **Protezione di rete**: è necessario considerare [la protezione della rete](../best-practices-network-security.md) per la connessione al Cloud Microsoft tramite ExpressRoute.
 
## <a name="office-365"></a>Office 365

Se si prevede di attivare Office 365 in ExpressRoute, esaminare i documenti seguenti per altre informazioni sui requisiti di Office 365.


- [Panoramica delle ExpressRoute per Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routing con ExpressRoute per Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [Intervalli di indirizzi IP e URL di Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Pianificazione della rete e ottimizzazione delle prestazioni per Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Strumenti e opzioni di calcolo della larghezza di banda di rete](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Integrazione di Office 365 con ambienti locali](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## <a name="crm-online"></a>CRM Online 
Se si prevede di abilitare CRM Online su ExpressRoute, rivedere i documenti seguenti per ulteriori informazioni su CRM Online

- [CRM Online URL](https://support.microsoft.com/kb/2655102) e [intervalli di indirizzi IP](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Trovare un provider di integrazione applicativa ExpressRoute. Vedere [ExpressRoute partner e posizioni peering](expressroute-locations.md).
- Fare riferimento ai requisiti per il [Routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
- Configurare la connessione ExpressRoute.
    - [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
    - [Configurare la distribuzione](expressroute-howto-routing-classic.md)
    - [Creare un collegamento a un circuito ExpressRoute un VNet](expressroute-howto-linkvnet-classic.md)

