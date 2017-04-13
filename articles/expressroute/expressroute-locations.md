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

| **Provider di servizi**  |**Microsoft Azure** | **Office 365 e CRM Online** | **Percorsi** |
|-----------------------|--------------------|----------------|---------------|
| **AARNet** | Supportati | Supportati | Melbourne, Sydney |
| **[Reti Aryaka]( http://www.aryaka.com/)** | Supportati | Supportati | Amsterdam, silicio picchi, Singapore, Tokyo, Washington controller di dominio |
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Supportati | Supportati | Amsterdam, Chicago, Roma, Londra, silicio picchi, Singapore, Sydney, Washington controller di dominio |
| **[British Telecom]( http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Supportati | Supportati | Amsterdam, Hong Kong, Londra, silicio picchi, Singapore, Sydney, Tokyo, Washington controller di dominio |
|**CenturyLink** | Prossimamente | Prossimamente| Silicio picchi |
|**Cina Telecom globale** | Supportati | Non è supportata | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Supportati | Prossimamente | Dallas, Montreal + Roma |
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)**  |  Supportati | Supportati | Amsterdam, Dublin, Londra, Tokyo |
| **Comcast** | Supportati | Supportati | Chicago, silicio picchi, Washington controller di dominio |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Supportati | Supportati | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportati | Supportati | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londra, Los Angeles, Melbourne, New York, Osaka, Parigi +, SAN Paulo, Seattle, silicio picchi, Singapore, Sydney, Tokyo, Roma, Washington controller di dominio |
| **euNetworks** |  Supportati | Supportati | Amsterdam |
| **GÉANT** | Supportati | Supportati | Amsterdam |
| **[Inc. Giappone iniziativa Internet - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |  Supportati | Supportati | Osaka, Tokyo |
| **[InterCloud]( https://www.intercloud.com/)** | Supportati | Supportati | Data Center di Washington Amsterdam, Londra, Singapore |
| **La connessione Internet soluzioni - Cloud** | Supportati | Supportati | Amsterdam, Londra |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)**  | Supportati | Supportati | Amsterdam, Londra, Parigi |
| **Jisc** | Supportati | Supportati | Londra | 
| **[Comunicazioni di livello 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportati | Supportati | Amsterdam, Chicago, Dallas, Las Vegas +, Londra, Milano, silicio picchi, Washington controller di dominio |
| **Megaport** | Supportati | Supportati | Dallas, Hong Kong Las Vegas, Los Angeles Melbourne, New York Seattle, Singapore Sydney, Washington controller di dominio |
| **MTB** | Supportati | Supportati | Londra |
| **Dati di generazione successivi** | Prossimamente | Prossimamente | Newport(Wales) + |
| **NEXTDC** | Supportati | Supportati | Melbourne, Sydney |
| **Comunicazioni NTT** | Supportati | Supportati | Osaka Londra, Los Angeles, Tokyo |
| **[Arancione]( http://www.orange-business.com/en/products/business-vpn-galerie)** | Supportati | Supportati | Amsterdam, Hong Kong Londra, silicio picchi, Singapore Sydney, Washington controller di dominio |
| **PCCW globale limitato** | Supportati | Supportati | Hong Kong |
| **[SingTel]( http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |  Supportati | Supportati | Singapore |
| **Softbank** | Supportati | Supportati | Osaka, Tokyo | 
| **[Comunicazioni tata](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Supportati | Supportati | Amsterdam, Chennai, Hong Kong, Londra, Mumbai, Data Center di Washington silicio picchi, Singapore |
| **[Gruppo TeleCity]( http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Supportati | Supportati | Amsterdam, Dublin, Londra |
| **Telefonica** | Supportati | Supportati | SAN Paulo |
| **Telenor** | Supportati | Supportati | Amsterdam, Londra |
| **[Telstra Corporation]( http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Supportati | Supportati | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Supportati | Supportati | Amsterdam, Hong Kong, Londra, silicio picchi, Singapore, Sydney, Tokyo, Washington controller di dominio |
| **Vodafone** | Supportati | Non è supportata | Londra | 
| **[Gruppo Zayo]( http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Supportati | Supportati | Chicago, Los Angeles, New York silicio picchi, Roma, Washington controller di dominio |

 **+**indica disponibile a breve

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

#### <a name="us-government-cloud"></a>Cloud governo degli Stati Uniti

| **Provider di servizi**  |**Microsoft Azure** | **Office 365** | **Percorsi** |
|-----------------------|--------------------|----------------|---------------|
| **[AT & T NetBond]( https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Supportati | Supportati | Chicago, Washington controller di dominio |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportati | Supportati | Chicago, Roma, Milano, Washington controller di dominio |
| **[Comunicazioni di livello 3]( http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportati | Supportati | Chicago, New York +, Washington controller di dominio |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Supportati | Supportati | Chicago, Dallas + New York, Washington controller di dominio |

#### <a name="china"></a>Cina

| **Provider di servizi**  |**Microsoft Azure** | **Office 365** | **Percorsi** |
|-----------------------|--------------------|----------------|---------------|
| **Cina telecomunicazioni** | Supportati | Non è supportata | Pechino, Shanghai|
Per ulteriori informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/).

#### <a name="germany"></a>Germania

| **Provider di servizi**  |**Microsoft Azure** | **Office 365** | **Percorsi** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt]( http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Supportati | Non è supportata | Berlino +, Francoforte|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportati | Non è supportata | Francoforte|
| **e protette** | Supportati | Non è supportata | Berlino|
| **Interxion** | Supportati | Non è supportata | Francoforte|

## <a name="nonpartners"></a>Connettività mediante il provider di servizi non presente nell'elenco

Se il proprio provider di integrazione applicativa non è elencato nelle sezioni precedenti, è comunque possibile creare una connessione.

- Contattare il provider di integrazione applicativa per verificare se sono connessi a uno degli scambi nella tabella precedente. È possibile controllare i collegamenti seguenti per raccogliere ulteriori informazioni sui servizi offerti dai provider di exchange. Diversi provider di integrazione applicativa è già connessi a scambi Ethernet.

    - [Exchange Equinix Cloud](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- Disporre il proprio provider di connettività per estendere la rete peering percorso scelto.
    - Assicurarsi che il proprio provider di integrazione applicativa si estende la connettività in modo disponibilità, in modo che non esistono senza singoli punti di errore.
- Ordinare un circuito ExpressRoute con exchange come provider di integrazione applicativa per connettersi a Microsoft.
    - Seguire i passaggi descritti in [creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) per configurare la connettività.

|**Provider di integrazione applicativa**|**Exchange**|**Percorsi**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapore|
|**Comunicazioni Alaska**|Equinix|Milano|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure )**|Equinix|New York, Washington controller di dominio|
|**[Comunicazioni XO](http://www.xo.com/)**|Equinix|Silicio picchi|


## <a name="expressroute-system-integrators"></a>ExpressRoute integratori

Abilitazione della connettività privata in base alle esigenze può essere difficile, in base alle dimensioni di rete. È possibile lavorare con uno dei integratori di elencate nella tabella seguente per onboarding a ExpressRoute.

|**La integrazione dei sistemi**|**Continente**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Area Asia, Europa, US |
|**[Soluzioni dotnet](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Servizi professionali Equinix](http://www.equinix.com/services/consulting/)**|CONTATTACI|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Area Asia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | CONTATTACI |
|**[Leadership di progetto](http://www.projectleadership.net/azure)** | CONTATTACI |

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni su ExpressRoute, vedere [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Verificare che siano soddisfatti tutti i prerequisiti. Vedere la sezione [Prerequisiti ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa di posizione"
