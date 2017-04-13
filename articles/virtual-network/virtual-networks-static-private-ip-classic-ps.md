<properties 
   pageTitle="Come impostare un indirizzo IP privato statico in modalità classica tramite PowerShell | Microsoft Azure"
   description="Informazioni sulle IP privato statico (DIP) e come possono essere gestiti in modalità classica e PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-private-ip-address-classic-in-powershell"></a>Come impostare un indirizzo IP statico privato (classica) PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione classica. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione Manager delle risorse](virtual-networks-static-private-ip-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

I comandi di PowerShell di esempio seguenti si aspettano un ambiente semplice già stato creato. Se si desidera eseguire i comandi così come sono visualizzati nel documento, creare innanzitutto l'ambiente di testing descritto in [creare un VNet](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Come verificare se è disponibile un indirizzo IP specifico
Per verificare se l' indirizzo IP *192.168.1.101* è disponibile in un VNet denominato *TestVNet*, eseguire il seguente comando di PowerShell e verificare che il valore per *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Output previsto:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale
Script di PowerShell riportato di seguito viene creato un nuovo servizio cloud denominato *TestService*, quindi recupera un'immagine da Azure, crea una macchina virtuale denominata *DNS01* nel nuovo servizio cloud utilizzando l'immagine recuperato, imposta la macchina virtuale in una subnet denominata *front-end*e *192.168.1.7* come un indirizzo IP statico privato per la macchina virtuale:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Output previsto:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Come recuperare statico privato indirizzo IP per una macchina virtuale
Per visualizzare le informazioni di indirizzo IP private statiche per macchine Virtuali creati con lo script precedente, eseguire il seguente comando di PowerShell e osservare i valori per *indirizzo IP*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Output previsto:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP statico privato aggiunto al macchine Virtuali di script riportati sopra, eseguire il comando PowerShell seguente:
    
    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Output previsto:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un statico privato indirizzo IP in macchine Virtuali creati con lo script sopra Run il seguente comando:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Output previsto:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sugli indirizzi [IP pubblico riservato](virtual-networks-reserved-public-ip.md) .
- Informazioni sugli indirizzi [IP pubblico (ILPIP) a livello di istanza](virtual-networks-instance-level-public-ip.md) .
- Consultare le [API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).
