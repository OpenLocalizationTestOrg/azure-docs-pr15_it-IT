<properties
    pageTitle="Gestire archivi chiave Azure utilizzando l'automazione di Azure | Microsoft Azure"
    description="Informazioni sull'utilizzo del servizio di automazione di Azure per gestire Azure chiave archivio."
    services="Key-Vault, automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/29/2016"
    ms.author="magoedte;csand"/>

#<a name="managing-azure-key-vault-using-azure-automation"></a>Gestione di archivio di chiave Azure utilizzando l'automazione Azure

Questa Guida verrà presentate il servizio di Azure automazione e come può essere utilizzato per semplificare la gestione delle chiavi e informazioni riservate in Azure chiave archivio.

## <a name="what-is-azure-automation"></a>Che cos'è l'automazione Azure?

[Automazione di Azure](../automation/automation-intro.md) è un servizio di Azure per semplificare la gestione dei cloud attraverso l'automazione dei processi e configurazione di stato desiderato. Utilizza l'automazione di Azure, manuali, ripetuti, attività lunga ed errori può essere automatizzata per aumentare l'affidabilità, l'efficienza e il valore time to per l'organizzazione.

Automazione Azure fornisce un motore di esecuzione del flusso di lavoro altamente affidabili ad alta disponibilità scale secondo le proprie esigenze. Automazione di Azure, processi possono essere avviati manualmente, sistemi di terze parti 3o o intervalli pianificati in modo che le attività decidere esattamente quando necessario.

Ridurre il carico operativo e liberare IT e il personale attrezzi per concentrarsi su lavoro che consente di aggiungere valore aziendale spostando le attività di gestione cloud per essere eseguita automaticamente da Azure automazione.


## <a name="how-can-azure-automation-help-manage-azure-key-vault"></a>Come automazione Azure consente di gestire archivi chiave Azure?

Archivio di chiave può essere gestito in Azure automazione utilizzando [AzureRM chiave archivio i cmdlet] (https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) e [i cmdlet di Azure classica chiave archivio](https://msdn.microsoft.com/library/azure/dn868052.aspx). Il modulo di Azure per la gestione classica archivio chiave è disponibile automaticamente in Azure automazione ed è possibile importare il [modulo AzureRM KeyVault](https://www.powershellgallery.com/packages/AzureRM.KeyVault/1.1.4) in Azure automazione, in modo che è possibile eseguire molte attività di gestione archivio chiave all'interno del servizio. È anche possibile coppia di questi cmdlet in Azure automazione con i cmdlet per altri servizi di Azure, per automatizzare attività complesse tra servizi Azure e 3 ° sistemi di terze parti.

Con i cmdlet di Azure chiave archivio è possibile eseguire queste operazioni tra gli altri: 

- Creare e configurare un archivio di chiave
- Creare o importare una chiave
- Creare o aggiornare un segreto
- Aggiornare gli attributi di una chiave
- Ottenere una chiave o segreto
- Eliminare una chiave o segreto

Ecco alcuni esempi di utilizzo di PowerShell per gestire archivi chiave:  

* [Archivio chiave Azure - Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step)
* [Impostazione e configurazione di un insieme di credenziali chiave Azure](https://www.simple-talk.com/cloud/platform-as-a-service/setting-up-and-configuring-an-azure-key-vault)


## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le nozioni di base di Azure automazione e come può essere utilizzato per gestire archivi chiave Azure, seguire questi collegamenti per ulteriori informazioni sull'automazione di Azure.

* Vedere l' automazione Azure [Introduzione esercitazioni](../automation/automation-first-runbook-graphical.md).
* Vedere gli [script di PowerShell di archivio di Azure chiave](https://gallery.technet.microsoft.com/scriptcenter/site/search?query=azure%20key%20vault&f%5B0%5D.Value=azure%20key%20vault&f%5B0%5D.Type=SearchText&ac=5).
