<properties
   pageTitle="Tipi di nodo servizio tessuti e macchine Virtuali scala set | Microsoft Azure"
   description="Descrive come tipi di nodo servizio tessuti riguardano macchine Virtuali scala set e in remoto come connettersi a un'istanza di impostare la scala macchine Virtuali o un nodo cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="the-relationship-between-service-fabric-node-types-and-virtual-machine-scale-sets"></a>La relazione tra i tipi di nodo tessuti servizi e dei set di scala macchina virtuale

Set di scala macchina virtuale sono una risorsa Azure calcolare che è possibile utilizzare per distribuire e gestire un insieme di macchine virtuali come un set. Ogni tipo di nodo definiti in un cluster di servizio tessuti sia configurato come un Set di scala macchine Virtuali separata. Ogni tipo di nodo quindi può essere ridimensionato o verso il basso in modo indipendente, è diversi insiemi di porte aperte e può avere metriche capacità diversi.

Lo screenshot seguente mostra un cluster che è disponibili due tipi di nodo: front-end e back-end.  Ogni tipo di nodo è cinque nodi.

![Schermata di un cluster che è disponibili due tipi di nodo][NodeTypes]

## <a name="mapping-vm-scale-set-instances-to-nodes"></a>Mappatura di macchine Virtuali scala impostare istanze a nodi

Come si può notare sopra, le istanze di macchine Virtuali scala impostare iniziare dall'istanza 0 e quindi passare la. La numerazione verrà applicata ai nomi. Ad esempio BackEnd_0 è istanza 0 del Set di back-end macchine Virtuali scala. Questo Set di scala particolare macchine Virtuali ha cinque varianti, denominate BackEnd_0, BackEnd_1, BackEnd_2, BackEnd_3 e BackEnd_4.

Durante il ridimensionamento di un Set di scala macchine Virtuali viene creata una nuova istanza. Il nuovo nome dell'istanza macchine Virtuali scala impostata sarà generalmente il nome macchine Virtuali scala impostare + il numero di istanza successivo. In questo esempio è BackEnd_5.


## <a name="mapping-vm-scale-set-load-balancers-to-each-node-typevm-scale-set"></a>Mapping scala macchine Virtuali configurare servizi di bilanciamento del carico per ogni tipo di nodo/macchina virtuale Set di scala

Se non è stato distribuito il cluster dal portale di o utilizzato il modello di gestione risorse di esempio fornite, quindi quando si riceve un elenco di tutte le risorse in un gruppo di risorse si vedranno i servizi di bilanciamento del carico per ogni tipo di impostazione scala macchine Virtuali o nodo.

Il nome da simile al: **kg -&lt;nome nodo&gt;**. Ad esempio kg-sfcluster4doc-0, come mostrato in questa schermata:


![Risorse][Resources]


## <a name="remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node"></a>Connessione a un'istanza di impostare la scala macchine Virtuali o un nodo cluster in remoto
Ogni tipo di nodo definiti in un cluster sia configurato come un Set di scala macchine Virtuali separata.  Questo significa che è possono ridimensionare i tipi di nodo verso l'alto o verso il basso in modo indipendente e possono essere eseguite di diversi SKU macchine Virtuali. Diversamente da quanto succede singola istanza macchine virtuali, le istanze di macchine Virtuali scala impostare sono ottenere un indirizzo IP del proprio. In modo che siano un po' impegnativa quando si sta cercando un indirizzo IP e porte che è possibile usare in remoto connettersi a una specifica istanza.

Ecco i passaggi da seguire per scoprirle.

### <a name="step-1-find-out-the-virtual-ip-address-for-the-node-type-and-then-inbound-nat-rules-for-rdp"></a>Passaggio 1: Individuare le all'indirizzo IP per il tipo di nodo e quindi le regole in entrata NAT per RDP

Per ottenere che, è necessario ottenere i valori di regole NAT in ingresso che sono stati definiti come parte della definizione delle risorse per **Microsoft.Network/loadBalancers**.

Nel portale, passare alle **Impostazioni**e blade bilanciamento carico.

![LBBlade][LBBlade]


In **Impostazioni**, fare clic su **regole in entrata NAT**. Adesso offre l'indirizzo IP e porte che è possibile usare in remoto connettersi per la prima istanza di macchine Virtuali scala impostata. Nella figura seguente è **104.42.106.156** e **3389**

![NATRules][NATRules]

### <a name="step-2-find-out-the-port-that-you-can-use-to-remote-connect-to-the-specific-vm-scale-set-instancenode"></a>Passaggio 2: Individuare la porta che è possibile usare in remoto connettersi al specifiche macchine Virtuali scala impostare istanza/nodo

In questo documento è parlato corrispondenze tra le istanze di macchine Virtuali scala impostata per i nodi. Che verrà utilizzato per determinare la porta esatta.

Le porte vengono assegnate in ordine crescente di istanza macchine Virtuali scala impostata. in questo esempio per il tipo di nodo front-end, pertanto le porte per ciascuna delle cinque istanze sono i seguenti: a questo punto, è necessario eseguire il mapping stesso per l'istanza macchine Virtuali scala impostata.

|**Istanza di Set di macchine Virtuali scala**|**Porta**|
|-----------------------|--------------------------|
|FrontEnd_0|3389|
|FrontEnd_1|3390|
|FrontEnd_2|3391|
|FrontEnd_3|3392|
|FrontEnd_4|3393|
|FrontEnd_5|3394|


### <a name="step-3-remote-connect-to-the-specific-vm-scale-set-instance"></a>Passaggio 3: Connettere remota all'istanza di macchine Virtuali scala impostare specifica

Nella schermata seguente viene utilizzato connessione Desktop remoto per connettersi alla FrontEnd_1:

![RDP][RDP]

## <a name="how-to-change-the-rdp-port-range-values"></a>Come modificare i valori dell'intervallo porta RDP

### <a name="before-cluster-deployment"></a>Prima della distribuzione cluster

Quando configurano cluster utilizzando un modello di Manager delle risorse, è possibile specificare l'intervallo in **inboundNatPools**.

Passare alla definizione delle risorse per **Microsoft.Network/loadBalancers**. In che si è disponibile la descrizione di **inboundNatPools**.  Sostituire i valori *frontendPortRangeStart* e *frontendPortRangeEnd* .

![InboundNatPools][InboundNatPools]


### <a name="after-cluster-deployment"></a>Dopo la distribuzione cluster
Questo è un po' più complesso e potrebbe causare in macchine virtuali Guida riciclate. A questo punto è necessario impostare nuovi valori tramite PowerShell Azure. Assicurarsi che Azure PowerShell 1.0 o versioni successive è installato nel computer in uso. Se non è già in precedenza, consigliabile eseguire i passaggi descritti in [come installare e configurare Azure PowerShell.](../powershell-install-configure.md)

Accedere al proprio account Azure. Se il comando PowerShell non riesce per qualche motivo, è necessario verificare se si dispone di Azure PowerShell installato correttamente.

```
Login-AzureRmAccount
```

Eseguire le operazioni seguenti per ottenere informazioni dettagliate sul bilanciamento del carico e vengono visualizzati i valori per la descrizione di **inboundNatPools**:

```
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load balancer name>
```

A questo punto è possibile impostare *frontendPortRangeEnd* e *frontendPortRangeStart* per i valori desiderati.

```
$PropertiesObject = @{
    #Property = value;
}
Set-AzureRmResource -PropertyObject $PropertiesObject -ResourceGroupName <RG name> -ResourceType Microsoft.Network/loadBalancers -ResourceName <load Balancer name> -ApiVersion <use the API version that get returned> -Force
```


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle funzionalità "Distribuire ovunque" e il confronto con i cluster gestiti Azure](service-fabric-deploy-anywhere.md)
- [Protezione del cluster](service-fabric-cluster-security.md)
- [Servizio tessuti SDK e Guida introduttiva](service-fabric-get-started.md)


<!--Image references-->
[NodeTypes]: ./media/service-fabric-cluster-nodetypes/NodeTypes.png
[Resources]: ./media/service-fabric-cluster-nodetypes/Resources.png
[InboundNatPools]: ./media/service-fabric-cluster-nodetypes/InboundNatPools.png
[LBBlade]: ./media/service-fabric-cluster-nodetypes/LBBlade.png
[NATRules]: ./media/service-fabric-cluster-nodetypes/NATRules.png
[RDP]: ./media/service-fabric-cluster-nodetypes/RDP.png
