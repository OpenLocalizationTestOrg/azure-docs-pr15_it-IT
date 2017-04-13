<properties
    pageTitle="Distribuire un'app nel set di scala macchina virtuale | Microsoft Azure"
    description="Distribuire un'app nel set di scala macchina virtuale"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>


# <a name="upgrade-a-virtual-machine-scale-set"></a>Aggiornare un set di scala macchina virtuale

Questo articolo descrive come possibile fare in modo che un aggiornamento del sistema operativo a una scala di Azure macchina virtuale impostare senza alcuna interruzione. In questo contesto, un aggiornamento del sistema operativo comporta la modifica della versione o SKU del sistema operativo o URI di un'immagine personalizzata. Aggiornamento senza indica il tempo di inattività aggiornando macchine virtuali di uno alla volta o nei gruppi (ad esempio un unico dominio guasto alla volta) invece di tutte contemporaneamente. In questo modo, è possibile mantenere in esecuzione qualsiasi macchine virtuali che non vengono aggiornate.

Per evitare ambiguità, è possibile distinguere tre tipi di aggiornamento del sistema operativo che è possibile eseguire:

- Modificare la versione o SKU di un'immagine della piattaforma. Ad esempio la modifica Ubuntu versione 14.04.2-LTS 14.04.201506100 a 14.04.201507060, modifica o Ubuntu 15.10/più tardi SKU alla 16.04.0-LTS/latest. Questo scenario viene descritto in questo articolo.

- Modificare l'URI che fa riferimento a una nuova versione di un'immagine personalizzata creata (**proprietà** > **virtualMachineProfile** > **storageProfile** > **osDisk** > **immagine** > **uri**). Questo scenario viene descritto in questo articolo.

- Aggiornamento del sistema operativo all'interno di una macchina virtuale (in questo esempi di installare una patch di protezione ed eseguire Windows Update). Questo scenario è supportato ma non contenuto in questo articolo.

Le prime due opzioni sono supportati i requisiti rientranti in questo articolo. È necessario creare una nuova scala imposta per eseguire la terza opzione.

Imposta scala macchina virtuale che vengono distribuite come parte di un cluster di [Azure servizio tessuti](https://azure.microsoft.com/services/service-fabric/) non è incluse qui.

La sequenza di base per la modifica del sistema operativo versione/SKU di un'immagine di piattaforma o URI di un'immagine personalizzata simile al seguente:

1. È possibile ottenere il modello di set di scala macchina virtuale.

2. Modificare la versione, SKU o URI valore nel modello.

3. Aggiornare il modello.

4. Eseguire una chiamata di *manualUpgrade* in macchine virtuali nel set di scala. Questo passaggio è pertinente solo se *upgradePolicy* è impostato su **manuale** nel set di scala. Se è impostata su **automatico**, tutte le macchine virtuali vengono aggiornate contemporaneamente, causando un tempo di inattività.


Con queste informazioni di base in considerazione, vediamo come è possibile aggiornare la versione di una scala impostare PowerShell e l'API REST. In questi esempi vengono illustrate nel caso di un'immagine della piattaforma, ma in questo articolo fornisce informazioni sufficienti adattare il processo di un'immagine personalizzata.

## <a name="powershell"></a>PowerShell##

In questo esempio viene aggiornato una scala di macchina virtuale di Windows impostata per la nuova versione 4.0.20160229. Dopo aver aggiornato il modello, viene eseguita un'istanza di macchina virtuale Aggiorna uno alla volta.

```powershell
$rgname = "myrg"
$vmssname = "myvmss"
$newversion = "4.0.20160229"
$instanceid = "1"

# get the VMSS model
$vmss = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname

# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.imageReference.version = $newversion

# update the virtual machine scale set model
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss

# now start updating instances
Update-AzureRmVmssInstance -ResourceGroupName $rgname -VMScaleSetName $vmssname -InstanceId $instanceId
```

Se si sta aggiornando URI per un'immagine personalizzata anziché la modifica di una versione di immagine della piattaforma, sostituire la riga "impostare la nuova versione" con simile al seguente:

```powershell
# set the new version in the model data
$vmss.virtualMachineProfile.storageProfile.osDisk.image.uri= $newURI
```


## <a name="the-rest-api"></a>API REST

Ecco un paio di Python esempi che utilizzano l'API REST di Azure per implementare un aggiornamento di versione del sistema operativo. Entrambi utilizzare la libreria leggera [azurerm](https://pypi.python.org/pypi/azurerm) delle funzioni spaziale API REST Azure per eseguire un recupero sul modello di set di scala, seguito da un caricamento di un modello aggiornato. Soddisfino anche visualizzazioni istanze macchina virtuale per identificare le macchine virtuali dal dominio di aggiornamento.

### <a name="vmssupgrade"></a>Vmssupgrade

 [Vmssupgrade](https://github.com/gbowerman/vmsstools) è uno script Python che viene utilizzato per distribuire un aggiornamento del sistema operativo a una scala di macchina virtuale in esecuzione imposta un dominio di aggiornamento contemporaneamente.

![Script di Vmssupgrade per la scelta macchine virtuali o un dominio di aggiornamento](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssupgrade-screenshot.png)

Questo script consente di scegliere specifiche macchine virtuali consente di aggiornare o specificare un dominio di aggiornamento. Supporta la modifica di una versione di immagine della piattaforma o URI di un'immagine personalizzata.

### <a name="vmsseditor"></a>Vmsseditor

[Vmsseditor](https://github.com/gbowerman/vmssdashboard) è un editor generico per i set di scala macchina virtuale che mostra lo stato del computer virtuale come un heatmap in un'unica riga rappresenta un dominio di aggiornamento. Tra le altre cose, è possibile aggiornare il modello per un set di scala con una nuova versione, SKU o immagine personalizzata URI e quindi selezionare domini guasto per l'aggiornamento. Quando si esegue questa operazione, tutte le macchine virtuali di tale dominio aggiornamento vengono aggiornate nel nuovo modello. In alternativa, è possibile eseguire un aggiornamento in base alla dimensione batch preferito.  

La schermata seguente mostra un modello di una scala impostare per Ubuntu 14.04-2LTS versione 14.04.201507060. Molte altre opzioni sono stati aggiunti a questo strumento da quando è stata eseguita questa schermata.

![Modello di Vmsseditor di una scala impostare per Ubuntu 14.04-2LTS](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor1.png)

Dopo **l'aggiornamento** e **Ottenere informazioni dettagliate**, macchine virtuali in UD 0 avviate da aggiornare.

![Aggiornamento con Vmsseditor in corso](./media/virtual-machine-scale-sets-upgrade-scale-set/vmssEditor2.png)
