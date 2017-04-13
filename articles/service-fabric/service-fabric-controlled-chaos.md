<properties
   pageTitle="Forzare Chaos tessuti servizio cluster | Microsoft Azure"
   description="Uso Cluster analisi servizio API e inserimento guasto per gestire Chaos del cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="rsinha"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="motanv"/>

# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Forzare controllati Chaos tessuti servizio cluster
Sistemi distribuiti su larga scala come infrastrutture basate su cloud sono per sé origini non affidabili. Azure tessuti servizio consente agli sviluppatori scrivere servizi affidabili nella parte superiore di un'infrastruttura origini non affidabile. Per gli sviluppatori per scrivere servizi efficaci, devono essere in grado di provocare errori rispetto a tali origini non affidabile infrastruttura per verificare la stabilità dei servizi.

L'errore di inserimento e Cluster Analysis Services (noto anche come il servizio di analisi guasto) consente agli sviluppatori di indurre azioni guasto per testare i servizi. Tuttavia, target errori simulati iniziare solo finora. Per sfruttare ulteriormente il test, è possibile utilizzare Chaos.

CHAOS consente di simulare errori continui, interfoliazione (normale e anomalo del) in tutto il cluster "Extended" periodi di tempo. Dopo aver configurato Chaos con la frequenza e il tipo di errori, è possibile avviare o arrestare tramite c# API o PowerShell per generare errori in cluster e il servizio.

Durante l'esecuzione Chaos, produce diversi eventi che acquisiscono lo stato dell'esecuzione al momento. Ad esempio, un ExecutingFaultsEvent contiene tutti gli errori che vengono eseguiti in quell'iterazione. Un ValidationFailedEvent contiene i dettagli di un errore durante la convalida cluster è stato trovato. È possibile richiamare l'API GetChaosReportAsync per ottenere il report verrà eseguito Chaos.

## <a name="faults-induced-in-chaos"></a>Errori dovuti Chaos
CHAOS genera errori attraverso l'intero cluster tessuti servizio e comprime errori che vengono visualizzati in mesi o anni in poche ore. La combinazione di errori interfogliati con la frequenza degli errori elevato trova casi angolo che in caso contrario vengono perse. Questo esercizio Chaos conduce ad un notevole miglioramento della qualità di codice del servizio.

CHAOS provoca errori categorie seguenti:

 - Riavviare un nodo
 - Riavviare un pacchetto di codice distribuito
 - Rimuovere una replica
 - Riavviare una replica
 - Spostare una replica principale (configurabile)
 - Spostare una replica secondaria (configurabile)

CHAOS viene eseguito in più iterazioni. Ogni iterazione è costituito da errori e convalida dei cluster per il periodo specificato. È possibile configurare il tempo impiegato per il cluster stabilizzare e per la convalida venga eseguita correttamente. Se viene rilevato un errore di convalida dei cluster, Chaos genera e persiste ValidationFailedEvent con il timestamp UTC e i dettagli relativi agli errori.

Si consideri ad esempio un'istanza di Chaos è impostato per l'esecuzione di un'ora con un massimo di tre errori simultanei. CHAOS provoca tre errori e quindi convalida integrità cluster. Si scorre precedente passaggio finché non si trova in modo esplicito arrestato tramite l'API StopChaosAsync o un'ora passa. Se il cluster diventerà non corretti in qualsiasi iterazione (vale a dire non stabilizzazione all'interno di una volta configurato), Chaos genera un ValidationFailedEvent. Questo evento indica che un elemento funzionato correttamente e potrebbe essere necessario approfondire.

Nella sua forma attuale Chaos provoca solo gli errori attendibili. Questo significa che, in assenza di errori esterni, una perdita di base o una perdita di dati non si verifica.

## <a name="important-configuration-options"></a>Opzioni di configurazione importanti
 - **TimeToRun**: tempo totale per tale esecuzioni Chaos prima che venga completato con successo. È possibile interrompere Chaos prima che ha eseguito per il periodo di TimeToRun tramite l'API StopChaos.
 - **MaxClusterStabilizationTimeout**: la maggior quantità di tempo di attesa per il cluster diventi integro prima di archiviare nuovamente su di esso. Questo attesa consiste nel ridurre il carico sul cluster durante recupero. I controlli eseguiti sono:
    - Se lo stato di cluster è consentito
    - Se l'integrità dei servizi è consentito
    - Se la replica di destinazione Imposta dimensione viene eseguita per partizione di servizio
    - Che non esistono repliche InBuild
 - **MaxConcurrentFaults**: il numero massimo di errori simultanei che eseguito in ogni iterazione. È il superiore movimento confuso causato il numero, più efficace. Il risultato failover più complessi e le combinazioni di transizione. CHAOS garantisce che, in assenza di errori esterni, nessuna perdita di base o la perdita di dati, indipendentemente da un valore come alto ha questa configurazione.
 - **EnableMoveReplicaFaults**: attiva o disattiva gli errori che causano la replica primario o secondario da spostare. Questi errori sono disabilitati per impostazione predefinita.
 - **WaitTimeBetweenIterations**: la quantità di tempo di attesa tra iterazioni, vale a dire dopo un arrotondamento di errori e convalida corrispondente.
 - **WaitTimeBetweenFaults**: la quantità di tempo di attesa tra due errori consecutivi in un'iterazione.

## <a name="how-to-run-chaos"></a>Come eseguire Chaos
**C#:**

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }

        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;
            var stabilizationTimeout = TimeSpan.FromSeconds(30.0);
            var timeToRun = TimeSpan.FromMinutes(60.0);
            var maxConcurrentFaults = 3;

            var parameters = new ChaosParameters(
                stabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun);

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            while (true)
            {
                var report = client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult();

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```
**PowerShell:**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRun -MaxConcurrentFaults $concurrentFaults -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    $stopped = $true
                }

                Write-Host $e
            }
        }
    }

    if($stopped -eq $true)
    {
        break
    }

    Start-Sleep -Seconds 1
}

Stop-ServiceFabricChaos
```
