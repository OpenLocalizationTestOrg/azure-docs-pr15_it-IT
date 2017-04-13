<properties
   pageTitle="Gestire le macchine virtuali tramite PowerShell Azure | Microsoft Azure"
   description="Informazioni su comandi che è possibile utilizzare per automatizzare le attività di gestione delle macchine virtuali."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gestire le macchine virtuali tramite PowerShell Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


È possibile automatizzare molte attività eseguite ogni giorno per gestire le macchine virtuali utilizzando i cmdlet PowerShell di Azure. In questo articolo offre i comandi di esempio per le attività più semplice e collegamenti ad articoli che mostrano i comandi per attività più complesse.

>[AZURE.NOTE] Se non è ancora stato installato e configurato PowerShell di Azure, è possibile visualizzare istruzioni vedere l'articolo [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Come usare i comandi di esempio
È necessario sostituire parte del testo nei comandi con testo che è appropriato per l'ambiente. I simboli di indicano il testo è necessario sostituire < e >. Quando si sostituisce il testo, rimuovere i simboli e lascia le virgolette in posizione.

## <a name="get-a-vm"></a>Ottenere una macchina virtuale
Si tratta di un'attività di base in cui si userà spesso. Usarlo per ottenere informazioni su una macchina virtuale, eseguire le attività in una macchina virtuale o ottenere l'output per l'archiviazione in una variabile.

Per informazioni su macchina virtuale, eseguire questo comando, sostituendo tutto racchiuso tra virgolette, inclusi i caratteri < e >:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Per archiviare l'output in una variabile $vm, eseguire:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Accedere a una macchina virtuale basato su Windows

Eseguire questi comandi:

>[AZURE.NOTE] È possibile ottenere il nome del servizio di macchine virtuali e cloud dalla visualizzazione del comando **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Interrompere una macchina virtuale

Eseguire il comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilizzare questo parametro per mantenere la virtuale indirizzi IP del servizio cloud, nel caso in cui è l'ultimo macchine Virtuali di tale servizio cloud. <br><br> Se si utilizza il parametro StayProvisioned, verrà comunque effettuata per la macchina virtuale.

## <a name="start-a-vm"></a>Avviare una macchina virtuale

Eseguire il comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Allegare un disco dati
Questa operazione richiede alcuni passaggi. Prima di tutto, utilizzare la * * * Aggiungi-AzureDataDisk * * * cmdlet per aggiungere il disco all'oggetto $vm. È quindi possibile utilizzare i cmdlet di **Aggiornamento AzureVM** per aggiornare la configurazione della macchina virtuale.

È anche necessario decidere se si desidera allegare un nuovo disco o uno che contiene i dati. Per un nuovo disco, il comando Crea file con estensione vhd e associa.

Per allegare un nuovo disco, eseguire il comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Per allegare un disco dati esistente, eseguire il comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Per allegare dischi di dati da un file con estensione vhd esistente nell'archiviazione blob, eseguire il comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Creare una macchina virtuale basato su Windows

Per creare una nuova macchina virtuale basato su Windows Azure, usare le istruzioni in [Usare Azure PowerShell per creare e preconfigurare macchine virtuali basato su Windows](virtual-machines-windows-classic-create-powershell.md). I passaggi in questo argomento è tramite la creazione di un PowerShell Azure comando impostato che crea una macchina virtuale basato su Windows che possa essere preconfigurata:

- Con l'appartenenza di dominio Active Directory.
- Con dischi aggiuntivi.
- Come membro di un oggetto esistente bilanciamento del carico imposta.
- Con un indirizzo IP statico.
