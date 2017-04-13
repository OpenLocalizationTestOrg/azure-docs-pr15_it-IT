<properties 
   pageTitle="Istanza livello IP pubblico (ILPIP) | Microsoft Azure"
   description="Informazioni sulle ILPIP (PIP) e come possono essere gestiti"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="instance-level-public-ip-overview"></a>Panoramica IP pubblico livello di istanza
Un'istanza livello IP pubblico (ILPIP) è un indirizzo IP pubblico che è possibile assegnare direttamente all'istanza di macchine Virtuali o ruolo, anziché al servizio cloud appartenenti all'istanza macchine Virtuali o ruolo. Questa operazione non viene eseguita dell'indirizzo VIP (virtual IP) che è assegnata al servizio cloud. È invece un indirizzo IP aggiuntivo che è possibile utilizzare per connettersi direttamente all'istanza macchine Virtuali o ruolo.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come [eseguire questa procedura utilizzando il modello di Manager delle risorse](virtual-network-ip-addresses-overview-arm.md). 

Assicurarsi di comprendere il funzionamento di [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

>[AZURE.NOTE] In passato, un ILPIP è stata definita una PIP acronimo IP pubblico. 

![Differenza tra ILPIP e VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Come mostrato nella figura 1, il servizio cloud è possibile accedere tramite un VIP, mentre le singole macchine virtuali in genere è possibile accedere tramite VIP:&lt;il numero di porta&gt;. Assegnando un ILPIP a una macchina virtuale specifica, che è possono accedere macchine Virtuali direttamente utilizzando l'indirizzo IP.

Quando si crea un servizio cloud in Azure, i record DNS A corrispondenti vengono creati automaticamente per consentire l'accesso al servizio tramite un nome di dominio completo (FQDN) invece di usare l'indirizzo VIP effettivo. Lo stesso processo di verifica per ILPIP, che consente l'accesso all'istanza macchine Virtuali o ruolo da FQDN anziché il ILPIP. Ad esempio, se si crea un servizio cloud denominato *contosoadservice*e si configura un ruolo web denominato *contosoweb* con due istanze, Azure verrà registrato le operazioni seguenti per le istanze di record:

- contosoweb\_IN_0.contosoadservice.cloudapp.net
- contosoweb\_IN_1.contosoadservice.cloudapp.net 

>[AZURE.NOTE] È possibile assegnare un solo ILPIP per ogni istanza macchine Virtuali o ruolo. È possibile utilizzare un massimo di 5 ILPIP per abbonamento. In questo momento ILPIP non è supportata per le macchine virtuali multi-NIC.

## <a name="why-should-i-request-an-ilpip"></a>Perché è opportuno richiedere un ILPIP?
Se si desidera essere in grado di connettersi all'istanza macchine Virtuali o ruolo da assegnato direttamente a un indirizzo IP, invece di usare il cloud service VIP:&lt;il numero di porta&gt;, richiedere un ILPIP per le macchine Virtuali o l'istanza del ruolo.
- **FTP passivo** - concedendo un ILPIP alle macchine Virtuali, è possibile ricevere il traffico su qualsiasi porta non è necessario aprire un endpoint a cui inviare il traffico. In questo modo scenari come FTP passivo nel punto in cui le porte ne vengono scelti in modo dinamico.
- **IP in uscita** : il traffico in uscita provenienti da macchina virtuale in uscita con ILPIP come origine e identifica in modo univoco macchine Virtuali a entità esterne.

## <a name="how-to-request-an-ilpip-during-vm-creation"></a>Come richiedere un ILPIP durante la creazione di macchine Virtuali
Script di PowerShell riportato di seguito viene creato un nuovo servizio cloud denominato *FTPService*, quindi recupera un'immagine da Azure e crea una macchina virtuale denominata *FTPInstance* utilizzando l'immagine recuperato imposta macchine Virtuali di utilizzare un ILPIP e aggiunge la macchina virtuale al nuovo servizio:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Come recuperare le informazioni ILPIP per una macchina virtuale
Per visualizzare le informazioni ILPIP per macchine Virtuali creati con lo script precedente, eseguire il seguente comando di PowerShell e osservare i valori per *PublicIPAddress* e *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## <a name="how-to-remove-an-ilpip-from-a-vm"></a>Come rimuovere un ILPIP da una macchina virtuale
Per rimuovere ILPIP aggiunto a macchine Virtuali nello script precedente, eseguire il comando PowerShell seguente:
    
    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Remove-AzurePublicIP `
  	| Update-AzureVM

## <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Come aggiungere un ILPIP a una macchina virtuale esistente
Per aggiungere un ILPIP a macchine Virtuali creati con lo script precedente, eseguire il comando seguente:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
  	| Set-AzurePublicIP -PublicIPName ftpip2 `
  	| Update-AzureVM

## <a name="how-to-associate-an-ilpip-to-a-vm-by-using-a-service-configuration-file"></a>Come associare un ILPIP a una macchina virtuale tramite un file di configurazione del servizio
È inoltre possibile associare un ILPIP a una macchina virtuale tramite un file di configurazione (CSCFG) del servizio. il xml di esempio riportato di seguito viene illustrato come configurare un servizio cloud per utilizzare un ILPIP denominato *MyPublicIP* per un'istanza di ruolo: 
    
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Passaggi successivi

- Comprendere il funzionamento [gli indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.

- Le informazioni [riservate IP](virtual-networks-reserved-public-ip.md).
 