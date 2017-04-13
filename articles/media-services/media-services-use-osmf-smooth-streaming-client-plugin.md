<properties 
    pageTitle="Smussate plug-in Streaming per il framework degli elementi multimediali Apri origine" 
    description="Informazioni su come usare il plug-in Azure Media Services Smooth Streaming per il framework degli elementi multimediali Adobe per Apri origine." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="juliako"/>


# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Come usare il buon Microsoft Streaming plug-in per il framework degli elementi multimediali di Adobe Apri origine

##<a name="overview"></a>Panoramica


Plug-in Microsoft Smooth Streaming per Apri origine Media Framework 2.0 (SS per OSMF) estende le funzionalità predefinite dell'OSMF e aggiunge la riproduzione del contenuto Microsoft Smooth Streaming ai lettori OSMF nuovi ed esistenti. Il plug-in aggiunge anche Smooth Streaming funzionalità di riproduzione a stroboscopio Media riproduzione Simmetrici.

SS per OSMF include due versioni di plug-in:

- Statica plug-in Smooth Streaming per OSMF (SWC)

- Dinamico plug-in Smooth Streaming per OSMF (. SWF)

In questo documento si presuppone che il lettore ha una conoscenza generale di OSMF e OSMF plug-in. Per ulteriori informazioni su OSMF, vedere la documentazione del [sito ufficiale OSMF](http://osmf.org/).

###<a name="smooth-streaming-plugin-for-osmf-20"></a>Plug-in Streaming smussate per OSMF 2.0

Il plug-in supporta il caricamento e la riproduzione di contenuto Smooth Streaming su richiesta con le caratteristiche seguenti:

- Riproduzione Smooth Streaming su richiesta (Riproduci, pausa, Seek, Interrompi)
- Guarda la Smooth Streaming (Play)
- Live funzioni DVR (pausa, ricerca, la riproduzione DVR, Go to Live)
- Supporto per i codec video - h. 264
- Supporto per i codec Audio - AAC
- In più lingue audio, cambio con API predefinite OSMF
- Selezione di qualità max riproduzione con API predefinite OSMF
- Car sottotitoli con plug-in didascalie OSMF
- Adobe&reg; Flash&reg; Player 11,4 o versione successiva.
- Questa versione supporta solo OSMF 2.0.

## <a name="supported-features-and-known-issues"></a>Caratteristiche supportate e problemi noti

Per un elenco completo delle funzionalità supportate, le caratteristiche non supportate e i problemi noti, vedere [questo documento](http://download.microsoft.com/download/3/1/B/31B63D97-574E-4A8D-BF8D-170744181724/Smooth_Streaming_Plugin_for_OSMF.pdf).


## <a name="loading-the-plugin"></a>Caricare il plug-in
Plug-in OSMF può essere caricato in modo statico (in fase di compilazione) o in modo dinamico (in fase di esecuzione). Plug-in Smooth Streaming per il download OSMF include versioni statiche e dinamiche.

- Il caricamento statico: per caricare in modo statico, è necessario un file di libreria statica (SWC). Plug-in statico vengono aggiunti come un riferimento a progetti e unione all'interno del file di output finale durante la compilazione.

- Caricamento dinamico: per caricare in modo dinamico, è necessario un precompilato (SWF). Plug-in dinamici vengano caricate in fase di esecuzione e non incluso nell'output del progetto. (Output compilato) Plug-in dinamici possono essere caricati utilizzando i protocolli HTTP e FILE.

Per ulteriori informazioni sul caricamento statico e dinamico, vedere la [pagina plug-in OSMF](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf)di ufficiale.

###<a name="ss-for-osmf-static-loading"></a>SS per il caricamento statico OSMF
Il frammento di codice seguente viene illustrato come caricare il plug-in SS per OSMF statico e riprodurre un video di base utilizzando OSMF MediaFactory classe. Prima di includere SS per il codice OSMF, assicurarsi che il riferimento al progetto include il plug-in di statica "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc".

```
package 
{
    
    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    
    
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        

        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }
    
        private function initMediaPlayer():void
        {
        
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            
            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}
```


###<a name="ss-for-osmf-dynamic-loading"></a>SS per il caricamento dinamico OSMF

Il frammento di codice seguente viene illustrato come caricare in modo dinamico il plug-in SS per OSMF e riprodurre una basic video utilizzando la classe OSMF MediaFactory. Prima di includere SS per il codice OSMF, copiare il plug-in di dinamico "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" nella cartella del progetto se si desidera caricare FILE protocollo, o in un server web per carico HTTP. Non è necessario includere "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swc" nei riferimenti del progetto.

 
creare un pacchetto {
    
    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;

    
    //Sets the size of the SWF
    
    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;
        
        
        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }
        
        private function initMediaPlayer():void
        {
            
            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);
            
            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();
            
            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );
            
            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  
            
        }
        
        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }
        
        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }
        
        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }
        
        
        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;
            
            state =  event.state;
            
            switch (state)
            {
                case MediaPlayerState.LOADING: 
                    
                    // A new source is started to load.
                    
                    break;
                
                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 
                    
                    break;
                
                case MediaPlayerState.BUFFERING :
                    
                    break;
                
                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }
        
        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }
        
        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.
            
            var resource:URLResource= new URLResource( sourceURL );
            
            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     
        
    }
}

##<a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Riproduzione stroboscopio con il plug-in di dinamico ODMF II

Smooth Streaming OSMF dinamico del plug-in è compatibile con [Stroboscopio Media riproduzione Simmetrici](http://osmf.org/strobe_mediaplayback.html). È possibile utilizzare SS OSMF del plug-in per aggiungere la riproduzione del contenuto Smooth Streaming SMP. A tale scopo, copiare "MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf" in un server web per il caricamento di HTTP utilizzando la procedura seguente:

1.  Individuare la [pagina di configurazione stroboscopio riproduzione](http://osmf.org/dev/2.0gm/setup.html). 
2.  Impostare il src a un'origine Smooth Streaming (ad esempio http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest) 
3.  Apportare le modifiche di configurazione desiderata e fare clic su Anteprima e aggiornamento.
 
    **Nota** Il contenuto è necessario un crossdomain. XML valido. 
4.  Copiare e incollare il codice in una semplice pagina HTML utilizzando un editor di testo, ad esempio nell'esempio seguente:



        <html>
        <body>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
        </embed>
        </object>
        </body>
        </html>



5. Aggiungere il codice di incorporamento smussate OSMF Streaming plug-in e salvare.

        <html>
        <object width="920" height="640"> 
        <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
        <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
        <param name="allowFullScreen" value="true"></param>
        <param name="allowscriptaccess" value="always"></param>
        <param name="wmode" value="direct"></param>
        <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
            type="application/x-shockwave-flash" 
            allowscriptaccess="always" 
            allowfullscreen="true" 
            wmode="direct" 
            width="920" 
            height="640" 
            flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
        </embed>
        </object>
        </html>


6.  Salvare una pagina HTML e pubblicare in un server web. Passare alla pagina web utilizzando i Preferiti Flash&reg; lettore abilitato browser Internet (Internet Explorer, Chrome, Firefox, così via).
7.  Sfrutteranno Smooth Streaming contenuto all'interno di Adobe&reg; Flash&reg; Windows Media Player.

Per ulteriori informazioni sullo sviluppo OSMF generale, vedere la [pagina di sviluppo OSMF](http://osmf.org/resources.html)ufficiale.

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



##<a name="see-also"></a>Vedere anche

[Microsoft adattata Streaming plug-in per l'aggiornamento OSMF](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 
