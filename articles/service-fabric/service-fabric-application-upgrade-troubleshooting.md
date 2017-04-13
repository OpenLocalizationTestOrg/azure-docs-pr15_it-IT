<properties
   pageTitle="Risoluzione dei problemi di aggiornamento delle applicazioni | Microsoft Azure"
   description="In questo articolo vengono illustrati alcuni problemi comuni intorno l'aggiornamento di un'applicazione di servizio tessuti e viene spiegato come risolverli."
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

# <a name="troubleshoot-application-upgrades"></a>Risolvere i problemi di aggiornamento delle applicazioni

In questo articolo vengono illustrati alcuni dei problemi comuni intorno l'aggiornamento di un'applicazione di Azure servizio tessuti e viene spiegato come risolverli.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Risolvere i problemi di un aggiornamento dell'applicazione non riuscito

Quando un aggiornamento non riesce, l'output del comando **Get-ServiceFabricApplicationUpgrade** contiene informazioni aggiuntive per il debug dell'errore.  L'elenco seguente specifica come è possono utilizzare le informazioni aggiuntive:

1. Identificare il tipo di errore.
2. Individuare la causa dell'errore.
3. Isolare uno o più componenti di esito negativo per un'analisi più approfondita.

Queste informazioni sono disponibili quando servizio tessuti rileva l'errore indipendentemente dal fatto che **FailureAction** per annullare o sospendere l'aggiornamento.

### <a name="identify-the-failure-type"></a>Identificare il tipo di errore

In output di **Get-ServiceFabricApplicationUpgrade**, **FailureTimestampUtc** identifica il timestamp (in formato UTC) in cui è stato rilevato un errore di aggiornamento da tessuti di servizio e **FailureAction** è stato attivato. **FailureReason** identifica uno dei tre possibili cause dell'errore di alto livello:

1. UpgradeDomainTimeout - indica che un determinato dominio aggiornamento ha richiesto troppo tempo per completare e **UpgradeDomainTimeout** scaduto.
2. OverallUpgradeTimeout - indica che l'aggiornamento globale ha richiesto troppo tempo per completare e **UpgradeTimeout** scaduto.
3. Il controllo diagnostico - indica che dopo l'aggiornamento di un dominio di aggiornamento, l'applicazione è rimasto non corretti in base a criteri dello stato specificato **HealthCheckRetryTimeout** scaduto.

Queste voci solo visualizzato nell'output se l'aggiornamento ha esito negativo e inizia ripristino dello stato precedente. Ulteriori informazioni vengono visualizzate a seconda del tipo dell'errore.

### <a name="investigate-upgrade-timeouts"></a>Provare a utilizzare timeout di aggiornamento

Eseguire l'aggiornamento timeout errori sono in genere causati da problemi di disponibilità dei servizi. L'output dopo il paragrafo è tipica degli aggiornamenti in servizio repliche o istanze non avviati nella nuova versione di codice. Il campo **UpgradeDomainProgressAtFailure** consente di acquisire uno snapshot di tutte le operazioni di aggiornamento in sospeso al momento dell'errore.

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
~~~

In questo esempio, l'aggiornamento non riuscita nell'aggiornamento del dominio *MYUD1* e due partizioni (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* e *4b43f4d8-b26b-424e-9307-7a7a62e79750*) sono state bloccate. Le partizioni sono state bloccate perché il runtime non è riuscito a inserire repliche principale (*WaitForPrimaryPlacement*) nei nodi di destinazione *il nodo 1* e *Nodo4*.

Comando **Get-ServiceFabricNode** può essere utilizzato per verificare che siano questi due nodi di aggiornamento del dominio *MYUD1*. *UpgradePhase* indica *PostUpgradeSafetyCheck*, il che significa che si verificano questi controlli di protezione dopo avere completato l'aggiornamento di tutti i nodi l'aggiornamento del dominio. Tutte le informazioni fa riferimento a un potenziale problema con la nuova versione del codice dell'applicazione. I problemi più comuni sono servizio errori nell'apertura o promozione a percorsi di codice principale.

Un *UpgradePhase* di *PreUpgradeSafetyCheck* indica che si sono verificati problemi di preparazione del dominio aggiornamento prima che è stata eseguita. In questo caso, i problemi più comuni sono gli errori di servizio nella chiusura o retrocessione da percorsi di codice principale.

Corrente **UpgradeState** è *RollingBackCompleted*, pertanto è stata eseguita l'aggiornamento originale con un'operazione di ripristino **FailureAction**, che ripristinati automaticamente l'aggiornamento in caso di errore. Se l'aggiornamento originale è stato eseguito con un manuale **FailureAction**, quindi l'aggiornamento da invece in uno stato sospeso per consentire live il debug dell'applicazione.

### <a name="investigate-health-check-failures"></a>Esaminare gli errori di controllo dell'integrità

Errori di controllo dell'integrità possono essere avviati da vari problemi che possono verificarsi dopo tutti i nodi di un dominio aggiornamento completato l'aggiornamento e passando tutti i controlli di sicurezza. L'output che segue è tipico di un errore di aggiornamento a causa di verifiche integrità non riuscito. Il campo **UnhealthyEvaluations** consente di acquisire uno snapshot dei controlli che non è al momento dell'aggiornamento in base alle specificato [criterio di integrità](service-fabric-health-introduction.md).

~~~
PS D:\temp> Get-ServiceFabricApplicationUpgrade fabric:/DemoApp

ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
~~~

In analisi degli errori di controllo dell'integrità della prima richiede la comprensione del modello di integrità dei servizi tessuti. Ma anche senza ad esempio una conoscenza approfondita, è possibile vedere che due servizi siano danneggiati: *tessuti: / DemoApp/Svc3* e *tessuti: / DemoApp/Svc2*, insieme di report di stato di errore ("InjectedFault" in questo caso). In questo esempio, due di quattro servizi sono danneggiati, sotto la destinazione predefinita di 0% non corretti (*MaxPercentUnhealthyServices*).

L'aggiornamento è stato sospesi al momento dell'errore specificando un **FailureAction** del manuale quando si avvia l'aggiornamento. Questa modalità consente di esaminare il sistema di live in stato di errore prima di eseguire altre azioni.

### <a name="recover-from-a-suspended-upgrade"></a>Recupero da un aggiornamento sospeso

Con un ripristino **FailureAction**, non c'è alcun ripristino necessario, in quanto l'aggiornamento automatico tornare all'esito negativo. Con un manuale **FailureAction**, sono disponibili diverse opzioni di ripristino:

1. Attivare manualmente un ripristino dello stato precedente
2. Procedere con il resto dell'aggiornamento manuale
3. Riprendere l'aggiornamento monitorata

Il comando **Start ServiceFabricApplicationRollback** può essere utilizzato in qualsiasi momento per avviare Ripristino dell'applicazione. Dopo il comando restituisce correttamente, la richiesta di ripristino è stata registrata nel sistema e inizia immediatamente dopo.

Il comando **Curriculum ServiceFabricApplicationUpgrade** può essere utilizzato per procedere con il resto dell'aggiornamento manualmente un aggiornamento del dominio alla volta. In questa modalità vengono effettuati controlli di sicurezza solo dal sistema. Non è più integrità verificata. Questo comando può essere utilizzato solo quando *UpgradeState* Mostra *RollingForwardPending*, il che significa che l'aggiornamento del dominio corrente ha completato l'aggiornamento ma quella successiva non è avviato (in sospeso).

Il comando **Aggiorna ServiceFabricApplicationUpgrade** può essere utilizzato per riprendere l'aggiornamento monitorata con entrambi safety e sanitario in corso.

~~~
PS D:\temp> Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored

UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :

PS D:\temp>
~~~

L'aggiornamento continua dall'aggiornamento del dominio in cui era stata sospesa e usare lo stesso aggiornamento parametri e criteri di integrità come prima. Se necessario, tutti i parametri di aggiornamento e criteri di integrità nell'output precedente possono essere modificate nello stesso comando quando si riattiva l'aggiornamento. In questo esempio, l'aggiornamento è stato ripristinare in modalità monitorate, con i parametri e i criteri di integrità invariati.

## <a name="further-troubleshooting"></a>La risoluzione del problema

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Servizio tessuti non stanno seguendo i criteri di integrità specificato

Causa possibile 1:

Servizio tessuti converte tutte le percentuali nei valori effettivi delle entità (ad esempio repliche, le partizioni e servizi) per la valutazione dell'integrità e arrotonda sempre per eccesso a intere entità. Ad esempio, se la massima _MaxPercentUnhealthyReplicasPerPartition_ è 21% e assenza cinque replica, tessuti servizio grazie a fino a due repliche non corretti (tale is,'Math.Ceiling (5\*0,21)). In questo modo, criteri di integrità impostare di conseguenza.

Causa possibile 2:

Criteri di integrità vengono specificati in termini di percentuali di servizi totali e istanze del servizio non specifiche. Ad esempio, prima di un aggiornamento, se dispone di un'applicazione di quattro service istanze A, B, C e D, dove servizio D è danneggiato, ma con un impatto minimo per l'applicazione. È possibile ignorare il servizio non corretti noto D durante l'aggiornamento e impostare il parametro *MaxPercentUnhealthyServices* al 25%, presupponendo che solo a, B e C devono essere corretti.

Tuttavia, durante l'aggiornamento, D può diventare integro mentre C diventa non corretti. L'aggiornamento potrebbe riuscire perché solo il 25% dei servizi è danneggiato. Tuttavia, potrebbero verificarsi errori imprevisti a causa di C viene danneggiato in modo imprevisto anziché D. In questo caso, D dovrebbe essere basato come tipo di servizio diverso da A, B e C. Poiché i criteri di integrità sono specificati per ogni tipo di servizio, percentuale non corretti diversa soglie possono essere applicate a diversi servizi. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Non è stato specificato un criterio di aggiornamento dell'applicazione integrità, ma il problema persiste per alcuni timeout che è mai stato specificato

Quando Criteri di integrità non sono disponibili per la richiesta di aggiornamento, vengono create da *ApplicationManifest.xml* della versione applicazione corrente. Ad esempio, se si esegue l'aggiornamento dell'applicazione X dalla versione 1.0 alla versione 2.0, criteri di integrità applicazione specificata per la versione 1.0 vengono utilizzati. Se un criterio di integrità diverso deve essere utilizzato per l'aggiornamento, il criterio deve essere specificato come parte della chiamata API di aggiornamento dell'applicazione. I criteri specificati come parte della chiamata API vengono applicate solo durante l'aggiornamento. Al termine dell'aggiornamento, vengono utilizzati i criteri specificati in *ApplicationManifest.xml* .

### <a name="incorrect-time-outs-are-specified"></a>Timeout non corretti vengono specificati

Può essere utile su cosa succede quando timeout sono impostate in modo incoerente. Ad esempio, potrebbe essere un *UpgradeTimeout* è minore di *UpgradeDomainTimeout*. La risposta è che viene restituito un errore. Se *UpgradeDomainTimeout* è minore di somma di *HealthCheckWaitDuration* e *HealthCheckRetryTimeout*o *UpgradeDomainTimeout* è minore della somma di *HealthCheckWaitDuration* e *HealthCheckStableDuration*, vengono restituiti errori.

### <a name="my-upgrades-are-taking-too-long"></a>Personali aggiornamenti siano impiegando troppo tempo

Il tempo per il completamento dell'aggiornamento dipende dal verifiche integrità e timeout specificato. Verifica dell'integrità e timeout dipendono dal tempo impiegato per copiare, distribuire e stabilizzare l'applicazione. Viene anche aggressive timeout con, è probabile più gli aggiornamenti non riusciti, è consigliabile ripagati iniziando timeout più tempo.

Ecco un rapido promemoria sulle modalità di interazione di timeout con l'ora di aggiornamento:

Aggiornamenti per un aggiornamento del dominio non è possibile completare più velocemente di quanto *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Errore di aggiornamento non può essere più velocemente di quanto *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

I tempi di aggiornamento per un aggiornamento del dominio sono limitato dalla *UpgradeDomainTimeout*.  Se lo stato dell'applicazione mantiene il passaggio avanti e indietro, quindi l'aggiornamento scadenza in *UpgradeDomainTimeout* *HealthCheckRetryTimeout* e *HealthCheckStableDuration* siano diverso da zero. *UpgradeDomainTimeout* avvia rovescia dopo l'aggiornamento per l'aggiornamento del dominio corrente inizia.

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare l'applicazione mediante Visual Studio](service-fabric-application-upgrade-tutorial.md) fornisce un aggiornamento dell'applicazione utilizzando Visual Studio.

[Aggiornare l'applicazione di uso di Powershell](service-fabric-application-upgrade-tutorial-powershell.md) fornisce un aggiornamento dell'applicazione tramite PowerShell.

È possibile controllare come l'applicazione viene aggiornata utilizzando i [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Effettuare l'aggiornamento delle applicazioni compatibili per imparare a utilizzare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Informazioni su come usare la funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento a [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere i problemi comuni in aggiornamenti dell'applicazione facendo riferimento alla procedura di [Risoluzione dei problemi di aggiornamento delle applicazioni](service-fabric-application-upgrade-troubleshooting.md).
 
