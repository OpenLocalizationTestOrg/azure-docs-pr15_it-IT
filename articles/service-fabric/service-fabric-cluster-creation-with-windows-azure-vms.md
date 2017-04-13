<properties
   pageTitle="Creare un cluster di autonomo con macchine virtuali di Azure che esegue Windows | Microsoft Azure"
   description="Informazioni su come creare e gestire un cluster di Azure servizio tessuti su Azure macchine virtuali che eseguono Windows Server."
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
   ms.date="08/05/2016"
   ms.author="dkshir;chackdan"/>



# <a name="create-a-three-node-standalone-service-fabric-cluster-with-azure-virtual-machines-running-windows-server"></a>Creare un cluster di tessuti servizio autonomo tre nodo con Azure macchine virtuali che eseguono Windows Server

In questo articolo viene descritto come creare un cluster in basato su Windows Azure macchine (), tramite il programma di installazione di tessuti servizio autonomo per Windows Server. Si tratta di un caso particolare di [creare e gestire un cluster che esegue Windows Server](service-fabric-cluster-creation-for-windows-server.md) in macchine virtuali sono [Macchine virtuali di Azure che eseguono Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md), ma non si sta creando [un cluster tessuti servizio basato su cloud Azure](service-fabric-cluster-creation-via-portal.md). La differenza è che il cluster tessuti servizio autonomo creato tramite la procedura seguente viene gestito interamente dall'utente, durante la Azure cluster tessuti servizio basato su cloud sono gestiti e aggiornati dal provider di risorse servizio tessuti.


## <a name="steps-to-create-the-standalone-cluster"></a>Procedura per creare il cluster autonomo

1. Accedere al portale di Azure e creare un nuovo Windows Server 2012 R2 Data Center macchine Virtuali in un gruppo di risorse. Leggere l'articolo [creare una macchina virtuale di Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md) per altri dettagli.
2. Aggiungere un paio ulteriori Windows Server 2012 R2 Data Center macchine virtuali allo stesso gruppo di risorse. Assicurarsi che le macchine con gli stessi nome utente e password quando creato. Una volta creato dovrebbero essere visualizzati tutti macchine tre virtuali nella stessa rete virtuale.
3. Connettersi a ciascuna delle macchine virtuali e disattivare Windows Firewall utilizzando [Server Manager, il dashboard di Server locale](https://technet.microsoft.com/library/jj134147.aspx). In questo modo che il traffico di rete può comunicare tra il computer. Mentre si è connessi a ogni computer, ottenere l'indirizzo IP aprendo un prompt dei comandi e digitando `ipconfig`. In alternativa è possibile visualizzare l'indirizzo IP di ogni computer selezionando la risorsa virtuali per il gruppo di risorse nel portale di Azure.
4. Connettersi a una delle macchine virtuali e verificare che è possibile effettuare il ping altri due macchine virtuali correttamente.
5. Connettersi a una delle macchine virtuali e [scaricare il pacchetto di tessuti servizio autonomo per Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) in una nuova cartella nel computer ed estrarre il pacchetto.
6. Aprire il file *ClusterConfig.Unsecure.MultiMachine.json* nel blocco note e modificare ogni nodo con tre indirizzi IP dei computer. Modificare il nome del cluster nella parte superiore e salvare il file.  Di seguito è illustrato un esempio parziale del manifesto cluster.

    ```
    {
        "name": "TestCluster",
        "clusterManifestVersion": "1.0.0",
        "apiVersion": "2015-01-01-alpha",
        "nodes": [
        {
            "nodeName": "vm0",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.5",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        },
        {
            "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.4",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc2/r0",
            "upgradeDomain": "UD1"
        },
        {
            "nodeName": "vm2",
            "metadata": "Replace the localhost with valid IP address or FQDN below",
            "iPAddress": "10.7.0.6",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc3/r0",
            "upgradeDomain": "UD2"
        }
    ],
    ```

7. Aprire una [finestra di PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Passare alla cartella in cui estratte il programma di installazione autonomo scaricato e salvato il file manifesto cluster. Eseguire il seguente comando di PowerShell.

    ```
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
    ```

8. Verrà visualizzato di PowerShell eseguire, connettersi a ogni computer e creare un cluster. Dopo circa un minuto, è possibile verificare se il cluster è operativo connettendosi a Esplora aree tessuti di servizio in uno dei indirizzo IP del computer, ad esempio utilizzando `http://10.7.0.5:19080/Explorer/index.html`. Poiché si tratta di un cluster di autonoma utilizzando macchine virtuali di Azure, per rendere sicure verranno è necessario [distribuire certificati alle macchine virtuali di Azure](service-fabric-windows-cluster-x509-security.md) o configurare uno dei computer come [controller di Windows Server Active Directory (AD) per l'autenticazione di Windows](service-fabric-windows-cluster-windows-security.md), proprio come si farebbe in locale.


## <a name="next-steps"></a>Passaggi successivi
- [Creare cluster di tessuti servizio autonomo in Windows Server o Linux](service-fabric-deploy-anywhere.md)
- [Aggiungere o rimuovere nodi in un cluster di tessuti servizio autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
- [Impostazioni di configurazione per cluster di Windows autonomo](service-fabric-cluster-manifest.md)
- [Proteggere un cluster autonomo in Windows la sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
- [Proteggere un cluster autonomo in Windows utilizzando X509 certificati](service-fabric-windows-cluster-x509-security.md)
