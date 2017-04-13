<properties
   pageTitle="Aggiungere o rimuovere nodi in un cluster di tessuti servizio autonomo | Microsoft Azure"
   description="Informazioni su come aggiungere o rimuovere nodi a un cluster di Azure servizio tessuti fisici sulla macchina virtuale che eseguono Windows Server, che può essere locale o in qualsiasi cloud."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/20/2016"
   ms.author="dkshir;chackdan"/>


# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Aggiungere o rimuovere nodi a un cluster di tessuti servizio autonomo che esegue Windows Server

Dopo avere [creato il cluster tessuti servizio autonomo nei computer Windows Server](service-fabric-cluster-creation-for-windows-server.md), le esigenze dell'organizzazione può modificare in modo che potrebbe essere necessario aggiungere o rimuovere più nodi al cluster. In questo articolo vengono fornite procedure dettagliate a tale scopo.


## <a name="add-nodes-to-your-cluster"></a>Aggiungere nodi al cluster

1. Preparare la macchina virtuale/macchina da aggiungere al cluster seguendo i passaggi indicati nella sezione [preparare il computer per soddisfare i prerequisiti per la distribuzione cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Pianificare il dominio e aggiornamento del dominio si intende aggiungere macchine Virtuali/computer a.
3. Desktop remoto (RDP) in macchine Virtuali/computer in cui si desidera aggiungere al cluster.
4. Copia o il [download del pacchetto autonomo servizio tessuti per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) in macchine Virtuali/computer e decomprimere il pacchetto.
5. Eseguire Powershell come amministratore e passare al percorso del file del pacchetto decompresso.
6. Eseguire Powershell *AddNode.ps1* con i parametri che descrive il nuovo nodo da aggiungere. Nell'esempio seguente viene aggiunto un nuovo nodo denominato VM5, con il tipo di NodeType0, indirizzo IP 182.17.34.52 in UD1 e FD1. *ExistingClusterConnectionEndPoint* è un endpoint di connessione per un nodo è già presente nelle cluster esistente. Per l'endpoint, è possibile scegliere l'indirizzo IP di *qualsiasi* nodo del cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Rimuovere i nodi dal cluster

1. A seconda del livello di Reliablity scelto per il cluster, non è possibile rimuovere i primi n (3/5/7/9) nodi del tipo di nodo principale
2. Esecuzione RemoveNode comando in un cluster di sviluppo non è supportata.
2. Desktop remoto (RDP) in macchine Virtuali/computer in cui si desidera rimuovere dal cluster.
2. Copia o [scaricare il pacchetto autonomo per tessuti servizio per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e decomprimere il pacchetto al computer in macchine Virtuali/uso.
3. Eseguire Powershell come amministratore e passare al percorso del file del pacchetto decompresso.
4. Eseguire *RemoveNode.ps1* in PowerShell. Nell'esempio seguente rimuove il nodo corrente dal cluster. *ExistingClientConnectionEndpoint* è un endpoint di connessione client per tutti i nodi che rimangono nel cluster. Scegliere l'indirizzo IP e la porta dell'endpoint di *qualsiasi* **altro nodo** del cluster. Questo **altro nodo** , a sua volta aggiornata la configurazione cluster per il nodo rimosso. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

Si noti che anche dopo aver rimosso un nodo, potrebbero essere visualizzate come viene premuto nelle query e SFX. Si tratta di un problema noto e verrà risolto in una versione futura. 


## <a name="next-steps"></a>Passaggi successivi
- [Impostazioni di configurazione per cluster di Windows autonomo](service-fabric-cluster-manifest.md)
- [Proteggere un cluster autonomo in Windows la sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows utilizzando X509 certificati](service-fabric-windows-cluster-x509-security.md)
- [Creare un cluster di tessuti servizio autonomo con macchine virtuali di Azure che esegue Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)
