<properties 
   pageTitle="Avviso di gestione di monitoring prodotti Microsoft | Microsoft Azure"
   description="Un avviso indica un problema che richiede attenzione da un amministratore.  In questo articolo vengono descritte le differenze come avvisi vengono creati e gestiti in System Center Operations Manager (SCOM) e registro Analitica e procedure consigliate all'utilizzo di due prodotti per una strategia di gestione degli avvisi ibrida." 
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
   ms.date="09/06/2016"
   ms.author="bwren" />

# <a name="managing-alerts-with-microsoft-monitoring"></a>Gestione degli avvisi di monitoraggio di Microsoft 

Un avviso indica un problema che richiede attenzione da un amministratore.  Vi sono differenze nette tra System Center Operations Manager (SCOM) e Analitica Log in operazioni di gestione famiglia di prodotti (OMS) in termini di creazione avvisi, come vengono gestiti e analizzati e come si riceve una notifica che è stato rilevato un problema critico.

## <a name="alerts-in-operations-manager"></a>Avvisi in Operations Manager
Avvisi nel SCOM generati da Monitor per indicare un problema specifico o singole regole.  Un monitor può generare un avviso quando si entra uno stato di errore quando una regola può generare un avviso per indicare un problema critico non direttamente correlato allo stato di un oggetto gestito.  Management Pack includono una serie di flussi di lavoro che consente di creare avvisi per l'applicazione o servizio che gestiscono.  Parte del processo di configurazione di un nuovo management pack è l'ottimizzazione per assicurarsi di non ricevere avvisi eccessivi per i problemi che non è consigliabile critiche.

![Visualizzazione degli avvisi SCOM](media/operations-management-suite-monitoring-alerts/scom-alert-view.png)

SCOM fornisce completa di gestione degli avvisi di avvisi con uno stato che può essere modificato dagli amministratori come funzionano per risolvere il problema.  Quando è stato risolto il problema, l'amministratore imposta l'avviso verso chiuso momento in cui non verrà più visualizzato nelle visualizzazioni la visualizzazione degli avvisi attivi.  Avvisi generati da Monitor possono essere risolti automaticamente quando il monitor ritorna allo stato di integrità.

![Stato dell'avviso](media/operations-management-suite-monitoring-alerts/scom-alert-status.png)

## <a name="alerts-in-log-analytics"></a>Avvisi nell'Analitica Log
Un avviso in Analitica Log viene creato da una query di log che viene eseguita automaticamente a intervalli regolari.  È possibile creare una regola di avviso da una query di log.  Se la query restituisca i risultati che soddisfano i criteri specificati, viene creato un avviso.  Può trattarsi di una query specifica che consente di creare un avviso se viene rilevato un determinato evento oppure è possibile utilizzare una query più generale che consente di cercare qualsiasi evento di errore relativo a una determinata applicazione.

Registro Analitica avvisi scritto all'archivio OMS come un evento e possono essere recuperati con una query di log.  Non è stato come SCOM eventi in modo che è possibile indicare se è stato risolto il problema.

![Avviso OMS](media/operations-management-suite-monitoring-alerts/oms-alert.png)

Quando SCOM viene utilizzato come origine dati per Analitica Log avvisi SCOM vengono scritte all'archivio OMS durante la creazione e modifica.  

![Avviso SCOM](media/operations-management-suite-monitoring-alerts/scom-alert.png)

[Soluzione di gestione degli avvisi](http://technet.microsoft.com/library/mt484092.aspx) viene fornito un riepilogo di avvisi attivi e diverse query comuni per recuperare diversi set di avvisi.  In questo modo si analisi più efficienti gli avvisi di un report in SCOM.  È possibile eseguire il drill-dai riepiloghi ai dati di dettagliati e creare query ad hoc per recuperare i diversi gruppi di avvisi.

![Soluzione di gestione degli avvisi](media/operations-management-suite-monitoring-alerts/alert-management.png)

## <a name="notifications"></a>Notifiche
Le notifiche di SCOM inviare posta elettronica o un testo in risposta agli avvisi che soddisfano specifici criteri.  È possibile creare sottoscrizioni di notifica diversi che sono presenti delle persone diverse una notifica a seconda di criteri quali l'oggetto che si sta controllando, la gravità dell'avviso, il tipo di problema rilevato o l'ora del giorno.

Alcuni abbonamenti è utilizzabile per implementare una strategia di notifica di completamento per un numero elevato di management pack.

![Avvisi SCOM](media/operations-management-suite-monitoring-alerts/alerts-overview-scom.png)

Analitica log possibile invio di una notifica tramite posta elettronica che è stato creato un avviso impostando un'azione di notifica di messaggio di posta elettronica per ogni [regola di avviso](http://technet.microsoft.com/library/mt614775.aspx).  Non ha la possibilità di SCOM Sottoscrivi avvisi molteplici con una singola regola.  Inoltre, è necessario creare le regole di avviso poiché non è disponibile alcuna preconfigurato OMS.

![Registro Analitica avvisi](media/operations-management-suite-monitoring-alerts/alerts-overview-oms.png)

Non è completamente possibile gestire avvisi SCOM nel Log Analitica attraverso dal momento che è possibile modificare solo nella Console.  Registro Analitica può essere utilizzato come parte di una gestione degli avvisi elaborare attraverso per fornire tale SCOM solo strumenti di analisi non è disponibile.

## <a name="alert-remediation"></a>Risoluzione degli avvisi
[Monitoraggio](http://technet.microsoft.com/library/mt614775.aspx) fa riferimento a un tentativo di correggere automaticamente il problema identificato da un avviso.
  
SCOM consente di eseguire Diagnostica e ripristini in risposta a un monitor immettere uno stato.  Si verifica questo evento simultaneo monitor la creazione dell'avviso.  Diagnostica e ripristini vengono in genere implementati come script utilizzabile con l'agente.  La diagnostica tenta di raccogliere ulteriori informazioni sul problema rilevato durante il tentativo di risolvere il problema di un ripristino.

Registro Analitica consente di avviare un [runbook automazione Azure](https://azure.microsoft.com/documentation/services/automation/) o una chiamata un webhook in risposta a un avviso Log Analitica.  Runbook può contenere logica complessa implementata in PowerShell.  Lo script viene eseguito in Azure e possa accedere a qualsiasi Azure risorse o risorse esterne disponibili dal cloud.  Automazione Azure hanno la possibilità di eseguire runbook su un server nel centro dati locale, ma questa caratteristica non è attualmente disponibile quando si avvia dal runbook in risposta agli avvisi dei Log Analitica.

Entrambi ripristini in SCOM e runbook in OMS può includere gli script di PowerShell, ma ripristini sono difficili da creare e gestire perché deve essere contenuti all'interno di un management pack.  Runbook sono archiviate nel modello di automazione Azure che fornisce funzionalità di creazione, test e gestione runbook.

Se si usa SCOM come origine dati per Log Analitica, è possibile creare un avviso di Log Analitica utilizzando una query di log per recuperare gli avvisi SCOM salvati nell'archivio OMS.  In questo modo è possibile eseguire un runbook automazione Azure in risposta a un avviso SCOM.  Naturalmente, poiché dal runbook verrà eseguito in Azure, questo non sarebbe una strategia valida per il ripristino dei problemi in locale.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni dettagliate degli [avvisi in System Center Operations Manager (SCOM)](https://technet.microsoft.com/library/hh212913.aspx).