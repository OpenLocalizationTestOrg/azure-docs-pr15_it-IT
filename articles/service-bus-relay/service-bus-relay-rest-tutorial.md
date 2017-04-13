<properties
    pageTitle="Servizio Bus resto esercitazioni utilizzando inoltro dei messaggi | Microsoft Azure"
    description="Compilare una semplice applicazione di servizio Bus inoltro host esposti un'interfaccia basata su resto."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Esercitazione servizio Bus inoltro resto

In questa esercitazione viene illustrato come creare una semplice applicazione host Bus di servizio esposti un'interfaccia basata su resto. RESTO consente ai client web, ad esempio un web browser accedere a API di Bus servizio tramite richieste HTTP.

In questa esercitazione utilizza il resto di Windows Communication Foundation (WCF) modello di programmazione per creare un servizio resto su Bus di servizio. Per ulteriori informazioni, vedere [WCF resto del modello di programmazione](https://msdn.microsoft.com/library/bb412169.aspx) e [Progettazione e implementazione Services](https://msdn.microsoft.com/library/ms729746.aspx) nella documentazione WCF.

## <a name="step-1-create-a-service-namespace"></a>Passaggio 1: Creare uno spazio dei nomi del servizio

Il primo passaggio consiste per creare uno spazio dei nomi e per ottenere una chiave di firma di Access condiviso (SA). Uno spazio dei nomi fornisce un limite di applicazione per ogni applicazione esposto tramite Bus di servizio. Una chiave SA viene generata automaticamente dal sistema quando viene creato un spazio dei nomi di servizio. La combinazione dei nomi di servizio e chiave SA fornisce le credenziali per Bus di servizio per l'accesso a un'applicazione di autenticazione.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Passaggio 2: Definire un contratto di servizio WCF basati su REST da usare con Bus di servizio

Come con altri servizi di assistenza Bus, quando si crea un servizio di stile resto, è necessario definire il contratto. Il contratto specifica le operazioni che supporta l'host. Un'operazione di servizio possa essere considerata come metodo di servizio web. Contratti vengono creati mediante la definizione di un'interfaccia C++, c# o Visual Basic. Ogni metodo nell'interfaccia corrisponde a un'operazione di servizio specifico. L'attributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) deve essere applicato a ogni interfaccia e l'attributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) deve essere applicato a ogni operazione. Se un metodo di un'interfaccia contenente [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) non ha [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), tale metodo non viene esposto. Il codice utilizzato per le attività seguenti viene illustrato nell'esempio seguendo la procedura descritta.

La differenza tra un contratto di servizio Bus base e un contratto resto stile principale è l'aggiunta di una proprietà per [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Questa proprietà consente di eseguire il mapping di un metodo nell'interfaccia di un metodo sul lato dell'interfaccia. In questo caso, verrà usata [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) collegare un metodo HTTP GET. In questo modo Bus di servizio recuperare e interpretare i comandi inviati all'interfaccia in modo accurato.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Per creare un contratto di servizio Bus con un'interfaccia

1. Aprire Visual Studio come amministratore: destro il programma nel menu **Start** e quindi scegliere **Esegui come amministratore**.

2. Creare un nuovo progetto applicazione console. Fare clic sul menu **File** e selezionare **Nuovo**, quindi selezionare **progetto**. Nella finestra di dialogo **Nuovo progetto** fare clic su **Visual c#**, selezionare il modello **Applicazione Console** e denominarla **ImageListener**. Utilizzare il **percorso**predefinito. Fare clic su **OK** per creare il progetto.

3. Per un progetto c#, Visual Studio crea un `Program.cs` file. Questa classe contiene un oggetto vuoto `Main()` metodo necessario per un progetto di applicazione console creare in modo corretto.

4. Aggiungere riferimenti a Bus di servizio e **System.ServiceModel.dll** al progetto installando il pacchetto di servizio Bus NuGet. Questo pacchetto aggiunge automaticamente i riferimenti agli raccolte Bus di servizio, nonché la WCF **ServiceModel**. In Esplora soluzioni fare clic sul progetto **ImageListener** e quindi fare clic su **Gestisci pacchetti NuGet**. Fare clic sulla scheda **Sfoglia** , quindi cercare `Microsoft Azure Service Bus`. Fare clic su **installazione**e accettare le condizioni di utilizzo.

5. È necessario aggiungere un riferimento a **ServiceModel** in modo esplicito al progetto:

    un. In Esplora soluzioni fare clic sulla cartella **riferimenti** nella cartella del progetto e quindi fare clic su **Aggiungi riferimento**.

    b. Nella finestra di dialogo **Aggiungi riferimento** fare clic sulla scheda **Framework** sul lato sinistro e nella casella di **ricerca** , digitare **System.ServiceModel.Web**. Selezionare la casella di controllo **System.ServiceModel.Web** , quindi fare clic su **OK**.

6. Aggiungere quanto segue `using` le istruzioni nella parte superiore del file Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) è quello che consente l'accesso a caratteristiche di base di WCF. Servizio Bus utilizza molti degli oggetti e attributi di WCF per definire i contratti di servizio. Utilizzare questo spazio dei nomi nella maggior parte delle applicazioni di inoltro Bus di servizio. Analogamente, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) consente di definire il canale, che è l'oggetto a cui si comunica Bus di servizio e il client web browser. Infine, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contiene tipi che consentono di creare applicazioni web.

7. Rinominare la `ImageListener` spazio dei nomi da **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Direttamente dopo la parentesi graffa di apertura della dichiarazione di spazio dei nomi, definire una nuova interfaccia denominata **IImageContract** e applicarvi l'attributo **ServiceContractAttribute** con un valore di `http://samples.microsoft.com/ServiceModel/Relay/`. Il valore dello spazio dei nomi diverso dallo spazio dei nomi utilizzate nell'ambito del codice. Il valore dello spazio dei nomi viene utilizzato come un identificatore univoco per il presente contratto e deve avere le informazioni sulla versione. Per ulteriori informazioni, vedere [Il controllo delle versioni di servizio](http://go.microsoft.com/fwlink/?LinkID=180498). Specificare lo spazio dei nomi in modo esplicito impedisce l'aggiunta al nome del contratto il valore dello spazio dei nomi predefinito.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. All'interno di `IImageContract` interfaccia, dichiarare un metodo per l'operazione singola il `IImageContract` contratto esposti nell'interfaccia e applicare il `OperationContractAttribute` dell'attributo al metodo che si desidera esporre come parte del contratto di servizio Bus pubblico.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. Nell'attributo **OperationContract** , aggiungere il valore di **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    In questo modo consente Bus di servizio per indirizzare le richieste HTTP GET per `GetImage`e per convertire i valori restituiti `GetImage` in una risposta HTTP GETRESPONSE. In un secondo momento nell'esercitazione, utilizzare un web browser per accedere a questo metodo e visualizzare l'immagine nel browser.

11. Subito dopo il `IImageContract` definizione, dichiarare un canale che eredita da entrambe le `IImageContract` e `IClientChannel` interfacce.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Un canale è l'oggetto WCF mediante il quale il servizio e il client passare informazioni tra loro. In un secondo momento, è necessario creare il canale nell'applicazione host. Servizio Bus utilizza quindi il canale per passare le richieste HTTP GET dal browser all'implementazione **GetImage** . Servizio Bus utilizza anche il canale per accettare il valore restituito **GetImage** e convertirla in un GETRESPONSE HTTP per il browser client.

12. Dal menu **Genera** scegliere **Genera soluzione** per verificare l'accuratezza del proprio lavoro finora.

### <a name="example"></a>Esempio

Il codice riportato di seguito mostra l'interfaccia di base che definisce un contratto di servizio Bus.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Passaggio 3: Implementare un contratto di servizio WCF basati su REST usare Bus di servizio

Creazione di un servizio Bus di servizio resto stile, è necessario creare innanzitutto il contratto, definito mediante un'interfaccia. Il passaggio successivo consiste nel implementare l'interfaccia. Questa operazione consiste nel creare una classe denominata **ImageService** che implementa l'interfaccia di **IImageContract** definite dall'utente. Dopo avere implementato il contratto, configurare l'interfaccia utilizzando un file di App. File di configurazione contiene le informazioni necessarie per l'applicazione, ad esempio il nome del servizio, il nome del contratto e il tipo di protocollo utilizzato per comunicare con Bus di servizio. Il codice utilizzato per le attività seguenti viene fornito nell'esempio seguendo la procedura descritta.

Come con i passaggi precedenti, non c'è molto piccolo differenza tra l'implementazione di un contratto resto stile e un contratto di servizio Bus base.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Per implementare un contratto Bus di servizio resto stile

1. Creare una nuova classe denominata **ImageService** subito dopo la definizione dell'interfaccia **IImageContract** . La classe **ImageService** implementata l'interfaccia **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Analogamente alle altre interfacce, è possibile implementare la definizione in un altro file. Tuttavia, per questa esercitazione, l'implementazione viene visualizzato nello stesso file di definizione dell'interfaccia e `Main()` metodo.

2. Applicare l'attributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) alla classe **IImageService** per indicare che la classe è un'implementazione di un contratto WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Come detto in precedenza, questo spazio dei nomi non è uno spazio dei nomi tradizionale. Se, tuttavia, è parte dell'architettura di WCF che identifica il contratto. Per ulteriori informazioni, vedere l'argomento di [Nomi di contratto dati](https://msdn.microsoft.com/library/ms731045.aspx) nella documentazione WCF.

3. Aggiungere un'immagine jpg al progetto.  

    Si tratta di un'immagine visualizzata nel browser ricezione dal servizio. Pulsante destro del mouse del progetto, quindi fare clic su **Aggiungi**. Fare clic su **Elemento esistente**. Utilizzare la finestra di dialogo **Aggiungi elemento esistente** per esplorare un jpg appropriato e quindi fare clic su **Aggiungi**.

    Quando si aggiunge il file, assicurarsi che **Tutti i file** sia selezionata nell'elenco a discesa accanto al **nome del File:** campo. Il resto di questa esercitazione si presuppone che il nome dell'immagine "immagine. jpg". Se si dispone di un file diverso, sarà necessario rinominare l'immagine o modificare il codice per compensare.

4. Per assicurarsi che il servizio in esecuzione è possibile trovare il file di immagine, in **Esplora soluzioni** fare clic sul file immagine e quindi fare clic su **proprietà**. Nel riquadro **delle proprietà** impostare **Copia nella Directory di Output** su **Copia se più recente**.

5. Aggiungere un riferimento all'assembly **System.Drawing.dll** al progetto e aggiungere anche il seguente associato `using` istruzioni.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. In classe **ImageService** , aggiungere il seguente costruttore che carica la bitmap e prepara inviare al browser.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Subito dopo il codice precedente, aggiungere il metodo **GetImage** seguente nella classe **ImageService** per restituire un messaggio HTTP che contiene l'immagine.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Questa implementazione utilizza **MemoryStream** per recuperare l'immagine e prepararlo per la trasmissione nel browser. Verrà avviata la posizione del flusso da zero, dichiara il contenuto di flusso in formato jpeg e crea un flusso di informazioni.

8. Dal menu **Compila** fare clic su **Compila soluzione**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Per definire la configurazione per l'esecuzione del servizio web su Bus di servizio

1. In **Esplora soluzioni**fare doppio clic su **App** per aprirlo nell'editor di Visual Studio.

    **App.** simile a un file di configurazione WCF e include il nome del servizio, endpoint (ovvero, la posizione servizio Bus espone per i client e host comunicare con loro) e associazione (il tipo del protocollo che viene utilizzato per comunicare). La principale differenza è che l'endpoint del servizio configurato fa riferimento a un'associazione [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) , che non fa parte di .NET Framework.

2. Il `<system.serviceModel>` XML è un elemento WCF che consente di definire uno o più servizi. In questo caso, viene utilizzato per definire il nome e servizio endpoint. Nella parte inferiore della `<system.serviceModel>` elemento (ma si trovano all'interno `<system.serviceModel>`), aggiungere un `<bindings>` elemento con il contenuto seguente. Consente di definire le associazioni utilizzate nell'applicazione. È possibile definire più associazioni, ma per questa esercitazione si definisce una sola.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Questo passaggio consente di definire un'associazione servizio Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) con **relayClientAuthenticationType** impostata su **Nessuno**. Questa impostazione indica che un endpoint che utilizza l'associazione non richiede credenziali client.

3. Dopo la `<bindings>` elemento, aggiungere un `<services>` elemento. Analogamente alle associazioni, è possibile definire più servizi in un singolo file di configurazione. Per questa esercitazione, è comunque definire una sola.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Questo passaggio consente di configurare un servizio che utilizza l' impostazione predefinita definita in precedenza **webHttpRelayBinding**. Utilizza anche l'impostazione predefinita **sbTokenProvider**, che viene definita nel passaggio successivo.

4. Dopo la `<services>` elemento, creare un `<behaviors>` elemento con il contenuto seguente sostituendo "SAS_KEY" con la chiave di *Firma di Access condiviso* (SA) in precedenza ottenuto dal [portale di Azure][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Ancora nell'App, nel `<appSettings>` elemento, l'intera connessione valore stringa con la stringa di connessione in precedenza ottenuto dal portale di sostituzione. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. Dal menu **Genera** scegliere **Genera soluzione** per generare l'intera soluzione.

### <a name="example"></a>Esempio

Il codice seguente mostra implementazione contratto e servizio per un servizio basato su resto in esecuzione su Bus di servizio tramite l'associazione **WebHttpRelayBinding** .

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Nell'esempio seguente mostra il file app associato al servizio.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Passaggio 4: Ospitare il servizio WCF basati su REST per utilizzare Bus di servizio

Questo passaggio viene descritto come eseguire un servizio web usando un'applicazione console bus di servizio. Nell'esempio seguendo la procedura descritta viene fornito un elenco completo di codice scritto in questo passaggio.

### <a name="to-create-a-base-address-for-the-service"></a>Per creare un indirizzo di base per il servizio

1. Nel `Main()` dichiarazione della funzione, creare una variabile per archiviare lo spazio dei nomi del progetto Bus di servizio. Assicurarsi di sostituire `yourNamespace` con il nome dello spazio dei nomi del servizio è stato creato in precedenza.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Servizio Bus utilizza il nome dello spazio dei nomi per creare un URI univoco.

2. Creare un `Uri` istanza per l'indirizzo di base del servizio basato sullo spazio dei nomi.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Per creare e configurare l'host del servizio web

- Creare l'host del servizio web usando l'indirizzo URI creata in precedenza in questa sezione.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    L'host del servizio è l'oggetto WCF che crea un'istanza dell'applicazione host. In questo esempio passa il tipo di host che si desidera creare (un **ImageService**) e l'indirizzo al quale si desidera esporre l'applicazione host.

### <a name="to-run-the-web-service-host"></a>Per eseguire l'host del servizio web

1. Aprire il servizio.

    ```
    host.Open();
    ```
    Il servizio è in esecuzione.

2. Visualizzare un messaggio che indica che il servizio sia in esecuzione e come arrestare il servizio.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Al termine, chiudere l'host del servizio.

    ```
    host.Close();
    ```

## <a name="example"></a>Esempio

Nell'esempio seguente include il contratto di servizio e implementazione nei passaggi precedenti nell'esercitazione e ospita il servizio in un'applicazione console. Compilare il codice seguente in un file eseguibile denominato ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>Compilazione del codice

Dopo aver creato la soluzione, eseguire le operazioni seguenti per eseguire l'applicazione:

1. Premere **F5**o passare al percorso del file eseguibile (ImageListener\bin\Debug\ImageListener.exe) per eseguire il servizio. Mantenere l'app in esecuzione, poiché sono necessarie per eseguire il passaggio successivo.

2. Copiare e incollare l'indirizzo dal prompt dei comandi in un browser per visualizzare l'immagine.

3. Al termine, premere **INVIO** nella finestra prompt dei comandi per chiudere l'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Ora che è già stata creata un'applicazione che utilizza il servizio di inoltro Bus di servizio, vedere gli articoli seguenti per informazioni su messaggi inoltrati:

- [Panoramica dell'architettura di Azure Bus di servizio](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Come utilizzare il servizio di inoltro Bus](service-bus-dotnet-how-to-use-relay.md)

[Portale di Azure]: https://portal.azure.com