<properties
    pageTitle="Evento Azure hub archivio | Microsoft Azure"
    description="Panoramica delle funzionalità Azure evento hub archivio."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="azure-event-hubs-archive"></a>Evento Azure hub archivio

Azure evento hub archivio consente di inviare automaticamente i dati di flusso nell'hub di eventi a un account di archiviazione Blob di propria scelta con maggiore flessibilità per specificare l'ora o ridimensionare l'intervallo di propria scelta. Impostazione di archiviazione è veloce, non esistono alcun costo amministrativo per eseguirlo e viene modificato in scala automaticamente con l' evento hub [unità effettiva](event-hubs-overview.md#capacity-and-security). Evento hub archivio è il modo più semplice per caricare dati flussi in Azure e consente di focalizzare l'attenzione su elaborazione dei dati piuttosto che acquisizione dei dati.

Archivio di hub evento Azure consente di elaborare in tempo reale e in base al batch pipeline sullo stesso flusso. In questo modo è possibile creare soluzioni che possono adattarsi alle tue esigenze nel tempo. Se si sta creando sistemi basati su batch oggi con un occhio verso futuri elaborazione in tempo reale o si desidera aggiungere un percorso freddo efficiente a una soluzione in tempo reale esistente, evento hub archivio consente di lavorare con i dati più semplice di flusso.

## <a name="how-event-hubs-archive-works"></a>Funzionamento dell'evento hub archivio

Hub di evento è un buffer permanente ora criteri di conservazione per ingresso telemetria, simile a un file di log distribuito. Il tasto per ridurre in evento hub è il [modello consumer partizionata](event-hubs-overview.md#partition-key). Ogni partizione è un segmento indipendente di dati e viene utilizzato in modo indipendente. Nel tempo questi dati età off, in base al periodo di conservazione configurabili. Di conseguenza, un determinato Hub evento mai "sia pieno."

Evento hub archivio consente di specificare il proprio account di archiviazione Blob Azure e il contenitore in cui verrà utilizzato per archiviare i dati archiviati. Questo account può essere nella stessa regione l'Hub di evento o in un'altra area, aggiungere flessibilità della caratteristica di archivio di hub evento.

Dati archiviati nel formato [Apache Avro][] ; formato compatto, veloce e binario che fornisce le strutture di dati complessi con schema all'interno del testo. In questo formato viene utilizzato ampiamente ecosistema Hadoop, oltre che da flusso Analitica e Azure Data Factory. Ulteriori informazioni sull'uso di Avro sono disponibili più avanti in questo articolo.

### <a name="archive-windowing"></a>Finestre di archivio

Evento hub archivio consente di impostare una finestra per controllare l'archiviazione. Questa finestra è una dimensione minima e configurazione di ora con un "prima wins criterio," indicare che il primo trigger rilevato causerà un'operazione di archiviazione. Se si dispone di una finestra di archiviazione di 15-minuto/100 MB e inviare 1 MB/s, la finestra dimensioni attiverà prima l'intervallo di tempo. Ogni partizione archivi in modo indipendente e scrive un blob blocco completate al momento dell'archiviazione, denominata per volta quando si è verificato l'intervallo di archivio. La convenzione di denominazione è il seguente:

```
<Namespace>/<EventHub>/<Partition>/<YYYY>/<MM>/<DD>/<HH>/<mm>/<ss>
```

### <a name="scaling-to-throughput-units"></a>Ridimensionamento delle unità di velocità

Il traffico hub evento dipende dalle [unità effettiva](event-hubs-overview.md#capacity-and-security). Un'unità singola velocità consente 1 MB/s o 1000 eventi di ingresso e due volte la quantità di uscita al secondo. Hub evento standard possono essere configurati con 1-20 unità velocità e altre può essere acquistate tramite un aumento di quota [richiedere supporto][]. L'uso oltre unità acquistate velocità è limitato. Evento hub archivio copia dati direttamente dall'archivio evento hub interno, ignorando le quote di uscita unità effettiva e salvare l'uscita per altri utenti di elaborazione, ad esempio Analitica flusso o motori.

Una volta configurata, evento hub archivio viene eseguito automaticamente, non appena si invia il primo evento. Continua in esecuzione in qualsiasi momento. Per rendere più semplice per l'elaborazione downstream per indicare che è il processo, quando nessun dato evento hub scrive file vuoti. In questo modo un digitazione prevedibili e un indicatore che possa feed processori il batch.

## <a name="setting-up-event-hubs-archive"></a>La configurazione di evento hub archivio

Evento hub archivio possono essere configurato in fase di creazione evento Hub tramite il portale e gestione di risorse di Azure. È sufficiente abilitare archivio facendo clic **sul** pulsante. Per configurare un Account di archiviazione e di un contenitore, fare clic su sezione **contenitore** della stessa e. Poiché evento hub archivio utilizza l'autenticazione al servizio di archiviazione, non è necessario specificare una stringa di connessione di spazio di archiviazione. Lo strumento di selezione risorsa seleziona automaticamente la risorsa URI per l'account di archiviazione. Se si utilizza Gestione risorse di Azure, è necessario fornire in modo esplicito questo URI come stringa.

L'intervallo di tempo predefinita è di cinque minuti. Il valore minimo è 1, al massimo 15. Nella finestra **dimensioni** sono un intervallo di 500 10 MB.

![][1]

## <a name="adding-archive-to-an-existing-event-hub"></a>Aggiunta di archivio a un Hub evento esistente

Archivi possono essere configurati su hub evento esistente in uno spazio dei nomi hub evento. La caratteristica non è disponibile nei nomi di tipo messaggistica o misto meno recenti. Per abilitare l'archiviazione con un Hub evento esistente o per modificare le impostazioni di archiviazione, fare clic su uno spazio dei nomi per caricare e il **Nozioni di base** , quindi fare clic sull'evento Hub per il quale si desidera attivare o modificare le impostazioni di archiviazione. Infine, fare clic su nella sezione **proprietà** della stessa e aprire come illustrato nella figura seguente.

![][2]

È inoltre possibile configurare evento hub archivio tramite Gestione risorse Azure modelli. Per ulteriori informazioni vedere [l'articolo](event-hubs-resource-manager-namespace-event-hub-enable-archive.md).

## <a name="exploring-the-archive-and-working-with-avro"></a>Esplorare l'archiviazione e l'utilizzo di Avro

Una volta configurata, evento hub archivio vengono creati file nell'account di archiviazione di Azure e container fornito nella finestra ora configurate. È possibile visualizzare i file in uno strumento, ad esempio [Esplora archivi Azure][]. È possibile scaricare i file in locale a lavorare su di essi.

File creati da archivio hub eventi hanno schema Avro riportato di seguito:

![][3]

Un modo semplice per esplorare i file Avro consiste nell'utilizzare vaso [Strumenti Avro][] da Apache. Dopo avere scaricato questo vaso, è possibile visualizzare lo schema di un file specifico Avro eseguendo il comando seguente:

```
java -jar avro-tools-1.8.1.jar getschema \<name of archive file\>
```

Questo comando restituisce

```
{

    "type":"record",
    "name":"EventData",
    "namespace":"Microsoft.ServiceBus.Messaging",
    "fields":[
                 {"name":"SequenceNumber","type":"long"},
                 {"name":"Offset","type":"string"},
                 {"name":"EnqueuedTimeUtc","type":"string"},
                 {"name":"SystemProperties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Properties","type":{"type":"map","values":["long","double","string","bytes"]}},
                 {"name":"Body","type":["null","bytes"]}
             ]
}
```

È anche possibile utilizzare strumenti Avro per convertire il file in formato JSON ed eseguire altre attività di elaborazione.

Per eseguire l'elaborazione più avanzate, scaricare e installare Avro per la scelta della piattaforma. Al momento della pubblicazione di questo articolo sono disponibili implementazioni per C, C++, C\#, Java, NodeJS, Perl, PHP, Python e trascrizione.

Apache Avro ha completo Getting Started Guide per [Java][] e [Python][]. È anche possibile leggere l'articolo [Introduzione a evento hub archivio](event-hubs-archive-python.md) .

## <a name="how-event-hubs-archive-is-charged"></a>Come viene addebitata evento hub archivio

Evento hub archivio è a consumo in modo analogo a unità effettiva, come tariffa oraria. Le spese sono direttamente proporzionale al numero di unità di velocità acquistate per lo spazio dei nomi. Come unità di velocità vengono aumentate e ridotte, evento hub archivio aumenta e li diminuisce per ottenere prestazioni corrispondente. I controlli verificano in parallelo. Le spese per l'archiviazione hub evento sono 0,10 dollari orarie unitario velocità offerti scontato 50% durante il periodo di anteprima.

Evento hub archivio è davvero in modo più semplice per recuperare i dati in Azure. Usa Lake di dati di Azure, Factory di dati di Azure e Azure HDInsight, è possibile eseguire elaborazione batch e altri analitica scelta utilizzando strumenti noti e piattaforme la scala è necessario.

## <a name="next-steps"></a>Passaggi successivi

È possibile trovare ulteriori informazioni hub di evento, visitare i collegamenti seguenti:

- Un' [applicazione di esempio che utilizza evento hub][]completa.
- Esempio [scalabilità evento elaborazione con gli hub di evento][] .
- [Cenni preliminari sui hub di eventi][]

[Apache Avro]: http://avro.apache.org/
[richiesta di assistenza]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[1]: ./media/event-hubs-archive-overview/event-hubs-archive1.png
[2]: media/event-hubs-archive-overview/event-hubs-archive2.png
[Esplora archivi Azure]: http://azurestorageexplorer.codeplex.com/
[3]: ./media/event-hubs-archive-overview/event-hubs-archive3.png
[Strumenti Avro]: http://www-us.apache.org/dist/avro/avro-1.8.1/java/avro-tools-1.8.1.jar
[Java]: http://avro.apache.org/docs/current/gettingstartedjava.html
[Python]: http://avro.apache.org/docs/current/gettingstartedpython.html
[Cenni preliminari sui hub di eventi]: event-hubs-overview.md
[applicazione di esempio che utilizza hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Scalabilità evento elaborazione con gli hub di evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3