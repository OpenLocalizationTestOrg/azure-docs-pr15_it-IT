<properties
   pageTitle="Creazione di un front-end web per l'applicazione mediante ASP.NET di base | Microsoft Azure"
   description="Esporre l'applicazione di servizio tessuti sul Web con un progetto ASP.NET Core Web API e comunicazione tra i servizi tramite ServiceProxy."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/11/2016"
   ms.author="seanmck"/>


# <a name="build-a-web-service-front-end-for-your-application-using-aspnet-core"></a>Creazione di un front-end di web servizio per l'applicazione mediante ASP.NET di base

Per impostazione predefinita, i servizi di Azure servizio tessuti non forniscono un'interfaccia pubblica sul Web. Per esporre funzionalità dell'applicazione client HTTP, sarà necessario creare un progetto web per fungere da punto di ingresso e quindi comunicare da tale posizione per i singoli servizi.

In questa esercitazione verrà sollevare in cui ci si trovava l'esercitazione [creazione di un'applicazione in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md) e aggiungere un servizio web che precede il servizio informazioni sullo stato del contatore. Se non è già stato fatto, è necessario tornare indietro e scorrere tale esercitazione prima di tutto.

## <a name="add-an-aspnet-core-service-to-your-application"></a>Aggiunta di un servizio ASP.NET di base per l'applicazione

ASP.NET di base è una struttura di sviluppo web leggera e multipiattaforma che è possibile utilizzare per creare l'interfaccia utente moderna web e web API. Aggiungere un progetto API Web ASP.NET all'applicazione esistente.

>[AZURE.NOTE] Per completare questa esercitazione, è necessario [installare .NET Core 1.0][dotnetcore-install].

1. In Esplora soluzioni rapida **servizi** all'interno del progetto di applicazione e scegliere **Aggiungi > nuovo servizio tessuti**.

    ![Aggiunta di un nuovo servizio a un'applicazione esistente][vs-add-new-service]

2. Nella pagina **Crea un servizio** scegliere **ASP.NET di base** e assegnarle un nome.

    ![Scelta di servizio web ASP.NET nella finestra di dialogo nuovo servizio][vs-new-service-dialog]

3. Nella pagina successiva fornisce un set di ASP.NET di base di modelli di progetto. Si noti che sono gli stessi modelli che si vogliono visualizzare se è stato creato un progetto principale ASP.NET all'esterno di un'applicazione di servizio tessuti. Per questa esercitazione verrà scelto **API Web**. Tuttavia, è possibile applicare gli stessi concetti alla creazione di un'applicazione web completo.

    ![Scelta del tipo di progetto ASP.NET][vs-new-aspnet-project-dialog]

    Dopo aver creato il progetto API Web, sarà necessario due servizi dell'applicazione. Mentre si continua a compilare l'applicazione, aggiungere altri servizi nello stesso modo. Ognuno può essere in modo indipendente versione e aggiornate.

>[AZURE.TIP] Per ulteriori informazioni sulla creazione di servizi ASP.NET di base, vedere la [Documentazione di base di ASP.NET](https://docs.asp.net).

## <a name="run-the-application"></a>Eseguire l'applicazione

Per avere un'idea di abbiamo, distribuire la nuova applicazione e dare un'occhiata il comportamento predefinito che contiene un modello API Web Core ASP.NET.

1. Premere F5 in Visual Studio per eseguire il debug dell'app.

2. Una volta completata la distribuzione, Visual Studio verrà avviato il browser nella radice del servizio di API Web ASP.NET, ad esempio http://localhost:33003. Il numero di porta in modo casuale assegnato e può essere diverso nel computer in uso. Il modello API Web Core ASP.NET non fornisce il comportamento predefinito per la radice, in modo che verrà visualizzato un errore nel browser.

3. Aggiungere `/api/values` nella posizione nel browser. Verrà visualizzata la `Get` metodo su ValuesController nel modello di API Web. Restituisce la risposta predefinita forniti dal modello: una matrice JSON che contiene due stringhe:

    ![Valori predefiniti restituiti da modello API Web Core ASP.NET][browser-aspnet-template-values]

    Al termine dell'esercitazione, si verranno sono sostituite questi valori predefiniti con il valore del contatore più recente dal servizio informazioni sullo stato.


## <a name="connect-the-services"></a>Connettersi ai servizi

Servizio tessuti flessibilità completa in modo si comunica servizi affidabili. In una singola applicazione, potrebbe essere servizi accessibili tramite TCP, altri servizi accessibili tramite un'API REST HTTP e ancora altri servizi accessibili tramite socket web. Per informazioni sulle opzioni disponibili e sui compromessi nella correlate, vedere [comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md). In questa esercitazione verranno seguire uno degli approcci più semplici e utilizzare la `ServiceProxy` / `ServiceRemotingListener` classi che vengono fornite in SDK.

Nel `ServiceProxy` approccio (basato su chiamate di procedura remota o RPC), è possibile definire un'interfaccia come contratto pubblico per il servizio. È quindi possibile utilizzare l'interfaccia per generare una classe proxy per interagire con il servizio.


### <a name="create-the-interface"></a>Creare l'interfaccia

Iniziamo creando l'interfaccia come il contratto tra il servizio informazioni sullo stato e i relativi client, incluso il progetto ASP.NET di base.

1. In Esplora soluzioni fare doppio clic la soluzione e scegliere **Aggiungi** > **Nuovo progetto**.

2. Scegliere la voce **c#** nel riquadro di spostamento a sinistra e quindi selezionare il modello **Libreria** . Assicurarsi che la versione di .NET Framework sia impostata su **4.5.2**.

    ![Creazione di un progetto di interfaccia per il servizio informazioni sullo stato][vs-add-class-library-project]

3. Affinché l'interfaccia sia utilizzabile da `ServiceProxy`, è necessario derivano dall'interfaccia IService. Questa interfaccia è incluso in uno dei pacchetti servizio tessuti NuGet. Per aggiungere il pacchetto, il pulsante destro del nuovo progetto libreria di classi e scegliere **Gestisci pacchetti NuGet**.

4. Cercare il pacchetto di **Microsoft.ServiceFabric.Services** e installarlo.

    ![Aggiunta del pacchetto NuGet servizi][vs-services-nuget-package]

5. Nella libreria di classi, creare un'interfaccia con un unico metodo `GetCountAsync`, ed estendere l'interfaccia da IService.

    ```c#
    namespace MyStatefulService.Interfaces
    {
        using Microsoft.ServiceFabric.Services.Remoting;

        public interface ICounter: IService
        {
            Task<long> GetCountAsync();
        }
    }
    ```


### <a name="implement-the-interface-in-your-stateful-service"></a>Implementare l'interfaccia del servizio informazioni sullo stato

Una volta definita l'interfaccia, è necessario implementare nel servizio informazioni sullo stato.

1. Aggiungere un riferimento al progetto libreria di classi che contiene l'interfaccia del servizio informazioni sullo stato.

    ![Aggiunta di un riferimento al progetto libreria di classi nel servizio informazioni sullo stato][vs-add-class-library-reference]

2. Individuare la classe da cui eredita `StatefulService`, ad esempio `MyStatefulService`, ed estendere per implementare il `ICounter` interfaccia.

    ```c#
    using MyStatefulService.Interfaces;

    ...

    public class MyStatefulService : StatefulService, ICounter
    {        
          // ...
    }
    ```

3. A questo punto implementare l'unico metodo definita nel `ICounter` interfaccia, `GetCountAsync`.

    ```c#
    public async Task<long> GetCountAsync()
    {
      var myDictionary =
        await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

        using (var tx = this.StateManager.CreateTransaction())
        {          
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");
            return result.HasValue ? result.Value : 0;
        }
    }
    ```


### <a name="expose-the-stateful-service-using-a-service-remoting-listener"></a>Esporre il servizio informazioni sullo stato utilizzando un comunicare ascoltatore remoti di servizio

Con la `ICounter` interfaccia implementata, il passaggio finale consente al servizio informazioni sullo stato di essere chiamato da altri servizi consiste nell'aprire un canale di comunicazione. Per informazioni sullo stato servizi, servizio tessuti fornisce un metodo di override denominato `CreateServiceReplicaListeners`. Con questo metodo, è possibile specificare uno o più listener di comunicazione, in base al tipo di comunicazione che si desidera attivare il servizio.

>[AZURE.NOTE] Comando si chiama il metodo equivalente per l'apertura di un canale di comunicazione ai servizi senza informazioni sullo stato `CreateServiceInstanceListeners`.

In questo caso, si sostituirà esistente `CreateServiceReplicaListeners` metodo e specificare un'istanza di `ServiceRemotingListener`, che consente di creare un endpoint RPC che può essere chiamata da client tramite `ServiceProxy`.  

```c#
using Microsoft.ServiceFabric.Services.Remoting.Runtime;

...

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new List<ServiceReplicaListener>()
    {
        new ServiceReplicaListener(
            (context) =>
                this.CreateServiceRemotingListener(context))
    };
}
```


### <a name="use-the-serviceproxy-class-to-interact-with-the-service"></a>Utilizzare la classe ServiceProxy per interagire con il servizio

Servizio informazioni sullo stato è ora pronto per ricevere il traffico da altri servizi. In modo che rimane consiste nell'aggiungere il codice di comunicare dal servizio web ASP.NET.

1. Nel progetto ASP.NET, aggiungere un riferimento alla libreria di classi che contiene il `ICounter` interfaccia.

2. Dal menu **Compila** aprire **Gestione configurazione**. Dovrebbero essere visualizzate simile al seguente:

    ![Libreria di classi di configurazione manager che mostra come AnyCPU][vs-configuration-manager]

    Si noti che il progetto di libreria di classi **MyStatefulService.Interface**sia configurato per creare per qualsiasi CPU. Per il corretto funzionamento con tessuti servizio, è necessario essere in modo esplicito destinato x64. Fare clic sull'elenco a discesa piattaforma e scegliere **Nuovo**e quindi crea un x64 configurazione della piattaforma.

    ![Creazione di nuova piattaforma per libreria di classi][vs-create-platform]

3. Aggiungere il pacchetto di Microsoft.ServiceFabric.Services al progetto ASP.NET allo stesso modo per progetto libreria di classi in precedenza. In questo modo la `ServiceProxy` classe.

4. Nella cartella **controller** , aprire la `ValuesController` classe. Si noti che la `Get` metodo attualmente appena restituisce una matrice di stringa hardcoded di "Valore1" e "Valore2", che corrisponde a visto in precedenza nel browser. Sostituire l'implementazione con il codice seguente:

    ```c#
    using MyStatefulService.Interfaces;
    using Microsoft.ServiceFabric.Services.Remoting.Client;

    ...

    public async Task<IEnumerable<string>> Get()
    {
        ICounter counter =
            ServiceProxy.Create<ICounter>(new Uri("fabric:/MyApplication/MyStatefulService"), new ServicePartitionKey(0));

        long count = await counter.GetCountAsync();

        return new string[] { count.ToString() };
    }
    ```

    La prima riga di codice è una chiave. Per creare il proxy ICounter al servizio informazioni sullo stato, è necessario fornire due tipi di informazioni: un ID partizione e il nome del servizio.

    È possibile utilizzare partizioni ai servizi informazioni sullo stato scala da suddividere le loro stato in diversi intervalli, in base a una chiave definite dall'utente, ad esempio un ID cliente o CAP. Nell'applicazione di semplice, il servizio informazioni sullo stato possiede solo una partizione, in modo che il tasto non ha importanza. Qualsiasi tasto che fornisce, verrà visualizzata la stessa partizione. Per ulteriori informazioni su come partizione services, vedere [come partizione affidabile servizi tessuti](service-fabric-concepts-partitioning.md).

    Il nome del servizio è un URI dell'infrastruttura di modulo: /&lt;nome_applicazione&gt;/&lt;NOME_SERVIZIO&gt;.

    Con questi due tipi di informazioni, è possibile servizio tessuti identificare il computer in cui devono essere inviate le richieste. Il `ServiceProxy` classe gestisce anche diretta nel caso in cui si verifica un errore di computer che ospita la partizione di servizio informazioni sullo stato e un altro computer deve essere promossi per sostituirlo. Questa astrazione consente la scrittura di codice client di occuparsi di altri servizi in modo significativo più semplice.

    Una volta ottenuto il proxy, è sufficiente richiamare il `GetCountAsync` metodo e tornare al relativo risultato.

5. Premere F5 per eseguire l'applicazione modificata. Come prima, Visual Studio verrà automaticamente avviato il browser nella radice del progetto web. Aggiungere il percorso "api/valori" e verrà visualizzato il valore corrente del contatore restituito.

    ![Il valore di contatore informazioni sullo stato visualizzato nel browser][browser-aspnet-counter-value]

    Aggiornare il browser periodicamente per visualizzare il valore del contatore aggiornare.


>[AZURE.WARNING] Il server web ASP.NET di base fornito nel modello, noto come Kestrel, è [attualmente non supportata per la gestione del traffico internet diretto](https://docs.asp.net/en/latest/fundamentals/servers.html#kestrel). Per gli scenari di produzione, è consigliabile ospitare gli endpoint ASP.NET di base dietro [API Gestione] [ api-management-landing-page] o un altro gateway esposto a internet. Si noti che tessuti servizio non è supportato per la distribuzione in IIS.


## <a name="what-about-actors"></a>Informazioni sugli operatori?

In questa esercitazione incentrate sull'aggiunta di un front-end web che comunica con un servizio informazioni sullo stato. Tuttavia, è possibile eseguire un modello molto simile a comunicare con gli operatori. In realtà è un po' più semplice.

Quando si crea un progetto dell'attore, Visual Studio genera automaticamente un progetto dell'interfaccia di. È possibile utilizzare l'interfaccia per generare un proxy attore nel progetto web per comunicare con l'attore. Il canale di comunicazione viene fornito automaticamente. In modo che non occorre fare nulla che corrisponde alla creazione di un `ServiceRemotingListener` come si è fatto per il servizio informazioni sullo stato in questa esercitazione.

## <a name="how-web-services-work-on-your-local-cluster"></a>Funzionano dei servizi web sul cluster locale

In generale, è possibile distribuire esattamente la stessa applicazione di servizio tessuti a un cluster più computer in cui è stato distribuito il cluster locale e altamente la certezza che funziona come previsto. In questo modo il cluster locale è semplicemente una configurazione di cinque compressa in un unico computer.

Quando si tratta di servizi web, tuttavia è presente un gesto chiave. Quando il cluster dietro un bilanciamento del carico, come avviene in Azure, è necessario assicurarsi che i servizi web sono installati su ogni computer poiché bilanciamento del carico sarà il traffico semplicemente circolari tra il computer. È possibile eseguire questa operazione mediante l'impostazione di `InstanceCount` per il servizio sul valore speciale-"1".

Quando si esegue un servizio web in locale, è necessario assicurarsi che solo un'istanza di al contrario, viene eseguito il servizio. In caso contrario, viene eseguita in conflitti da più processi in ascolto sullo stesso percorso e porta. Di conseguenza, il conteggio delle istanze del servizio web imposti su "1" per le distribuzioni locali.

Per informazioni su come configurare valori diversi per ambiente diverso, vedere [gestione dell'applicazione parametri per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

## <a name="next-steps"></a>Passaggi successivi

- [Creare un cluster di Azure per distribuire l'applicazione nel cloud](service-fabric-cluster-creation-via-portal.md)
- [Acquisire familiarità con la comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md)
- [Ulteriori informazioni sulle partizioni servizi informazioni sullo stato](service-fabric-concepts-partitioning.md)

<!-- Image References -->

[vs-add-new-service]: ./media/service-fabric-add-a-web-frontend/vs-add-new-service.png
[vs-new-service-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-service-dialog.png
[vs-new-aspnet-project-dialog]: ./media/service-fabric-add-a-web-frontend/vs-new-aspnet-project-dialog.png
[browser-aspnet-template-values]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-template-values.png
[vs-add-class-library-project]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-project.png
[vs-add-class-library-reference]: ./media/service-fabric-add-a-web-frontend/vs-add-class-library-reference.png
[vs-services-nuget-package]: ./media/service-fabric-add-a-web-frontend/vs-services-nuget-package.png
[browser-aspnet-counter-value]: ./media/service-fabric-add-a-web-frontend/browser-aspnet-counter-value.png
[vs-configuration-manager]: ./media/service-fabric-add-a-web-frontend/vs-configuration-manager.png
[vs-create-platform]: ./media/service-fabric-add-a-web-frontend/vs-create-platform.png


<!-- external links -->
[dotnetcore-install]: https://www.microsoft.com/net/core#windows
[api-management-landing-page]: https://azure.microsoft.com/en-us/services/api-management/
