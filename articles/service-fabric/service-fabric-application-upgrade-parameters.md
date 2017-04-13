
<properties
   pageTitle="Aggiornamento dell'applicazione: parametri aggiornamento | Microsoft Azure"
   description="Descrive i parametri relativi all'aggiornamento di un'applicazione di servizio tessuti, inclusi verifiche integrità per eseguire e i criteri per annullare automaticamente l'aggiornamento."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>



# <a name="application-upgrade-parameters"></a>Parametri di aggiornamento dell'applicazione

In questo articolo vengono illustrati i vari parametri che si applicano durante l'aggiornamento di un'applicazione di Azure servizio tessuti. I parametri includono il nome e la versione dell'applicazione. Si tratta di comandi che controllano il timeout e i controlli che vengono applicati durante l'aggiornamento e specificano i criteri che devono essere applicati quando un aggiornamento ha esito negativo.


<br>

| Parametro | Descrizione |
| --- | --- |
| NomeApplicazione | Nome dell'applicazione che si sta aggiornando. Esempi: tessuti: / VisualObjects, tessuti: / ClusterMonitor  |
| TargetApplicationTypeVersion | La versione dell'applicazione digitare che le destinazioni di aggiornamento. |
| FailureAction | Azione eseguita dal servizio tessuti quando l'aggiornamento ha esito negativo. L'applicazione può essere ripristinata la versione pre-aggiornamento (ripristino) o l'aggiornamento può essere arrestato nel dominio di aggiornamento corrente. In tal caso, la modalità di aggiornamento viene modificata anche su manuale. Valori consentiti sono manuale e ripristino. |
| HealthCheckWaitDurationSec | Il tempo di attesa (in secondi) dopo l'aggiornamento è stato completato nel dominio aggiornamento prima di servizio tessuti valuta lo stato dell'applicazione. La durata anche può essere considerata ora che un'applicazione deve essere eseguita prima che possono essere considerato integro. Se la verifica dell'integrità passa, il processo di aggiornamento procede al successivo aggiornamento dominio.  Se la verifica dell'integrità non riesce, tessuti servizio Ritenta per un intervallo (UpgradeHealthCheckInterval) la verifica dell'integrità nuovamente fino a raggiungere la HealthCheckRetryTimeout. Il valore consigliato e predefinito è 0 secondi. |
| HealthCheckRetryTimeoutSec | La durata (in secondi) tessuti tale servizio continua eseguire analisi dell'integrità della prima di dichiarare l'aggiornamento come non riuscita. Il valore predefinito è 600 secondi. La durata viene avviata raggiunto HealthCheckWaitDuration. All'interno di questo HealthCheckRetryTimeout servizio tessuti potrebbero risultare più verifiche integrità dello stato delle applicazioni. Il valore predefinito è 10 minuti e deve essere personalizzato in modo appropriato per l'applicazione. |
| HealthCheckStableDurationSec | La durata (in secondi) per verificare che l'applicazione è stabile prima di spostarlo al successivo aggiornamento dominio o completato l'aggiornamento. In questo periodo di attesa viene utilizzato per impedire modifiche non vengano rilevate sanitario destra dopo la verifica dell'integrità viene eseguita. Il valore predefinito è 120 secondi e deve essere personalizzato in modo appropriato per l'applicazione. |
| UpgradeDomainTimeoutSec | Tempo massimo (in secondi) per l'aggiornamento di un unico dominio di aggiornamento. Se si raggiunge il timeout, l'aggiornamento si arresta e continua in base all'impostazione per UpgradeFailureAction. Il valore predefinito non è mai (infinito) e deve essere personalizzato in modo appropriato per l'applicazione. |
| UpgradeTimeout | Timeout (in secondi) che si applica per l'intero aggiornamento. Se si raggiunge il timeout, viene attivata l'aggiornamento si interrompe e UpgradeFailureAction. Il valore predefinito non è mai (infinito) e deve essere personalizzato in modo appropriato per l'applicazione. |
| UpgradeHealthCheckInterval | Frequenza che lo stato di salute sia selezionato. Questo parametro viene specificato nella sezione ClusterManager del _cluster_ _manifesto_e non è stato specificato come parte del cmdlet aggiornamento. Il valore predefinito è 60 secondi.  |






<br>
## <a name="service-health-evaluation-during-application-upgrade"></a>Valutazione di integrità dei servizi durante l'aggiornamento dell'applicazione

<br>
I criteri di valutazione di integrità sono facoltativi. Se i criteri di valutazione di integrità non vengono specificati all'avvio di un aggiornamento, tessuti servizio vengono utilizzati i criteri di integrità di applicazione specificati nel ApplicationManifest.xml dell'istanza dell'applicazione.


<br>

| Parametro | Descrizione |
| --- | --- |
| ConsiderWarningAsError | Valore predefinito è False. Considerare gli eventi di stato di avviso per l'applicazione come errori durante la valutazione dell'integrità dell'applicazione durante l'aggiornamento. Per impostazione predefinita, tessuti servizio non viene valutata eventi relativi allo stato di avviso per essere errori (errori), in modo che l'aggiornamento può procedere anche se sono presenti gli avvisi.   |
| MaxPercentUnhealthyDeployedApplications | Predefinito e un valore consigliato è uguale a 0. Specificare il numero massimo di applicazioni (vedere la [sezione integrità](service-fabric-health-introduction.md)) che possono essere danneggiate prima dell'applicazione viene considerato non corretti e non riesce l'aggiornamento. Questo parametro definisce lo stato dell'applicazione sul nodo e consente di rilevare i problemi durante l'aggiornamento. In genere le repliche dell'applicazione è possibile ottenere bilanciamento del carico a altro nodo, che consente l'applicazione visualizzare integro, in modo da consentire l'aggiornamento continuare. Specificando un integrità MaxPercentUnhealthyDeployedApplications rigido tessuti servizio possibile individuare rapidamente un problema con il pacchetto dell'applicazione e permettono di produrre una rapida aggiornamento hanno esito negativo. |
| MaxPercentUnhealthyServices | Predefinito e un valore consigliato è uguale a 0. Specificare il numero massimo di servizi nell'istanza dell'applicazione che può essere non corretti prima dell'applicazione viene considerato non corretti e non riesce l'aggiornamento. |
| MaxPercentUnhealthyPartitionsPerService | Predefinito e un valore consigliato è uguale a 0. Specificare il numero massimo di partizioni in un servizio che può essere non corretti prima che il servizio è considerato non corretti. |
| MaxPercentUnhealthyReplicasPerPartition | Predefinito e un valore consigliato è uguale a 0. Specificare il numero massimo di repliche nella partizione che può essere danneggiato prima che la partizione viene considerata non corretti. |
| UpgradeReplicaSetCheckTimeout | **Servizio senza informazioni sullo stato**- all'interno di un unico dominio di aggiornamento, tessuti servizio tenta di verificare che siano disponibili altre istanze del servizio. Se il conteggio delle istanze destinazione più monitor, tessuti servizio attesa di più istanze sia disponibile fino a un valore di timeout massimo. Questo valore di timeout viene specificato utilizzando la proprietà UpgradeReplicaSetCheckTimeout. Se il timeout scade, tessuti servizio procede con l'aggiornamento, indipendentemente dal numero di istanze del servizio. Se il conteggio delle istanze destinazione, tessuti servizio non attendere e procede immediatamente con l'aggiornamento. **Servizio informazioni sullo stato**, ossia in un unico dominio di aggiornamento, tessuti servizio tenta di verificare che il set di replica è una base. Servizio tessuti attesa di una base sia disponibile fino a un valore di timeout massimo (specificato dalla proprietà UpgradeReplicaSetCheckTimeout). Se il timeout scade, tessuti servizio procede con l'aggiornamento, indipendentemente dalla base. Questa impostazione viene impostato come mai (infinito) quando si inoltra in sequenza e 900 secondi quando ripristino dello stato precedente. |
| /Forcerestart | Se si aggiorna una configurazione o un pacchetto di dati senza aggiornare il codice del servizio, il riavvio del servizio solo se la proprietà /forcerestart è impostata su true. Una volta completato l'aggiornamento, tessuti servizio di notifica al servizio è disponibile un nuovo pacchetto di configurazione o di un pacchetto di dati. Il servizio è responsabile per applicare le modifiche. Se necessario, possibile riavviare il servizio stesso. |



<br>
<br>
È possibile specificare i criteri MaxPercentUnhealthyServices, MaxPercentUnhealthyPartitionsPerService e MaxPercentUnhealthyReplicasPerPartition per ogni tipo di servizio per un'istanza dell'applicazione. Impostando i parametri di servizio consente di un'applicazione contenga tipi diversi servizi con i criteri di valutazione diversi. Ad esempio, un tipo di servizio gateway senza stato può avere un MaxPercentUnhealthyPartitionsPerService diverso da un tipo di servizio informazioni sullo stato motore per una particolare istanza dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare l'applicazione mediante Visual Studio](service-fabric-application-upgrade-tutorial.md) fornisce un aggiornamento dell'applicazione utilizzando Visual Studio.

[Aggiornare l'applicazione di uso di Powershell](service-fabric-application-upgrade-tutorial-powershell.md) fornisce un aggiornamento dell'applicazione tramite PowerShell.

Effettuare l'aggiornamento delle applicazioni compatibili per imparare a utilizzare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come usare la funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento a [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere i problemi comuni in aggiornamenti dell'applicazione facendo riferimento alla procedura di [Risoluzione dei problemi di aggiornamento delle applicazioni](service-fabric-application-upgrade-troubleshooting.md).
 
