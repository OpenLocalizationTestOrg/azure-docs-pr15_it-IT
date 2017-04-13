<properties
 pageTitle="Gestire i nodi di calcolo cluster HPC Pack | Microsoft Azure"
 description="Informazioni sugli strumenti di script di PowerShell per aggiungere, rimuovere, avviare e arrestare HPC Pack cluster nodi di calcolo Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="07/22/2016"
 ms.author="danlep"/>

# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Gestire il numero e la disponibilità dei nodi di calcolo in un cluster HPC Pack in Azure

Se è stato creato un cluster HPC Pack in macchine virtuali di Azure, è consigliabile modi per aggiungere, rimuovere, avviare (provisioning) o interrompere il nodo (annullare l'implementazione) un numero di calcolo macchine virtuali nel cluster con facilmente. Per eseguire queste operazioni, eseguire gli script di PowerShell Azure che vengono installati in macchine Virtuali di nodo principale. Questi script consentono di controllare il numero e la disponibilità delle risorse cluster HPC Pack in modo che è possibile controllare i costi.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## <a name="prerequisites"></a>Prerequisiti

* **Cluster HPC Pack in macchine virtuali di Azure** - creare un cluster HPC Pack nel modello di distribuzione classica utilizzando almeno HPC Pack 2012 R2 Update 1. Ad esempio, è possibile automatizzare la distribuzione utilizzando l'immagine di macchine Virtuali Pack HPC corrente in uno script di PowerShell Azure e Azure Marketplace. Per informazioni e i prerequisiti, vedere [creare un Cluster HPC con lo script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md).

    Dopo la distribuzione, trovare il nodo script di gestione in % CCP\_HOME % directory bin sul nodo principale. È necessario eseguire ogni script come amministratore.

* **Azure pubblicare il certificato di file o la gestione delle impostazioni** , è necessario eseguire una delle operazioni seguenti sul nodo principale:

    * **Importazione di Azure pubblicare file di impostazioni**. A tale scopo, eseguire i cmdlet di PowerShell Azure seguenti sul nodo principale:

    ```
    Get-AzurePublishSettingsFile

    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```

    * **Configurare il certificato di gestione Azure sul nodo principale**. Se si dispone del file. cer, importare nell'archivio certificati CurrentUser\My e quindi eseguire il seguente cmdlet di PowerShell Azure per l'ambiente di Azure (AzureCloud o AzureChinaCloud):

    ```
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Aggiungere il nodo macchine virtuali di calcolo

Aggiungere nodi di calcolo con lo script **HpcIaaSNode.ps1 Aggiungi** .

### <a name="syntax"></a>Sintassi
```
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametri

* **Nome** - nome del cloud service tale nuovo nodo di calcolo macchine virtuali verranno tutti aggiunti al.

* **ImageName** - nome dell'immagine macchine Virtuali di Azure, che può essere ottenuto tramite il portale di classica Azure e Azure PowerShell cmdlet **Get-AzureVMImage**. L'immagine deve soddisfare i requisiti seguenti:

    1. Installare un sistema operativo Windows.

    2. Il ruolo di nodo di calcolo, è necessario installare HPC Pack.

    3. L'immagine deve essere un'immagine privata nella categoria utente immagine macchine Virtuali di Azure non pubblica.

* **Quantità** - numero del nodo di calcolo macchine virtuali da aggiungere.

* **InstanceSize** - dimensioni del nodo di calcolo macchine virtuali.

* **DomainUserName** - nome utente, che verrà utilizzato per aggiungere nuove macchine virtuali al dominio.

* **DomainUserPassword** - Password dell'utente del dominio.

* **NodeNameSeries** (facoltativo) - modello di denominazione per i nodi di elaborazione. Il formato deve essere &lt; *radice\_nome*&gt;&lt;*avviare\_numero*&gt;%. Ad esempio, MyCN % 10% indica che una serie di calcolo i nomi dei nodi partire da MyCN11. Se non viene specificato, lo script utilizza il nodo configurato per la denominazione serie nel cluster HPC.

### <a name="example"></a>Esempio

Nell'esempio seguente aggiunge nodo di calcolo di grandi dimensioni 20 macchine virtuali nel cloud servizio *hpcservice1*, in base immagine di macchine Virtuali *hpccnimage1*.

```
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Rimuovere il nodo macchine virtuali di calcolo

Rimuovere i nodi di calcolo con lo script **HpcIaaSNode.ps1 Rimuovi** .

### <a name="syntax"></a>Sintassi

```
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametri

* **Nome** - i nomi dei nodi cluster da rimuovere. Sono supportati i caratteri jolly. Il nome del set di parametro è nome. Non è possibile specificare il **nome** e **nodo** parametri.

* **Nodo** - HpcNode l'oggetto per i nodi da rimuovere, che può essere ottenuto tramite il cmdlet HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametro è nodo. Non è possibile specificare il **nome** e **nodo** parametri.

* **DeleteVHD** (facoltativo) - impostazione per eliminare dischi associati per macchine virtuali che vengono rimossi.

* **Forza** (facoltativo) - impostazione forzare nodi HPC non in linea prima di rimuoverli.

* **Confermare** (facoltativo) - Richiedi conferma prima di eseguire il comando.

* **WhatIf** - impostazione per descrivere cosa accadrebbe se si esegue il comando senza effettivamente eseguire il comando.

### <a name="example"></a>Esempio

Nell'esempio seguente impone nodi non in linea con i nomi che iniziano *HPCNode-CN -* e li rimuove i nodi e i dischi associati.

```
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Avviare il nodo macchine virtuali di calcolo

Avviare nodi di calcolo con lo script **HpcIaaSNode.ps1 Start** .

### <a name="syntax"></a>Sintassi

```
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametri

* **Nome** - nomi dei nodi cluster per l'avvio. Sono supportati i caratteri jolly. Il nome del set di parametro è nome. Non è possibile specificare il **nome** e **nodo** parametri.

* **Nodo**- HpcNode l'oggetto per i nodi per l'avvio, che può essere ottenuto tramite il cmdlet HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametro è nodo. Non è possibile specificare il **nome** e **nodo** parametri.

### <a name="example"></a>Esempio

Nell'esempio seguente viene avviato nodi il cui nome inizia *HPCNode-CN -*.

```
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Interrompere il nodo macchine virtuali di calcolo

Interrompere nodi di calcolo con lo script **HpcIaaSNode.ps1 Interrompi** .

### <a name="syntax"></a>Sintassi

```
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametri


* **Nome**- nomi dei nodi cluster deve essere interrotto. Sono supportati i caratteri jolly. Il nome del set di parametro è nome. Non è possibile specificare il **nome** e **nodo** parametri.

* **Nodo** - HpcNode l'oggetto per i nodi da arrestare, che può essere ottenuto tramite il cmdlet HPC PowerShell [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Il nome del set di parametro è nodo. Non è possibile specificare il **nome** e **nodo** parametri.

* **Forza** (facoltativo) - impostazione forzare nodi HPC non in linea prima di arrestare loro.

### <a name="example"></a>Esempio

Nell'esempio seguente impone nodi non in linea con i nomi che iniziano *HPCNode-CN -* e quindi si interromperà i nodi.

Interrompi-HPCIaaSNode.ps1-nome HPCNodeCN-*-forza

## <a name="next-steps"></a>Passaggi successivi

* Se si desidera un modo per aumentare o ridurre i nodi cluster secondo il carico di lavoro corrente dei processi e delle attività nel cluster automaticamente, vedere [automaticamente aumentare e ridurre le risorse cluster HPC Pack di Azure secondo il carico di lavoro cluster](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md).
