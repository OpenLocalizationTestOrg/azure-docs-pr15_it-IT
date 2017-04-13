<properties
   pageTitle="Riservato IP | Microsoft Azure"
   description="Informazioni su indirizzi IP riservati e come possono essere gestiti"
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

# <a name="reserved-ip-overview"></a>Panoramica IP riservato
Indirizzi IP in Azure rientrano in due categorie: dinamico e riservato. Indirizzi IP gestiti da Azure sono dinamici per impostazione predefinita. Questo significa che l'indirizzo IP utilizzato da un servizio cloud specificato (VIP) o per accedere a un ruolo o macchine Virtuali istanza direttamente (ILPIP) può cambiare nel tempo, quando le risorse sono arresto o rilasciato.

Per impedire la modifica di indirizzi IP, è possibile prenotare un indirizzo IP. Indirizzi IP riservato può essere utilizzato solo come un VIP assicurare che l'indirizzo IP del servizio cloud saranno gli stessi anche quando le risorse sono arresto o rilasciato. Inoltre, è possibile convertire IP dinamico esistente utilizzato come un VIP a un indirizzo IP riservato.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Informazioni su come prenotare un indirizzo IP pubblico statico utilizzando il [modello di distribuzione di Manager delle risorse](virtual-network-ip-addresses-overview-arm.md).

Assicurarsi di comprendere il funzionamento di [indirizzi IP](virtual-network-ip-addresses-overview-classic.md) in Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Quando è necessario un indirizzo IP riservato?
- **Per assicurarsi che l'indirizzo IP riservato nell'abbonamento**. Se si desidera prenotare un indirizzo IP che non viene rilasciato dall'abbonamento in nessun caso, è necessario utilizzare un indirizzo IP pubblico riservato.  
- **Si vuole che l'indirizzo IP di rimanere con il servizio cloud anche in stato di arresto o deallocato (macchine virtuali)**. Se si desidera il servizio a cui accedere tramite un indirizzo IP che non verrà modificate anche quando macchine virtuali nel servizio cloud siano Interrompi o deallocato.
- **Per assicurarsi che il traffico in uscita da Azure utilizza un indirizzo IP prevedibile**. Si dispone del firewall locale è configurato per consentire solo il traffico da indirizzi IP specifici. Per la prenotazione di un indirizzo IP, si saprà indirizzo IP di origine e non è necessario aggiornare le regole firewall a causa di una modifica apportata IP.

## <a name="faq"></a>Domande frequenti
1. È possibile usare un indirizzo IP riservato per tutti i servizi Azure?  
  - IP riservato può essere usato solo per macchine virtuali e ruoli istanza del servizio cloud esposti tramite un indirizzo VIP.
1. Quanti indirizzi IP riservati possono aggiungere?  
  - In questa fase, tutte le sottoscrizioni Azure sono autorizzate a utilizzare 20 IP riservato. Tuttavia, è possibile richiedere altri indirizzi IP riservato. Vedere la pagina [sottoscrizione e limiti di servizio](../azure-subscription-service-limits.md) per ulteriori informazioni.
1. È disponibile un costo per IP riservato?
  - Per informazioni sui prezzi, vedere [Dettagli prezzi dell'indirizzo IP riservato](http://go.microsoft.com/fwlink/?LinkID=398482) .
1. Come è prenotare un indirizzo IP?
  - È possibile utilizzare PowerShell o l' [API REST di Azure Management](https://msdn.microsoft.com/library/azure/dn722420.aspx) per prenotare un indirizzo IP in un'area specifica. Questo indirizzo IP riservato sarà associato all'abbonamento. Non è possibile prenotare un indirizzo IP tramite il portale di gestione.
1. È possibile usare questo con gruppo affinità in base a VNets?
  - Indirizzi IP riservati sono supportati solo in VNets internazionali. Non è supportato per VNets è associato ai gruppi di affinità. Per ulteriori informazioni sull'associazione di un VNet con un'area o un gruppo di affinità, vedere [sulle internazionali VNets e affinità gruppi](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Come gestire VIP riservato

Prima di poter usare IP riservato, è necessario aggiungere all'abbonamento. Per creare un indirizzo IP riservato dal pool di indirizzi IP disponibili nella posizione *Centrale USA* , eseguire il comando PowerShell seguente:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Tuttavia, non è possibile specificare IP è riservato. Per visualizzare gli indirizzi IP prenotati nell'abbonamento, eseguire il seguente comando di PowerShell e osservare i valori per *ReservedIPName* e *indirizzo*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Una volta un indirizzo IP riservato, rimane associato all'abbonamento finché non viene eliminato. Per eliminare l'indirizzo IP riservato illustrato sopra, eseguire il comando PowerShell seguente:

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Come riservare l'indirizzo IP di un servizio cloud esistente

È possibile prenotare l'indirizzo IP di un servizio cloud esistente aggiungendo il parametro *- Nome* . Per prenotare l'indirizzo IP di un servizio cloud *TestService* nella posizione *Centrale USA* , eseguire il comando PowerShell seguente:

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Come associare un indirizzo IP riservato a un nuovo servizio cloud
Script riportato di seguito viene creato un nuovo indirizzo IP riservato, quindi viene associata a un nuovo servizio cloud denominato *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Quando si crea un indirizzo IP riservato da utilizzare con un servizio cloud, è comunque necessario fare riferimento a macchina virtuale utilizzando *VIP:&lt;il numero di porta >* per le comunicazioni in ingresso. La prenotazione di un indirizzo IP non significa che è possibile connettersi direttamente la macchina virtuale. L'indirizzo IP riservato viene assegnato al servizio cloud che la macchina virtuale è stata distribuita in. Se si desidera connettersi a una macchina virtuale da IP direttamente, è necessario configurare un indirizzo IP pubblico a livello di istanza. Un indirizzo IP pubblico a livello di istanza è un tipo di IP pubblico (noto come un ILPIP) che sono state assegnate direttamente la macchina virtuale. Non può essere riservato. Per ulteriori informazioni, vedere [Livello di istanza pubblico IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Come rimuovere un indirizzo IP riservato da una distribuzione in esecuzione
Per rimuovere l'indirizzo IP riservato aggiunto al nuovo servizio creato nello script precedente, eseguire il comando PowerShell seguente:

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Rimozione di un indirizzo IP riservato da una distribuzione in esecuzione non rimuove la prenotazione dall'abbonamento. Libera semplicemente IP che verrà utilizzato da un'altra risorsa nell'abbonamento.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Come associare un indirizzo IP riservato a una distribuzione in esecuzione
Script riportato di seguito viene creato un nuovo servizio cloud denominato *TestService2* con una nuova macchina virtuale denominata *TestVM2*e associa l'indirizzo IP riservato esistente denominato *MyReservedIP* al servizio cloud.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Come associare un indirizzo IP riservati in un servizio cloud usando un file di configurazione del servizio
È inoltre possibile associare un indirizzo IP riservati in un servizio cloud usando un file di configurazione (CSCFG) del servizio. il xml di esempio riportato di seguito viene illustrato come configurare un servizio cloud per usare un VIP riservato denominato *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Passaggi successivi

- Comprendere il funzionamento [gli indirizzi IP](virtual-network-ip-addresses-overview-classic.md) nel modello di distribuzione classica.

- Le informazioni [riservati indirizzi IP privati](virtual-networks-reserved-private-ip.md).

- Informazioni sugli [indirizzi istanza livello pubblico IP (ILPIP)](virtual-networks-instance-level-public-ip.md).
