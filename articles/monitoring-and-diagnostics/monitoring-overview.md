<properties
    pageTitle="Panoramica di monitoraggio di Microsoft Azure | Microsoft Azure"
    description="Panoramica di livello superiore di monitoraggio e diagnostica di Microsoft Azure inclusi gli avvisi, webhooks, autoscale e così via."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"l
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="robb"/>

# <a name="overview-of-monitoring-in-microsoft-azure"></a>Panoramica di monitoraggio di Microsoft Azure

Questo articolo offre una panoramica di monitoraggio risorse Azure. Vengono forniti collegamenti a informazioni su tipi specifici di risorse.  Per informazioni di alto livello sul monitoraggio dell'applicazione dal punto di vista non Azure, vedere [Guida di diagnostica e monitoraggio](../best-practices-monitoring.md).

Le applicazioni cloud sono complesse con numerosi elementi mobili. Monitoraggio fornisce dati per verificare che l'applicazione resti alto e in esecuzione in uno stato integro. Consente inoltre a bloccare potenziali problemi o risolvere i problemi di oltre quelli. Inoltre, è possibile utilizzare dati di monitoraggio di ottenere informazioni approfondite complete sull'applicazione. Tale knowledge possibile per migliorare le prestazioni dell'applicazione o gestibilità o automatizzare le azioni che in caso contrario richiedono intervento manuale.

Il diagramma seguente illustra una visualizzazione concettuale di monitoraggio di Azure, inclusi il tipo di log che è possibile raccogliere e cosa può fare con i dati.   

![Modello logico per il monitoraggio e diagnostica per le risorse non calcolo](./media/monitoring-overview/MonitoringAzureResources-non-compute_v3.png)

Figura 1: Modello concettuale per il monitoraggio e diagnostica per le risorse non calcolo

<br/>

![Modello logico per il monitoraggio e diagnostica per le risorse di calcolo](./media/monitoring-overview/MonitoringAzureResources-compute_v3.png)

Figura 2: Modello concettuale per il monitoraggio e diagnostica per le risorse di calcolo


## <a name="monitoring-sources"></a>Monitoraggio delle origini
### <a name="activity-logs"></a>Registri delle attività
È possibile cercare il Registro di attività (precedentemente denominati operativo o i log di controllo) per informazioni sulla risorsa visto dall'infrastruttura di Azure. Il registro contiene informazioni, ad esempio ore quando le risorse vengono create o eliminate.  

### <a name="host-vm"></a>Host macchina virtuale
**Calcolare solo**


Alcune calcolare risorse come servizi Cloud, macchine virtuali, e servizio tessuti dispone di una macchina virtuale Host dedicato interagiscono con. La macchina virtuale Host è pari a macchine Virtuali radice nel modello di hypervisor Hyper-V. In questo caso, è possibile raccogliere metriche nella solo macchina virtuale Host oltre il sistema operativo Guest.  

Per altri servizi di Azure, non c'è necessariamente un mapping 1:1 tra la risorsa e una macchina virtuale Host specifico in modo metriche macchine Virtuali host non sono disponibili.


### <a name="resource---metrics-and-diagnostics-logs"></a>Risorsa - metriche e registri di diagnostica
Metrica sere varia in base al tipo di risorsa. Ad esempio macchine virtuali fornisce statistiche sul disco rigido IO e percentuale di CPU. Ma tali Stat non sono disponibili per una coda Bus di servizio che invece offre la metrica come coda dimensione e un messaggio di velocità.

Per le risorse di elaborazione è possibile ottenere le metriche sui moduli del sistema operativo Guest e diagnostica come Azure diagnostica. Diagnostica Windows Azure consente di raccogliere e l'invio di raccolta dati di diagnostica in altre posizioni, tra cui lo spazio di archiviazione Azure.

Un elenco di metriche attualmente sere è disponibile in [metriche è supportato](monitoring-supported-metrics.md).

### <a name="application---diagnostics-logs-application-logs-and-metrics"></a>Applicazione - registri di diagnostica, registri applicazioni e metriche
**Calcolare solo**

Applicazioni possono essere eseguite nella parte superiore del sistema operativo Guest nel modello di calcolo. Creano propri set di registri e metriche.

Tipi di metriche includono

- Contatori delle prestazioni
- Registri delle applicazioni
- Registri eventi di Windows
- Origine evento .NET
- Registri di IIS
- Manifesto in base a ETW
- Anomalo
- Log degli errori di clienti


## <a name="uses-for-monitoring-data"></a>Viene utilizzato per controllare i dati

### <a name="visualize"></a>Visualizzare
La visualizzazione di dati di monitoraggio di diagrammi e grafici consente di individuare le tendenze più rapidamente sfogliare i dati.  

Alcuni metodi di visualizzazione includono:

- Usare il portale di Azure
- La distribuzione dei dati a informazioni dettagliate sui applicazione Azure
- La distribuzione dei dati da Microsoft PowerBI
- Inviare i dati a uno strumento di visualizzazione di terze parti utilizzando uno dei due streaming live o mediante lo strumento di lettura da un archivio di archiviazione Azure

### <a name="archive"></a>Archivio
Monitoraggio dei dati in genere è scritto allo spazio di archiviazione Azure e mantenuto sono finché non viene eliminato.

Come utilizzare i dati in vari modi:

- Una volta scritto, è possibile impostare altri strumenti all'interno o all'esterno di Azure leggerlo ed elaborarlo.
- Per scaricare i dati in locale di un archivio locale o modificare i criteri di conservazione nel cloud per mantenere i dati per lunghi periodi di tempo.  
- Mantenere i dati in archiviazione Azure tempo indefinito, anche se è necessario acquistare spazio di archiviazione Azure in base alla quantità di dati che è mantenere.
-

### <a name="query"></a>Query
È possibile utilizzare l'API REST Monitor Azure, cross piattaforma interfaccia riga di comando (CLI) comandi, i cmdlet di PowerShell o .NET SDK per accedere ai dati del sistema o lo spazio di archiviazione Azure

Alcuni esempi:

-  Recupero dei dati per un'applicazione di monitoraggio personalizzata che è stato scritto
-  Creazione di query personalizzate e invio di tali dati a un'applicazione di terze parti.

### <a name="route"></a>Distribuzione
È possibile trasmettere monitoraggio dei dati in altre posizioni in tempo reale.

Alcuni esempi:

- Inviare a informazioni dettagliate sui applicazione in modo che è possibile utilizzare gli strumenti di visualizzazione non esiste.
- Inviare a un hub di evento in modo che è possibile inviare agli strumenti di terze parti per eseguire analisi in tempo reale.

### <a name="automate"></a>Automatizzare
È possibile utilizzare dati di monitoraggio per avviare eventi o alcuni esempi di processi intero pari successivo:

- Dati di utilizzo per scalare automaticamente per calcolare le istanze verso l'alto o verso il basso in base al caricamento dell'applicazione.
- Inviare messaggi di posta elettronica quando una metrica supera una soglia predeterminata.
- Chiamare un URL web (webhook) per eseguire un'azione in un sistema all'esterno di Azure
- Avviare un runbook in Azure automazione per eseguire una varietà di attività

## <a name="methods-of-use"></a>Metodi d'uso
In generale, è possibile modificare il rilevamento di dati, routing e il recupero utilizzando uno dei metodi seguenti. Non tutti i metodi disponibili per tutte le azioni o i tipi di dati.

- [Portale di Azure](https://portal.azure.com)
- [PowerShell](insights-powershell-samples.md)  
- [Multipiattaforma comando interfaccia della riga)](insights-cli-samples.md)
- [API REST](https://msdn.microsoft.com/library/dn931943.aspx)
- [.NET SDK](https://msdn.microsoft.com/library/dn802153.aspx)

## <a name="azures-monitoring-offerings"></a>Offerte di monitoraggio di Azure
Azure è disponibili per i servizi dell'infrastruttura privo di telemetria di applicazione di monitoraggio offerte. La migliore strategia per il monitoraggio combina uso delle tutti e tre per comprendere completa e informazioni dettagliate sull'integrità dei servizi.

- [Azure Monitor](http://aka.ms/azmondocs) -offerte di visualizzazione, query, routing, avviso, autoscale e automazione dati sia da ogni singola risorsa Azure (registri diagnostici) e l'infrastruttura di Azure (registro delle attività). In questo articolo fa parte della documentazione di Azure Monitor. Il nome di Azure Monitor è stato rilasciato il 25 settembre in Ignite 2016.  Il nome precedente è stato "Approfondimenti Azure".  
- [Informazioni dettagliate sui applicazione](https://azure.microsoft.com/documentation/services/application-insights/) -fornisce rilevamento RTF e diagnostica per problemi a livello di applicazione del servizio, ben integrato nella parte superiore di dati di Azure monitoraggio. È la piattaforma di diagnostica predefinita per App servizio Web Apps.  È possibile inviare i dati da altri servizi a essa.  
- [Registro Analitica](https://azure.microsoft.com/documentation/services/log-analytics/) parte di [Operazioni Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – fornisce una soluzione di gestione IT globale per locale e infrastruttura terze parti basate su cloud (ad esempio AWS) oltre a risorse Azure.  Dati di Azure Monitor possono essere instradati direttamente al Log Analitica in modo da visualizzare metriche e registri per l'intero ambiente in un'unica posizione.     


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni su

- [Azure Monitor in un video da Ignite 2016](https://myignite.microsoft.com/videos/4977)
- [Guida introduttiva di Azure Monitor](monitoring-get-started.md)
- [Azure diagnostica](../azure-diagnostics.md) se si sta tentando di diagnosticare i problemi dell'applicazione di servizio Cloud, macchina virtuale o servizio tessuti.
- [Informazioni dettagliate sui applicazione](https://azure.microsoft.com/documentation/services/application-insights/) se si sta tentando di diagnostici problemi nell'applicazione del servizio Web app.
- [Risoluzione dei problemi di archiviazione di Azure](../storage/storage-e2e-troubleshooting.md) quando si usa lo spazio di archiviazione BLOB, tabelle o code
- [Registro Analitica](https://azure.microsoft.com/documentation/services/log-analytics/) e la [famiglia di prodotti Gestione operazioni](https://www.microsoft.com/cloud-platform/operations-management-suite)
