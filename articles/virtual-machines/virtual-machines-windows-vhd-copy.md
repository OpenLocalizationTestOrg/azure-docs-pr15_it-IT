<properties
    pageTitle="Creare una copia di una macchina virtuale specializzata in Azure | Microsoft Azure"
    description="Informazioni su come creare una copia di una macchina virtuale Windows specializzati in esecuzione in Azure, nel modello di distribuzione Manager delle risorse."
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
    ms.date="10/20/2016"
    ms.author="cynthn"/>
    
    
    
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Creare una copia di una macchina virtuale Windows specializzati in esecuzione in Azure 

In questo articolo viene illustrato come utilizzare lo strumento AZCopy per creare una copia del file da una macchina Windows specializzati è in esecuzione in Azure. È quindi possibile utilizzare la copia del file per creare una nuova macchina virtuale. 

- Se per copiare una macchina virtuale GRG, vedere [come creare un'immagine di macchine Virtuali da una macchina virtuale Azure GRG esistente](virtual-machines-windows-capture-image.md).

- Se si desidera caricare un disco rigido virtuale da una macchina virtuale locale, ad esempio una creata con Hyper-V, vedere [caricare un disco rigido virtuale di Windows da una macchina virtuale locale in Azure](virtual-machines-windows-upload-image.md).


## <a name="before-you-begin"></a>Prima di iniziare

Verificare che l'opzione è:

- Avere informazioni sugli **account di spazio di archiviazione di origine e destinazione**. Per l'origine macchine Virtuali, è necessario per i nomi di account e contenitore di spazio di archiviazione. In genere, il nome del contenitore sarà **dischi rigidi virtuali**. È necessario disporre di un account di archiviazione di destinazione. Se non hai già uno, è possibile creare uno tramite il portale di uno (**Altri servizi** > account di archiviazione > Aggiungi) o utilizzare il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) . 

- Dispongono di Azure [PowerShell 1.0](../powershell-install-configure.md) (o versione successiva) installato.

- Avere scaricato e installato lo [strumento AzCopy](../storage/storage-use-azcopy.md). 


## <a name="deallocate-the-vm"></a>Rilasciare la macchina virtuale

Rilasciare la macchina virtuale che consente di liberare il disco rigido virtuale da copiare. 

- **Portale**: fare clic su **macchine virtuali** > **myVM** > Interrompi
- **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` ne esegue la macchina virtuale denominata **myVM** nel gruppo di risorse **myResourceGroup**.

Lo **stato** per macchine Virtuali nel portale di Azure le modifiche dal **arrestato** su **arrestato (rilasciato)**.


## <a name="get-the-storage-account-urls"></a>Ottenere l'URL di account di archiviazione

È necessario gli URL degli account di archiviazione di origine e destinazione. L'URL è simile a: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se si conosce il nome di account e contenitore di archiviazione, è possibile sostituire solo le informazioni tra parentesi quadre per creare l'URL. 

È possibile utilizzare il portale di Azure e Azure Powershell per ottenere l'URL:

- **Portale**: fare clic su **altri servizi** > **gli account di archiviazione**  >  <storage account> **BLOB** e il file del disco rigido virtuale di origine è probabilmente nel contenitore di **dischi rigidi virtuali** . Fare clic su **proprietà** per il contenitore e copiare il testo etichettato **URL**. È necessario gli URL dei contenitori di origine e destinazione. 

- **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` riceve le informazioni per macchine Virtuali denominato **myVM** nel gruppo di risorse **myResourceGroup**. Nei risultati della sezione individuare il **profilo di spazio di archiviazione** per **Uri disco rigido virtuale**. La prima parte dell'Uri è l'URL per il contenitore e l'ultima parte corrisponde al nome del sistema operativo disco rigido virtuale per la macchina virtuale.

## <a name="get-the-storage-access-keys"></a>Ottenere i tasti di scelta di spazio di archiviazione

Individuare i tasti di scelta per l'origine e destinazione gli account di archiviazione. Per ulteriori informazioni sui tasti di scelta, vedere [gli account di archiviazione su Azure](../storage/storage-create-storage-account.md).

- **Portale**: fare clic su **altri servizi** > **gli account di archiviazione**  >  <storage account> **Tutte le impostazioni** > **i tasti di scelta**. Copiare il codice etichettato con **chiave1**.
- **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` Ottiene la chiave di spazio di archiviazione per l' account di archiviazione **mystorageaccount** il gruppo di risorse **myResourceGroup**. Copiare il codice etichettato con **chiave1**.


## <a name="copy-the-vhd"></a>Copiare il disco rigido virtuale 

È possibile copiare i file tra gli account di archiviazione utilizzando AzCopy. Per il contenitore di destinazione, se il contenitore specificato non esiste, esso verrà creato automaticamente. 

Per utilizzare AzCopy, aprire un prompt dei comandi sul computer locale e passare alla cartella in cui è installato AzCopy. Sarà simile a *file c:\Programmi\Microsoft (x86) \Microsoft SDKs\Azure\AzCopy*. 

Per copiare tutti i file in un contenitore, utilizzare l'opzione **/S** . Può essere utilizzato per copiare il disco rigido virtuale del sistema operativo e tutti i dischi dati qualora siano nello stesso contenitore. In questo esempio viene illustrato come copiare tutti i file il contenitore **mysourcecontainer** nell' account di archiviazione **mysourcestorageaccount** il contenitore **mydestinationcontainer** nell'account di archiviazione **mydestinationstorageaccount** . Sostituire i nomi degli account di archiviazione di contenitori con uno personalizzato. Sostituire `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` con chiavi personalizzate.

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se si desidera copiare un disco rigido virtuale specifico in un contenitore con più file, è inoltre possibile specificare il nome del file con l'opzione /Pattern. In questo esempio verrà copiato solo il file denominato **myFileName.vhd** .

```
    AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
        /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
        /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
        /Pattern:myFileName.vhd
```


Al termine, verrà visualizzato un messaggio che ha un aspetto simile:

```
  Finished 2 of total 2 file(s).
  [2016/10/07 17:37:41] Transfer summary:
  -----------------
  Total files transferred: 2
  Transfer successfully:   2
  Transfer skipped:        0
  Transfer failed:         0
  Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Risoluzione dei problemi

- Quando si utilizza AZCopy, se viene visualizzato l'errore "Server non è possibile autenticare la richiesta. Verificare che il valore dell'intestazione di autorizzazione è costituito correttamente tra cui la firma." e si sta usando 2 di chiave o la chiave di archiviazione secondario, provare a usare la chiave primaria o 1a archiviazione.


## <a name="next-steps"></a>Passaggi successivi

- È possibile creare una nuova macchina virtuale collegando [la copia del file per una macchina virtuale come disco del sistema operativo](virtual-machines-windows-create-vm-specialized.md).












