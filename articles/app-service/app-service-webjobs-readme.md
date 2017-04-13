<properties
    pageTitle="WebJobs nel servizio App Azure"
    description="Informazioni su come creare WebJobs per eseguire test di sfondo, interagire con i servizi come spazio di archiviazione e Bus di servizio e creare attività pianificate."
    services="app-service"
    documentationCenter=""
    authors="christopheranderson"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="chrande"/>

# <a name="using-webjobs-in-azure-app-service"></a>Utilizzo di WebJobs in Azure App servizio

Questo articolo fornisce collegamenti a risorse di documentazione sull'utilizzo WebJobs Azure e Azure WebJobs SDK. Azure WebJobs offrono un modo semplice per eseguire script o programmi come processi in background in [App servizio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). È possibile caricare ed eseguire un file eseguibile ad esempio come cmd, bat, exe (.NET), ps1, m, php, copia, js e vaso. Questi programmi vengono eseguiti come WebJobs una pianificazione (cron) o continuamente.

SDK WebJobs risulta più facile usare lo spazio di archiviazione di Azure. WebJobs SDK include un sistema di trigger in cui viene utilizzato Microsoft Azure lo spazio di archiviazione BLOB, code e tabelle, nonché code Bus di servizio e di associazione.

Creazione, la distribuzione e gestione WebJobs avviene con integrato utensili in Visual Studio. È possibile creare WebJobs dai modelli, pubblicare e gestire (Esegui/Interrompi/monitor/debug) loro.

Il dashboard WebJobs nel portale di Azure offre funzionalità di gestione potenti che forniscono controllo completo sull'esecuzione di WebJobs, compresa la possibilità di richiamare singole funzioni all'interno di WebJobs. Il dashboard viene visualizzata anche funzione runtime e l'output di registrazione.

[AZURE.INCLUDE [app-service-blueprint-webjobs](../../includes/app-service-blueprint-webjobs.md)]
