<properties
 pageTitle="Creare un nodo principale HPC Pack in una macchina virtuale Azure | Microsoft Azure"
 description="Informazioni su come utilizzare il portale di Azure e il modello di distribuzione di Manager delle risorse per creare un nodo principale di Microsoft HPC Pack in una macchina virtuale Azure."
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="08/17/2016"
 ms.author="danlep"/>

# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Creare il nodo principale di un cluster HPC Pack in una macchina virtuale Azure con un'immagine Marketplace


Utilizzare un' [immagine di macchina virtuale Microsoft HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) da Azure Marketplace e il portale Azure per creare il nodo principale di un cluster HPC. In questa figura macchine Virtuali Pack HPC si basa su Windows Server 2012 R2 Data Center con HPC Pack 2012 R2 aggiornamento 3 preinstallato. Utilizzare il nodo principale per una prova di distribuzione concetto di HPC Pack in Azure. È quindi possibile aggiungere nodi di calcolo al cluster per eseguire HPC carichi di lavoro.



>[AZURE.TIP]Per distribuire un cluster HPC Pack completo in Azure che include il nodo principale e nodi di calcolo, è consigliabile utilizzare un metodo automatico. Opzioni includono lo [script di distribuzione HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) e il modello di gestione risorse [cluster HPC Pack per carichi di lavoro di Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/) . Per altri modelli disponibili, vedere [Opzioni di cluster HPC Pack in Azure](virtual-machines-windows-hpcpack-cluster-options.md) . 


## <a name="planning-considerations"></a>Considerazioni sulla pianificazione

Come mostrato nella figura seguente, si distribuisce il nodo principale HPC Pack in un dominio di Active Directory in una rete virtuale Azure.

![Nodo principale HPC Pack][headnode]

* **Dominio di active Directory** : il HPC Pack nodo principale deve essere associata a un dominio di Active Directory in Azure prima di iniziare servizi HPC nella macchina virtuale. Come illustrato in questo articolo, per una prova di distribuzione concetto, è possibile convertire la macchina virtuale creati per il nodo principale come controller di dominio prima di avviare servizi HPC. In alternativa è possibile distribuire un controller di dominio separata e foresta in Azure a cui si partecipa a macchine Virtuali di nodo principale.

* **Azure virtuali** - quando si usa il modello di distribuzione di Manager delle risorse per distribuire il nodo principale, specificare o creare una rete virtuale Azure. Utilizzare la rete virtuale se è necessario aggiungere il nodo principale a un dominio di Active Directory esistente. È necessario anche in un secondo momento per aggiungere il nodo di calcolo macchine virtuali al cluster.

    
## <a name="steps-to-create-the-head-node"></a>Procedura per creare il nodo principale

Di seguito sono i passaggi necessari per utilizzare il portale Azure per creare una macchina virtuale Azure per il nodo principale HPC Pack utilizzando il modello di distribuzione di Manager delle risorse. 


1. Se si desidera creare una nuova foresta di Active Directory in Azure con controller di dominio separata macchine virtuali, un'opzione consiste nell'utilizzare un [modello di Manager delle risorse](https://azure.microsoft.com/documentation/templates/active-directory-new-domain-ha-2-dc/). Per una prova semplice di distribuzione concetto, è possibile ignorare questo passaggio e configurare il nodo principale macchine Virtuali stesso come un controller di dominio. Questa opzione è descritto più avanti in questo articolo.
    
2. In [HPC Pack 2012 R2 nella pagina di Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) in Azure Marketplace, fare clic su **Crea macchina virtuale**. 

3. Nel portale, nella pagina **HPC Pack 2012 R2 in Windows Server 2012 R2** , selezionare il modello di distribuzione di **Manager delle risorse** e quindi fare clic su **Crea**.

    ![Immagine di HPC Pack][marketplace]

4. Utilizzare il portale per configurare le impostazioni e creare la macchina virtuale. Se ha familiarità con Azure, seguire l'esercitazione [creare una macchina virtuale di Windows nel portale di Azure](virtual-machines-windows-hero-tutorial.md). Per una prova di distribuzione concetto, in genere possibile accettare i valori predefiniti o impostazioni consigliate.

    >[AZURE.NOTE]Se si desidera aggiungere il nodo principale a un dominio di Active Directory esistente in Azure, assicurarsi che si specifica la rete virtuale per tale dominio quando si crea la macchina virtuale.
       
4. Dopo aver creato la macchina virtuale e la macchina virtuale è in esecuzione, [connettere la macchina virtuale](virtual-machines-windows-connect-logon.md) da Desktop remoto. 

5. Unire la macchina virtuale a un insieme di strutture di dominio esistente oppure creare un insieme di strutture di dominio in macchine Virtuali stesso.

    * Se la macchina virtuale è stato creato in una rete virtuale Azure con un insieme di strutture di dominio esistente, partecipare macchina virtuale alla foresta utilizzando gli strumenti di gestione Server o Windows PowerShell standard. Quindi riavviare.

    * Se la macchina virtuale è stato creato in una nuova rete virtuale (senza un insieme delle strutture esistenti), favorire la macchina virtuale come controller di dominio. Usare i passaggi standard per installare e configurare il ruolo di servizi di dominio Active Directory sul nodo principale. Per informazioni dettagliate, vedere [installare un nuovo Windows Server 2012 Active Directory insieme di strutture](https://technet.microsoft.com/library/jj574166.aspx).

5. Dopo la macchina virtuale è in esecuzione e fa parte di un insieme di strutture di Active Directory, avviare i servizi HPC Pack come indicato di seguito:

    un. Connettere il nodo principale macchine Virtuali utilizzando un account di dominio sia un membro del gruppo Administrators locale. Ad esempio, utilizzare l'account di amministratore che configurare una volta creato il nodo principale macchine Virtuali.

    b. Per una configurazione di nodo principale predefinita, avviare Windows PowerShell come amministratore e digitare quanto segue:

    ```
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```

    È possibile richiedere alcuni minuti per i servizi di HPC Pack iniziare.

    Per le opzioni di configurazione aggiuntive nodo principale, digitare `get-help HPCHNPrepare.ps1`.


## <a name="next-steps"></a>Passaggi successivi

* È ora possibile utilizzare con il nodo principale del cluster HPC Pack. Ad esempio, avviare HPC Cluster Manager e completare l' [Elenco di attività di distribuzione](https://technet.microsoft.com/library/jj884141.aspx).
* Se si desidera incrementare cluster per calcolare la capacità su richiesta, aggiungere [burst Azure nodi](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md) in un servizio cloud. 

* Provare a eseguire un carico di lavoro test sul cluster. Ad esempio, vedere il HPC Pack [Guida introduttiva](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/virtual-machines-windows-hpcpack-cluster-headnode/marketplace.png
