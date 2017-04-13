<properties
    pageTitle="Come usare archiviazione Blob da Xamarin | Microsoft Azure"
    description="La raccolta di Client Azure lo spazio di archiviazione per Xamarin consente agli sviluppatori di creare iOS, Android e Windows Store App con le interfacce utente nativa. In questa esercitazione viene illustrato come utilizzare Xamarin per creare un'applicazione che utilizza archiviazione Blob Azure."
    services="storage"
    documentationCenter="xamarin"
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2016"
    ms.author="micurd"/>

# <a name="how-to-use-blob-storage-from-xamarin"></a>Come usare archiviazione Blob da Xamarin

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## <a name="overview"></a>Panoramica

Creare iOS, Android e Windows Store App con le interfacce utente nativa Xamarin consente agli sviluppatori per utilizzare un condivisa c# base di codice. In questa esercitazione viene illustrato come utilizzare archiviazione Blob Azure con un'applicazione Xamarin. Se si desidera per altre informazioni sull'archiviazione di Azure, prima di leggere i il codice, vedere [Introduzione a Microsoft Azure archiviazione](storage-introduction.md).

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-mobile-authentication-guidance](../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>Creare una nuova applicazione Xamarin

Per questa Guida introduttiva, è necessario creare un'app destinato iOS o Android e Windows. Questa app verrà semplicemente creare un contenitore e caricare un blob in questo contenitore. Si inizierà a usare Visual Studio in Windows per questa Guida introduttiva, ma le esperienze stesso possono essere applicate quando si crea un'app utilizzando Xamarin Studio su Mac OS.

Seguire questa procedura per creare l'applicazione:

1. Se non è già fatto, scaricare e installare [Xamarin per Visual Studio](https://www.xamarin.com/download).
2. Aprire Visual Studio e creare un'App vuota (nativo condivisa): **File > Nuovo > progetto > multipiattaforma > vuota App(Native Shared)**.
3. Fare doppio clic la soluzione nel riquadro di Esplora e selezionare **Gestisci pacchetti NuGet per soluzione**. Cercare **WindowsAzure.Storage** e installare l'ultima versione stabile a tutti i progetti della soluzione.
4. Creare ed eseguire il progetto.

A questo punto è un'applicazione che consente di fare clic su un pulsante che viene incrementato un contatore.

> [AZURE.NOTE] La raccolta di Client Azure lo spazio di archiviazione per Xamarin supporta attualmente seguenti tipi di progetto: condiviso nativo, Xamarin.Forms condivisi, Xamarin.Android e Xamarin.iOS.

## <a name="create-container-and-upload-blob"></a>Contenitore di creare e caricare blob

Successivamente, aggiungere il codice per la classe condivisa `MyClass.cs` che consente di creare un contenitore e si carica un blob in questo contenitore. `MyClass.cs`avrà un aspetto simile al seguente:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    using System.Threading.Tasks;

    namespace XamarinApp
    {
        public class MyClass
        {
            public MyClass ()
            {
            }

            public static async Task createContainerAndUpload()
            {
                // Retrieve storage account from connection string.
                CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

                // Create the blob client.
                CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

                // Retrieve reference to a previously created container.
                CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

                // Create the container if it doesn't already exist.
                await container.CreateIfNotExistsAsync();

                // Retrieve reference to a blob named "myblob".
                CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

                // Create the "myblob" blob with the text "Hello, world!"
                await blockBlob.UploadTextAsync("Hello, world!");
            }
        }
    }

Assicurarsi di sostituire "your_account_name_here" e "your_account_key_here" con il nome dell'account effettivo e la chiave account. È quindi possibile utilizzare questa classe condivisa nella iOS, Android e Windows Phone applicazione. È possibile aggiungere semplicemente `MyClass.createContainerAndUpload()` a ogni progetto. Per esempio:

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

    using Android.App;
    using Android.Widget;
    using Android.OS;

    namespace XamarinApp.Droid
    {
        [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
        public class MainActivity : Activity
        {
            int count = 1;

            protected override async void OnCreate (Bundle bundle)
            {
                base.OnCreate (bundle);

                // Set our view from the "main" layout resource
                SetContentView (Resource.Layout.Main);

                // Get our button from the layout resource,
                // and attach an event to it
                Button button = FindViewById<Button> (Resource.Id.myButton);

                button.Click += delegate {
                    button.Text = string.Format ("{0} clicks!", count++);
                };

              await MyClass.createContainerAndUpload();
            }
        }
    }

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

    using System;
    using UIKit;

    namespace XamarinApp.iOS
    {
        public partial class ViewController : UIViewController
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.createContainerAndUpload();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc that aren't in use.
            }
        }
    }

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage > MainPage.xaml.cs

    using Windows.UI.Xaml.Controls;
    using Windows.UI.Xaml.Navigation;

    // The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=391641

    namespace XamarinApp.WinPhone
    {
        /// <summary>
        /// An empty page that can be used on its own or navigated to within a Frame.
        /// </summary>
        public sealed partial class MainPage : Page
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.createContainerAndUpload();
            }
        }
    }


## <a name="run-the-application"></a>Eseguire l'applicazione

È ora possibile eseguire questa applicazione in un emulatore Android o Windows Phone. È anche possibile eseguire questa applicazione in un emulatore iOS, ma sarà necessario un Mac. Per istruzioni specifiche su come eseguire questa operazione, leggere la documentazione per [la connessione di Visual Studio per Mac](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

Dopo aver eseguito l'app, verrà creato il contenitore `mycontainer` nell'account di archiviazione. Deve contenere il blob `myblob`, che contiene il testo `Hello, world!`. È possibile verificarlo tramite [Esplora archivi di Microsoft Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva, è stato illustrato come creare un'applicazione multipiattaforma in Xamarin che utilizza l'archiviazione Azure. Questa introduzione specificamente basato su uno scenario in archiviazione Blob. Tuttavia, è possibile eseguire molto più con, non solo lo spazio di archiviazione Blob, ma anche con tabelle, File e lo spazio di archiviazione di coda. Controllare gli articoli seguenti per altre informazioni:
- [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md)
- [Guida introduttiva a archivio tabelle Azure utilizzando .NET](storage-dotnet-how-to-use-tables.md)
- [Guida introduttiva allo spazio di archiviazione di Azure coda utilizzando .NET](storage-dotnet-how-to-use-queues.md)
- [Guida introduttiva di Azure spazio di archiviazione File in Windows](storage-dotnet-how-to-use-files.md)

[AZURE.INCLUDE [storage-try-azure-tools-blobs](../../includes/storage-try-azure-tools-blobs.md)]
