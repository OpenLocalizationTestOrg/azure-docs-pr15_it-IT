<properties
    pageTitle="Gestire RemoteApp di Azure utilizzando l'automazione di Azure | Microsoft Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire RemoteApp di Azure."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-remoteapp-using-azure-automation"></a>Gestione RemoteApp di Azure utilizzando l'automazione di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione di Azure RemoteApp.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizzando l'automazione di Azure, è possibile automatizzare attività manuale, ripetuto frequentemente, lunga ed errori per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili ad alta disponibilità scale secondo le proprie esigenze. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT e il personale attrezzi per concentrarsi su lavoro che consente di aggiungere valore aziendale spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-remoteapp"></a>Come automazione Azure consente di gestire RemoteApp di Azure?

RemoteApp può essere gestito in Azure automazione da utilizzare i cmdlet di PowerShell disponibili in [Strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automazione Azure è i cmdlet di RemoteApp PowerShell disponibili all'esterno della casella, in modo che sia possibile eseguire tutte le attività di gestione RemoteApp all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per la gestione RemoteApp di Azure, seguire questi collegamenti per ulteriori informazioni sull'automazione di Azure.

* Vedere l' automazione Azure [Introduzione esercitazione](../automation/automation-first-runbook-graphical.md)
