<properties
    pageTitle="Gestire Azure Web App usando l'automazione Azure | Microsoft Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire Azure Web App."
    services="app-service\web, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-web-app-using-azure-automation"></a>Gestione di Azure Web App utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione di Azure Web App.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizza l'automazione di Azure, manuali, ripetuti, attività lunga ed errori può essere automatizzata per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili ad alta disponibilità scale secondo le proprie esigenze. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT e il personale attrezzi per concentrarsi su lavoro che consente di aggiungere valore aziendale spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Come automazione Azure consente di gestire Azure Web App?

App Web possono essere gestiti in Azure automazione utilizzando i cmdlet di PowerShell disponibili nei [moduli di Azure PowerShell](../powershell-install-configure.md). È possibile [installare i cmdlet di App PowerShell Web di automazione di Azure](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/), in modo che sia possibile eseguire tutte le attività di gestione Web App all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi Azure automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Ecco alcuni esempi di gestione dei servizi di App con l'automazione:

* [Script per la gestione Web Apps](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per la gestione di Azure Web App, seguire questi collegamenti per ulteriori informazioni sull'automazione di Azure.

* Vedere l' automazione Azure [Introduzione esercitazione](../automation/automation-first-runbook-graphical.md)
