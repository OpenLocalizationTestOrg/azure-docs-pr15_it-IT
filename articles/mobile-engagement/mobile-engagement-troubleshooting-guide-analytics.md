<properties 
   pageTitle="Azure coinvolgimento Mobile risoluzione dei problemi guida - Analitica" 
   description="Risoluzione dei problemi Analitica, monitoraggio, segmentazione e Dashboard in Azure Mobile coinvolgimento" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-analytics-monitoring-segmentation-and-dashboard-issues"></a>Guida alla risoluzione dei problemi per problemi Analitica, monitoraggio, segmentazione e Dashboard

Si verificano le seguenti possibili problemi riscontrati con come Azure Mobile coinvolgimento raccoglie informazioni sulle applicazioni e dispositivi, gli utenti.

## <a name="missingdelayed-information"></a>Informazioni mancanti/posticipato

### <a name="issue"></a>Problema
- Informazioni subisce un ritardo nella visualizzazione Analitica, segmentazione o Dashboard.
- Informazioni non sono presente in monitoraggio.
- Informazioni non sono presente Analitica, segmentazione o Dashboard.
- Limiti di raggiungere segmentazione.

### <a name="causes"></a>Cause

- È possibile utilizzare l'API Analitica, API Monitor, ed è visibile tramite l'API segmenti API per verificare l'eventuale qualsiasi tipo di dati presente l'interfaccia utente.
- Se SDK coinvolgimento di Azure Mobile non è corretto integrato l'app non sarà possibile visualizzare le informazioni nel Analitica, segmentazione, monitoraggio o dashboard.
- Segmenti non possono essere modificato dopo la creazione, segmenti possono essere solo (copiato) "duplicato" o "eliminato" (eliminati). Segmenti possono contenere solo 10 criteri.
- Il modo migliore per verificare le informazioni mancanti dal monitoraggio consiste nel configurare un dispositivo di prova, disinstallare e/o reinstallare l'app nel dispositivo test.
- Informazioni verranno aggiornate ogni 24 ore per Analitica, segmentazione o dashboard.
- Informazioni di nuovi segmenti potrebbero non essere visualizzate fino a 24 ore dopo la creazione anche se il segmento è basato sulle informazioni precedente.
- Filtrare i dati dell'analitica nell'interfaccia utente verranno visualizzati tutti gli esempi di questo tipo indipendentemente dalla versione dell'applicazione (ad esempio "blocchi" filtrate in base al nome verranno visualizzato dalla versione 1 e 2 dell'app).
- Periodo di tempo per Analitica è in base alla data le impostazioni degli utenti dei dispositivi, in modo un utente il cui telefono impostato la data in modo non corretto può visualizzate nel periodo di tempo errato.
- Nessun lato server dati sono stato effettuato l'accesso quando si utilizza il pulsante "verificare" inserisce, solo i dati vengono registrati per le campagne push reali.

## <a name="cant-locate-items-in-ui"></a>Non è possibile individuare gli elementi nell'interfaccia utente

### <a name="issue"></a>Problema
- Non è possibile creare segmenti in base a determinate incorporate o informazioni personalizzate app tag criteri.
- Impossibile trovare alcune incorporate o informazioni personalizzate app tag criteri Analitica, monitoraggio o dashboard.
- Non è possibile interpretare i dati in Analitica, monitoraggio, segmentazione o dashboard.

### <a name="causes"></a>Cause

- Alcuni incorporati in elementi e tag info app sono disponibili come criteri di push solo ma potrebbe non essere aggiunti a un segmento o visibile da Analitica, monitoraggio o Dashboard. 
- Per gli elementi predefiniti e tag di informazioni app che non possono essere aggiunti a un segmento, sarà necessario all'elenco di configurazione di criteri in ogni campagna di destinazione eseguire la stessa funzione di destinazione in base a un segmento.
- Visualizzare il menu di scelta rapida nella sezione Analitica, monitoraggio, segmentazione e dashboard dell'interfaccia utente coinvolgimento di Azure Mobile per altre informazioni e alle informazioni.

## <a name="crash-troubleshooting"></a>Arresto anomalo di risoluzione dei problemi

### <a name="issue"></a>Problema
- Applicazione determina l'arresto anomalo visualizzate nel Analitica, monitoraggio o Dashboard.

### <a name="causes"></a>Cause

- Per risolvere i problemi di applicazione determina l'arresto anomalo visto in Analitica, monitoraggio o Dashboard assicurarsi di selezionare le note sulla versione per i problemi noti con le versioni precedenti di SDK.
- Per la risoluzione di errori delle applicazioni, eseguire un evento da un dispositivo di test con l'applicazione installata e cercare l'ID di dispositivo nella sezione "Monitor – eventi" dell'interfaccia utente di impegno di Azure Mobile. Eseguire quindi l'evento che causa l'applicazione per arrestarsi in modo anomalo e cercare ulteriori informazioni nella sezione "Arresto anomalo – Monitor" dell'interfaccia utente di impegno di Azure Mobile. 

 
