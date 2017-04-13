<properties 
   pageTitle="Come impostare un indirizzo IP privato interno statico"
   description="Informazioni sulle IP interno statico (DIP) e come possono essere gestiti"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/22/2016"
   ms.author="jdial" />

# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Come impostare un interno privato indirizzo IP tramite PowerShell (classica)
Nella maggior parte dei casi, non sarà necessario specificare un indirizzo IP statico interno per la macchina virtuale. Macchine virtuali in una rete virtuale riceverà automaticamente un indirizzo IP interno da un intervallo specificato. Ma in alcuni casi, specificando un indirizzo IP statico per una macchina virtuale particolare senso. Ad esempio, se la macchina virtuale deve eseguire DNS o sarà un controller di dominio. Un indirizzo IP interno viene mantenuto macchine Virtuali anche tramite uno stato Interrompi/annullare l'implementazione. 

> [AZURE.IMPORTANT] Azure include due diversi modelli di distribuzione per la creazione e utilizzo delle risorse: [Gestione risorse e classica](../resource-manager-deployment-model.md). In questo articolo è descritta utilizzando il modello di distribuzione classica. Si consiglia di più nuove distribuzioni di utilizzare il [modello di distribuzione di Manager delle risorse](virtual-networks-static-private-ip-arm-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Come verificare se è disponibile un indirizzo IP specifico
Per verificare se l' indirizzo IP *10.0.0.7* è disponibile in un vnet denominato *TestVnet*, eseguire il seguente comando di PowerShell e verificare che il valore per *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

>[AZURE.NOTE] Se si desidera verificare il comando sopra in un ambiente sicuro segui le istruzioni in [creare una rete virtuale](virtual-networks-create-vnet-classic-portal.md) per creare un vnet denominato *TestVnet* e verificare che venga utilizzato lo spazio di indirizzi *10.0.0.0/8* .

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Come specificare un indirizzo IP interno statico durante la creazione di una macchina virtuale
Script di PowerShell riportato di seguito viene creato un nuovo servizio cloud denominato *TestService*, quindi recupera un'immagine da Azure, quindi crea una macchina virtuale denominata *TestVM* nel nuovo servizio cloud utilizzando l'immagine recuperato, imposta la macchina virtuale in una subnet denominata *Subnet 1*e *10.0.0.7* come un indirizzo IP interno statico per la macchina virtuale:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzureSubnet –SubnetNames Subnet-1 `
  	| Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
  	| New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Come recuperare informazioni IP statiche interne per una macchina virtuale
Per visualizzare le informazioni IP interne statiche per macchine Virtuali creati con lo script precedente, eseguire il seguente comando di PowerShell e osservare i valori per *indirizzo IP*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Come rimuovere un indirizzo IP interno statico da una macchina virtuale
Per rimuovere IP interno statico aggiunto a macchine Virtuali nello script precedente, eseguire il comando PowerShell seguente:
    
    Get-AzureVM -ServiceName TestService -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Come aggiungere un indirizzo IP interno statico a una macchina virtuale esistente
Per aggiungere una statica interna IP per la macchina virtuale creati con lo script sopra Run il seguente comando:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
  	| Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
  	| Update-AzureVM

## <a name="next-steps"></a>Passaggi successivi

[IP riservato](virtual-networks-reserved-public-ip.md)

[Livello di istanza pubblico IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[API REST IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx)
 
