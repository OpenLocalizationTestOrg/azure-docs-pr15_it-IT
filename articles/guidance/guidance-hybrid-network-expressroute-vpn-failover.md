<properties
   pageTitle="Implementare un'architettura di rete ibrido disponibilità | Microsoft Azure"
   description="Come implementare un'architettura di rete protetta da sito che si estende su una rete virtuale Azure e una rete locale connessi mediante ExpressRoute failover gateway VPN."
   services="guidance,virtual-network,vpn-gateway,expressroute"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-highly-available-hybrid-network-architecture"></a>Implementare un'architettura di rete ibrido disponibilità

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

In questo articolo vengono illustrati procedure consigliate per la connessione di una rete locale a reti virtuali in Azure usando ExpressRoute, con una sito di rete privata virtuale (VPN) come connessione failover. Flusso del traffico tra la rete locale e una rete virtuale Azure (VNet) tramite una connessione ExpressRoute.  Se c'è una perdita di connettività di circuito ExpressRoute, verrà reindirizzato il traffico attraverso un tunnel VPN IPSec.

> [AZURE.NOTE] Azure include due diversi modelli di distribuzione: [Gestione risorse] [ resource-manager-overview] e classica. Questo progetto utilizza Manager delle risorse, Microsoft consiglia per le distribuzioni di nuove.

Casi di utilizzo tipico per questa architettura includono:

- Applicazioni ibride nel punto in cui vengono distribuite carichi di lavoro tra una rete locale e Azure.

- Applicazioni in esecuzione su larga scala, critici carichi di lavoro che richiedono un numero elevato di scalabilità.

- Su larga scala strutture di backup e ripristino per i dati che devono essere salvati fuori sede.

- Carichi di lavoro di gestione dati.

- Uso di Azure come un sito di ripristino di emergenza.

Si noti che se circuito ExpressRoute è disponibile, la distribuzione dei VPN solo gestirà connessioni peering private. Peering pubbliche e a Microsoft peering connessioni passerà tramite Internet.

## <a name="architecture-diagram"></a>Diagramma dell'architettura

>[AZURE.NOTE] Il [servizio Gateway VPN Azure] [ azure-vpn-gateway] implementare i due tipi di gateway virtuali; Gateway virtuali VPN ed ExpressRoute virtuali gateway. In questo documento, il termine *Gateway VPN* si riferisce al servizio Azure, mentre le frasi *gateway virtuali VPN* e *gateway virtuali ExpressRoute* vengono utilizzati per fare riferimento a implementazioni VPN ed ExpressRoute del gateway rispettivamente.

Nel diagramma seguente evidenzia i componenti principali in questa architettura:

> Un documento di Visio che include questo diagramma architettura è disponibile per il download all' [area download Microsoft][visio-download]. Il diagramma è "rete ibrido - ER VPN" pagina.

![[0]][0]

- **Reti virtuali Azure (VNets).** Ogni VNet si trova in una singola regione Azure e può contenere più livelli dell'applicazione. Livelli dell'applicazione possono essere segmentati mediante subnet in ogni VNet e/o i gruppi di sicurezza (NSGs) di rete.

- **Rete aziendale in locale.** Si tratta di una rete di computer e dispositivi, la connessione tramite una rete LAN privata in esecuzione all'interno dell'organizzazione.

- **Accessorio VPN.** Si tratta di un dispositivo o servizio che fornisce connettività esterne alla rete locale. Accessorio VPN sia un dispositivo hardware o può essere una soluzione di software, ad esempio il Routing e remoto accesso servizio () in Windows Server 2012.

> [AZURE.NOTE] Per un elenco dei dispositivi VPN supportati e informazioni sulla configurazione di dispositivi VPN selezionate per la connessione a un Gateway VPN Azure, vedere le istruzioni per il dispositivo appropriato nell' [elenco dei dispositivi VPN supportati da Azure][vpn-appliance].

- **Gateway virtuali VPN.** Il gateway virtuali VPN consente VNet a cui connettersi accessorio VPN nella rete locale. Il gateway virtuali VPN è configurato per accettare le richieste dalla rete locale solo tramite l'accessorio VPN. Per ulteriori informazioni, vedere [connettersi a una rete locale a una rete Microsoft Azure][connect-to-an-Azure-vnet].

- **Gateway di rete virtuale ExpressRoute.** Il gateway virtuali ExpressRoute consente VNet a cui connettersi circuito ExpressRoute utilizzato per la connettività di rete locale.

- **Subnet gateway.** Il gateway virtuali vengono conservate nella stessa subnet.

- **Connessione VPN.** Proprietà che specificano il tipo di connessione (IPSec) e la chiave condivise con l'accessorio VPN locale per crittografare il traffico la connessione.

- **ExpressRoute circuito.** Si tratta di un livello 2 o circuito livello 3 forniti dal provider di connettività di rete join il locali con Azure attraverso router bordo. Il circuito utilizza l'infrastruttura hardware gestito dal provider di integrazione applicativa.

- **Applicazione di livelli cloud.** Si tratta dell'applicazione presente nel Azure. Può includere più livelli, con più subnet la connessione tramite servizi di bilanciamento del carico Azure. Il traffico in ogni subnet può essere soggetto a regole definite mediante i [Gruppi di sicurezza di Azure rete][azure-network-security-group](NSGs). Per ulteriori informazioni, vedere [Guida introduttiva a Microsoft Azure sicurezza][getting-started-with-azure-security].

## <a name="recommendations"></a>Consigli

Azure offre molte risorse diverse e tipi di risorse, in modo che l'architettura di riferimento può essere completato il provisioning molti modi diversi. Sono disponibili un modello di gestione di risorse Azure per installare l'architettura di riferimento che segue questi suggerimenti. Se si sceglie di creare il proprio architettura di riferimento è necessario seguire queste raccomandazioni, a meno che non si dispone di un requisito specifico non appartenente a un suggerimento.

### <a name="vnet-and-gatewaysubnet"></a>VNet e GatewaySubnet

Creare il gateway di rete virtuale ExpressRoute e il gateway virtuali VPN in VNet stesso. Questo errore indica che condividono la stessa subnet denominata **GatewaySubnet**

Se il VNet include già una subnet denominata **GatewaySubnet**, assicurarsi che abbia un /27 o maggiore spazio di indirizzi. Se è troppo piccola subnet esistente, rimuoverlo come indicato di seguito e crearne uno nuovo, come illustrato al punto successivo:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Remove-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet
```

Se il VNet non contiene una subnet denominata **GatewaySubnet**, crearne uno nuovo come indicato di seguito:

```powershell
$vnet = Get-AzureRmVirtualNetworkGateway -Name <yourvnetname> -ResourceGroupName <yourresourcegroup>
Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet -AddressPrefix "10.200.255.224/27"
$vnet = Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="vpn-and-expressroute-gateways"></a>Gateway VPN ed ExpressRoute

Verificare che l'organizzazione soddisfi i [requisiti di prerequisiti ExpressRoute] [ expressroute-prereq] per la connessione a Azure.

Se si dispone già di un gateway virtuali VPN nel VNet Azure, rimuoverlo, come illustrato di seguito.

```powershell
Remove-AzureRmVirtualNetworkGateway -Name <yourgatewayname> -ResourceGroupName <yourresourcegroup>
```

Seguire le istruzioni per [l'implementazione di un'architettura di rete ibrido con Azure ExpressRoute] [ implementing-expressroute] per stabilire la connessione ExpressRoute.

Seguire le istruzioni per [l'implementazione di un'architettura di rete ibrido con Azure e VPN locale] [ implementing-vpn] per stabilire la connessione al gateway virtuali VPN.

Dopo aver stabilito le connessioni di gateway virtuali, testare l'ambiente come segue:

1. Assicurarsi che sia possibile connettersi alla rete locale ai VNet Azure.

2. Contattare il provider per interrompere la connettività ExpressRoute per la verifica.

3. Verificare che comunque il sia possibile connettersi dalla rete locale per il VNet Azure usando la connessione di gateway virtuali VPN.

4. Contattare il provider di reestabilish ExpressRoute connettività.

## <a name="considerations"></a>Considerazioni

Per motivi di ExpressRoute, vedere [implementare un'architettura di rete ibrido con Azure ExpressRoute] [ guidance-expressroute] indicazioni.

Per motivi di VPN da sito, vedere di [implementare un'architettura di rete ibrido con Azure e VPN locale] [ guidance-vpn] indicazioni.

Per motivi di sicurezza di Azure generali, vedere [servizi cloud Microsoft e la sicurezza della rete][best-practices-security].

## <a name="solution-deployment"></a>Distribuzione di soluzioni

Se si dispone di un'infrastruttura locale già configurata con un dispositivo di rete adatto, è possibile distribuire l'architettura di riferimento procedendo come segue:

1. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy.json)

2. Attendere che il collegamento per aprire il portale di Azure, attenersi alla seguente procedura: 
  - Il nome del **gruppo di risorse** è già definito nel file di parametri, quindi selezionare **Crea nuovo** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

3. Attendere che la distribuzione completare.

4. Fare clic con il pulsante destro sul pulsante e selezionare uno dei due "Apri collegamento in una nuova scheda" o "Apri collegamento in un'altra finestra":  
[![Distribuire Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-vpn-er%2Fazuredeploy-expressRouteCircuit.json)

5. Attendere che il collegamento aprire il portale di Azure, quindi immettere attenersi alla seguente procedura: 
  - Selezionare **Usa esistente** nella sezione **gruppo di risorse** e immettere `ra-hybrid-vpn-er-rg` nella casella di testo.
  - Selezionare l'area nella casella a discesa **posizione** .
  - Non modificare il **Modello radice Uri** o caselle di testo **Parametro radice Uri** .
  - Esaminare le condizioni e quindi fare clic sulla casella di controllo **che accetto i termini e condizioni indicate in precedenza** .
  - Fare clic sul pulsante di **acquisto** .

<!-- links -->

[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vpn-appliance]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[azure-vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[connect-to-an-Azure-vnet]: https://technet.microsoft.com/library/dn786406.aspx
[azure-network-security-group]: ../virtual-network/virtual-networks-nsg.md
[getting-started-with-azure-security]: ./../security/azure-security-getting-started.md
[expressroute-prereq]: ../expressroute/expressroute-prerequisites.md
[implementing-expressroute]: ./guidance-hybrid-network-expressroute.md#implementing-this-architecture
[implementing-vpn]: ./guidance-hybrid-network-vpn.md#implementing-this-architecture
[guidance-expressroute]: ./guidance-hybrid-network-expressroute.md
[guidance-vpn]: ./guidance-hybrid-network-vpn.md
[best-practices-security]: ../best-practices-network-security.md
[solution-script]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/Deploy-ReferenceArchitecture.ps1
[solution-script-bash]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetwork.parameters.json
[virtualnetworkgateway-vpn-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-vpn.parameters.json
[virtualnetworkgateway-expressroute-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/virtualNetworkGateway-expressRoute.parameters.json
[er-circuit-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-hybrid-network-vpn-er/parameters/expressRouteCircuit.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[naming conventions]: ./guidance-naming-conventions.md
[azure-cli]: https://azure.microsoft.com/documentation/articles/xplat-cli-install/
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[0]: ./media/blueprints/hybrid-network-expressroute-vpn-failover.png "Architettura di un'architettura di rete ibrido disponibilità tramite VPN ed ExpressRoute gateway"
[ARM-Templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
