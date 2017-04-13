<properties
   pageTitle="Come ridimensionare una VM Linux | Microsoft Azure"
   description="Come scalare o modificare la scala verso il basso una macchina virtuale Linux, modificando la dimensione di memoria virtuale."
   services="virtual-machines-linux"
   documentationCenter="na"
   authors="mikewasson"
   manager="timlt"
   editor=""
   tags=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="mikewasson"/>


# <a name="how-to-resize-a-linux-vm"></a>Come ridimensionare una VM Linux

## <a name="overview"></a>Panoramica 

Dopo avere stabilito una macchine (), potete ridimensionare la macchina virtuale verso l'alto o verso il basso per modificare la [dimensione memoria virtuale][vm-sizes]. In alcuni casi, è necessario rendere nuovamente la macchina virtuale prima di tutto. Questo problema può verificarsi se la nuova dimensione non è disponibile nel cluster hardware che ospita la macchina virtuale.

In questo articolo viene illustrato come ridimensionare una VM Linux Usa [CLI Azure][azure-cli].

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modello di distribuzione classica.


## <a name="resize-a-linux-vm"></a>Ridimensionare una macchina virtuale Linux 

Per ridimensionare una macchina virtuale, eseguire i passaggi seguenti.

1. Eseguire il seguente comando CLI. Questo comando Elenca le dimensioni di macchine Virtuali disponibili nel cluster hardware ospita la macchina virtuale.

    ```
    azure vm sizes -g <resource-group> --vm-name <vm-name>
    ```

2. Se le dimensioni desiderate sono elencata, eseguire il seguente comando per ridimensionare la macchina virtuale.

    ```
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    ```

    La macchina virtuale verrà riavviato durante questo processo. Dopo il riavvio, verranno rimappati il sistema operativo esistente e dischi di dati. Qualsiasi parte del disco temporaneo andranno perse.

    Utilizzare la `--enable-boot-diagnostics` opzione Abilita [la diagnostica avvio][boot-diagnostics], agli utenti di accedere errori relativi all'avvio.

3. In caso contrario, se la dimensione desiderata non è elencata, eseguire i comandi seguenti per rilasciare la macchina virtuale, ridimensionarla e quindi riavviare la macchina virtuale.

    ```
    azure vm deallocate -g <resource-group> <vm-name>
    azure vm set -g <resource-group> --vm-size <new-vm-size> -n <vm-name>  
        --enable-boot-diagnostics --boot-diagnostics-storage-uri
        https://<storage-account-name>.blob.core.windows.net/ 
    azure vm start -g <resource-group> <vm-name>
    ```

   > [AZURE.WARNING] Deallocazione macchina virtuale rilascia anche tutti gli indirizzi IP dinamici assegnati a macchina virtuale. Il disco del sistema operativo e dati non è interessato.
   
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori scalabilità, eseguire più istanze di macchine Virtuali e scalabilità. Per ulteriori informazioni, vedere [automaticamente le dimensioni computer Linux in un Set di scala macchina virtuale][scale-set]. 

<!-- links -->
   
[azure-cli]: ../xplat-cli-install.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[scale-set]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md 
[vm-sizes]: virtual-machines-linux-sizes.md