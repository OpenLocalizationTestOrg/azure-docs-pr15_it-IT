<properties 
   pageTitle="Accelerazione di rete per una macchina virtuale - PowerShell | Microsoft Azure"
   description="Informazioni su come configurare accelerata rete per una macchina virtuale Azure tramite PowerShell."
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

# <a name="accelerated-networking-for-a-virtual-machine"></a>Rete accelerata per una macchina virtuale

> [AZURE.SELECTOR]
- [Portale di Azure](virtual-network-accelerated-networking-portal.md)
- [PowerShell](virtual-network-accelerated-networking-powershell.md)

Rete accelerata consente singola radice i/o virtualizzazione (SR IOV) a una macchine (), migliorando le prestazioni di rete. Questo percorso prestazioni elevate consente di ignorare l'host da datapath ridurre latenza, variazione e l'utilizzo della CPU da utilizzare con i carichi di lavoro rete più complesse in tipi di macchine Virtuali supportati. In questo articolo viene spiegato come utilizzare Azure PowerShell per configurare accelerata Networking nel modello di distribuzione Manager delle risorse di Azure. È anche possibile creare una macchina virtuale con accelerata rete tramite il portale di Azure. Per informazioni su come, fare clic sulla casella portale Azure nella parte superiore di questo articolo.

L'immagine seguente mostra le comunicazioni tra due macchine virtuali (macchine Virtuali) con e senza accelerata Networking:

![Confronto](./media/virtual-network-accelerated-networking-powershell/image1.png)

Senza accelerata rete tutto il traffico di rete e ridurre la macchina virtuale deve scorrere all'host e il parametro virtuale. Il parametro virtuale fornisce tutti i criteri, ad esempio gruppi di sicurezza di rete, gli elenchi di controllo di accesso, isolamento e altri servizi di rete virtualizzato per il traffico di rete. Per ulteriori informazioni, leggere l'articolo [virtualizzazione di rete Hyper-V e Virtual Switch](https://technet.microsoft.com/library/jj945275.aspx) .

Con accelerazione di rete, il traffico di rete raggiunge la scheda di rete (NIC) e quindi inoltrato a macchina virtuale. Tutti i criteri di rete che consente di applicare l'opzione virtuale senza accelerata rete vengono scaricati e applicati in hardware. L'applicazione dei criteri in hardware consente la scheda di rete per inoltrare il traffico di rete direttamente a macchine Virtuali, ignorare l'host e il parametro virtuale mantenendo tutti i criteri applicato nell'host.

Vantaggi dell'accelerazione rete vengono applicate solo alle macchine Virtuali che viene abilitata su. Per ottenere risultati ottimali, è ideale per abilitare questa funzionalità in almeno due macchine virtuali connessi alla stessa VNet.  Per le comunicazioni tra VNets o connessione locale, questa caratteristica minimo l'impatto a latenza complessiva.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

##<a name="benefits"></a>Vantaggi

- **Latenza inferiore / superiori pacchetti al secondo (pps):** Rimozione virtuale cambia la datapath rimuove il tempo di pacchetti nell'host per l'elaborazione dei criteri e aumenta il numero di pacchetti che possono essere elaborati all'interno della macchina virtuale.
- **Ridotte variazione:** Elaborazione cambia virtuale dipende dalla quantità di criteri che deve essere applicato e il carico di lavoro della CPU che esegue l'elaborazione. Possibilità di scaricare l'applicazione dei criteri per i componenti hardware consente di rimuovere tale variabilità offrendo pacchetti direttamente a macchine Virtuali, la rimozione all'host comunicazioni macchine Virtuali e tutte le interruzioni di software e i cambi di contesto.
- **Utilizzo della CPU ridotto:** Ignorare il parametro virtuale nell'host dei clienti potenziali a minore utilizzo della CPU per l'elaborazione il traffico di rete.

## <a name="limitations"></a>Limitazioni

Quando si utilizza questa funzionalità, sono presenti le limitazioni seguenti:
 
- **Creazione dell'interfaccia di rete:** Networking accelerata può essere abilitata solo per una nuova interfaccia di rete.  Non può essere attivato in un'interfaccia di rete esistente.
- **La creazione di macchine Virtuali:** Un'interfaccia di rete con rete accelerata abilitata può essere associata a una macchina virtuale solo quando si crea la macchina virtuale. Interfaccia di rete non è possibile collegare una macchina virtuale esistente.
- **Aree:** Presenti solo alle aree ovest Central US e Ovest Europa Azure. Espanderà il set di aree in futuro.
- **Del sistema operativo supportati:** Microsoft Windows Server 2012 R2 e Windows Server 2016 Technical Preview 5. Presto verrà aggiunti supporto Linux e Windows Server 2012.
- **Dimensione memoria virtuale:** Standard_D15_v2 e Standard_DS15_v2 sono supportate solo le dimensioni istanza macchine Virtuali. Per ulteriori informazioni, vedere l'articolo [dimensioni macchine Virtuali di Windows](../virtual-machines/virtual-machines-windows-sizes.md) . Espanderà il set di dimensioni di istanza macchine Virtuali supportati in futuro.

Le modifiche apportate a queste limitazioni vengono annunciate tramite la pagina di [rete virtuale Azure aggiornamenti](https://azure.microsoft.com/updates/accelerated-networking-in-preview) .

## <a name="create-a-windows-vm-with-accelerated-networking"></a>Creare una macchina virtuale di Windows con la rete accelerata

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione all'interno di una singola sessione di PowerShell. Se non si dispone già PowerShell installato e configurato, completare i passaggi nell'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .
2. Per eseguire la registrazione per l'anteprima, inviare un messaggio di posta elettronica a [Accelerata sottoscrizioni di rete](mailto:axnpreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID di abbonamento e uso. Ignorare i passaggi rimanenti fino a dopo aver ricevuto un messaggio indicante che è stata accettata in anteprima.
3. Registrare la funzionalità con l'abbonamento tramite l'immissione di comandi seguenti:

        Register-AzureRmProviderFeature -FeatureName AllowAcceleratedNetworkingFeature -ProviderNamespace Microsoft.Network
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network

4. Sostituire *westcentralus* con il nome di un'altra posizione supportato da questa funzionalità elencata nella sezione [limitazioni](#limitations) di questo articolo (se si desidera). Immettere il seguente comando per impostare una variabile relativa al percorso:

        $locName = "westcentralus"

5. Sostituire *RG1* con un nome per il gruppo di risorse che conterrà la nuova interfaccia di rete e immettere i comandi seguenti per la creazione:

        $rgName = "RG1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

6. Modificare *VM1 NIC1* a ciò che si desidera assegnare un nome nell'interfaccia di rete e quindi immettere il comando seguente:

        $NICName = "VM1-NIC1"

7. Interfaccia di rete deve essere connesso a una subnet all'interno di una rete virtuale Azure esistente (VNet) nella stessa posizione o la [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription) dell'interfaccia di rete. Ulteriori informazioni su VNets, leggere l'articolo [informazioni generali sui rete virtuale](virtual-networks-overview.md) se non si ha familiarità con loro. Per creare un VNet, eseguire i passaggi da vedere l'articolo [creare un VNet](virtual-networks-create-vnet-arm-ps.md) . Modificare i valori"" di $Variables seguenti il nome della VNet e si vuole connettere l'interfaccia di rete alla subnet.

        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"

    Se non si conosce il nome di un VNet esistente nel percorso che scelto nel passaggio 3, immettere i comandi seguenti:
        
        $VirtualNetworks = Get-AzureRmVirtualNetwork
        $VirtualNetworks | Where-Object {$_.Location -eq $locName} | Format-Table Name, Location
        
    Se l'elenco restituito è vuota, è necessario creare un VNet nella posizione. Per creare un VNet, eseguire i passaggi da vedere l'articolo [creare una rete virtuale](virtual-networks-create-vnet-arm-ps.md) .

    Per ottenere il nome delle subnet all'interno di VNet, digitare i comandi seguenti e sostituire *Subnet1* sopra con il nome di una subnet:
        
        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

8. Immettere i comandi seguenti per recuperare la VNet e subnet e assegnarle alle variabili.

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $rgName
        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

9. Identificare una risorsa indirizzo di indirizzi IP pubblica che può essere associata all'interfaccia di rete in modo che connettersi tramite Internet. Se non si desidera accedere macchine Virtuali con l'interfaccia di rete tramite Internet, è possibile ignorare questo passaggio. Senza un indirizzo IP pubblico, è necessario connettersi alla macchina virtuale da un'altra macchina virtuale connesso a VNet stesso. 

    Modificare *PIP1* al nome di una risorsa indirizzo di indirizzi IP pubblico che esiste nel percorso si sta creando interfaccia di rete e che non è attualmente associato a un'altra interfaccia di rete. Se necessario, modificare $rgName il nome del gruppo di risorse la risorsa indirizzo IP pubblica è presente in e immettere il comando seguente:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $rgName

    Se non si conosce il nome di una risorsa di indirizzo IP pubblico esistente, immettere i comandi seguenti:

        $PublicIPAddresses = Get-AzureRmPublicIPAddress
        $PublicIPAddresses | Where-Object { $_.Location -eq $locName } |Format-Table Name, Location, IPAddress, IpConfiguration

    Se la colonna **IPConfiguration** ha alcun valore in output restituito, la risorsa indirizzo IP pubblica non è associata a un'interfaccia di rete esistente e può essere utilizzata. Se l'elenco è vuoto o non sono presenti risorse di indirizzo IP pubbliche disponibile, è possibile creare uno utilizzando il comando nuovo AzureRmPublicIPAddress.

    >[AZURE.NOTE] Indirizzi IP pubblici dispone di una tariffa nominale. Ulteriori informazioni sui prezzi, leggere la pagina [indirizzo IP prezzi](https://azure.microsoft.com/pricing/details/ip-addresses) .
10. Se si decide di non aggiungere una risorsa indirizzo IP pubblica all'interfaccia, rimuovere *- PublicIPAddress $PIP1* alla fine del comando che segue. Creare l'interfaccia di rete con la rete accelerata immettendo il comando seguente:

        $nic = New-AzureRmNetworkInterface -Location $locName -Name $NICName -ResourceGroupName $rgName -Subnet $Subnet -EnableAcceleratedNetworking -PublicIpAddress $PIP1 

11. Assegnare l'interfaccia di rete per una macchina virtuale quando si crea la macchina virtuale seguendo le istruzioni disponibili nei passaggi 3 e 6 dell'articolo [Crea una macchina virtuale](../virtual-machines/virtual-machines-windows-ps-create.md) . Nel passaggio 6-2, sostituire *Standard_A1* con uno dei formati macchine Virtuali elencati nella sezione [limitazioni](#limitations) di questo articolo.

    >[AZURE.NOTE] Se si modifica il *nome* del $locName, $rgName o $nic variabili in questo articolo, passaggio 6 per creare un articolo macchine Virtuali avrà esito negativo. Tuttavia, è possibile modificare i *valori* delle variabili.

12. Dopo aver creata la macchina virtuale, scaricare il [driver accelerata Networking](https://gallery.technet.microsoft.com/Azure-Accelerated-471b5d84), connettere la macchina virtuale ed eseguire il programma di installazione di driver all'interno della macchina virtuale.

13. Pulsante destro del mouse sul pulsante di Windows e fare clic su **Gestione dispositivi**. Verificare che la **Scheda Mellanox ConnectX 3 virtuale funzione Ethernet** visualizzata sotto l'opzione di **rete** espanso, come illustrato nella figura seguente:

    ![Gestione dispositivi](./media/virtual-network-accelerated-networking-powershell/image2.png)