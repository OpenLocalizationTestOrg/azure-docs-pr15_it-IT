<properties
   pageTitle="Percorsi ExpressRoute | Microsoft Azure"
   description="In questo articolo fornisce una panoramica dettagliata delle posizioni in cui vengono forniti i servizi e su come connettere alle aree Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2016"
   ms.author="cherylmc" />

# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute partner e posizioni peering

Le tabelle in questo articolo forniscono informazioni sugli ExpressRoute connettività provider geografico ExpressRoute servizi cloud Microsoft supportati per ExpressRoute ed ExpressRoute integratori (SIs).

## <a name="partners"></a>Provider di integrazione applicativa ExpressRoute

ExpressRoute è supportato in tutti Azure aree geografiche e posizioni. La seguente mappa comprende un elenco delle aree geografiche Azure ed ExpressRoute percorsi. ExpressRoute percorsi fanno riferimento a quelle in cui Microsoft peer con diversi provider di servizi.

![Mappa di posizione][0]

Si avrà accesso ai servizi Azure attraverso tutte le aree in un'area geopolitiche se si è connessi a almeno un percorso di ExpressRoute all'interno dell'area geopolitiche. Nella tabella seguente fornisce una mappa delle aree Azure in posizioni ExpressRoute all'interno di un'area geopolitiche.

|**Area geopolitiche**|**Aree Azure**|**ExpressRoute percorsi**|
|---|---|---|
|**Nord America**|Stati Uniti orientali, ovest degli Stati Uniti, Stati Uniti orientali 2, centrale USA, Sud centrale USA, Nord centrale USA, Canada centrale, Canada orientale|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, silicio picchi, Washington controller di dominio, Montreal +, Quebec città +, Roma|
|**Sud America**|Brasile sud|SAN Paulo|
|**Europa**|Europa Nord America, Europa occidentale, Regno Unito ovest, Regno Unito sud|Amsterdam, Dublin, Londra, Newport(Wales) +, Parigi|
|**Area Asia**|Asia orientale, Asia sudorientale|Hong Kong, Singapore|
|**Giappone**|Giappone-ovest, Giappone est|Osaka, Tokyo|
|**Australia**|Australia Sudest, Australia orientale|Melbourne, Sydney|
|**India**|India ovest, India centrale, India sud|Chennai, Mumbai|



Nella tabella seguente vengono fornite informazioni su aree geografiche e confini geopolitici per nuvole nazionali.

|**Area geopolitiche**|**Aree Azure**|**ExpressRoute percorsi**|
|---|---|---|---|
|**Cloud governo degli Stati Uniti**|Stati Uniti Gov Iowa, Usa Gov Virginia|Chicago, Roma, Milano, Washington controller di dominio|
|**Cina**|Cina Nord, est Cina|Pechino, Shanghai|
|**Germania**|Germania centrale, Germania est|Berlino, Francoforte|


La connettività tra le regioni geopolitiche non è supportata nello SKU ExpressRoute standard. È necessario attivare il componente aggiuntivo premium ExpressRoute supportare connettività globale. Connettività a ambienti cloud nazionali non è supportata. È possibile lavorare con il proprio provider di connessione in caso di necessità.


## <a name="connectivity-provider-locations"></a>Posizioni di provider di integrazione applicativa

> [AZURE.SELECTOR]
[Percorsi dal Provider](expressroute-locations.md#connectivity-provider-locations)
[provider in base alla posizione](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>Azure di produzione
| **Posizione**  | **Provider di servizi** |
|---------------|-----------------------|
| **Amsterdam** | Reti Aryaka, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, soluzioni Internet - connettersi Cloud, Interxion, Verizon comunicazioni, arancione, Tata comunicazioni, TeleCity gruppo Telenor, livello 3 |
| **Atlanta** | Equinix |
| **Chennai** | Comunicazioni tata |
| **Chicago** | AT & T NetBond, Comcast, Equinix, livello 3 comunicazioni Zayo gruppo |
| **Dallas** | AT & T NetBond, Cologix, Equinix, livello 3 comunicazioni, Megaport |
| **Progetto Dublin** | Gruppo Colt, Telecity |
| **Hong Kong** | British Telecom, in Cina Telecom globale, Equinix, Megaport, arancione, PCCW globale limitato, comunicazioni Tata, Verizon |
| **Londra** | AT & T NetBond, British Telecom, Colt, Equinix, InterCloud, soluzioni Internet - connettersi Cloud, Interxion, Jisc, livello 3 comunicazioni, MTB, NTT comunicazioni, arancione, Tata comunicazioni, Telecity gruppo Telenor, Verizon, Vodafone |
| **Bologna** | Livello 3 comunicazioni +, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo gruppo |
| **Melbourne** | AARNet, Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** | Equinix, Megaport, Zayo gruppo |
| **Newport(Wales) +** | Successivo generazione dati + MAIUSC + |
| **Montreal** | Cologix + |
| **Mumbai** | Comunicazioni tata |
| **Osaka** | Equinix Internet iniziativa Giappone s.p.a. - IIJ, NTT comunicazioni, Softbank |
| **Milano** | Interxion, Equinix + |
| **SAN Paulo** | Equinix, Telefonica |
| **Milano** | Megaport Equinix, comunicazioni di livello 3, |
| **Silicio picchi** | Reti Aryaka, AT & T NetBond, British Telecom, CenturyLink +, Comcast, Equinix, livello 3, arancione, Tata comunicazioni, Verizon, Zayo gruppo comunicazioni |
| **Singapore** | Reti Aryaka, AT & T NetBond, British Telecom, Equinix, InterCloud, Megaport, arancione, SingTel, le comunicazioni Tata, Verizon |
| **Sydney** | AARNet, AT & T NetBond, British Telecom, Equinix, Megaport, NEXTDC, arancione, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka reti British Telecom, Colt, Equinix, Internet iniziativa Giappone s.p.a. - IIJ, NTT comunicazioni, Softbank, Verizon |
| **Roma** | Cologix, Equinix, Zayo gruppo |
| **Data Center di Washington** | Reti Aryaka, AT & T NetBond, British Telecom, Comcast, Equinix, InterCloud, livello 3, Megaport, arancione, Tata comunicazioni, Verizon, Zayo gruppo comunicazioni |

 **+**indica disponibile a breve

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

#### <a name="us-government-cloud"></a>Cloud governo degli Stati Uniti

| **Posizione**  |**Provider di servizi** |
|---------------|--------------------|
| **Chicago** | AT & T NetBond, Equinix, livello 3 comunicazioni, Verizon |
| **Dallas** |  Equinix, Verizon + |
| **New York** | Equinix, livello 3 comunicazioni +, Verizon |
| **Data Center di Washington** | AT & T NetBond, Equinix, livello 3 comunicazioni, Verizon |

#### <a name="china"></a>Cina

| **Posizione**  | **Provider di servizi** |
|---------------|-----------------------|
| **Pechino** | Cina telecomunicazioni |
| **Shanghai** |  Cina telecomunicazioni |
Per ulteriori informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>Germania

| **Posizione**  | **Provider di servizi** |
|---------------|-----------------------|
| **Berlino** | Colt + e protette |
| **Francoforte** | Colt, Equinix, Interxion |

## <a name="nonpartners"></a>Connettività mediante il provider di servizi non presente nell'elenco

Se il proprio provider di integrazione applicativa non è elencato nelle sezioni precedenti, è comunque possibile creare una connessione.

- Contattare il provider di integrazione applicativa per verificare se sono connessi a uno degli scambi nella tabella precedente. È possibile controllare i collegamenti seguenti per raccogliere ulteriori informazioni sui servizi offerti dai provider di exchange. Diversi provider di integrazione applicativa è già connessi a scambi Ethernet.

    - [Exchange Equinix Cloud](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Disporre il proprio provider di connettività per estendere la rete peering percorso scelto.
    - Assicurarsi che il proprio provider di integrazione applicativa si estende la connettività in modo disponibilità, in modo che non esistono senza singoli punti di errore.
- Ordinare un circuito ExpressRoute con exchange come provider di integrazione applicativa per connettersi a Microsoft.
    - Seguire i passaggi descritti in [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) per configurare la connettività.

|**Posizione**|**Exchange**|**Provider di integrazione applicativa**|
|-------------|------------|-------------------------|
| **New York** | Equinix | Lightower |
| **Milano** | Equinix | Comunicazioni Alaska |
| **Silicio picchi** | Equinix | Comunicazioni XO |
| **Singapore** | Equinix | 1CLOUDSTAR |
| **Data Center di Washington** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute integratori

Abilitazione della connettività privata in base alle esigenze può essere difficile, in base alle dimensioni di rete. È possibile lavorare con uno dei integratori di elencate nella tabella seguente per onboarding a ExpressRoute.

|**Continente**|**Integratori**|
|-------------|---------------------|
| **Area Asia** | Avanade Inc OneAs1a|
| **Europa** | Avanade Inc Dotnet soluzioni|
| **CONTATTACI** | Avanade Inc, servizi professionali Equinix, Perficient, Leadership di progetto|

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Verificare che siano soddisfatti tutti i prerequisiti. Vedere la sezione [Prerequisiti ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa di posizione"
