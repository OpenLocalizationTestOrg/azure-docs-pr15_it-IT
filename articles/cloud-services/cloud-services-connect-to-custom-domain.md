<properties
  pageTitle="Connettere un Controller di dominio personalizzato di un servizio Cloud | Microsoft Azure"
  description="Informazioni su come connettere i ruoli di web/lavoro a un dominio di Active Directory personalizzato tramite PowerShell e un dominio interno"
  services="cloud-services"
  documentationCenter=""
  authors="Thraka"
  manager="timlt"
  editor=""/>

  <tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="adegeo"/>

# <a name="connecting-azure-cloud-services-roles-to-a-custom-ad-domain-controller-hosted-in-azure"></a>Ruoli di servizi Cloud Azure connessione a un Controller di dominio Active Directory ospitato in Azure personalizzato

È innanzitutto configurerà una rete virtuale (VNet) in Azure. Quindi verrà aggiunto un Controller di dominio Active Directory (ospitato una macchina virtuale Azure) per la VNet. Successivamente, verrà aggiungere VNet già creati i ruoli di servizio cloud esistenti e successivamente connetterle al Controller di dominio.

Prima di iniziare, alcuni aspetti da tenere presenti:

1.  In questa esercitazione utilizza PowerShell, quindi effettuare verificare di che avere installato PowerShell di Azure e pronto per l'invio. Per ottenere assistenza per la configurazione Azure PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

2.  Istanze del Controller di dominio Active Directory e ruolo Web/lavoro devono essere nel VNet.

Seguire questa procedura dettagliata e se si verificano problemi, inviarci un commento riportata di seguito. Un utente verrà inviata all'utente (Sì, è leggere commenti).

3. La rete a cui fa riferimento nel cloud service <mark>deve essere</mark> una **rete virtuale classica**.

## <a name="create-a-virtual-network"></a>Creare una rete virtuale

È possibile creare una rete virtuale in Azure tramite il portale classica Azure o PowerShell. Per questa esercitazione verranno utilizzati PowerShell. Per creare una rete virtuale tramite il portale classico Azure, vedere [Creare virtuali](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

```powershell
#Create Virtual Network

$vnetStr =
@"<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
    <VirtualNetworkConfiguration>
    <VirtualNetworkSites>
        <VirtualNetworkSite name="[your-vnet-name]" Location="West US">
        <AddressSpace>
            <AddressPrefix>[your-address-prefix]</AddressPrefix>
        </AddressSpace>
        <Subnets>
            <Subnet name="[your-subnet-name]">
            <AddressPrefix>[your-subnet-range]</AddressPrefix>
            </Subnet>
        </Subnets>
        </VirtualNetworkSite>
    </VirtualNetworkSites>
    </VirtualNetworkConfiguration>
</NetworkConfiguration>
"@;

$vnetConfigPath = "<path-to-vnet-config>"
Set-AzureVNetConfig -ConfigurationPath $vnetConfigPath
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Dopo aver completato la configurazione della rete virtuale, sarà necessario creare un Controller di dominio Active Directory. Per questa esercitazione, si verrà impostazione di un Controller di dominio Active Directory una macchina virtuale Azure.

A tale scopo, creare una macchina virtuale tramite PowerShell usando i comandi seguenti:

```powershell
# Initialize variables
# VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.

$vnetname = '<your-vnet-name>'
$subnetname = '<your-subnet-name>'
$vmsvc1 = '<your-hosted-service>'
$vm1 = '<your-vm-name>'
$username = '<your-username>'
$password = '<your-password>'
$affgrp = '<your- affgrp>'

# Create a VM and add it to the Virtual Network

New-AzureQuickVM -Windows -ServiceName $vmsvc1 -Name $vm1 -ImageName $imgname -AdminUsername $username -Password $password -AffinityGroup $affgrp -SubnetNames $subnetname -VNetName $vnetname
```

## <a name="promote-your-virtual-machine-to-a-domain-controller"></a>Alzare di livello la macchina virtuale Controller di dominio
Per configurare la macchina virtuale come un Controller di dominio Active Directory, è necessario accedere a macchina virtuale e configurarla.

Per accedere a macchina virtuale, è possibile ottenere il file RDP tramite PowerShell, usare i comandi seguenti.

```powershell
# Get RDP file
Get-AzureRemoteDesktopFile -ServiceName $vmsvc1 -Name $vm1 -LocalPath <rdp-file-path>
```

Una volta che si sta effettuando la macchina virtuale, configurazione macchina virtuale come un Controller di dominio Active Directory seguendo la procedura dettagliata su [come configurare il cliente Controller di dominio Active Directory](http://social.technet.microsoft.com/wiki/contents/articles/12370.windows-server-2012-set-up-your-first-domain-controller-step-by-step.aspx).

## <a name="add-your-cloud-service-to-the-virtual-network"></a>Aggiungere il servizio Cloud per la rete virtuale

È necessario aggiungere alla distribuzione di servizio cloud VNet appena creato. A tale scopo, modificare il cscfg servizio cloud aggiungendo le sezioni pertinenti per il cscfg utilizzando Visual Studio o l'editor di propria scelta.

```xml
<ServiceConfiguration serviceName="[hosted-service-name]" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="[os-family]" osVersion="*">
    <Role name="[role-name]">
    <Instances count="[number-of-instances]" />
    </Role>
    <NetworkConfiguration>

    <!--optional-->
    <Dns>
        <DnsServers><DnsServer name="[dns-server-name]" IPAddress="[ip-address]" /></DnsServers>
    </Dns>
    <!--optional-->

    <!--VNet settings
        VNet and subnet must be classic virtual network resources, not Azure Resource Manager resources.-->
    <VirtualNetworkSite name="[virtual-network-name]" />
    <AddressAssignments>
        <InstanceAddress roleName="[role-name]">
        <Subnets>
            <Subnet name="[subnet-name]" />
        </Subnets>
        </InstanceAddress>
    </AddressAssignments>
    <!--VNet settings-->

    </NetworkConfiguration>
</ServiceConfiguration>
```

Quindi compilare il progetto di servizi cloud e distribuirlo in Azure. Per visualizzare la Guida alla distribuzione del pacchetto di servizi cloud in Azure, informazioni su [come creare e distribuire un servizio Cloud](cloud-services-how-to-create-deploy.md#deploy)

## <a name="connect-your-webworker-roles-to-the-domain"></a>Connettere il dominio i ruoli web/lavoro

Dopo il progetto di servizio cloud viene distribuito in Azure, collegare le istanze del ruolo per il dominio personalizzato utilizzando l'estensione di dominio Active Directory. Per aggiungere l'estensione di dominio Active Directory per la distribuzione di servizi cloud esistente e aggiungere il dominio personalizzato, eseguire i comandi seguenti PowerShell:

```powershell
# Initialize domain variables

$domain = '<your-domain-name>'
$dmuser = '$domain\<your-username>'
$dmpswd = '<your-domain-password>'
$dmspwd = ConvertTo-SecureString $dmpswd -AsPlainText -Force
$dmcred = New-Object System.Management.Automation.PSCredential ($dmuser, $dmspwd)

# Add AD Domain Extension to the cloud service roles

Set-AzureServiceADDomainExtension -Service <your-cloud-service-hosted-service-name> -Role <your-role-name> -Slot <staging-or-production> -DomainName $domain -Credential $dmcred -JoinOption 35
```

E questo è tutto.

Cloud services ora devono essere aggiunti a controller di dominio personalizzato. Se si desidera ottenere ulteriori informazioni su diverse opzioni disponibili per la procedura configurare le estensioni di dominio Active Directory, utilizzare la Guida di PowerShell, come illustrato di seguito.

```powershell
help Set-AzureServiceADDomainExtension
help New-AzureServiceADDomainExtensionConfig
```
