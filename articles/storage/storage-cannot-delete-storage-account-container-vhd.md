<properties
    pageTitle="Risolvere i problemi di eliminare gli account di archiviazione Azure, contenitori o dischi rigidi virtuali in una distribuzione classica | Microsoft Azure"
    description="Risolvere i problemi di eliminare gli account di archiviazione Azure, contenitori o dischi rigidi virtuali in una distribuzione classica"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="tysonn"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="genli"/>

# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>Risolvere i problemi di eliminare gli account di archiviazione Azure, contenitori o dischi rigidi virtuali in una distribuzione classica

[AZURE.INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

È possibile ricevere errori quando si tenta di eliminare l'account di archiviazione Azure, contenitore o disco rigido virtuale in [Azure portale](https://portal.azure.com/) o di [Azure portale classica](https://manage.windowsazure.com/). I problemi possono essere dovuti a circostanze seguenti:

-   Quando si elimina una macchina virtuale, il disco e il disco rigido virtuale non vengono automaticamente eliminati. Che potrebbe essere il motivo dell'errore su eliminazione dell'account di archiviazione. Abbiamo non eliminare il disco in modo che è possibile utilizzare il disco per installare un altro macchine Virtuali.

-   È ancora presente un leasing su un disco o blob associato il disco.

Se il problema Azure non è indirizzato in questo articolo, visitare i forum Azure su [MSDN e Overflow dello Stack](https://azure.microsoft.com/support/forums/). È possibile registrare il problema questi forum o a @AzureSupport su Twitter. Inoltre, è possibile archiviare una richiesta di supporto Azure selezionando **ottenere supporto** nel sito di [supporto di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptoms"></a>Sintomi

La sezione seguente sono elencati gli errori comuni che potrebbero essere visualizzati quando si tenta di eliminare l'account di archiviazione Azure, contenitori o dischi rigidi virtuali.

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>Scenario 1: Impossibile eliminare un account di archiviazione

Quando si sposta all'account di archiviazione nel [portale di Azure](https://portal.azure.com/) o [Azure portale classica](https://manage.windowsazure.com/) e selezionare **Elimina**, è possibile vedere il seguente messaggio di errore:

*Account di archiviazione StorageAccountName contiene le immagini di macchine Virtuali. Assicurarsi che le immagini di macchine Virtuali vengono rimossi prima di eliminare l'account di archiviazione.*

È possibile visualizzare l'errore seguente:

**Portale su Azure**:

*Impossibile eliminare < macchine virtuali archiviazione account nome---> account di archiviazione. Impossibile eliminare l'account di archiviazione < macchine virtuali archiviazione account nome--->: ' account di archiviazione < macchine virtuali archiviazione account nome---> sono disponibili alcune immagini attivo e/o dischi. Assicurarsi che le immagini e/o dischi vengono rimossi prima di eliminare l'account di archiviazione. ".*

**Portale classica su Azure**:

*Account di archiviazione < macchine virtuali archiviazione account nome---> sono disponibili alcune immagini attivo e/o dischi, ad esempio xxxxxxxxx-xxxxxxxxx-O-209490240936090599. Assicurarsi che le immagini e/o dischi vengono rimossi prima di eliminare l'account di archiviazione.*

O

**Portale su Azure**:

Account di archiviazione *< macchine virtuali archiviazione account nome---> sono 1 contenitori che dispone di un'immagine attiva e/o gli elementi del disco. Assicurarsi che tali elementi vengono rimossi dal repository immagine prima di eliminare l'account di archiviazione*.

**Portale classica su Azure**:

Account di spazio di archiviazione non è possibile inviare *< macchine virtuali archiviazione account nome---> ha 1 ai contenitori che dispone di un'immagine attiva e/o gli elementi del disco. Assicurarsi che tali elementi vengono rimossi dal repository immagine prima di eliminare l'account di archiviazione. Quando si tenta di eliminare un account di archiviazione e sono presenti dischi ancora attivi è associati, verrà visualizzato un messaggio per indicare che esistono dischi attivi che devono essere eliminate*.

### <a name="scenario-2-unable-to-delete-a-container"></a>Scenario 2: Impossibile eliminare un contenitore

Quando si tenta di eliminare il contenitore di spazio di archiviazione, può essere visualizzato l'errore seguente:

Impossibile eliminare il contenitore di spazio di archiviazione * <container name>. Errore: "sul contenitore è attualmente un leasing e nessun ID leasing è stato specificato nella richiesta*.

### <a name="scenario-3-unable-to-delete-a-vhd"></a>Scenario 3: Impossibile eliminare un disco rigido virtuale

Dopo avere eliminato una macchina virtuale e quindi provare a eliminare i BLOB per dischi rigidi virtuali di associato, si potrebbe visualizzato il messaggio seguente:

*Non è possibile eliminare blob ' percorso/XXXXXX-XXXXXX-os-1447379084699.vhd'. Errore: "è attualmente un leasing il blob e nessun ID leasing è stato specificato nella richiesta.*

## <a name="solution"></a>Soluzione
Per risolvere i problemi più comuni, provare a eseguire il metodo seguente:

### <a name="step-1-delete-any-os-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>Passaggio 1: Eliminare qualsiasi disco del sistema operativo che impediscono l'eliminazione dell'account di archiviazione, contenitore o disco rigido virtuale

1. Passare al [portale classica Azure](https://manage.windowsazure.com/).
2. Selezionare **macchina virtuale** > **dischi**.

    ![Immagine di dischi in macchine virtuali nel portale classica Azure.](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)

3. Individuare i dischi che sono associati all'account di archiviazione, contenitore o disco rigido virtuale che si desidera eliminare. Quando si passa il percorso del disco, si troverà l'account di archiviazione associato, contenitore o disco rigido virtuale.

    ![Immagine che mostra informazioni sulla posizione dei dischi nel portale classica Azure](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)

4. Verificare che nessun macchine Virtuali sia presente nel campo **collegato** dei dischi e quindi eliminare i dischi.

    > [AZURE.NOTE] Se un disco è collegato a una macchina virtuale, non sarà possibile per eliminarla. Dischi sono disconnessi da una macchina virtuale eliminata in modo asincrono. Potrebbe richiedere alcuni minuti dopo l'eliminazione per questo campo per liberare la macchina virtuale.

### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>Passaggio 2: Eliminare le immagini di macchine Virtuali che impediscono l'eliminazione dell'account di archiviazione o contenitore

1. Passare al [portale classica Azure](https://manage.windowsazure.com/).
2. Selezionare **macchina virtuale** > **immagini**e quindi eliminare le immagini che sono associate all'account di archiviazione, contenitore o disco rigido virtuale.

    In seguito, provare nuovamente a eliminare l'account di archiviazione, contenitore o disco rigido virtuale.

> [AZURE.WARNING] Assicurarsi di eseguire il backup tutto ciò che si vuole salvare prima di eliminare l'account. Non è possibile ripristinare un account di archiviazione eliminate o recuperare il contenuto in essa contenuto prima dell'eliminazione. Questa operazione anche vale per tutte le risorse nella finestra account: dopo aver eliminato un disco rigido virtuale, blob, tabella, coda o file, viene eliminato definitivamente. Assicurarsi che la risorsa non sia in uso.

## <a name="about-the-stopped-deallocated-status"></a>Informazioni sullo stato arrestato (rilasciato)

Macchine virtuali che sono stati creati nel modello di distribuzione classica e che sono state mantenute avrà lo stato **arrestato (rilasciato)** per il [portale di Azure](https://portal.azure.com/) e [Azure portale classica](https://manage.windowsazure.com/).

**Portale classica azure**:

![Arrestato (Deallocated) stato per macchine virtuali nel portale Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)


**Portale di azure**:

![Arrestato (deallocato) status macchine virtuali nel portale classica Azure.](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

Uno stato "Arrestato (rilasciato)" Rilascia le risorse del computer, ad esempio rete, CPU e memoria. Dischi, tuttavia, vengono conservati ancora in modo che è possibile rapidamente ricreare la macchina virtuale se necessario. Questi dischi vengono creati nella parte superiore di dischi rigidi virtuali, che sono supportati da archiviazione Azure. Account di archiviazione sono questi dischi rigidi virtuali e dischi dispongano leasing su tali dischi rigidi virtuali.

## <a name="next-steps"></a>Passaggi successivi

- [Eliminare un account di archiviazione](storage-create-storage-account.md#delete-a-storage-account)
- [Come interrompere leasing bloccato di spazio di archiviazione blob Microsoft Azure (PowerShell)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)
