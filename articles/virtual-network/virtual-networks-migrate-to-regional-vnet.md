<properties 
   pageTitle="Come eseguire la migrazione da gruppi di affinità a una rete locale (VNet)"
   description="Informazioni su come eseguire la migrazione da gruppi di affinità a vnets internazionali"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-migrate-from-affinity-groups-to-a-regional-virtual-network-vnet"></a>Come eseguire la migrazione da gruppi di affinità a una rete locale (VNet)

È possibile usare un gruppo di affinità per garantire che le risorse create nello stesso gruppo affinità fisica ospitate dal server che sono vicine, abilitare queste risorse comunicare più rapidamente. In passato, affinità gruppi sono stati un requisito per la creazione di reti virtuali (VNets). In quel momento, il servizio di gestione di rete che gestiti VNets Impossibile funzionano solo all'interno di un insieme di server fisici o unità in scala. Miglioramenti apportati all'architettura hanno migliorato l'ambito della gestione della rete su un'area.

In seguito a questi miglioramenti all'architettura, affinità gruppi non è più consigliati o necessari per le reti virtuali. Uso dei gruppi di affinità per VNets è sostituita da aree geografiche. VNets associate aree geografiche sono denominate VNets internazionali.

Inoltre, è consigliabile che non si usa affinità gruppi in generale. Oltre a questo requisito VNet affinità gruppi sono stati anche importanti da utilizzare per assicurarsi che le risorse, ad esempio elaborazione e di archiviazione, sono stati immessi vicini. Tuttavia, con l'architettura di rete Azure corrente, questi requisiti di posizione non sono più necessari. Per alcuni casi specifici rimanenti nel punto in cui si desidera utilizzare un gruppo di affinità, vedere [gruppi affinità e macchine virtuali](#Affinity-groups-and-VMs) .

## <a name="creating-and-migrating-to-regional-vnets"></a>Creazione e la migrazione a VNets internazionali

In futuro, quando si creano nuovi VNets, utilizzare *l'area geografica*. Verrà visualizzato come opzione nel portale di gestione. Si noti che nel file di configurazione di rete, questo modo viene illustrato come *posizione*.

>[AZURE.IMPORTANT] Sebbene sia ancora tecnicamente possibile creare una rete virtuale associata a un gruppo di affinità, non esiste alcun motivo valido per farlo. Nuove caratteristiche, ad esempio gruppi di sicurezza di rete, sono disponibili solo quando si utilizza un VNet internazionali e non sono disponibili per le reti virtuali associate a gruppi di affinità.

### <a name="about-vnets-currently-associated-with-affinity-groups"></a>Sulle VNets attualmente associata affinità gruppi

VNets attualmente associate affinità gruppi abilitati per la migrazione a VNets internazionali. Per eseguire la migrazione a un VNet internazionali, procedere come segue:

1. Esportare il file di configurazione della rete. È possibile usare PowerShell o il portale di gestione. Per istruzioni tramite il portale di gestione, vedere [configurare i VNet tramite un File di configurazione della rete](virtual-networks-using-network-configuration-file.md).

1. Modificare il file di configurazione della rete, sostituendo i vecchi valori con i nuovi valori. 

    > [AZURE.NOTE] Il **percorso** è l'area specificata per il gruppo affinità associato il VNet. Ad esempio, se il VNet è associata a un gruppo di affinità che si trova negli Stati Uniti ovest, quando esegue la migrazione, la posizione dell'utente deve puntare su US ovest. 
    
    Modificare le seguenti righe nel file di configurazione della rete, sostituire i valori con il proprio: 

    **Valore precedente:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 

    **Nuovo valore:** \<VirtualNetworkSitename = percorso "VNetUSWest" = "US Ovest"\>

1. Salvare le modifiche e [importare](virtual-networks-using-network-configuration-file.md) la configurazione di rete in Azure.

>[AZURE.NOTE] La migrazione non causano un tempo di inattività per i servizi.

## <a name="affinity-groups-and-vms"></a>Gruppi di affinità e macchine virtuali

Come detto in precedenza, affinità gruppi non è più generalmente sono consigliati per macchine virtuali. Utilizzare un gruppo di affinità solo quando un set di macchine virtuali deve avere la latenza di rete più bassa assoluto tra macchine virtuali. Inserendo macchine virtuali in un gruppo di affinità macchine virtuali verranno tutti inserite nell'unità di scala o cluster di elaborazione stesso.

È importante tenere presente che con un gruppo di affinità possono disporre di due, probabilmente è negativo, conseguenze:

- Il set di dimensioni macchine Virtuali saranno limitato per il set di dimensioni macchine Virtuali offerti dall'unità in scala calcolo.

- Esiste una probabilità di non riuscire a allocare una nuova macchina virtuale. Si verifica quando l'unità di scala specifica per il gruppo affinità non è compresa nel capacità.

### <a name="what-to-do-if-you-have-a-vm-in-an-affinity-group"></a>Cosa fare se si dispone di una macchina virtuale in un gruppo di affinità

Non è necessario essere rimossi dal gruppo affinità macchine virtuali che appartengono a un gruppo di affinità.

Una volta distribuita una macchina virtuale, viene distribuito in un'unità in scala singola. Gruppi di affinità possono limitare il set di dimensioni macchine Virtuali disponibili per una nuova distribuzione di macchine Virtuali, ma qualsiasi macchina virtuale esistente che viene distribuito già è limitato al set di dimensioni macchine Virtuali disponibili nell'unità di scala in cui viene distribuita la macchina virtuale. Per questi motivi, la rimozione di una macchina virtuale dal gruppo affinità avrà alcun effetto.
 
