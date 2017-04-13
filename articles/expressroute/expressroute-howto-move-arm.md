<properties
   pageTitle="Spostare circuiti ExpressRoute da classico a Gestione risorse | Microsoft Azure"
   description="Questa pagina illustra come spostare un circuito classico per il modello di distribuzione di Manager delle risorse."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="ganesr"/>


# <a name="move-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Circuiti ExpressRoute classica spostare il modello di distribuzione di Manager delle risorse

## <a name="configuration-prerequisites"></a>Prerequisiti di configurazione

- È necessaria la versione più recente di moduli Azure PowerShell (almeno versione 1.0).
- Assicurarsi che siano state esaminate i [Prerequisiti](expressroute-prerequisites.md), [routing requisiti](expressroute-routing.md)e [flussi di lavoro](expressroute-workflows.md) prima di iniziare la configurazione.
- Prima di procedere ulteriormente, esaminare le informazioni fornite in [spostamento un circuito ExpressRoute da classica di Manager delle risorse](expressroute-move.md). Assicurarsi che hanno pienamente compresi i limiti e le limitazioni di ciò che è possibile.
- Se si desidera spostare un circuito Azure ExpressRoute dal modello di distribuzione classica nel modello di distribuzione di Manager delle risorse di Azure, è necessario disporre circuito completamente configurato e operativo nel modello di distribuzione classica.
- Verificare di disporre di un gruppo di risorse che è stato creato nel modello di distribuzione Manager delle risorse.

## <a name="move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Spostare il circuito ExpressRoute nel modello di distribuzione di Manager delle risorse

È necessario spostare un circuito ExpressRoute per il modello di distribuzione di Manager delle risorse in modo che è possibile utilizzare la visualizzazione classica e i modelli di distribuzione di Manager delle risorse. È possibile eseguire questa operazione eseguendo i comandi di PowerShell seguenti.

### <a name="step-1-gather-circuit-details-from-the-classic-deployment-model"></a>Passaggio 1: Raccogliere dettagli circuito dal modello di distribuzione classica

È necessario raccogliere informazioni sul circuito ExpressRoute prima di tutto.

Accedere all'ambiente di classica Azure e raccogliere la chiave del servizio. È possibile usare frammento di PowerShell riportato di seguito per raccogliere le informazioni:

    # Sign in to your Azure account
    Add-AzureAccount

    # Select the appropriate Azure subscription
    Select-AzureSubscription "<Enter Subscription Name here>"

    # Import the PowerShell modules for Azure and ExpressRoute
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

    # Get the service keys of all your ExpressRoute circuits
    Get-AzureDedicatedCircuit

Copiare la **chiave del servizio** di circuito che si desidera spostare nel modello di distribuzione di Manager delle risorse.

### <a name="step-2-sign-in-to-the-resource-manager-environment-and-create-a-new-resource-group"></a>Passaggio 2: Accedere all'ambiente di gestione risorse e creare un nuovo gruppo di risorse

È possibile creare un nuovo gruppo di risorse utilizzando il frammento di codice seguente:

    # Sign in to your Azure Resource Manager environment
    Login-AzureRmAccount

    # Select the appropriate Azure subscription
    Get-AzureRmSubscription -SubscriptionName "<Enter Subscription Name here>" | Select-AzureRmSubscription

    #Create a new resource group if you don't already have one
    New-AzureRmResourceGroup -Name "DemoRG" -Location "West US"

È anche possibile usare un gruppo di risorse esistente se si dispone già di una.

### <a name="step-3-move-the-expressroute-circuit-to-the-resource-manager-deployment-model"></a>Passaggio 3: Spostare circuito ExpressRoute nel modello di distribuzione di Manager delle risorse

A questo punto si è pronti a muovere i circuito ExpressRoute dalla visualizzazione classica nel modello di distribuzione di Manager delle risorse. Rivedere le informazioni fornite in [spostamento un circuito ExpressRoute da classico per il modello di distribuzione di Manager delle risorse](expressroute-move.md) prima di procedere ulteriormente.

È possibile eseguire questa operazione eseguendo il frammento di codice seguente:

    Move-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "DemoRG" -Location "West US" -ServiceKey "<Service-key>"

>[AZURE.NOTE] Al termine dello spostamento, il nuovo nome è elencato in cmdlet precedente verrà utilizzato per la risorsa di indirizzi. Il circuito risulta essere rinominato.

## <a name="enable-an-expressroute-circuit-for-both-deployment-models"></a>Abilitare un circuito ExpressRoute per entrambi i modelli di distribuzione

Prima di controllare l'accesso al modello di distribuzione, è necessario spostare il circuito ExpressRoute per il modello di distribuzione di Manager delle risorse.

Eseguire il cmdlet seguente per abilitare l'accesso a entrambi i modelli di distribuzione:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to TRUE
    $ckt.AllowClassicOperations = $true

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Al termine questa operazione, sarà possibile visualizzare il circuito nel modello di distribuzione classica.

Eseguire le operazioni seguenti per visualizzare i dettagli del circuito ExpressRoute:

    get-azurededicatedcircuit

È necessario essere possibile visualizzare la chiave del servizio nell'elenco. È ora possibile gestire i collegamenti alle circuito ExpressRoute utilizzando i comandi dei modelli di distribuzione classica standard per VNets classica e i comandi ARM standard per ARM VNETs. Gli articoli seguenti consentono all'utente come gestire i collegamenti alle circuito ExpressRoute:

- [Collegare la rete virtuale il circuito ExpressRoute nel modello di distribuzione Manager delle risorse](expressroute-howto-linkvnet-arm.md)
- [Collegare la rete virtuale il circuito ExpressRoute nel modello di distribuzione classica](expressroute-howto-linkvnet-classic.md)


## <a name="disable-the-expressroute-circuit-to-the-classic-deployment-model"></a>Disabilitare il circuito ExpressRoute per il modello di distribuzione classica

Eseguire il cmdlet seguente per disattivare l'accesso al modello di distribuzione classica:

    # Get details of the ExpressRoute circuit
    $ckt = Get-AzureRmExpressRouteCircuit -Name "DemoCkt" -ResourceGroupName "DemoRG"

    #Set "Allow Classic Operations" to FALSE
    $ckt.AllowClassicOperations = $false

    # Update circuit
    Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt

Al termine questa operazione, non sarà possibile visualizzare il circuito nel modello di distribuzione classica.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il circuito, assicurarsi di eseguire le operazioni seguenti:

- [Creare e modificare il routing per il circuito ExpressRoute](expressroute-howto-routing-arm.md)
- [Collegare il circuito ExpressRoute rete virtuale](expressroute-howto-linkvnet-arm.md)
