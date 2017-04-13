<properties
    pageTitle="Gestione dei servizi Cloud Azure utilizzando l'automazione di Azure | Microsoft Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire i servizi cloud Azure in scala."
    services="cloud-services, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/20/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-cloud-services-using-azure-automation"></a>Gestione dei servizi Cloud Azure utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione dei servizi cloud Azure.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizzando l'automazione di Azure, è possibile automatizzare attività lunga, manuali, errori e frequenti per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili e altamente disponibili soddisfa esigenze specifiche esigenze dell'organizzazione si adatta. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT / personale attrezzi per concentrarsi su lavoro che aggiunge business valore spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-cloud-services"></a>Come automazione Azure consente di gestire servizi cloud Azure?

Servizi cloud Azure possono essere gestiti in Azure automazione da utilizzare i cmdlet di PowerShell disponibili in [Strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automazione Azure è disponibili i cmdlet PowerShell di servizio cloud all'esterno della casella, in modo che sia possibile eseguire tutte le attività di gestione dei servizi cloud all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Alcuni esempi di utilizzo di automazione di Azure per gestire i servizi Cloud Azure includono:

- [Distribuzione di continuo di un servizio Cloud ogni volta che cscfg o cspkg viene aggiornato in archiviazione Blob Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
- [Il riavvio di istanze del servizio Cloud in parallelo, un aggiornamento del dominio alla volta](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per gestire i servizi cloud Azure, seguire questi collegamenti per ulteriori informazioni sull'automazione di Azure.

- [Panoramica di automazione Azure](../automation/automation-intro.md)
- [Il primo runbook](../automation/automation-first-runbook-graphical.md)
- [Mappa di Azure risorse di automazione](https://azure.microsoft.com/documentation/learning-paths/automation/)
 
