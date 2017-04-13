<properties
 pageTitle="Limiti di utilità di pianificazione e le impostazioni predefinite"
 description="Limiti di utilità di pianificazione e le impostazioni predefinite"
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

# <a name="scheduler-limits-and-defaults"></a>Limiti di utilità di pianificazione e le impostazioni predefinite

## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>Limita, limiti, le impostazioni predefinite e le quote di utilità di pianificazione

[AZURE.INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Intestazione x-ms-richiesta-id

Ogni richiesta effettuata in base al servizio Utilità di pianificazione restituisce un'intestazione di risposta denominata**id richiesta di ms x**. Questa intestazione contiene un valore opaco che identifica la richiesta.

Se dopo aver verificato che la richiesta viene correttamente formulata una richiesta in modo coerente non funziona, si può usare questo valore per segnalare l'errore a Microsoft. Nel report, includere il valore di x-ms-richiesta-id, il tempo approssimativo che è stata effettuata la richiesta, l'identificatore della sottoscrizione, insieme di processo, e/o processo e il tipo di operazione che si è tentato di richiesta.

## <a name="see-also"></a>Vedere anche


 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Azure utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Azure utilità di pianificazione disponibilità e l'affidabilità](scheduler-high-availability-reliability.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)
