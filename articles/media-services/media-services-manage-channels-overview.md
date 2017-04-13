<properties 
    pageTitle="Panoramica di Live acqua tramite servizi multimediali di Windows Azure | Microsoft Azure" 
    description="In questo argomento fornisce una panoramica di live acqua tramite servizi multimediali di Windows Azure." 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ne" 
    ms.topic="article" 
    ms.date="10/17/2016"
    ms.author="juliako"/>

#<a name="overview-of-live-steaming-using-azure-media-services"></a>Panoramica di Live acqua tramite servizi multimediali di Windows Azure

##<a name="overview"></a>Panoramica

Durante l'esecuzione di eventi di trasmissione in diretta con servizi multimediali di Windows Azure comunemente coinvolge i componenti seguenti:

- Fotocamera utilizzato per trasmettere un evento.
- Codificatore video live che converte i segnali dalla fotocamera a flussi che vengono inviati a un servizio di trasmissione live.

    Facoltativamente, è possibile ora live più sincronizzata Encoder. Per alcuni critiche live eventi che disponibilità molto elevato e qualità dell'esperienza, è consigliabile utilizzare Encoder ridondanti attivo attivo con la sincronizzazione dell'ora per ottenere di failover senza perdita di dati.
- Servizio di trasmissione live che consente di eseguire le operazioni seguenti:
    
    - caricamento dei contenuti live utilizzando diversi protocolli flussi live (ad esempio RTP o Smooth Streaming)
    - codificare, facoltativamente, il flusso in velocità adattata flusso
    - visualizzare in anteprima il flusso in diretta
    - registrare e archiviare contenuti acquisiti per trasmettere successive (punti)
    - fornire il contenuto tramite protocolli flussi comuni (ad esempio MPEG trattino, uniforme, HLS, unità 10K) direttamente ai clienti o a un contenuto rete (CDN) per la distribuzione.


**Servizi multimediali di Microsoft Azure** (AMS) consente di acquisire, codificare, visualizzare in anteprima, archiviare e fornire il contenuto di flusso in diretta.

Durante l'esecuzione del contenuto ai clienti l'obiettivo è fornire un video di alta qualità a vari dispositivi in condizioni di rete diversa. A tale scopo, utilizzare live Encoder per codificare il flusso in un flusso di video (velocità adattata) con più velocità.  Per occuparsi di streaming su diversi dispositivi, utilizzare servizi multimediali [imballaggio dinamico](media-services-dynamic-packaging-overview.md) per dinamicamente assemblare nuovamente il flusso di altri protocolli. Servizi multimediali di supporta il recapito della velocità di adattamento seguenti streaming tecnologie: HTTP Live Streaming (HLS), Smooth Streaming, MPEG trattino e unità 10K (per solo licenziatari a questo punto Adobe/accesso).

Servizi multimediali di Windows Azure, **canali**, **applicazioni**e **StreamingEndpoints** gestire tutte le funzionalità trasmissione live inclusi acquisizione, formattazione, DVR, sicurezza, scalabilità e ridondanza.

Un **canale** rappresenta una pipeline di elaborazione del contenuto di flusso live. Un canale può ricevere un live flussi di input nei modi seguenti:

- Un locale live codificatore Invia con più velocità **RTP** o **Smooth Streaming** (frammentati MP4) per il canale è configurato per il recapito della **pass-through** . Il recapito **pass-through** è quando flussi acquisiti attraversano s **canale**senza ulteriori elaborazioni. È possibile utilizzare le seguenti Encoder live che generano multi-velocità Smooth Streaming: elementare, lingua inglese, Cisco.  Le seguenti Encoder live output RTP: Adobe Flash Media Live codificatore (FMLE), Telestream Wirecast e Tricaster trascodificatori.  Un codificatore live anche possibile inviare un flusso di velocità singola a un canale che non è abilitata per la codifica live, ma che non è consigliabile. Quando richiesto, al cliente servizi multimediali di un flusso.

    >[AZURE.NOTE] Uso di un metodo pass-through è il modo più economico a live streaming quando si fa più eventi per un lungo periodo di tempo e hanno già investito in Encoder locale. Visualizzare i dettagli [prezzi](/pricing/details/media-services/) .
    
    
- Un codificatore live locale invia un flusso di velocità singola per il canale è abilitato per eseguire la codifica live con i servizi di supporto in uno dei formati seguenti: RTP o Smooth Streaming (frammentati MP4). È supportata anche RTP (MPEG-TS), purché si disponga di una connessione dedicata all'interfaccia di dati di Azure. Le seguenti Encoder live con output RTP sono noti per lavorare con i canali di questo tipo: Telestream Wirecast, FMLE. Il canale avrà la codifica live di in entrata velocità singolo flusso in un flusso di video con più velocità (adattata). Quando richiesto, al cliente servizi multimediali di un flusso.


Partire dalla versione 2.10 servizi multimediali, quando si crea un canale, è possibile specificare in che modo desiderato per il canale ricevere il flusso di input e se impostare o meno desiderato per il canale eseguire la codifica live del flusso. Sono disponibili due opzioni:

- **Nessuno** (pass-through): specificare il valore, se si prevede di utilizzare un codificatore live locale che verrà visualizzato con più velocità flusso (flusso pass-through). In questo caso, il flusso di posta in arrivo passati all'output senza qualsiasi codifica. Questo è il comportamento di un canale prima versione 2.10.  

- **Standard** : scegliere questo valore, se si prevede di utilizzare i servizi di supporto per codificare il flusso di live velocità singolo flusso multi-velocità. Questo metodo è più economico per scalabilità rapidamente per gli eventi poco frequenti. Tenere presente che non esiste un impatto fatturazione per la codifica live e tenere presente che uscire da un canale di codifica live nello stato "Running" causeranno addebiti.  È consigliabile interrompere l'esecuzione canali immediatamente al termine dell'evento flusso live per evitare i costi aggiuntivi orari. 

##<a name="comparison-of-channel-types"></a>Confronto dei tipi di canale

Nella tabella seguente viene fornita una Guida per il confronto di due tipi di canale supportati in servizi multimediali

Caratteristica|Canale pass-through|Canale standard
---|---|---
Velocità singolo input viene codificata in più bitrate nel cloud|No|Sì
Risoluzione massima, numero di livelli|1080p, 8 livelli, 60 + f/s|720p, 6 livelli, 30 f/s
Protocolli di input|RTP, Smooth Streaming|RTP Smooth Streaming e RTP
Prezzo|Vedere la [pagina prezzi](/pricing/details/media-services/) e fare clic sulla scheda "Live Video"|Vedere i [prezzi pagina](/pricing/details/media-services/) 
Limite massimo di esecuzione|24 / 7|8 ore
Supporto per l'inserimento Tablet|No|Sì
Supporto per la segnalazione di Active Directory|No|Sì
Didascalie CEA 608/708 pass-through|Sì|Sì
Possibilità di recuperare dalla breve blocchi in contributo feed|Sì|Non (canale inizierà slating dopo 6 + secondi senza dati di input)
Supporto per GOP input non uniforme|Sì|No: input da correggere 2 secondi GOP
Supporto per l'input tariffe variabili frame|Sì|No, è necessario fisso input frequenza.<br/>Varianti secondarie sono tollerate, ad esempio, durante scene animazione elevato. Ma non è possibile eliminare codificatore a 10 frame/sec.
Auto-arresto dei canali di input quando feed viene persa|No|Dopo aver 12 ore, se non è alcun programma in esecuzione 

##<a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Utilizzo di canali che ricevono flusso in diretta con più velocità da Encoder locale (pass-through)

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolti nel flusso di lavoro **pass-through** .

![Flusso di lavoro Live](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Per ulteriori informazioni, vedere [utilizzo di canali che Multi ricezione-velocità flusso Live da Encoder locale](media-services-live-streaming-with-onprem-encoders.md).

##<a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Utilizzo dei canali abilitati per eseguire la codifica live con servizi multimediali di Windows Azure

Il diagramma seguente illustra le parti principali della piattaforma AMS coinvolti in Live Streaming del flusso di lavoro in cui un canale è abilitato per eseguire la codifica live con i servizi di supporto.

![Flusso di lavoro Live](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Per ulteriori informazioni, vedere [uso di canali che sono abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md).

##<a name="description-of-a-channel-and-its-related-components"></a>Descrizione di un canale e i componenti correlati

###<a name="channel"></a>Canale

Servizi di supporto, s [canale](https://msdn.microsoft.com/library/azure/dn783458.aspx)sono responsabili dell'elaborazione del contenuto di flusso live. Un canale fornisce un endpoint input (acquisizione URL) che è quindi fornire un trascodificatore live. Il canale riceve live flussi di input da trascodificatore live e rende disponibili per la trasmissione tramite uno o più StreamingEndpoints. Canali offrono un endpoint di anteprima (preview URL) che consente di visualizzare in anteprima e convalidare il flusso prima di recapito e ulteriori elaborazioni.

Quando si crea il canale, è possibile ottenere l'URL di caricamento e l'URL di anteprima. Per ottenere questi URL, non è necessario essere in stato avviato il canale. Quando si è pronti iniziare l'inserimento di dati da un trascodificatore live nel canale, è necessario avviare il canale. Una volta trascodificatore live viene avviato il caricamento di dati, è possibile visualizzare l'anteprima del flusso.

Ogni account servizi multimediali può contenere più canali, più programmi e più StreamingEndpoints. A seconda delle esigenze della larghezza di banda e la sicurezza StreamingEndpoint services possono essere dedicati per uno o più canali. Qualsiasi StreamingEndpoint può effettuare il pull da un canale.


###<a name="program"></a>Programma 

Un [programma](https://msdn.microsoft.com/library/azure/dn783463.aspx) consente di controllare la pubblicazione e l'archiviazione dei segmenti di un flusso in diretta. I canali di gestire i programmi. Il canale e applicazione di una relazione è molto simile a supporti tradizionali in un canale dispone di un flusso costante di contenuto e un programma ambito è impostato su alcune evento programmato in tale canale.
È possibile specificare il numero di ore che si desidera conservare il contenuto registrato per il programma impostando la proprietà **ArchiveWindowLength** . Per un massimo di 25 ore, è possibile impostare questo valore da un minimo di 5 minuti. 

ArchiveWindowLength determina anche la maggior quantità di client ora è possibile cercare indietro nel tempo dalla posizione corrente live. Programmi possono essere eseguiti il periodo di tempo specificato, ma verrà continuamente eliminato contenuto che si trova dietro la lunghezza della finestra. Questo valore di questa proprietà determina anche la durata delle manifesti client espandibile.

Ogni programma è associato a una risorsa. Per pubblicare il programma è necessario creare un indicatore di posizione per la risorsa associata. Con questo locator consentirà di generare un URL di trasmissione che è possibile fornire per i clienti.

Un canale supporta fino a tre programmi contemporaneamente in esecuzione in modo che è possibile creare più archivi di stesso flusso di posta in arrivo. In questo modo è possibile pubblicare e archiviare diverse parti di un evento in base alle esigenze. Ad esempio, l'esigenza consiste nell'archiviare 6 ore di un programma di trasmettere solo ultimi 10 minuti. A tale scopo, è necessario creare due programmi contemporaneamente in esecuzione. Un programma è impostato per archiviare 6 ore dell'evento, ma il programma non è stato pubblicato. Un altro programma è impostato per l'archiviazione di 10 minuti e questo programma viene pubblicato.


##<a name="billing-implications"></a>Implicazioni di fatturazione

Un canale verrà avviata non appena si tratta di transizioni a "Running" tramite l'API di fatturazione.  

Nella tabella seguente illustra come eseguire il mapping degli stati canale a stati fatturazione nel portale di API e Azure. Si noti che gli Stati sono leggermente diversi tra l'API e UX Portal. Non appena un canale è in stato "Esecuzione" tramite l'API o nello stato "Ready" o "Streaming" nel portale di Azure, la fatturazione sarà attiva.

Per interrompere il canale da fatturazione è ulteriormente, è necessario interrompere il canale tramite l'API o nel portale di Azure.
Si è responsabile dell'interruzione dei canali dopo aver con il canale. Errore per interrompere il canale genereranno fatturazione "Continua".

>[AZURE.NOTE]Quando si utilizzano i canali Standard, AMS verrà automatica arresto qualsiasi canale è ancora stato "Running" 12 ore dopo il feed di input viene perso e non sono presenti programmi in esecuzione. Tuttavia, verrà comunque fatturato per l'intervallo di tempo il canale in stato "Running".

###<a id="states"></a>Stati canale e come sono mappati alla modalità di fatturazione 

Lo stato corrente di un canale. I valori possibili includono:

- **Arrestato**. Questo è lo stato iniziale del canale dopo la sua creazione (a meno che per l'avvio automatico è stata selezionata nel portale.) Nessuna fatturazione si verifica questo stato. In questo stato, è possibile aggiornare le proprietà del canale ma streaming non è consentito.
- **Avvio**. Verrà avviato il canale. Nessuna fatturazione si verifica questo stato. Nessun aggiornamento o streaming consentito durante questo stato. Se si verifica un errore, il canale ritorna allo stato interrotto.
- **In esecuzione**. Il canale è in grado di elaborare flussi in diretta. Ora è fatturazione l'uso. È necessario interrompere il canale per evitare ulteriormente fatturazione. 
- **Interruzione**. Interruzione in corso il canale. Nessuna fatturazione si verifica questo stato temporaneo. Nessun aggiornamento o streaming consentito durante questo stato.
- **Eliminazione**. Il canale da eliminare. Nessuna fatturazione si verifica questo stato temporaneo. Nessun aggiornamento o streaming consentito durante questo stato.

Nella tabella seguente illustra come eseguire il mapping degli stati canale nella modalità di fatturazione. 
 
Stato di canale|Indicatori di interfaccia utente del portale|Si tratta di fatturazione?
---|---|---
Avvio|Avvio|Nessuna (stato temporaneo)
In esecuzione|Pronto (non i programmi in esecuzione)<br/>o<br/>Trasmissione (almeno un programma in esecuzione)|SÌ
Interruzione|Interruzione|Nessuna (stato temporaneo)
Arrestato|Arrestato|No


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="related-topics"></a>Argomenti correlati

[Servizi multimediali Azure MP4 frammentati Live acquisizione specifica](media-services-fmp4-live-ingest-overview.md)

[Utilizzo di canali abilitati per eseguire codifica Live con servizi multimediali di Windows Azure](media-services-manage-live-encoder-enabled-channels.md)

[Utilizzo dei canali che ricevono flusso in diretta con più velocità da Encoder locale](media-services-live-streaming-with-onprem-encoders.md)

[Le quote e limitazioni](media-services-quotas-and-limitations.md).  

[Concetti relativi ai servizi di supporto](media-services-concepts.md)
