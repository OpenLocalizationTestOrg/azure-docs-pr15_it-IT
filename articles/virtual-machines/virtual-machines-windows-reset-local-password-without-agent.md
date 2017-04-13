<properties
   pageTitle="Reimpostare una password di Windows locale quando non è installato agente guest Azure | Microsoft Azure"
   description="Come reimpostare la password di un account utente di Windows locale quando l'agente di Azure guest non è installato o funzionante in una macchina virtuale"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/05/2016"
   ms.author="iainfou"/>

# <a name="how-to-reset-local-windows-password-for-azure-vm"></a>Come reimpostare la password di Windows locale per macchine Virtuali di Azure
È possibile reimpostare la password di Windows locale di una macchina virtuale Azure tramite che il [portale di Azure e Azure PowerShell](virtual-machines-windows-reset-rdp.md) fornito che è installato l'agente di Azure guest. Questo metodo è il modo migliore per reimpostare una password per una macchina virtuale Azure. Se si verificano problemi con l'agente di Azure guest non risponde o non funziona per l'installazione dopo il caricamento di un'immagine personalizzata, è possibile reimpostare manualmente una password di Windows. In questo articolo viene illustrato come reimpostare una password dell'account locale associando disco virtuale di origine del sistema operativo a un'altra macchina virtuale. 

> [AZURE.WARNING] Utilizzare questo processo solo come ultima risorsa. È consigliabile reimpostare una password prima tramite il [portale di Azure e Azure PowerShell](virtual-machines-windows-reset-rdp.md) .


## <a name="overview-of-the-process"></a>Panoramica del processo
Passaggi di base per l'esecuzione di una locale reimpostazione della password per una macchina virtuale di Windows in Azure quando non è possibile accedere all'agente guest Azure è il seguente:

- Eliminare l'origine macchine Virtuali. Dischi virtuali vengono mantenuti.
- Allegare disco rigido del sistema operativo della macchina virtuale origine a un'altra macchina virtuale all'interno di abbonamento Azure. Questa macchina virtuale è definita la macchina virtuale sulla risoluzione dei problemi.
- Creare alcuni file di configurazione sul disco rigido del sistema operativo di origine Virtual Machine macchina virtuale sulla risoluzione dei problemi.
- Scollegare del disco rigido del sistema operativo della macchina virtuale dalla macchina virtuale sulla risoluzione dei problemi.
- Utilizzare un modello di Manager delle risorse per creare una macchina virtuale utilizzando il disco virtuale originale.
- Quando si avvia la nuova macchina virtuale, i file di configurazione creati aggiornare la password dell'utente necessario.


## <a name="detailed-steps"></a>Procedura dettagliata
È consigliabile reimpostare una password tramite il [portale di Azure e Azure PowerShell](virtual-machines-windows-reset-rdp.md) prima di provare la procedura seguente. Accertarsi di avere una copia di backup della macchina virtuale prima di iniziare. 

1. Eliminare la macchina virtuale interessata nel portale di Azure. La macchina virtuale solo se si elimina i metadati, il riferimento di macchine Virtuali all'interno di Azure. Dischi virtuali vengono mantenuti quando viene eliminata la macchina virtuale:

    - Selezionare la macchina virtuale nel portale di Azure, fare clic su *Elimina*:

    ![Eliminare macchine Virtuali esistenti](./media/virtual-machines-windows-reset-local-password-without-guest-agent/delete_vm.png)

2. Collegare il disco di sistema operativo di origine Virtual Machine per la risoluzione dei problemi macchine Virtuali. Risoluzione dei problemi macchina virtuale deve essere nella stessa regione disco rigido del sistema operativo di origine Virtual Machine (ad esempio `West US`):

    - Selezionare la macchina virtuale sulla risoluzione dei problemi nel portale di Azure. Fare clic su *dischi* | *allegare esistente*:

    ![Allegare un disco esistente](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_attach_existing.png)

    Selezionare *Il File disco rigido virtuale* e quindi selezionare l'account di archiviazione che contiene l'origine in macchine Virtuali:

    ![Selezionare l'account di archiviazione](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_storageaccount.PNG)

    Selezionare il contenitore di origine. Il contenitore di origine è in genere *dischi rigidi virtuali*:

    ![Selezionare lo spazio di archiviazione contenitore](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_container.png)

    Selezionare il disco rigido virtuale OS allegare. Fare clic su *Seleziona* per completare il processo:

    ![Selezionare il disco virtuale di origine](./media/virtual-machines-windows-reset-local-password-without-guest-agent/disks_select_source_vhd.png)

3. Connettere la macchina virtuale sulla risoluzione dei problemi con Desktop remoto e assicurarsi che il disco di sistema operativo di origine Virtual Machine è visibile:

    - Selezionare la macchina virtuale sulla risoluzione dei problemi nel portale di Azure e fare clic su *Connetti*.
    - Aprire il file RDP per il download. Immettere il nome utente e la password della macchina virtuale sulla risoluzione dei problemi.
    - In Esplora File cercare il disco di dati allegato. Se l'origine della macchina virtuale è il disco solo i dati associato a macchina virtuale sulla risoluzione dei problemi, deve essere unità f:

    ![Visualizzare il disco di dati allegato](./media/virtual-machines-windows-reset-local-password-without-guest-agent/troubleshooting_vm_fileexplorer.png)

4. Creare `gpt.ini` in `\Windows\System32\GroupPolicy` in unità di origine Virtual Machine (se è presente GPT, rinominare gpt.ini.bak):

    > [AZURE.WARNING] Assicurarsi di non accidentalmente creare i file seguenti in C:\Windows, l'unità di sistema operativo per la macchina virtuale sulla risoluzione dei problemi. Creare i file seguenti nell'unità del sistema operativo per l'origine in macchine Virtuali collegate come un disco dati.

    - Aggiungere le seguenti righe nel `gpt.ini` file creato:

    ```
    [General]
    gPCFunctionalityVersion=2
    gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
    Version=1
    ```

    ![Creare GPT](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_gpt_ini.png)
 
5. Creare `scripts.ini` in `\Windows\System32\GroupPolicy\Machine\Scripts`. Assicurarsi che vengono visualizzate le cartelle nascoste. Se necessario, creare la `Machine` o `Scripts` cartelle.

    - Aggiungere le seguenti righe di `scripts.ini` file creato:

    ```
    [Startup]
    0CmdLine=C:\Windows\System32\FixAzureVM.cmd
    0Parameters=
    ```

    ![Creare scripts.ini](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_scripts_ini.png)
 
6. Creare `FixAzureVM.cmd` in `\Windows\System32` con il contenuto seguente, sostituendo `<username>` e `<newpassword>` con i valori:

    ```
    NET USER <username> <newpassword>
    ```

    ![Creare FixAzureVM.cmd](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_fixazure_cmd.png)

    Quando si definisce la nuova password, è necessario soddisfare i requisiti di complessità delle password configurata per la macchina virtuale.

7. Nel portale di Azure, scollegare il disco da macchina virtuale sulla risoluzione dei problemi:

    - Selezionare la macchina virtuale sulla risoluzione dei problemi nel portale di Azure, fare clic su *dischi*.
    - Selezionare il disco di dati allegato nel passaggio 2, fare clic su *Disconnetti*:

    ![Scollegare disco](./media/virtual-machines-windows-reset-local-password-without-guest-agent/detach_disk.png)

8. Prima di creare una macchina virtuale, si ottengono URI da disco di origine del sistema operativo:

    - Selezionare l'account di archiviazione nel portale di Azure, fare clic su *BLOB*.
    - Selezionare il contenitore. Il contenitore di origine è in genere *dischi rigidi virtuali*:

    ![Selezionare lo spazio di archiviazione blob di account](./media/virtual-machines-windows-reset-local-password-without-guest-agent/select_storage_details.png)

    Selezionare il disco rigido virtuale OS macchine Virtuali di origine e fare clic sul pulsante *Copia* accanto al nome di *URL* :

    ![Copiare disco URI](./media/virtual-machines-windows-reset-local-password-without-guest-agent/copy_source_vhd_uri.png)

9. Creare una macchina virtuale dal disco rigido del sistema operativo di origine Virtual Machine:

    - Utilizzare [questo modello di gestione risorse di Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd) per creare una macchina virtuale da un disco rigido virtuale specializzato. Fare clic sul `Deploy to Azure` pulsante per aprire il portale di Azure con i dettagli basato su modelli compilati automaticamente.
    - Se si desidera mantenere tutte le impostazioni precedenti per la macchina virtuale, selezionare *Modifica modello* per fornire il VNet esistente, alla subnet, scheda di rete o IP pubblico.
    - Nel `OSDISKVHDURI` casella di testo parametro, Incolla URI dell'origine del disco rigido virtuale ottenere nel passaggio precedente:

    ![Creare una macchina virtuale da modello](./media/virtual-machines-windows-reset-local-password-without-guest-agent/create_new_vm_from_template.png)

10. Dopo la nuova macchina virtuale è in esecuzione, connettersi a macchine Virtuali mediante Desktop remoto con la nuova password specificata nel `FixAzureVM.cmd` script.

11. Da sessione remota per la nuova macchina virtuale, rimuovere i file seguenti per pulire l'ambiente:

    - Da %Windir%\System32.
        - rimuovere FixAzureVM.cmd
    - Da %windir%\System32\GroupPolicy\Machine\
        - rimuovere scripts.ini
    - Da %windir%\System32\GroupPolicy
        - rimuovere GPT (GPT esistenti prima, se è stato rinominato in gpt.ini.bak, Rinomina il file con estensione bak al GPT)

## <a name="next-steps"></a>Passaggi successivi
Se non è ancora possibile connettersi con Desktop remoto, vedere la [Guida alla risoluzione dei problemi di RDP](virtual-machines-windows-troubleshoot-rdp-connection.md). La [Guida alla risoluzione dei problemi dettagliata RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) è simile alla risoluzione dei problemi relativi a metodi anziché passaggi specifici. È anche possibile [aprire una richiesta di supporto Azure](https://azure.microsoft.com/support/options/) per ricevere assistenza pratica.