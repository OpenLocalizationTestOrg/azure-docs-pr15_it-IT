<properties
   pageTitle="Integrazione con operazioni Management Suite (OMS) | Microsoft Azure"
   description="Oltre a utilizzare le caratteristiche standard di OMS, è possibile integrare con altre applicazioni di gestione e servizi per fornire un ambiente ibrido di gestione per fornire gli scenari di gestione personalizzati univoci per l'ambiente o la gestione di personalizzato esperienza utente per i clienti.  In questo articolo viene fornita una panoramica le diverse opzioni per l'integrazione con OMS e collegamenti ad articoli che fornisce informazioni tecniche dettagliate."
   services="operations-management-suite"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="bwren" />

# <a name="integrating-with-operations-management-suite-oms"></a>Integrazione con operazioni Management Suite (OMS)

Operazioni Management Suite è basato sul cloud IT soluzione Microsoft che consente di gestire e proteggere le in locale e infrastruttura cloud.  Oltre a utilizzare le caratteristiche standard di OMS, è possibile integrare con altre applicazioni di gestione e servizi per fornire un ambiente ibrido di gestione per fornire gli scenari di gestione personalizzati univoci per l'ambiente o la gestione di personalizzato esperienza utente per i clienti.  In questo articolo viene fornita una panoramica le diverse opzioni per l'integrazione con servizi di OMS e collegamenti ad articoli che fornisce informazioni tecniche dettagliate. 



## <a name="log-analytics"></a>Registro Analitica
Gestione delle raccolte da Log Analitica vengono memorizzati in un repository ospitato in Azure.  Tutti i dati archiviati nel repository è disponibile nelle ricerche log che forniscono analisi rapida su grandi quantità di dati.  I requisiti di integrazione possono essere per popolare l'archivio con i nuovi dati renderlo disponibile per l'analisi o per estrarre dati da archivio per fornire una nuova visualizzazione o per l'integrazione con un altro strumento di gestione.

Ogni parte di dati nel repository verrà archiviati come record.  Quando si inseriscono dati repository, è necessario fornire agli utenti con il tipo di record che utilizza la soluzione e una descrizione delle relative proprietà.  Quando si recuperano dati, è necessario queste informazioni sui dati di cui che si sta lavorando.

![Compilare repository OMS](media/operations-management-suite-integration/repository.png)


### <a name="populate-the-log-analytics-repository"></a>Popolare repository Analitica Log
Sono disponibili diversi metodi per l'inserimento in archivio OMS.  Il metodo da utilizzare dipenderà fattori, ad esempio i dati di origine in cui si trova, il formato dei dati e che è necessario per supportare i client.  Una volta dati vengono archiviati nel repository, non è rilevante come sono state raccolte.

Le sezioni seguenti descrivono le diverse opzioni per la compilazione repository OMS.

#### <a name="connected-sources-and-data-sources"></a>Connesse origini e le origini dati 
Origini connesse sono le posizioni in cui è possibile recuperare i dati per il repository OMS.  Origini dati e le soluzioni eseguite origini connesse e definiscono gli specifici dati raccolti.  Se l'applicazione scrive dati a una di queste origini dati, è possibile raccogliere essa mediante la configurazione dell'origine dati.  Ad esempio, se l'applicazione consente di creare eventi di registro di sistema, quindi si possono essere raccolti dall'origine dati registro di sistema su un agente di Linux.

- [Origini dati nel Log Analitica](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluzioni

Soluzioni estendono le funzionalità di OMS.  Una soluzione potrebbe raccogliere dati dal sito di origine connesso o può effettuare analisi nei record già stati raccolti nel repository.  Ogni soluzione fornito da Microsoft ha un singolo articolo che fornisce i dettagli sui dati raccolti.

- [Soluzioni in Analitica Log](../log-analytics/log-analytics-add-solutions.md)



#### <a name="http-data-collector-api"></a>Raccolta di dati HTTP API

L'API di raccolta dati di Log Analitica HTTP è una API REST che consente di aggiungere dati JSON all'archivio Log Analitica.  È possibile sfruttare questa API quando è installata un'applicazione che non fornisce dati tramite una delle altre origini dati o le soluzioni.  Può essere utilizzato per popolare l'archivio da qualsiasi client che possono chiamare l'API e non si basa sulla programmazione delle attività insieme di qualsiasi origine dati o una soluzione.

- [Registro Analitica dati HTTP Raccoglitore API](../log-analytics/log-analytics-data-collector-api.md)


### <a name="retrieve-data-from-the-log-analytics-repository"></a>Recuperare i dati dal repository dei Log Analitica

Sono disponibili diversi metodi per il recupero dei dati dal repository OMS.  È possibile gli utenti per recuperare dati tramite la console OMS e fornire diversi tipi di visualizzazioni e analisi.  È inoltre possibile recuperare i dati da un processo esterno, ad esempio un'altra soluzione di gestione.

#### <a name="log-searches"></a>Ricerche dei registri

Tutti i dati archiviati nel repository OMS è disponibile tramite le ricerche log.  Gli utenti possono eseguire le proprie analisi ad hoc nella console di OMS o creare un dashboard con una visualizzazione per una ricerca di log specifico.  Le soluzioni possono contenere le visualizzazioni personalizzate con le visualizzazioni in base a ricerche predefinite.  È possibile utilizzare l'API di registro ricerca per accedere ai dati nel repository OMS da uno strumento di gestione o applicazione esterno.  

- [Ricerche log in Analitica Log](../log-analytics/log-analytics-log-searches.md)
- [Registro Analitica Registro ricerca API REST](../log-analytics/log-analytics-log-search-api.md)
- [Cmdlet di Analitica log](https://msdn.microsoft.com/library/mt188224.aspx)



#### <a name="custom-views"></a>Visualizzazioni personalizzate 
Progettazione visualizzazione consente di creare visualizzazioni personalizzate nella console di OMS che consentono agli utenti di visualizzazione e analisi dei dati della soluzione.  Ogni visualizzazione include un riquadro che viene visualizzato nella pagina principale della console e qualsiasi numero di parti di visualizzazione che si basano sulle ricerche log definite dall'utente.
  
- [Progettazione visualizzazione Analitica log](../log-analytics/log-analytics-view-designer.md)


#### <a name="power-bi"></a>Power BI

Registro Analitica possibile automaticamente Esporta dati dal repository OMS in Power BI in modo che è possibile sfruttare le relative visualizzazioni e gli strumenti di analisi.  Esegue l'esportazione in base alla pianificazione in modo che i dati vengono mantenuti aggiornati. 

- [Esportare dati Analitica Log in Power BI](../log-analytics/log-analytics-powerbi.md)




## <a name="automation"></a>Automazione

OMS automatizzare processi per rispondere ai dati raccolti o eseguire altre funzioni di gestione.  Può raccogliere dati dall'applicazione e inserirlo nell'archivio OMS oppure è possibile automatizzare la correzione di un problema noto in risposta ai dati nell'archivio di. 

![Automazione](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbook

Runbook in Azure automazione eseguire gli script di PowerShell e flussi di lavoro nel cloud Azure.  È possibile usarli per gestire le risorse in Azure o altre risorse che è possibile accedervi dal cloud.  Runbook può anche essere eseguito in un data center locale utilizzando ibrido Runbook lavoro.  È possibile iniziare una runbook dal portale di Azure o da processi esterni utilizzando un numero di metodi, ad esempio PowerShell o l'API di automazione.

- [A partire da un runbook automazione Azure](../automation/automation-starting-a-runbook.md)
- [Cmdlet di automazione Azure](https://msdn.microsoft.com/library/dn690262.aspx)
- [API REST di automazione](https://msdn.microsoft.com/library/mt662285.aspx)
- [Automazione .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Avvisi

Regole di avviso eseguire automaticamente ricerche log in base a una programmazione.  Se i risultati della soddisfano specifici criteri avviso risultante può avviare un runbook in Azure automazione o chiamata webhook che può avviare un processo esterno.  Entrambe le risposte possono includere i dettagli dell'avviso inclusi i dati restituiti nella ricerca del registro.

- [Avvisi nell'Analitica Log](../log-analytics/log-analytics-alerts.md)
- [API di avviso Analitica log](../log-analytics/log-analytics-api-alerts.md)


## <a name="backup-and-site-recovery"></a>Eseguire il backup e il ripristino del sito

Azure eseguire il Backup e il ripristino del sito offrono servizi per proteggere i dati aziendali e verifica della disponibilità di server e le applicazioni.  È possibile sfruttare questi servizi per eseguire scenari quali servizi di backup per un'applicazione o avviare caso di errore di una macchina virtuale.

- [Azure cmdlet per la copia di Backup](https://msdn.microsoft.com/library/mt619253.aspx)
- [Ripristino di Azure del sito all'API REST](https://msdn.microsoft.com/library/azure/mt750497.aspx)
- [Cmdlet di ripristino sito Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluzioni personalizzate

È possibile racchiudere logica di integrazione in una soluzione personalizzata per eseguire nell'area di lavoro o nell'area di lavoro del cliente.  La soluzione può includere uno dei metodi di integrazione in questo articolo oltre ad altre risorse per fornire uno scenario di gestione completa.  Le risorse della soluzione sono disponibili in modo che quando la soluzione viene rimossa, tutte le risorse che ha creato vengono rimosse dall'area di lavoro OMS e Azure abbonamento.

Ad esempio, la soluzione potrebbe includere un runbook automazione per raccogliere ed elaborare dati e quindi compilare repository Analitica Log tramite l'API di raccolta dati HTTP.  È anche possibile includere una visualizzazione personalizzata che presenta e analizza i dati raccolti.  

- Creazione di soluzioni personalizzate (disponibile a breve)    

## <a name="next-steps"></a>Passaggi successivi
- Riferimento [SDK OMS](operations-management-suite-sdk.md) per informazioni tecniche su automazione servizi OMS.  
