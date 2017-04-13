<properties
    pageTitle="Analizzare i file multimediali tramite il portale di Azure | Microsoft Azure"
    description="In questo argomento viene illustrato come elaborare i file multimediali con processori multimediali di elementi multimediali Analitica (MPs) tramite il portale di Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="analyze-your-media-using-the-azure-portal"></a>Analizzare i file multimediali tramite il portale di Azure

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere [Versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="overview"></a>Panoramica

Azure Analitica di servizi di supporto è una raccolta di riconoscimento vocale e visione componenti (scala aziendale, conformità, sicurezza e portata globale) che rendono più semplice per le aziende e alle aziende di derivano approfondimenti dai propri file video. Per ulteriori informazioni preliminari Analitica servizi multimediali di Azure consultare [questo](media-services-analytics-overview.md) argomento. 

In questo argomento viene illustrato come elaborare i file multimediali con processori multimediali di elementi multimediali Analitica (MPs) tramite il portale di Azure. Elementi multimediali Analitica MPs generare file JSON o MP4. Se un processore media prodotto estensione MP4, è possibile scaricare gradualmente il file. Se un processore media prodotto un file JSON, è possibile scaricare il file dallo spazio di archiviazione blob Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Scegliere una risorsa che si desidera analizzare 
 
1. Nel [portale di Azure](https://portal.azure.com/), selezionare il proprio account di servizi multimediali di Windows Azure.
2. Nella finestra **Impostazioni** selezionare **risorse**.  
.
    ![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze001.png)

2. Selezionare la risorsa che si desidera analizzare e fare clic sul pulsante **Analizza** .
        
    ![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze002.png)

3. Nella finestra di **risorse multimediali processo con Media Analitica** , selezionare il processore. 

    Il resto dell'articolo spiega perché e su come utilizzare ogni processore. 
   
4. Premere **Crea** all'inizio di un processo.

## <a name="azure-media-indexer"></a>Media Azure indicizzatore

Processore di elementi multimediali **Azure Media indicizzatore** consente di verificare che supportano le ricerche di contenuti e i file multimediali, come generare sottotitoli tracce didascalie. In questa sezione fornisce alcuni dettagli sulle opzioni che è possibile specificare per questo Management Pack.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Lingua

Linguaggio naturale per essere riconosciuti nel file multimediale. Ad esempio inglese o spagnolo. 

### <a name="captions"></a>Didascalie

È possibile scegliere un formato di didascalia generati dal contenuto. Un processo di indicizzazione può generare sottotitoli codificati file nei formati seguenti:  

- **Sami di Inari**
- **TTML**
- **WebVTT**

Chiusa didascalia (CC) file nei formati seguenti possono essere utilizzati per rendere accessibile agli utenti con disability udito file audio e video.

### <a name="aib-file"></a>File AIB

Selezionare questa opzione se si desidera generare il file Audio indice Blob per l'utilizzo con l'IFilter Server SQL personalizzata. Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blog.

### <a name="keywords"></a>Parole chiave

Selezionare questa opzione se si desidera generare un file XML di parole chiave. Il file contiene parole chiave estratte da contenuto vocale con frequenza e informazioni di offset.

### <a name="job-name"></a>Nome del processo

Un nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare l'avanzamento di un processo. 

### <a name="output-file"></a>File di output

Un nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-hyperlapse"></a>Media Azure Hyperlapse

Azure Hyperlapse Media è un Management che crea video smussati tempo trascorso dal contenuto prima persona o azione fotocamera.  Per ulteriori informazioni, vedere [questo](media-services-hyperlapse-content.md) argomento. In questa sezione fornisce alcuni dettagli sulle opzioni che è possibile specificare per questo Management Pack.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Velocità 

Specificare la velocità con cui velocizzare il video di input. Il risultato è un rendering delle immagini stabilizzata e tempo trascorso dell'input video.

### <a name="job-name"></a>Nome del processo

Un nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare l'avanzamento di un processo. 

### <a name="output-file"></a>File di output

Un nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-face-detector"></a>Rilevamento nominale Media Azure

Processore media **Azure Media nominale rilevamento** (programma minimo) consente di contare, tenere traccia dei movimenti del e valutare anche la partecipazione del pubblico e reazione tramite le espressioni del volto. Questo servizio contiene due caratteristiche: 

- **Rilevamento nominale**

    Rilevamento nominale Trova e tiene traccia delle risorse umane facce all'interno di un video. Più facce possono essere rilevate e successivamente tenere traccia durante lo spostamento, con i metadati di ora e luogo restituiti in un file JSON. Durante la verifica, tenterà di assegnare un ID coerente per la stessa faccia mentre la persona è gli spostamenti sullo schermo, anche se sono nascosto o lasciare brevemente la cornice.

    >[AZURE.NOTE]Questo services non vengono eseguite riconoscimento viso. Persona che lascia il frame o diventa nascosto per troppo a lungo sarà assegnato un nuovo ID quando restituiscono.

- **Rilevamento emozioni**
    
    Rilevamento emozioni è un componente facoltativo del processore Media rilevamento nominale che restituisce analisi più attributi affettivi dalle facce rilevate, inclusi "Happiness", sadness, paura, anger e così via. 

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Modalità di rilevamento

Uno dei seguenti metodi può essere utilizzato dal processore:

- rilevamento nominale
- per il rilevamento emozioni nominale
- rilevamento di aggregazione emozioni

### <a name="job-name"></a>Nome del processo

Un nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare l'avanzamento di un processo. 

### <a name="output-file"></a>File di output

Un nome descrittivo che consente di identificare il contenuto di output. 

## <a name="azure-media-motion-detector"></a>Rilevazione movimento Media Azure

Processore media **Rilevazione movimento di Azure Media** (programma minimo) consente di identificare in modo efficiente sezioni di interesse all'interno di un video in caso contrario lungo e se. Rilevamento di attività può essere usato su ripresa statico fotocamera per identificare le sezioni del video in cui si verifica l'animazione. Viene generato un file JSON che contiene i metadati con data e ora e il riquadro area geografica in cui si è verificato l'evento.

Questa tecnologia rivolto feed video di sicurezza, in grado di classificare animazione in falsi, ad esempio ombreggiature e modifiche di illuminazione ed eventi importanti. In questo modo è possibile generare gli avvisi di sicurezza da fotocamera feed senza da posta indesiderata con gli eventi irrilevanti infiniti, la possibilità di estrarre allontanato di interesse da video controllo molto lungo.

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Anteprime Video Media Azure

Questo processore consentono di creare riepiloghi dei video lunghi selezionando automaticamente frammenti interessanti del video di origine. Questo è utile quando si desidera fornire una rapida panoramica delle cosa aspettarsi in un video di lunga durata. Per informazioni dettagliate ed esempi, vedere [Usa Azure Media Video miniature per creare un riepilogo di Video](media-services-video-summarization.md)

![Analizzare i video](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nome del processo

Un nome descrittivo che consente di identificare il processo. [Questo](media-services-portal-check-job-progress.md) articolo descrive come è possibile monitorare l'avanzamento di un processo. 

### <a name="output-file"></a>File di output

Un nome descrittivo che consente di identificare il contenuto di output. 


##<a name="next-steps"></a>Passaggi successivi

Servizi multimediali di visualizzazione percorsi formativi.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


