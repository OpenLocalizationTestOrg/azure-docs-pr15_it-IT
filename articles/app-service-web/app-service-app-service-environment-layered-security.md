<properties 
    pageTitle="Architettura di sicurezza a più livelli con ambienti servizio App" 
    description="Implementare un'architettura di sicurezza a più livelli con App servizio ambienti." 
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
    ms.date="08/30/2016" 
    ms.author="stefsch"/>   

# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>Implementare un'architettura di sicurezza a più livelli con ambienti servizio App

## <a name="overview"></a>Panoramica ##
 
Poiché App servizio ambienti fornire un ambiente di runtime isolato distribuito in una rete virtuale, gli sviluppatori di creare un'architettura di sicurezza a più livelli che fornisce diversi livelli di accesso alla rete per ogni livello di applicazione fisica.

Un desiderata comune consiste nel nascondere API back-end da generale accesso a Internet e consentire solo API da chiamare dalle App web padre.  [Gruppi di sicurezza (NSGs) di rete] [ NetworkSecurityGroups] può essere usata su subnet contenente App servizio ambienti per limitare l'accesso pubblico per applicazioni API.

La figura seguente mostra un'architettura di esempio con un'app WebAPI in base a distribuito in un ambiente App.  Tre istanze di app web separato, distribuite in tre App servizio ambienti separati, effettuare chiamate back-end della stessa applicazione WebAPI.

![Architettura concettuale][ConceptualArchitecture] 

Sul segno più verde indica che il gruppo di sicurezza di rete nella subnet contenente "apiase" consente le chiamate in ingresso dalle App web padre, come le chiamate e dallo stesso.  Tuttavia stesso gruppo di sicurezza di rete nega esplicitamente l'accesso per il traffico in ingresso generale da Internet. 

Il resto di questo argomento illustra i passaggi necessari per configurare il gruppo di sicurezza di rete nella subnet contenente "apiase".

## <a name="determining-the-network-behavior"></a>Determinare il comportamento di rete ##
Per sapere quali regole di sicurezza della rete sono necessarie, è necessario determinare quali client di rete saranno autorizzati a raggiungere l'ambiente di servizio App contenente l'app API e quali client verranno bloccati.

Poiché [i gruppi di sicurezza rete (NSGs)] [ NetworkSecurityGroups] applicati alla subnet e App servizio ambienti distribuiti in subnet, le regole contenute in un NSG applicano a **tutte** le App in esecuzione in un ambiente App.  Utilizzare l'architettura di esempio per questo articolo dopo un gruppo di sicurezza di rete viene applicato alla subnet contenente "apiase", tutte le App in esecuzione in ambiente servizio App "apiase" saranno protetti dallo stesso set di regole di protezione. 

- **Determinare l'indirizzo IP in uscita dei chiamanti padre:**  Che cos'è l'indirizzo IP o gli indirizzi dei chiamanti padre?  Questi indirizzi dovranno essere esplicitamente consentito l'accesso di NSG.  Poiché le chiamate tra App servizio ambienti vengono considerate chiamate "Internet", significa che l'indirizzo IP in uscita assegnate a ciascuna delle tre ambienti di servizio App padre esigenze aziendali per consentire l'accesso in NSG per subnet "apiase".   Per ulteriori informazioni su come determinare l'indirizzo IP in uscita per le applicazioni in esecuzione in un ambiente App, vedere l' [Architettura di rete] [ NetworkArchitecture] articolo introduttivo.
- **L'app API back-end necessario chiamare se stesso?**  Un punto sofisticato e trascurato che si trova in alcuni casi è lo scenario in cui l'applicazione di back-end deve chiamare se stesso.  Se è necessario chiamare se stesso un'applicazione di API back-end in un ambiente App, questo viene considerato come una chiamata "Internet".  Nell'architettura di esempio è necessario consentire l'accesso dall'indirizzo IP in uscita della "apiase" ambiente servizio App anche.

## <a name="setting-up-the-network-security-group"></a>Configurare il gruppo di sicurezza di rete ##
Dopo aver creato il set di indirizzi IP in uscita sono note, il passaggio successivo consiste per creare un gruppo di sicurezza di rete.  Gruppi di sicurezza di rete possono essere creati per sia Manager delle risorse in base a reti virtuali, come le reti virtuali classiche.  Gli esempi riportati di seguito mostrano creazione e configurazione di un NSG in una rete virtuale classica tramite Powershell.

Per l'architettura di esempio, gli ambienti si trovano in Sud Central US, in modo che in quell'area viene creato un NSG vuoto:

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

Prima di tutto esplicita consentire regola viene aggiunto per l'infrastruttura di gestione Azure come indicato nell'articolo per [il traffico in ingresso] [ InboundTraffic] per gli ambienti servizio App.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
Successivamente, vengono aggiunti due regole per consentire chiamate HTTP e HTTPS dal primo padre App servizio ambiente ("fe1ase").

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Sciacquare e ripetere l'operazione per il secondo e terzo padre App servizio ambienti ("fe2ase" e "fe3ase").

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Infine, concedere l'accesso all'indirizzo IP in uscita dell'ambiente di servizio App dell'API back-end in modo che è possibile richiamare in se stesso.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

Senza altre regole di protezione di rete è necessario configurare in quanto ogni NSG dispone di un set di regole predefinite che bloccano l'accesso in ingresso da Internet per impostazione predefinita.

L'elenco completo delle regole nel gruppo di sicurezza di rete è illustrato di seguito.  Si noti come quest'ultima regola, è evidenziata, blocca l'accesso in ingresso da tutti i chiamanti diversi da quelli che hanno ottenuto in modo esplicito l'accesso.

![Configurazione di NSG][NSGConfiguration] 

Il passaggio finale è applicare il NSG alla subnet contenente ambiente servizio App "apiase".  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

Con NSG applicato alla subnet, solo tre padre App servizio ambienti e l'ambiente di servizio App contenente API back-end, possono chiamare nell'ambiente di "apiase".


## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi ##
Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Informazioni sui [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md). 

Informazioni sulle [indirizzi IP in uscita] [ NetworkArchitecture] e App servizio ambienti.

[Porte di rete] [ InboundTraffic] utilizzato da App servizio ambienti.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
