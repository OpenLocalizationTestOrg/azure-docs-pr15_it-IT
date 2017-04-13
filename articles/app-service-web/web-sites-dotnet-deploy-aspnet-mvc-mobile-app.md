<properties 
    pageTitle="Distribuire un'app web per dispositivi mobili ASP.NET MVC 5 nel servizio App Azure" 
    description="Esercitazione che illustra come distribuire un'app web al servizio App Azure utilizzo delle funzionalità per dispositivi mobili in applicazione web ASP.NET MVC 5." 
    services="app-service" 
    documentationCenter=".net" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="01/12/2016" 
    ms.author="cephalin;riande"/>


# <a name="deploy-an-aspnet-mvc-5-mobile-web-app-in-azure-app-service"></a>Distribuire un'app web per dispositivi mobili ASP.NET MVC 5 nel servizio App Azure

In questa esercitazione verrà fornite le nozioni di base creare un'applicazione web ASP.NET MVC 5 che è probabile vengano mobile e distribuire al servizio App Azure. Per questa esercitazione, è necessario [Visual Studio Express 2013 per Web] [ Visual Studio Express 2013] o Professional di Visual Studio se si è già presente. È possibile utilizzare [Visual Studio 2015] , ma le schermate sarà diverse ed è necessario utilizzare i modelli di 4 ASP.NET.

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-youll-build"></a>Che cosa, sarà necessario creare

Per questa esercitazione si aggiungerà funzionalità per dispositivi mobili all'applicazione di conferenza elenco semplice fornito nel [progetto di partenza][StarterProject]. La schermata seguente mostra le sessioni ASP.NET nell'applicazione completata visto in emulatore browser in strumenti di sviluppo di Internet Explorer 11 F12.

![][FixedSessionsByTag]

È possibile utilizzare gli strumenti di sviluppo di Internet Explorer 11 F12 e lo [strumento Fiddler] [ Fiddler] per facilitare il debug dell'applicazione. 

## <a name="skills-youll-learn"></a>Competenze che verranno fornite informazioni

Ecco cosa si apprenderanno:

-   Informazioni su come utilizzare Visual Studio 2013 per pubblicare l'applicazione web direttamente a un'app web nel servizio App Azure.
-   Utilizzano dei modelli di ASP.NET MVC 5 framework avvio CSS per migliorare la visualizzazione su dispositivi mobili
-   Come creare visualizzazioni specifici di dispositivi mobili per browser per dispositivi mobili specifici, ad esempio l'iPhone e Android di destinazione
-   Come creare visualizzazioni risponde (visualizzazioni che rispondono a diversi browser su dispositivi)

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

Configurare l'ambiente di sviluppo installando Azure SDK per .NET 2.5.1 o versione successiva. 

1. Per installare Azure SDK per .NET, fare clic sul collegamento. Se non si dispone di Visual Studio 2013 è ancora stato installato, verrà installato dal collegamento. In questa esercitazione richiede Visual Studio 2013. [Azure SDK per Visual Studio 2013][AzureSDKVs2013]
1. Nella finestra di installazione guidata piattaforma Web, fare clic su **installazione** e procedere con l'installazione.

È necessario anche un emulatore browser per dispositivi mobili. Verranno illustrate le operazioni seguenti:

-   Browser emulatore in [Strumenti di sviluppo di Internet Explorer 11 F12] [ EmulatorIE11] (utilizzato nel browser per dispositivi mobili in tutte le schermate). Impostazioni predefinite di stringa agente utente che ha per Windows Phone 8, Windows Phone 7 e l'iPad di Apple.
-   Browser emulatore in [Google Chrome DevTools][EmulatorChrome]. La presentazione contiene predefiniti per numerosi dispositivi Android, nonché Apple iPhone, iPad di Apple e Amazon Kindle Fire. Inoltre emula eventi tocco.
-   [Opera emulatore per dispositivi mobili][EmulatorOpera]

Progetti di Visual Studio con C\# codice sorgente sono disponibili per accompagnare in questo argomento:

-   [Download di progetto starter][StarterProject]
-   [Completato il download di project][CompletedProject]

##<a name="bkmk_DeployStarterProject"></a>Distribuire il progetto di partenza per un'app web Azure

1.  Scaricare l' applicazione conferenza elenco [progetto di partenza][StarterProject].

2.  In Esplora risorse, quindi destro del mouse sul file ZIP scaricato e quindi scegliere *proprietà*.

3.  Nella finestra di dialogo **proprietà** scegliere il pulsante **Sblocca** . (Sblocco impedisce a un avviso di sicurezza che si verifica quando si tenta di utilizzare un file *zip* scaricato dal web).

4.  Fare clic sul file ZIP e selezionare **Estrai tutto** per decomprimere il file. 

5.  In Visual Studio, aprire il file *C#\Mvc5Mobile.sln* .

6.  In Esplora soluzioni fare clic sul progetto e fare clic su **pubblica**.

    ![][DeployClickPublish]

7.  Pubblicazione Web, fare clic su **Servizio di Microsoft Azure App**.

    ![][DeployClickWebSites]

8.  Se ancora stato ancora eseguito l'accesso Azure, fare clic su **Aggiungi un account**.

    ![][DeploySignIn]

9.  Seguire le istruzioni per accedere all'account di Azure.

11. La finestra di dialogo servizio App dovrebbe ora illustrano come eseguire l'accesso. Fare clic su **Nuovo**.

    ![][DeployNewWebsite]  

12. Nella casella **Nome applicazione Web** , specificare un prefisso di nome univoca per l'app. Il nome dell'app web completo sarà * &lt;prefisso >*. azurewebsites.net. Inoltre, selezionare o specificare il nome di un nuovo gruppo di risorse nel **gruppo di risorse**. Scegliere **Nuovo** per creare un nuovo piano di servizio di App.

    ![][DeploySiteSettings]

13. Configurare il nuovo piano di servizio di App e fare clic su **OK**. 

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7a.png)

13. Tornare nella finestra di dialogo Crea App servizio fare clic su **Crea**.

    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7b.png) 

13. Dopo la Azure risorse vengono create, finestra di dialogo venga riempita con le impostazioni per la nuova app pubblica Web. Fare clic su **pubblica**.

    ![][DeployPublishSite]

    Al termine della Visual Studio pubblicazione del progetto di partenza per l'applicazione web Azure, si apre il Visualizzatore desktop per visualizzare l'applicazione web live.

14. Avviare l'emulatore browser per dispositivi mobili, copiare l'URL per l'applicazione di una conferenza (*<prefix>*. azurewebsites.net) in emulatore, quindi fare clic sul pulsante superiore destro e scegliere **Sfoglia per tag**. Se si usa Internet Explorer 11 come browser predefinito, è sufficiente digitare `F12`, quindi `Ctrl+8`e quindi modificare il profilo del browser per **Windows Phone**. Nell'immagine seguente mostra la visualizzazione *AllTags* in modalità verticale (da scegliere **Sfoglia per tag**).

    ![][AllTags]

>[AZURE.TIP] Mentre è possibile eseguire il debug l'applicazione MVC 5 in Visual Studio, è possibile pubblicare un'applicazione web in Azure nuovamente per verificare l'app web live direttamente dal browser per dispositivi mobili o un emulatore browser.

La visualizzazione è molto leggibile in un dispositivo mobile. È possibile visualizzare anche già alcuni effetti visivi applicati da framework avvio CSS.
Fare clic sul collegamento **ASP.NET** .

![][SessionsByTagASP.NET]

La visualizzazione di tag ASP.NET è installata zoom sullo schermo, avvio esegue automaticamente per l'utente. Tuttavia, è possibile migliorare la visualizzazione in modo da adattarlo browser per dispositivi mobili. Ad esempio, la colonna **Data** è difficile da leggere. In un secondo momento nell'esercitazione è necessario modificare la visualizzazione di *AllTags* per rendere mobile descrittivo.

##<a name="bkmk_bootstrap"></a>Framework avvio CSS

Novità di 5 MVC modello è il supporto di avvio incorporato. È già stato come migliora immediatamente le diverse visualizzazioni all'interno dell'applicazione. Ad esempio, la barra di spostamento nella parte superiore è comprimibile automaticamente quando la larghezza del browser è inferiore. Nel browser desktop, provare a ridimensionare la finestra del browser e vedere come la barra di spostamento assume l'aspetto. Verrà visualizzata la progettazione web risponde integrato in avvio.

Per visualizzare l'aspetto dell'app Web senza avvio, aprire *App\_avviare\\BundleConfig.cs* e commento le righe che contengono *bootstrap.js* e *bootstrap.css*. Il codice seguente mostra le ultime due istruzioni del `RegisterBundles` metodo dopo la modifica:

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

Premere `Ctrl+F5` per eseguire l'applicazione.

Osservare la barra di spostamento comprimibile è ora solo un elenco non ordinato normale. Fare nuovamente clic su **Sfoglia per tag** e quindi fare clic su **ASP.NET**.
Nella visualizzazione per dispositivi mobili emulatore noterà che non è più installato zoom sullo schermo, ed è necessario scorrere lateralmente per visualizzare il lato destro della tabella.

![][SessionsByTagASP.NETNoBootstrap]

Annullare le modifiche e aggiornare il browser per dispositivi mobili per verificare che la visualizzazione orientata al cellulare è stata ripristinata.

Avvio non è specifico di ASP.NET MVC 5 e possono usufruire di queste funzionalità in qualsiasi applicazione web. Ma ora è incorporato in modello di progetto ASP.NET MVC 5, in modo che l'applicazione Web MVC 5 possibile usufruire di avvio per impostazione predefinita.

Per ulteriori informazioni sull'avvio, passare a [avvio] [ BootstrapSite] sito.

Nella sezione successiva verrà visualizzata come fornire visualizzazioni specifiche del browser mobile.

##<a name="bkmk_overrideviews"></a>Ignorare le visualizzazioni, layout e visualizzazioni parziali

È possibile ignorare una qualsiasi visualizzazione (inclusi i layout e visualizzazioni parziali) per browser per dispositivi mobili in generale, per un browser per dispositivi mobili singoli o per qualsiasi browser specifico. Per fornire una visualizzazione specifica mobile, è possibile copiare un file di visualizzazione e aggiungere *. Cellulare* al nome del file. Per creare una visualizzazione di *indice* per dispositivi mobili, ad esempio, è possibile copiare *visualizzazioni\\Home\\Index.cshtml* a *visualizzazioni\\Home\\Index.Mobile.cshtml*.

In questa sezione, sarà necessario creare un file di layout mobile specifico.

Per iniziare, copiare *visualizzazioni\\Shared\\\_Layout.cshtml* a *visualizzazioni\\Shared\\\_Layout.Mobile.cshtml*. Apri * \_Layout.Mobile.cshtml* e modificare il titolo da **Applicazione MVC5** **all'Applicazione MVC5 (cellulare)**.

In ogni `Html.ActionLink` chiamata per la barra di spostamento, rimuovere "Sfoglia per" in ogni collegamento *ActionLink*. Il codice seguente mostra la completa `<ul class="nav navbar-nav">` tag del file di layout per dispositivi mobili.

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

Copia il *visualizzazioni\\Home\\AllTags.cshtml* file *visualizzazioni\\Home\\AllTags.Mobile.cshtml*. Aprire il nuovo file e modificare il `<h2>` elemento da "Tag" a "tag (M)":

    <h2>Tags (M)</h2>

Passare alla pagina contrassegni utilizzando un browser desktop ed emulatore browser per dispositivi mobili. Emulatore browser per dispositivi mobili Mostra due modifiche (il titolo da * \_Layout.Mobile.cshtml* e il titolo da *AllTags.Mobile.cshtml*).

![][AllTagsMobile_LayoutMobile]

Invece la visualizzazione del desktop non è stato modificato (con titoli da * \_Layout.cshtml* e *AllTags.cshtml*).

![][AllTagsMobile_LayoutMobileDesktop]

##<a name="bkmk_browserviews"></a>Creazione di visualizzazioni specifiche del Browser

Oltre alle visualizzazioni specifiche di desktop e mobile, è possibile creare visualizzazioni per un determinato browser. Ad esempio, è possibile creare visualizzazioni specifici per il browser Android o iPhone. In questa sezione, sarà necessario creare un layout per il browser iPhone e una versione iPhone della visualizzazione *AllTags* .

Aprire *il file* e aggiungere il codice seguente alla fine della `Application_Start` metodo.

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

Questo codice definisce una nuova modalità di visualizzazione denominata "iPhone" corrispondente in base alle richieste in arrivo. Se la richiesta in ingresso soddisfa la condizione che è stato definito (ovvero, se l'agente utente contiene la stringa "iPhone"), MVC ASP.NET cercherà visualizzazioni il cui nome contiene il suffisso "iPhone".

>[AZURE.NOTE] Quando si aggiungono le modalità di visualizzazione specifiche del browser per dispositivi mobili, ad esempio per iPhone e Android, assicurarsi di impostare il primo argomento `0` (inserire nella parte superiore dell'elenco) per assicurarsi che la modalità specifiche del browser avrà la priorità sul modello per dispositivi mobili (*. Mobile.cshtml). Se il modello mobile è invece nella parte superiore dell'elenco, verrà selezionata la modalità di visualizzazione desiderata (il primo wins Confronta e il modello portatile corrisponde a tutti i browser per dispositivi mobili). 

Nel codice del pulsante destro del mouse `DefaultDisplayMode`, scegliere **Risolvi**, quindi `using System.Web.WebPages;`. Aggiunge un riferimento alla `System.Web.WebPages` spazio dei nomi, dove il `DisplayModeProvider` e `DefaultDisplayMode` tipi sono definiti.

![][ResolveDefaultDisplayMode]

In alternativa, è possibile aggiungere solo manualmente la riga seguente per il `using` sezione del file.

    using System.Web.WebPages;

Salvare le modifiche. Copia il *visualizzazioni\\Shared\\\_Layout.Mobile.cshtml* file *visualizzazioni\\Shared\\\_Layout.iPhone.cshtml*. Aprire il nuovo file e quindi modificare il titolo da `MVC5 Application (Mobile)` a `MVC5 Application (iPhone)`.

Copia il *visualizzazioni\\Home\\AllTags.Mobile.cshtml* file *visualizzazioni\\Home\\AllTags.iPhone.cshtml*. Nel nuovo file, modificare il `<h2>` elemento da "tag (M)" a "Tag (iPhone)".

Eseguire l'applicazione. Eseguire un emulatore browser per dispositivi mobili, verificare che l'agente utente è impostata su "iPhone" e passare alla visualizzazione *AllTags* . Se si utilizza l'emulatore in strumenti di sviluppo di Internet Explorer 11 F12, configurare emulazione per le operazioni seguenti:

-   Profilo del browser = **Windows Phone**
-   Stringa agente utente = **personalizzata**
-   Stringa personalizzata = **Apple-iPhone5C1/1001.525**

La schermata seguente mostra la visualizzazione *AllTags* rendering in emulatore in strumenti di sviluppo di Internet Explorer 11 F12 con la stringa agente utente personalizzato (si tratta di una stringa di agente utente iPhone 5 C).

![][AllTagsIPhone_LayoutIPhone]

Nel browser per dispositivi mobili, selezionare il collegamento di **altoparlanti** . Perché non è una visualizzazione per dispositivi mobili (*AllSpeakers.Mobile.cshtml*), la visualizzazione di altoparlanti predefinita (*AllSpeakers.cshtml*) viene visualizzata utilizzando la visualizzazione layout di dispositivi mobili (*\_Layout.Mobile.cshtml*). Come illustrato di seguito, il titolo **Dell'applicazione MVC5 (cellulare)** sia definito nel * \_Layout.Mobile.cshtml*.

![][AllSpeakers_LayoutMobile]

È possibile disattivare globalmente una visualizzazione (non mobile) predefinita dal rendering all'interno di un layout per dispositivi mobili mediante l'impostazione `RequireConsistentDisplayMode` a `true` nel *visualizzazioni\\\_ViewStart.cshtml* file, ad esempio seguente:

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

Quando `RequireConsistentDisplayMode` è impostato su `true`, il layout per dispositivi mobili (*\_Layout.Mobile.cshtml*) vengono usate solo per le visualizzazioni per dispositivi mobili (ad esempio quando il file della visualizzazione del modulo * **ViewName**. Mobile.cshtml*). È consigliabile impostare `RequireConsistentDisplayMode` a `true` se il layout per dispositivi mobili non funziona bene con le visualizzazioni non mobile. Nella schermata seguente viene illustrato il rendering della pagina di *altoparlanti* quando `RequireConsistentDisplayMode` è impostato su `true` (senza la stringa "(cellulare)" nella barra di spostamento nella parte superiore).

![][AllSpeakers_LayoutMobileOverridden]

È possibile disattivare la modalità di visualizzazione coerente in una visualizzazione specifica mediante l'impostazione `RequireConsistentDisplayMode` a `false` nel file di visualizzazione. Il markup seguente nel *visualizzazioni\\Home\\AllSpeakers.cshtml* file imposta `RequireConsistentDisplayMode` a `false`:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

In questa sezione abbiamo visto come creare visualizzazioni e i layout per dispositivi mobili e su come creare visualizzazioni per dispositivi specifici, ad esempio iPhone e i layout.
Il vantaggio principale di framework CSS avvio invece il layout risponde, il che significa che è possibile applicare un singolo foglio di stile diverse desktop, telefono e tablet browser per creare un aspetto più coerente. Nella sezione successiva verrà visualizzata come sfruttare avvio per creare viste compatibili con dispositivi mobili.

##<a name="bkmk_Improvespeakerslist"></a>Migliorare l'elenco degli altoparlanti

Come si è appena visto, la visualizzazione *degli altoparlanti* è leggibile, ma i collegamenti sono piccoli e sono difficili da toccare in un dispositivo mobile. In questa sezione, si verrà renderla *AllSpeakers* orientata mobile, che consente di visualizzare i collegamenti di grandi dimensioni, semplice tocco e contiene una casella di ricerca per trovare rapidamente gli altoparlanti.

È possibile utilizzare l'applicazione di stili avvio [gruppo elenco collegato][] per migliorare la visualizzazione *degli altoparlanti* . In *visualizzazioni\\Home\\AllSpeakers.cshtml*, sostituire il contenuto del file Razor con il codice riportato di seguito.

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

Il `class="list-group"` attributo la `<div>` tag applica lo stile elenco avvio e la `class="input-group-item"` attributo si applica stili elemento di elenco avvio a ogni collegamento.

Aggiornare il browser per dispositivi mobili. La visualizzazione aggiornata è simile alla seguente:

![][AllSpeakersFixed]

Lo stile di avvio [gruppo elenco collegato][] rende l'intera finestra per ogni collegamento selezionabile, ovvero una quantità migliore esperienza utente. Passare alla visualizzazione desktop e osservare l'aspetto.

![][AllSpeakersFixedDesktop]

Anche se è stato migliorato la visualizzazione browser per dispositivi mobili, è difficile esplorazione lungo elenco di altoparlanti. Avvio non fornisce una ricerca filtro funzionalità-the-pronte, ma è possibile aggiungere con alcune righe di codice. Verrà prima di tutto aggiungere una casella di ricerca alla visualizzazione, quindi agganciare con il codice JavaScript per la funzione filtro. In *visualizzazioni\\Home\\AllSpeakers.cshtml*, aggiungere un \<modulo\> tag subito dopo il \<h2\> tag, come illustrato di seguito:

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

Si noti che la `<form>` e `<input>` entrambi i contrassegni sono gli stili avvio applicati a tali proprietà. Il `<span>` elemento aggiunge un avvio [glyphicon][] alla casella di ricerca.

Aggiungere un file JavaScript denominato *filter.js*nella cartella *script* . Aprire il file e incollare il codice seguente:

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

Inoltre, è necessario includere filter.js nei pacchetti registrati. Apri *App\_avviare\\BundleConfig.cs* e modificare le aggregazioni prima. Modificare la prima `bundles.Add` istruzione (per il raggruppamento **jquery** ) per includere *script\\filter.js*, come indicato di seguito:

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

Raggruppamento di **jquery** già è stato eseguito il rendering per l'impostazione predefinita * \_Layout* visualizzazione. In un secondo momento, è possibile utilizzare lo stesso codice JavaScript per applicare la funzionalità di filtro ad altre visualizzazioni elenco.

Aggiornare il browser per dispositivi mobili e passare alla visualizzazione *AllSpeakers* . Nella casella di ricerca, digitare "sc". L'elenco di utenti che parlano ora deve essere filtrato in base alla stringa di ricerca.

![][AllSpeakersFixedSearchBySC]

##<a name="bkmk_improvetags"></a>Migliorare l'elenco di contrassegni

Ad esempio la visualizzazione di *altoparlanti* , la visualizzazione di *tag* è leggibile, ma i collegamenti sono piccolo e difficile da toccare in un dispositivo mobile. È possibile correggere la visualizzazione di *tag* nello stesso modo risolvere la visualizzazione di *altoparlanti* , se si utilizzano le modifiche al codice descritte in precedenza, ma con i seguenti `Html.ActionLink` sintassi del metodo in *visualizzazioni\\Home\\AllTags.cshtml*:

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

Il browser desktop aggiornato simile al seguente:

![][AllTagsFixedDesktop]

E il browser per dispositivi mobili aggiornati è simile al seguente: 

![][AllTagsFixed]

>[AZURE.NOTE] Se si nota che la formattazione dell'elenco originale è ancora presente nel browser per dispositivi mobili e chiedersi Dov'è l'applicazione di stili avvio pile, si tratta di un elemento dell'azione precedente per creare visualizzazioni specifiche per dispositivi mobili. Tuttavia, ora che si utilizza il framework avvio CSS per creare una progettazione web risponde, andare a capo e rimuovere queste visualizzazioni specifici di dispositivi mobili e le visualizzazioni layout mobile specifico. Dopo avere fatto, il browser per dispositivi mobili aggiornati verranno visualizzati gli stili di avvio.

##<a name="bkmk_improvedates"></a>Migliorare l'elenco di date

È possibile migliorare la visualizzazione di *date* come migliorata di visualizzazioni *altoparlanti* e il *tag* se si utilizzano le modifiche al codice descritte in precedenza, ma con i seguenti `Html.ActionLink` sintassi del metodo in *visualizzazioni\\Home\\AllDates.cshtml*:

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

Si riceverà una visualizzazione browser per dispositivi mobili aggiornati simile alla seguente:

![][AllDatesFixed]

È possibile migliorare ulteriormente la visualizzazione di *date* organizzando i valori di data e ora per Data. Questa operazione può essere eseguita con lo stile di avvio [pannelli][] . Sostituire il contenuto del *visualizzazioni\\Home\\AllDates.cshtml* file con il codice seguente:

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

Questo codice viene creato un separato `<div class="panel panel-primary">` tag per ciascuna data distinta nell'elenco e utilizzerà il [gruppo di elenco collegato][] per i collegamenti ai rispettivi come prima. Ecco il browser per dispositivi mobili l'aspetto quando si esegue questo codice:

![][AllDatesFixed2]

Passare al browser desktop. Nota nuovo aspetto coerente.

![][AllDatesFixed2Desktop]

##<a name="bkmk_improvesessionstable"></a>Migliorare la visualizzazione SessionsTable

In questa sezione, è necessario creare una visualizzazione *SessionsTable* più mobile orientata al. Questa modifica è più estesa delle modifiche precedente.

Nel browser per dispositivi mobili, toccare il pulsante **Tag** e quindi immettere `asp` nella casella di ricerca.

![][AllTagsFixedSearchByASP]

Toccare il collegamento **ASP.NET** .

![][SessionsTableTagASP.NET]

Come si può notare, la visualizzazione viene formattata come una tabella in cui è attualmente progettata per essere visualizzata nel browser desktop. Tuttavia, è un po' difficile da leggere in un browser per dispositivi mobili. Per risolvere il problema, aprire *visualizzazioni\\Home\\SessionsTable.cshtml* e quindi sostituire il contenuto del file con il codice seguente:

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

Il codice non 3 elementi:

-   utilizza avvio [gruppo personalizzato elenco collegate][] per formattare le informazioni di sessione in verticale, in modo che tutte le informazioni sono leggibile in un browser per dispositivi mobili (mediante le classi, ad esempio testo elementi di gruppo dell'elenco)
-   Applica il [sistema di griglia][] per il layout, in modo che gli elementi di sessione orizzontalmente nel browser desktop e verticalmente nel browser per dispositivi mobili (usando la classe col-md-4)
-   utilizza le [utilità risponde][] per nascondere i tag sessione se visualizzati nel browser per dispositivi mobili (usando la classe x nascosti)

È anche possibile toccare un collegamento del titolo per passare alla sessione rispettiva. Nell'immagine seguente riflette le modifiche al codice.

![][FixedSessionsByTag]

Il sistema di avvio della griglia che è stato applicato automaticamente dispone le sessioni in senso verticale in browser per dispositivi mobili. Si noti inoltre che non vengono visualizzati i contrassegni. Passare al browser desktop.

![][SessionsTableFixedTagASP.NETDesktop]

Nel browser desktop, si noti che i contrassegni che vengono visualizzati. Vedere anche che il sistema di avvio griglia che è stato applicato dispone gli elementi di sessione in due colonne. Se si ingrandisce il browser, si noterà che la disposizione impostato su in tre colonne.

##<a name="bkmk_improvesessionbycode"></a>Migliorare la visualizzazione SessionByCode

Infine, si risoluzione la visualizzazione di *SessionByCode* per rendere mobile descrittivo.

Nel browser per dispositivi mobili, toccare il pulsante **Tag** e quindi immettere `asp` nella casella di ricerca.

![][AllTagsFixedSearchByASP]

Toccare il collegamento **ASP.NET** . Vengono visualizzate le sessioni per il tag ASP.NET.

![][FixedSessionsByTag]

Selezionare il collegamento di **creazione di una singola applicazione di pagina con ASP.NET e AngularJS** .

![][SessionByCode3-644]

La visualizzazione del desktop predefinita è fitta, ma è possibile migliorare l'aspetto facilmente tramite alcuni componenti di grafica di avvio.

Apri *visualizzazioni\\Home\\SessionByCode.cshtml* e sostituire il contenuto con il markup seguente:

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

Il nuovo tag utilizza pannelli avvio gli stili per migliorare la visualizzazione versione mobile. 

Aggiornare il browser per dispositivi mobili. Nella figura seguente riflette le modifiche al codice appena apportate:

![][SessionByCodeFixed3-644]

## <a name="wrap-up-and-review"></a>Riepilogare e rivedere

In questa esercitazione è illustrato come utilizzare ASP.NET MVC 5 per lo sviluppo di applicazioni Web orientata mobile. Sono inclusi:

-   Distribuire un'applicazione ASP.NET MVC 5 a un servizio di App web app
-   Consente di creare layout web risponde nell'applicazione MVC 5 avvio
-   Sostituiscono i layout, visualizzazioni e visualizzazioni parziali, sia a livello globale e per una singola visualizzazione
-   Layout per i controlli e parziale ignorare imposizione mediante la `RequireConsistentDisplayMode` proprietà
-   Creare visualizzazioni che utilizzano browser specifici, ad esempio il browser iPhone
-   Applicare gli stili di avvio nel codice Razor

## <a name="see-also"></a>Vedere anche

-   [principi di base 9 di progettazione web risponde](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
-   [Avvio][BootstrapSite]
-   [Blog di avvio ufficiale][]
-   [Esercitazione avvio Twitter dalla Repubblica esercitazione][]
-   [Avvio test][]
-   [W3C Recommendation Web per dispositivi mobili procedure consigliate per applicazioni][]
-   [W3C Recommendation candidati per le query di elementi multimediali][]

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]:#bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[gruppo elenco collegato]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[pannelli]: http://getbootstrap.com/components/#panels
[gruppo personalizzato elenco collegato]: http://getbootstrap.com/components/#list-group-custom-content
[sistema griglia]: http://getbootstrap.com/css/#grid
[utilità risponde]: http://getbootstrap.com/css/#responsive-utilities
[Blog di avvio ufficiale]: http://blog.getbootstrap.com/
[Esercitazione avvio Twitter dalla Repubblica esercitazione]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[Avvio test]: http://www.bootply.com/
[W3C Recommendation Web per dispositivi mobili procedure consigliate per applicazioni]: http://www.w3.org/TR/mwabp/
[W3C Recommendation candidati per le query di elementi multimediali]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png
 
