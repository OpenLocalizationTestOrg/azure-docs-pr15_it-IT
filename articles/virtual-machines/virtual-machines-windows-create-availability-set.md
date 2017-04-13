<properties
    pageTitle="Creare un set di disponibilità macchine Virtuali | Microsoft Azure"
    description="Informazioni su come creare una disponibilità impostare per macchine virtuali tramite portal Azure o PowerShell utilizzando il modello di distribuzione di Manager delle risorse."
    keywords="set di disponibilità"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Creare un set di disponibilità 

Quando tramite il portale, se si desidera che la macchina virtuale da parte di una disponibilità impostata, è necessario creare la disponibilità imposta prima di tutto.

Per ulteriori informazioni sulla creazione e l'utilizzo di set di disponibilità, vedere [gestire la disponibilità di macchine virtuali](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utilizzare il portale per creare una disponibilità impostare prima di creare nelle macchine virtuali

1. Nel menu hub, fare clic su **Sfoglia** e selezionare **Imposta disponibilità**.

2. Nella **disponibilità imposta blade**, fare clic su **Aggiungi**.

    ![Schermata che mostra il pulsante Aggiungi per la creazione di una nuova disponibilità impostate.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. In e **Imposta la disponibilità di creare** il completare le informazioni per l'insieme.

    ![Schermata che mostra le informazioni è necessario immettere per creare il set di disponibilità.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nome** - il nome deve essere 1-80 caratteri costituiti da numeri, lettere, periodi, caratteri di sottolineatura e trattini. Il primo carattere deve essere una lettera o un numero. L'ultimo carattere deve essere una lettera, numero o un carattere di sottolineatura.
    - **Domini guasto** - domini guasto definiscono il gruppo di macchine virtuali che condividono un parametro di origine e di rete power comune. Per impostazione predefinita, le macchine virtuali separate domini guasto fino a tre e possono essere modificate in compreso tra 1 e 3.
    - **Aggiornare domini** - aggiornamento cinque domini vengono assegnati per impostazione predefinita e questo può impostare compreso tra 1 e 20. Aggiornamento domini indicano i gruppi di macchine virtuali e hardware fisico sottostante che può essere riavviato nello stesso momento. Ad esempio, se si specifica cinque aggiornare domini, quando vengono configurate più di cinque macchine virtuali all'interno di un singolo Set di disponibilità, la macchina virtuale sesta verrà inserito lo stesso dominio di aggiornamento sulla prima macchina virtuale, la settima UD stesso come la seconda macchina virtuale e così via. L'ordine del riavvio potrebbe non essere sequenza, ma solo un aggiornamento di dominio verrà riavviato alla volta.
    - **Abbonamento** , selezionare l'abbonamento da utilizzare se si usano più monitor.
    - **Gruppo risorse** : selezionare un gruppo di risorse esistente facendo clic sulla freccia e selezionare un gruppo di risorse dall'elenco a discesa verso il basso. È anche possibile creare un nuovo gruppo di risorse digitando un nome. Il nome può contenere i caratteri seguenti: lettere, numeri, punti, trattini, caratteri di sottolineatura e apertura o la parentesi di chiusura. Il nome non può terminare con un periodo. Tutte le macchine nel gruppo disponibilità è necessario creare nello stesso gruppo di risorse il set di disponibilità.
    - **Percorso** , selezionare una posizione dall'elenco a discesa.

4. Dopo avere immesso le informazioni, fare clic su **Crea**. Dopo aver creato il gruppo di disponibilità, è possibile visualizzarlo nell'elenco dopo l'aggiornamento del portale.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Utilizzare il portale per creare una macchina virtuale e una disponibilità impostare contemporaneamente

Se si sta creando una nuova macchina virtuale tramite il portale, è anche possibile creare disponibilità di una nuova impostazione per la macchina virtuale durante la creazione della macchina virtuale prima del set.

![Schermata che mostra il processo di creazione di una nuova disponibilità impostata durante la creazione della macchina virtuale.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Aggiungere una nuova macchina virtuale a un insieme di disponibilità esistente

Per ogni ulteriori macchine Virtuali creati che devono appartenere nel set di, assicurarsi che crea nello stesso **gruppo di risorse** e quindi selezionare la disponibilità esistente impostata nel passaggio 3. 

![Schermata che mostra come selezionare una disponibilità esistente impostata per usare per la macchina virtuale.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Usare PowerShell per creare un set di disponibilità

In questo esempio viene creato una disponibilità impostare nel gruppo di risorse **RMResGroup** la posizione **Degli Stati Uniti ovest** . Questa operazione deve essere eseguita prima di creare la prima macchina virtuale che si trova il set.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Per ulteriori informazioni, vedere [AzureRmAvailabilitySet di nuovo](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Risoluzione dei problemi

- Quando si crea una macchina virtuale, se il set di disponibilità desiderato non è nell'elenco a discesa nel portale si può crearlo in un altro gruppo di risorse. Se non si conosce il gruppo di risorse per la propria disponibilità impostata, passare al menu hub e fare clic su Sfoglia > Imposta disponibilità per visualizzare un elenco di set di disponibilità e che appartengono a gruppi di risorse.


## <a name="next-steps"></a>Passaggi successivi

Aggiungere ulteriore spazio di archiviazione per le macchine Virtuali mediante l'aggiunta di un [disco dati](virtual-machines-windows-attach-disk-portal.md).
