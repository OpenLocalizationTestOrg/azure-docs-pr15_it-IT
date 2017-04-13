<properties
 pageTitle="Utilità di pianificazione disponibilità e l'affidabilità"
 description="Utilità di pianificazione disponibilità e l'affidabilità"
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
 ms.date="08/16/2016"
 ms.author="deli"/>


# <a name="scheduler-high-availability-and-reliability"></a>Utilità di pianificazione disponibilità e l'affidabilità

## <a name="azure-scheduler-high-availability"></a>Utilità di pianificazione Azure ad alta disponibilità

Come un servizio di Azure piattaforma principale, utilità di pianificazione di Azure altamente disponibile e le caratteristiche di distribuzione del servizio geografico ridondanti e replica geografico internazionali processo.

### <a name="geo-redundant-service-deployment"></a>Distribuzione di servizio geografico ridondanti

Azure utilità di pianificazione è disponibile tramite l'interfaccia utente in quasi ogni area geografica geografico che è in Azure oggi. L'elenco delle aree Azure utilità di pianificazione è disponibile in è [elencato qui](https://azure.microsoft.com/regions/#services). Se viene visualizzato con un data center nell'area ospitata non è disponibile, le funzionalità di failover di Azure Scheduler sono in modo che il servizio è disponibile un'altra area dati.

### <a name="geo-regional-job-replication"></a>Replica processo geografico internazionali

Non solo è l'utilità di pianificazione di Azure front-end disponibili per le richieste di gestione, ma il proprio lavoro è replicato geografico. Quando c'è un'interruzione in un'area, Azure utilità di pianificazione non riesce sopra e garantisce che viene eseguito il processo da un'altra data center dell'area geografica accoppiata.

Ad esempio, se si è creato un processo in Sud Central US, utilità di pianificazione di Azure replica automaticamente il processo in North Central US. Quando si verifica un errore in Sud Central US, utilità di pianificazione di Azure garantisce che viene eseguito il processo da North Central US. 

![][1]

Di conseguenza, utilità di pianificazione di Azure garantisce che i dati siano all'interno dell'area geografica più ampia stesso in caso di errore Azure. Di conseguenza, non è necessario duplicare il lavoro solo per aggiungere disponibilità: utilità di Azure fornisce automaticamente funzionalità di disponibilità per i processi.

## <a name="azure-scheduler-reliability"></a>Utilità di pianificazione Azure affidabilità

Utilità di pianificazione Azure garantisce propria disponibilità ed approccio è diverso per i processi creati dall'utente. Ad esempio, il lavoro potrebbe richiamare un endpoint HTTP non disponibile. Utilità di pianificazione Azure comunque tenta di eseguire il processo correttamente, consentendo di opzioni alternative di occuparsi di errore. Utilità di pianificazione Azure responsabile in due modi:

### <a name="configurable-retry-policy-via-retrypolicy"></a>Criteri configurabili Riprova tramite "retryPolicy"

Utilità di pianificazione Azure consente di configurare un criterio di tentativi. Per impostazione predefinita, se un processo non riesce, utilità di pianificazione tenta il processo nuovamente quattro volte, intervalli di 30 secondi. È possibile configurare nuovamente il criterio di tentativi da utilizzare maggiormente (ad esempio, dieci volte, intervalli di 30 secondi) o maggiore (ad esempio, due volte, in base a intervalli giornalieri.)

Come esempio di quando può utile, è possibile creare un processo che esegue una volta alla settimana e richiama un endpoint HTTP. Se l'endpoint HTTP verso il basso per alcune ore quando viene eseguito il processo, è consigliabile non attendere una settimana più per eseguire nuovamente poiché anche il criterio di Riprova predefinito non viene eseguito il processo. In tal caso, si potrebbero riconfigurare criteri Riprova standard a provare ogni tre ore, ad esempio, invece di ogni 30 secondi.

Per informazioni su come configurare un criterio di tentativi, fare riferimento al [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoint-configurability-via-erroraction"></a>Alternativo configurabilità Endpoint tramite "errorAction"

Se l'endpoint di destinazione per il processo di pianificazione Azure rimane non è raggiungibile, utilità di pianificazione di Azure ricorre all'endpoint di gestione degli errori alternativo dopo aver seguito i criteri di tentativi. Se è configurato un endpoint di gestione degli errori alternativo, utilità di pianificazione di Azure richiama. Con un endpoint alternativo, i proprio processi sono altamente disponibili in caso di errore.

Se ad esempio nella figura seguente, utilità di pianificazione di Azure segue il criterio Riprova per raggiunto un servizio web Reggio Emilia. Dopo i tentativi hanno esito negativo, viene verificato se è presente un'alternativa. Quindi prosegue e inizia effettuare richieste alternativo con lo stesso criterio Riprova.

![][2]

Si noti che lo stesso criterio Riprova viene applicata l'azione originale e l'azione errore alternativo. È anche possibile avere tipo azione dell'azione errore alternativo essere diversi dal tipo di azione dell'azione principale. Ad esempio, mentre l'azione principale può chiamare un endpoint HTTP, l'azione errore invece potrebbe essere una coda di spazio di archiviazione, coda bus del servizio o servizio bus argomento azione che indica la registrazione degli errori.

Per informazioni su come configurare un endpoint alternativo, fare riferimento al [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## <a name="see-also"></a>Vedere anche

 [Che cos'è l'utilità di pianificazione?](scheduler-intro.md)

 [Azure utilità di pianificazione fondamentali, terminologia e gerarchia entità](scheduler-concepts-terms.md)

 [Per iniziare a usare l'utilità di pianificazione nel portale di Azure](scheduler-get-started-portal.md)

 [Piani e fatturazione in utilità di pianificazione di Azure](scheduler-plans-billing.md)

 [Modalità di compilazione di pianificazioni complesse e ricorrenza avanzata con utilità di pianificazione di Azure](scheduler-advanced-complexity.md)

 [Guida di riferimento all'API REST utilità di pianificazione Azure](https://msdn.microsoft.com/library/mt629143)

 [Fare riferimento a Azure dei cmdlet di PowerShell utilità di pianificazione](scheduler-powershell-reference.md)

 [Limiti di Azure utilità di pianificazione, le impostazioni predefinite e codici di errore](scheduler-limits-defaults-errors.md)

 [Azure autenticazione in uscita utilità di pianificazione](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png
