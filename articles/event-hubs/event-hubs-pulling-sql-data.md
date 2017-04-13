<properties
   pageTitle="Estrazione dei dati SQL in Azure evento hub | Microsoft Azure"
   description="Panoramica dei hub evento importare da esempio SQL"
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

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Estrazione di dati da SQL a un Hub di evento Azure

Una tipica architettura per un'applicazione per l'elaborazione dei dati in tempo reale a altro comporta l'inserendolo prima di tutto a un Hub di evento Azure. Potrebbe essere uno scenario di IoT, ad esempio il monitoraggio del traffico in diversi estende di un percorso o uno scenario di gioco, monitoraggio le azioni di horde di frenzied contestants o uno scenario aziendale, ad esempio il monitoraggio all'occupazione finale predefiniti. In questi casi, i produttori di dati sono in genere esterni oggetti produrre dati serie temporale che è necessario raccogliere, analizzare, archiviare e verificare e si può hanno investito una procedura complessa in predefiniti infrastruttura per questi processi. Che cos' fare se si desidera importare dati da un elemento come un database anziché una fonte di dati di flusso e usarlo in combinazione con altri dati flussi? Si consideri il caso in cui si desidera utilizzare Azure flusso Analitica, remoto dati Explorer (RDX) o un altro strumento per l'analisi e il comportamento delle modifica lenta dati da Microsoft Dynamics AX o un sistema di gestione di attrezzature personalizzato? Per risolvere il problema, abbiamo scritto e Apri-origine di un campione di Nuvola piccola che è possibile modificare e distribuire in grado di estrarre i dati da una tabella SQL e push a un Hub di evento Azure da utilizzare come input nelle applicazioni analitiche downstream. Tenere presente che si tratta di uno scenario raro e l'operazione opposta delle quali si farebbe con un Hub di evento. Tuttavia, se è necessario nel caso in cui si tratta è necessario, è possibile trovare il codice nella raccolta esempi di Azure, [di seguito](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Si noti che il codice in questo esempio è sufficiente che, un campione. È **non** può essere un'applicazione di produzione e non tentativi sono stati introdotti per rendere più adatto per l'utilizzo in tale ambiente: è stricly DIY, esempio incentrati su sviluppo. È necessario esaminare tutti i tipi di sicurezza, funzionalità e le prestazioni e costo fattori prima di liquidazione su un'architettura-to-end.

## <a name="application-structure"></a>Struttura dell'applicazione

L'applicazione è scritta in c# e il file readme.md nel campione contiene tutte le informazioni che necessarie per modificare, creare e pubblicare l'applicazione. Nelle sezioni seguenti offrono una panoramica delle funzionalità dell'applicazione.

Iniziamo con sul presupposto che si ha accesso a una tabella di SQL Azure. È necessario anche impostato un Hub di evento Azure e conoscere la connessione stringa necessaria per accedervi.

Quando si avvia la soluzione SqlToEventHub, legge un file di configurazione (App) per ottenere un numero di elementi, come descritto nel file readme.md. Si tratta di un'operazione facile comprensione, ad esempio il nome di tabella dati ECC e quindi è necessario rehash le spiegazioni qui. 

Dopo l'applicazione ha leggere il file di configurazione, viene passa un ciclo, la lettura della tabella SQL e inserimento record a hub evento, quindi in attesa per un intervallo di inattività definite dall'utente prima di eseguire l'operazione di nuovo. Alcuni aspetti sono notare:

1. L'applicazione si basa sul si presuppone che la tabella SQL viene aggiornata mediante un processo esterno e si desidera inviare tutti e solo gli aggiornamenti a un Hub di evento.
2. La tabella SQL deve disporre di un campo contenente un numero univoco e crescente, ad esempio un numero di record. Può essere costituiti semplicemente da un campo denominato "Id" o qualsiasi altro elemento che viene incrementato come qualsiasi aggiorna il database consente di aggiungere record, ad esempio "Creation_time" o "Sequence_number". L'applicazione note e memorizza il valore del campo in ogni iterazione. In ogni passaggio successivo del ciclo, l'applicazione di una query essenzialmente della tabella per tutti i record in cui il valore del campo supera il valore illustrato l'ora dell'ultima del ciclo. Stiamo chiamando il l'ultimo valore "offset".
3. L'applicazione crea una tabella "TableOffsets" all'avvio, per archiviare gli offset. La tabella viene creata con la query "CreateOffsetTableQuery" definita nel file di configurazione. 
4. Sono disponibili diverse query utilizzata per l'utilizzo con la tabella offset definita nel file di configurazione come "OffsetQuery", "UpdateOffsetQuery" e "InsertOffsetQuery". È consigliabile non modificare questi.
5. Infine, la query "DataQuery" definita nel file di configurazione è la query verrà eseguita per recuperare i record dalla tabella SQL. È attualmente limitata ai record prime 1000 in ogni passaggio del ciclo per ottimizzare le prestazioni - se, ad esempio 25.000 record sono stati aggiunti al database successivamente all'ultima query potrebbe richiedere un po' di tempo per eseguire la query. Limitando la query a 1.000 record ogni volta, le query sono molto più veloce. Selezionare le prime 1000 semplice inserisce batch successive di 1.000 record hub evento.    

## <a name="next-steps"></a>Passaggi successivi

Per distribuire la soluzione, duplicare o scaricare l'applicazione SqlToEventHub, modificare il file App, compilarlo e infine pubblicarlo. Dopo aver pubblicato l'applicazione, è possibile visualizzarlo in esecuzione nel portale di classica Azure in servizi Cloud e controllare gli eventi in arrivo per l'hub di evento. Si noti che la frequenza viene determinata da due fattori: la frequenza degli aggiornamenti per la tabella SQL e l'intervallo di inattività specificato nel file di configurazione dell'applicazione.