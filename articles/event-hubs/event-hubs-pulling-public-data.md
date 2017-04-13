<properties
    pageTitle="Eseguire il pull dei dati pubblici in Azure evento hub | Microsoft Azure"
    description="Panoramica dei hub evento importare da esempio web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/25/2016"
    ms.author="spyros;sethm" />

# <a name="pulling-public-data-into-azure-event-hubs"></a>Eseguire il pull dei dati pubblici in Azure evento hub

Negli scenari tipici Internet di elementi (IoT), si dispone di dispositivi che è possibile programmare un push dei dati di Azure, a un Hub di evento Azure o un hub IoT. Entrambe le tali hub sono punti di ingresso in Azure per l'archiviazione, l'analisi e la visualizzazione con numerose di strumenti disponibili in Microsoft Azure. Tuttavia, entrambi richiedono push dati ad essi formattato come JSON e protetti in modi specifici. Verrà visualizzato il seguente domanda. Cosa si fare se si desidera importare dati da origini pubbliche o private nel punto in cui i dati esposto come un servizio web o un feed di qualche, ma non è la possibilità di modificare la modalità di pubblicazione i dati? È consigliabile meteo oppure il traffico o quotazioni - non è possibile stabilire NOAA, o WSDOT o NASDAQ per configurare un push per l'Hub di evento. Per risolvere il problema, abbiamo scritto e Apri-origine di un campione di Nuvola piccola che è possibile modificare e distribuire in grado di estrarre i dati da un'origine tali e push per l'Hub di evento. A questo punto, è possibile eseguire le operazioni desiderate con, oggetto, naturalmente, le condizioni di licenza dal produttore. È possibile trovare l'applicazione [di seguito](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Si noti che il codice in questo esempio mostra solo come estrarre dati da web tipiche feed e come scrivere a un Hub di evento Azure. NON è destinato a un'applicazione di produzione e non tentativi sono stati introdotti per rendere più adatto per l'utilizzo in un ambiente di questo: è strictfly un DIY solo esempio incentrati sui sviluppo. Inoltre, l'esistenza di questo esempio non è equivale consigliato è necessario **estrarre** i dati in Azure anziché **push** . Rivedere sicurezza, funzionalità e le prestazioni e costo fattori prima di liquidazione su un'architettura-to-end.

## <a name="application-structure"></a>Struttura dell'applicazione

L'applicazione è scritta in c# e la [descrizione di esempio](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contiene tutte le informazioni che necessarie per modificare, creare e pubblicare l'applicazione. Nelle sezioni seguenti offrono una panoramica delle funzionalità dell'applicazione.

Iniziamo con sul presupposto che si ha accesso a un feed di dati. È ad esempio in modo da estrarre i dati del traffico reparto stato di Washington di trasporto o i dati meteo da NOAA, per visualizzare i report personalizzati o per combinare i dati con altri dati dell'applicazione. È necessario anche impostato un Hub di evento Azure e conoscere la connessione stringa necessaria per accedervi.

Quando si avvia la soluzione GenericWebToEH, legge un file di configurazione (App) per ottenere diversi aspetti:

1. L'URL o un elenco di URL per il sito di pubblicazione dei dati. Se possibile, si tratta di un sito che pubblica i dati JSON, ad esempio quelli a cui fa riferimento WSDOT [qui](http://www.wsdot.wa.gov/Traffic/api/). 
2. Credenziali per l'URL, se necessario. Molte origini pubbliche non è necessario credenziali oppure è possibile inserire le credenziali nella stringa dell'URL. Altri richiedono fornire separatamente. Si noti che è possibile specificare solo un insieme di credenziali in questa applicazione, in modo che funziona solo se si specifica un URL, non un elenco di URL.
3. Stringa di connessione e il nome dell'evento Hub nello spazio dei nomi di evento hub, al quale si sposterà i dati. Queste informazioni sono disponibili nel portale di Azure.
4. Un intervallo di inattività, in millisecondi per l'intervallo tra il sito di dati pubblici di polling. Questa impostazione implica alcune pensiero. Se sondaggio troppo frequentemente, potrebbero perdere dati. mano, se sondaggio troppo frequentemente, è possibile ottenere una grande quantità di dati ripetitivi o peggiore ancora, potrebbe essere bloccato come un bot nefandi. Considerare la frequenza di aggiornamento dell'origine dati - meteo o il traffico dati possono essere aggiornati ogni 15 minuti, ma quotazioni cercando ogni pochi secondi, a seconda del fatto nel punto in cui viene visualizzato. 
5. Un contrassegno per indicare l'applicazione se i dati sarà disponibile a breve come JSON o XML. Dal momento che è necessario inserire i dati a un Hub di evento, l'applicazione ha un modulo per convertire XML in JSON prima dell'invio.

Dopo aver letto il file di configurazione, l'applicazione rileva un ciclo - accesso al sito web pubblico, la conversione dei dati se necessario, la scrittura per il tuo fulcro evento e quindi in attesa per l'intervallo di sospensione prima di eseguire l'operazione di nuovo. In particolare:

  * Il sito Web pubblico di lettura. Per ricevere dati già pronti a inviare l'istanza della classe RawXMLWithHeaderToJsonReader viene utilizzato da Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Legge il flusso di origine nel metodo GetData () e quindi la suddivide in parti più piccole (ovvero records) utilizzando GetXmlFromOriginalText. 
  Questo metodo leggerà XML oltre al tipo corretto JSON o JSON matrice. L'elaborazione viene avviato utilizzando la configurazione di MergeToXML da App (impostazione predefinita = vuoto).
  * I dati di invio e ricezione sono implementati come un ciclo nel metodo Process () \ Program.cs. 
  Dopo aver ricevuto i risultati di output da GetData (), accoda metodo separati i valori per l'Hub di evento.

## <a name="next-steps"></a>Passaggi successivi

Per distribuire la soluzione, duplicare o scaricare l'applicazione [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) , modificare il file App, compilarlo e infine pubblicarlo. Dopo aver pubblicato l'applicazione, è possibile visualizzarlo in esecuzione nel portale di classica Azure in servizi Cloud ed è possibile modificare alcune delle impostazioni di configurazione (ad esempio la destinazione Hub di eventi e l'intervallo di sospensione) nella scheda **Configura** .

Vedere altri esempi di hub di evento nella [raccolta di esempi Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) e su [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).
