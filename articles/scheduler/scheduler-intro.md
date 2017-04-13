<properties
 pageTitle="Che cos'è l'utilità di pianificazione di Azure? | Microsoft Azure"
 description="Utilità di pianificazione Azure consente di descrivere in modo dichiarativo le azioni da eseguire nel cloud. Quindi pianifica e esegue automaticamente le azioni."
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="what-is-azure-scheduler"></a>Che cos'è l'utilità di pianificazione di Azure?

Utilità di pianificazione Azure consente di descrivere in modo dichiarativo le azioni da eseguire nel cloud. Quindi pianifica e esegue automaticamente le azioni.  Utilità di pianificazione responsabile tramite il [portale di Azure](scheduler-get-started-portal.md), codice, [API REST](https://msdn.microsoft.com/library/mt629143.aspx)o Azure PowerShell.

Utilità di pianificazione crea, gestisce e richiama il lavoro programmato.  Utilità di pianificazione non qualsiasi carichi di lavoro o eseguire il codice. IT solo il codice _richiama_ ospitato in un' posizione, ovvero in Azure, in locale, o con un altro provider. Richiama tramite HTTP, HTTPS, una coda di spazio di archiviazione, una coda di bus servizio o un argomento di bus di servizio.

Utilità di pianificazione consente di pianificare [processi](scheduler-concepts-terms.md), conserva la cronologia dei risultati di esecuzione processo uno possibile esaminare e in modo deterministico e affidabile pianifica carichi di lavoro da eseguire. Azure WebJobs (parte della caratteristica Web Apps in Azure App servizio) e altre funzionalità di programmazione di Azure usare utilità di pianificazione in background. [API REST utilità di pianificazione](https://msdn.microsoft.com/library/mt629143.aspx) consente di gestire le comunicazioni per le azioni. Di conseguenza, utilità di pianificazione supporta facilmente [le pianificazioni complesse e ricorrenza avanzata](scheduler-advanced-complexity.md) .

Esistono diversi scenari che che richiedono l'utilizzo della pianificazione. Per esempio:

+ _Azioni di applicazione periodica:_ Raccolta dati da Twitter in un feed periodicamente.
+ _Manutenzione giornaliera:_ Giorno eliminazione dei registri, esecuzione di backup e altre attività di manutenzione. Ad esempio, un amministratore può scegliere del backup del database alle 1:00 ogni giorno per nove mesi successivi.

Utilità di pianificazione consente di creare, aggiornare, eliminare, visualizzare e gestire [raccolte di processo](scheduler-concepts-terms.md) e processi a livello di programmazione, usando gli script e nel portale.

## <a name="see-also"></a>Vedere anche

 [Azure utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Modalità di compilazione di pianificazioni complesse e ricorrenza avanzata con utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)
