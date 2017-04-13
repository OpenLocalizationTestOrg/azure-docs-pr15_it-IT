<properties 
    pageTitle="Esercitazioni del flusso di lavoro di Avanced Media Encoder Premium" 
    description="Il documento contiene le procedure dettagliate che illustrano come eseguire operazioni avanzate con flusso di lavoro Media Encoder Premium e anche come creare flussi di lavoro complessi con Progettazione flussi di lavoro." 
    services="media-services" 
    documentationCenter="" 
    authors="xstof" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"  
    ms.author="xstof;xpouyat;juliako"/>

#<a name="advanced-media-encoder-premium-workflow-tutorials"></a>Esercitazioni del flusso di lavoro di Media Encoder Premium avanzate

##<a name="overview"></a>Panoramica 

Il documento contiene procedure dettagliate che illustrano come personalizzare flussi di lavoro con **Progettazione flussi di lavoro**. È possibile trovare i file del flusso di lavoro effettivo [qui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/PremiumEncoderWorkflowSamples).  

##<a name="toc"></a>SOMMARIO

Vengono illustrati i seguenti argomenti:

- [Codifica MXF in una singola velocità MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)
    - [Iniziare un nuovo flusso di lavoro](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_start_new) 
    - [Con l'Input di File multimediali](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_file_input)
    - [Verifica di flussi di elementi multimediali](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_streams)
    - [Aggiunta di un video codificatore per. Generazione di un file MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_file_generation)
    - [La codifica del flusso audio](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio)
    - [Multiplexing flussi Audio e Video in un contenitore MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_audio_and_fideo)
    - [Scrivere il file MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_writing_mp4)
    - [Creazione di una risorsa di servizi multimediale dal file di output](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_asset_from_output)
    - [Verificare di aver flusso di lavoro in locale](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_test)
- [Codifica MXF in multibitrate MP4s - imballaggio dinamico attivato](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging)
    - [Aggiunta di uno o più risultati MP4 aggiuntivi](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_more_outputs)
    - [Configurare i nomi di file output](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_conf_output_names)
    - [Aggiunta di un brano separato](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_audio_tracks)
    - [Aggiunta di. File SMIL ISM](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging_ism_file)
- [Codifica MXF in multibitrate MP4 - progetto avanzata](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4)
    - [Panoramica del flusso di lavoro per migliorare](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_overview)
    - [Convenzioni di denominazione dei file](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_file_naming)
    - [Proprietà componente nella radice del flusso di lavoro di pubblicazione](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_publishing)
    - [Hanno generato il file di output nomi si basano sui valori di proprietà pubblicati](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to__multibitrate_MP4_output_files)
- [Aggiunta di anteprime a multibitrate output MP4](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)
    - [Panoramica del flusso di lavoro per aggiungere le anteprime a](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to_multibitrate_MP4_overview)
    - [Aggiunta di codifica JPG](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4__with_jpg)
    - [Relative alla conversione spazio colore](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_color_space)
    - [Scrivere le anteprime](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_writing_thumbnails)
    - [Rilevamento degli errori in un flusso di lavoro](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_errors)
    - [Flusso di lavoro completato](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4_finish)
- [Basate sul tempo limitazione per motivi di output multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim)
    - [Panoramica del flusso di lavoro per iniziare ad aggiungere limitazione per motivi di](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_start)
    - [Utilizzando il compensatore di flusso](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_use_stream_trimmer)
    - [Flusso di lavoro completato](media-services-media-encoder-premium-workflow-tutorials.md#time_based_trim_finish)
- [Introduzione al componente script](media-services-media-encoder-premium-workflow-tutorials.md#scripting)
    - [Gli script all'interno di un flusso di lavoro: world hello](media-services-media-encoder-premium-workflow-tutorials.md#scripting_hello_world)
- [Basate su frame limitazione per motivi di output multibitrate MP4](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim)
    - [Introduzione ai progetti per iniziare ad aggiungere limitazione per motivi di](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_start)
    - [Usare l'elenco dei Clip XML](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clip_list)
    - [Modifica dell'elenco di clip da un componente di script](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_modify_clip_list)
    - [Aggiunta di una comoda proprietà ClippingEnabled](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim_clippingenabled_prop)

##<a id="MXF_to_MP4"></a>Codifica MXF in una singola velocità MP4
 
In questa procedura è necessario creare un singolo velocità. File MP4 con AAC-HE codifica audio da un. File di input MXF. 

###<a id="MXF_to_MP4_start_new"></a>Iniziare un nuovo flusso di lavoro 

Aprire Progettazione flussi di lavoro e selezionare "File"-"nuova area di lavoro"-"trascodifica progetto" 

Il nuovo flusso di lavoro verrà visualizzato 3 elementi: 

- File di origine primario
- Elenco dei clip XML
- File di output/bene  

![Nuovo flusso di lavoro codifica](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-transcode-blueprint.png)

*Nuovo flusso di lavoro codifica*

###<a id="MXF_to_MP4_with_file_input"></a>Con l'Input di File multimediali

Per accettare il file di supporto di input, uno inizia con l'aggiunta di un componente di Input di File multimediali. Per aggiungere un componente al flusso di lavoro, cercare nella casella di ricerca Repository e trascinare la voce desiderata nel riquadro di progettazione. Ripetere l'operazione per l'Input di File multimediali e connettere il componente del File di origine principale pin di input Filename da Input File multimediali.

![Input di File multimediali connessi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-input.png)

*Input di File multimediali connessi*

Prima di poter proseguire molte altre operazioni, è innanzitutto necessario far sapere ai Progettazione flussi di lavoro quali si desidera utilizzare per progettare il flusso di lavoro con file di esempio. A tale scopo, fare clic sull'icona riquadro di progettazione e cercare la proprietà di File di origine principale nel riquadro delle proprietà destra. Fare clic sull'icona della cartella e selezionare il file desiderato per testare il flusso di lavoro. Come questa operazione, il componente Input di File multimediali esaminare il file, inserire il PIN di output per riflettere il file che controllato.

![Input di File multimediali popolati](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-populated-media-file-input.png)

*Input di File multimediali popolati*

Mentre si specifica con quali input si desidera utilizzare, non indica ancora nel punto in cui l'output codificato devono essere inseriti in. Simile alla modalità principale File di origine è stato configurato, configurare la proprietà Output cartella variabili, appena sotto di esso.

![Proprietà configurate di Input e di Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configured-io-properties.png)

*Proprietà configurate di Input e di Output*

###<a id="MXF_to_MP4_streams"></a>Verifica di flussi di elementi multimediali

Spesso sono desiderata per il flusso di aspetto simile a quella flussi tramite il flusso di lavoro. Per esaminare un flusso in qualsiasi punto del flusso di lavoro, basta fare clic sull'output o pin di input in uno qualsiasi dei componenti. In questo caso, provare a fare clic su pin di output Video non compressi da Input File multimediali. Verrà aperta una finestra di dialogo che consente di controllare il video in uscita.

![Verifica pin di output Video non compresso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-inspecting-uncompressed-video-output.png)

*Verifica pin di output Video non compresso*

In questo caso, indica, ad esempio che è stiamo la gestione di un input 1920x1080 24 frame al secondo 4:2:2 campioni per un video di quasi 2 minuti.

###<a id="MXF_to_MP4_file_generation"></a>Aggiunta di un video codificatore per. Generazione di un file MP4

Si noti che a questo punto, un Video non compresso e più pin di output Audio non compresso sono disponibili per l'utilizzo in Input File multimediali. Per la codifica del video in entrata, è necessario un componente codifica - in questo caso per la generazione. File MP4.

Per codificare video all'h. 264, aggiungere il componente del codificatore Video AVC nell'area di progettazione. Questo componente accetta un flusso di video uncompress come input e offre un flusso di video compresso AVC sul proprio pin di output.

![Codificatore AVC non connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-avc-encoder.png)

*Codificatore AVC non connesso*

Le proprietà determinano la codifica esattamente come si verifica in. Di seguito sono descritte alcune delle impostazioni più importanti:

- Output larghezza e altezza di Output: consentono di determinare la risoluzione del video codificato. In questo caso passiamo con 640x360
- Frequenza: quando è impostato su pass-through sufficiente adotterà la frequenza di origine, è possibile ignorare questa attraverso. Si noti che la conversione di frequenza fotogrammi non è animazione-indennizzo.
- Profilo e livello: consentono di determinare il profilo AVC e il livello. Per ottenere facilmente ulteriori informazioni sui diversi livelli e profili, fare clic sull'icona di punto interrogativo sul componente AVC Video Encoder e la pagina Guida Mostra informazioni più dettagliate su ciascuno dei livelli. Per il campione passiamo con profilo principale a livello di 3,2 (impostazione predefinita).
- Valutare la modalità di controllo e frequenza bit (kbps): in questo scenario si scelga una costante velocità bit (CBR) output Kbps 1200
- Formato video: questa operazione consiste VUI (Video usabilità informazioni) scritte all'interno del flusso di h. 264 (lato le informazioni che potrebbero essere usato da un decodificatore per migliorare la visualizzazione ma non essenziale decodificare correttamente):
- NTSC (tipica per Giappone utilizzando 30 f/s o degli Stati Uniti)
- PAL (tipica per Europa, utilizzando 25fps)
- GOP dimensioni modalità: è necessario configurare GOP fisse ai fini con un intervallo di chiave di 2 secondi con GOP chiuso. In questo modo la compatibilità con i servizi di supporto Azure imballaggio dinamico fornisce.

Per feed il codificatore AVC, connettere il pin di output Video non compresso dal componente Media File Input al pin di input Video non compresso dal codificatore AVC.

![Codificatore AVC connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-avc-encoder.png)

*Connesso codificatore AVC principali*

###<a id="MXF_to_MP4_audio"></a>La codifica del flusso audio

A questo punto è abbiamo codificata video, ma l'audio non compresso originale deve essere compresso. Per questo è occorre accedere con AAC codifica dal componente codificatore AAC (Dolby). Aggiungere al flusso di lavoro.

![Codificatore AVC non connesso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-unconnected-aac-encoder.png)

*Codificatore AAC non connesso*

A questo punto è presente un problemi di compatibilità: esiste solo un singolo non compresso input pin audio dal codificatore AAC mentre molto probabilmente l'Input di File multimediali avrà due diversi non compressa flusso audio disponibili: uno per il canale audio sinistro e uno per destra. (Se si utilizzano Borda suoni agli eventi, che è 6 canali) Non è quindi possibile connettersi direttamente dal sito di origine di Input di File multimediali l'audio nell'Encoder audio AAC. Il componente AAC prevede un flusso audio "interfoliazione" cosiddetto: un solo flusso che contiene a sinistra e i canali destro alternati tra loro. Se si conosce il file multimediale origine quali audio sono la posizione dell'origine, possiamo quindi generare tali interfoliazione flusso audio con l'altoparlante correttamente assegnate e le posizioni a sinistra e destra.

Prima di tutto una sarà possibile generato un flusso di interfoliazione dai canali audio sorgente necessari. Il componente Interleaver flusso Audio gestirà questo per noi. Aggiungere al flusso di lavoro e connettere l'output audio dall'Input di File multimediali al suo interno.

![Connessione Interleaver flusso Audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-audio-stream-interleaver.png)

*Connessione Interleaver flusso Audio*

Ora che si dispone di un flusso audio interfoliazione, è comunque non specificare dove assegnare le posizioni a sinistra o destra del relatore da. Per specificare queste informazioni è possibile sfruttare chi assegna posizione del relatore.

![Aggiunta di un chi assegna posizione del relatore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-speaker-position-assigner.png)

*Aggiunta di un chi assegna posizione del relatore*

Configurare chi assegna posizione del relatore per l'utilizzo con un flusso di input stereo tramite un filtro preimpostato Encoder del canale predefinito denominato "2.0 (L, R)" e "Personalizzati". (Questa assegna la posizione sinistra del relatore sul canale 1 e la posizione dell'altoparlante destro canale 2.)

Collegare l'output di chi assegna posizione del relatore per l'input di codificatore AAC. Quindi, informare il codificatore AAC per l'uso con un "2.0 (L, R)" canale predefinito, in modo che è in grado di gestire l'audio stereo come input.

###<a id="MXF_to_MP4_audio_and_fideo"></a>Multiplexing flussi Audio e Video in un contenitore MP4

Dato il nostro AVC flusso video codificato e il nostro AAC codificato flusso audio, possiamo ridurre entrambi in un. Contenitore MP4. Il processo di combinazione di diversi flussi in un unico è denominato "multiplexing" (o "muxing"). In questo caso è stiamo sovrapposizione l'audio e il video in un unico coerente. Pacchetto MP4. Il componente che le coordinate per un. MP4 contenitore viene definito il Multiplexer ISO MPEG-4. Aggiungere una nell'area di progettazione e connettere gli input AVC Video Encoder e il codificatore AAC.

![MPEG-4 connesso multiplatore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-mpeg4-multiplexer.png)

*MPEG-4 connesso multiplatore*

###<a id="MXF_to_MP4_writing_mp4"></a>Scrivere il file MP4

Quando si scrive un file di output, viene utilizzato il componente di Output del File. È possibile connettersi all'output del Multiplexer ISO MPEG-4 in modo che l'output viene scritto su disco. A tale scopo, connettere il pin di output contenitore (MPEG-4) al pin di input scrittura l'Output del File.

![Connessione File Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connected-file-output.png)

*Connessione File Output*

Il nome del file che verrà utilizzato viene determinato mediante la proprietà del File. Tale proprietà può essere hardcoded a un determinato valore, probabilmente una sarà necessario impostarla mediante un'espressione.

Disporre il flusso di lavoro di determinare automaticamente l'output della proprietà name da un'espressione di File, scegliere il pulsante accanto al nome di File (accanto all'icona di cartella). Menu a discesa selezionare "Espressione". Verrà visualizzata l'editor di espressioni. Cancellare il contenuto dell'editor prima di tutto.

![Editor espressioni vuota](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-empty-expression-editor.png)

*Editor espressioni vuota*

L'editor di espressioni consente di immettere un qualsiasi valore letterale, con una o più variabili. Variabili iniziano con un segno di dollaro. Come si premere il tasto $, l'editor visualizzerà una casella di elenco a discesa con una scelta delle variabili disponibili. In questo caso si userà una combinazione della variabile di output directory e la variabile di nome di file di input di base:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}.MP4

![Compilato all'Editor di espressioni](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-expression-editor.png)

*Compilato all'Editor di espressioni*

>[AZURE.NOTE]Per vedere un file di output del processo di codifica in Azure, è necessario specificare un valore nell'editor di espressioni. 

Quando si conferma l'espressione premendo ok, verrà visualizzate in anteprima la finestra delle proprietà per il valore in questo momento risolve di proprietà File.

![File espressione restituisce output dir](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-file-expression-resolves-output-dir.png)

*File espressione restituisce output dir*

###<a id="MXF_to_MP4_asset_from_output"></a>Creazione di una risorsa di servizi multimediale dal file di output

Mentre abbiamo prodotto un file di output MP4, è necessario indicare che appartiene il file per la risorsa di output che servizi multimediali di genererà conseguente all'esecuzione del flusso di lavoro. A tale scopo, viene utilizzato il nodo Output File/risorse sull'area di lavoro del flusso di lavoro. Tutti i file in arrivo in questo nodo renderà parte del bene servizi multimediali di Windows Azure risultante.

Connettere il componente di File di Output per il componente di Output File/risorse per completare il flusso di lavoro.

![Flusso di lavoro completato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow.png)

*Flusso di lavoro completato*

###<a id="MXF_to_MP4_test"></a>Verificare di aver flusso di lavoro in locale

Per testare il flusso di lavoro in locale, fare clic sul pulsante Riproduci nella barra degli strumenti nella parte superiore di seguito. Al termine il flusso di lavoro in esecuzione, esaminare l'output generato nella cartella di output configurato. Verrà visualizzato il file di output MP4 termine che codificato dal file di origine di input MXF.

##<a id="MXF_to_MP4_with_dyn_packaging"></a>Codifica MXF in MP4 - multibitrate imballaggio dinamico attivato

In questa procedura è necessario creare un set di più file MP4 velocità con AAC con codificata audio da un'unica. File di input MXF.

Quando un output di risorse con più velocità desiderato per l'utilizzo in combinazione con le caratteristiche di imballaggio dinamico offerte dai servizi multimediali di Windows Azure, più file MP4 allineate GOP di ogni una velocità diversa e risoluzione devono essere generati. A tale scopo, la procedura dettagliata [Codifica MXF in una singola velocità MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4) ci offre un valido punto di partenza.

![Avvio del flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow.png)

*Avvio del flusso di lavoro*

###<a id="MXF_to_MP4_with_dyn_packaging_more_outputs"></a>Aggiunta di uno o più risultati MP4 aggiuntivi

Tutti i file MP4 nostro bene servizi multimediali di Windows Azure risultante supporterà una diversa velocità e la risoluzione. Aggiungere uno o più file di output MP4 al flusso di lavoro.

Per assicurarsi che abbiamo il nostro Encoder video creato con le stesse impostazioni, risulta più pratico duplicare il codificatore di Video AVC già esistente e configurare un'altra combinazione di risoluzione e velocità (aggiungere uno dei 960 x 540 25 frame al secondo 2,5 Mbps). Per duplicare il codificatore esistente, copia incollarla nell'area di progettazione.

Collegare il nuovo componente di AVC pin di output Video non compresso di Input di File multimediali.

![Secondo codificatore AVC connessi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-avc-encoder-connected.png)

*Secondo codificatore AVC connessi*

A questo punto adattare la configurazione per il nuovo codificatore AVC generare l'output 960 x 540 Mbps 2,5. (Utilizzare la proprietà "Output larghezza", "Output altezza" e "Frequenza bit (kbps)" per l'oggetto).

Dato si desidera utilizzare bene risultante insieme imballaggio dinamico servizi multimediali di Windows Azure, l'endpoint di trasmissione deve essere in grado di generare da questi file MP4 frammenti HLS/Fragmented. MP4/trattino esattamente allineati tra loro in modo che i client di passaggio tra diversi bitrate ottengano un'esperienza audio e video continuo smussata singola. Per verificare che si verificano, è necessario assicurarsi che, in proprietà entrambi codificatore AVC GOP ("gruppo di immagini") di dimensioni per entrambi i file MP4 sia impostata su 2 secondi, possono essere eseguite da:

- impostare la modalità di ridimensionamento GOP dimensioni fisse GOP e
- Intervallo di Frame chiave da due secondi.
- inoltre configurare il controllo IDR GOP chiuso GOP per assicurarsi che tutti GOP permanente sulle proprie senza dipendenze

Per il flusso di lavoro con facilità a comprendere, rinominare il codificatore AVC prima di "AVC Video Encoder 640x360 1200kbps" e il secondo codificatore AVC "AVC Video Encoder 960 x 540 kbps 2500".

Aggiungere un secondo ISO MPEG-4 Multiplexer e una seconda Output del File. Connettere il multiplexer al nuovo codificatore AVC e verificare che l'output viene indicato nell'Output di File. Connettersi anche input AAC codifica audio output al nuovo del multiplexer. L'Output del File, a sua volta può quindi essere connesso a del nodo di File di Output/risorse da aggiungere al bene servizi multimediali che verrà creato.

![Collegare Muxer secondo e il File di Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-second-muxer-file-output-connected.png)

*Collegare Muxer secondo e il File di Output*

Per la compatibilità con imballaggio dinamico servizi multimediali di Windows Azure, configurare la modalità di blocco del multiplexer a GOP conteggio o la durata e impostare GOP per singolo blocco a 1. (Dovrebbe essere l'impostazione predefinita).

![Modalità di blocco Muxer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-muxer-chunk-modes.png)

*Modalità di blocco Muxer*

Nota: può essere necessario ripetere questa procedura per tutte le altre velocità e la risoluzione combinazioni che si desidera aggiungere all'output della risorsa.

###<a id="MXF_to_MP4_with_dyn_packaging_conf_output_names"></a>Configurare i nomi di file output

Include più di un singolo file aggiunto al bene output. In questo modo la necessità di verificare che i nomi di file per ognuno dei file di output sono diversi tra loro e forse anche applicare una convenzione di denominazione dei file in modo che risulta evidente dal nome del file si vuole eseguire.

Nomi di file output può essere controllato tramite espressioni nella finestra di progettazione. Aprire il riquadro di proprietà per uno dei componenti di Output di File e aprire l'editor per la proprietà del File. Il primo file di output è stato configurato tramite l'espressione seguente (vedere l'esercitazione per passare da [MXF a una singola velocità output MP4](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4)):

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}.MP4

Questo errore indica che il nome del file dipendono da due variabili: directory di output per scrivere in e il nome di base di file di origine. Il primo è esposto come proprietà nella radice del flusso di lavoro e quest'ultimo varia a seconda del file in arrivo. Si noti che la directory di output utilizzare per il testing locale; Questa proprietà verrà ignorata dal motore di flusso di lavoro quando viene eseguito il flusso di lavoro dal processore basato su cloud media in servizi multimediali di Windows Azure.
Per concedere a entrambi i file di output di denominazione coerenti output, modificare il primo file per la denominazione espressione:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

e la seconda per:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_960x540_2.MP4

Eseguire un test intermedio eseguito per verificare che entrambi i file di output MP4 generati correttamente.

###<a id="MXF_to_MP4_with_dyn_packaging_audio_tracks"></a>Aggiunta di un brano separato

Come si vedrà in seguito quando si genera un file ISM da allegare i file di output MP4, è anche richiederà un file di MP4 solo audio come traccia audio per la trasmissione adattata. Per creare il file, aggiungere un muxer aggiuntive per il flusso di lavoro (Multiplexer ISO-MPEG-4) e connettere il pin di output del codificatore AAC con il proprio pin input per tenere traccia 1.

![Audio Muxer aggiunti](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-added.png)

*Audio Muxer aggiunti*

Creare una terza componente di File di Output per generare l'output di flusso in uscita dalla muxer e configurare il file per la denominazione espressione come:
    
    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_128kbps_audio.MP4

![Audio Muxer la creazione di File di Output](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-audio-muxer-creating-file-output.png)

*Audio Muxer la creazione di File di Output*

###<a id="MXF_to_MP4_with_dyn_packaging_ism_file"></a>Aggiunta di. File SMIL ISM

Per imballaggio dinamico per l'uso in combinazione con MP4 file (e MP4 solo audio) il nostro bene servizi multimediali, occorre anche un file manifesto (denominato anche un file "SMIL": sincronizzati multimediali Integration Language). Questo file indica a servizi multimediali di Windows Azure quali file MP4 sono disponibili per imballaggio dinamico e su quelli da prendere in considerazione per lo streaming audio. Un file manifesto tipico per un set del MP4 con un solo flusso audio è simile alla seguente:
    
    <?xml version="1.0" encoding="utf-8" standalone="yes"?>
    <smil xmlns="http://www.w3.org/2001/SMIL20/Language">
      <head>
        <meta name="formats" content="mp4" />
      </head>
      <body>
        <switch>
          <video src="H264_1900kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_1300kbps_AAC_und_ch2_96kbps.mp4" />
          <video src="H264_900kbps_AAC_und_ch2_96kbps.mp4" />
          <audio src="AAC_ch2_96kbps.mp4" title="AAC_und_ch2_96kbps" />
        </switch>
      </body>
    </smil>

Il file ISM contiene all'interno di un'istruzione switch, un riferimento a ciascuno dei singoli file video MP4 e oltre a questi riferimenti di file audio anche (più) in un MP4 che contiene solo l'audio.

Generare il file manifesto per l'insieme del MP4 può essere eseguita tramite un componente denominato "AMS manifesto Writer". Per utilizzarla, trascinare nell'area di e connettersi "Scrivere completa" PIN di output da tre componenti di File di Output per l'input AMS manifesto Writer. Assicurarsi quindi di collegare l'output del AMS manifesto Writer di Output File/risorse.

Come con il nostro altri file componenti di output, configurare il nome di output ISM file con un'espressione:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_manifest.ism

Il flusso di lavoro completato l'aspetto di seguito:

![Termine MXF multibitrate MP4 flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-mxf-to-multibitrate-mp4-workflow.png)

*Termine MXF multibitrate MP4 flusso di lavoro*

##<a id="MXF_to__multibitrate_MP4"></a>Codifica MXF in multibitrate MP4 - progetto avanzata

Nella [procedura dettagliata del flusso di lavoro precedente](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging) abbiamo visto come una singola risorsa input MXF può essere convertita in una risorsa di output con file MP4 multi-velocità, estensione MP4 solo audio e un file manifesto per l'utilizzo in combinazione con imballaggio dinamico servizi multimediali di Windows Azure.

Questa procedura illustra come alcuni degli aspetti possono essere migliorate e apportate più comodo.

###<a id="MXF_to_multibitrate_MP4_overview"></a>Panoramica del flusso di lavoro per migliorare

![Flusso di lavoro Multibitrate MP4 a migliorare](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-enhance.png)

*Flusso di lavoro Multibitrate MP4 a migliorare*

###<a id="MXF_to__multibitrate_MP4_file_naming"></a>Convenzioni di denominazione dei file

Il flusso di lavoro precedente sono state specificate un'espressione semplice come base per la generazione di nomi di file di output. Abbiamo alcune duplicazione attraverso: tutti gli i componenti di file di output singoli specificato tale espressione.

Ad esempio, il componente di output di file per il primo file video è configurato con questa espressione:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_640x360_1.MP4

Mentre per l'output della seconda video, è possibile utilizzare un'espressione come:

    ${ROOT_outputWriteDirectory}\\${ROOT_sourceFileBaseName}_960x540_2.MP4

Non prevede più pulito, meno errore soggetto e più comodo se è possibile rimuovere alcuni la duplicazione e rendere maggiormente configurabile invece? Per fortuna siamo in grado di: funzionalità di espressione della finestra di progettazione in combinazione con la possibilità di creare proprietà personalizzate nel nostro radice del flusso di lavoro ci offre un livello aggiuntivo di comodità.

Si supponga che nomefile configurazione è necessario unità da bitrate dei singoli file MP4. Questi bitrate che siamo per configurare in un'unica posizione centrale (nella radice del grafico), da dove sarà possibile accedervi per configurare e generazione del nome di file di unità. A tale scopo, iniziamo pubblicando proprietà velocità da entrambe Encoder AVC nella radice del flusso di lavoro, in modo che diventa accessibile dalla entrambi radice anche a partire dal Encoder AVC. (Anche se è visualizzata in due diversi punti esiste un solo valore sottostante.)

###<a id="MXF_to__multibitrate_MP4_publishing"></a>Proprietà componente nella radice del flusso di lavoro di pubblicazione

Aprire il primo codificatore AVC, passare alla proprietà frequenza bit (kbps) e nell'elenco a discesa scegliere pubblica.

![La proprietà di velocità di pubblicazione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-bitrate-property.png)

*La proprietà di velocità di pubblicazione*

Configurare la finestra di dialogo Pubblica su cui pubblicare la radice del grafico del flusso di lavoro con un nome pubblicato di "video1bitrate" e un nome visualizzato leggibile "Velocità di trasmissione Video 1". Configurare una personalizzata nome del gruppo denominato "Streaming bitrate" e scegliere pubblica.

![La proprietà di velocità di pubblicazione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-bitrate-property.png)

*Finestra di dialogo pubblicazione per la proprietà velocità*

Ripetere la stessa per la proprietà di velocità del secondo codificatore AVC e denominarla "video2bitrate" con un nome visualizzato "Video 2 velocità", nello stesso gruppo personalizzato "Streaming bitrate".

Se è ora esaminare le proprietà di primo livello del flusso di lavoro, vedremo il gruppo personalizzato con due proprietà pubblicato visualizzate. Entrambe riflettere il valore della loro rispettive velocità codificatore AVC.

![Proprietà velocità pubblicati nella radice del flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-bitrate-props-on-workflow-root.png)

Ogni volta che si desidera accedere a queste proprietà codice o da un'espressione, è possibile farlo in questo:

- dal codice all'interno del testo da un componente immediatamente sotto la radice: node.getPropertyAsString('.. / video1bitrate', null)
- all'interno di un'espressione: ${ROOT_video1bitrate}
 
Ora è necessario completare il gruppo "Streaming bitrate" pubblicando anche la velocità di traccia audio su di esso. All'interno delle proprietà del codificatore AAC, cercare l'impostazione di velocità e selezionare pubblica nell'elenco a discesa accanto a. Pubblicare la radice del grafico con nome "audio1bitrate" e visualizzare nome "Audio 1 velocità" tra il gruppo personalizzato "Streaming bitrate".

![Finestra di dialogo pubblicazione per velocità audio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publishing-dialog-for-audio-bitrate.png)

*Finestra di dialogo pubblicazione per velocità audio*

![Proprietà video e audio risultante nella radice](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-resulting-video-and-audio-props-on-root.png)

*Proprietà video e audio risultante nella radice*

Si noti che se si modifica uno di questi tre valori anche nuovamente Configura e i valori nei rispettivi componenti sono collegate con (e dove pubblicati da).

###<a id="MXF_to__multibitrate_MP4_output_files"></a>Hanno generato il file di output nomi si basano sui valori di proprietà pubblicati

Anziché hardcoded ai nomi di file generato, è possibile ora modificare l'espressione nomefile ciascuno dei componenti di File di Output per utilizzare le proprietà di velocità che è appena pubblicati nella radice grafico. Inizia con il primo file di output, individuare la proprietà di File e modificare l'espressione seguente:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video1bitrate}kbps.MP4

I parametri diversi in questa espressione accessibili e immesso premendo il segno di dollaro sulla tastiera nella finestra di espressione. Uno dei parametri disponibili è la proprietà video1bitrate, pubblicati in precedenza.

![Accesso ai parametri all'interno di un'espressione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-accessing-parameters-within-an-expression.png)

*Accesso ai parametri all'interno di un'espressione*

Eseguire la stessa operazione per il file di output per il secondo video:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_video2bitrate}kbps.MP4

e per l'output di file di solo audio:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_${ROOT_audio1bitrate}bps_audio.MP4

Se si modificano la velocità di uno dei file audio o video, da riconfigurare il codificatore rispettivo e la convenzione di denominazione di file basato su velocità verrà utilizzato il valore completamente automatico.

##<a id="thumbnails_to__multibitrate_MP4"></a>Aggiunta di anteprime a multibitrate output MP4

A partire da un flusso di lavoro che genera [un output MP4 da un MXF multibitrate di input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ora esamineremo in aggiunta anteprime per l'output.

###<a id="thumbnails_to__multibitrate_MP4_overview"></a>Panoramica del flusso di lavoro per aggiungere le anteprime a

![Flusso di lavoro Multibitrate MP4 iniziare da](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-multibitrate-mp4-workflow-to-start-from.png)

*Flusso di lavoro Multibitrate MP4 iniziare da*

###<a id="thumbnails_to__multibitrate_MP4__with_jpg"></a>Aggiunta di codifica JPG

Cuore di nostro generazione anteprima sarà componente codificatore JPG, grado generare l'output file JPG.

![Codificatore JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-jpg-encoder.png)

*Codificatore JPG*

È tuttavia direttamente non è possibile collegare il nostro flusso Video non compresso dall'Input di File multimediali codificatore JPG. Se, tuttavia, è previsto che vengano passati singoli frame. Questa operazione, possiamo fare tramite il componente porta fotogramma Video.

![La connessione di una verifica frame al codificatore JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-frame-gate-to-jpg-encoder.png)

*La connessione di una verifica frame al codificatore JPG*

Porta frame ogni così tanti secondi o frame consente a un fotogramma video passare. L'intervallo e l'ora offset con cui si verifica questo evento può essere configurato nelle proprietà.

![Proprietà porta fotogramma video](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-video-frame-gate-properties.png)

*Proprietà porta fotogramma video*

Creare una miniatura minuto impostando la modalità di tempo (secondi) e l'intervallo di 60.

###<a id="thumbnails_to__multibitrate_MP4_color_space"></a>Relative alla conversione spazio colore

Mentre sembra logico che entrambi PIN Video non compresso di porta frame e l'Input di File multimediali può essere connesso, si otterrebbe un avviso se si preferisce eseguire questa operazione.

![Errore di spazio di colore di input](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-input-color-space-error.png)

*Errore di spazio di colore di input*

In questo modo la quali colori rappresentazione delle informazioni in nostro originale non elaborato non compresso flusso video, provenienti da nostro MXF è diverso da cosa si aspetta codificatore JPG. In particolare, una cosiddetta "spazio colore" di "RGB" o "Gradazioni di grigio" è previsto che scorrono. Questo significa che sarà necessario la conversione prima applicata relativo a un spazio colore flusso video in ingresso di porta fotogramma Video.

Trascinare il flusso di lavoro il convertitore di spazio di colore - Intel e connetterlo al nostro porta cornice.

![La connessione di un problema di spazio di colore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-connect-color-space-convertor.png)

*La connessione di un problema di spazio di colore*

Nella finestra delle proprietà, selezionare la voce BGR 24 dall'elenco predefinito.

###<a id="thumbnails_to__multibitrate_MP4_writing_thumbnails"></a>Scrivere le anteprime

Diverso da nel video MP4, il componente del codificatore JPG sarà più di un file. Per risolvere il problema, è possibile utilizzare un componente di Writer di File JPG Ricerca scena: verrà eseguire le anteprime JPG in ingresso e in cui vengono scritte, ogni nome di file viene preceduto da un numero diverso. (In genere che indica il numero di secondi/unità nel flusso di cui l'anteprima quelle disegnata dal numero.)


![Introduzione a Writer di File di scena ricerca JPG](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer.png)

*Introduzione a Writer di File di scena ricerca JPG*

Configurare le proprietà Percorso cartella di Output con l'espressione: ${ROOT_outputWriteDirectory} 

e la proprietà Filename prefisso con: 

    ${ROOT_sourceFileBaseName}_thumb_

Il prefisso determinerà come file di anteprima sono vengano denominati. Si verrà aggiunto un suffisso con un numero che indica la posizione del cursore all'interno del flusso.


![Proprietà Writer di File JPG Ricerca scena](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scene-search-jpg-file-writer-properties.png)

*Proprietà Writer di File JPG Ricerca scena*

Connettere il nodo File di Output/bene il Writer di File JPG Ricerca scena.

###<a id="thumbnails_to__multibitrate_MP4_errors"></a>Rilevamento degli errori in un flusso di lavoro

Connettere l'input del convertitore di spazio di colore per l'output video non compresso non elaborato. Eseguire un test locale eseguito per il flusso di lavoro. È probabile il flusso di lavoro verrà improvvisamente interruzione dell'esecuzione e indicare con un contorno rosso sul componente che si è verificato un errore:

![Errore di conversione spazio colore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error.png)

*Errore di conversione spazio colore*

Selezionare l'icona "E" rosso piccola in alto a destra nell'angolo del componente convertitore di spazio di colori per vedere che cos'è il motivo per il tentativo di codifica non è riuscita.

![Finestra di dialogo Errore convertitore spazio colore](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-color-space-converter-error-dialog.png)

*Finestra di dialogo Errore convertitore spazio colore*

Si scopre, come si può notare, che deve essere rec601 per la conversione richiesta di YUV in RGB in arrivo spazio di colori standard per il convertitore di spazio di colore. Apparentemente del flusso non indica il rec601. (Registrazione 601 è uno standard di codifica interlacciati segnali video analogici in formato video digitale. Consente di specificare un'area attiva dettagliata relativa agli 720 campioni di luminosità e 360 campioni di crominanza per riga. Il colore di sistema di codifica dei è noto come allineamento 4:2:2.)

Per risolvere il problema, si verrà indica sui metadati di nostro flusso è stiamo sulla gestione del contenuto rec601. Per eseguire questa operazione si userà un componente di aggiornamento di tipo di dati Video, è necessario inserire tra l'origine non elaborati e il componente di conversione spazio colore. L'aggiornamento di tipo di dati consente l'aggiornamento manuale di alcuni dati video le proprietà di tipo. Configurarlo per indicare un spazio colori Standard di "Registrazione 601". In questo modo il programma di aggiornamento tipo di dati di Video aggiungere tag di flusso con lo spazio di colore "Registrazione 601" Se non è stata ancora definita nessuno spazio tra i colori. (Non sostituiscono i metadati esistenti, a meno che non è stata selezionata la casella di controllo Sostituisci.)

![Aggiornamento di colori Standard di spazio sul programma di aggiornamento di tipo di dati](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-update-color-space-standard-on-data-type.png)

*Aggiornamento di colori Standard di spazio sul programma di aggiornamento di tipo di dati*

###<a id="thumbnails_to__multibitrate_MP4_finish"></a>Flusso di lavoro completato

Ora che il nostro flusso di lavoro è terminato, eseguire un altro test Esegui per visualizzarlo passare.

![Termine flusso di lavoro per l'output multi-mp4 con le anteprime delle](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-for-multi-mp4-thumbnails.png)

*Termine flusso di lavoro per l'output multi-mp4 con le anteprime delle*

##<a id="time_based_trim"></a>Basate sul tempo limitazione per motivi di output multibitrate MP4

A partire da un flusso di lavoro che genera [un output MP4 da un MXF multibitrate di input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ora esamineremo in tagliare il video di origine in base a un timestamp.

###<a id="time_based_trim_start"></a>Panoramica del flusso di lavoro per iniziare ad aggiungere limitazione per motivi di

![Avvio del flusso di lavoro per aggiungere la rimozione di a](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-starting-workflow-to-add-trimming.png)

*Avvio del flusso di lavoro per aggiungere la rimozione di a*

###<a id="time_based_trim_use_stream_trimmer"></a>Utilizzando il compensatore di flusso

Il componente flusso Trimmer consente tagliare l'inizio e fine di un flusso di input in intervalli informazioni (secondi, minuti,...) base. Il compensatore non supporta la rimozione di basate su frame.

![Trimmer di flusso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-stream-trimmer.png)

*Trimmer di flusso*

Anziché il collegamento di Encoder AVC chi assegna posizione del relatore per l'Input di File multimediali direttamente, è necessario inserire tra quelli trimmer flusso. (Una per il segnale video e l'altra per il segnale audio interfoliazione.)

![Inserire tra Trimmer di flusso](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-put-stream-trimmer-in-between.png)

*Inserire tra Trimmer di flusso*

Configurare il compensatore di seguito in modo che Microsoft elaborerà solo video e audio tra 15 secondi e 60 secondi nel video.

Passare alle proprietà del Trimmer di flusso Video e configurare ora di inizio (15s) e le proprietà di data/ora fine (60s). Per assicurarsi che sia il nostro trimmer audio e video vengono sempre configurate per le stesse ore di inizio e i valori di fine, verrà pubblicato quelli nella radice del flusso di lavoro.

![Pubblicare proprietà ora inizio dal flusso Trimmer](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-start-time-from-stream-trimmer.png)

*Pubblicare proprietà ora inizio dal flusso Trimmer*

![Pubblicare finestra di dialogo proprietà per ora di inizio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-start-time.png)

*Pubblicare finestra di dialogo proprietà per ora di inizio*

![Pubblicare finestra di dialogo proprietà per data/ora fine](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-publish-dialog-for-end-time.png)

*Pubblicare finestra di dialogo proprietà per data/ora fine*


Se è ora controllare nella cartella radice del flusso di lavoro, entrambe le proprietà sarà visualizzato e configurabili preciso da tale posizione.

![Pubblicato le proprietà disponibili nella radice](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-published-properties-available-on-root.png)

*Pubblicato le proprietà disponibili nella radice*

Aprire le proprietà di limitazione per motivi di trimmer audio e configurare ora di inizio e fine di un'espressione che fa riferimento a proprietà pubblicate nella radice del nostro flusso di lavoro.

Per l'audio pulizia ora di inizio:

    ${ROOT_TrimmingStartTime}

e per l'ora di fine:

    ${ROOT_TrimmingEndTime}

###<a id="time_based_trim_finish"></a>Flusso di lavoro completato

![Flusso di lavoro completato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-finished-workflow-time-base-trimming.png)

*Flusso di lavoro completato*


##<a id="scripting"></a>Introduzione al componente script

Componenti script possono essere eseguiti gli script non autorizzati durante le fasi di esecuzione del flusso di lavoro. Sono disponibili quattro diversi script che possono essere eseguite, ognuna con caratteristiche specifiche e la sua posizione nel ciclo di vita del flusso di lavoro:

- **commandScript**
- **realizeScript**
- **processInputScript**
- **lifeCycleScript**

La documentazione del componente script passa in modo più dettagliato per ogni le risposte sopra. Nella [sezione seguente](media-services-media-encoder-premium-workflow-tutorials.md#frame_based_trim), il componente di scripting **realizeScript** viene utilizzato per creare un file xml di cliplist in tempo reale quando viene avviato il flusso di lavoro. Questo script viene chiamato durante l'installazione di componenti, accade solo una volta nel relativo ciclo di vita.


###<a id="scripting_hello_world"></a>Gli script all'interno di un flusso di lavoro: world hello

Trascinare un componente di script nell'area di progettazione e rinominarla (ad esempio, "SetClipListXML").

![Aggiunta di un componente script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Aggiunta di un componente script*

Quando si analizzano le proprietà del componente script, i quattro tipi diversi script sarà indicato, ogni configurabili in modo da uno script diverso.

![Proprietà componente script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Proprietà componente script*

Deselezionare la processInputScript e aprire l'editor per la realizeScript. È ora stiamo installati e pronto per avviare la creazione di script.

Gli script sono scritti in Groovy, un linguaggio di script compilato in modo dinamico per la piattaforma Java che mantiene la compatibilità con linguaggio. In realtà, la maggior parte dei codice Java sia codice Groovy valido.

Di seguito scrivere uno script groovy world semplice hello nel contesto di nostro realizeScript. Nell'editor, è necessario immettere quanto segue:

    node.log("hello world");

Eseguire un'esecuzione di test locale. Dopo l'esecuzione, controllare (tramite la scheda di sistema per il componente script) la proprietà log.

![Output di Hello world log](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output.png)

*Output di Hello world log*

L'oggetto nodo si chiama il metodo di log, si intende il nostro "nodo" corrente o il componente è stiamo scripting all'interno. Ogni componente come tale è in grado di output di registrazione di dati, disponibile tramite la scheda di sistema. In questo caso, vengono visualizzate la stringa letterale "hello world". Importante tenere presente Ecco che questo può rivelarsi uno strumento di debug estremamente utile, che forniscono informazioni su che in realtà eseguendo lo script.

Dal nostro ambiente di scripting, è inoltre avere accesso alle proprietà su altri componenti. Provare questa procedura:


    //inspect current node: 
    def nodepath = node.getNodePath(); 
    node.log("this node path: " + nodepath);
    
    //walking up to other nodes: 
    def parentnode = node.getParentNode(); 
    def parentnodepath = parentnode.getNodePath(); 
    node.log("parent node path: " + parentnodepath);
    
    //read properties from a node: 
    def sourceFileExt = parentnode.getPropertyAsString( "sourceFileExtension", null ); 
    def sourceFileName = parentnode.getPropertyAsString("sourceFileBaseName", null); 
    node.log("source file name with extension " + sourceFileExt + " is: " + sourceFileName);

La finestra log mostrerà Contattaci le operazioni seguenti:

![Registrare l'output per l'accesso a percorsi dei nodi](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-output2.png)

*Registrare l'output per l'accesso a percorsi dei nodi*


##<a id="frame_based_trim"></a>Basate su frame limitazione per motivi di output multibitrate MP4

A partire da un flusso di lavoro che genera [un output MP4 da un MXF multibitrate di input](media-services-media-encoder-premium-workflow-tutorials.md#MXF_to_MP4_with_dyn_packaging), ora esamineremo in tagliare il video di origine in base a conteggi dei frame.

###<a id="frame_based_trim_start"></a>Introduzione ai progetti per iniziare ad aggiungere limitazione per motivi di

![Flusso di lavoro per iniziare ad aggiungere limitazione per motivi di](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-workflow-start-adding-trimming-to.png)

*Flusso di lavoro per iniziare ad aggiungere limitazione per motivi di*

###<a id="frame_based_trim_clip_list"></a>Usare l'elenco dei Clip XML

In tutte le esercitazioni del flusso di lavoro precedente, è stato usato il componente Input di File multimediali come l'origine di input video. Per questo scenario specifico tuttavia, si inizierà a usare il componente origine elenco Clip invece. Si noti che non deve essere il modo migliore di lavorare; utilizzare solo l'origine dell'elenco di Clip quando c'è ragione per farlo (ad esempio nel sotto i casi in cui stiamo facendo uso delle funzionalità di limitazione per motivi dell'elenco di clip).

Per passare dalla Input File multimediali per l'origine dell'elenco di Clip, trascinare il componente origine elenco Clip nella finestra di progettazione e connettere il pin Clip elenco XML per il nodo Clip elenco XML di progettazione flussi di lavoro. Questa operazione deve popolare l'origine dell'elenco di Clip con pin di output, in base a input video. Connettere il PIN non compressa Video e Audio non compressi dall'origine dell'elenco di Clip ai rispettivi AVC Encoder e Interleaver flusso Audio. A questo punto, rimuovere l'Input di File multimediali.

![Sostituire l'Input di File multimediali con l'origine dell'elenco di Clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-replaced-media-file-with-clip-source.png)

*Sostituire l'Input di File multimediali con l'origine dell'elenco di Clip*

Il componente origine elenco Clip accetta come input un "XML elenco Clip". Quando si seleziona il file di origine di test per in locale, questo codice xml elenco clip è compilato automaticamente in base dell'utente.

![Proprietà Clip elenco XML compilato automaticamente in base](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-auto-populated-clip-list-xml-property.png)

*Proprietà Clip elenco XML compilato automaticamente in base*

Osservando un po' più da vicino al codice xml, ecco come appare come:

![Finestra di dialogo elenco clip modifica](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-edit-clip-list-dialog.png)

*Finestra di dialogo elenco clip modifica*

Questo tuttavia non riflettere le funzionalità del clip elenco xml. Abbiamo è possibile aggiungere un elemento "Taglio" in entrambi i video e audio origine, ad esempio seguente:

![Aggiunta di un elemento Annulla per l'elenco dei clip](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-adding-trim-element-to-clip-list.png)

*Aggiunta di un elemento Annulla per l'elenco dei clip*

Se si modifica il codice xml elenco clip … sopra ed eseguire una verifica locale, verrà visualizzato il video correttamente stati rimossi da 10 a 20 secondi nel video.

Contrasto cosa accade quando si esegue un'esecuzione locale attraverso, questa stessa xml cliplist non avrebbe lo stesso effetto se vengono applicati in un flusso di lavoro viene eseguito in servizi multimediali di Windows Azure. Avvio di Azure Premium codificatore xml cliplist viene generato ogni volta che, in base al input file che è stato assegnato il processo di codifica. Questo errore indica che le modifiche facciamo sul linguaggio xml Purtroppo da essere sovrascritti.

Per contrastare xml cliplist venga cancellato quando viene avviato un processo di codifica, è possibile nuovamente generato automaticamente in modo semplice subito dopo l'inizio il flusso di lavoro. È possibile eseguire tali azioni personalizzate tramite che cos'è definito "Script componente". Per ulteriori informazioni, vedere [Introduzione al componente script](media-services-media-encoder-premium-workflow-tutorials.md#scripting).


Trascinare un componente di script nell'area di progettazione e rinominarla "SetClipListXML".

![Aggiunta di un componente script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-scripted-comp.png)

*Aggiunta di un componente script*

Quando si analizzano le proprietà del componente script, i quattro tipi diversi script sarà indicato, ogni configurabili in modo da uno script diverso.

![Proprietà componente script](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-scripted-comp-properties.png)

*Proprietà componente script*


###<a id="frame_based_trim_modify_clip_list"></a>Modifica dell'elenco di clip da un componente di script

Prima possiamo nuovamente scrivere xml cliplist generato durante l'avvio del flusso di lavoro, è necessario avere accesso alle proprietà xml cliplist e contenuto. È possibile farlo alla seguente:

    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: " + clipListXML);

![Elenco dei clip posta in arrivo in corso la registrazione](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-incoming-clip-list-logged.png)

*Elenco dei clip posta in arrivo in corso la registrazione*

Prima di tutto è necessario un modo per stabilire da quale punto fino a cui si desidera tagliare il video. Per rendere questo comodo all'utente meno tecniche del flusso di lavoro, pubblicare due proprietà nella radice del grafico. Per eseguire questa operazione, fare clic con il pulsante destro di progettazione e selezionare "Aggiungi proprietà":

- Prima proprietà: "ClippingTimeStart" di tipo: "TIMECODE"
- Secondo proprietà: "ClippingTimeEnd" di tipo: "TIMECODE"

![Aggiungere finestra di dialogo proprietà per ora di inizio di ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-start-time.png)

*Aggiungere finestra di dialogo proprietà per ora di inizio di ritaglio*

![Pubblicato ritaglio proprietà ora nella radice del flusso di lavoro](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-clip-time-props.png)

*Pubblicato ritaglio proprietà ora nella radice del flusso di lavoro*

Configurare le proprietà su un valore appropriato:

![Configurare l'inizio di ritaglio e terminare le proprietà](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-configure-clip-start-end-prop.png)

*Configurare l'inizio di ritaglio e terminare le proprietà*

A questo punto, dall'interno dello script, per accedere entrambe le proprietà, come segue:

    
    // get start and end of clipping:
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    node.log("clipping start: " + clipstart);
    node.log("clipping end: " + clipend);

![Finestra di log con un inizio e fine di ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-show-start-end-clip.png)

*Finestra di log con un inizio e fine di ritaglio*

Di seguito analizzare le stringhe timecode in una più pratico utilizzare form, utilizzando un'espressione regolare semplice:
    
    //parse the start timing: 
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1); 
    node.log("timecode start is: " + starttimecode); 
    def startframerate = startregresult.group(2); 
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend); 
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2); 
    node.log("framerate end is: " + endframerate);

![Finestra di log con output di timecode analizzato](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-output-parsed-timecode.png)

*Finestra di log con output di timecode analizzato*

Con queste informazioni portata di mano, è ora possibile modificare il xml cliplist per riflettere le ore di inizio e fine per il ritaglio ad alta precisione desiderata del filmato.

![Codice di script per aggiungere elementi Annulla](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-add-trim-elements.png)

*Codice di script per aggiungere elementi Annulla*

Questa operazione è stata eseguita tramite operazioni di modifica normale stringa. Immagine ClipArt modificata elenco xml risultante viene scritta nuovamente la proprietà clipListXML nella radice del flusso di lavoro tramite il metodo "setProperty". Finestra log dopo l'esecuzione di un altro test da mostrare Contattaci le operazioni seguenti:

![Registrazione l'elenco dei clip risultante](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-log-result-clip-list.png)

*Registrazione l'elenco dei clip risultante*

Eseguire un'esecuzione di test per vedere come sono stati ritagliati flussi video e audio. Come eseguire le varie esecuzione di test più valori diversi per i punti di limitazione per motivi, si noterà che quelle verrà non considerare tuttavia! Il motivo è che la finestra di progettazione, a differenza di runtime Azure, non esegue l'override xml cliplist ogni esecuzione. Ciò significa che solo la prima volta impostati i punti in e out, impedirà xml trasformare, tutte le altre volte la clausola di protezione (se (clipListXML.indexOf ("<trim>") = = -1)) impedirà il flusso di lavoro di aggiunta di un altro elemento Annulla quando è già presente una presentazione.

Per il flusso di lavoro con facilità verificare in locale, è migliore aggiungere codice zenzero mantenere che esamina se un elemento Annulla era già presenta. In caso affermativo, è possibile rimuoverlo prima di continuare modificando il codice xml con i nuovi valori. Invece di usare stringhe normale, è probabile che sia sicuro eseguire questa operazione tramite modello a oggetti xml reale analisi.

Per poter aggiungere tale codice anche se è necessario aggiungere un numero di istruzioni di importazione prima di tutto all'inizio dello script:
    
    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;

A questo punto è possibile aggiungere il codice di pulizia necessario:

    //for local testing: delete any pre-existing trim elements from the clip list xml by parsing the xml into a DOM:
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder();
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already: 
    XPath xpath = XPathFactory.newInstance().newXPath();
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements,dom,XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }

    node.log("about to delete any existing trim nodes");
     //delete the trim nodes: 
    elementsToDelete.each{ 
        e -> e.getParentNode().removeChild(e);
    }; 
    node.log("deleted any existing trim nodes");
    
    //serialize the modified clip list xml dom into a string: 
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result); 
    clipListXML = result.getWriter().toString();
    
Questo codice passa sopra il punto in cui è aggiungere i taglio gli elementi xml cliplist.

A questo punto è possibile eseguire e modificare il flusso di lavoro come molto simile a quella vogliamo mantenendo le modifiche applicate mai ora.    

###<a id="frame_based_trim_clippingenabled_prop"></a>Aggiunta di una comoda proprietà ClippingEnabled

Come non è sempre consigliabile pulizia per l'esecuzione, si concludere il flusso di lavoro mediante l'aggiunta di un contrassegno comodo boolean che indica se non si desidera abilitare pulizia / ritaglio.

Come prima, pubblicare una nuova proprietà nella radice del flusso di lavoro denominata "ClippingEnabled" di tipo "BOOLEAN".

![Pubblicare una proprietà per l'attivazione di ritaglio](./media/media-services-media-encoder-premium-workflow-tutorials/media-services-enable-clip.png)

*Pubblicare una proprietà per l'attivazione di ritaglio*

Con la seguente clausola protezione semplice, è possibile selezionare in caso di limitazione per motivi e decidere se il nostro elenco di clip come tale deve essere modificate o meno.

    //check if clipping is required: 
    def clippingrequired = node.getProperty("../ClippingEnabled"); 
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML); 
        node.log("no clipping required"); 
        return; 
    }


###<a id="code"></a>Codice completo

    import javax.xml.parsers.*; 
    import org.xml.sax.*; 
    import org.w3c.dom.*;
    import javax.xml.*;
    import javax.xml.xpath.*; 
    import javax.xml.transform.*; 
    import javax.xml.transform.stream.*; 
    import javax.xml.transform.dom.*;
    
    // get cliplist xml: 
    def clipListXML = node.getProperty("../clipListXml");
    node.log("clip list xml coming in: \n" + clipListXML);
    // get start and end of clipping: 
    def clipstart = node.getProperty("../ClippingTimeStart").toString();
    def clipend = node.getProperty("../ClippingTimeEnd").toString();
    
    //parse the start timing:
    def startregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipstart); 
    startregresult.matches(); 
    def starttimecode = startregresult.group(1);
    node.log("timecode start is: " + starttimecode);
    def startframerate = startregresult.group(2);
    node.log("framerate start is: " + startframerate);
    
    //parse the end timing: 
    def endregresult = (~/(\d\d:\d\d:\d\d:\d\d)\/(\d\d)/).matcher(clipend);
    endregresult.matches(); 
    def endtimecode = endregresult.group(1); 
    node.log("timecode end is: " + endtimecode); 
    def endframerate = endregresult.group(2);

    node.log("framerate end is: " + endframerate);
    
    //for local testing: delete any pre-existing trim elements 
    //from the clip list xml by parsing the xml into a DOM:
    
    DocumentBuilderFactory factory=DocumentBuilderFactory.newInstance();
    DocumentBuilder builder=factory.newDocumentBuilder(); 
    InputSource is=new InputSource(new StringReader(clipListXML)); 
    Document dom=builder.parse(is);

    //find the trim element inside videoSource and audioSource and remove it if it exists already:
    XPath xpath = XPathFactory.newInstance().newXPath(); 
    String findAllTrimElements = "//trim"; 
    NodeList trimelems = xpath.evaluate(findAllTrimElements, dom, XPathConstants.NODESET);

    //copy trim nodes into a "to-be-deleted" collection 
    Set<Element> elementsToDelete = new HashSet<Element>(); 
    for (int i = 0; i < trimelems.getLength(); i++) { 
        Element e = (Element)trimelems.item(i); 
        elementsToDelete.add(e); 
    }
    
    node.log("about to delete any existing trim nodes");
    //delete the trim nodes:
    elementsToDelete.each{ e -> 
        e.getParentNode().removeChild(e); 
    };
    node.log("deleted any existing trim nodes");

    //serialize the modified clip list xml dom into a string:
    def transformer = TransformerFactory.newInstance().newTransformer();
    StreamResult result = new StreamResult(new StringWriter());
    DOMSource source = new DOMSource(dom);
    transformer.transform(source, result);
    clipListXML = result.getWriter().toString();

    //check if clipping is required:
    def clippingrequired = node.getProperty("../ClippingEnabled");
    node.log("clipping required: " + clippingrequired.toString()); 
    if(clippingrequired == null || clippingrequired == false) 
    {
        node.setProperty("../clipListXml",clipListXML);
        node.log("no clipping required");
        return; 
    }

    //add trim elements to cliplist xml 
    if ( clipListXML.indexOf("<trim>") == -1 ) 
    {
        //trim video 
        clipListXML = clipListXML.replace("<videoSource>","<videoSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\"" + endframerate +"\"> " + endtimecode + 
            " </outPoint>\n </trim> \n"); 
        //trim audio 
        clipListXML = clipListXML.replace("<audioSource>","<audioSource>\n <trim>\n <inPoint fps=\""+ 
            startframerate +"\">" + starttimecode + 
            "</inPoint>\n" + "<outPoint fps=\""+ endframerate +"\">" + 
            endtimecode + "</outPoint>\n </trim>\n");
        node.log( "clip list going out: \n" +clipListXML ); 
        node.setProperty("../clipListXml",clipListXML); 
    }


##<a name="also-see"></a>Vedere anche 

[Introduzione a Premium codifica in Media Azure servizi](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Come usare Premium codifica in Media Azure servizi](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codifica contenuto su richiesta con il servizio di supporto Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

[Codec e i formati multimediali codificatore Premium del flusso di lavoro](media-services-premium-workflow-encoder-formats.md)

[File di flusso di lavoro di esempio](http://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)

[Strumento di esplorazione di servizi multimediali di Azure](http://aka.ms/amse)

##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
