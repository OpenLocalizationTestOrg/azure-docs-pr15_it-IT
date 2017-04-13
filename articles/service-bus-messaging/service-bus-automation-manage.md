<properties
    pageTitle="Gestire Bus di servizio Azure utilizzando l'automazione di Azure | Microsoft Azure"
    description="Informazioni su come utilizzare il servizio di automazione di Azure per gestire Bus di servizio Azure."
    services="service-bus, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

# <a name="managing-azure-service-bus-using-azure-automation"></a>Gestione di Azure servizio Bus utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione di Azure servizio Bus.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure per semplificare la gestione dei cloud attraverso l'automazione dei processi e configurazione di stato desiderato. Utilizza l'automazione di Azure, manuali, ripetuti, attività lunga ed errori può essere automatizzata per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili ad alta disponibilità scale secondo le proprie esigenze. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT e il personale attrezzi per concentrarsi su lavoro che consente di aggiungere valore aziendale spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Come automazione Azure consente di gestire Bus di servizio Azure?

È possibile gestire Bus di servizio con l'automazione di Azure tramite l' [API REST Bus di servizio](https://msdn.microsoft.com/library/azure/mt639375.aspx). All'interno di automazione di Azure è possibile eseguire gli script di PowerShell per eseguire molte delle attività Bus di servizio tramite l'API REST. È anche possibile associare queste chiamate API REST di automazione di Azure con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Ecco alcuni esempi di utilizzo di PowerShell per gestire Bus di servizio Azure:

* [Cmdlet di PowerShell personalizzati per gestire le code Bus di servizio Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Come creare code, argomenti e le sottoscrizioni che utilizzano uno script di PowerShell Bus di servizio](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Creare tramite PowerShell spazi dei nomi di servizio Bus Azure](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

Il modulo PowerShell per l'utilizzo di bus di servizio Azure di automazione runbook può essere scaricato dalla [Raccolta di PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).


## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per gestire Bus di servizio Azure, seguire questi collegamenti per ulteriori informazioni sull'automazione di Azure.

* Vedere l' automazione Azure [Introduzione esercitazione](../automation/automation-first-runbook-graphical.md)
* Vedere come [gestire Bus di servizio con PowerShell](service-bus-powershell-how-to-provision.md)
