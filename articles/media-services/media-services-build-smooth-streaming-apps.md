<properties 
    pageTitle="Arrotondare Streaming di Windows Store App esercitazione | Microsoft Azure" 
    description="Informazioni su come utilizzare servizi multimediali di Windows Azure per creare un'applicazione di archiviazione Windows c# con un controllo MediaElement XML flusso smussate la riproduzione di contenuto." 
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



#<a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Modalità di compilazione di un Smooth Streaming applicazioni di Windows Store

Smussate Streaming Client SDK per Windows 8 consente agli sviluppatori di applicazioni di Windows Store in grado di riprodurre contenuto su richiesta e live Smooth Streaming. Oltre a riproduzione di base di Smooth Streaming contenuto che SDK offre funzionalità avanzate come la protezione Microsoft PlayReady, qualità dell'audio restrizione livello, DVR Live flusso commutazione, in attesa di aggiornamenti di stato (ad esempio modifiche a livello di qualità) e gli eventi di errore e così via. Per ulteriori informazioni delle funzionalità supportate, vedere [le note sulla versione](http://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes). Per ulteriori informazioni, vedere [Windows Media Player Framework per Windows 8](http://playerframework.codeplex.com/). 

In questa esercitazione include quattro lezioni:

1. Creare un'applicazione di archiviazione flusso smussate base
2. Aggiungere una barra di scorrimento per controllare lo stato di avanzamento di elementi multimediali
3. Selezionare Smooth Streaming flussi
4. Selezionare le tracce Smooth Streaming

##<a name="prerequisites"></a>Prerequisiti

- Windows 8 a 32 o 64 bit. È possibile ottenere [Valutazione di Windows 8 Enterprise](http://msdn.microsoft.com/evalcenter/jj554510.aspx) da MSDN.
- Visual Studio 2012 o Visual Studio Express 2012 o versione successiva. Da [qui](http://www.microsoft.com/visualstudio/11/downloads)è possibile ottenere la versione di valutazione.
- [Microsoft Smooth Streaming Client SDK per Windows 8](http://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Homehttp://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).


Soluzione completa per ogni lezione può essere scaricata dal esempi di codice per sviluppatori MSDN (raccolta di codice): 

- [Lezione 1](http://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - semplice semplice Windows 8 Streaming Media Player 
- Controllare la [Lezione 2](http://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - un lettore multimediale Smooth Streaming semplice Windows 8 con una barra di scorrimento, 
- [Lezione 3](http://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - semplice Windows 8 Streaming lettore multimediale con selezione di flusso,  
- [Lezione 4](http://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - semplice Windows 8 Streaming lettore multimediale con selezione traccia.

##<a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lezione 1: Creare un'applicazione di archiviazione flusso smussate base

In questa lezione si creerà un'applicazione di Windows Store con un controllo MediaElement per riprodurre flusso smussata contenuto.  L'aspetto dell'applicazione in esecuzione:

![Esempio di applicazione Smooth Streaming Windows Store][PlayerApplication]
 
Per ulteriori informazioni sullo sviluppo di applicazioni di Windows Store, vedere [sviluppare ottimo App per Windows 8](http://msdn.microsoft.com/windows/apps/br229512.aspx). In questa lezione vengono descritte le procedure seguenti:

1.  Creare un progetto di Windows Store
2.  Progettare l'interfaccia utente (XAML)
3.  Modificare il file code-behind
4.  Compilare e testare l'applicazione

**Per creare un progetto di Windows Store**

1.  Eseguire Visual Studio 2012 o versione successiva.
2.  Dal menu **FILE** fare clic su **Nuovo**e quindi fare clic su **progetto**.
3.  Nella finestra di dialogo Nuovo progetto, digitare o selezionare i valori seguenti:

Nome|Valore
---|---
Gruppo di modelli|Installato/Modelli/Visual c# / Windows Store
Modello|App vuota (XAML)
Nome|SSPlayer
Posizione|C:\SSTutorials
Nome soluzione|SSPlayer
Crea directory per soluzione|(selezionata)

4.  Fare clic su **OK**.

**Per aggiungere un riferimento al Client SDK Streaming smussato**

1.  In Esplora soluzioni destro **SSPlayer**e quindi fare clic su **Aggiungi riferimento**.
2.  Digitare o selezionare i valori seguenti:

Nome|Valore
---|---
Gruppo di riferimento|Windows/estensioni
Guida di riferimento|Selezionare Microsoft Smooth Streaming Client SDK per Windows 8 e pacchetto di Microsoft Visual C++ Runtime
    
3.  Fare clic su **OK**. 

Dopo aver aggiunto i riferimenti, è necessario selezionare la piattaforma di destinazione (x64 o x86), aggiunta di riferimenti non funzionerà per la configurazione di piattaforma qualsiasi CPU.  In Esplora risorse, si vedrà interrogativo avviso giallo per questi aggiunto i riferimenti.

**Per progettare l'interfaccia utente di Windows Media player**

1.  Esplora soluzioni fare doppio clic su **MainPage** per aprirlo in visualizzazione struttura.
2.  Individuare il ** &lt;griglia&gt; ** e ** &lt;/Grid&gt; ** tag file XAML e incollare il codice seguente tra i due tag:

        <Grid.RowDefinitions>
            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
        </Grid.RowDefinitions>
        
        <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="http://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
        </StackPanel>

        <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
        </StackPanel>

        <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
        </StackPanel>

    Il controllo MediaElement viene utilizzato per riprodurre i file multimediali. Il dispositivo di scorrimento denominata sliderProgress verrà utilizzata nella prossima lezione per controllare lo stato di avanzamento di elementi multimediali.

3.  Premere **CTRL + S** per salvare il file.

Il controllo MediaElement non supporta Smooth Streaming contenuto esterna predefinite. Per attivare il supporto Smooth Streaming, è necessario registrare il gestore di flusso di byte Smooth Streaming estensione di file e tipo MIME.  Per registrare, utilizzare il metodo MediaExtensionManager.RegisterByteStremHandler dello spazio dei nomi Windows.Media.

In questo file XAML, alcuni gestori sono associati ai controlli.  È necessario definire tali gestori.

**Per modificare il file code-behind**

1.  In Esplora soluzioni destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  Nella parte superiore del file, aggiungere la seguente istruzione:

        using Windows.Media;

3.  All'inizio della classe **MainPage** , aggiungere il membro di dati seguenti:

        private MediaExtensionManager extensions = new MediaExtensionManager();

4.  Alla fine del costruttore **MainPage** , aggiungere due righe seguenti:

        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
        
5.  Alla fine della classe **MainPage** , oltre il codice seguente:

        #region UI Button Click Events
        private void btnPlay_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Play();
            txtStatus.Text = "MediaElement is playing ...";
        }
        
        private void btnPause_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Pause();
            txtStatus.Text = "MediaElement is paused";
        }
        
        private void btnSetSource_Click(object sender, RoutedEventArgs e)
        {
            sliderProgress.Value = 0;
            mediaElement.Source = new Uri(txtMediaSource.Text);
        
            if (chkAutoPlay.IsChecked == true)
            {
                txtStatus.Text = "MediaElement is playing ...";
            }
            else
            {
                txtStatus.Text = "Click the Play button to play the media source.";
            }
        }
        
        private void btnStop_Click(object sender, RoutedEventArgs e)
        {
            mediaElement.Stop();
            txtStatus.Text = "MediaElement is stopped";
        }
        
        private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            txtStatus.Text = "Seek to position " + sliderProgress.Value;
            mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
        }
        #endregion

    Definisce il gestore eventi sliderProgress_PointerPressed.  Sono disponibili ulteriori works per assicurarne il funzionamento, che rientrano nella lezione successiva di questa esercitazione.
6.  Premere **CTRL + S** per salvare il file.

Il termine il file code-behind è simile alla seguente:

![CodeView nell'applicazione di Visual Studio di smussate Streaming di Windows Store][CodeViewPic]

**Compilare e testare l'applicazione**

1.  Dal menu **COMPILA** fare clic su **Gestione configurazione**.
2.  Modificare **piattaforma soluzione attiva** in base alla piattaforma di sviluppo.
3.  Premere **F6** per compilare il progetto. 
4.  Premere **F5** per eseguire l'applicazione.
5.  Nella parte superiore dell'applicazione, utilizzare l'URL di trasmissione graduale predefinito o immetterne uno diverso. 
6.  Fare clic su **Imposta origine**. Poiché **Riproduzione automatica** è attivata per impostazione predefinita, gli elementi multimediali sono riprodurre automaticamente.  È possibile controllare il supporto utilizzando la **riproduzione**, pulsanti **Pausa** e **interrompere** .  È possibile controllare il volume di elementi multimediali utilizzando il dispositivo di scorrimento verticale.  Tuttavia il dispositivo di scorrimento orizzontale per controllare lo stato di avanzamento di elementi multimediali non è completamente implementata ancora. 

È stata completata lesson1.  In questa lezione, utilizzare un controllo MediaElement per la riproduzione Smooth Streaming contenuto.  Nella prossima lezione, aggiungere un dispositivo di scorrimento per controllare lo stato di avanzamento del contenuto Smooth Streaming.


##<a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lezione 2: Aggiungere una barra di scorrimento per controllare lo stato di avanzamento di elementi multimediali
Per creare un'applicazione di Windows Store in lezione 1, con un controllo MediaElement XAML per la riproduzione Smooth Streaming contenuti multimediali.  Si tratta di alcune funzioni di base elementi multimediali quali start e stop pausa.  In questa lezione si aggiungerà una barra di scorrimento per l'applicazione.

In questa esercitazione verrà usata un timer per aggiornare la posizione del dispositivo di scorrimento in base alla posizione corrente del controllo MediaElement.  Dispositivo di scorrimento inizio e fine anche caso è necessario essere aggiornati in caso di contenuto in diretta.  Questa operazione può essere gestita meglio in caso di aggiornamento adattata origine.

Origini multimediali sono oggetti che generano dati multimediali.  Il sistema di risoluzione origine accetta un flusso di URL o byte e crea l'origine di supporto appropriato per il contenuto.  La risoluzione di origine è la modalità standard per le applicazioni creare origini multimediali. 

In questa lezione vengono descritte le procedure seguenti:

1.  Registrare il gestore Smooth Streaming 
2.  Aggiungere i gestori di eventi a livello di origine adattata manager
3.  Aggiungere i gestori di eventi a livello di origine adattata
4.  Aggiungere gestori MediaElement
5.  Aggiungere il dispositivo di scorrimento correlato codice a barre
6.  Compilare e testare l'applicazione

**Per registrare il gestore di flusso di byte Smooth Streaming e passare il propertyset**

1.  In Esplora soluzioni fare clic con il pulsante destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  All'inizio del file, aggiungere la seguente istruzione:

        using Microsoft.Media.AdaptiveStreaming;

3.  All'inizio della classe MainPage, aggiungere i membri dati seguenti:

        private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
        private IAdaptiveSourceManager adaptiveSourceManager;
    
4.  All'interno del costruttore **MainPage** , aggiungere il codice seguente dopo il **seguente. Inizializzare Components();** interlinea e le righe di codice di registrazione in lezione precedente:
    
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
    
5.  All'interno del costruttore **MainPage** , modificare i due metodi RegisterByteStreamHandler per aggiungere la via parametri:

        // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
        // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
        // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
        extensions.RegisterByteStreamHandler(
            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
        propertySet);

6.  Premere **CTRL + S** per salvare il file.

**Per aggiungere il gestore eventi a livello di origine adattata manager**

1.  In Esplora soluzioni fare clic con il pulsante destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  All'interno della classe **MainPage** , aggiungere il membro di dati seguenti:

        private AdaptiveSource adaptiveSource = null;

3.  Alla fine della classe **MainPage** , aggiungere il gestore eventi seguenti:
    
        #region Adaptive Source Manager Level Events
        private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
        }
        #endregion Adaptive Source Manager Level Events

4.  Alla fine del costruttore **MainPage** , aggiungere la riga seguente per sottoscrivere l'evento open di origine adattata:
    
    + = adaptiveSourceManager.AdaptiveSourceOpenedEvent nuova AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);

5.  Premere **CTRL + S** per salvare il file.

**Per aggiungere gestori di eventi a livello di origine adattata**

1.  In Esplora soluzioni fare clic con il pulsante destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  All'interno della classe **MainPage** , aggiungere il membro di dati seguenti:
    
        private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
        private Manifest manifestObject;
    
3.  Alla fine della classe **MainPage** , aggiungere gestori eventi seguenti:

        #region Adaptive Source Level Events
        private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
        {
            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
        }
        
        private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
        {
            adaptiveSourceStatusUpdate = args;
        }
        
        private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
        {
            txtStatus.Text = "Error: " + args.HttpResponse;
        }
        #endregion Adaptive Source Level Events

4.  Al termine del metodo **mediaElement AdaptiveSourceOpened** , aggiungere il codice riportato di seguito per sottoscrivere gli eventi:
    
        adaptiveSource.ManifestReadyEvent +=
                    mediaElement_ManifestReady;
        adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 
            mediaElement_AdaptiveSourceStatusUpdated;
        adaptiveSource.AdaptiveSourceFailedEvent += 
            mediaElement_AdaptiveSourceFailed;
    
5.  Premere **CTRL + S** per salvare il file.

Gli stessi eventi sono disponibili in origine adattata Manager anche a livello, che può essere utilizzato per la gestione delle funzionalità comuni a tutti gli elementi multimediali nell'app. Ogni AdaptiveSource include i propri eventi e tutti gli eventi AdaptiveSource verranno applicato a catena in AdaptiveSourceManager.

**Per aggiungere gestori di elemento multimediale**

1.  In Esplora soluzioni fare clic con il pulsante destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  Alla fine della classe **MainPage** , aggiungere gestori eventi seguenti:
    
        #region Media Element Event Handlers 
        private void MediaOpened(object sender, RoutedEventArgs e)
        {
            txtStatus.Text = "MediaElement opened";
        }
        
        private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
        {
            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
        }
        
        private void MediaEnded(object sender, RoutedEventArgs e)
        {
            txtStatus.Text ="MediaElement ended.";
        }
        #endregion Media Element Event Handlers

3.  Alla fine del costruttore **MainPage** , aggiungere il codice seguente pedice agli eventi:
    
        mediaElement.MediaOpened += MediaOpened;
        mediaElement.MediaEnded += MediaEnded;
        mediaElement.MediaFailed += MediaFailed;

4.  Premere **CTRL + S** per salvare il file.

**Per aggiungere una barra di scorrimento codice correlati**

1.  In Esplora soluzioni fare clic con il pulsante destro **MainPage**e quindi fare clic su **Visualizza codice**.
2.  All'inizio del file, aggiungere la seguente istruzione:
    
        using Windows.UI.Core;

3.  All'interno della classe **MainPage** , aggiungere i membri dati seguenti:
    
        public static CoreDispatcher _dispatcher;
        private DispatcherTimer sliderPositionUpdateDispatcher;
    
4.  Alla fine del costruttore **MainPage** , aggiungere il codice seguente:

        _dispatcher = Window.Current.Dispatcher;
        PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
        sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
    
5.  Alla fine della classe **MainPage** , aggiungere il codice seguente:
    
        #region sliderMediaPlayer
        private double SliderFrequency(TimeSpan timevalue)
        {
            long absvalue = 0;
            double stepfrequency = -1;
        
            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }
        
            TimeSpan totalDVRDuration = new TimeSpan(absvalue);
        
            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }
        
            return stepfrequency;
        }
        
        void updateSliderPositionoNTicks(object sender, object e)
        {
            sliderProgress.Value = mediaElement.Position.TotalSeconds;
        }
        
        public void setupTimer()
        {
            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
        }

        public void startTimer()
        {
            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderStartTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
        }
        
        // Slider start and end time must be updated in case of live content
        public async void setSliderEndTime(long startTime)
        {
            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
        }
        #endregion sliderMediaPlayer

    **Nota:** CoreDispatcher viene utilizzato per apportare modifiche al thread UI da non dell'interfaccia utente. In caso di bottiglia thread dispatcher, sviluppatore può scegliere di utilizzare dispatcher fornito dall'elemento dell'interfaccia utente e intende aggiornare.  Per esempio:
    
        await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 
          timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
        double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 
          sliderProgress.Maximum = absvalue; }); 
        

6.  Al termine del metodo **mediaElement_AdaptiveSourceStatusUpdated** , aggiungere il codice seguente:
    
        setSliderStartTime(args.StartTime);
        setSliderEndTime(args.EndTime);

7.  Al termine del metodo **MediaOpened** , aggiungere il codice seguente:
    
    sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan); sliderProgress.Width = mediaElement.Width; setupTimer();

8.  Premere **CTRL + S** per salvare il file.

**Compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.  Premere **F5** per eseguire l'applicazione.
3.  Nella parte superiore dell'applicazione, utilizzare l'URL di trasmissione graduale predefinito o immetterne uno diverso. 
4.  Fare clic su **Imposta origine**. 
5.  Testare la barra di scorrimento.

Lezione 2 è stata completata.  In questa lezione è stato aggiunto un dispositivo di scorrimento all'applicazione. 

##<a name="lesson-3-select-smooth-streaming-streams"></a>Lezione 3: Selezionare Smooth Streaming flussi
Streaming smussate è in grado di contenuto di flusso con tracce audio in più lingue selezionabili per i visualizzatori.  In questa lezione, attivare visualizzatori selezionare flussi. In questa lezione vengono descritte le procedure seguenti:

1. Modificare il file XAML
2. Modificare il file behand codice
3. Compilare e testare l'applicazione


**Per modificare il file XAML**

1. In Esplora soluzioni destro **MainPage**e quindi fare clic su **Progettazione**.
2. Individuare &lt;Grid. RowDefinitions&gt;e modificare il RowDefinitions in modo che l'aspetto:

        <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
        </Grid.RowDefinitions>

3. All'interno di &lt;griglia&gt;&lt;/Grid&gt; tag, aggiungere il codice riportato di seguito per definire un controllo casella di riepilogo, in modo che gli utenti possono vedere l'elenco dei flussi disponibili e selezionare flussi:

        <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
        </Grid>

4. Premere **CTRL + S** per salvare le modifiche.


**Per modificare il file code-behind**

1. In Esplora soluzioni destro **MainPage**e quindi fare clic su **Visualizza codice**.
2. Aggiungere una nuova classe SSPlayer nello spazio dei nomi: classe #region flusso
    
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
    
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
    
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
    
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream

3. All'inizio della classe MainPage, aggiungere le definizioni di variabili seguenti:

        private List<Stream> availableStreams;
        private List<Stream> availableAudioStreams;
        private List<Stream> availableTextStreams;
        private List<Stream> availableVideoStreams;

4. Aggiungere l'area geografica di classe MainPage:

        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
        
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
        
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
        
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
        
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
        
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
        
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
        
            // Select the frist video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
        
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
        
            // Select the frist audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
        
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
        
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection

5. Individuare il metodo mediaElement_ManifestReady, aggiungere il codice seguente alla fine della funzione:
    
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();

    In modo quando manifesto MediaElement è pronta, il codice riceve un elenco dei flussi disponibili e compila la casella di riepilogo dell'interfaccia utente con l'elenco.

6. All'interno della classe MainPage, individuare l'interfaccia utente pulsanti fare clic su Opzioni internazionali di eventi e quindi aggiungere la definizione di funzione seguente:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.  Premere **F5** per eseguire l'applicazione.
3.  Nella parte superiore dell'applicazione, utilizzare l'URL di trasmissione graduale predefinito o immetterne uno diverso. 
4.  Fare clic su **Imposta origine**. 
5.  La lingua predefinita è audio_eng. Provare a passare tra audio_eng e audio_es. Ogni volta che, si seleziona un nuovo flusso, è necessario fare clic sul pulsante Invia.

Lezione 3 è stata completata.  In questa lezione, aggiungere la funzionalità per scegliere flussi.

##<a name="lesson-4-select-smooth-streaming-tracks"></a>Lezione 4: Selezionare le tracce Smooth Streaming
Una presentazione Smooth Streaming può contenere più file video codificati con livelli di qualità diverso (bit rate) e risoluzioni. In questa lezione, si consente agli utenti di selezionare le tracce. In questa lezione vengono descritte le procedure seguenti:

1. Modificare il file XAML
2. Modificare il file behand codice
3. Compilare e testare l'applicazione

**Per modificare il file XAML**

1. In Esplora soluzioni destro **MainPage**e quindi fare clic su **Progettazione**.
2. Individuare il &lt;griglia&gt; tag con il nome **gridStreamAndBitrateSelection**, aggiungere il codice seguente alla fine del tag:

        <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
        </StackPanel>

3. Premere **CTRL + S** per salvare le modifiche apportate he


**Per modificare il file code-behind**

1. In Esplora soluzioni destro **MainPage**e quindi fare clic su **Visualizza codice**.
2. Aggiungere una nuova classe SSPlayer nello spazio dei nomi:
    
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
    
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
    
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
    
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
    
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track

3. All'inizio della classe MainPage, aggiungere le definizioni di variabili seguenti:
    
        private List<Track> availableTracks;

4. Aggiungere l'area geografica di classe MainPage:
    
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>

        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();

            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;

            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;

                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }

        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }

        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }

        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }

        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection

5. Individuare il metodo mediaElement_ManifestReady, aggiungere il codice seguente alla fine della funzione:

        getTracks(manifestObject);
        refreshAvailableTracksListBoxItemSource();

6. All'interno della classe MainPage, individuare l'interfaccia utente pulsanti fare clic su Opzioni internazionali di eventi e quindi aggiungere la definizione di funzione seguente:

        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
        }

**Compilare e testare l'applicazione**

1. Premere **F6** per compilare il progetto. 
2.  Premere **F5** per eseguire l'applicazione.
3.  Nella parte superiore dell'applicazione, utilizzare l'URL di trasmissione graduale predefinito o immetterne uno diverso. 
4.  Fare clic su **Imposta origine**. 
5.  Per impostazione predefinita, verranno selezionate tutte le tracce del flusso video. Per provare tutte le tariffe bit, è possibile selezionare il tasso di bit più basso e quindi selezionare la velocità di bit più alta disponibile. È necessario fare clic su INVIO dopo ogni modifica.  È possibile visualizzare le modifiche di qualità video.

Lezione 4 è stata completata.  In questa lezione, aggiungere la funzionalità per scegliere le tracce.


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##<a name="other-resources"></a>Altre risorse:
- [Come creare un'applicazione graduale Streaming Windows 8 JavaScript con caratteristiche avanzate](http://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
- [Panoramica tecnica flusso smussato](http://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
 
