<properties
   pageTitle="Raccogliere i registri tramite diagnostica Azure Linux | Microsoft Azure"
   description="In questo articolo viene descritto come configurare Azure diagnostica per raccogliere i registri da un cluster di servizio tessuti Linux in esecuzione in Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# <a name="collect-logs-by-using-azure-diagnostics"></a>Raccogliere i registri tramite diagnostica Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando si esegue un cluster di Azure servizio tessuti, è utile anche per raccogliere i registri da tutti i nodi in una posizione centrale. Con i log in una posizione centrale semplifica analizzare e risoluzione dei problemi, se si tratta di servizi, l'applicazione o il cluster stesso. Un modo per caricare e raccogliere i log consiste nell'utilizzare l'estensione di diagnostica di Azure, che consente di caricare i registri archiviazione Azure. È possibile leggere gli eventi dallo spazio di archiviazione e inserirli in un prodotto, ad esempio [Ricerca flessibile](service-fabric-diagnostic-how-to-use-elasticsearch.md) o un'altra soluzione log di analisi.

## <a name="log-sources-that-you-might-want-to-collect"></a>Origini dei log che è possibile raccogliere
- **Servizio tessuti registri**: emesso da piattaforma tramite [LTTng](http://lttng.org) e caricati al proprio account di archiviazione. Registri possono essere eventi operativi o runtime che genera la piattaforma. Questi file di log sono archiviate nel percorso che specifica il manifesto cluster. (Per visualizzare i dettagli di account di archiviazione, cercare il tag **AzureTableWinFabETWQueryable** e cercare **StoreConnectionString**)
- **Gli eventi dell'applicazione**: emesso dal codice del servizio. È possibile utilizzare qualsiasi soluzione di registrazione che scrive file di log basata su testo, ad esempio LTTng. Per ulteriori informazioni, vedere la documentazione LTTng su analisi dell'applicazione.  


## <a name="deploy-the-diagnostics-extension"></a>Distribuire l'estensione di diagnostica
Il primo passo per la raccolta di log è per distribuire l'estensione di diagnostica su ciascuna delle macchine virtuali cluster servizio tessuti. L'estensione di diagnostica consente di raccogliere i registri in ogni macchina virtuale e li carica in account di archiviazione specificato. I passaggi variano in base se si usa il portale di Azure e gestione di risorse di Azure.

**Per distribuire l'estensione di diagnostica in macchine virtuali del cluster durante la creazione di cluster, impostare su **On**.** Dopo aver creato il grafico, è possibile modificare questa impostazione tramite il portale.

Configurare Linux Azure diagnostica (LAD) per raccogliere i file e inserirle nel proprio account di archiviazione. Questo processo viene illustrato come scenario 3 ("caricare i file di log") vedere l'articolo [Utilizzo LAD monitoraggio e la diagnosi macchine virtuali Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md). Seguire questa procedura Ottiene consente di accedere alle tracce. È possibile caricare le tracce in un visualizzatore di propria scelta.

È inoltre possibile distribuire l'estensione di diagnostica tramite Gestione risorse di Azure. Il processo è simile per Windows e Linux e descritte per cluster di Windows in [come raccogliere i registri con Azure diagnostica](service-fabric-diagnostics-how-to-setup-wad.md).

È anche possibile utilizzare operazioni Management Suite, come descritto nelle [Operazioni di gestione Suite Log Analitica con Linux](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Dopo aver completato la configurazione, l'agente LAD controlla i file di log specificato. Ogni volta che una nuova riga viene aggiunto al file, crea una voce del Registro di sistema che viene inviata all'archiviazione che è stato specificato.


## <a name="next-steps"></a>Passaggi successivi
Per comprendere in modo più dettagliato gli eventi che è necessario esaminare la risoluzione dei problemi, vedere [la documentazione LTTng](http://lttng.org/docs) e [LAD utilizzando](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md).
