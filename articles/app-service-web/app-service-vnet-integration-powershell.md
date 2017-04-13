<properties
    pageTitle="Collegare l'app alla rete virtuale tramite PowerShell"
    description="Istruzioni su come connettersi e lavorare con le reti virtuali tramite PowerShell"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="wpickett"
    editor="cephalin"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="ccompy"/>

# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>Collegare l'app alla rete virtuale tramite PowerShell #

## <a name="overview"></a>Panoramica ##

Nel servizio di App Azure, è possibile connettersi l'app (web, mobile o API) a una rete virtuale Azure (VNet) nell'abbonamento. Questa funzionalità è denominata VNet integrazione. Non confondere la caratteristica VNet integrazione con la funzionalità di ambiente servizio App, che consente di eseguire un'istanza di Azure App servizio della rete virtuale.

La funzionalità di integrazione VNet presenta un'interfaccia utente (UI) nel portale di nuovo che è possibile utilizzare per l'integrazione con le reti virtuali distribuiti utilizzando il modello di distribuzione classico o il modello di distribuzione di Manager delle risorse di Azure. Per ulteriori informazioni sulla funzionalità, vedere [integrazione l'app con una rete virtuale Azure](web-sites-integrate-with-vnet.md).

In questo articolo è non su come utilizzare l'interfaccia utente ma su come attivare l'integrazione con PowerShell. Poiché i comandi per ogni modello di distribuzione sono diversi, in questo articolo contiene una sezione per ogni modello di distribuzione.  

Prima di continuare con questo articolo, assicurarsi di avere:

- Le ultime Azure PowerShell SDK installato. È possibile installare con l'installazione guidata piattaforma Web.
- Un'app nel servizio di App Azure in esecuzione in un server Standard o Premium SKU.

## <a name="classic-virtual-networks"></a>Reti virtuali classiche ##

In questa sezione illustra tre attività per le reti virtuali che utilizzano il modello di distribuzione classica:

1. Connettere l'app a una rete virtuale preesistente che dispone di un gateway e configurata per la connettività punto al sito.
1. Aggiornare le informazioni sull'integrazione di rete virtuale per l'app.
1. Scollegare l'app rete virtuale.

### <a name="connect-an-app-to-a-classic-vnet"></a>Connettere un'app a un VNet classica ###

Per connettere un'app a una rete, seguire questi tre passaggi:

1. Dichiarare all'app web che verrà aggiunta una particolare rete virtuale. L'app verrà generato un certificato che sarà assegnato alla rete virtuale per la connettività punto al sito.
1. Caricare il certificato di app web alla rete virtuale e quindi recuperare URI del pacchetto VPN punto al sito.
1. Aggiornare la connessione di rete virtuale dell'applicazione web con il pacchetto di punto al sito URI.

Il primo e terzo passaggio è interamente script, ma il secondo passaggio è necessaria un'azione singola, manuale tramite il portale oppure accesso per eseguire azioni **mettere** o **PATCH** per l'endpoint di Manager delle risorse Azure virtuali. Contattare il supporto di Azure che questa opzione. Prima di iniziare, accertarsi di avere una classica rete virtuale che contiene la connettività punto al sito già attivato e un gateway distribuito. Per creare il gateway e abilitare la connettività punto al sito, è necessario utilizzare il portale come descritto nella [creazione di un gateway VPN][createvpngateway].

Deve essere nello stesso abbonamento il piano di servizio App che contiene l'app si integra con la rete virtuale classica.

##### <a name="set-up-azure-powershell-sdk"></a>Configurare Azure PowerShell SDK #####

Aprire una finestra di PowerShell e configurare l'account Azure e la sottoscrizione tramite:

    Login-AzureRmAccount

Comando verrà aperto un messaggio per ottenere le credenziali di Azure. Dopo l'accesso, utilizzare uno dei comandi seguenti per selezionare l'abbonamento a cui si desidera utilizzare. Verificare che si sta utilizzando l'abbonamento a cui il virtuali e il piano di servizio App siano in.

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

o

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>Variabili utilizzate in questo articolo #####

Per semplificare i comandi, viene descritto come impostare una variabile di PowerShell **$Configuration** con la configurazione specifica.

Impostare una variabile come indicato di seguito in PowerShell con i parametri seguenti:

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

Il percorso di app dovrebbe essere la posizione senza spazi. Ad esempio, US Ovest è westus.

    $Configuration.WebAppLocation = "[Your web app Location]"

L'elemento successivo è nel punto in cui deve essere scritti il certificato. Deve trattarsi di un percorso di scrittura nel computer locale. Assicurarsi di includere CER alla fine.

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

Per vedere che cos'è impostata, digitare **$Configuration**.

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

Il resto di questa sezione presuppone che si disponga di una variabile creata come descritto in precedenza.

##### <a name="declare-the-virtual-network-to-the-app"></a>Dichiarare virtuali all'app #####

Utilizzare il comando seguente per indicare l'app che verrà utilizzato questa rete virtuale specifica. In questo modo l'app generare i certificati necessari:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

Se questo comando ha avuto esito positivo, **$vnet** deve contenere una variabile di **proprietà** . La variabile di **proprietà** deve contenere un'identificazione personale certificato e i dati del certificato.

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>Caricare il certificato di app web alla rete virtuale #####

Un manuale, unico passaggio è necessario per ciascuna sottoscrizione e virtuali. Ovvero se ci si connette App nell'abbonamento alla A virtuali, sarà necessario eseguire questo passaggio solo una volta indipendentemente dalle App quanti configurare. Se si aggiunge una nuova app a un'altra rete virtuale, sarà necessario ripetere l'operazione. Il motivo è che viene generato un insieme di certificati a un livello di abbonamento in Azure App servizio e il set viene generato una volta per ogni virtuali App si connetterà.

I certificati verranno sono già stati impostati se è stata seguita la procedura seguente o se è integrato con la stessa rete virtuale tramite il portale.

Il primo passaggio consiste nel generare il file. cer. Il secondo passaggio consiste nel caricare il file. cer alla rete virtuale. Per generare il file. cer dalla chiamata API nel passaggio precedente, eseguire i comandi seguenti.

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

Il certificato troverà nella posizione che **$Configuration.GeneratedCertificatePath** specifica.

Per caricare il certificato manualmente, utilizzare il [portale di Azure] [ azureportal] e **Sfoglia virtuali (classica)** > **connessioni VPN** > **punto al sito** > **Gestione certificati**. Da qui, caricare il certificato.

##### <a name="get-the-point-to-site-package"></a>Ottenere il pacchetto del punto di sito #####

Il passaggio successivo a configurare una connessione di rete virtuale in un'app web è per ottenere il pacchetto del punto al sito e fornire all'app web.

Salvare il modello seguente in un file denominato GetNetworkPackageUri.json in un punto qualsiasi del computer, ad esempio C:\Azure\Templates\GetNetworkPackageUri.json.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


Impostare i parametri di input:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

Chiamare lo script:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


La variabile **$output. Outputs.packageUri** conterrà il pacchetto URI da assegnare all'app web.

##### <a name="upload-the-point-to-site-package-to-your-app"></a>Caricare il pacchetto del punto-sito applicazione in uso. #####

Il passaggio finale è necessario fornirgli l'app il pacchetto. È sufficiente eseguire il comando successivo:

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

Se un messaggio viene richiesto di confermare che si sovrascrivono una risorsa esistente, assicurarsi che consentita l'esecuzione.

Dopo avere stabilito questo comando, l'app dovrebbe ora connessi alla rete virtuale. Per confermare la riuscita, accedere alla console di app e digitare quanto segue:

    SET WEBSITE_

Se c'è una variabile di ambiente denominata WEBSITE_VNETNAME che contiene un valore che corrisponda al nome della rete virtuale di destinazione, tutte le configurazioni sono riuscita.

### <a name="update-classic-vnet-integration-information"></a>Aggiornare le informazioni di integrazione VNet classiche ###

Per aggiornare o Sincronizza nuovamente le informazioni, è sufficiente ripetere i passaggi che è stata seguita quando è stato creato l'integrazione in primo luogo. Questi passaggi:

1. Definire le informazioni di configurazione.
1. Dichiarare virtuali all'app.
1. È possibile ottenere il pacchetto del punto al sito.
1. Caricare il pacchetto del punto al sito per l'app.

### <a name="disconnect-your-app-from-a-classic-vnet"></a>Scollegare l'app da un VNet classica ###

Per disconnettersi l'app, è necessario le informazioni di configurazione durante l'integrazione di rete virtuale è state impostate. Utilizzo di tali informazioni, è quindi un comando da disconnettere l'app dalla rete virtuale.

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Reti virtuali Manager delle risorse ##

Reti virtuali Manager delle risorse hanno API di gestione risorse di Azure, che semplificano alcuni processi rispetto alle reti virtuali classiche. Si dispone di uno script che consenta di completare le attività seguenti:

- Creare una rete virtuale Manager delle risorse e integrare l'app.
- Creare un gateway, configurare la connettività punto al sito in una rete virtuale Manager delle risorse preesistenti e quindi integrare l'app.
- Integrare l'app con una rete virtuale preesistenti Manager delle risorse che ha un gateway e connettività abilitato punto al sito.
- Scollegare l'app rete virtuale.

### <a name="resource-manager-vnet-app-service-integration-script"></a>Script di integrazione di Manager delle risorse VNet App Service ###

Copiare il seguente script e salvarlo in un file. Se non si desidera utilizzare lo script, è possibile imparare per informazioni su come configurare l'ambiente con una rete virtuale Manager delle risorse.


    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
          else
        {
          Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

Salvare una copia dello script. In questo articolo viene chiamato V2VnetAllinOne.ps1, ma è possibile usare un altro nome. Non sono presenti argomenti per questo script. Sufficiente eseguirlo. La prima cosa che produrrà lo script viene chiesto di accedere. Dopo l'accesso, lo script ottiene i dettagli sull'account e restituisce un elenco delle sottoscrizioni. Senza contare la richiesta per le credenziali, l'esecuzione di script iniziale è simile alla seguente:

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demo sottoscrizione (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) Test MS (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Demo viola sottoscrizione (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Scegliere un'opzione: 3

    Account: ccompy@microsoft.com ambiente: AzureCloud abbonamento: Tenant 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d: 722278f-fef1-499f-91ab-2323d011db47

    Immettere il gruppo di risorse dell'App: hcdemo rg immettere il nome dell'App: v2vnetpowershell cosa si desidera eseguire?

    1) Aggiungere una nuova rete virtuale a un'App
    2) Aggiungere una rete virtuale esistente a un'App
    3) Rimuovere una rete virtuale da un'App

Il resto di questa sezione sono descritte le tre opzioni.

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Creare un Manager delle risorse VNet e integrare usarli ###

Per creare una nuova rete virtuale che usa il modello di distribuzione di Manager delle risorse e integrazione con l'app, selezionare **1) aggiungere una nuova rete virtuale a un'App**. Viene chiesto per il nome della rete virtuale. In questo caso, come è possibile visualizzare le impostazioni seguenti, usato il nome, v2pshell.

Lo script fornisce informazioni dettagliate sulla rete virtuale che si sta creando. Se desidera, posso modificare uno o più valori. In esecuzione in questo esempio, creata una rete virtuale che contiene le impostazioni seguenti:

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

Se si desidera modificare uno o più valori, digitare **s** e apportare le modifiche. Quando si è soddisfatti con le impostazioni di rete virtuale, digitare **N** o premere semplicemente INVIO quando viene chiesto di modificare le impostazioni. Da tale posizione sulla fino al completamento, lo script che indica alcune delle quali it' contenenti eseguendo fino a quando non viene avviata la creazione del gateway virtuali. Passaggio può richiedere fino a un'ora. Non c'è alcun indicatore di stato in questa fase, ma lo script consente di sapere quando è stato creato il gateway.

Al termine dell'esecuzione dello script, indicato sarà **completato**. A questo punto, si verificherà una rete virtuale Manager delle risorse che contiene il nome e le impostazioni che è stata selezionata. La nuova rete virtuale inoltre essere integrata con l'app.

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>Integrare l'app con un preesistenti Manager delle risorse VNet ###

Quando sta integrazione con una rete virtuale preesistente, se si specifica una rete virtuale Manager delle risorse che non dispone di un gateway o connettività punto al sito, lo script configurerà che. Se il VNET ha già tali operazioni di configurazione, lo script passa direttamente l'integrazione di app. Per avviare il processo, è sufficiente selezionare **2) aggiungere una rete virtuale esistente a un'App**.

Questa opzione funziona solo se si dispone di una rete virtuale preesistenti Manager delle risorse che è nello stesso abbonamento l'app. Dopo aver selezionato l'opzione, verrà visualizzato un elenco delle proprie reti virtuale Manager delle risorse.   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Scegliere un'opzione: 5

È sufficiente selezionare la rete virtuale che si vuole integrare con. Se si dispone già di un gateway che dispone di connettività al sito abilitata, lo script verrà semplicemente integrare l'app con la rete virtuale. Se non si dispone di un gateway, sarà necessario specificare subnet gateway. Subnet gateway deve essere lo spazio degli indirizzi virtuali e non potrà essere in qualsiasi altra subnet. Se si dispone di una rete virtuale senza un gateway ed eseguire questo passaggio, aspetti simile al seguente:

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

In questo esempio creato un gateway di rete virtuale che contiene le impostazioni seguenti:

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

Se si desidera modificare le impostazioni, è possibile eseguire questa operazione. In caso contrario, premere INVIO e lo script verrà creare il gateway e allegare l'app alla rete virtuale. L'ora di creazione di gateway è ancora un'ora, tuttavia, dunque, verificare che si tenere presenti. Al termine tutti gli elementi, lo script leggerà **completato**.

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>Scollegare l'app da un Manager delle risorse VNet ###

Disconnessione l'app da rete virtuale non accetta verso il basso il gateway e disabilitare la connettività punto al sito. Si potrebbe, dopo tutto, utilizzare il che si stava cercando. Anche non la disconnessione da altre applicazioni diverso da quello specificato. Per eseguire questa operazione, selezionare **3) rimuovere una rete virtuale da un'App**. Quando si esegue questa operazione, si vedrà simile al seguente:

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Anche se lo script dice Elimina non elimina la rete virtuale. Che è sufficiente rimuovere l'integrazione. Dopo avere verificato che questo sia che si vuole eseguire, il comando viene elaborato molto rapidamente e indica **True** al termine.

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com
