<properties 
    pageTitle="Dettagli relativi alla configurazione della rete per l'utilizzo di strada" 
    description="Dettagli sulla configurazione di rete per l'esecuzione di App servizio ambienti in reti virtuali connessa a un circuito ExpressRoute." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Dettagli sulla configurazione di rete per gli ambienti servizio App con ExpressRoute 

## <a name="overview"></a>Panoramica ##
I clienti possono collegare un [Azure ExpressRoute] [ ExpressRoute] circuito a infrastruttura di rete virtuale, estendendo così la rete locale in Azure.  È possibile creare un ambiente App in una subnet della [rete virtuale] [ virtualnetwork] dell'infrastruttura.  Le applicazioni in esecuzione sull'ambiente servizio App possono quindi connessioni protette alle risorse di back-end accessibili solo tramite la connessione ExpressRoute.  

Un ambiente App possono essere creato in **dei due** virtuali un Manager delle risorse di Azure, **o** una distribuzione classica modello virtuali.  Con una modifica apportata nel giugno 2016, ASEs anche ora possono essere distribuiti in reti virtuali che usano gli intervalli di indirizzi pubblico o gli spazi indirizzo RFC1918 (ad esempio indirizzi privati). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Connettività di rete necessari ##
Esistono requisiti di connettività di rete per gli ambienti servizio App che non possono essere soddisfatte inizialmente in una rete virtuale connessa a un ExpressRoute.  Servizio di App ambienti richiedono tutte le operazioni seguenti per il corretto funzionamento:


-  Connettività di rete in uscita per gli endpoint archiviazione Azure in tutto il mondo in entrambe le porte 80 e 443.  Sono inclusi i punti finali che si trova nella stessa regione come ambiente servizio App, nonché i punti finali dello spazio di archiviazione disponibile in **altre** aree Azure.  Risolvere i punti finali dello spazio di archiviazione Azure in seguenti domini DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* e *file.core.windows.net*.  
-  Connettività di rete in uscita per il servizio di Azure file sulla porta 445.
-  Connettività di rete in uscita per i punti finali DB Sql che si trova nella stessa regione l'ambiente di servizio App.  Consente di risolvere i punti finali DB SQL all'interno del dominio seguente: *database.windows.net*.  È necessario aprire accesso alle porte 1433 11000 11999 e 14999 14000.  Per ulteriori informazioni vedere [l'articolo sull'utilizzo di porta V12 di Database Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Connettività di rete in uscita per gli endpoint di piano di gestione Azure (endpoint ASM e ARM).  Sono inclusi in uscita connettività a *management.core.windows.net* e *management.azure.com*. 
-  Connettività di rete in uscita *ocsp.msocsp.com*, *mscrl.microsoft.com* e *crl.microsoft.com*.  Questa operazione è necessaria per supportare funzionalità SSL.
-  La configurazione del DNS per la rete virtuale deve essere in grado di risolvere tutti i punti finali e domini menzionati ai punti precedenti.  Se non è possibile risolvere questi endpoint, tentativi di creazione di ambiente servizio App avrà esito negativo e ambienti servizio App esistenti verranno contrassegnati come non corretti.
-  Accesso in uscita sulla porta 53 è necessario per la comunicazione con i server DNS.
-  Se è presente un server DNS personalizzato a altra estremità di un gateway VPN, il server DNS deve essere raggiungibile dalla subnet contenente l'ambiente di servizio App. 
-  Il percorso di rete in uscita non passano attraverso proxy aziendale interno e non è possibile forzare tunnel locale.  In questo modo modifica l'indirizzo NAT effettivo del traffico in uscita dall'ambiente servizio di App.  Modifica dell'indirizzo NAT del traffico in uscita dell'ambiente servizio App causerà errori di connettività a-molti dei punti finali elencati sopra.  Il risultato tentativi di creazione di ambiente servizio App, come in precedenza integro ambienti ibridi di servizio App viene contrassegnato come non corretti.  
-  In ingresso accesso alla rete per porte necessarie per gli ambienti servizio App deve disporre dell'autorizzazione come descritto in questo [articolo][requiredports].

Possono essere soddisfatti i requisiti DNS garantendo un'infrastruttura DNS valida viene configurata e gestita per la rete virtuale.  Se per qualsiasi motivo la configurazione del DNS viene modificato dopo la creazione di un ambiente App, gli sviluppatori possono forzare un ambiente App per selezionare la configurazione del nuovo DNS.  Attivazione in sequenza ambiente riavviare il computer tramite l'icona "Riavviare" nella parte superiore della stessa e di gestione App servizio ambiente nel [portale di Azure] [ NewPortal] impedirà l'ambiente selezionare la configurazione del nuovo DNS.

Possono essere soddisfatti i requisiti di accesso di rete in ingresso mediante la configurazione di un [gruppo di sicurezza di rete] [ NetworkSecurityGroups] subnet dell'ambiente servizio App per consentire l'accesso necessario come descritto in questo [articolo][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>Abilitare la connettività di rete in uscita per un ambiente App##
Per impostazione predefinita, un circuito ExpressRoute appena creato annuncia una route predefinita che consente la connessione Internet in uscita.  Con questa configurazione un ambiente App saranno in grado di connettersi ad altri endpoint Azure.

Tuttavia una configurazione cliente comune consiste nel definire le proprie route predefinita (0.0.0.0/0) che impone Internet il traffico in uscita invece flusso locale.  Questo flusso di traffico interruzioni sempre App servizio ambienti perché il traffico in uscita bloccati in locale oppure NAT da un set di indirizzi che non funzionano con vari endpoint Azure riconosciuto.

La soluzione consiste nel definire uno (più) route definite dall'utente (UDRs) su subnet che contiene l'ambiente di servizio App.  Un UDR definisce route specifici della subnet che verranno utilizzato il valore anziché la route predefinita.

Se possibile, si consiglia di utilizzare le seguenti operazioni:

- La configurazione di ExpressRoute annuncia 0.0.0.0/0 e per impostazione predefinita forza tunnel tutto il traffico in uscita locale.
- UDR applicati alla subnet contenente l'ambiente di servizio App definisce 0.0.0.0/0 con un tipo di hop successivo di Internet (un esempio è più in basso in questo articolo).

L'effetto combinato di questa procedura è che il livello di subnet UDR avranno la precedenza sui ExpressRoute forzata tunneling, in modo da garantire l'accesso a Internet in uscita dall'ambiente servizio di App.

> [AZURE.IMPORTANT] Route definite in una UDR **deve** essere abbastanza specifico per hanno la precedenza su qualsiasi route annunciate da ExpressRoute configurazione non corretta.  Nell'esempio seguente viene utilizzata l'intervallo di indirizzi 0.0.0.0/0 generali e come tali potrebbero venire accidentalmente annullata da annunci route con più intervalli di indirizzi.
>
>Gli ambienti servizio App non sono supportate con configurazioni ExpressRoute tale **annunciare tra route dal percorso di peering pubblico al percorso peering privato**.  Configurazioni ExpressRoute contenenti pubblico peering configurato, verrà visualizzato annunci route da Microsoft per un elevato numero di intervalli di indirizzi IP di Microsoft Azure.  Se questi intervalli di indirizzi tra annunciata nel percorso peering privato, il risultato finale è che tutti i pacchetti di rete in uscita dalla subnet dell'ambiente servizio App sarà tunnel vigore a infrastruttura di rete locale del cliente.  Questo flusso di rete non è attualmente supportato con App servizio ambienti.  Per risolvere questo problema è interrompere route relative alla pubblicità tra dal percorso di peering pubblico al percorso peering privato.

Informazioni generali su route definite dall'utente sono disponibile in questa [Panoramica][UDROverview].  

Informazioni dettagliate sulla creazione e configurazione di route definite dall'utente è disponibile in questa [Modalità Guida][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Esempio di configurazione UDR per un ambiente App ##

**Prerequisiti**

1. Installare Powershell Azure dalla [pagina Download Azure] [ AzureDownloads] (datata giugno 2015 o versioni successiva).  In "Strumenti della riga di comando" è un collegamento "Installa" in "Windows Powershell" per l'installazione i cmdlet di Powershell più recente.

2. Si consiglia di una subnet univoca viene creata in accesso esclusivo da un ambiente App.  In questo modo che UDRs applicati alla subnet solo aprirà il traffico in uscita per l'ambiente di servizio App.
3. **Importante**: non distribuire l'ambiente di servizio di App fino a **dopo** la procedura di configurazione seguente è seguita.  In questo modo che la connettività di rete in uscita sia disponibile prima di distribuire un ambiente App.

**Passaggio 1: Creare una tabella di route denominata**

Il frammento di codice seguente crea una tabella di route denominata "DirectInternetRouteTable" nell'area Ovest Contattaci Azure:

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Passaggio 2: Creare uno o più route della tabella**

È necessario aggiungere uno o più route alla tabella route per consentire l'accesso a Internet in uscita.  

L'approccio consigliato per la configurazione di accesso in uscita a Internet è definire una route per 0.0.0.0/0 come illustrato di seguito.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

Tenere presente che 0.0.0.0/0 è un intervallo ampio indirizzo e pertanto verrà sostituito da più specifici intervalli di indirizzi annunciati dalla ExpressRoute.  Per ripetere l'operazione nuovamente consigliato in precedenza, UDR con una route 0.0.0.0/0 dovrebbe essere usata in combinazione con una configurazione ExressRoute che ne annuncia solo 0.0.0.0/0 anche. 

In alternativa, è possibile scaricare un elenco esaustivo e aggiornato di intervalli CIDR in uso da Azure.  Il file Xml contenente tutti gli intervalli di indirizzi IP di Azure è disponibile dall' [Area Download Microsoft][DownloadCenterAddressRanges].  

È tuttavia notare che questi intervalli cambiano nel tempo, pertanto la necessità di eseguire aggiornamenti manuali periodici alle route definite dall'utente per mantenere aggiornati.  Inoltre, poiché non esiste un limite massimo predefinito di 100 route in un singolo UDR, sarà necessario "riepilogare" gli intervalli di indirizzi IP di Azure in modo da adattarlo entro il limite di 100 route, tenendo presente che UDR definito indirizza devono essere più specifico le route annunciato dal ExpressRoute.  


**Passaggio 3: Associare la tabella route alla subnet contenente l'ambiente di servizio App**

L'ultimo passaggio consiste nel associare la tabella route alla subnet in cui verrà distribuito l'ambiente di servizio App.  Il comando seguente associa "DirectInternetRouteTable" a "ASESubnet" che conterranno un ambiente App.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Passaggio 4: Passaggi finali**

Dopo la tabella route è associata alla subnet, è consigliabile verificare e confermare l'effetto desiderato.  Ad esempio, distribuire una macchina virtuale nella subnet e verificare che:


- In questo articolo **non** scorrono verso il basso il circuito ExpressRoute, il traffico in uscita per endpoint sia Azure e Azure non indicato in precedenza.  È importante verificare questo comportamento, poiché se il traffico in uscita dalla subnet è ancora del resto tunnel locale, ambiente servizio App sempre non verrà creato. 
- Le ricerche DNS per gli endpoint indicato in precedenza sono tutti risolvere correttamente. 

Una volta i passaggi sopra descritti confermano, sarà necessario eliminare la macchina virtuale alla subnet in quanto deve essere "vuoto" durante la creazione dell'ambiente servizio App.
 
Quindi procedere con la creazione di un ambiente App!

## <a name="getting-started"></a>Guida introduttiva
Tutti gli articoli e la modalità-per per gli ambienti servizio App sono disponibili nel [file Leggimi per gli ambienti di servizio dell'applicazione](../app-service/app-service-app-service-environments-readme.md).

Per iniziare a utilizzare App servizio ambienti, vedere [Introduzione all'ambiente di servizio App][IntroToAppServiceEnvironment]

Per ulteriori informazioni sulla piattaforma Azure App servizio, vedere [Servizio App Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->
