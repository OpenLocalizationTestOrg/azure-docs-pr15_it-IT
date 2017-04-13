<properties 
   pageTitle="Come spostare un'istanza di macchine Virtuali o ruolo ricoperto dai partecipanti a una subnet diversa"
   description="Informazioni su come spostare macchine virtuali e istanze del ruolo a un'altra subnet"
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

# <a name="how-to-move-a-vm-or-role-instance-to-a-different-subnet"></a>Come spostare un'istanza di macchine Virtuali o ruolo ricoperto dai partecipanti a una subnet diversa

È possibile usare PowerShell per spostare le macchine virtuali da una subnet a altra nella stessa rete virtuale (VNet). Modifica il CSCFG, anziché con PowerShell, è possono spostare istanze del ruolo.

>[AZURE.NOTE] In questo articolo contiene informazioni che sono relativo solo per distribuzioni classiche Azure.

Perché spostare macchine virtuali di un'altra subnet? Migrazione subnet è utile quando subnet meno recenti è troppo piccola e non possono essere espansi a causa di esistente macchine virtuali in esecuzione in tale subnet. In questo caso, è possibile creare una nuova subnet ed eseguire la migrazione di macchine virtuali per la nuova subnet, quindi una volta completata la migrazione, è possibile eliminare il vecchia subnet vuota.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Come spostare una macchina virtuale a un'altra subnet

Per spostare una macchina virtuale, eseguire il cmdlet Set-AzureSubnet PowerShell, utilizzando l'esempio riportato di seguito come modello. Nell'esempio seguente, stiamo passando TestVM dalla propria subnet presenta alla Subnet 2. Assicurarsi di modificare l'esempio in modo da riflettere l'ambiente. Si noti che ogni volta che si esegue il cmdlet di aggiornamento AzureVM come parte di una procedura, verrà riavviato la macchina virtuale come parte del processo di aggiornamento.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
  	| Set-AzureSubnet –SubnetNames Subnet-2 `
  	| Update-AzureVM

Se è stato specificato un indirizzo IP privato interno statico per la macchina virtuale, è necessario deselezionare questa impostazione, prima che è possibile spostare la macchina virtuale in una nuova subnet. In questo caso, utilizzare le operazioni seguenti:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Remove-AzureStaticVNetIP `
  	| Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
  	| Set-AzureSubnet -SubnetNames Subnet-2 `
  	| Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Per spostare un'istanza di ruoli in un'altra subnet

Per spostare un'istanza del ruolo, modificare il file CSCFG. Nell'esempio seguente, stiamo passando "Role0" in virtuali *VNETName* dalla propria subnet presenta al *Subnet 2*. Perché l'istanza di ruolo è già stato distribuito, è necessario modificare solo il nome Subnet = Subnet 2. Assicurarsi di esempio per riflettere l'ambiente edit.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
