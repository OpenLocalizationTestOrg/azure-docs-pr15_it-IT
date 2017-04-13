<properties
   pageTitle="Creare VNet Peering utilizzare i modelli di gestione risorse | Microsoft Azure"
   description="Informazioni su come creare un peering virtuali mediante i modelli in Gestione risorse."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai;annahar"/>

# <a name="create-vnet-peering-using-resource-manager-templates"></a>Creare VNet Peering utilizzare i modelli di Manager delle risorse

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Per creare un VNet peering con i modelli di Manager delle risorse, seguire la procedura seguente:

1. Se non è mai utilizzato Azure PowerShell, vedere [come installare e configurare PowerShell Azure](../powershell-install-configure.md) e seguire le istruzioni fino alla fine per eseguire l'accesso a Azure e selezionare l'abbonamento.

    > [AZURE.NOTE] Cmdlet di PowerShell per gestire VNet peering viene fornito con [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Il testo seguente illustra la definizione di un collegamento di peering VNet VNet1 a VNet2, in base allo scenario precedente. Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet1.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }

3. La sezione seguente illustra la definizione di un collegamento di peering VNet VNet2 a VNet1, in base allo scenario precedente.  Copiare il contenuto seguente e salvarlo in un file denominato VNetPeeringVNet2.json.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }

    Come illustrato nel modello precedente, esistono alcune proprietà configurabili per VNet peering:

  	|Opzione|Descrizione|Impostazione predefinita|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Se lo spazio di indirizzi di un peer VNet è incluso come parte del tag virtual_network.|Sì|
  	|AllowForwardedTraffic|Se il traffico non provenienti da un VNet peered viene accettato o eliminato.|No|
  	|AllowGatewayTransit|Consente al peer VNet verrà utilizzato il gateway VNet.|No|
  	|UseRemoteGateways|Usare gateway VNet del peer. Peer VNet deve avere configurato un gateway e AllowGatewayTransit selezionato. È possibile utilizzare questa opzione se si è configurato un gateway.|No|

    Ogni collegamento VNet peering è l'insieme di proprietà indicate. Ad esempio, è possibile impostare AllowVirtualNetworkAccess su True per il collegamento di peering VNet VNet1 per VNet2 e impostarla su False per il collegamento di peering VNet in altra direzione.


4. Per distribuire il file del modello, è possibile eseguire il cmdlet New-AzureRmResourceGroupDeployment per creare o aggiornare la distribuzione. Per ulteriori informazioni sull'utilizzo dei modelli di Manager delle risorse, fare riferimento al presente [articolo](../resource-group-template-deploy.md).

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

    > [AZURE.NOTE] Sostituire il gruppo nome e modello di file di risorse in base alle esigenze.

    Sotto è illustrato un esempio basato sullo scenario precedente:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

    Mostra output:

        DeploymentName      : VNetPeeringVNet1
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

    Mostra output:

        DeploymentName      : VNetPeeringVNet2
        ResourceGroupName   : VNet101
        ProvisioningState       : Succeeded
        Timestamp           : 7/26/2016 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters          :
        Outputs         :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Al termine della distribuzione, è possibile eseguire il cmdlet seguente per visualizzare lo stato peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    Mostra output:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : VNet101
        VirtualNetworkName  : VNet1
        ProvisioningState       : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Dopo aver peering stabilito in questo scenario, dovrebbe essere possibile avviare le connessioni da qualsiasi computer virtuale a qualsiasi computer virtuale entrambi VNets. Per impostazione predefinita, VNet peering verrà provisioning degli ACL appropriate per consentire le comunicazioni tra VNets AllowVirtualNetworkAccess è vero. È comunque possibile applicare regole di gruppo (NSG) di sicurezza di rete per bloccare la connettività tra subnet specifiche o macchine virtuali di assumere il controllo di accesso tra due reti virtuale avanzato.  Per ulteriori informazioni di creazione di regole di NSG, fare riferimento al presente [articolo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Per creare un VNet peering abbonamenti, seguire la procedura seguente:

1. Accedere a Azure con privilegi un utente dell'account di sottoscrizione A ed eseguire il seguente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

    Non si tratta di un requisito peering è possibile stabilire anche se gli utenti singolarmente generano peering richieste per i rispettivi Vnets come le richieste corrispondono. Aggiunta di un utente con privilegi di altri VNet come gli utenti in VNet locale, è facile eseguire l'installazione.

2. Accedere a Azure con privilegi-dell'utente B account per abbonamento B ed eseguire il seguente cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Nell'oggetto utente 's sessione di accesso, eseguita questo cmdlet:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Ecco come viene definito il file JSON.  

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }

4. Nella sessione di accesso utente-B, eseguire il cmdlet seguente:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all

    Ecco come viene definito il file JSON:

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }

    Dopo aver peering stabilito in questo scenario, dovrebbe essere possibile avviare le connessioni da qualsiasi computer virtuale a qualsiasi macchina virtuale di entrambi VNets diversi abbonamenti.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. In questo scenario, è possibile distribuire il modello di esempio riportato di seguito per stabilire il VNet peering.  È necessario impostare la proprietà AllowForwardedTraffic su True, che consente il dispositivo virtuale di rete in VNet peered per inviare e ricevere il traffico.

    Ecco il modello per la creazione di un VNet peering da HubVNet a VNet1. Si noti che AllowForwardedTraffic sia impostato su false.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
            }
        ]
        }

2. Ecco il modello per la creazione di un VNet peering da VNet1 a HubVnet. Si noti che AllowForwardedTraffic è impostato su true.

        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }


3. Dopo avere stabilito peering, è possibile fare riferimento al presente [articolo](virtual-network-create-udr-arm-ps.md) per definire routes(UDR) definite dall'utente per reindirizzare il traffico VNet1 tramite un dispositivo virtuale per usare le funzionalità. Quando si specifica l'indirizzo hop successivo nella route, è possibile impostare l'indirizzo IP del dispositivo virtuale in peer VNet HubVNet.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Per creare un peering tra reti virtuali da diversi modelli di distribuzione, seguire la procedura seguente:
1. Il testo seguente illustra la definizione di un collegamento di peering VNet VNET1 a VNET2 in questo scenario. Per una rete virtuale classica a una rete manager delle risorse Azure peer è necessario solo un collegamento.

    Assicurarsi di inserire il proprio ID abbonamento per dove si trova il classico virtuali o VNET2 e modificare MyResouceGroup al nome del gruppo risorse appropriato.

    {"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", "contentVersion": "1.0.0.0", "parametri": {}, "variabili": {}, "risorse": [{"apiVersion": "2016-06-01", "tipo": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings", "nome": "VNET1/LinkToVNET2", "posizione": "[resourceGroup () .location]", "proprietà": {"allowVirtualNetworkAccess": true, "allowForwardedTraffic": false, "allowGatewayTransit": false, "useRemoteGateways": false, "remoteVirtualNetwork": {"id": "[resourceId (' Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"}}}]}

2. Per distribuire il file del modello, eseguire il cmdlet seguente per creare o aggiornare la distribuzione.

        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all

        Output shows:

        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent

3. Dopo avere stabilito la distribuzione, è possibile eseguire il cmdlet seguente per visualizzare lo stato peering:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2

        Output shows:

        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Dopo aver peering stabilito tra un VNet classica e gestione delle risorse VNet, dovrebbe essere possibile avviare le connessioni da qualsiasi computer virtuale in VNET1 a qualsiasi macchina virtuale VNET2 e viceversa.
