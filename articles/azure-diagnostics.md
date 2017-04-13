<properties
    pageTitle="Panoramica di diagnostica Windows Azure | Microsoft Azure"
    description="Utilizzare diagnostica Windows Azure per il debug, la misurazione delle prestazioni, monitoraggio, analisi il traffico in servizi cloud, macchine virtuali e tessuti di servizio"
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/02/2016"
    ms.author="robb"/>


# <a name="what-is-microsoft-azure-diagnostics"></a>Che cos'è Microsoft Azure diagnostica


Diagnostica Windows Azure è la capacità all'interno di Azure che consente l'insieme di dati di diagnostica in un'applicazione distribuita. È possibile utilizzare l'estensione di diagnostica da varie origini diverse. Attualmente supportati sono Web servizio Cloud di Azure ruoli e di lavoro, macchine virtuali di Azure in esecuzione Microsoft Windows e servizio tessuti. Altri servizi di Azure avere propria diagnostica separata.

## <a name="data-you-can-collect"></a>È possibile raccogliere dati

Diagnostica Windows Azure può raccogliere i tipi di dati seguenti:

Origine dati|Descrizione
---|---
Contatori delle prestazioni | Sistema operativo e contatori personalizzati
Registri delle applicazioni     | Tenere traccia dei messaggi scritti dall'applicazione in uso
Registri eventi di Windows   | Informazioni inviate al sistema di registrazione eventi di Windows
Origine evento .NET    | Codice di eventi utilizzando la classe .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) di scrittura
Registri di IIS             | Informazioni sui siti web IIS
Manifesto in base a ETW   | Eventi generati da qualsiasi processo eventi di traccia per Windows
Anomalo          | Informazioni sullo stato del processo in caso di un arresto anomalo dell'applicazione
Registri di errori personalizzato    | Log creati dall'applicazione o servizio
Registri di Azure infrastruttura diagnostica|Informazioni sulla diagnostica stesso

L'estensione di diagnostica Windows Azure è possibile trasferire i dati a un account di archiviazione Azure o inviarla a servizi come [Applicazione approfondimenti](./application-insights/app-insights-cloudservices.md). È possibile utilizzare i dati per il debug e risoluzione dei problemi, la misurazione delle prestazioni, monitoraggio Uso risorse, analisi del traffico e pianificazione delle capacità e il controllo.


## <a name="versioning"></a>Controllo delle versioni
Visualizzare la [cronologia controllo delle versioni di diagnostica Windows Azure](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>Passaggi successivi
Scegliere il servizio che si sta tentando di raccogliere diagnostica e utilizzare gli articoli seguenti per iniziare. Utilizzare i collegamenti di diagnostica di Windows Azure generale per riferimento relativo ad attività specifiche.

## <a name="web-apps"></a>Web App
Si noti che Web Apps non utilizzino Azure diagnostica. Individuare le informazioni equivalenti su [Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Servizi cloud tramite diagnostica Azure
- In Visual Studio, vedere [Utilizzare Visual Studio per tenere traccia di un'applicazione di servizi Cloud](./vs-azure-tools-debug-cloud-services-virtual-machines.md) per iniziare. In caso contrario, vedere
- [Come monitorare i servizi Cloud tramite diagnostica Azure](./cloud-services/cloud-services-how-to-monitor.md)
- [Configurare la diagnostica Azure in un'applicazione di servizi Cloud](./cloud-services/cloud-services-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

- [Utilizzo di diagnostica Windows Azure con applicazione approfondimenti per i servizi Cloud](./application-insights/app-insights-cloudservices.md)
- [Tenere traccia del flusso di un'applicazione di servizi Cloud con Azure diagnostica](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Usare PowerShell per configurare la diagnostica sui servizi Cloud](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## <a name="virtual-machines-using-azure-diagnostics"></a>Macchine virtuali di utilizzo di diagnostica Azure
- In Visual Studio, vedere [Utilizzare Visual Studio per traccia macchine virtuali di Azure](./vs-azure-tools-debug-cloud-services-virtual-machines.md) per iniziare. In caso contrario, vedere
- [Configurare la diagnostica Azure una macchina virtuale Azure](./virtual-machines-dotnet-diagnostics.md)

Per argomenti più avanzati, vedere

- [Usare PowerShell per configurare la diagnostica in macchine virtuali di Azure](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Creare una macchina virtuale di Windows con il monitoraggio e diagnostica utilizzando il modello di gestione risorse di Azure](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## <a name="service-fabric-using-azure-diagnostics"></a>Servizio tessuti tramite diagnostica Azure
Introduzione a [Monitor un'applicazione di servizio tessuti](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Molti altri articoli di diagnostica servizio tessuti sono disponibili nella struttura di spostamento a sinistra quando si riceve questo articolo.

## <a name="general-azure-diagnostics-articles"></a>Articoli generale Azure diagnostica
- [Configurazione di azure diagnostica Schema](https://msdn.microsoft.com/library/azure/mt634524.aspx) - informazioni su come modificare il file di schema per raccogliere e l'invio di dati di diagnostica. Si noti che è possibile anche utilizzare Visual Studio per modificare il file di schema.
- [Come Azure diagnostica dati vengono archiviati in archiviazione Azure](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - conoscere i nomi delle tabelle e BLOB scritto in cui i dati di diagnostica.
- Informazioni su come [utilizzare contatori delle prestazioni in Azure diagnostica](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md).
- Informazioni su come [informazioni di diagnostica Route Azure a informazioni dettagliate sui applicazione](./azure-diagnostics-configure-applicationinsights.md)
- Se si verificano problemi con diagnostica avvio o cercare i dati in tabelle Azure lo spazio di archiviazione, vedere [Risoluzione dei problemi di diagnostica Azure](./azure-diagnostics-troubleshooting.md)
