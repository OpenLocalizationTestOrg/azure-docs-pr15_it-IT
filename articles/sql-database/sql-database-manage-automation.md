<properties
    pageTitle="Gestire i database di SQL Azure utilizzando l'automazione di Azure | Microsoft Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire i database di SQL Azure in scala."
    services="sql-database, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/26/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-sql-databases-using-azure-automation"></a>Gestione dei database di SQL Azure utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione dei database SQL Azure.


## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizzando l'automazione di Azure, è possibile automatizzare attività lunga, manuali, errori e frequenti per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili e altamente disponibili soddisfa esigenze specifiche esigenze dell'organizzazione si adatta. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT / personale attrezzi per concentrarsi su lavoro che aggiunge business valore spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-sql-databases"></a>Come automazione Azure consente di gestire i database di SQL Azure?

Database SQL Azure può essere gestito in Azure automazione da utilizzare i [cmdlet di PowerShell di Database SQL Azure](https://msdn.microsoft.com/library/dn546723.aspx) che sono disponibili in [Strumenti di Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automazione Azure è i cmdlet di PowerShell di Database SQL Azure disponibili all'esterno della casella, in modo che sia possibile eseguire tutte le attività di gestione DB SQL all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Automazione Azure è installato anche la possibilità di comunicare con SQL Server direttamente, inviando comandi SQL tramite PowerShell.

[Automazione di Azure runbook raccolta](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una varietà di prodotto team e community runbook per automatizzare la gestione di database SQL Azure, altri servizi di Azure e 3 ° sistemi di terze parti. Raccolta runbook includono:

 * [Eseguire query SQL su un database SQL Server](https://gallery.technet.microsoft.com/scriptcenter/How-to-use-a-SQL-Command-be77f9d2)
 * [Ridimensionare in verticale (alto o verso il basso) un Database di SQL Azure in una pianificazione](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-e957354f)
 * [Troncare i valori di una tabella SQL se il database si avvicina la dimensione massima](https://gallery.technet.microsoft.com/scriptcenter/Azure-Automation-Your-SQL-30f8736b)
 * [Se si sono molto frammentati l'indice tabelle in un Database di SQL Azure](https://gallery.technet.microsoft.com/scriptcenter/Indexes-tables-in-an-Azure-73a2a8ea)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per gestire i database di SQL Azure, seguire questi collegamenti a ulteriori informazioni su come automazione Azure.

- [Panoramica di automazione Azure](../automation/automation-intro.md)
- [Il primo runbook](../automation/automation-first-runbook-graphical.md)
- [Mappa di Azure risorse di automazione](https://azure.microsoft.com/documentation/learning-paths/automation/)
- [Azure automazione: L'agente SQL nel Cloud](https://azure.microsoft.com/blog/2014/06/26/azure-automation-your-sql-agent-in-the-cloud/) 
 
