<properties
   pageTitle="Come ridimensionare le funzioni Azure | Microsoft Azure"
   description="Comprendere le modalità di funzioni di Azure scalabilità per soddisfare le esigenze dei carichi di lavoro basate su eventi."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure funzioni, funzioni, elaborazione dell'evento, webhooks, calcolo dinamico, architettura senza server"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# <a name="how-to-scale-azure-functions"></a>Come ridimensionare le funzioni di Azure

## <a name="introduction"></a>Introduzione

Il vantaggio di Azure funzioni è che risorse di elaborazione sono utilizzate solo quando necessario. Ciò significa che non pagare inattive macchine virtuali o prenotare la capacità quando potrebbe essere necessario. Se, tuttavia, la piattaforma assegna capacità di calcolo quando il codice è in esecuzione, scalabilità per la gestione di caricamento e quindi il ridimensionamento quando codice non è in esecuzione.

Il meccanismo per la nuova funzionalità è il piano di servizio dinamico.  

In questo articolo viene fornita una panoramica del funzionamento il piano di servizio dinamico e come la piattaforma Scale su richiesta per eseguire il codice.

Se non si è ancora familiarità con le funzioni di Azure, assicurarsi di selezionare l'articolo di [funzioni di Azure Panoramica](functions-overview.md) per comprendere meglio le funzionalità.

## <a name="configure-azure-functions"></a>Configurare le funzioni di Azure

Due impostazioni principali riguardano proporzioni dei caratteri:

* Piano di [servizio App Azure piano](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) o servizio dinamico
* Dimensione della memoria per l'ambiente di esecuzione

Il costo di una funzione varia in base al piano di servizio selezionato. Con un piano di servizio dinamico, costo è una funzione del tempo di esecuzione, le dimensioni della memoria e numero di esecuzioni. In base alle tariffe accumulare solo quando il codice viene effettivamente in esecuzione.

Un piano di servizio App ospita le funzioni in macchine virtuali esistenti, potrebbero essere utilizzate anche per eseguire altro codice. Dopo aver pagato per tali macchine virtuali di ogni mese, non esiste senza costi aggiuntivi per le funzioni di esecuzione su di essi.

## <a name="choose-a-service-plan"></a>Scegliere un piano di servizio

Quando si crea funzioni, è possibile selezionare per eseguirle in un piano di servizio dinamico o un [piano di servizio di App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Nel piano del servizio di App, le funzioni verranno eseguito su una macchina virtuale dedicata, come lavoro App web oggi per Basic, Standard o SKU avanzate.
Questa macchina virtuale dedicata allocata alle App e le funzioni e sempre disponibile se codice viene attivamente eseguito o meno. Questo è consigliabile se si dispone di esistente, utilizzato in macchine virtuali che eseguono già altro codice o se si prevede di eseguire funzioni continuamente o quasi continuamente. Una macchina virtuale separa costo dalle dimensioni runtime e della memoria. Di conseguenza, è possibile limitare il costo di molte funzioni di esecuzione prolungata al costo di uno o più macchine virtuali che vengono eseguiti in.

[AZURE.INCLUDE [Dynamic Service plan](../../includes/functions-dynamic-service-plan.md)]
