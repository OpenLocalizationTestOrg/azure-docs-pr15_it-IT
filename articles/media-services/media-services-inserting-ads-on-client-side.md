<properties 
    pageTitle="Inserimento di annunci sul lato client | Microsoft Azure" 
    description="In questo argomento viene illustrato come inserire annunci sul lato client." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="juliako"/>


#<a name="inserting-ads-on-the-client-side"></a>Inserimento di annunci sul lato client

In questo argomento contiene informazioni su come inserire tipi diversi di annunci sul lato client.

Per informazioni sul supporto di sottotitoli codificati e ad in Live streaming video, vedere [supportati sottotitoli e standard di inserimento di Active Directory](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

>[AZURE.NOTE] Azure Media Player attualmente non supporta gli annunci.

##<a id="insert_ads_into_media"></a>Inserimento di annunci in file multimediali

Servizi multimediali di Azure fornisce supporto per l'inserimento di Active Directory mediante la piattaforma Windows Media: Framework Windows Media Player. Modelli di Windows Media Player con supporto di Active Directory sono disponibili per i dispositivi Windows 8, Silverlight, Windows Phone 8 e iOS. Ogni framework player il codice di esempio che illustra come implementare un'applicazione di riproduzione. Esistono tre tipi diversi di annunci che è possibile inserire l'elenco di elementi multimediali:.

- **Lineare** : annunci frame completo che sospendere il video principale.
- **Nonlinear** – annunci sovrapposizione che vengono visualizzati durante la riproduzione di video principale, in genere un logo o un'altra immagine statica inseriti in Windows Media player.
- **Complementare** , annunci visualizzati all'esterno di Windows Media player.

Annunci possono essere inseriti in qualsiasi momento nella sequenza temporale del video principale. È necessario indicare il lettore i tempi di riproduzione Active Directory e gli annunci pubblicitari da riprodurre. Questa operazione viene eseguita mediante un insieme di file basato su XML standard: modello vastissime (Video Active Directory servizio capacità), digitale Video più Active Directory Playlist (VMAP), il modello di sequenza astratti Media (CANTO) e digitale Video Windows Media Player Active Directory interfaccia definizione (VPAID). File maggior specificano quali annunci da visualizzare. File VMAP specificano quando riprodurre diversi annunci e contengono maggior XML. File CANTO sono un altro modo per gli annunci di sequenza che può contenere maggior XML. File VPAID definiscono un'interfaccia tra il lettore video e Active Directory o server Active Directory.

Ogni modello di Windows Media player presenta alcune differenze sostanziali e ogni trattati in un argomento specifico. In questo argomento venga descritti i meccanismi di base consente di inserire gli annunci. Le applicazioni di lettore di video richiedere annunci da un server di Active Directory. Il server di Active Directory può rispondere in diversi modi:

- Ripristinare un file grande
- Ripristinare un file VMAP (con vastissime capacità incorporato)
- Ripristinare un file CANTO (con vastissime capacità incorporato)
- Ripristinare un file maggior con annunci VPAID

 
###<a name="using-a-video-ad-service-template-vast-file"></a>Tramite un File di modello (vastissime capacità) servizio Active Directory Video

Un file maggior specifica quali Active Directory o visualizzare annunci. XML riportato di seguito è illustrato un esempio di un file maggior per un annuncio lineare:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
    
Annuncio lineare verrà indicato i **<Linear>** elemento. Consente di specificare la durata dell'annuncio, tenere traccia degli eventi, fare clic su a, fare clic su verifica e un numero di **<MediaFile>** elementi. Eventi di rilevamento vengono specificati all'interno di **<TrackingEvents>** elemento e consentire a un server di Active Directory tenere traccia di vari eventi che si verificano durante la visualizzazione di Active Directory. In questo caso l'inizio, punto intermedio, completo ed espandere gli eventi vengono rilevati. Quando l'annuncio venga visualizzato l'evento start. Il punto intermedio si verifica quando almeno 50% della sequenza temporale di Active Directory è stato visualizzato. L'evento complete si verifica quando l'annuncio è eseguita alla fine. L'evento Espandi si verifica quando l'utente si espande il lettore video a schermo intero. Clickthroughs vengono specificati con un **<ClickThrough>** elemento all'interno di un **<VideoClicks>** elemento e specifica un URI a una risorsa da visualizzare quando l'utente fa clic su Active Directory. ClickTracking viene specificato un **<ClickTracking>** elemento, all'interno di **<VideoClicks>** elemento e specifica una risorsa di verifica per il lettore multimediale richiedere quando l'utente fa clic su Active Directory. Il **<MediaFile>** elementi specificano informazioni per una codifica specifica di un annuncio. In caso di più monitor **<MediaFile>** elemento, il lettore video possibile scegliere la codifica migliore per la piattaforma. 

Annunci lineari possono essere visualizzati in un ordine specifico. A tale scopo, aggiungere altri <Ad> gli elementi per il vastissime capacità di file e specificare l'ordine utilizzando l'attributo sequenza. Nell'esempio seguente viene illustrata questa situazione:
    
    <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResouce]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
    
Annunci non lineari vengono specificati in un <Creative> anche l'elemento. Nell'esempio seguente viene illustrato un <Creative> elemento che descrive un annuncio non lineare.

    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>

 
Il **<NonLinearAds>** elemento può contenere uno o più **<NonLinear>** elementi, ognuno dei quali può descrivere un annuncio non lineare. Il **<NonLinear>** elemento specifica la risorsa per l'annuncio non lineare. La risorsa può essere un **<StaticResouce>**, un **<IFrameResource>**, o un **<HTMLResouce>**.**<StaticResource>** descrive una risorsa non HTML e definisce un attributo creativeType che specifica la modalità di visualizzazione della risorsa:

Immagine/gif, jpeg/immagine, immagine/png: la risorsa viene visualizzata in un formato HTML **<img>** tag.

Applicazione/x-javascript: la risorsa viene visualizzata in un tag HTML <**script**>.

Applicazione/x-shockwave-flash – la risorsa viene visualizzato in Flash player.

**<IFrameResource>**descrive una risorsa HTML che può essere visualizzata in un IFrame. **<HTMLResource>**descrive una porzione di codice HTML che può essere inserito in una pagina web. **<TrackingEvents>**specificare gli eventi di rilevamento e l'URI per richiedere quando si verifica l'evento. In questo esempio vengono rilevati gli eventi acceptInvitation e Comprimi. Per ulteriori informazioni sulla **<NonLinearAds>** elemento e ai relativi oggetti figlio, vedere IAB.NET/VAST. Si noti che la **<TrackingEvents>** elemento si trova all'interno di** <NonLinearAds> ** elemento anziché il **<NonLinear>** elemento.

Annunci complementare definiti all'interno di un <CompanionAds> elemento. Il <CompanionAds> elemento può contenere uno o più <Companion> elementi. Ogni <Companion> elemento descrive un annuncio complementare e può contenere un <StaticResource>, <IFrameResource>, o <HTMLResource> che vengono specificati in esattamente come in un annuncio non lineare. Un file grande può contenere più annunci complementare e l'applicazione di Windows Media player può scegliere Active Directory più appropriata per la visualizzazione. Per ulteriori informazioni su vastissime capacità, vedere [maggior 3.0](http://www.iab.net/media/file/VASTv3.0.pdf).

###<a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Tramite un Video digitale più File di Playlist (VMAP) di Active Directory

Un file VMAP consente di specificare quando si verificano le interruzioni di Active Directory, è ogni interruzione il periodo di tempo, annunci quanti possono essere visualizzati all'interno di un'interruzione di e i tipi di annunci potrebbero essere visualizzato durante un'interruzione. Le seguenti operazioni in un file VMAP di esempio che definisce un'interruzione di Active Directory single:
    
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[http://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scaleable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scaleable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
     
Un file VMAP inizia con un <VMAP> elemento che contiene uno o più <AdBreak> elementi, ogni definizione di un'interruzione di Active Directory. Ogni interruzione di Active Directory specifica un tipo di interruzione, l'ID di interruzione e orario. L'attributo breakType specifica il tipo di Active Directory che è possibile riprodurre durante l'interruzione: lineare, non lineare, oppure visualizzare. Visualizzare gli annunci mappa annunci complementare maggior in. In un elenco di separati da virgola (senza spazi), è possibile specificare più di un tipo di Active Directory. Il breakID è un identificatore facoltativo per Active Directory. Il timeOffset consente di specificare quando deve essere visualizzato Active Directory. È possibile specificarlo in uno dei modi seguenti:

1. Ora in formato hh o mmm dove .mmm è millisecondi. Il valore dell'attributo specifica l'ora di inizio della sequenza temporale del video all'inizio dell'interruzione di Active Directory.
1. Percentuale – nel formato n % dove n corrisponde alla percentuale di sequenza temporale del video da riprodurre prima la riproduzione di Active Directory
1. Inizio/fine – consente di specificare che deve essere visualizzato un annuncio prima o dopo il video è stato visualizzato
1. Posizionare – consente di specificare l'ordine delle interruzioni di Active Directory quando l'intervallo delle interruzioni di Active Directory è sconosciuto, ad esempio streaming live. L'ordine di ciascuna interruzione di Active Directory è specificato nel formato #n dove n è un numero intero 1 o versione successiva. 1 indica l'annuncio deve essere riprodotto il prima possibile 2 indica che l'annuncio deve essere riprodotto nella seconda opportunità e così via.

All'interno dell'elemento <**AdBreak**> può essere un elemento <**AdSource**>. L'elemento <**AdSource**> contiene gli attributi seguenti:

1. ID: specifica un identificatore per l'origine di Active Directory
1. allowMultipleAds – un valore Boolean che indica se è possano visualizzare più annunci durante l'interruzione di Active Directory
1. followRedirects – un valore Boolean facoltativo che consente di specificare se deve rispettare il lettore video reindirizza all'interno di una risposta di Active Directory

L'elemento <**AdSource**> fornisce il lettore una risposta di Active Directory all'interno del testo o un riferimento a una risposta di Active Directory. Può contenere uno degli elementi seguenti:

- <VASTAdData>indica che una risposta di Active Directory maggior è incorporata all'interno del file VMAP
- <AdTagURI>URI che fa riferimento a una risposta di Active Directory da un altro sistema
- <CustomAdData>-un non autorizzato stringa che rappresenta una risposta non maggior

In questo esempio viene specificata una risposta di Active Directory in linea con un <VASTAdData> elemento che contiene una risposta maggior Active Directory. Per ulteriori informazioni sugli altri elementi, vedere [VMAP](http://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

L'elemento <**AdBreak**> può inoltre contenere un elemento <**TrackingEvents**>. L'elemento <**TrackingEvents**> consente di tenere traccia di inizio o alla fine di un'interruzione di Active Directory o se si è verificato un errore durante l'interruzione di Active Directory. L'elemento <**TrackingEvents**> contiene uno o più <**verifica**> elementi, ognuno dei quali specifica un evento di rilevamento e una verifica URI. Gli eventi di verifica possibili sono:

1. breakStart-verifica l'inizio di un'interruzione di Active Directory
1. breakEnd: rilevare il completamento di un'interruzione di Active Directory
1. Errore: rileva un errore durante l'interruzione di Active Directory

Nell'esempio seguente mostra un file VMAP che specifica degli eventi

    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>

Per ulteriori informazioni sull'elemento <**TrackingEvents**> e ai relativi oggetti figlio, vedere http://iab.org/VMAP.pdf

###<a name="using-a-media-abstract-sequencing-template-mast-file"></a>Usare un sunto Media sequenza File modello (CANTO)

Un file CANTO consente di specificare i trigger che definiscono quando viene visualizzato un annuncio. Di seguito è un file di esempio CANTO che contiene i trigger per un annuncio rullino pre, un annuncio rullino metà e un annuncio di post-implementazione.

    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
    
        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>

 

Un file CANTO inizia con un **<MAST>** elemento che contiene una **<triggers>** elemento. Il <triggers> elemento contiene uno o più **<trigger>** elementi che definiscono quando deve essere riprodotto un annuncio. 

Il **<trigger>** elemento contiene un **<startConditions>** elemento che specificare quando deve iniziare un annuncio per la riproduzione. Il **<startConditions>** elemento contiene uno o più <condition> elementi. Quando ogni <condition> restituisce true un trigger viene avviato o revocato a seconda che la <condition> è contenuta una **< startConditions**> o **<endConditions>** elemento rispettivamente. Quando più <condition> elementi sono presenti, vengono considerate come l'operatore OR implicito, qualsiasi condizione restituiscono true determinerà trigger avviare. <condition>elementi possono essere annidati. Quando figlio <condition> elementi sono preimpostati, vengono considerate come AND impliciti, tutte le condizioni devono restituire VERO per il trigger avviare. Il <condition> elemento contiene gli attributi seguenti che definiscono la condizione: 

1. **tipo** : Specifica il tipo di condizione, evento o la proprietà
1. **nome** : il nome della proprietà o evento da utilizzare durante la valutazione
1. **valore** : il valore che verrà valutata rispetto a una proprietà
1. **operatore** – l'operazione da utilizzare durante la valutazione: EQ (uguale a), NEQ (diverso da), GTR (superiore), GEQ (maggiore o uguale a), LT (minore), LEQ (minore o uguale a), MOD (modulo)

**<endConditions>**contengono anche <condition> elementi. Quando la condizione restituisce true trigger viene reimpostata. Il <trigger> elemento contiene anche un <sources> elemento che contiene uno o più <source> elementi. Il <source> elementi definiscono l'URI per la risposta di Active Directory e il tipo di risposta di Active Directory. In questo esempio viene fornito un URI a una VASTA risposta. 


    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
 

###<a name="using-video-player-ad-interface-definition-vpaid"></a>Utilizzo di definizione dell'interfaccia di Active Directory lettore Video (VPAID)

VPAID è un'API per l'attivazione di unità di Active Directory eseguibile comunicare con un lettore video. In questo modo esperienze altamente interattivi Active Directory. L'utente può interagire con Active Directory e Active Directory è possibile rispondere alle azioni intraprese dal visualizzatore. Ad esempio un annuncio può visualizzare i pulsanti che consentono di visualizzare altre informazioni o una versione più di Active Directory. Il lettore video deve supportare l'API VPAID e ad eseguibile deve implementare l'API. Quando un lettore richiede che un annuncio da un server di Active Directory del server può rispondere con una risposta maggior che contiene un annuncio VPAID.

Un annuncio eseguibile viene creato nel codice che deve essere eseguito in un ambiente di runtime, ad esempio Adobe Flash™ o JavaScript che possono essere eseguite in un web browser. Quando un server di Active Directory restituisce una risposta maggior contenente un annuncio VPAID, il valore di apiFramework attributo la <MediaFile> elemento deve essere "VPAID". Questo attributo specifica che il contenitore Active Directory è un annuncio eseguibile VPAID. Impostare l'attributo type per il tipo MIME del file eseguibile, ad esempio "application/x-shockwave-flash" o "applicazione/x-javascript". Il frammento XML seguente viene illustrato il <MediaFile> elemento da una VASTA risposta contenente un annuncio eseguibile VPAID. 

    
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type=”application/x-shockwaveflash”
                  width=”640” height=”480” apiFramework=”VPAID”>
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
 

Un annuncio eseguibile può essere inizializzato utilizzando il <AdParameters> elemento all'interno di <Linear> o <NonLinear> elementi in una risposta maggior. Per ulteriori informazioni sulla <AdParameters> elemento, vedere [maggior 3.0](http://www.iab.net/media/file/VASTv3.0.pdf). Per ulteriori informazioni sull'API VPAID, vedere [VPAID 2.0](http://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

##<a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>L'implementazione di Windows o Windows Phone 8 lettore con supporto di Active Directory

La piattaforma Microsoft Media: Framework Player per Windows 8 e Windows Phone 8 contiene un insieme di applicazioni di esempio che illustrano come implementare un'applicazione del lettore video utilizzando il framework. È possibile scaricare il Framework lettore e gli esempi da [Windows Media Player Framework per Windows 8 e Windows Phone 8](https://playerframework.codeplex.com).

Quando si apre la soluzione Microsoft.PlayerFramework.Xaml.Samples verrà visualizzato un numero di cartelle all'interno del progetto. La cartella relative alla pubblicità contiene il codice di esempio riguardano la creazione di un lettore video con supporto di Active Directory. All'interno della pubblicità cartella è un numero di file XAML/cs che mostra come inserire annunci in modo diverso. L'elenco seguente vengono descritti:

- AdPodPage.xaml viene illustrato come visualizzare un'unità di Active Directory.
- AdSchedulingPage.xaml viene illustrato come pianificare annunci.
- FreeWheelPage.xaml viene illustrato come utilizzare il plug-in FreeWheel per pianificare gli annunci.
- MastPage.xaml viene illustrato come pianificare annunci con un file CANTO.
- ProgrammaticAdPage.xaml viene illustrato come pianificare a livello di programmazione annunci in un video.
- ScheduleClipPage.xaml viene illustrato come pianificare un annuncio senza un file di grandi.
- VastLinearCompanionPage.xaml viene illustrato come inserire lineare e Active Directory correlata.
- VastNonLinearPage.xaml viene illustrato come inserire un annuncio non lineare.
- VmapPage.xaml viene illustrato come specificare gli annunci con un file VMAP.

Ognuno di questi esempi viene utilizzata la classe di MediaPlayer definita da framework Windows Media player. Maggior parte degli esempi usare plug-in cui aggiungere il supporto per diversi formati di risposta di Active Directory. Nell'esempio ProgrammaticAdPage a livello di programmazione interagisce con un'istanza MediaPlayer.

###<a name="adpodpage-sample"></a>Esempio di AdPodPage

In questo esempio viene usata la AdSchedulerPlugin per definire quando visualizzare un annuncio. In questo esempio è programmato un annuncio rullino metà riprodotto dopo 5 secondi. L'unità di Active Directory (un gruppo di annunci da visualizzare nell'ordine) viene specificato in un file di grande restituito da un server di Active Directory. URI al file maggior specificato nel <RemoteAdSource> elemento.

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
    
        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>
    
                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>
    
                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>

Per ulteriori informazioni sul AdSchedulerPlugin, vedere [relative alla pubblicità in Framework Player in Windows 8 e Windows Phone 8](http://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

###<a name="adschedulingpage"></a>AdSchedulingPage

In questo esempio Usa anche il AdSchedulerPlugin. Consente di pianificare tre striscioni, un annuncio pre- rullino, un annuncio rullino metà e un annuncio di post-implementazione. URI da vastissime capacità per ogni annuncio viene specificato un <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>
    
                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>


###<a name="freewheelpage"></a>FreeWheelPage

In questo esempio si utilizza FreeWheelPlugin che specifica un attributo di origine che specifica un URI che punta a un file di SmartXML che specifica Active Directory del contenuto, nonché le informazioni sulla pianificazione Active Directory.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="mastpage"></a>MastPage

In questo esempio si utilizza MastSchedulerPlugin che consente di utilizzare un file CANTO. L'attributo origine specifica il percorso del file CANTO.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="programmaticadpage"></a>ProgrammaticAdPage

In questo esempio a livello di programmazione interagisce con MediaPlayer. Il file ProgrammaticAdPage.xaml crea un'istanza MediaPlayer:

    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>

Il file ProgrammaticAdPage.xaml.cs viene creato un AdHandlerPlugin aggiunge TimelineMarker per specificare quando deve essere visualizzato un annuncio e quindi aggiunge un gestore per l'evento MarkerReached che carica un RemoteAdSource che specifica un URI in un file di grandi e quindi viene riprodotto Active Directory.
    
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;
    
            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }
    
            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }

###<a name="scheduleclippage"></a>ScheduleClipPage

In questo esempio viene usata la AdSchedulerPlugin per pianificare un annuncio rullino metà specificando un file con estensione wmv contenente Active Directory.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearcompanionpage"></a>VastLinearCompanionPage

In questo esempio viene illustrato come utilizzare il AdSchedulerPlugin per pianificare un annuncio lineare rullino metà con un annuncio correlata. Il <RemoteAdSource> elemento consente di specificare il percorso del file grande.
    
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
    
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage

In questo esempio Usa AdSchedulerPlugin pianificare lineare e un annuncio non lineare. Il percorso del file maggior specificato con la <RemoteAdSource> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>
    
                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>
                            
                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

###<a name="vmappage"></a>VMAPPage

In questo esempio viene usata la VmapSchedulerPlugin per pianificare gli annunci tramite un file VMAP. URI al file VMAP viene specificato nell'attributo dell'origine di <VmapSchedulerPlugin> elemento.
    
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>

##<a name="implementing-an-ios-video-player-with-ad-support"></a>Implementare un lettore di Video con supporto di Active Directory di iOS


La piattaforma Microsoft Media: Framework Player per iOS contiene un insieme di applicazioni di esempio che illustrano come implementare un'applicazione del lettore video utilizzando il framework. È possibile scaricare il Framework lettore e gli esempi da [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). La pagina github contiene un collegamento a una pagina Wiki che contiene informazioni aggiuntive su framework lettore e un'introduzione all'esempio player: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).


###<a name="scheduling-ads-with-vmap"></a>Pianificazione gli annunci con VMAP

Nell'esempio seguente viene illustrato come pianificare gli annunci tramite un file VMAP.

    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest
    
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }          
            }

###<a name="scheduling-ads-with-vast"></a>Pianificazione gli annunci con vastissime capacità

Nell'esempio seguente viene illustrato come pianificare un annuncio maggior associazione in ritardo.
    
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
         
   Nell'esempio seguente viene illustrato come pianificare un annuncio maggior associazione anticipata.
Esempio 4: pianificare un anticipata associazione Active Directory maggior //Download il vastissime capacità file se (! [ framework.adResolver downloadManifest: & manifesto withURL: [NSURL URLWithString:@"http://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]]) {[logFrameworkError self];} else {adLinearTime.startTime = 7; adLinearTime.duration = 0;
        
        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }

Nell'esempio seguente viene illustrato come inserire un annuncio utilizzando approssimativa tagliare modifica (RCE)

    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

Nell'esempio seguente viene illustrato come pianificare un'unità di Active Directory.

    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;
    
    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Nell'esempio seguente viene illustrato come pianificare un annuncio rullino metà non permanenti. Un annuncio non permanenti viene riprodotto solo una volta indipendentemente da eventuali la ricerca consente di eseguire il visualizzatore.
    
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    
    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Nell'esempio seguente viene illustrato come pianificare un annuncio rullino metà permanente. Verrà visualizzato un annuncio a ogni volta che viene raggiunto il punto specificato nella sequenza temporale del video.

    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }


Nell'esempio seguente viene illustrato come pianificare un annuncio di post-implementazione.

    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Nell'esempio seguente viene illustrato come pianificare un annuncio pre rullino.
    
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

Nell'esempio seguente viene illustrato come pianificare un annuncio di sovrapposizione rullino metà.
    
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }



##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
##<a name="see-also"></a>Vedere anche

[Sviluppo di applicazioni lettore di video](media-services-develop-video-players.md)
