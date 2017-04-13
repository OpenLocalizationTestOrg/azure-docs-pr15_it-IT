<properties
   pageTitle="Ridimensionare un cluster di servizio tessuti avanti o indietro | Microsoft Azure"
   description="Ridimensionare un cluster di servizio tessuti avanti o indietro in base alla richiesta impostando le regole di ridimensionamento automatico per ogni set di scala tipo/macchine Virtuali di nodo. Aggiungere o rimuovere nodi in un cluster di tessuti di servizio"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Ridimensionare un cluster di servizio tessuti avanti o indietro con le regole di ridimensionamento automatico

Set di scala macchina virtuale sono una risorsa di elaborazione Azure che è possibile utilizzare per distribuire e gestire un insieme di macchine virtuali come un set. Ogni tipo di nodo definiti in un cluster di servizio tessuti sia configurato in un gruppo di scala macchine Virtuali separato. Ogni tipo di nodo quindi può essere ridimensionato o out in modo indipendente, contenere diversi gruppi di porte aperte e può avere metriche capacità diversi. Per altre informazioni nel documento di [servizio tessuti nodetypes](service-fabric-cluster-nodetypes.md) . Poiché tessuti servizio tipi di nodo del cluster sono costituiti scala macchine Virtuali imposta in back-end, è necessario impostare le regole di ridimensionamento automatico per ogni set di scala tipo/macchine Virtuali di nodo.

>[AZURE.NOTE] L'abbonamento deve avere abbastanza core per aggiungere nuove macchine virtuali che costituiscono il cluster. Non esiste al momento convalida del modello in modo che viene visualizzato un errore in fase di distribuzione, se uno qualsiasi dei limiti di quota raggiunto.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Scegliere la scala di tipo/macchine Virtuali di nodo imposta le dimensioni

Attualmente, il non utente è possibile specificare le regole di ridimensionamento automatico per set di scala macchine Virtuali tramite il portale, pertanto possiamo usare PowerShell Azure (1.0 +) per elencare i tipi di nodo e quindi aggiungervi le regole di ridimensionamento automatico.

Per ottenere l'elenco di set di scala macchine Virtuali che costituiscono il cluster, eseguire i cmdlet seguenti:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Impostare le regole di ridimensionamento automatico per set di nodi di tipo/macchine Virtuali scala

Se il cluster dispone di più tipi di nodo, ripetere che questa operazione per ogni nodo tipi/macchine Virtuali imposta che si desidera ridimensionare (Avanti o indietro). Prendere in considerazione il numero dei nodi è necessario che prima di configurare il ridimensionamento automatico. Il numero minimo di nodi contenenti il tipo di nodo principale dipende dal livello di affidabilità scelto. Altre informazioni sui [livelli di affidabilità](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Ridimensionamento verso il basso il nodo principale digitare minore l'effettua numerica minima di cluster instabile oppure porta verso il basso. Questo potrebbe causare la perdita di dati per le applicazioni e per i servizi di sistema.

Attualmente la funzionalità di ridimensionamento automatico non verrà gestita da carichi che potrebbero segnalare le applicazioni di servizio infrastruttura. In questo momento il ridimensionamento automatico che è ottenere puramente basate su tramite le prestazioni in modo contatori che vengono creati da ogni macchina virtuale scalare le istanze di set.  

Seguire queste istruzioni [per configurare il ridimensionamento automatico per ogni set di scala macchine Virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] In scala verso il basso scenario, a meno che il tipo di nodo dispone di un livello di durata di oro o argento è necessario chiamare il [cmdlet Rimuovi ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) con il nome del nodo appropriato.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Aggiungere manualmente macchine virtuali a un set di scala tipo/macchine Virtuali di nodo

Seguire le istruzioni o un esempio nella [raccolta modelli introduttive](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) per modificare il numero di macchine virtuali di ogni tipo di nodo. 

>[AZURE.NOTE] Aggiunta di macchine virtuali richiede tempo, in modo che non si prevede di componenti aggiuntivi da istantaneo. Pianificare in modo aggiungere capacità anche nel tempo, per consentire più di 10 minuti prima che la capacità di macchine Virtuali sia disponibile per la replica / service istanze per ottenere inserito.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Rimuovere manualmente macchine virtuali relativi al set di scala tipo/macchine Virtuali di nodo principale

>[AZURE.NOTE] I servizi di sistema tessuti servizio eseguiti nel tipo di nodo principale del cluster. In modo dovrebbero mai arrestare o ridurre il numero di istanze di tale tipi di nodo minore quali il livello di affidabilità giustifichi. Fare riferimento a [informazioni dettagliate sui livelli di affidabilità qui](service-fabric-cluster-capacity.md). 

È necessario eseguire il seguente istanza in macchine Virtuali una procedura alla volta. In questo modo per i servizi di sistema e i servizi informazioni sullo stato essere arrestare normalmente istanza macchine Virtuali che si desidera rimuovere e nuove repliche create in altri nodi.

1. Eseguire [Disabilita ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) con finalità 'RemoveNode' per disabilitare il nodo che si desidera rimuovere (istanza più alto in quel tipo di nodo).

2. Eseguire [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) per assicurarsi che il nodo effettivamente la transizione su disattivato. In caso contrario, attendere finché non viene disabilitato il nodo. Non è possibile Affrettati questo passaggio.

2. Seguire le istruzioni o un esempio nella [raccolta modelli introduttive](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) per modificare il numero di macchine virtuali di un'unità in tale nodo. L'istanza rimosso è l'istanza di macchine Virtuali più alto. 

3. Ripetere i passaggi da 1 a 3 in base alle esigenze, ma non è mai scalare verso il basso il numero di istanze di tipi di nodo principale inferiore a cosa giustifichi il livello di affidabilità. Fare riferimento a [informazioni dettagliate sui livelli di affidabilità qui](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Rimuovere manualmente macchine virtuali relativi al set di scala tipo/macchine Virtuali di nodo non primario

>[AZURE.NOTE] Per un servizio informazioni sullo stato, è necessario un determinato numero dei nodi per essere sempre fino a mantenere la disponibilità e mantenere lo stato del servizio. Minimo, è necessario il numero dei nodi uguale al numero di set di destinazione replica di partizione/servizio. 

È necessario execute istanza in macchine Virtuali una procedura seguente per volta. In questo modo per i servizi di sistema e i servizi informazioni sullo stato essere arrestare normalmente in istanza macchine Virtuali che si desidera rimuovere e nuove repliche creato dove else.

1. Eseguire [Disabilita ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) con intento 'RemoveNode' per disabilitare il nodo che si desidera rimuovere (istanza più alto in quel tipo di nodo).

2. Eseguire [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) per assicurarsi che il nodo effettivamente la transizione su disattivato. In caso contrario attendere fino a quando il nodo è disattivato. Non è possibile Affrettati questo passaggio.

2. Seguire le istruzioni o un esempio nella [raccolta modelli introduttive](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) per modificare il numero di macchine virtuali di un'unità in tale nodo. L'istanza di macchine Virtuali più alto rimuoverà. 

3. Ripetere i passaggi da 1 a 3 in base alle esigenze, ma non è mai scalare verso il basso il numero di istanze di tipi di nodo principale inferiore a cosa giustifichi il livello di affidabilità. Fare riferimento a [informazioni dettagliate sui livelli di affidabilità qui](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamenti può essere visualizzata in Esplora tessuti servizio

Durante il ridimensionamento di un cluster di Esplora aree di tessuti servizio rifletteranno il numero dei nodi (scala macchine Virtuali impostate istanze) che fanno parte del cluster.  Durante il ridimensionamento un cluster verso il basso si vedranno all'istanza di nodo rimosso/macchine Virtuali visualizzato in uno stato a meno che non si chiama [cmd Rimuovi ServiceFabricNodeState](https://msdn.microsoft.com/library/mt125993.aspx) con il nome di nodo appropriato.   

Di seguito è riportata una spiegazione per questo problema.

I nodi elencati in servizi tessuti soluzioni sono un riflesso dei tessuti servizio sistema servizi (FM specificamente) il numero dei nodi cluster era/è a conoscenza. Durante il ridimensionamento la scala di macchine Virtuali imposta verso il basso, la macchina virtuale è stata eliminata ma il servizio di sistema FM ancora ritiene che il nodo (associato a macchine Virtuali che è stata eliminata) viene restituito. Pertanto servizio tessuti Explorer continua a visualizzare il nodo (anche se lo stato di integrità potrebbe essersi verificato o sconosciuto).

Per assicurarsi che un nodo viene rimosso quando si rimuove una macchina virtuale, sono disponibili due opzioni:

1) Scegliere un livello di durata di oro o argento (disponibile presto) per i tipi di nodo del cluster, che consente l'integrazione di infrastruttura. Che quindi rimuoverà automaticamente i nodi dallo stato di servizi (FM) sistema durante il ridimensionamento in basso.
Fare riferimento a [informazioni dettagliate sui livelli di durata qui](service-fabric-cluster-capacity.md)

2) Una volta all'istanza macchine Virtuali è state modificate in verso il basso, è necessario chiamare il [cmdlet ServiceFabricNodeState Rimuovi](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Servizio tessuti cluster richiedono un certo numero dei nodi sia disponibile al momento per mantenere la disponibilità e mantenere stato - denominato "gestione di base". Pertanto, non è in genere sicuro per chiudere tutti i computer del cluster a meno che non è stato eseguito prima di tutto un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Passaggi successivi
Leggere le operazioni seguenti per vengono inoltre fornite informazioni sulla pianificazione capacità cluster, aggiornamento di un cluster e partizioni servizi:

- [Pianificare la capacità di cluster](service-fabric-cluster-capacity.md)
- [Aggiornamenti cluster](service-fabric-cluster-upgrade.md)
- [Servizi di informazioni sullo stato partizione per scala massima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
