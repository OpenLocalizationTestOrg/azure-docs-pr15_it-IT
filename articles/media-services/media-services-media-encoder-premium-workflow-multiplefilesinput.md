<properties
    pageTitle="Utilizzo di più file di input e le proprietà del componente con Premium Encoder | Microsoft Azure"
    description="In questo argomento viene illustrato come utilizzare setRuntimeProperties per utilizzare più file di input e passare dati personalizzati al processore media del flusso di lavoro di Media Encoder Premium."
    services="media-services"
    documentationCenter=""
    authors="xpouyat"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"  
    ms.author="xpouyat;anilmur;juliako"/>

# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Utilizzo di più file di input e le proprietà del componente con Encoder Premium

## <a name="overview"></a>Panoramica

Esistono scenari in cui si potrebbe necessario per personalizzare le proprietà del componente, specificare il contenuto XML elenco Clip oppure inviare più file di input quando si invia un'attività con il processore media **Del flusso di lavoro di Media Encoder Premium** . Alcuni esempi sono:

- Sovrapporre del testo nel video e impostare il valore di testo (ad esempio, la data corrente) in fase di esecuzione per ogni video input.
- Personalizzazione del XML elenco Clip (per specificare una o più file di origine, con o senza limitazione per motivi, ecc.).
- Sovrapposizione di un'immagine del logo nel video di input mentre viene codificato il video.

Per consentire il **Flusso di lavoro Media Encoder Premium** che si sta modificando alcune proprietà del flusso di lavoro quando si crea l'attività o inviano più file di input, è necessario usare una stringa di configurazione contenente **setRuntimeProperties** e/o **transcodeSource**. In questo argomento viene illustrato come utilizzarli.


## <a name="configuration-string-syntax"></a>Nella sintassi della stringa configurazione

Stringa di configurazione per impostare l'attività di codifica utilizza un documento XML è simile alla seguente:

    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


Di seguito è il codice c# che legge la configurazione XML da un file e passa all'attività in un processo:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


## <a name="customizing-component-properties"></a>Personalizzazione delle proprietà componente  

### <a name="property-with-a-simple-value"></a>Proprietà con un valore semplice
In alcuni casi è utile per personalizzare una proprietà componente insieme il flusso di lavoro che dovrà essere eseguita dal flusso di lavoro Media Encoder Premium.

Si supponga è progettato un flusso di lavoro che il testo sovrapposizioni su video e il testo (ad esempio, la data corrente) dovrebbe essere impostato in fase di esecuzione. È possibile eseguire questa operazione inviando il testo da impostare come nuovo valore per la proprietà text del componente di sovrapposizione da attività codifica. È possibile utilizzare questo meccanismo per modificare altre proprietà di un componente del flusso di lavoro (ad esempio la posizione o colori della sovrapposizione, la velocità di codificatore AVC ecc.).

**setRuntimeProperties** viene utilizzato per ignorare una proprietà nei componenti del flusso di lavoro.

Esempio:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


### <a name="property-with-an-xml-value"></a>Proprietà con un valore XML

Per impostare una proprietà che prevede un valore XML, racchiudere utilizzando `<![CDATA[ and ]]>`.

Esempio:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Assicurarsi di non spostare un a capo restituito subito dopo `<![CDATA[`.


### <a name="propertypath-value"></a>valore propertyPath

Negli esempi precedenti è stato il propertyPath "/ File multimediale Input/nomefile" o "/ inactiveTimeout" o "clipListXml".
Si tratta in genere, il nome del componente, quindi sul nome della proprietà. Il percorso può essere più o meno livelli, ad esempio "/ primarySourceFile" (perché la proprietà nella radice del flusso di lavoro) o "/ Video/opacità di sovrapposizione elaborazione/grafico" (perché la sovrapposizione in un gruppo).    

Per verificare se il nome del percorso e proprietà, utilizzare il pulsante di azione che immediatamente accanto a ogni proprietà. È possibile fare clic su questo pulsante di azione e selezionare **Modifica**. Questa operazione verrà visualizzato il nome effettivo della proprietà e immediatamente di sopra, lo spazio dei nomi.

![Azione/Edit](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Proprietà](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Più file di input

Tutte le attività che presenta al **Flusso di lavoro Media Encoder Premium** richiedono due attività:

- Il primo è un *Flusso di lavoro risorse* che contiene un file di flusso di lavoro. È possibile progettare i file del flusso di lavoro in [Progettazione flussi di lavoro](media-services-workflow-designer.md).
- Il secondo è una *Risorsa multimediale* che contiene i file multimediali che si desidera codificare.

Quando si desidera inviare più file multimediali per il codificatore di **Flusso di lavoro Media Encoder Premium** , i vincoli seguenti:

- Tutti i file multimediali trovino nella stessa *Risorsa multimediale*. Utilizzo di più risorse multimediali non è supportato.
- È necessario impostare il file principale in questa risorsa multimediale (se possibile, si tratta di file video principale che il codificatore viene chiesto di processo).
- È necessario passare i dati di configurazione che include l'elemento **setRuntimeProperties** e/o **transcodeSource** al processore.
  - **setRuntimeProperties** viene utilizzato per ignorare la proprietà filename o un'altra proprietà nei componenti del flusso di lavoro.
  - **transcodeSource** viene utilizzato per specificare il contenuto di Clip elenco XML.

Connessioni del flusso di lavoro:

 - Se si usa uno o più componenti di Input di File multimediale e piano utilizzare **setRuntimeProperties** per specificare il nome del file, non di collegare il pin componente file principale ad essi. Assicurarsi che vi è alcuna connessione tra l'oggetto file principale e il controllo di File multimediali.
 - Se si preferisce utilizzare Clip elenco XML e un componente di origine di elementi multimediali, quindi è possibile connettersi entrambe contemporaneamente.

![Nessuna connessione dal File di origine primario per l'Input File multimediali](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Se si utilizza setRuntimeProperties per impostare la proprietà filename non esiste nessuna connessione da file principale per i componenti di Input di File multimediali.*

![Connessione dall'elenco dei Clip XML per ritagliare origine elenco](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*È possibile connettersi XML elenco Clip multimediali origine e utilizzare transcodeSource.*


### <a name="clip-list-xml-customization"></a>ClipArt personalizzazione elenco XML
È possibile specificare il codice XML elenco Clip del flusso di lavoro in fase di esecuzione usando **transcodeSource** nella stringa di configurazione XML. È necessario il pin Clip elenco XML da collegare al componente di origine di elementi multimediali del flusso di lavoro.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Se si desidera specificare /primarySourceFile per utilizzare questa proprietà per assegnare un nome di file di output utilizzando 'le espressioni di, è consigliabile passando XML elenco Clip come una proprietà *dopo* la proprietà /primarySourceFile, per evitare che l'elenco dei Clip sovrascritti impostando /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Con la rimozione di alta precisione aggiuntiva:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


## <a name="example"></a>Esempio

Valutare la possibilità di esempio in cui si desidera sovrapporre un'immagine del logo nel video di input, mentre il video viene codificato. In questo esempio, il video di ingresso è denominato "MyInputVideo.mp4" e il logo è "MyLogo.png". Eseguire la procedura seguente:

- Creare una risorsa di flusso di lavoro con il file del flusso di lavoro (vedere nell'esempio seguente).
- Creare una risorsa multimediale, che contiene due file: MyInputVideo.mp4 come file principale e MyLogo.png.
- Inviare un'attività per il processore media del flusso di lavoro di Media Encoder Premium con i beni inpui sopra e specificare la stringa seguente configurazione.

Configurazione:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


Nell'esempio precedente, il nome del file video viene inviato per il componente di Input di File multimediale e la proprietà primarySourceFile. Il nome del file logo viene inviato a un altro Input di File multimediali che sia connesso al componente sovrimpressione grafica.

>[AZURE.NOTE]Il nome di file video viene inviato per la proprietà primarySourceFile. Il motivo per questa operazione consiste nell'usare questa proprietà del flusso di lavoro per la creazione di utilizzo di espressioni, ad esempio il nome del file output corretto.


### <a name="step-by-step-workflow-creation-that-overlays-a-logo-on-top-of-the-video"></a>Creazione di dettagliate del flusso di lavoro che si sovrappone un logo nella parte superiore del video     

Ecco la procedura per creare un flusso di lavoro che entrerà in due file come input: un video e un'immagine. Verrà sovrapposto l'immagine nella parte superiore del video.

Aprire **Progettazione flussi di lavoro** e selezionare **File** > **Nuova area di lavoro** > **Trascodifica progetto**.

Il nuovo flusso di lavoro mostra tre elementi:

- File di origine primario
- Elenco dei clip XML
- File di output/bene  

![Nuovo flusso di lavoro codifica](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nuovo flusso di lavoro codifica*


Per accettare il file di supporto di input, iniziare con l'aggiunta di un componente di Input di File multimediali. Per aggiungere un componente al flusso di lavoro, cercare nella casella di ricerca Repository e trascinare la voce desiderata nel riquadro di progettazione.

Aggiungere il file video da utilizzare per la progettazione del flusso di lavoro. A tale scopo, fare clic sul riquadro di sfondo in Progettazione flussi di lavoro e cercare la proprietà di File di origine principale nel riquadro delle proprietà destra. Fare clic sull'icona della cartella e selezionare il file video appropriato.

![Origine File principale](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Origine File principale*


Successivamente, specificare il file video nel componente Input di File multimediali.   

![Origine di Input di File multimediali](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Origine di Input di File multimediali*


Come questa operazione, il componente Input di File multimediali esaminare il file, inserire il PIN di output per riflettere il file che verificato.

Il passaggio successivo consiste nell'aggiungere una "Video dati tipo per l'aggiornamento" per specificare lo spazio di colore per Rec.709. Aggiungere un convertitore di formato Video"" che è impostato su tipo di dati Layout/Layout = configurabili piano. Questa operazione verrà convertito flusso video in un formato che può essere considerato come origine del componente sovrapposti.

![Aggiornamento di tipo di dati e formato video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Aggiornamento di tipo di dati video e formato*

![Tipo di layout = configurabili piano](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo di layout è configurabile piano*

Successivamente, aggiungere un componente di sovrapposizione Video e connettere il pin video (non compresso) per il pin video (non compresso) di input di file multimediali.

Aggiungere un altro File di input di dati multimediali (per caricare il file del logo), fare clic su questo componente e rinominarla "Logo Input di File multimediali" e selezionare un'immagine (un file con estensione png, ad esempio) per la proprietà di file. Connettere il pin immagine non compressa della sovrapposizione pin immagine non compressa.

![Origine file componente e immagine sovrapposta](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Origine file componente e immagine sovrapposta*


Se si desidera modificare la posizione del logo per il video (ad esempio, è consigliabile posizionare al 10% di fuori dell'angolo superiore sinistro del video), deselezionare la casella di controllo "Input manuale". È possibile eseguire questa operazione perché si utilizza un Input di File multimediali per fornire il file del logo per il componente di sovrapposizione.

![Posizione di sovrapposizione](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posizione di sovrapposizione*


Per codificare video all'h. 264, aggiungere i componenti di codificatore AVC Video Encoder e AAC nell'area di progettazione. Connettere il PIN.
Configurare il codificatore AAC e selezionare Audio formato conversione/predefinito: 2.0 (L, R).

![Encoder audio e Video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Encoder audio e Video*


A questo punto componenti **ISO Mpeg-4 Multiplexer** e **File di Output** e connettere il PIN, come illustrato.

![MP4 multiplexer e file di output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer e file di output*


È necessario impostare il nome del file di output. Fare clic sul componente di **File di Output** e modificare l'espressione per il file:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome del file output](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome del file output*

È possibile eseguire il flusso di lavoro locale per verificare che sia in esecuzione correttamente.

Termine, è possibile eseguire in servizi multimediali di Windows Azure.

Prima di tutto, preparare una risorsa in servizi multimediali di Windows Azure con due file al suo interno: il file video e il logo. È possibile eseguire questa operazione utilizzando .NET o API REST. È possibile eseguire questa operazione tramite il portale di Azure e [Risorse di servizi multimediali di Azure](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

In questa esercitazione viene illustrato come gestire le risorse con AMSE. Esistono due modi per aggiungere file a una risorsa:

- Creare una cartella locale, copiare i due file al suo interno e trascinare e rilasciare la cartella da cui la scheda **risorse** .
- Caricare il file video come una risorsa, visualizzare le informazioni di risorse, fare clic sulla scheda file e caricare un file aggiuntivo (logo).

>[AZURE.NOTE]Assicurarsi di impostare un file principale del bene (file video principale).

![File di risorse in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*File di risorse in AMSE*


Selezionare la risorsa e scegliere di codificare con Encoder Premium. Caricare il flusso di lavoro e selezionarlo.

Fare clic sul pulsante per passare i dati al processore e aggiungere il seguente codice XML per impostare le proprietà di runtime:

![Codificatore Premium in AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificatore Premium in AMSE*


Incollare quindi i dati XML seguenti. È necessario specificare il nome del file video per l'Input File multimediali sia primarySourceFile. Specificare il nome del nome del file per il logo troppo.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Se si utilizza .NET SDK per creare ed eseguire l'attività, sono necessario passare come stringa di configurazione questi dati XML.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);

Una volta completato il processo, il file MP4 del bene di output visualizza la sovrapposizione!

![Sovrapposizione del video](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sovrapposizione del video*


È possibile scaricare il flusso di lavoro di esempio da [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


## <a name="see-also"></a>Vedere anche

- [Introduzione a Premium codifica in Media Azure servizi](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

- [Come utilizzare la codifica Premium in servizi multimediali di Windows Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

- [Codifica contenuto su richiesta tramite servizi multimediali di Windows Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

- [Formati di flusso di lavoro Media Encoder Premium e codec](media-services-premium-workflow-encoder-formats.md)

- [File di flusso di lavoro di esempio](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

- [Strumento di esplorazione di servizi multimediali di Azure](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
