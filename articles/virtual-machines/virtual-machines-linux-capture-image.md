<properties
    pageTitle="Acquisire un VM Linux da utilizzare come modello | Microsoft Azure"
    description="Informazioni su come acquisire e generalizzare un'immagine di una basati su Linux Azure macchine () creata con il modello di distribuzione di Manager delle risorse di Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="iainfou"/>


# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Acquisire una macchina virtuale Linux in esecuzione in Azure

Seguire i passaggi descritti in questo articolo per generalizzare e acquisire il Azure Linux macchine () nel modello di distribuzione Manager delle risorse. Quando si generalizza la macchina virtuale, rimuovere le informazioni sull'account personale e preparare la macchina virtuale da utilizzare come immagine. Quindi possibile acquisire un'immagine GRG virtuale sul disco rigido (disco rigido virtuale) per il sistema operativo, dischi rigidi virtuali per dischi di dati allegato e un [modello di Manager delle risorse](../azure-resource-manager/resource-group-overview.md) per le nuove distribuzioni macchine Virtuali. 

Per creare macchine virtuali mediante l'immagine, impostare le risorse di rete per ogni nuova macchina virtuale e utilizzare il modello (file JavaScript Object Notation o JSON,) per distribuire dalle immagini acquisite disco rigido virtuale. In questo modo, è possibile replicare una macchina virtuale con la configurazione del software corrente, allo stesso modo che si utilizzano immagini in Azure Marketplace.

>[AZURE.TIP]Se si desidera creare una copia delle VM Linux esistente con lo stato specifico per il backup o il debug, vedere [creare una copia di una macchina virtuale Linux in esecuzione in Azure](virtual-machines-linux-copy-vm.md). E se si desidera caricare un VHD Linux da una macchina virtuale locale, vedere [caricare e creare una VM Linux da disco personalizzata immagine](virtual-machines-linux-upload-vhd.md).  

## <a name="before-you-begin"></a>Prima di iniziare

Verificare che siano soddisfatti i prerequisiti seguenti:

* **Macchine Virtuali di azure creati nel modello di distribuzione Manager delle risorse** , se non è stata creata una VM Linux, è possibile usare il [portale](virtual-machines-linux-quick-create-portal.md), [CLI Azure](virtual-machines-linux-quick-create-cli.md)o [modelli di Manager delle risorse](virtual-machines-linux-cli-deploy-templates.md). 

    Configurare la macchina virtuale in base alle esigenze. Ad esempio, [aggiungere dati dischi](virtual-machines-linux-add-disk.md), applicare gli aggiornamenti e installare le applicazioni. 
* **Azure CLI** - installa [Azure CLI](../xplat-cli-install.md) su un computer locale.

## <a name="step-1-remove-the-azure-linux-agent"></a>Passaggio 1: Rimuovere l'agente di Azure Linux

Prima di tutto, è possibile eseguire il comando **waagent** con il parametro di **annullare l'implementazione** in VM Linux. Questo comando Elimina file e dati per preparare la macchina virtuale per generalizzazione. Per informazioni dettagliate, vedere la [Guida di Azure Linux agente utente](virtual-machines-linux-agent-user-guide.md).

1. Connettere il VM Linux utilizzando un client SSH.

2. Nella finestra di SSH, digitare il comando seguente:

    ```
    sudo waagent -deprovision+user
    ```
    >[AZURE.NOTE] Eseguire solo questo comando in una macchina virtuale che si desidera acquisire come immagine. Non garantisce che l'immagine è deselezionata di tutte le informazioni riservate o è adatto per la ridistribuzione.

3. Digitare **y** per continuare. È possibile aggiungere il **-forzare** parametro per evitare questo passaggio di conferma.

4. Al termine dell'esecuzione del comando, digitare **uscire**. Questo passaggio si chiude il client SSH.

    
## <a name="step-2-capture-the-vm"></a>Passaggio 2: Raccogliere la macchina virtuale

Utilizzare CLI Azure per generalizzare e acquisire la macchina virtuale. Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. Esempi di nomi di parametro includono **myResourceGroup**, **myVnet**e **myVM**.

5. Dal computer locale, aprire il CLI Azure e [accesso al proprio abbonamento Azure](../xplat-cli-connect.md). 

6. Assicurarsi che siano in modalità di gestione risorse.

    ```
    azure config mode arm
    ```

7. Arrestare la macchina virtuale che già stato rimosso con deprovisioning utilizzando il comando seguente:

    ```
    azure vm deallocate -g MyResourceGroup -n myVM
    ```

8. Generalizzare la macchina virtuale con il comando seguente:

    ```
    azure vm generalize -g MyResourceGroup -n myVM
    ```

9. Eseguire il comando **acquisire macchine virtuali di azure** , che acquisisce la macchina virtuale. Nell'esempio seguente, dischi rigidi virtuali immagini acquisiti con i nomi che iniziano con **MyVHDNamePrefix**e l'opzione **-t** specifica un percorso per il modello **MyTemplate.json**. 

    ```
    azure vm capture MyResourceGroup MyResourceGroup MyVHDNamePrefix -t MyTemplate.json
    ```

    >[AZURE.IMPORTANT]I file di immagine disco rigido virtuale vengano creati per impostazione predefinita nello stesso account archiviazione macchina virtuale originale. Utilizzare lo *stesso account di archiviazione* per archiviare i dischi rigidi virtuali per le nuove macchine virtuali create dall'immagine. 

6. Per trovare la posizione dell'immagine acquisita, aprire il modello JSON in un editor di testo. In **storageProfile**, individuare l' **uri** dell' **immagine** che si trova nel contenitore di **sistema** . Ad esempio, l'URI dell'immagine su disco del sistema operativo è simile a`https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Passaggio 3: Creare una macchina virtuale dall'immagine acquisita
A questo punto è possibile utilizzare l'immagine con un modello per creare una VM Linux. Questa procedura viene illustrato come utilizzare CLI Azure e il modello di file JSON acquisiti per creare la macchina virtuale in una nuova rete virtuale.

### <a name="create-network-resources"></a>Creare le risorse di rete

Per utilizzare il modello, è innanzitutto necessario configurare una rete virtuale e scheda di rete per la nuova macchina virtuale. È consigliabile che si crea un gruppo di risorse per le risorse nella posizione in cui è archiviato l'immagine di macchina virtuale. Eseguire i comandi nomi simili a quelli seguenti, altro per le risorse e un percorso di Azure appropriato ("centralus" in questi comandi):

    azure group create MyResourceGroup1 -l "centralus"

    azure network vnet create MyResourceGroup1 myVnet -l "centralus"

    azure network vnet subnet create MyResourceGroup1 myVnet mySubnet

    azure network public-ip create MyResourceGroup1 myIP -l "centralus"

    azure network nic create MyResourceGroup1 myNIC -k mySubnet -m myVnet -p myIP -l "centralus"

### <a name="get-the-id-of-the-nic"></a>Ottenere l'Id della NIC

Per distribuire una macchina virtuale dall'immagine tramite JSON salvati durante l'acquisizione, è necessario l'Id della scheda di rete. Ottenerlo eseguendo il comando seguente:

    azure network nic show MyResourceGroup1 myNIC

L' **Id** nell'output è simile a`/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`



### <a name="create-a-vm"></a>Creare una macchina virtuale
Eseguire il comando seguente per creare la macchina virtuale dall'immagine acquisita macchine Virtuali. Utilizzare il parametro **-f** per specificare il percorso del file JSON modello che è stato salvato.

    azure group deployment create MyResourceGroup1 MyDeployment -f MyTemplate.json

Nell'output del comando, viene chiesto di fornire un nuovo nome macchine Virtuali, il nome utente amministratore e la password e l'Id della scheda NIC creata in precedenza.

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    vmName: myNewVM
    adminUserName: myAdminuser
    adminPassword: ********
    networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic

Nell'esempio seguente viene visualizzato per una corretta distribuzione:

    + Inizializzazione configurazioni di modello e parametri
    + Creazione di le informazioni sulla distribuzione: creato modello distribuzione xxxxxxx
    + In attesa per la distribuzione completare dati: DeploymentName: MyDeployment dati: ResourceGroupName: MyResourceGroup1 dati: ProvisioningState: ha avuto esito positivo dati: Timestamp: xxxxxxx dati: modalità: dati incrementali: nome del tipo di valore


    data:    ------------------  ------------  -------------------------------------

    dati: vmName stringa myNewVM


    dati: vmSize Standard_D1 stringa


    dati: adminUserName stringa myAdminuser


    dati: adminPassword SecureString non definito


    dati: networkInterfaceId info /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic stringa: distribuzione del gruppo di creare un comando OK

### <a name="verify-the-deployment"></a>Verificare la distribuzione

A questo punto SSH alla macchina virtuale creati per verificare la distribuzione e iniziare a usare la nuova macchina virtuale. Per connettersi tramite SSH, trovare l'indirizzo IP del macchine Virtuali creato eseguendo il comando seguente:

    azure network public-ip show MyResourceGroup1 myIP

L'indirizzo IP pubblico viene elencato nell'output del comando. Per impostazione predefinita è connettersi a VM Linux da SSH sulla porta 22.

## <a name="create-additional-vms"></a>Creare altre macchine virtuali
Utilizzare il modello e l'immagine acquisita per distribuire altre macchine virtuali mediante la procedura nella sezione precedente. Altre opzioni per creare macchine virtuali dall'immagine includano utilizzando un modello di Guida introduttiva o eseguire il comando **creare macchine virtuali di azure** .

### <a name="use-the-captured-template"></a>Utilizzare il modello acquisito

Per utilizzare il modello e l'immagine acquisita, seguire questa procedura (dettagliata nella sezione precedente):

* Assicurarsi che l'immagine di macchine Virtuali è lo stesso account di archiviazione che ospita disco rigido virtuale la Virtual Machine.
* Copiare il file del modello JSON e specificare un nome univoco per il disco del sistema operativo del disco rigido virtuale macchine Virtuali di nuovo (o dischi rigidi virtuali). Ad esempio, specificare un nome univoco per il disco rigido virtuale, simile a **osDisk** il **storageProfile**, in **disco rigido virtuale** **uri**,`https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Creare una scheda di rete in stesso o un'altra rete virtuale.
* Utilizzando il file di modello modificato JSON, creare una distribuzione in gruppo di risorse in cui configurare la rete virtuale.

### <a name="use-a-quickstart-template"></a>Usare un modello di Guida introduttiva

Se si desidera che la rete configurare automaticamente quando si crea una macchina virtuale dall'immagine, è possibile specificare le risorse in un modello. Ad esempio, vedere il [modello 101 macchine virtuali dall'utente immagine](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) GitHub. Questo modello consente di creare una macchina virtuale dalla immagine personalizzata e virtuali necessarie, indirizzo IP pubblico e risorse NIC. Per una procedura dettagliata di utilizzo del modello nel portale di Azure, vedere [come creare una macchina virtuale da un'immagine personalizzata con un modello di Manager delle risorse](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Usare la macchina virtuale azure creare comando

In genere è più semplice utilizzare un modello di Manager delle risorse per creare una macchina virtuale dall'immagine. Tuttavia, è possibile creare la macchina virtuale _imperativo_ usando il comando **azure macchine virtuali create** con **-Q** (**-immagine urn**) parametro. Se si usa questo metodo, è anche possibile passare il parametro **-d** (**del disco rigido del sistema operativo-disco virtuale**) per specificare il percorso del file con estensione vhd OS per la nuova macchina virtuale. In questo file deve essere nel contenitore di dischi rigidi virtuali dell'account di archiviazione in cui è archiviato il file di immagine disco rigido virtuale. Il comando Copia il disco rigido virtuale per la nuova macchina virtuale automaticamente al contenitore di **dischi rigidi virtuali** .

Prima esecuzione **azure macchine virtuali create** con l'immagine, eseguire le operazioni seguenti:

1.  Creare un gruppo di risorse o identificare un gruppo di risorse esistente per la distribuzione.

2.  Creare una risorsa indirizzo IP pubblica e una risorsa NIC per la nuova macchina virtuale. Per la procedura creare una rete virtuale, l'indirizzo IP pubblico e NIC tramite CLI, vedere questo articolo. (**creare macchine virtuali azure** anche possibile creare una scheda di rete, ma è necessario passare altri parametri per una rete virtuale e subnet)


Eseguire un comando che passa URI per il nuovo file disco rigido virtuale del sistema operativo e l'immagine esistente. In questo esempio viene creata una dimensione macchine Virtuali Standard_A1 nell'area orientale degli Stati Uniti.

    azure vm create MyResourceGroup1 myNewVM eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u myAdminname -p myPassword -f myNIC

Per le opzioni di comandi aggiuntivi, eseguire `azure help vm create`.

## <a name="next-steps"></a>Passaggi successivi

Per gestire le macchine virtuali con CLI, vedere le attività di [Distribuisci e gestire macchine virtuali utilizzando i modelli di gestione di risorse Azure e CLI Azure](virtual-machines-linux-cli-deploy-templates.md).
