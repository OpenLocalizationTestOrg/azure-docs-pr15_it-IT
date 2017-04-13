<properties 
    pageTitle="Cenni preliminari sull'architettura di rete del servizio di App ambienti" 
    description="Panoramica dell'architettura della rete topologia ofApp ambienti dei servizi." 
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

# <a name="network-architecture-overview-of-app-service-environments"></a>Cenni preliminari sull'architettura di rete del servizio di App ambienti

## <a name="introduction"></a>Introduzione ##
App servizio ambienti vengono sempre creati in una subnet di una [rete virtuale] [ virtualnetwork] -App in esecuzione in un ambiente App possano comunicare con endpoint privato si trova all'interno della stessa topologia di rete virtuale.  Poiché i clienti possono bloccare parti dell'infrastruttura di rete virtuale, è importante conoscere i tipi di comunicazione di rete che si verificano con un ambiente App.

## <a name="general-network-flow"></a>Flusso di rete generale ##
 
Quando un ambiente di servizio App (base) utilizza un indirizzo IP virtuale pubblico (VIP) per le app, tutto il traffico arriva in tale VIP pubblico.  Sono inclusi il traffico HTTP e HTTPS per le applicazioni, oltre che il traffico per FTP, funzionalità di debug remoto e le operazioni di gestione Azure.  Per un elenco completo delle porte specifiche (obbligatori e facoltativi) sono disponibili in VIP pubblico vedere l'articolo su come [controllare il traffico in ingresso] [ controllinginboundtraffic] in un ambiente di servizio App. 

Gli ambienti servizio App supportano anche le App in esecuzione associati solo a un indirizzo interno virtuali, noto anche come indirizzo ILB (bilanciamento del carico interno).  In un ILB abilitato il traffico ASE, HTTP e HTTPS per App, nonché il debug delle chiamate remote, arrivano nell'indirizzo ILB.  Per le configurazioni ASE ILB più comuni, il traffico FTP/FTPS verrà recapitati anche nell'indirizzo ILB.  Tuttavia le operazioni di gestione Azure passeranno alle porte 454/455 VIP pubbliche di un ILB attivato ASE.

La figura seguente mostra una panoramica dei flussi di rete in ingresso e in uscita diversi per un ambiente App in App sono associati a un indirizzo IP pubblico:

![Flussi di rete generale][GeneralNetworkFlows]

Un ambiente App possono comunicare con diversi tipi di endpoint clienti privati.  Ad esempio, App in esecuzione nell'ambiente servizio App possibile connettersi al server di database in esecuzione in macchine virtuali IaaS nella topologia di rete virtuale stesso.

>[AZURE.IMPORTANT] Visualizzazione Diagramma reticolare, la "altre calcolare risorse" vengono distribuite in un'altra dall'ambiente servizio di App. La distribuzione delle risorse nella stessa Subnet con la ASE Blocca connettività da ASE alle risorse (ad eccezione di routing specifica all'interno-ASE). Distribuire in un'altra Subnet invece (nella stessa VNET). Quindi saranno in grado di connettersi l'ambiente di servizio App. Non è necessaria alcuna configurazione aggiuntiva.

Servizio App ambienti anche di comunicare con database Sql e lo spazio di archiviazione di Azure risorse necessarie per la gestione e gestione di un ambiente di servizio App.  Alcune delle risorse Sql e lo spazio di archiviazione che un ambiente App comunica con si trovano nella stessa regione ambiente servizio App, mentre gli altri utenti si trovano nelle aree di Azure remote.  Di conseguenza, in uscita a Internet è sempre necessaria una connessione per un ambiente App per il corretto funzionamento. 

Poiché un ambiente App viene distribuito in una subnet, è possono utilizzare gruppi di sicurezza di rete per controllare il traffico in ingresso alla subnet.  Per informazioni dettagliate su come controllare il traffico in ingresso per un ambiente App, vedere l' [articolo]seguente[controllinginboundtraffic].

Per informazioni dettagliate su come consentire la connettività Internet in uscita da un ambiente di servizio App, vedere l'articolo seguente sull'uso di [Strada][ExpressRoute].  La stessa procedura descritta nell'articolo si applica quando si lavora con la connettività a siti e utilizzo forzata tunneling.

## <a name="outbound-network-addresses"></a>Indirizzi di rete in uscita ##
Quando un ambiente App effettua chiamate in uscita, un indirizzo IP è sempre associato a chiamate in uscita.  L'indirizzo IP specifico utilizzato varia a seconda che l'endpoint chiamato si trova all'interno della topologia virtuali o all'esterno della topologia della rete virtuale.

Se l'endpoint chiamato non **di fuori** della topologia di rete virtuale, l'indirizzo in uscita (nello specifico indirizzo NAT in uscita) utilizzato è l'indirizzo VIP pubblico dell'ambiente servizio App.  Questo indirizzo sono disponibili nell'interfaccia utente del portale per l'ambiente servizio App blade proprietà.
 
![Indirizzo IP in uscita][OutboundIPAddress]

È possibile determinare questo indirizzo anche per ASEs contenenti solo un VIP pubblico creando un'app nell'ambiente di servizio di App e quindi eseguire una *nslookup* sull'indirizzo dell'applicazione. L'indirizzo IP risultante sia pubblico VIP sia indirizzo NAT in uscita dell'ambiente servizio App.

Se l'endpoint chiamato **all'interno** della topologia di rete virtuale, l'indirizzo dell'app chiamata in uscita sarà l'indirizzo IP interno della risorsa calcolo singoli esecuzione dell'app.  Tuttavia non esiste un mapping di indirizzi IP interni virtuali alle App persistente.  App è possono spostarsi tra risorse di elaborazione diversi e al pool di elaborazione disponibili risorse in un ambiente App è possono modificare a causa di ridimensionamento delle operazioni.

Tuttavia, poiché un ambiente App sempre si trova all'interno di una subnet, si è certi che l'indirizzo IP interno di una risorsa di calcolo in esecuzione di un'app si troverà sempre all'interno dell'intervallo CIDR della subnet.  Di conseguenza, quando vengono utilizzati diffusamente ACL o gruppi di sicurezza di rete per proteggere l'accesso agli altri endpoint all'interno della rete virtuale, l'intervallo di subnet contenente l'ambiente di servizio App deve concedere l'accesso.

Il diagramma seguente illustra questi concetti in modo più dettagliato:

![Indirizzi di rete in uscita][OutboundNetworkAddresses]

Nella figura precedente:

- Poiché l'indirizzo VIP pubblico dell'ambiente servizio App 192.23.1.2, che è l'indirizzo IP in uscita usato per effettuare chiamate per gli endpoint "Internet".
- L'intervallo CIDR di subnet contenitore per l'ambiente di servizio App è 10.0.1.0/26.  Altri endpoint all'interno dell'infrastruttura di rete virtuale stesso vedranno chiamate dalle App come provenienti da in un punto qualsiasi all'interno di questo intervallo di indirizzi.

## <a name="calls-between-app-service-environments"></a>Chiamate tra ambienti servizio App ##
Uno scenario più complesso può verificarsi se distribuire più ambienti di servizio App nella stessa rete virtuale ed effettuare chiamate in uscita da un ambiente di servizio App in un altro ambiente servizio App.  Questi tipi di incrociato ambiente servizio App chiamate anche essere considerate chiamate "Internet".

Il diagramma seguente illustra un esempio di architettura con App in un ambiente servizio App (ad esempio "Porta anteriore" web apps) chiamata App in un ambiente di servizi di App secondo (ad esempio interno back-end API App non deve essere accessibili da Internet). 

![Chiamate tra ambienti servizio App][CallsBetweenAppServiceEnvironments] 

Nell'esempio precedente l'ambiente di servizio App "ASE uno" ha un indirizzo IP in uscita di 192.23.1.2.  Se un'app in esecuzione su questo ambiente servizio App effettua una chiamata in uscita a un'app in esecuzione in un ambiente di servizi App secondo ("ASE due") trova nella stessa rete virtuale, la chiamata in uscita sarà considerato come una chiamata "Internet".  Di conseguenza il traffico di rete in arrivo sul secondo ambiente servizio App verranno visualizzati come provenienti da 192.23.1.2 (ad esempio non subnet intervallo di indirizzi dell'ambiente servizio App prima).

Anche se le chiamate tra ambienti diversi servizi di App sono considerate chiamate "Internet", se entrambi gli ambienti servizio App si trovano nella stessa regione Azure il traffico di rete rimarrà nella rete Azure internazionali e non fisica scorrerà tramite Internet.  Di conseguenza è possibile usare un gruppo di sicurezza di rete alla subnet del secondo ambiente servizio App per consentire solo le chiamate in arrivo dall'ambiente di servizio App prima (il cui indirizzo IP in uscita è 192.23.1.2), in modo da garantire la comunicazione sicura tra gli ambienti di servizio App.

## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi ##
Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Informazioni dettagliate in ingresso porte utilizzate da App servizio ambienti e utilizzo di gruppi di sicurezza di rete per controllare il traffico in ingresso è disponibile [qui][controllinginboundtraffic].

Informazioni sull'uso di utente definito indirizza per concedere l'accesso a Internet in uscita per gli ambienti servizio App è disponibile in questo [articolo][ExpressRoute]. 


<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[controllinginboundtraffic]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[ExpressRoute]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!-- IMAGES -->
[GeneralNetworkFlows]: ./media/app-service-app-service-environment-network-architecture-overview/NetworkOverview-1.png
[OutboundIPAddress]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundIPAddress-1.png
[OutboundNetworkAddresses]: ./media/app-service-app-service-environment-network-architecture-overview/OutboundNetworkAddresses-1.png
[CallsBetweenAppServiceEnvironments]: ./media/app-service-app-service-environment-network-architecture-overview/CallsBetweenEnvironments-1.png

