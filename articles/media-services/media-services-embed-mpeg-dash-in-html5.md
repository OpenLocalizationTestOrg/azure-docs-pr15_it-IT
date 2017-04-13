<properties 
    pageTitle="Incorporamento di Video MPEG-tratto adattata flusso in un'applicazione di HTML5 con DASH.js | Microsoft Azure" 
    description="In questo argomento viene illustrato come incorporare un Video MPEG-tratto adattata Streaming in un'applicazione di HTML5 con DASH.js." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="embedding-a-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Incorporamento di Video MPEG-tratto adattata flusso in un'applicazione di HTML5 con DASH.js

##<a name="overview"></a>Panoramica

MPEG-tratto è uno standard ISO per la trasmissione adattamento del contenuto video, che offre vantaggi significativi per chi desidera distribuire video di alta qualità e adattata i flussi di output. Con MPEG-tratto flusso video verrà aperto automaticamente a una definizione inferiore diventa congestione della rete. Consente di ridurre la probabilità del Visualizzatore di visualizzare un video "sospeso" mentre il lettore download successivi alcuni secondi per riprodurre (o buffer). Come si riduce eccessivo traffico sulla rete, il lettore video restituirà a sua in un flusso di qualità superiore. Un'ora di inizio più veloce per video comporta anche la possibilità di adattare la larghezza di banda necessario. Ciò significa che i primi secondi possono essere riprodotti in un segmento di qualità inferiore veloci per scaricare e quindi passare a un contenuto una volta sufficienti qualità superiore è stati memorizzati.

Dash.js è un lettore di video MPEG-tratto Apri origine scritto in JavaScript. L'obiettivo è fornire un lettore efficace e multipiattaforma che può essere riutilizzato liberamente nelle applicazioni che richiedono la riproduzione del video. Fornisce la riproduzione MPEG-tratto in un browser che supporti W3C Media origine estensioni (MSE) oggi Chrome, Microsoft Edge e IE11 (altri browser è indicato il proprio scopo per il supporto MSE). Per ulteriori informazioni su DASH.js, js vedere repository dash.js GitHub.


##<a name="creating-a-browser-based-streaming-video-player"></a>Creazione di un lettore di video streaming basate su browser

Per creare una pagina web semplice che consente di visualizzare un lettore di video con previsto controlli ad esempio un Riproduci, pausa, riavvolgere e così via, sarà necessario:

1. Creare una pagina HTML
1. Aggiungere il tag video
1. Aggiungere il lettore dash.js
1. Inizializzare Windows Media player
1. Aggiungere uno stile CSS
1. Visualizzare i risultati in un browser che implementa MSE

Inizializzazione Windows Media player può essere completata in un piccolo gruppo di righe di codice JavaScript. Usa dash.js, realmente è semplicissimo incorporare video MPEG-tratto nelle applicazioni basata su browser.

##<a name="creating-the-html-page"></a>Creazione di una pagina HTML

Il primo passaggio consiste nel creare una pagina HTML standard che contiene l'elemento **video** , salvare il file come basicPlayer.html, come illustrato nell'esempio seguente:

    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>

##<a name="adding-the-dashjs-player"></a>Aggiunta di Windows Media Player DASH.js

Per aggiungere l'implementazione di riferimento dash.js all'applicazione, è necessario acquisire il file dash.all.js dalla 1.0 versione del progetto dash.js. Questo deve essere salvato nella cartella JavaScript dell'applicazione. Il file è un file di pratica che riunisce in un unico file tutto il codice dash.js necessarie. Se si dispone di un aspetto intorno repository dash.js, verranno trovare i singoli file, testare codice e molto altro ancora, ma se tutti da eseguire è usare dash.js, sarà il file dash.all.js è necessario.

Per aggiungere il lettore dash.js alle applicazioni, aggiungere un tag script sezione dell'intestazione di basicPlayer.html:

    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>


Creare una funzione per inizializzare il lettore al caricamento della pagina. Aggiungere il seguente script dopo la riga in cui si carica dash.all.js:

    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>

Questa funzione crea innanzitutto un DashContext. Viene utilizzato per configurare l'applicazione per un ambiente di runtime specifico. Dal punto di vista tecnico, definisce le classi che il framework di inserimento dipendenza deve utilizzare durante la creazione dell'applicazione. Nella maggior parte dei casi, si utilizzerà Dash.di.DashContext.

Successivamente, un'istanza della classe principale di framework dash.js, MediaPlayer. Questa classe contiene i principali metodi necessari come riprodurre e posizionare il puntatore, gestisce la relazione con l'elemento video e anche gestisce interpretazione del file multimediale presentazione descrizione (MPD) che descriva il video da riprodurre.

La funzione Startup della classe MediaPlayer è chiamata per assicurarsi che sia pronto per la riproduzione video Windows Media player. Tra l'altro grazie a questa funzione che tutte le classi necessarie (in base al contesto) sono state caricate. Quando il lettore è pronto, è possibile allegare l'elemento video per l'utilizzo della funzione attachView(). In questo modo MediaPlayer inserire il flusso video nell'elemento e controllarne la riproduzione in base alle esigenze.

Passare l'URL di file MPD MediaPlayer in modo che conoscenza il video che è previsto per la riproduzione. La funzione setupVideo() appena creata sarà necessario eseguire dopo la pagina stato completamente caricato. Ripetere l'operazione utilizzando l'evento onload dell'elemento corpo. Modificare il <body> elemento:

    <body onload="setupVideo()">

Infine, impostare le dimensioni dell'elemento video con CSS. In un ambiente di trasmissione adattativo, questa operazione è particolarmente importante poiché non possono modificare le dimensioni del video riprodotto durante la riproduzione adatta fino alla modifica delle condizioni di rete. In questo semplice demo semplicemente forzare l'elemento video all'80% della finestra del browser disponibili mediante l'aggiunta di foglio di stile CSS seguente alla sezione dell'intestazione di pagina:
    
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>

##<a name="playing-a-video"></a>Riproduzione di un Video

Per riprodurre un video, scegliere il browser il file basicPlayback.html e fare clic su Riproduci del lettore video visualizzato.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##<a name="see-also"></a>Vedere anche

[Sviluppo di applicazioni lettore di video](media-services-develop-video-players.md)

[GitHub dash.js archivio](https://github.com/Dash-Industry-Forum/dash.js) 
