<properties 
    pageTitle="Servizi multimediali di Azure MP4 frammentati live acquisizione specifica | Microsoft Azure" 
    description="Questa specifica descrive il protocollo e il formato per MP4 frammentato in base a acquisizione flusso live per servizi multimediali di Microsoft Azure. Servizi multimediali di Microsoft Azure offre il servizio di trasmissione live che consente ai clienti di flusso eventi live e trasmissione di contenuto in tempo reale tramite Microsoft Azure come la piattaforma cloud. In questo documento viene inoltre procedure consigliate nella creazione ridondanti altamente affidabile live acquisizione meccanismi." 
    services="media-services" 
    documentationCenter="" 
    authors="cenkdin" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"     
    ms.author="cenkdin;juliako"/>

#<a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Servizi multimediali di Azure MP4 frammentati live acquisizione specifica

Questa specifica descrive il protocollo e il formato per MP4 frammentato in base a acquisizione flusso live per servizi multimediali di Microsoft Azure. Servizi multimediali di Microsoft Azure offre il servizio di trasmissione live che consente ai clienti di flusso eventi live e trasmissione di contenuto in tempo reale tramite Microsoft Azure come la piattaforma cloud. In questo documento viene inoltre procedure consigliate nella creazione ridondanti altamente affidabile live acquisizione meccanismi.


##<a name="1-conformance-notation"></a>1. notazione di conformità

Le parole chiave "deve", "Non deve", "REQUIRED", "SHALL", "Non dovrà", "Opportuno", "Non dovrebbe", "Consigliato", "Può" e "Facoltativo" in questo documento devono essere interpretati come descritto in RFC2119.

##<a name="2-service-diagram"></a>2. diagramma del servizio di 

La figura seguente mostra architettura di alto livello del servizio di trasmissione dinamica in servizi multimediali di Microsoft Azure:

1.  Codificatore Live inserisce feed live in canali che vengono creati e viene completato il provisioning tramite Microsoft Azure Media Services SDK.
2.  Canali, applicazioni e Streaming endpoint servizi multimediali di Microsoft Azure handle tutte le funzionalità trasmissione live acquisizione, formattazione, inclusi il cloud DVR, sicurezza, scalabilità e ridondanza.
3.  Se lo si desidera clienti possono scegliere di distribuire un layer CDN tra l'endpoint Streaming ed endpoint client.
4.  Flusso endpoint client da endpoint Streaming utilizzando i protocolli HTTP adattata Streaming (ad esempio Smooth Streaming, trattino, unità 10K o HLS).

![Image1][image1]


##<a name="3-bit-stream-format--iso-14496-12-fragmented-mp4"></a>3. formato flusso di bit: ISO 14496-12 frammentato MP4

Il formato di comunicazione per streaming live caricamento descritto in questo documento è in base [ISO-14496-12]. Per informazioni su formato MP4 frammentato ed estensioni per entrambi i file punti consultare la [[MS-SSTR]](http://msdn.microsoft.com/library/ff469518.aspx) e multimediali acquisizione attivi.

###<a name="live-ingest-format-definitions"></a>Attiva caricamento le definizioni di formato 

Di seguito è riportato un elenco di formato speciale definizioni applicabili per live caricamento in servizi multimediali di Microsoft Azure:

1. Casella 'moov', 'ftyp' e LiveServerManifestBox devono essere inviati con ogni richiesta (HTTP POST).  Ogni volta che il codificatore necessario riconnettersi per riprendere flusso moltissime immagini e devono essere inviato all'inizio del flusso.  Per ulteriori informazioni, vedere sezione 6 [1].
2. Sezione 3.3.2 [1] definisce una casella facoltativa denominata live StreamManifestBox per il caricamento. A causa di logica di routing di bilanciamento del carico di Microsoft Azure, l'uso di questa casella è deprecata e non dovrebbe essere presente quando il caricamento nel servizio di supporto di Microsoft Azure. Se questa casella è presenta, servizi multimediali di Windows Azure automaticamente ignorate.
3. TrackFragmentExtendedHeaderBox definiti in 3.2.3.2 [1] deve essere presente per ogni frammento.
4. La versione 2 del TrackFragmentExtendedHeaderBox deve essere usata per generare i segmenti con URL identiche in più Data Center. Il campo index frammento è richiesta per formati di Data Center tra failover di basate su indici streaming, ad esempio Apple HTTP Live Streaming (HLS) e basate su indici MPEG-trattino.  Per abilitare la data center tra failover, l'indice frammento deve essere sincronizzato in più Encoder e aumenta da 1 per ogni frammento media successivi anche attraverso il riavvio di codificatore o errori.
5. Sezione 3.3.6 [1] definisce casella denominata MovieFragmentRandomAccessBox ('mfra') che possono essere inviate alla fine di acquisizione live per indicare fine del flusso (fine del flusso) per il canale. A causa di logica di acquisizione di servizi multimediali di Windows Azure, l'uso di sovraccarico (fine del flusso) è deprecata e la casella di 'mfra' acquisizione live non dovrebbe essere inviate. Inviato, servizi multimediali di Windows Azure automaticamente ignorate. Si consiglia di utilizzare [Canale Reimposta](https://msdn.microsoft.com/library/azure/dn783458.aspx#reset_channels) per ripristinare lo stato del punto di caricamento e anche è consigliabile usare [Programma](https://msdn.microsoft.com/library/azure/dn783463.aspx#stop_programs) per terminare una presentazione e flusso.
6. La durata di frammento MP4 dovrebbe essere costante, per ridurre le dimensioni dei manifesti client e migliorare euristica download client mediante l'utilizzo di tag di ripetizione.  La durata può variare per compensa non intero frame rate.
7. La durata di frammento MP4 deve essere compreso tra circa 2 e 6 secondi.
8. Gli indici (TrackFragmentExtendedHeaderBox fragment_ absolute_ tempo e fragment_index) e MP4 frammento timestamp dovrebbero arrivare in ordine crescente.  Servizi multimediali di Windows Azure flessibili a frammenti duplicati, è molto limitato riordinare frammenti in base alla sequenza temporale di elementi multimediali.

##<a name="4-protocol-format--http"></a>4. formato protocol-HTTP

ISO frammentato MP4 in base a live acquisizione usi servizi multimediali di Microsoft Azure standard lunga HTTP POST richiesta per la trasmissione dei dati di elementi multimediali codificati inseriti in formato MP4 frammentato al servizio. Ogni POST HTTP invia una completa frammentato MP4 flusso di bit ("flusso") a partire da a partire da caselle dell'intestazione (casella "ftyp", "Live Server manifesto casella" e 'moov') e continuare con una sequenza di frammenti (caselle "moof" e 'mdat'). Fare riferimento alla sezione 9.2 in [1] per la sintassi di URL per la richiesta HTTP POST. Esempio di URL POST è: 

    http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)

###<a name="requirements"></a>Requisiti

Ecco i requisiti dettagliati:

1. Codificatore deve iniziare la trasmissione tramite l'invio di una richiesta HTTP POST con un "corpo vuoto" (lunghezza zero contenuto) utilizzando lo stesso URL di acquisizione. Questo consente di rilevare rapidamente se l'endpoint di acquisizione live è valido e se è presente alcuna autenticazione o altre condizioni obbligatori. Per protocollo HTTP, server non sarà possibile inviare risposta HTTP indietro fino a quando non viene ricevuta l'intera richiesta inclusi corpo del POST. Natura lunga dell'evento live, senza questo passaggio, il codificatore potrebbe non essere possibile rilevare eventuali errori fino al termine dell'invio di tutti i dati.
2. Codificatore necessario gestire eventuali errori o richieste di autenticazione in seguito (1). Se (1) ha avuto esito positivo con una risposta 200 continuare.
3. Codificatore deve iniziare una nuova richiesta HTTP POST con il flusso MP4 frammentato.  Il payload deve iniziare con le caselle dell'intestazione seguite da frammenti.  Nota che la casella "ftyp", "Live Server manifesto casella" e 'moov' (in questo ordine) deve essere inviata con ogni richiesta, anche se il codificatore necessario riconnettersi perché la richiesta precedente è stata interrotta prima fine del flusso. 
4. Codificatore necessario utilizzare suddivisione in blocchi della codifica di trasferimento per il caricamento poiché non è possibile prevedere la lunghezza del contenuto intera dell'evento live.
5. Al termine dell'evento, dopo l'invio ultimo frammento il codificatore normalmente deve terminare la sequenza di messaggio suddivisione in blocchi della codifica di trasferimento (la maggior parte delle stack di client HTTP gestirlo automaticamente). Codificatore necessario attendere che il servizio restituire il codice di risposta finale e quindi interrompere la connessione. 
6. Utilizzare codificatore non deve sostantivo Events() come descritto in 9.2 in [1] per acquisizione dinamica in servizi multimediali di Microsoft Azure.
7. Se la richiesta HTTP POST cessazione o scade prima del termine di flusso con un errore di TCP, il codificatore deve rilasciare una nuova richiesta POST utilizzando una nuova connessione e seguire i requisiti sopra con il requisito aggiuntivo che il codificatore necessario inviarla di nuovo i due frammenti MP4 precedenti per ogni fase del flusso e riprendere senza introdurre discontinuità nella sequenza temporale media.  Inviare di nuovo le ultime due frammenti MP4 per ciascuna traccia garantisce che non si verifica perdita di dati.  In altre parole, se un flusso contiene una traccia audio e video e la richiesta POST corrente non riesce, il codificatore deve riconnettersi e inviarla di nuovo i frammenti ultime due per la traccia audio, in precedenza sono stati inviati correttamente, e i frammenti ultime due per la traccia video, che in precedenza inviati, per garantire che non si verifica perdita di dati.  Il codificatore deve mantenere un "Avanti" buffer frammenti di elementi multimediali, consente di inviare nuovamente durante la riconnessione.

##<a name="5-timescale"></a>5. scala cronologica 

[[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx) descrive l'utilizzo della "Scala cronologica" per SmoothStreamingMedia (sezione 2.2.2.1), StreamElement (sezione 2.2.2.3), StreamFragmentElement(2.2.2.6) e LiveSMIL (sezione 2.2.7.3.1). Se non è presenta il valore della scala cronologica, il valore predefinito utilizzato è 10.000.000 (10 MHz). Sebbene smussate Streaming specifica del formato non bloccare l'uso di altri valori della scala cronologica, la maggior parte degli impieghi implementazioni codificatore questo valore predefinito (10 MHz) per generare Smooth Streaming caricamento dei dati. A causa di [Azure Media dinamico imballaggio](media-services-dynamic-packaging-overview.md) caratteristica, è consigliabile usare kHz 90 scala cronologica per i flussi video e 44,1 o 48.1 kHz per flussi audio. Se i valori della scala cronologica diversi vengono utilizzati per diversi flussi, è necessario inviare la scala cronologica livello di flusso. Fare riferimento a [[MS-SSTR]](https://msdn.microsoft.com/library/ff469518.aspx).     

##<a name="6-definition-of-stream"></a>6. definizione di "Flusso"  

"Flusso" è l'unità di base dell'operazione di acquisizione live per la composizione di presentazione in diretta, la gestione di trasmissione failover e ridondanza scenari. "Flusso" è definito come un univoco frammentato MP4 flusso di bit che può contenere una singola traccia o più tracce. Una presentazione attiva completa potrebbe contenere uno o più flussi a seconda della configurazione di encoder(s) live. Gli esempi riportati di seguito illustrano varie opzioni di utilizzo flusso o ai flussi per la composizione di una presentazione attiva completa.

**Esempio:** 

Clienti creare una presentazione di trasmissione live che include il bitrate audio/video seguenti:

Video: 3000kbps, 1500kbps, 750kbps

Audio – 128kbps

###<a name="option-1-all-tracks-in-one-stream"></a>Opzione 1: Tutte le tracce in un flusso

In questo caso, un singolo codificatore genera tutte le tracce audio/video e raggrupparle in una sola frammentato MP4 flusso di bit che viene inviato tramite una connessione HTTP POST. In questo esempio è un solo flusso di dati per la presentazione live:

![image2, consente][image2]

###<a name="option-2-each-track-in-a-separate-stream"></a>Opzione 2: Ogni traccia nel flusso separato

Questa opzione, encoder(s) inserire solo una traccia in ogni flusso di bit MP4 frammento e registrare tutti i flussi su più connessioni HTTP distinte. Questa operazione con Encoder uno o più Encoder. Dal punto di vista dell'acquisizione live, presentazione in diretta è composto da quattro flussi.

![image3][image3]

###<a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Opzione 3: Incorporare traccia audio con la traccia video velocità più bassa in un flusso

In questo caso, il cliente sceglie raggruppare la traccia audio con la traccia video velocità più bassa in un frammento MP4 flusso di bit e lasciare le tracce di video due è il proprio flusso. 


![image4][image4]


###<a name="summary"></a>Riepilogo

Che cos'è visualizzata sopra non è un elenco completo di tutte le opzioni di acquisizione possibili per questo esempio. In realtà, qualsiasi raggruppamento di tracce in flussi è supportata dagli acquisizione live. Clienti e fornitori codificatore possono scegliere le rispettive implementazioni in base a complessità ingegneria, capacità codificatore e la ridondanza e considerazioni sul failover. Tuttavia si noti che nella maggior parte dei casi è solo una traccia audio per l'intera presentazione live pertanto è importante garantire la healthiness del flusso di acquisizione che contiene la traccia audio. Questa considerazione spesso restituisce come risultato l'applicazione pratica di traccia audio nel proprio flusso (come opzione 2) o aggregazione con velocità video traccia più in basso (come opzione 3). Anche per migliorare la ridondanza e tolleranza, l'invio di tenere traccia lo stesso audio in due diversi flussi (opzione 2 con ridondanti tracce audio) o aggregazione brano almeno due delle più bassi tracce di video di velocità (3 opzione con la funzionalità audio inclusi in almeno due flussi video) è consigliabile per live caricamento in servizi multimediali di Microsoft Azure.

##<a name="7-service-failover"></a>7. Failover del servizio di 

Specificato natura di streaming live, non è fondamentale per garantire la disponibilità del servizio supporto failover buona. Servizi multimediali di Microsoft Azure è progettato per gestire i diversi tipi di errori tra cui errori di rete, gli errori di server, i problemi di spazio di archiviazione e così via. Quando utilizzato in combinazione con una logica di failover adeguata dal lato codificatore live, dei clienti possono ottenere un servizio di trasmissione live affidabilità dal cloud.

In questa sezione verranno illustrati gli scenari di failover di servizio. In questo caso, l'errore si verifica in un punto qualsiasi all'interno del servizio e come un errore di rete. Ecco alcuni suggerimenti per l'implementazione di codificatore per la gestione di failover del servizio:


1. Utilizzare un timeout secondo 10 per stabilire la connessione TCP.  Se un tentativo di stabilire la connessione dura più di 10 secondi, annullare l'operazione e riprovare. 
2. Utilizzare un timeout breve per l'invio della richiesta HTTP blocchi di messaggio.  Se la durata di frammento destinazione MP4 N secondi, utilizzare un timeout di invio tra N e 2N secondi; Utilizzare un timeout di 6-12 secondi, ad esempio, se la durata di frammento MP4 è 6 secondi.  Se si verifica un timeout, ripristinare la connessione, aprire una nuova connessione e riprendere flusso acquisizione sulla nuova connessione. 
3. Mantenere un buffer in sequenza contenente ultime due frammenti, per ogni traccia correttamente e completamente inviati al servizio.  Se la richiesta HTTP POST per un flusso viene interrotta o si verifica timeout nelle versioni precedenti alla fine del flusso di aprire una nuova connessione iniziare un'altra richiesta HTTP POST, inviare di nuovo le intestazioni di flusso, inviare di nuovo le ultime due frammenti per ogni traccia e riprendere il flusso senza introdurre una discontinuità nella sequenza temporale media.  Consente di ridurre il rischio di perdita di dati.
4. È consigliabile che il codificatore non limita il numero di tentativi per stabilire una connessione o riprendere streaming quando si verifica un errore TCP.
5. Dopo un errore TCP:
    1. La connessione corrente deve essere chiuso ed è necessario creare una nuova connessione per una nuova richiesta HTTP POST.
    2. Nuovo HTTP POST URL deve essere lo stesso URL POST iniziale.
    3. Nuovo POST di HTTP deve Includi intestazioni di flusso (casella "ftyp", "Live Server manifesto casella" e 'moov') identiche alle intestazioni di flusso nel POST iniziale.
    4. Le ultime due frammenti inviati per ciascuna traccia necessario inviare di nuovo e streaming ripresa senza introdurre una discontinuità nella sequenza temporale di elementi multimediali.  I timestamp frammento MP4 devono avere incrementi continuamente, anche in tutte le richieste HTTP POST.
6. Il codificatore deve terminare la richiesta HTTP POST se dati non viene inviati a una velocità adeguata con la durata di frammento MP4.  Una richiesta HTTP POST che non invia dati può impedire a servizi multimediali di Windows Azure rapidamente la disconnessione dal codificatore in caso di un aggiornamento del servizio.  Per questo motivo, HTTP POST per caricato in memoria bassa tracce (segnale di Active Directory) devono essere breve durata, terminazione non appena viene inviato il frammento caricato in memoria bassa.

##<a name="8-encoder-failover"></a>8. Failover codificatore

Codificatore failover è il secondo tipo di scenario di failover che deve essere affrontato per il recapito di trasmissione live-to-end. In questo scenario, la condizione di errore dove si trova sul lato codificatore. 

![image5][image5]


Di seguito sono le aspettative dal punto finale di acquisizione dinamica in caso di failover codificatore:

1. DEVE creata una nuova istanza di codificatore per continuare streaming, come illustrato nella figura precedente (flusso di 3000 k video con linea tratteggiata).
2. Nuovo codificatore necessario utilizzare lo stesso URL per le richieste POST HTTP dell'istanza non riuscito.
3. Richiesta POST del nuovo codificatore deve includere stesso frammentati MP4 caselle dell'intestazione del nome dell'istanza non riuscito.
4. Nuovo codificatore deve essere sincronizzato correttamente con tutti gli altri Encoder in esecuzione per la stessa presentazione live per generare esempi audio/video sincronizzati con i limiti allineati frammento.
5. Il nuovo flusso deve essere livello semantico equivalente con il flusso precedente e intercambiabile a livello di intestazione e il frammento.
6. Nuovo codificatore tentare di ridurre al minimo la perdita di dati.  Fragment_absolute_time e fragment_index di file multimediali frammenti dovrebbero aumentare dal punto in cui il codificatore ultimo interrotta.  La fragment_absolute_time e fragment_index devono aumentare in modo continuo, ma è possibile presentare una discontinuità se necessario.  Servizi multimediali di Azure ignorerà frammenti che sia già ricevuti ed elaborata, pertanto è preferibile rischiare di rinvio frammenti rispetto alla introdurre discontinuità nella sequenza temporale di elementi multimediali. 

##<a name="9-encoder-redundancy"></a>9. ridondanza encoder 

Per alcuni critiche live eventi che ancora maggiore disponibilità e qualità dell'esperienza, è consigliabile utilizzare Encoder ridondanti attivo attivo per ottenere di failover senza perdita di dati.

![image6][image6]

Come illustrato nella figura precedente, sono disponibili due gruppo codificatore inserendo contemporaneamente due copie di ciascun flusso nel servizio live di. Questa impostazione è supportata poiché servizi multimediali di Microsoft Azure è in grado di filtrare frammenti duplicati in base a flusso ID e frammento timestamp. Il flusso di live risultante e archivio sarà un unico della copia di tutti i flussi dell'aggregazione possibili migliore da due origini. Ad esempio in un caso estremo ipotetico, a condizione che non esiste un codificatore (non deve essere lo stesso) in esecuzione in un determinato momento per ogni flusso, la risultante flusso live dal servizio sarà continuo senza perdita di dati. 

Requisiti per questo scenario sono simile ai requisiti di maiuscole/minuscole codificatore Failover con l'eccezione che il secondo set codificatore di esegue allo stesso tempo Encoder principale.

##<a name="10-service-redundancy"></a>10. ridondanza del servizio di  

Per distribuzione globale altamente ridondante, a volte è necessario disporre area tra copia di backup per la gestione di emergenza internazionali. Espansione sulla topologia di "Codificatore ridondanza", clienti possono scegliere di utilizzare una distribuzione service ridondanti in un'area diversa connessa con il set di 2a codificatore di. I clienti potrebbero funzionare anche con un provider di rete CDN per distribuire un GTM (globale il traffico Manager) davanti distribuzioni due service per diretta instradare il traffico client. I requisiti per l'Encoder sono analogo a quello "Codificatore ridondanza" con l'unica eccezione che il secondo set codificatore di necessario puntare a un altro live acquisizione punto finale. Il diagramma seguente illustra il programma di installazione:

![image7][image7]

##<a name="11-special-types-of-ingestion-formats"></a>11. particolari tipi di formati di acquisizione 

In questa sezione vengono illustrate alcune tipo speciale di formati di acquisizione live sono progettate per gestire alcuni scenari specifici.

###<a name="sparse-track"></a>Traccia caricato in memoria bassa

Durante l'esecuzione di una presentazione di trasmissione in diretta con esperienza rich client, è spesso necessario per la trasmissione di eventi ora sincronizzata o segnali in banda con i dati multimediali principale. Un esempio è dinamico inserimento annunci live. Questo tipo di evento segnalazione è diverso da regolare l'audio/video streaming a causa di natura caricato in memoria bassa. In altre parole, i dati di segnalazione in genere non viene eseguita continuamente e l'intervallo può essere difficile prevedere. Il concetto di rilevamento caricato in memoria bassa è stato appositamente progettato per il caricamento e trasmettere dati di segnalazione in banda.

Di seguito è un'implementazione consigliata per il caricamento di traccia caricato in memoria bassa:

1. Creare frammentato MP4 bit-flusso separato che contiene solo caricato in memoria bassa tracce senza tracce audio/video.
2. Nella finestra di"Live Server manifesto" come definito nella sezione 6 [1], utilizzare il parametro "parentTrackName" per specificare il nome della traccia padre. Fare riferimento alla sezione 4.2.1.2.1.2 [1] per altri dettagli.
3. Nella finestra di"Live Server manifesto" manifestOutput deve essere impostata su "true".
4. Natura caricato in memoria bassa dell'evento segnalazione, è consigliabile che:
    1. All'inizio dell'evento live, codificatore invia caselle dell'intestazione iniziale al servizio che consentirebbe il servizio registrare la traccia caricato in memoria bassa nel manifesto del client.
    2. Il codificatore deve terminare la richiesta HTTP POST quando non inviato.  Un POST HTTP esecuzione prolungata che non invia dati può impedire servizi multimediali di Windows Azure rapidamente la disconnessione dal codificatore in caso di un servizio aggiornamento o server riavviare il computer, come il server multimediale verrà bloccato temporaneamente in un'operazione di ricezione sul socket. 
    3. Durante il periodo quando i dati di segnalazione non sono disponibili, chiudere il codificatore opportuno HTTP POST richiedere.  Durante la richiesta POST è attiva, il codificatore per l'invio di dati 
    4. Quando si inviano frammenti caricato in memoria bassa, codificatore impostare necessaria un'intestazione di lunghezza del contenuto se è disponibile.
    5. Quando si inviano caricato in memoria bassa frammento con una nuova connessione, codificatore dovrebbe iniziare l'invio da caselle dell'intestazione seguite da frammenti di nuovo. Si tratta di gestire il caso in cui failover dove si trova tra e la nuova connessione caricato in memoria bassa viene stabilita in un nuovo server che non sono visibili la traccia caricato in memoria bassa prima.
    6. Frammento di traccia caricato in memoria bassa saranno disponibile al client quando l'elemento padre corrispondente traccia frammento che è uguale o maggiore valore timestamp è disponibile al client. Ad esempio, se il frammento caricato in memoria bassa ha un timestamp di t = 1000, è previsto dopo il client rileva video (presupponendo che il nome della traccia padre è video) frammento timestamp 1000 o versioni successive, è possibile scaricare il frammento caricato in memoria bassa t = 1000. Si noti che il segnale effettivo ben potrebbe essere utilizzato per un'altra posizione nella sequenza temporale presentazione per lo scopo designato. Nell'esempio precedente, è possibile che il frammento caricato in memoria bassa del t = 1000 ha un payload XML che è possibile inserire un annuncio in una posizione di alcuni secondi in un secondo momento.
    7. Payload della traccia caricato in memoria bassa frammento può essere in vari formati diversi (ad esempio XML o testo o binario, e così via) a seconda di diversi scenari. 


###<a name="redundant-audio-track"></a>Brano ridondante

In Streaming adattata HTTP uno scenario tipico (ad esempio Smooth Streaming o trattino), è spesso solo una traccia audio nell'intera presentazione. Diversamente da quanto succede le tracce video che hanno più livelli di qualità per il client per scegliere da condizioni di errore, la traccia audio può essere un singolo punto di errore se l'acquisizione di flusso che contiene la traccia audio è stata interrotta. 

Per risolvere il problema, servizi multimediali di Microsoft Azure supporta l'acquisizione di tracce audio ridondanti. L'idea è la stessa traccia audio può essere inviata più volte in flussi differenti. Il servizio verrà registrare solo la traccia audio una volta nel manifesto del client, ma è possibile utilizzare le tracce audio ridondanti come backup per il recupero audio frammenti se la traccia audio principale si verificano problemi. Per l'acquisizione ridondanti tracce audio, il codificatore è necessario:

1. Creare la stessa traccia audio in più frammento MP4 Stream di bit. Le tracce audio ridondanti devono essere livello semantico equivalente con esattamente lo stessa timestamp frammento e intercambiabile a livello di intestazione e il frammento.
2. Assicurarsi che "audio" voce di Live Server manifesto (6 sezione [1]) essere uguale per tutte le tracce audio ridondanti.

Di seguito è un'implementazione consigliata per ridondanti tracce audio:

1. Invia ogni traccia audio univoco in un flusso autonomamente.  Invia anche un flusso ridondante per ciascuno di questi flussi traccia audio, in cui il flusso di 2a differisce dal 1 solo per l'identificatore nell'URL HTTP POST: {protocollo} :// {indirizzo del server} / {path}/Streams({identifier}) punto di pubblicazione.
2. Utilizzare flussi distinti per inviare i due bitrate video più bassi. Ciascuno di questi flussi anche deve contenere una copia di ogni traccia audio univoco.  Ad esempio, quando sono supportate più lingue, questi flussi devono contenere tracce audio per ogni lingua.
3. Utilizzare le istanze di server distinto (codificatore) per la codifica e inviare i flussi ridondanti indicati nel (1) e (2). 



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png

 