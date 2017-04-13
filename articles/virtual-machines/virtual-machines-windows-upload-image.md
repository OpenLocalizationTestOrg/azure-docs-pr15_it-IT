<properties
    pageTitle="Caricare un disco rigido virtuale di Windows per Gestione risorse | Microsoft Azure"
    description="Informazioni su come caricare un computer Windows a virtuale disco rigido virtuale locali in Azure, utilizzando il modello di distribuzione di Manager delle risorse. È possibile caricare un file da sia un GRG né una macchina virtuale specializzata."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Caricare un disco rigido virtuale da una macchina virtuale locale in Azure di Windows 


In questo articolo viene illustrato come creare e caricare un disco rigido Windows virtuale (disco rigido virtuale) da utilizzare per la creazione di una macchina virtuale Azure. È possibile caricare un disco rigido virtuale da una macchina virtuale GRG o una macchina virtuale specializzata. 

Per ulteriori informazioni sui dischi e dischi rigidi virtuali in Azure, vedere [informazioni sulla dischi e dischi rigidi virtuali per macchine virtuali](virtual-machines-linux-about-disks-vhds.md).


## <a name="prepare-the-vm"></a>Preparare la macchina virtuale 

È possibile caricare dischi rigidi virtuali GRG e specializzati in Azure. Ogni tipo richiede preparare la macchina virtuale prima di iniziare.

- **Disco rigido virtuale generalized** - un disco rigido virtuale GRG ha tutte le informazioni dell'account personale rimosse mediante Sysprep. Se si prevede di utilizzare il disco rigido virtuale come un'immagine per creare nuove macchine virtuali da, è necessario:
    - [Preparare un disco rigido virtuale di Windows per caricare in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 
    - [Generalize la macchina virtuale utilizzando Sysprep](virtual-machines-windows-generalize-vhd.md). 

- **Disco rigido virtuale specializzata** - un disco rigido virtuale specializzato mantiene gli account utente, applicazioni e altri dati di stato dalla macchina virtuale originale. Se si prevede di utilizzare il disco rigido virtuale come-consiste nel creare una nuova macchina virtuale, assicurarsi che la procedura seguente vengono completata. 
    - [Preparare un disco rigido virtuale di Windows per caricare in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). **Non** generalizzare macchine Virtuali utilizzando Sysprep.
    - Rimuovere tutti gli strumenti di virtualizzazione di guest e gli agenti che vengono installati nella macchina virtuale (ad esempio VMware strumenti).
    - Assicurarsi che la macchina virtuale è configurata per estrarre il relativo indirizzo IP e le impostazioni DNS tramite DHCP. In questo modo che il server Ottiene un indirizzo IP all'interno di VNet all'avvio. 

## <a name="log-in-to-azure"></a>Accedere a Azure

Se si dispone già di PowerShell versione 1.4 o sopra installati di lettura [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

1. Aprire PowerShell Azure e accedere al proprio account Azure. Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.

    ```powershell
    Login-AzureRmAccount
    ```


2. È possibile ottenere l'ID di abbonamento per le proprie sottoscrizioni disponibili.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Impostare la sottoscrizione corretta utilizzando l'ID di sottoscrizione. Sostituire `<subscriptionID>` con l'ID dell'abbonamento corretto.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Ottenere l'account di archiviazione

È necessario un account di archiviazione in Azure per memorizzare l'immagine di macchine Virtuali caricato. È possibile usare un account di archiviazione esistente o crearne uno nuovo. 

Per visualizzare gli account di archiviazione disponibile, digitare:

```powershell
Get-AzureRmStorageAccount
```

Se si desidera utilizzare un account di archiviazione esistente, passare alla sezione [caricare l'immagine di macchina virtuale](#upload-the-vm-vhd-to-your-storage-account) .

Se è necessario creare un account di archiviazione, seguire la procedura seguente:

1. È necessario il nome del gruppo di risorse in cui deve essere creato l'account di archiviazione. Per informazioni su tutti i gruppi di risorse inclusi nell'abbonamento, digitare:

    ```powershell
    Get-AzureRmResourceGroup
    ```

Per creare un gruppo di risorse denominato **myResourceGroup** nell'area **Occidentale degli Stati Uniti** , digitare:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Creare un account di archiviazione denominato **mystorageaccount** in questo gruppo di risorse utilizzando il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx) :

    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" -SkuName "Standard_LRS" -Kind "Storage"
    ```
            
    Valori validi per - SkuName sono:

    - **Standard_LRS** - localmente ridondante dello spazio di archiviazione. 
    - **Standard_ZRS** - archiviazione ridondanti zona.
    - **Standard_GRS** - archiviazione ridondanti geografico. 
    - **Standard_RAGRS** - lo spazio di archiviazione di accesso in lettura geografico ridondante. 
    - **Premium_LRS** - archiviazione localmente ridondanti Premium. 



## <a name="upload-the-vhd-to-your-storage-account"></a>Caricare il disco rigido virtuale al proprio account di archiviazione

Utilizzare il cmdlet [Aggiungi AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) per caricare l'immagine in un contenitore nell'account di archiviazione. In questo esempio carica i file **myVHD.vhd** da `"C:\Users\Public\Documents\Virtual hard disks\"` a un archivio account denominato **mystorageaccount** nel gruppo di risorse **myResourceGroup** . Il file verrà inserito il contenitore denominato **mycontainer** e il nuovo nome di file sarà **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se ha esito positivo, si riceverà una risposta che sarà simile alla seguente:

```
  C:\> Add-AzureRmVhd -ResourceGroupName myResourceGroup -Destination https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
  MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
  MD5 hash calculation is completed.
  Elapsed time for the operation: 00:03:35
  Creating new page blob of size 53687091712...
  Elapsed time for upload: 01:12:49

  LocalFilePath           DestinationUri
  -------------           --------------
  C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

A seconda della connessione di rete e le dimensioni del file disco rigido virtuale, questo comando può richiedere un po' di tempo per completare


## <a name="next-steps"></a>Passaggi successivi

- [Creare una macchina virtuale in Azure da un disco rigido virtuale GRG](virtual-machines-windows-create-vm-generalized.md)
- [Creare una macchina virtuale in Azure da un disco rigido virtuale specializzato](virtual-machines-windows-create-vm-specialized.md) tramite il collegamento come un disco del sistema operativo quando si crea una nuova macchina virtuale.


