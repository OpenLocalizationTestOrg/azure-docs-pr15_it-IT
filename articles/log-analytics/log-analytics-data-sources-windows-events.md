<properties 
   pageTitle="Dei registri eventi di Windows in Log Analitica | Microsoft Azure"
   description="Registri eventi di Windows sono una delle origini dati più comune utilizzate da Log Analitica.  In questo articolo viene descritto come configurare l'insieme di registri eventi di Windows e dettagli dei record che creano repository OMS."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="windows-event-log-data-sources-in-log-analytics"></a>Origini dati registro eventi di Windows in Analitica Log

Registri eventi di Windows sono uno dei più comuni [origini dati](log-analytics-data-sources.md) utilizzato per gli agenti di Windows, poiché si tratta di metodo utilizzato dalla maggior parte delle applicazioni per registrare le informazioni e gli errori.  È possibile raccogliere gli eventi dai registri standard, ad esempio sistema e applicazione oltre a specificare gli eventuali log personalizzati creati da è necessario eseguire il monitoraggio delle applicazioni.

![Eventi di Windows](media/log-analytics-data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Registri eventi di Windows configurazione

Configurare i registri eventi di Windows dal [menu di dati di impostazioni del Log di Analitica](log-analytics-data-sources.md#configuring-data-sources).

Registro Analitica raccoglie solo gli eventi dai registri eventi di Windows specificati nelle impostazioni.  È possibile aggiungere un nuovo file di log a digitare il nome di log e facendo clic su **+**.  Per ogni log verranno raccolti solo gli eventi con gravità selezionato.  Controllare la gravità per il log particolare che si desidera raccogliere.  Non è possibile fornire eventuali altri criteri per filtrare gli eventi.

![Configurare gli eventi di Windows](media/log-analytics-data-sources-windows-events/configure.png)


## <a name="data-collection"></a>Raccolta di dati

Registro Analitica raccoglie ogni evento che corrisponde a una gravità selezionato da un registro eventi monitorato durante l'evento viene creato.  L'agente registra la posizione in ogni registro eventi raccolti da.  Se l'agente è in linea per un periodo di tempo, quindi Log Analitica raccoglie eventi dall'ultimo libero, anche se gli eventi creati mentre l'agente non è offline.


## <a name="windows-event-records-properties"></a>Proprietà record eventi di Windows

Record degli eventi di Windows con un tipo di **evento** in modo che le proprietà nella tabella seguente.

| Proprietà | Descrizione |
|:--|:--|
| Computer            | Nome del computer in cui l'evento raccolti da. |
| EventCategory       | Categoria dell'evento. |
| EventData           | Tutti i dati in formato non elaborato. |
| EventID             | Numero dell'evento. |
| EventLevel          | Gravità dell'evento in formato numerico. |
| EventLevelName      | Gravità dell'evento sotto forma di testo. |
| Registro eventi            | Nome del log raccolti dall'evento. |
| ParameterXml        | Valori dei parametri di evento in formato XML. |
| ManagementGroupName | Nome del gruppo di gestione per gli agenti SCOM.  Per altri agenti, si tratta AOI-<workspace ID> |
| RenderedDescription | Descrizione dell'evento con valori di parametro |
| Origine              | Origine dell'evento. |
| SourceSystem  | Tipo di evento raccolti dall'agente. <br> OpsManager: agente di Windows, uno dei due pubblicitario connettersi o SCOM <br> Linux: tutti gli agenti Linux  <br> AzureStorage-diagnostica Windows Azure |
| TimeGenerated       | Data e ora che dell'evento è stato creato in Windows. |
| Nome utente            | Nome utente dell'account che ha eseguito l'accesso all'evento. |



## <a name="log-searches-with-windows-events"></a>Ricerche dei registri eventi di Windows

Nella tabella seguente vengono forniti esempi di ricerche log che recuperano record degli eventi di Windows.

| Query | Descrizione |
|:--|:--|
| Tipo = evento | Tutti gli eventi di Windows. |
| Tipo = evento EventLevelName = errore | Tutti gli eventi di Windows con gravità dell'errore. |
| Tipo = evento & #124; Misurare Count dall'origine | Eventi di conteggio di Windows dall'origine. |
| Tipo = evento EventLevelName = errore & #124; Misurare Count dall'origine | Eventi di errore conteggio di Windows dall'origine. |

## <a name="next-steps"></a>Passaggi successivi

- Configurare Analitica Log per raccogliere altre [origini dati](log-analytics-data-sources.md) per l'analisi.
- Informazioni sulle [ricerche dei registri](log-analytics-log-searches.md) analizzare i dati raccolti mediante le soluzioni e le origini dati.  
- Utilizzare i [Campi personalizzati](log-analytics-custom-fields.md) per analizzare i record di eventi nei singoli campi.
- Configurare l' [insieme di contatori](log-analytics-data-sources-performance-counters.md) dagli agenti di Windows.