<properties 
   pageTitle="Interfacce di rete | Microsoft Azure"
   description="Informazioni sulle interfacce di rete Azure in Gestione risorse di Azure."
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="jdial" />

# <a name="network-interfaces"></a>Interfacce di rete

Un'interfaccia di rete (NIC) è interconnessione tra un virtuale macchina e di rete software sottostante. In questo articolo spiega quali un'interfaccia di rete è e come utilizzarlo nel modello di distribuzione Manager delle risorse di Azure.

Si consiglia di distribuzione di nuove risorse utilizzando il modello di distribuzione di Manager delle risorse, ma è anche possibile distribuire macchine virtuali con la connettività di rete nel modello di distribuzione [classica](virtual-network-ip-addresses-overview-classic.md) . Se si ha familiarità con il modello classico, esistono differenze importanti in rete macchine Virtuali nel modello di distribuzione Manager delle risorse. Altre informazioni sulle differenze, leggere l'articolo [macchina virtuale rete - classica](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments) .

In Azure, un'interfaccia di rete:

1. Una risorsa che è possibile creare, eliminare, con le proprie impostazioni configurabili.
2. Deve essere connesso a una subnet in una rete virtuale Azure (VNet) quando viene creato. Se non si ha familiarità con VNets, ulteriori informazioni, leggere l'articolo [informazioni generali sui rete virtuale](virtual-networks-overview.md) . La scheda di rete deve essere connesso a un VNet che è presente nella stessa [posizione](https://azure.microsoft.com/regions) di Azure e [abbonamento](../azure-glossary-cloud-terminology.md#subscription) come scheda di rete. Dopo la creazione di una scheda di rete, è possibile modificare subnet a che sia connesso, ma non è possibile modificare VNet sia connesso a.
3. È assegnato un nome che non può essere modificato dopo aver creato la scheda di rete. Il nome deve essere univoco all'interno di un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups)di Azure, ma non deve essere univoco all'interno della sottoscrizione, la posizione Azure in che viene creato o VNet sia connesso a. NIC diversi vengono in genere create all'interno di un abbonamento a Azure. Si consiglia di definire una convenzione di denominazione che facilita la gestione di più schede di rete più semplice che utilizzando nomi predefiniti. Vedere l'articolo [consigliati convenzioni di denominazione per le risorse Azure](../guidance/guidance-naming-conventions.md) per i suggerimenti.
4. Può essere collegato a una macchina virtuale, ma può essere associato solo a una singola macchina virtuale che è presente nello stesso percorso di rete.
5. Ha un indirizzo MAC, viene mantenuto con la scheda di rete per come rimane collegato a una macchina virtuale. L'indirizzo MAC viene mantenuta se si riavvia la macchina virtuale (dall'interno del sistema operativo) o arrestato (deselezionare allocato) iniziare a usare il portale di Azure, PowerShell Azure o la riga di comando di Azure. Se è scollegato da una macchina virtuale ed associati a una macchina virtuale diversa, la scheda di rete riceve un indirizzo MAC diverso. Se la scheda di rete viene eliminato, l'indirizzo MAC verrà assegnata altre schede.
6. Deve avere una primaria **privato** *IPv4* statico o dinamico indirizzo IP assegnato.
8. Può avere una risorsa di indirizzo IP pubblica ad esso associati.
9. Supporta accelerata networking con la virtualizzazione dei / o di radice singola (SR IOV) per dimensioni specifiche di macchine Virtuali in esecuzione versioni specifiche del sistema operativo Microsoft Windows Server. Per ulteriori informazioni su questa caratteristica di anteprima, leggere l'articolo [Accelerated di rete per una macchina virtuale](virtual-network-accelerated-networking-powershell.md) .
10. Grado di ricevere il traffico non destinato a indirizzi IP privati assegnati se inoltro IP è abilitata per la scheda di rete. Se una macchina virtuale è in esecuzione il software firewall, ad esempio, instrada i pacchetti non destinati indirizzo IP. La macchina virtuale deve comunque eseguire software in grado di inoltro del traffico, ma a tale scopo, inoltro IP deve essere abilitata per una scheda.
11. Viene spesso creata nello stesso gruppo di risorse come macchine Virtuali è collegato alle o VNet stesso che sia connesso a, anche se non è necessario essere.

## <a name="vms-with-multiple-network-interfaces"></a>Macchine virtuali con più interfacce di rete

Più schede di rete può essere collegati a una macchina virtuale ma in questo modo, tenere presente quanto segue:  

- Dimensione memoria virtuale deve supportare più schede di rete. Per maggiori informazioni sulle dimensioni macchine Virtuali supportano più schede di rete, leggere gli articoli [macchine Virtuali di Windows Server dimensioni](../virtual-machines/virtual-machines-windows-sizes.md) o [le dimensioni di macchine Virtuali Linux](../virtual-machines/virtual-machines-linux-sizes.md) .   
- Con almeno due schede di rete, è necessario creare la macchina virtuale. Se la macchina virtuale viene creata con un solo NIC, anche se le dimensioni di macchine Virtuali supporta più monitor, è possibile allegare NIC aggiuntive per la macchina virtuale dopo averlo creato. Come la macchina virtuale è stata creata con almeno due schede di rete, è possibile allegare NIC aggiuntive per la macchina virtuale dopo averlo creato, come la dimensione di memoria virtuale supporta più di due schede di rete.  
- È possibile disconnettersi NIC secondaria (NIC primaria non può essere disconnesso) da una macchina virtuale se la macchina virtuale ha almeno tre schede allegate. Non è possibile scollegare NIC se la macchina virtuale include due o meno NIC collegato.  
- L'ordine delle schede dall'interno della macchina virtuale sarà casuale e può inoltre modificare in Azure infrastruttura aggiornamenti. Tuttavia, gli indirizzi IP ed ethernet corrispondente MAC indirizzi, rimarrà invariato. Si supponga ad esempio che il sistema operativo identifica Azure NIC1 come Eth1. Indirizzo IP 10.1.0.100 e indirizzo MAC 00-0D-3A-B0-39-0D Eth1. Dopo un'infrastruttura di Azure aggiornare e riavviare il computer, il sistema operativo a questo punto potrebbe identificare Azure NIC1 come Eth2, ma gli indirizzi IP e MAC saranno gli stessi come avveniva quando il sistema operativo identificato Azure NIC1 come Eth1. Quando si è iniziato dal cliente, riavviare l'ordine NIC rimarrà invariato all'interno del sistema operativo.  
- Se la macchina virtuale fa parte di un [set di disponibilità](../azure-glossary-cloud-terminology.md#availability-set), tutte le macchine virtuali all'interno del set di disponibilità devono avere una singola scheda o più schede di rete. Se le macchine virtuali dispone di più schede di rete, il numero hanno non è necessario essere lo stesso, purché ogni macchina virtuale è almeno due schede di rete.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come creare una macchina virtuale con una singola scheda, leggere l'articolo [creare una macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md) .
- Informazioni su come creare una macchina virtuale con più schede di rete, leggere l'articolo [Distribuisci una macchina virtuale con più NIC](virtual-network-deploy-multinic-arm-ps.md) .
- Informazioni su come creare una scheda di rete con più configurazioni di indirizzi IP, leggere l'articolo di [indirizzi IP multipli per macchine virtuali di Azure](virtual-network-multiple-ip-addresses-powershell.md) .
