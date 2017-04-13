<properties
 pageTitle="Piani e fatturazione in Azure utilità di pianificazione"
 description="Piani e fatturazione in Azure utilità di pianificazione"
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
 ms.topic="article"
 ms.date="08/18/2016"
 ms.author="deli"/>

# <a name="plans-and-billing-in-azure-scheduler"></a>Piani e fatturazione in Azure utilità di pianificazione

## <a name="job-collection-plans"></a>Piani di raccolta di processo

Gli insiemi di processo sono entità fatturabili in Azure utilità di pianificazione. Processo raccolte contengono un numero di processi e sono disponibili in tre piani-Free, Standard e Premium: descritta di seguito.

|**Piano di raccolta di processo**|**Max # dei processi per il processo raccolta**|**Max ricorrenza**|**Processo max raccolte per abbonamento**|**Limiti**|
|:---|:---|:---|:---|:---|
|**Libera**|5 processi per ogni insieme di processo|Una volta all'ora. Non è possibile eseguire i processi più spesso una volta all'ora|Una sottoscrizione è consentita fino a 1 raccolta processo gratuito|Non è possibile utilizzare [l'oggetto di autorizzazione in uscita HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 processi per ogni insieme di processo|Una volta al minuto. Non è possibile eseguire i processi più spesso di un minuto|Una sottoscrizione è consentita fino a 100 raccolte di processo standard|Accesso ai set completo di funzionalità di utilità di pianificazione|
|**P10 Premium**|50 processi per ogni insieme di processo|Una volta al minuto. Non è possibile eseguire i processi più spesso di un minuto|Una sottoscrizione è consentita fino a 10.000 raccolte di processo P10 Premium. <a href="mailto:wapteams@microsoft.com">Contatta Microsoft</a> per altre informazioni.|Accesso ai set completo di funzionalità di utilità di pianificazione|
|**P20 Premium**|processi di 1000 per ogni insieme di processo|Una volta al minuto. Non è possibile eseguire i processi più spesso di un minuto|Una sottoscrizione è consentita fino a 10.000 raccolte di processo P20 Premium. <a href="mailto:wapteams@microsoft.com">Contatta Microsoft</a> per altre informazioni.|Accesso ai set completo di funzionalità di utilità di pianificazione|

## <a name="upgrades-and-downgrades-of-job-collection-plans"></a>Gli aggiornamenti ed effettua il downgrade dei piani di raccolta di processo

Si può aggiornare o eseguire il downgrade un processo insieme piano in qualsiasi momento tra i piani gratuito, Standard e Premium. Tuttavia, quando il downgrade a una raccolta di processo gratuito, il downgrade potrebbe non riuscire per uno dei motivi seguenti:

- Un processo gratuito insieme esiste già nella sottoscrizione
- Un processo nella raccolta di processo con una ricorrenza superiore quelle consentite per i processi di raccolte processo gratuito. La ricorrenza massima consentita in una raccolta di processo gratuito è una volta all'ora
- Sono disponibili più di 5 lavori nella raccolta di processo
- Un processo nella raccolta di processo con un'azione HTTP o HTTPS che utilizza un [oggetto di autorizzazione in uscita HTTP](scheduler-outbound-authentication.md)

## <a name="billing-and-azure-plans"></a>Fatturazione e Azure plan di messaggistica unificata

Le sottoscrizioni non vengono addebitate gratuitamente insiemi di processo. Se si dispone di più di 100 raccolte processo standard (10 unità fatturazione standard), è un'offerta migliore per tutte le raccolte di processo nel piano del premium.

Se si dispone di un insieme di processo standard e un insieme di processo premium, sono fatturato uno standard fatturazione unità _e_ premium fatturazione unità. Le fatture di servizio Utilità di pianificazione in base al numero di raccolte di processo attivi impostati come standard o premium; Questo è illustrato più avanti in prossime due sezioni.

## <a name="standard-billable-units"></a>Unità di misura fatturabile standard

Un'unità fatturabile standard può includere fino a 10 insiemi di processo standard. Poiché un insieme standard processo può contenere fino a 50 processi per ogni insieme di processo, un'unità di fatturazione standard consente una sottoscrizione a fino a 500 processi: tutte le esecuzioni quasi 22 milioni al mese.

Se si dispone tra 1 e 10 insiemi di processo standard, verrà addebitato 1 unità di fatturazione standard. Se si trovano tra insiemi di processo standard 11 e 20, verrà addebitato 2 unità di fatturazione standard. Se si dispone tra 21 e le raccolte di 30 processo standard, sarà effettuata per 3 unità fatturazione standard e così via.

## <a name="p10-premium-billable-units"></a>P10 Unità di misura Premium fatturabili

Un'unità fatturabile premium P10 può includere fino a 10.000 raccolte di processo premium P10. Poiché un insieme di processo premium P10 può contenere fino a 50 processi per ogni insieme di processo, un'unità di fatturazione premium consente una sottoscrizione a un massimo di 500.000 processi: tutte le esecuzioni quasi 22 miliardi al mese.

Se si trovano tra 1 e 10.000 raccolte processo premium, verrà addebitato 1 unità di fatturazione premio P10. Se si dispone tra 10,001 e le raccolte di processo premium 20.000, sarà effettuata per 2 unità di fatturazione premium P10 e così via.

Pertanto, insiemi di processo premium P10 hanno la stessa funzionalità gli insiemi di file di processo standard ma forniscono un'interruzione di prezzo nel caso in cui l'applicazione richiede numerose raccolte processo.

## <a name="p20-premium-billable-units"></a>P20 Unità di misura Premium fatturabili

Un'unità fatturabile premium P20 può includere fino a 5.000 insiemi di processo premium P20. Poiché un insieme di processo premium P20 può contenere fino a 1.000 operazioni per ogni insieme di processo, un'unità di fatturazione premium consente una sottoscrizione a fino a 5.000.000 processi: tutte le esecuzioni quasi 220 miliardi al mese.

Insiemi di processo premium P20 offre le stesse funzionalità delle raccolte di processo premium P10 ma anche supporta un maggior numero totale di processi globali rispetto premium P10 in modo da avere maggiore scalabilità e una maggiore processi numero per ogni insieme di processo.

## <a name="billing-and-active-status"></a>Stato attivo e della fatturazione

Raccolte di processo sono sempre attive a meno che l'abbonamento intera è passata in uno stato disabilitato temporaneo a causa di problemi di fatturazione. L'unico modo per garantire che un insieme di processo non viene effettuato è a uno impostarlo al piano _gratuito_ o eliminare la raccolta di processo.

Anche se è possibile disattivare tutti i processi all'interno di un insieme di processo in un'unica operazione, rimarrà invariata lo stato di fatturazione dell'insieme di processo, la raccolta di processo verrà _comunque_ fatturato. Analogamente, raccolte processo vuoto sono considerate attive e per la fatturazione.

## <a name="pricing"></a>Prezzi

Per ulteriori informazioni sui prezzi, vedere [Utilità di pianificazione prezzi](https://azure.microsoft.com/pricing/details/scheduler/).

## <a name="see-also"></a>Vedere anche


 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Azure utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)
