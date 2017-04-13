## <a name="send-messages-to-event-hubs"></a>Inviare messaggi a un hub di evento

La raccolta di client Java per evento hub è disponibile per poterlo usare nei progetti Maven dal [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)e farvi tramite la seguente dichiarazione dipendenza all'interno di file di progetto Maven:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Per diversi tipi di ambienti di compilazione, è possibile ottenere in modo esplicito più recenti file VASO rilasciati da [Repository centrale Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) o dal [punto di distribuzione release in GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Per un autore di eventi semplici, importare la *com.microsoft.azure.eventhubs* per le classi client hub di eventi e il pacchetto di *com.microsoft.azure.servicebus* per le classi di utilità, ad esempio eccezioni comuni condiviso con il client di messaggistica Bus di servizio Azure. 

Per l'esempio seguente, creare un nuovo progetto Maven per un'applicazione console/shell nell'ambiente di sviluppo di linguaggio preferito. La classe sarà denominata ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Sostituire dello spazio dei nomi e i nomi di evento Hub con i valori utilizzati per creare l'Hub di evento.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Creare un evento singolare trasformando in una stringa nella codifica UTF-8 byte. È quindi creare una nuova istanza di client hub evento dalla stringa di connessione e inviare il messaggio.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 
