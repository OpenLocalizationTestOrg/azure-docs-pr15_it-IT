<properties
    pageTitle="Gestire l'archiviazione di Azure utilizzando l'automazione di Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire l'archiviazione di Azure scala."
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="jolevy"/>



#<a name="managing-azure-storage-using-azure-automation"></a>Gestione dell'archiviazione Azure utilizzando l'automazione di Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione di BLOB Azure lo spazio di archiviazione, tabelle e code.


## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](https://azure.microsoft.com/services/automation/) è un servizio di Azure per semplificare la gestione dei cloud tramite automazione dei processi. Utilizzare l'automazione di Azure, lunga, manuali, errori e frequenti attività possono essere automatizzate per aumentare l'efficienza e l'affidabilità e ridurre i tempi di valore per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili e altamente disponibili soddisfa esigenze specifiche esigenze dell'organizzazione si adatta. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT / personale attrezzi per concentrarsi su lavoro che aggiunge business valore spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-storage"></a>Come automazione Azure consente di gestire l'archiviazione di Azure?

Spazio di archiviazione Azure può essere gestito in Azure automazione utilizzando i cmdlet di PowerShell disponibili in [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx). Automazione Azure è i cmdlet di PowerShell di spazio di archiviazione disponibili all'esterno della casella, in modo che sia possibile eseguire tutte le blob, tabella e le attività di gestione all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

[Automazione di Azure runbook raccolta](https://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contiene una varietà di prodotto team e community runbook per automatizzare la gestione di archiviazione Azure, altri servizi di Azure e 3 ° sistemi di terze parti. Raccolta runbook includono:

 * [Rimuovere spazio di archiviazione BLOB che vengono determinati giorni precedenti utilizzando l'automazione](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Scaricare un Blob dallo spazio di archiviazione Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Eseguire il backup di tutti i dischi per una singola macchina virtuale Azure o per tutte le macchine virtuali in un servizio Cloud](https://gallery.technet.microsoft.com/scriptcenter/Backup-all-disks-for-a-ede940d5)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere usato per gestire lo spazio di archiviazione di Azure BLOB, tabelle e code, seguire questi collegamenti a ulteriori informazioni su come automazione Azure.

Vedere l'esercitazione Azure automazione [creazione o importazione runbook di automazione di Azure](../automation/automation-creating-importing-runbook.md).
 
