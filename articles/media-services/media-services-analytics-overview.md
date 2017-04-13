<properties
    pageTitle="Panoramica Analitica dei servizi Media Azure | Microsoft Azure"
    description="Servizi multimediali di Azure offre l'anteprima pubblica di Azure Media Analitica, un insieme di servizi di visione sintesi e riconoscimento vocale e dal computer in scala aziendale, conformità, sicurezza e portata globale. Servizi multimediali Analitica Azure sono progettati con i componenti di piattaforma principali servizi multimediali di Windows Azure e pertanto sono pronti a gestire i file multimediali in scala il primo giorno di elaborazione. "
    services="media-services"
    documentationCenter=""
    authors="juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/24/2016"   
    ms.author="milanga;juliako;johndeu"/>

# <a name="azure-media-services-analytics-overview"></a>Panoramica di Analitica dei servizi Media Azure

##<a name="overview"></a>Panoramica

Altre organizzazioni e aziende sono adozione video come mezzo di comunicazione preferito per formare i dipendenti, partecipazione a clienti e funzioni di business del documento. Cloud computing funzionalità è particolarmente utile archiviare, flusso e accedere a questi file multimediali di grandi dimensioni, ma come società Ingrandisci loro raccolta contenuto video, devono avere un identico efficace per estrazione nuovi approfondimenti da video per creare più significativo, personalizzata interazioni con i gruppi di destinatari e allontanare a livello avanzato.

Per risolvere questo crescente esigenza di marketplace, servizi multimediali di Windows Azure offre Media Analitica, un insieme di riconoscimento vocale e visione componenti (scala aziendale, conformità, sicurezza e portata globale) che rendono più semplice per le aziende e alle aziende di derivano approfondimenti dai propri file video. Servizi multimediali Analitica Azure sono progettati con i componenti di piattaforma principali servizi multimediali di Windows Azure e pertanto sono pronti a gestire i file multimediali in scala il primo giorno di elaborazione.

Azure Media Analitica consentono agli sviluppatori di iniziare a utilizzare la funzionalità di visione per il video in un numero limitato e visualizzare questa funzionalità avanzate nelle applicazioni rapidamente. Azure Analitica Media è progettato per essere utilizzato da aziende con fondo scala, conformità, sicurezza e portata globale necessari affinché organizzazioni di grandi dimensioni.

Il diagramma seguente illustra **Analitica Media** e altre parti principali della piattaforma servizi multimediali. 

![Flusso di lavoro VoD](./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png)

Processori multimediali Media Analitica generano file JSON o MP4. Se un processore media prodotto estensione MP4, è possibile scaricare gradualmente il file. Se un processore media prodotto un file JSON, è possibile scaricare il file dallo spazio di archiviazione blob Azure. 

## <a name="azure-media-analytics-services"></a>Servizi multimediali Analitica Azure

- **Indicizzatore** – Azure Media indicizzatore consente di rendere il contenuto che supportano le ricerche, nonché generare sottotitoli tracce didascalie. Servizi multimediali di Azure rilasciato **Azure Media indicizzatore 2 anteprima** con supporto delle lingue dell'indicizzazione più veloce e più ampio. Lingue supportate includono inglese, spagnolo, francese, tedesco, italiano, cinese, portoghese e arabo. Per informazioni dettagliate ed esempi, vedere [video processo con Azure Media indicizzatore 2](media-services-process-content-with-indexer2.md)
 
- **Hyperlapse** – Microsoft Hyperlapse è il risultato di oltre 20 anni di ricerche visione computer in Microsoft Research (MSR), risultante dalla combinazione stabilizzazione video e l'ora di scadenza per creare contenuto video rapidi, consumo, grande il formato esteso. Oltre a creare intervalli, è anche possibile utilizzare Hyperlapse per creare video stabili da video shaky acquisiti tramite telefoni cellulari e videocamere. Per informazioni dettagliate ed esempi, vedere [I file multimediali Hyperlapse con Azure Media Hyperlapse](media-services-hyperlapse-content.md)
 
- **Rilevamento** : È possibile utilizzare questo servizio per rilevare animazione in un video con sfondi di elementi decorativi. Questo è ideale per chi desidera controllare per falsi gli eventi di animazione rilevato da fotocamere controllo sul feed video controllo. Per informazioni dettagliate ed esempi, vedere [Rilevamento per Azure Media Analitica](media-services-motion-detection.md).
 
- **Rilevamento nominale ed emozioni nominale** – tramite questo servizio, è possibile rilevare facce di persone e le emozioni, inclusi "Happiness", sadness, sorprese, anger, guidiziaria, paura, pretendere una risposta immediata e indifference/indipendente. Questa operazione diverse settore utili applicazioni, descritte di seguito, inclusi l'aggregazione e analisi opinioni dei persone partecipazione a un evento. Per informazioni dettagliate ed esempi, vedere [nominale e rilevamento emozioni per Azure Media Analitica](media-services-face-and-emotion-detection.md).
 
- **Riepilogo video** : riepilogo Video consentono di creare riepiloghi dei video lunghi selezionando automaticamente frammenti interessanti del video di origine. Questo è utile quando si desidera fornire una rapida panoramica delle cosa aspettarsi in un video di lunga durata. Per informazioni dettagliate ed esempi, vedere [Usa Azure Media Video miniature per creare un riepilogo di Video](media-services-video-summarization.md)

- **Riconoscimento ottico dei caratteri** - Azure Media Analitica OCR (riconoscimento ottico dei caratteri) consente di convertire il contenuto di testo in file video in testo digitale modificabile, che supportano le ricerche. In questo modo è possibile automatizzare l'estrazione dei metadati significativo da segnale video dei file multimediali.
 
- **Adattamento nominale Scalable** - **Redactor Media Azure** è un Management Analitica Media Azure che offre redazione nominale scalable nel cloud. Nominale redazione consente di modificare il video per sfocatura facce di persone selezionati. È consigliabile utilizzare il servizio di redazione nominale in scenari di sicurezza ed elementi multimediali di news pubblici. Alcuni minuti di materiale che contiene più facce possono richiedere ore redigi manualmente, ma con questo servizio il processo di redazione nominale richiederà pochi semplici passaggi. Per ulteriori informazioni, vedere [](media-services-face-redaction.md) l'articolo.

 
## <a name="common-scenarios"></a>Scenari comuni

Di seguito sono riportati alcuni scenari in cui Analitica Media Azure consente alle organizzazioni e aziende di tutto settore raccogliere nuovi approfondimenti da video per creare più destinatari personalizzate e sugli accordi dipendente, nonché gestire in modo più efficiente grandi quantità di contenuto video:

- **Chiamata Centra** – anche con l'avvento di social networking, chiamata cliente centri facilitano ancora gran parte delle transazioni del servizio clienti. Codifica audio dati è un'ampia varietà di informazioni sui clienti che può essere analizzato per migliorare la roadmap di prodotti e anche formare i dipendenti centro chiamata per ottenere soddisfazione del cliente. Utilizzando Azure Media indicizzatore, clienti sono in grado di estrarre testo e creare un indice di ricerca e dashboard per estrarre intelligence intorno a più comune segnala, segnala di origine di e altri dati rilevanti.

- **Moderazione del contenuto generato dall'utente** : da prese media di news per i reparti polizia, molte organizzazioni dispongono di pubblici portali esposto nel punto in cui sono accettare file multimediali UGC, come video e immagini. A causa di eventi imprevisti può raggiungere il volume di contenuto. In questi scenari è prossimità Impossibile eseguire una recensione efficace manuale del contenuto appropriato per i. I clienti possono si basano sul servizio di moderazione del contenuto per focalizzare l'attenzione sul contenuto appropriato.

- **Controllo** - con la crescita di camere, non c'è un'esplosione di video di controllo. Il controllo manuale del video di controllo è ora in modo intensivo e soggetto all'errore umano. Azure Analitica Media fornisce diversi componenti, ad esempio rilevamento, nominale rilevamento e Hyperlapse per semplificare il processo di revisione, la gestione di più semplice creare derivate.

## <a name="media-services-analytics-media-processors"></a>Processori multimediali Analitica servizi multimediali di Microsoft 

In questa sezione sono elencati tutti Media Services Analitica Media processori (Pannello di gestione) e viene illustrato come utilizzare .NET o resto per ottenere un oggetto pannello di gestione.

### <a name="mp-names"></a>Nomi di Pannello di gestione


- Anteprima dell'indicizzatore 2 Media Azure
- Media Azure indicizzatore
- Media Azure Hyperlapse
- Rilevamento nominale Media Azure
- Rilevazione movimento Media Azure
- Anteprime Video Media Azure
- Media Azure OCR

### <a name="net"></a>.NET

La funzione seguente accetta uno dei nomi Pannello di gestione specificati e restituire un oggetto pannello di gestione.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


## <a name="rest"></a>RESTO

Richiesta:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net
    
Risposta:
        
    . . .
    
    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

##<a name="demos"></a>Demo

[Demo di elementi multimediali Analitica Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

##<a name="next-steps"></a>Passaggi successivi

Esaminare i servizi multimediali di percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="related-articles"></a>Articoli correlati

[Annuncio Analitica servizi multimediali](https://azure.microsoft.com/blog/introducing-azure-media-analytics/)
  

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png
