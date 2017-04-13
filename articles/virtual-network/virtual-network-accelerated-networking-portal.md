<properties 
   pageTitle="Accelerazione di rete per una macchina virtuale - portale | Microsoft Azure"
   description="Informazioni su come configurare accelerata rete per una macchina virtuale Azure tramite il portale di Azure."
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
   ms.date="09/26/2016"
   ms.author="jdial" />

# <a name="accelerated-networking-for-a-virtual-machine"></a>Rete accelerata per una macchina virtuale

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Rete accelerata consente singola radice i/o virtualizzazione (SR IOV) a una macchine (), migliorando le prestazioni di rete. Questo percorso prestazioni elevate consente di ignorare l'host da datapath ridurre latenza, variazione e l'utilizzo della CPU da utilizzare con i carichi di lavoro rete più complesse in tipi di macchine Virtuali supportati. In questo articolo viene spiegato come utilizzare il portale di Azure per configurare accelerata Networking nel modello di distribuzione Manager delle risorse di Azure. È anche possibile creare una macchina virtuale con accelerazione di rete con PowerShell Azure. Per informazioni su come, fare clic sulla casella PowerShell nella parte superiore di questo articolo.

L'immagine seguente mostra le comunicazioni tra due macchine virtuali (macchine Virtuali) con e senza accelerata Networking:

![Confronto](./media/virtual-network-accelerated-networking-portal/image1.png)

Senza accelerata rete tutto il traffico di rete e ridurre la macchina virtuale deve scorrere all'host e il parametro virtuale. Il parametro virtuale fornisce tutti i criteri, ad esempio gruppi di sicurezza di rete, gli elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzato per il traffico di rete. Per ulteriori informazioni, leggere l'articolo [virtualizzazione di rete Hyper-V e Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Con accelerazione di rete, il traffico di rete raggiunge la scheda di rete (NIC) e quindi inoltrato a macchina virtuale. Tutti i criteri di rete che consente di applicare l'opzione virtuale senza accelerata rete vengono scaricati e applicati in hardware. L'applicazione dei criteri in hardware consente la scheda di rete per inoltrare il traffico di rete direttamente a macchine Virtuali, ignorare l'host e il parametro virtuale mantenendo tutti i criteri applicato nell'host.

Vantaggi dell'accelerazione rete vengono applicate solo alle macchine Virtuali che viene abilitata su. Per ottenere risultati ottimali, è ideale per abilitare questa funzionalità in almeno due macchine virtuali connessi alla stessa VNet. Per le comunicazioni tra VNets o connessione locale, questa caratteristica minimo l'impatto a latenza complessiva.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Vantaggi

- **Latenza inferiore / superiori pacchetti al secondo (pps):** Rimozione virtuale cambia la datapath rimuove il tempo di pacchetti nell'host per l'elaborazione dei criteri e aumenta il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
- **Ridotte variazione:** Elaborazione cambia virtuale dipende la quantità di criteri che deve essere applicato e il carico di lavoro della CPU che esegue l'elaborazione. Possibilità di scaricare l'applicazione dei criteri per i componenti hardware consente di rimuovere tale variabilità offrendo pacchetti direttamente a macchine Virtuali, la rimozione all'host comunicazioni macchine Virtuali e tutte le interruzioni di software e i cambi di contesto.
- **Utilizzo della CPU ridotta:** Ignorare la commutazione virtuale nell'host dei clienti potenziali a minore utilizzo della CPU per l'elaborazione il traffico di rete.

## <a name="limitations"></a>Limitazioni

Quando si utilizza questa funzionalità, sono presenti le limitazioni seguenti:
 
- **Creazione dell'interfaccia di rete:** Networking accelerata può essere abilitata solo per una nuova interfaccia di rete.  Non può essere attivato in un'interfaccia di rete esistente.
- **La creazione di macchine Virtuali:** Un'interfaccia di rete con rete accelerata abilitata può essere associata a una macchina virtuale solo quando si crea la macchina virtuale. Interfaccia di rete non è possibile collegare una macchina virtuale esistente.
- **Aree:** Presenti solo alle aree ovest Central US e Ovest Europa Azure. Espanderà il set di aree in futuro.
- **Del sistema operativo supportati:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Presto verrà aggiunti supporto Linux e Windows Server 2012.
- **Dimensione memoria virtuale:** Standard_D15_v2 e Standard_DS15_v2 sono supportate solo le dimensioni istanza macchine Virtuali. Per ulteriori informazioni, vedere l'articolo [dimensioni macchine Virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md) . Espanderà il set di dimensioni di istanza macchine Virtuali supportati in futuro.

Le modifiche apportate a queste limitazioni vengono annunciate tramite la pagina di [rete virtuale Azure aggiornamenti](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Creare una macchina virtuale di Windows con la rete accelerata

1. Eseguire la registrazione per l'anteprima inviando un messaggio di posta elettronica a [Accelerata sottoscrizioni di rete](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID di abbonamento e pensato. Ignorare i passaggi rimanenti fino a dopo aver ricevuto un messaggio indicante che è stata accettata in anteprima.
2. Accedere al portale di Azure all'http://portal.azure.com.
3. Creare una macchina virtuale, completando la procedura vedere l'articolo [creare una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) selezionare le opzioni seguenti:
    - Selezionare un sistema operativo elencato nella sezione limitazioni di questo articolo.
    - Selezionare un percorso (area) elencato nella sezione limitazioni di questo articolo.
    - Selezionare una dimensione di memoria virtuale elencata nella sezione limitazioni di questo articolo. Se uno dei formati supportati non è elencato, fare clic su **Visualizza tutto** e lo **scegliere una dimensione** per selezionare una dimensione da un elenco espanso.
    - In e **l'Impostazioni** , fare clic su *attivato* per **Accelerated networking**, come illustrato nella figura seguente:

        ![Impostazioni](./media/virtual-network-accelerated-networking-portal/image3.png)

    >[AZURE.NOTE] L'opzione accelerata saranno visibile solo se sono state eseguite:
    >
    >- Accettato in anteprima
    >- Sistemi operativi supportati, posizione e le dimensioni di macchine Virtuali indicate nella sezione limitazioni di questo articolo selezionati.

5. Dopo aver creata la macchina virtuale, scaricare il [driver accelerata rete](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connettersi o effettuare l'accesso macchine Virtuali ed eseguire il programma di installazione di driver all'interno della macchina virtuale.
6. Pulsante destro del mouse sul pulsante di Windows e fare clic su **Gestione dispositivi**. Verificare che la **Scheda Mellanox ConnectX 3 virtuale funzione Ethernet** visualizzata sotto l'opzione di **rete** espanso, come illustrato nella figura seguente:

    ![Gestione dispositivi](./media/virtual-network-accelerated-networking-portal/image2.png)