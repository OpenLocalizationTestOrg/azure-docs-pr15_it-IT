<properties
   pageTitle="Pianificazione delle capacità per le applicazioni di servizio tessuti | Microsoft Azure"
   description="Viene descritto come identificare il numero dei nodi di calcolo necessari per un'applicazione di servizio tessuti"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Pianificazione delle capacità per le applicazioni di servizio tessuti


In questo documento spiega in che modo stimare la quantità di risorse (CPU, RAM, lo spazio di archiviazione su disco) è necessario eseguire le applicazioni di Azure servizio tessuti. Vengono in genere per le proprie esigenze di risorse cambiare nel tempo. Alcune risorse in genere richiedono sviluppare/testare il servizio e quindi richiedono ulteriori risorse come passare nell'ambiente di produzione e l'applicazione cresce popolarità. Quando si progetta l'applicazione, pensare tramite i requisiti a lungo termine e selezionare opzioni che consentono il servizio di Adatta a soddisfare le esigenze elevato.

 Quando si crea un cluster di tessuti servizio, è possibile decidere quali tipi di macchine () costituiscono il cluster. Ogni macchina virtuale viene fornito con una quantità limitata di risorse in forma di CPU (Core e la velocità), la larghezza di banda di rete, RAM e spazio su disco. Quando il servizio aumenta nel tempo, è possibile aggiornare in macchine virtuali che offrono una maggiore quantità di risorse e/o aggiungere ulteriori macchine virtuali al cluster. Per la seconda opzione, è necessario progettare il servizio inizialmente in modo che possono usufruire delle nuove macchine virtuali che venga aggiunta in modo dinamico al cluster.

Alcuni servizi non gestiscono piccola nessun dati in macchine virtuali stessi. Di conseguenza, pianificazione delle capacità per questi servizi devono focalizzare l'attenzione principalmente sulle prestazioni, ovvero la selezione di CPU appropriate (Core e la velocità) delle macchine virtuali. Inoltre, è necessario considerare larghezza di banda di rete, tra cui la frequenza si verificano trasferimenti di rete e la quantità di dati trasferiti. Se il servizio deve eseguire nonché aumento dell'utilizzo del servizio, è possibile aggiungere ulteriori macchine virtuali al bilanciamento del carico e cluster le richieste di rete tra tutte le macchine virtuali.

Per i servizi che consentono di gestire grandi quantità di dati in macchine virtuali, la pianificazione della capacità deve concentrarsi principalmente su dimensioni. In questo modo, considerare con attenzione la capacità di RAM della macchina virtuale e spazio su disco. Il sistema di gestione della memoria virtuale di Windows rende spazio su disco aspetto RAM al codice dell'applicazione. Inoltre, il runtime tessuti servizio fornisce smart suddivisione in pagine tenere dati attivo solo in memoria e spostare i dati freddo su disco. Le applicazioni possono pertanto utilizzare più memoria fisica disponibile nella macchina virtuale. La presenza di RAM semplicemente aumenta prestazioni ottimali, poiché la macchina virtuale tenere ulteriore spazio di archiviazione disco RAM. Macchine Virtuali si seleziona dovrebbe essere disponibile un disco sufficiente per archiviare i dati che desidera nella macchina virtuale. Analogamente, la macchina virtuale deve avere RAM sufficiente per fornire le prestazioni desiderato. Se i dati del servizio che si sviluppa nel tempo, è possibile aggiungere ulteriori macchine virtuali al cluster e partizione i dati in tutte le macchine virtuali.

## <a name="determine-how-many-nodes-you-need"></a>Determinare quante nodi è necessario

Partizioni del servizio consente di ridimensionare i dati del servizio. Per ulteriori informazioni sulla suddivisione, vedere [Partizioni servizio tessuti](service-fabric-concepts-partitioning.md). Ogni partizione deve essere una macchina virtuale singola, ma più partizioni (piccole) possono essere inserite in una singola macchina virtuale. Pertanto, con più partizioni small offre una maggiore flessibilità rispetto alla alcuni partizioni di dimensioni maggiori. Il compromesso è che la presenza di numerose partizioni aumenta il sovraccarico di tessuti di servizio e non è possibile eseguire le operazioni di transazione in partizioni. È inoltre disponibile più il traffico di rete potenziali se il codice servizio deve spesso accedere a elementi di dati che vivono in partizioni diverse. Durante la progettazione del servizio, considerare con attenzione questi vantaggi e svantaggi per raggiungere una strategia efficace di partizione.

Si supponga che l'applicazione dispone di un singolo servizio informazioni sullo stato che contiene una dimensione di archiviazione che si prevede di raggiungere DB_Size GB in un anno. Che si desidera aggiungere altre applicazioni (e partizioni) mentre si verifica crescita oltre a tale anno.  Il replica fattore (), che determina il numero di repliche per il servizio influisce sulle DB_Size totale. Totale DB_Size tra ogni replica è il fattore di replica moltiplicato per DB_Size.  Node_Size rappresenta il disco spazio/RAM per nodo che si desidera utilizzare per il servizio. Per ottenere prestazioni ottimali, le DB_Size devono essere adatti memoria all'interno del cluster e deve essere scelto un Node_Size intorno RAM della macchina virtuale. Assegnando una Node_Size è maggiore della capacità di RAM è stata attivata per lo spostamento fornito dal runtime del servizio tessuti. In questo modo, le prestazioni potrebbero non essere ottimale se i dati interi viene considerati attivo (allora i dati sono tentativo in/out). Tuttavia, per molti servizi nel punto in cui è attiva solo una parte dei dati, è più economico.

Il numero dei nodi necessari per ottenere prestazioni ottimali può essere calcolato nel modo seguente:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Account per la crescita

È consigliabile calcolare il numero dei nodi in base a DB_Size che si sta aspettando il servizio per raggiungere, oltre a DB_Size che inizia con. Quindi aumentare il numero dei nodi man mano che aumenta il servizio in modo che siano non provisioning eccessivo il numero dei nodi. Ma il numero delle partizioni deve essere in base al numero dei nodi necessari quando si esegue il servizio al massimo.

È consigliabile avere alcuni computer aggiuntivi disponibili in qualsiasi momento, in modo che è possibile gestire eventuali picchi imprevisti o errori (ad esempio, se alcuni macchine virtuali andare verso il basso).  Durante la capacità deve determinata utilizzando i picchi previsti, un punto di partenza consiste nel prenotare alcuni macchine virtuali aggiuntive (percentuale di 5 a 10 aggiuntivi).

Precedente presuppone un singolo servizio informazioni sullo stato. Se si dispone di più di un servizio informazioni sullo stato, è necessario aggiungere DB_Size associato gli altri servizi nell'equazione. In alternativa, è possibile calcolare il numero dei nodi separatamente per ogni servizio informazioni sullo stato.  Il servizio potrebbe avere repliche o partizioni non bilanciate. Tenere presente che le partizioni potrebbero essere anche altri dati rispetto ad altri. Per ulteriori informazioni sulla suddivisione, vedere [partizioni articolo sulle procedure consigliate](service-fabric-concepts-partitioning.md). Tuttavia, l'equazione precedente è partizione e replica indipendente servizio tessuti garantire che le repliche sono distribuite tra i nodi in modalità ottimizzata.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilizzare un foglio di calcolo per il calcolo dei costi

A questo punto si spostare alcuni numeri reali nella formula. Un [foglio di calcolo di esempio](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) viene illustrato come pianificare la capacità per un'applicazione che contiene tre tipi di oggetti dati. Per ogni oggetto è simile alle dimensioni e quanti oggetti che si prevede di avere. È anche possibile selezionare quante repliche vogliamo di ogni tipo di oggetto. Il foglio di calcolo calcola l'importo totale di memoria verrà archiviato nel cluster.

Quindi abbiamo immettere una dimensione memoria virtuale e costo mensile. In base alle dimensioni macchine Virtuali, il foglio di calcolo indica il numero minimo di partizioni che è necessario utilizzare per dividere i dati in modo da adattarlo fisica nei nodi. Si potrebbe volere un numero più grande delle partizioni da adattato calcolo specifico dell'applicazione, è necessario il traffico di rete. Il foglio di calcolo Mostra il numero delle partizioni che gestiscono gli oggetti del profilo utente è aumentato da uno a sei.

A questo punto, il foglio di calcolo in base a tutte le informazioni, mostra che è possibile ottenere fisica tutti i dati con le partizioni desiderate e repliche in un cluster 26 nodi. Tuttavia, il cluster da essere compresse al massimo e, in modo che è possibile che alcuni altri nodi per supportare gli aggiornamenti e gli errori di nodo. Il foglio di calcolo anche illustrati con più di 57 nodi non forniti alcun valore aggiuntivo perché è necessario nodi vuoti. Nuovo, può essere necessario passare sopra 57 nodi comunque per supportare gli aggiornamenti e gli errori di nodo. È possibile modificare il foglio di calcolo in base alle esigenze specifiche dell'applicazione.   

![Foglio di calcolo per il calcolo dei costi][Image1]



## <a name="next-steps"></a>Passaggi successivi

Estrarre [partizione tessuti servizio servizi] [ 10] per ulteriori informazioni sulle partizioni del servizio.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
