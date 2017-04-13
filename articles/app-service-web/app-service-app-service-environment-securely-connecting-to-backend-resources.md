<properties 
    pageTitle="In modo sicuro la connessione alle risorse back-end da un ambiente servizio App" 
    description="Informazioni su come connettersi in modo sicuro a risorse di back-end da un ambiente di servizio App." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/04/2016" 
    ms.author="stefsch"/>   

# <a name="securely-connecting-to-backend-resources-from-an-app-service-environment"></a>In modo sicuro la connessione alle risorse back-end da un ambiente servizio App #

## <a name="overview"></a>Panoramica ##
Poiché un ambiente App viene sempre creato in **dei due** virtuali un Manager delle risorse di Azure, **o** una distribuzione classica modello [virtuali][virtualnetwork], le connessioni in uscita da un ambiente di servizio App ad altre risorse di back-end possono scorrere esclusivamente la rete virtuale.  Con una modifica apportata nel giugno 2016, ASEs possono anche essere distribuiti in reti virtuali che usano gli intervalli di indirizzi pubblico o gli spazi indirizzo RFC1918 (ad esempio indirizzi privati).  

Ad esempio, potrebbe essere SQL Server in esecuzione in un cluster di macchine virtuali con la porta 1433 bloccato.  L'endpoint potrebbe essere ACLd per consentire solo l'accesso da altre risorse sulla stessa rete virtuale.  

Se ad esempio endpoint sensibili possono essere eseguite in locale ed essere connesso a Azure tramite dei [Siti a] [ SiteToSite] o [Azure ExpressRoute] [ ExpressRoute] connessioni.  Di conseguenza, solo le risorse in reti virtuali collegati al sito per sito o tunnel ExpressRoute saranno in grado di accedere endpoint locale.

Per tutti gli scenari, App in esecuzione in un ambiente App saranno in grado di connettersi in modo sicuro ai vari server e risorse.  Il traffico in uscita dall'App in esecuzione in un ambiente App per gli endpoint privati nella stessa rete virtuale (o connessi alla stessa rete virtuale) verrà flusso solo tramite la rete virtuale.  Il traffico in uscita per gli endpoint privati non scorrerà tramite Internet.

Una sola avvertenza riguarda il traffico in uscita da un ambiente di servizio App per gli endpoint all'interno di una rete virtuale.  Ambienti servizio App non è raggiungibile endpoint macchine virtuali che si trova nella **stessa** subnet l'ambiente di servizio App.  In genere non deve essere un problema, purché App servizio ambienti distribuiti in una subnet riservata esclusivo a solo l'ambiente del servizio di App.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="outbound-connectivity-and-dns-requirements"></a>Requisiti di DNS e connettività in uscita ##
Per un ambiente App per il corretto funzionamento, è necessario l'accesso a vari endpoint in uscita. L'elenco completo degli endpoint esterni usati da un ASE è indicata nella sezione "Necessaria la connettività di rete" dell'articolo [Configurazione della rete per ExpressRoute](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) .

Servizio di App ambienti richiedono un'infrastruttura DNS valida configurata per la rete virtuale.  Se per qualsiasi motivo la configurazione del DNS viene modificato dopo la creazione di un ambiente App, gli sviluppatori possono forzare un ambiente App per selezionare la configurazione del nuovo DNS.  Attivazione in sequenza ambiente riavviare il computer tramite l'icona "Riavvia" nella parte superiore della stessa e di gestione App servizio ambiente nel portale impedirà l'ambiente selezionare la configurazione del nuovo DNS.

È anche consigliabile che i server DNS personalizzati nel vnet essere configurazione anticipo prima di creare un ambiente App.  Se la configurazione della rete virtuale DNS viene modificata durante la creazione di un ambiente App, che verrà generato il problema di processo di creazione di ambiente servizio App.  Infine proposta, se è presente un server DNS personalizzato a altra estremità di un gateway VPN e il server DNS non è raggiungibile o non è disponibile, il processo di creazione di ambiente servizio App anche non riuscirà.

## <a name="connecting-to-a-sql-server"></a>Connessione a un Server SQL
Una configurazione comune di SQL Server è un endpoint in ascolto sulla porta 1433:

![Endpoint SQL Server][SqlServerEndpoint]

Esistono due metodi per limitare il traffico a questo punto finale:


- [Elenchi di controllo di accesso di rete] [ NetworkAccessControlLists] (rete ACL)

- [Gruppi di sicurezza di rete][NetworkSecurityGroups]


## <a name="restricting-access-with-a-network-acl"></a>Limitazione dell'accesso a una rete ACL

È possibile proteggere la porta 1433 usando un elenco di controllo di accesso di rete.  Nell'esempio seguente whitelists client indirizzi provenienti da all'interno di una rete virtuale e blocca l'accesso a tutti gli altri client.

![Esempio di elenco di controllo accesso rete][NetworkAccessControlListExample]

Tutte le applicazioni in esecuzione in ambiente App nella stessa rete virtuale di SQL Server saranno in grado di connettersi all'istanza di SQL Server tramite l'indirizzo IP **VNet interno** per il computer virtuale di SQL Server.  

La stringa di connessione di esempio seguente fa riferimento a SQL Server utilizzando l'indirizzo IP privato.

    Server=tcp:10.0.1.6;Database=MyDatabase;User ID=MyUser;Password=PasswordHere;provider=System.Data.SqlClient

Anche se nel computer virtuale è anche un endpoint pubblico, a causa di rete ACL verranno rifiutati tentativi di connessione utilizzando l'indirizzo IP pubblico. 

## <a name="restricting-access-with-a-network-security-group"></a>Limitazione dell'accesso con un gruppo di sicurezza di rete
Un'alternativa per la protezione dell'accesso è con un gruppo di sicurezza di rete.  Gruppi di sicurezza di rete possono essere applicati a singole macchine virtuali o a una subnet contenente macchine virtuali.

Prima di tutto un gruppo di sicurezza di rete potrebbe essere necessario creare:

    New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"

Limitazione dell'accesso a solo il traffico interno VNet è molto semplice con un gruppo di sicurezza di rete.  Le regole predefinite in un gruppo di sicurezza di rete consentono l'accesso solo da altri client di rete nella stessa rete virtuale.

Di conseguenza il blocco di accesso a SQL Server è semplice come l'applicazione di un gruppo di sicurezza di rete con le regole predefinite per le macchine virtuali che esegue SQL Server o subnet contenente le macchine virtuali.

Nell'esempio seguente si applica un gruppo di sicurezza di rete alla subnet contengono:

    Get-AzureNetworkSecurityGroup -Name "testNSGExample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-1'
    
Il risultato finale è un set di regole di sicurezza che bloccano l'accesso esterno, consentendo accesso interno VNet:

![Regole di protezione di rete predefinite][DefaultNetworkSecurityRules]


## <a name="getting-started"></a>Guida introduttiva
Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per iniziare a utilizzare App servizio ambienti, vedere [Introduzione all'ambiente di servizio App][IntroToAppServiceEnvironment]

Per informazioni dettagliate intorno a controllare il traffico in ingresso per l'ambiente App, vedere [controllare il traffico in ingresso per un ambiente App][ControlInboundASE]

Per ulteriori informazioni sulla piattaforma Azure App servizio, vedere [Servizio App Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!-- LINKS -->
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[ControlInboundTraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[SiteToSite]: https://azure.microsoft.com/documentation/articles/vpn-gateway-site-to-site-create/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[NetworkAccessControlLists]: https://azure.microsoft.com/documentation/articles/virtual-networks-acl/
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/ 
[ControlInboundASE]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/ 

<!-- IMAGES -->
[SqlServerEndpoint]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/SqlServerEndpoint01.png
[NetworkAccessControlListExample]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/NetworkAcl01.png
[DefaultNetworkSecurityRules]: ./media/app-service-app-service-environment-securely-connecting-to-backend-resources/DefaultNetworkSecurityRules01.png 
