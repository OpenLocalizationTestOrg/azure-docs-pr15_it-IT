<properties 
   pageTitle="Gestire i server DNS utilizzato da una rete virtuale (VNet)"
   description="Informazioni su come aggiungere e rimuovere i server DNS in una rete virtuale (vnet)"
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

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Gestire i server DNS utilizzato da una rete virtuale (VNet)

È possibile gestire l'elenco dei server DNS utilizzati in un VNet nel portale di gestione o nel file di configurazione di rete. È possibile aggiungere fino a 12 server DNS per ogni VNet. Quando si specificano i server DNS, è importante verificare elencare i server DNS nell'ordine corretto per l'ambiente. Elenchi di server DNS non funzionano circolari. Vengono utilizzati nell'ordine in cui sono specificati. Se il primo server DNS nell'elenco in grado di raggiungere, il client utilizzerà il server DNS indipendentemente dal fatto che il server DNS funziona correttamente o meno. Per modificare l'ordine dei server DNS per la rete virtuale, rimuovere i server DNS dall'elenco e aggiungerli nuovamente nell'ordine in cui si desidera.

>[AZURE.WARNING] Dopo l'aggiornamento di elenco DNS, è necessario riavviare macchine virtuali nella rete virtuale in modo che essi sollevare le nuove impostazioni del server DNS. Macchine virtuali continuerà a utilizzare la configurazione corrente fino a quando non vengono riavviati.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Modificare un elenco di server DNS per una rete virtuale tramite il portale di gestione

1. Accedere al **portale di gestione**.

1. Nel riquadro di spostamento, fare clic su **reti**e quindi fare clic sul nome della rete virtuale nella colonna **nome** .

1. Fare clic su **Configura**.

1. **I server DNS**, è possibile configurare le operazioni seguenti:

    - **Per registrare (Aggiungi) un nuovo DNS server:** Digitare il nome e indirizzo IP nelle caselle. Verrà aggiunta di un server DNS alla rete virtuale elenco server DNS e registra anche il server DNS con Azure.

    - **Per aggiungere un server DNS che è stato registrato in precedenza:** Se già registrato un server DNS con Azure, è possibile selezionare dall'elenco prepopolato.

    - **Per rimuovere un server DNS dalla rete virtuale:** Fare clic sulla X accanto al server che si desidera rimuovere. Si noti che verrà rimossa solo il server dall'elenco virtuali. Il server DNS rimane registrato in Azure per le altre reti virtuale da utilizzare. Per eliminare un server DNS dall'abbonamento, passare alla pagina **reti -> server DNS** .

    - **Per riordinare i server DNS:** Rimuovere tutti i server DNS che vengono elencati e quindi aggiungerle nuovamente nell'ordine in cui si desidera. Tenere presente che non si tratta di un elenco DNS circolari.

    - **Per rinominare un server DNS:** Selezionare il server DNS nell'elenco, quindi digitare il nuovo nome. Questa operazione verrà registrare un nuovo server DNS in Azure, nonché aggiungere all'elenco server DNS per la rete virtuale. Il vecchio server DNS e il relativo indirizzo IP rimarranno registrati con Azure. È possibile eliminarlo nella pagina **DNS Servers** se non si usa il per eventuali altre reti virtuale.

1. Fare clic su **Salva** nella parte inferiore della pagina per salvare la configurazione del nuova server DNS.

1. Riavviare macchine virtuali nella rete virtuale in modo da acquisire le nuove impostazioni DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Modificare un elenco dei server DNS tramite un file di configurazione della rete

Per modificare un elenco dei server DNS tramite un file di configurazione della rete, è innanzitutto necessario esportare le impostazioni di configurazione dal portale di gestione. È quindi verrà modificare il file di configurazione della rete e importarlo nuovamente tramite il portale di gestione. Di seguito è un elenco di alto livello dei passaggi per completare il processo.

1. Esportare le impostazioni di rete virtuale in un file di configurazione della rete. Per ulteriori informazioni e istruzioni per esportare le impostazioni di configurazione della rete, vedere [Esportare le impostazioni di rete virtuale in un File di configurazione della rete](virtual-networks-using-network-configuration-file.md).

1. Specificare le informazioni sul server DNS per la rete virtuale. Per ulteriori informazioni su come specificare un server DNS, vedere [impostazione di un Server DNS in un File di configurazione della rete virtuale](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Per ulteriori informazioni sui file di configurazione di rete, vedere [Schema di configurazione della rete virtuale Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) e [configurare una rete virtuale utilizzando un File di configurazione della rete](virtual-networks-using-network-configuration-file.md).

1. Importare il file di configurazione della rete. Per ulteriori informazioni e procedure per importare il file di configurazione della rete, vedere [importare un File di configurazione della rete](virtual-networks-using-network-configuration-file.md).

1. Riavviare macchine virtuali nella rete virtuale in modo da acquisire le nuove impostazioni DNS.
