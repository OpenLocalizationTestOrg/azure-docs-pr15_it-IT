<properties 
    pageTitle="Ridistribuire macchine virtuali Linux | Microsoft Azure" 
    description="In questo articolo viene descritto come ridistribuire macchine virtuali Linux per limitare i problemi di connessione SSH." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Ridistribuire macchina virtuale al nuovo livello di nodo Azure

Se si dispone di stato con difficoltà può contribuire alla risoluzione dei problemi SSH o applicazione l'accesso a una Azure macchine (), ridistribuire la macchina virtuale. Quando si ridistribuisce una macchina virtuale, sposta la macchina virtuale in un nuovo nodo all'interno dell'infrastruttura di Azure e quindi si torna nella mantenendo tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una macchina virtuale con Azure CLI o il portale di Azure.

> [AZURE.NOTE] Dopo che si ridistribuisce una macchina virtuale, si perde il disco temporaneo e gli indirizzi IP dinamici associati interfaccia virtuali vengono aggiornati. 


## <a name="using-azure-cli"></a>Usa CLI Azure

Verificare che sia [Più recente CLI Azure installato](../xplat-cli-install.md) nel computer e attiva la modalità di gestione risorse (`azure config mode arm`).

Usare il seguente comando CLI Azure per ridistribuire la macchina virtuale:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

È possibile visualizzare lo stato della modifica macchine Virtuali come passano attraverso il processo di Ridistribuisci. Il `PowerState` della macchina virtuale va da "Running" a "aggiornamento', 'Avviando' e infine 'esecuzione' come passano attraverso il processo di ridistribuzione in un nuovo host. Controllare lo stato di macchine virtuali all'interno di un gruppo di risorse con:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Passaggi successivi
Se si sono verificati problemi durante la connessione per la macchina virtuale, è possibile trovare informazioni sulla [risoluzione dei problemi di connessioni SSH](virtual-machines-linux-troubleshoot-ssh-connection.md) o [SSH dettagliate risoluzione dei problemi](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Se non si accede a un'applicazione di una macchina virtuale, è inoltre possibile leggere i [problemi di risoluzione dei problemi delle applicazioni](virtual-machines-linux-troubleshoot-app-connection.md).