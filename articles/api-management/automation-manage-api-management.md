<properties
    pageTitle="Gestire Azure API Management utilizzando l'automazione di Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire Azure API Management."
    services="api-management, automation"
    documentationCenter=""
    authors="csand-msft"
    manager="eamono"
    editor=""/>

<tags
    ms.service="api-management"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="csand"/>



#<a name="managing-azure-api-management-using-azure-automation"></a>Gestione della gestione del API Azure utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione di Azure API Management.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizza l'automazione di Azure, manuali, ripetuti, attività lunga ed errori può essere automatizzata per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili ad alta disponibilità scale secondo le proprie esigenze. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT e il personale attrezzi per concentrarsi su lavoro che consente di aggiungere valore aziendale spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-api-management"></a>Come automazione Azure consente di gestire Azure API Management?

Gestione dell'API possono essere gestiti in Azure automazione utilizzando i [cmdlet di Windows PowerShell per API di gestione dell'API Windows Azure](https://azure.microsoft.com/updates/full-set-of-windows-powershell-cmdlets-for-azure-api-management-api/). All'interno di automazione di Azure è possibile scrivere gli script di PowerShell del flusso di lavoro per eseguire molte attività di gestione dell'API utilizzando i cmdlet. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Ecco alcuni esempi dell'uso di API gestione con l'automazione:
* [Gestione delle API di Azure-usare PowerShell per eseguire il backup e ripristino](https://blogs.msdn.microsoft.com/katriend/2015/10/02/azure-api-management-using-powershell-for-backup-and-restore/)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per la gestione di Azure API Management, seguire questi collegamenti per altre informazioni.

* Vedere l' automazione di Azure [esercitazione introduttiva](../automation/automation-first-runbook-graphical.md).
