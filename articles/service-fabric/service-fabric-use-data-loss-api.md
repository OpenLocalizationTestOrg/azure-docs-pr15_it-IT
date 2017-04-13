<properties
   pageTitle="Come richiamare perdita di dati nel servizio tessuti servizi | Microsoft Azure"
   description="Viene descritto come utilizzare la perdita di dati api"
   services="service-fabric"
   documentationCenter=".net"
   authors="LMWF"
   manager="rsinha"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/19/2016"
   ms.author="lemai"/>
   
# <a name="how-to-invoke-data-loss-on-services"></a>Come richiamare la perdita di dati sui servizi

>[AZURE.WARNING] In questo documento viene descritto come determinare la perdita di dati nel provider di servizi e deve essere utilizzata con cautela.

## <a name="introduction"></a>Introduzione
È possibile richiamare la perdita di dati in una partizione del servizio tessuti servizio da chiamare StartPartitionDataLossAsync().  Questa api utilizza il servizio di analisi e l'inserimento di guasto per svolgere un'attività per fare in modo condizioni perdita dei dati.

## <a name="using-the-fault-injection-and-analysis-service"></a>Tramite il servizio di analisi e l'inserimento di guasto

Il servizio di analisi e l'inserimento di guasto supporta attualmente l'API seguenti nella tabella seguente.  Nella parte destra del grafico sono i cmdlet di PowerShell corrispondente.  Fare riferimento alla documentazione di msdn su ogni API per ulteriori informazioni su ognuna di esse.

|           API C#                    |         Cmdlet di PowerShell                      |
|-------------------------------------|-----------------------------------------------:|
|[StartPartitionDataLossAsync] [dl]   |[Inizio ServiceFabricPartitionDataLoss] [psdl]   |
|[StartPartitionQuorumLossAsync] [ql] |[Inizio ServiceFabricPartitionQuorumLoss] [psql] |
|[StartPartitionRestartAsync] [rp]    |[Inizio ServiceFabricPartitionRestart] [psrp]    |

## <a name="conceptual-overview-of-running-a-command"></a>Panoramica sui concettuale di esecuzione di un comando

Il servizio di analisi e l'inserimento di guasto utilizza un modello asincrono nel punto in cui il comando inizia con una API, denominato API "Start" in questo documento quindi verifica l'avanzamento del comando utilizzando un "GetProgress" API fino al raggiungimento uno stato finale o fino a quando non si Annulla.
Per avviare un comando, chiamare l'API "Start" per l'API corrispondente.  Questa API restituisce quando il servizio di analisi e l'inserimento di guasto ha accettato la richiesta.  Tuttavia, non indica quanto è eseguire un comando, anche se è ancora iniziato.  Per controllare lo stato di avanzamento di un comando, chiamare API che corrisponde all'API "Start" già noto come "GetProgress".  API "GetProgress" restituirà un oggetto che indica lo stato corrente del comando all'interno di proprietà di stato.  Tempo indefinito finché non viene eseguito un comando:

1.  Essa viene completata correttamente.  Se si chiama "GetProgress" in cui è in questo caso, lo stato di avanzamento stato dell'oggetto verrà completato.
2.  Si verifica un errore irreversibile.  Se si chiama "GetProgress" in cui è in questo caso, lo stato di avanzamento stato dell'oggetto verrà sono possibili errori
3.  Si annulla tramite [CancelTestCommandAsync]  [ cancel] API o [Interrompi ServiceFabricTestCommand]  [ cancelps] cmdlet di PowerShell.  Se si chiama "GetProgress" in cui è in questo caso, lo stato di avanzamento stato dell'oggetto sarà annullato o ForceCancelled, a seconda di un argomento a tale API.  Vedere la documentazione relativa [CancelTestCommandAsync]  [ cancel] per altri dettagli.


## <a name="details-of-running-a-command"></a>Dettagli dell'esecuzione di un comando

Per avviare un comando, chiamare l'API iniziare con gli argomenti previsti.  Tutte le API avviare dispone di un argomento Guid denominato operationId.  È necessario tenere traccia delle argomento operationId, poiché viene utilizzato per tenere traccia dello stato di questo comando.  Questa operazione deve essere passata nel API "GetProgress" per tenere traccia dello stato del comando.  Il operationId deve essere univoco.

Dopo la chiamata correttamente l'API di iniziare, l'API GetProgress deve essere chiamato riprodotta a ciclo continuo fino al termine della proprietà di stato dell'oggetto restituito lo stato di avanzamento.  Tutti [del FabricTransientException]  [ fte] e ripetere dell'evento OperationCanceledException.
Quando il comando ha raggiunto uno stato finale (completato, Faulted o annullato), proprietà di risultato dell'oggetto restituito lo stato di avanzamento avrà informazioni aggiuntive.  Se lo stato completato, Result.SelectedPartition.PartitionId conterrà l'id di partizione che è stata selezionata.  Result.Exception sarà null.  Se lo stato contiene un errore, Result.Exception avrà il motivo per l'immissione di errori e servizio analisi errori il comando.  Result.SelectedPartition.PartitionId avrà l'id di partizione che è stata selezionata.  In alcuni casi, il comando potrebbe non ha luogo abbastanza per scegliere una partizione.  In questo caso, il PartitionId sarà 0.  Se lo stato è stato annullato, Result.Exception sarà null.  Ad esempio il caso Faulted Result.SelectedPartition.PartitionId avrà l'id di partizione che è stato scelto, ma se il comando di non luogo abbastanza a tale scopo, sarà 0.  Anche vedere l'esempio riportato di seguito.

Il codice di esempio seguente viene illustrato come avviare quindi controllare lo stato di avanzamento di un comando di causare la perdita di dati in una partizione specifica.

```csharp
    static async Task PerformDataLossSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/MyService");

        // The id of the target partition inside the target service
        Guid targetPartitionId = new Guid("00000000-0000-0000-0000-000002233445");

        PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(targetServiceName, targetPartitionId);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.        

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                // In a terminal state .Result.SelectedPartition.PartitionId will have the chosen partition
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);
                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}'", operationId, progress.Result.Exception);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(5.0d)).ConfigureAwait(false);
        }
    }
```

Nell'esempio seguente viene illustrato come utilizzare il PartitionSelector per scegliere una partizione casuale di un servizio specificato:

```csharp
    static async Task PerformDataLossUseSelectorSample()
    {
        // Create a unique operation id for the command below
        Guid operationId = Guid.NewGuid();

        // Note: Use the appropriate overload for your configuration
        FabricClient fabricClient = new FabricClient();

        // The name of the target service
        Uri targetServiceName = new Uri("fabric:/SampleService ");

        // Use a PartitionSelector that will have the Fault Injection and Analysis Service choose a random partition of “targetServiceName”
        PartitionSelector partitionSelector = PartitionSelector.RandomOf(targetServiceName);

        // Start the command.  Retry OperationCanceledException and all FabricTransientException's.  Note when StartPartitionDataLossAsync completes
        // successfully it only means the Fault Injection and Analysis Service has saved the intent to perform this work.  It does not say anything about the progress
        // of the command.
        while (true)
        {
            try
            {
                await fabricClient.TestManager.StartPartitionDataLossAsync(operationId, partitionSelector, DataLossMode.FullDataLoss).ConfigureAwait(false);
                break;
            }
            catch (OperationCanceledException)
            {
            }
            catch (FabricTransientException)
            {
            }
            catch (Exception e)
            {
                Console.WriteLine("Unexpected exception '{0}'", e);
                throw;
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }

        PartitionDataLossProgress progress = null;

        // Poll the progress using GetPartitionDataLossProgressAsync until it is either Completed or Faulted.  In this example, we're assuming
        // the command won't be cancelled.

        while (true)
        {
            try
            {
                progress = await fabricClient.TestManager.GetPartitionDataLossProgressAsync(operationId).ConfigureAwait(false);
            }
            catch (OperationCanceledException)
            {
                continue;
            }
            catch (FabricTransientException)
            {
                continue;
            }

            if (progress.State == TestCommandProgressState.Completed)
            {
                Console.WriteLine("Command '{0}' completed successfully", operationId);

                Console.WriteLine("Printing progress.Result:");
                Console.WriteLine("  Printing selected partition='{0}'", progress.Result.SelectedPartition.PartitionId);

                break;
            }
            else if (progress.State == TestCommandProgressState.Faulted)
            {
                // If State is Faulted, the progress object's Result property's Exception property will have the reason why.
                Console.WriteLine("Command '{0}' failed with '{1}', SelectedPartition {2}", operationId, progress.Result.Exception, progress.Result.SelectedPartition);
                break;
            }
            else
            {
                Console.WriteLine("Command '{0}' is currently Running", operationId);
            }

            await Task.Delay(TimeSpan.FromSeconds(1.0d)).ConfigureAwait(false);
        }
    }
```

## <a name="history-and-truncation"></a>Cronologia e troncamento

Dopo che un comando ha raggiunto uno stato finale, i metadati rimarrà nell'errore di inserimento di Analysis Services per una determinata ora, prima verrà rimosso per liberare spazio.  Se si chiama "GetProgress" tramite operationId di un comando dopo averlo rimosso, verrà restituito un FabricException con un codice di errore di KeyNotFound.

[dl]: https://msdn.microsoft.com/library/azure/mt693569.aspx
[ql]: https://msdn.microsoft.com/library/azure/mt693558.aspx
[rp]: https://msdn.microsoft.com/library/azure/mt645056.aspx
[psdl]: https://msdn.microsoft.com/library/mt697573.aspx
[psql]: https://msdn.microsoft.com/library/mt697557.aspx
[psrp]: https://msdn.microsoft.com/library/mt697560.aspx
[cancel]: https://msdn.microsoft.com/library/azure/mt668910.aspx
[cancelps]: https://msdn.microsoft.com/library/mt697566.aspx
[fte]: https://msdn.microsoft.com/library/azure/system.fabric.fabrictransientexception.aspx
