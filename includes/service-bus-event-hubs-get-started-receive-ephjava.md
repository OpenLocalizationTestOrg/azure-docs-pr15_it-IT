## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ricevere messaggi con EventProcessorHost Java

EventProcessorHost è una classe Java che semplifica ricevere gli eventi da hub di evento per la gestione dei punti di controllo permanenti e parallelo ricevuto tali hub evento. Con EventProcessorHost è possibile dividere eventi tra più destinatari, anche quando ospitato in nodi diversi. In questo esempio viene illustrato come utilizzare EventProcessorHost per un singolo destinatario.

###<a name="create-a-storage-account"></a>Creare un account di archiviazione

Per utilizzare EventProcessorHost, è necessario disporre di un [account di archiviazione di Azure][]:

1. Accedere al [portale classica Azure][]e fare clic su **Nuovo** nella parte inferiore dello schermo.

2. Fare clic su **Servizi di dati**, **lo spazio di archiviazione**e quindi **Creare rapido**e quindi digitare un nome per il proprio account di archiviazione. Selezionare il paese desiderato e quindi fare clic su **Crea Account lo spazio di archiviazione**.

    ![][11]

3. Fare clic sull'account di archiviazione appena creato e quindi fare clic su **Gestisci i tasti di scelta**:

    ![][12]

    Copiare la chiave primaria accesso da utilizzare più avanti in questa esercitazione.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Creare un progetto di linguaggio utilizzando l'EventProcessor Host

La raccolta di client Java per hub evento non è disponibile per poterlo usare nei progetti Maven dal [Repository centrale Maven][Maven Package], i riferimenti tramite la seguente dichiarazione dipendenza all'interno di file di progetto Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Per diversi tipi di ambienti di compilazione, è possibile ottenere in modo esplicito più recenti file VASO rilasciati da [Repository centrale Maven] [ Maven Package] o dal [punto di distribuzione release in GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Per l'esempio seguente, creare un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo di linguaggio preferito. La classe sarà denominata ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Usare il codice riportato di seguito per creare una nuova classe denominata ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Creare una classe finale denominata ```EventProcessorSample```, utilizzando il codice seguente.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Sostituire i campi seguenti con i valori utilizzati durante la creazione di account di archiviazione Hub evento.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] In questa esercitazione viene utilizzata una singola istanza EventProcessorHost. Per aumentare la velocità, è consigliabile eseguire più istanze di EventProcessorHost. In questi casi, le varie istanze automaticamente coordinano tra loro per il bilanciamento del carico gli eventi ricevuti. Se si vuole più ricevitori di ogni processo *tutti* gli eventi, è necessario utilizzare il concetto di **ConsumerGroup** . Quando si riceve eventi da computer diversi, potrebbe essere utile per specificare i nomi degli EventProcessorHost istanze basate su computer (o ruoli) in cui vengono distribuite.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Account di archiviazione Azure]: ../articles/storage/storage-create-storage-account.md
[Portale classica Azure]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

