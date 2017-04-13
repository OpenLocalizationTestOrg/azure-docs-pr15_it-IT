<properties
   pageTitle="Più indirizzi IP per macchine virtuali - PowerShell | Microsoft Azure"
   description="Informazioni su come assegnare più indirizzi IP a un computer virtuale tramite PowerShell Azure."
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
   ms.date="10/05/2016"
   ms.author="jdial;annahar" />


# <a name="assign-multiple-ip-addresses-to-virtual-machines"></a>Assegnare più indirizzi IP in macchine virtuali

Un Azure virtuale macchina possono contenere uno o più interfacce di rete (NIC) allegate. Qualunque NIC può avere uno o più pubblici o privati indirizzi IP assegnati. Se non si ha familiarità con indirizzi IP di Azure, leggere l'articolo di [indirizzi IP in Azure](virtual-network-ip-addresses-overview-arm.md) per altre informazioni relative ad esse. In questo articolo viene spiegato come utilizzare Azure PowerShell per assegnare più indirizzi IP a una macchina virtuale nel modello di distribuzione Manager delle risorse di Azure.

Assegnazione di più indirizzi IP a una macchina virtuale consente le funzionalità seguenti:

- Ospitare più siti Web o servizi con i certificati SSL su un singolo server e indirizzi IP diversi.
- Fungere da un dispositivo virtuale di rete, ad esempio un firewall o bilanciamento del carico.
- La possibilità di aggiungere uno degli indirizzi IP privati per qualsiasi delle schede in un pool di back-end di bilanciamento del carico Azure. In passato, solo l'indirizzo IP primario per la scheda principale era possibile aggiungere a un pool di back-end.

[AZURE.INCLUDE [virtual-network-preview](../../includes/virtual-network-preview.md)]

Per eseguire la registrazione per l'anteprima, inviare un messaggio di posta elettronica a [Più indirizzi IP](mailto:MultipleIPsPreview@microsoft.com?subject=Request%20to%20enable%20subscription%20%3csubscription%20id%3e) con l'ID di abbonamento e uso.

## <a name="scenario"></a>Scenario:

In questo articolo, associare tre configurazioni IP all'interfaccia di rete.
Verranno create e assegnate a una scheda con tre indirizzi IP privati e un indirizzo IP pubblico assegnato le configurazioni di esempio seguente:

- IPConfig-1: Un indirizzo IP privato dinamico (impostazione predefinita) e un indirizzo IP pubblico gli indirizzi dalla risorsa indirizzo IP pubblica denominata PIP1.
- IPConfig-2: Un indirizzo IP statico privato e nessun indirizzo IP pubblico.
- IPConfig-3: Un indirizzo IP privato dinamico e nessun indirizzo IP pubblico.

    ![Testo alternativo immagine](./media/virtual-network-multiple-ip-addresses-powershell/OneNIC-3IP.png)

Questo scenario presuppone che si abbia un gruppo di risorse denominato *RG1* in cui esiste una VNet denominata *VNet1* e una subnet chiamato *Subnet1*. Inoltre, presuppone che si dispone di una macchina virtuale denominata *VM1*, un'interfaccia di rete denominata *VM1 NIC1* ad esso associati e un indirizzo IP pubblico chiamato *PIP1*.

[In questo articolo](./virtual-machines/virtual-machines-windows-ps-create.md ) viene descritto come creare le risorse indicate in precedenza nel caso in cui non è stato creato in precedenza.

## <a name = "create"></a>Creare una macchina virtuale con più indirizzi IP

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione all'interno di una singola sessione di PowerShell. Se non si dispone già PowerShell installato e configurato, completare i passaggi nell'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

2. Modificare i valori"" di $Variables seguenti Azure [percorso di](https://azure.microsoft.com/regions) che rete virtuale viene, il nome del [gruppo di risorse](../azure-resource-manager/resource-group-overview.md#resource-groups), VNet all'interno di gruppo di risorse, alla subnet a che si desidera collegare la NIC e il nome della scheda di rete. Completare la procedura per aggiungere più indirizzi IP a qualsiasi NIC collegata a una macchina virtuale desiderato.

        $Location = "westcentralus"
        $RgName   = "RG1"
        $VNetName   = "VNet1"
        $SubnetName = "Subnet1"
        $NicName     = "VM1-NIC1"
        $PIP = "PIP"

    Se non si conosce il nome di un percorso di Azure esistente o un gruppo di risorse, digitare i comandi seguenti:

        Get-AzureRmLocation      | Format-Table Location
        Get-AzureRmResourceGroup | Format-Table ResourceGroupName

    <a name="subnet"></a>La scheda di rete deve essere connesso a una subnet all'interno di una rete virtuale Azure esistente (VNet). I tre componenti: NIC, alla subnet e VNet, deve essere presente nella stessa area geografica e [sottoscrizione](../azure-glossary-cloud-terminology.md#subscription).  Se non si ha familiarità con VNets, leggere l'articolo [informazioni generali sui rete virtuale](virtual-networks-overview.md) per ulteriori informazioni su come loro o vedere l'articolo [creare un VNet](virtual-networks-create-vnet-arm-ps.md) per informazioni su come crearne uno.

    Se non si conosce il nome di un VNet esistente, immettere il comando seguente e sostituire *VNet1* nella variabile precedente con il nome di un VNet:

        Get-AzureRmVirtualNetwork | Format-Table Name

    Se l'elenco restituito è vuota, è necessario creare un VNet. Per informazioni su come, vedere l'articolo [creare una rete virtuale](virtual-networks-create-vnet-arm-ps.md) .

    Digitare i comandi seguenti per ottenere il nome della subnet esistente all'interno di VNet e sostituire *Subnet1* sopra con il nome di una subnet:

        $VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RgName
        $VNet.Subnets | Format-Table Name, AddressPrefix

4. Immettere il comando seguente per recuperare la subnet e assegnarlo a una variabile.

        $Subnet = $VNet.Subnets | Where-Object { $_.Name -eq $SubnetName }

5. <a name="ipconfigs"></a>Definire le configurazioni IP da assegnare alla scheda. Ogni configurazione può avere una sola statico o dinamico indirizzo IP privato e uno associati risorsa indirizzo IP pubblico con un indirizzo statico o dinamico.

    Aggiungere o rimuovere qualsiasi numero di configurazioni che seguono a seconda di come numero di indirizzi IP che si desidera associare la scheda di rete e le impostazioni che si desidera configurare.

    **IPConfig-1**

    Modificare il valore *PIP1* al nome di una risorsa indirizzo di indirizzi IP pubblico che esiste nel percorso che si sta creando la scheda di rete in e che non è attualmente associato a un'altra scheda di rete. Modifica *RG1* il nome del gruppo di risorse la risorsa indirizzo IP pubblico è presente in. Modificare il nome da assegnare alla configurazione IP prima *IPConfig-1* . Immettere i comandi seguenti:

        $PIP1 = Get-AzureRmPublicIPAddress -Name "PIP1" -ResourceGroupName $RgName

        $IpConfigName1 = "IPConfig-1"
        $IPConfig1     = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName1 -Subnet $Subnet -PublicIpAddress $PIP1 -Primary

    Nota la *-primaria* passare. Quando si assegnano più configurazioni di indirizzi IP a una scheda di rete, una configurazione deve essere assegnata come *principale*. Se non si conosce il nome di una risorsa di indirizzo IP pubblico esistente, immettere il comando seguente: Get-AzureRMPublicIPAddress | Nome formato tabella posizione, indirizzo IP, IpConfiguration

    Se la colonna **IPConfiguration** ha alcun valore in output restituito, la risorsa indirizzo IP pubblica non è associata a una scheda di rete esistente e può essere utilizzata. Se l'elenco è vuoto o non sono presenti risorse di indirizzo IP pubbliche disponibile, è possibile creare uno utilizzando il comando **Nuovo AzureRmPublicIPAddress** .

    >[AZURE.NOTE] Indirizzi IP pubblici dispone di una tariffa nominale. Per ulteriori informazioni sui prezzi indirizzo IP, leggere la pagina [prezzi indirizzo IP](https://azure.microsoft.com/pricing/details/ip-addresses) .

    **IPConfig-2**

    Modificare il nome che si desidera assegnare alla seconda configurazione IP e modificare *10.0.0.5* a un indirizzo IP inutilizzato valido per la subnet che si desidera assegnare la scheda NIC per *IPConfig-2* . Immettere i comandi seguenti:

        $IPConfigName2 = "IPConfig-2"
        $IPAddress = 10.0.0.5

        $IPConfig2 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName2 -Subnet $Subnet -PrivateIpAddress $IPAddress

    Immettere il comando seguente, se non si conosce l'indirizzo IP intervallo assegnato alla subnet di indirizzi:

        $VNet.Subnets | Format-Table Name, AddressPrefix

    **IPConfig-3**

    Modificare *IPConfig 3* il nome che si desidera assegnare alla configurazione IP terza e immettere i comandi seguenti:

        $IPConfigName3 = "IPConfig-3"
        $IPConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet

    >[AZURE.NOTE] È possibile assegnare indirizzo IP privato fino a 250 a una scheda. Esiste un limite per il numero di indirizzi IP che può essere utilizzato all'interno di una sottoscrizione. Per maggiori informazioni, leggere l'articolo [limiti di Azure](../azure-subscription-service-limits.md#networking-limits---azure-resource-manager) .

6. Creare la scheda di rete utilizzando le configurazioni IP definite nel passaggio precedente.

        $nic = New-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName -Location $Location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3

7. Collegare la scheda di rete durante la creazione di una macchina virtuale eseguendo la procedura descritta nell'articolo [Crea una macchina virtuale](../virtual-machines/virtual-machines-windows-ps-create.md) . Se l'articolo crea una macchina virtuale che eseguono Windows Server, la procedura è la stessa operazione per una VM Linux, ad eccezione della selezione di un sistema operativo diverso. Eseguire i passaggi da 1 a 3 dell'articolo. Ignorare i passaggi 4 e 5 e quindi completato il passaggio 6 in creare un articolo macchine Virtuali.

    >[AZURE.WARNING] Passaggio 6 per creare un articolo macchine Virtuali non riuscirà se la variabile denominata $nic a un account nel passaggio 6 di questo articolo viene modificata o non completata la procedura precedente di questo articolo.

8. Visualizzazione privata IP indirizzi che DHCP Azure assegnato alla scheda e la risorsa indirizzo IP pubblica assegnato alla scheda immettendo il comando seguente:

        $nic.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary

9. <a name="os"></a>Aggiungere manualmente tutti gli indirizzi di indirizzi IP privati (inclusa il principale) per la configurazione di TCP/IP nel sistema operativo. 

**Windows**

1. Al prompt dei comandi, digitare *ipconfig/tutti*.  Visualizzare solo l'indirizzo IP privato *principale* (tramite DHCP).
2. Successivo digitare *ncpa. cpl* nella finestra del prompt dei comandi. Verrà aperta una nuova finestra.
3. Visualizzare le proprietà di **Connessione alla rete locale**.
4. Fare doppio clic su Internet Protocol versione 4 (IPv4)
5. Selezionare **utilizza il seguente indirizzo IP** e immettere i valori seguenti:
    - **Indirizzo IP**: immettere l'indirizzo IP privato *principale*
    - **Maschera subnet**: impostare in base a subnet. Ad esempio, se la subnet è un /24 subnet la maschera subnet è 255.255.255.0.
    - **Gateway predefinito**: il primo indirizzo IP nella subnet. Se subnet 10.0.0.0/24, indirizzo IP del gateway è 10.0.0.1.
    - Fare clic su **utilizza i seguenti indirizzi server DNS** e immettere i valori seguenti:
        - **Server DNS preferito:** Immettere 168.63.129.16 se non si usa il proprio server DNS.  Se si, immettere l'indirizzo IP del server DNS.
    - Fare clic sul pulsante **Avanzate** e aggiungere altri indirizzi IP. Aggiungere tutti gli indirizzi IP privati secondari elencati al passaggio 8 alla NIC con la stessa subnet specificata per l'indirizzo IP primario.
    - Fare clic su **OK** per chiudere le impostazioni TCP/IP e quindi su **OK** per chiudere le impostazioni della scheda. Questo sarà quindi ristabilire la connessione RDP.

6. Al prompt dei comandi, digitare *ipconfig/tutti*. Vengono visualizzati tutti gli indirizzi IP che è stato aggiunto e DHCP è disattivata.


**Linux (Ubuntu)**

1. Aprire una finestra del terminale.
2. Verificare che ci si trova l'utente principale. Se non si dispone, è possibile eseguire utilizzando il comando seguente:

            sudo -i
3. Aggiornare il file di configurazione dell'interfaccia di rete (presupponendo che 'eth0').
    - Mantenere la voce esistente per dhcp. Questo configurerà l'indirizzo IP primario usato da versioni precedenti.
    - Aggiungere una configurazione per un indirizzo IP statico aggiuntivo con i comandi seguenti:

                cd /etc/network/interfaces.d/
                ls

        Verrà visualizzato un file cfg.
4. Aprire il file: vi *filename*.

    È necessario visualizzare le righe alla fine del file:

            auto eth0
            iface eth0 inet dhcp
5. Aggiungere le seguenti righe dopo le righe presenti nel file:

            iface eth0 inet static
            address <your private IP address here>
6. Salvare il file tramite il comando seguente:

            :wq
7.  Reimpostare l'interfaccia di rete con il comando seguente:

            sudo ifdown eth0 && sudo ifup eth0

    >[AZURE.IMPORTANT] Eseguire ifdown e ifup nella stessa riga se si usa una connessione remota.
8. Verificare che l'indirizzo IP viene aggiunto all'interfaccia di rete con il comando seguente:

            ip addr list eth0

    Verrà visualizzato l'indirizzo IP che aggiunti come parte dell'elenco.

**Linux (Red Hat, CentOS e altri)**

1. Aprire una finestra del terminale.
2. Verificare che ci si trova l'utente principale. Se non si dispone, è possibile eseguire utilizzando il comando seguente:

            sudo -i
3. Immettere la password e seguire le istruzioni quando richiesto. Dopo avere effettuato l'utente principale, passare alla cartella di script di rete con il comando seguente:

            cd /etc/sysconfig/network-scripts
4. Elencare i file correlati ifcfg utilizzando il comando seguente:

            ls ifcfg-*

    *Ifcfg eth0* verrà visualizzato come un file.
5. Copiare il file *ifcfg eth0* e denominarla *ifcfg-eth0:0* con il comando seguente:

            cp ifcfg-eth0 ifcfg-eth0:0
6. Modificare il *ifcfg-eth0:0* file con il comando seguente:

            vi ifcfg-eth1
7. Cambiare il dispositivo per il nome appropriato nel file. *eth0:0* in questo caso, usare il comando seguente:

            DEVICE=eth0:0
8. Modificare il *IPADDR = YourPrivateIPAddress* riga in modo da rispecchiare l'indirizzo IP.
9. Salvare il file con il comando seguente:

            :wq
10. Riavviare i servizi di rete e assicurarsi che le modifiche hanno esito positivo eseguendo i comandi seguenti:

            /etc/init.d/network restart
            Ipconfig

    Verrà visualizzato l'indirizzo IP che è stato aggiunto, *eth0:0*nell'elenco restituito.

## <a name="add"></a>Aggiungere gli indirizzi IP per una macchina virtuale esistente

Completare la procedura seguente per aggiungere altri indirizzi IP a una scheda di rete esistente:

1. Aprire un prompt dei comandi di PowerShell e completare i passaggi rimanenti in questa sezione all'interno di una singola sessione di PowerShell. Se non si dispone già PowerShell installato e configurato, completare i passaggi nell'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md) .

2. Modificare i valori"" di $Variables seguenti il nome di NIC si desidera aggiungere gli indirizzi IP a e il gruppo di risorse e individuare in che la scheda di rete è presente:

        $NicName     = "RG1-VM1-NIC1"
        $RgName   = "RG1"
        $NicLocation = "westcentralus"

    Se non si conosce il nome della scheda NIC che si desidera modificare, immettere i comandi seguenti, quindi modificare i valori delle variabili precedente:

        Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location

3. Creare una variabile e impostarla su NIC esistente digitando il comando seguente:

        $nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $RgName

4. Recuperare l'ID di subnet NIC sia connesso a completando [passaggio 3](#subnet) di creare una macchina virtuale con più sezione indirizzi IP di questo articolo.

5. Creare le configurazioni IP da aggiungere alla rete seguendo le istruzioni riportate nel [passaggio 5](#ipconfigs) della crea una macchina virtuale con più sezione indirizzi IP di questo articolo.

6. A *$IPConfigName4* il nome della configurazione IP che è stato creato nel passaggio precedente. Per aggiungere la configurazione, immettere il comando seguente:

        Add-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName4 -NetworkInterface $nic -Subnet $Subnet1

7. Per impostare la scheda di rete con la configurazione IP, immettere il comando seguente:

        Set-AzureRmNetworkInterface -NetworkInterface $nic

8. Aggiungere gli indirizzi IP che è aggiunto alla scheda al sistema operativo macchine Virtuali seguendo le istruzioni riportate nel [passaggio 9](#os) di creare una macchina virtuale con più sezione indirizzi IP di questo articolo.
