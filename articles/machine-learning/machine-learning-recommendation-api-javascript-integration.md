<properties 
    pageTitle="Machine Learning consigli: Integrazione JavaScript | Microsoft Azure" 
    description="Documentazione Machine Learning consigli - JavaScript integrazione - Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Azure apprendimento consigli - integrazione JavaScript

>[AZURE.NOTE]È necessario iniziare a usare il servizio di Cognitive API consigli invece di questa versione. Il servizio Cognitive consigli andrà a sostituire il servizio e tutte le nuove funzionalità da sviluppare sono. Offre nuove funzionalità, come il batch di supporto, un migliore API Explorer, un esperienza di superficie, più coerente l'iscrizione/fatturazione API chiaro e così via.
> Ulteriori informazioni sulla [migrazione al nuovo servizio cognitiva](http://aka.ms/recomigrate)


In questo documento illustrano come integrare sito usando JavaScript. Il codice JavaScript consente di inviare eventi di acquisizione dei dati e utilizzare consigli dopo aver creato un modello di recommendation. Tutte le operazioni eseguite mediante JS possono essere eseguite anche dal lato server.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. panoramica generale
Integrazione del sito con Azure ML consigli sono costituiti da sulle 2 fasi:

1.  Inviare gli eventi in Azure ML consigli. In questo modo per creare un modello di recommendation.
2.  Utilizzare i suggerimenti. Una volta creato il modello è possibile utilizzare i suggerimenti. (Questo documento non illustrano come creare un modello, leggere la Guida introduttiva per ottenere ulteriori informazioni su come).


<ins>Fase I</ins>

Nella prima fase inserire nelle pagine html una piccola libreria JavaScript che consente la pagina inviare gli eventi quando si verificano nella pagina html in server Azure ML consigli (tramite Data Market):

![Disegno1][1]

<ins>Fase II</ins>

Nella seconda fase quando si desidera visualizzare i suggerimenti nella pagina si seleziona una delle opzioni seguenti:

1 i server (in fase di rendering della pagina) chiama Azure ML consigli Server (tramite Data Market) per ottenere suggerimenti. I risultati includono un elenco di elementi id. Il server necessita migliorare i risultati con gli elementi dei metadati (ad esempio immagini, descrizione) e inviare la pagina creata nel browser.

![Drawing2][2]

2 l'altra opzione consiste nell'utilizzare piccole file JavaScript da fase uno per ottenere un semplice elenco di elementi consigliati. I dati ricevuti qui sono più snelli di quella la prima opzione.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. prerequisiti di

1. Creare un nuovo modello utilizzando le API. Vedere la Guida introduttiva su come eseguire questa operazione.
2. Codificare il &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; con base 64. (Questa operazione da utilizzare per l'autenticazione di base per attivare il codice JS chiamare le API).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. inviare gli eventi di acquisizione dei dati utilizzando JavaScript
La procedura seguente facilitare l'invio di eventi:

1.  Includere libreria JQuery nel codice. È possibile scaricarlo dal nuget nell'URL seguente.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Includere la raccolta di Script di linguaggio indicazioni dall'URL seguente: http://aka.ms/RecoJSLib1

3.  Inizializzare libreria di Azure ML consigli con i parametri appropriati.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Inviare l'evento appropriato. Nella sezione dettagliate sotto in tutti i tipi di eventi (ad esempio fare clic su evento)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Limitazioni e Browser supportati
Si tratta di un'implementazione di riferimento e viene assegnato come. È opportuno che tutti i browser principali.

###<a name="32-type-of-events"></a>3.2. Tipo di eventi
Sono disponibili 5 tipi di evento che supporta la libreria: fare clic su, fare clic su suggerimento, Aggiungi al carrello di reparto, rimuovere reparto carrello e acquista. Esiste un evento aggiuntivo che consente di impostare il contesto dell'utente chiamato Login.

####<a name="321-click-event"></a>3.2.1. Fare clic su evento
Questo evento deve essere utilizzato ogni volta che un utente ha fatto clic su un elemento. In genere quando si fa clic su un elemento una nuova pagina viene aperto con i dettagli dell'elemento; in questa pagina deve essere attivato l'evento.

Parametri:
- evento (stringa, obbligatorio) - "fare clic su"
- elemento (stringa, obbligatorio) - identificatore univoco dell'elemento
- itemName (stringa, facoltativo) - il nome dell'elemento
- Descrizione articolo (stringa, facoltativo) - descrizione dell'articolo
- itemCategory (stringa, facoltativo) - la categoria dell'elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

O con dati facoltativi:

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Suggerimento fare clic su evento
Questo evento deve essere utilizzato ogni volta che un utente ha fatto clic su un elemento è stato ricevuto da Azure ML consigli come elemento consigliato. In genere quando si fa clic su un elemento una nuova pagina viene aperto con i dettagli dell'elemento; in questa pagina deve essere attivato l'evento.

Parametri:
- evento (stringa, obbligatorio) - "recommendationclick"
- elemento (stringa, obbligatorio) - identificatore univoco dell'elemento
- itemName (stringa, facoltativo) - il nome dell'elemento
- Descrizione articolo (stringa, facoltativo) - descrizione dell'articolo
- itemCategory (stringa, facoltativo) - la categoria dell'elemento
- semi (stringa in forma di matrice, facoltativo) - semi che ha generato la query recommendation.
- recoList (stringa in forma di matrice, facoltativo) - il risultato della richiesta recommendation che ha generato l'elemento a cui è stato fatto clic.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

O con dati facoltativi:

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Aggiungere evento carrello degli acquisti
Questo evento deve essere utilizzato quando si aggiunge un elemento al carrello.
Parametri:
* evento (stringa, obbligatorio) - "addshopcart"
* elemento (stringa, obbligatorio) - identificatore univoco dell'elemento
* itemName (stringa, facoltativo) - il nome dell'elemento
* Descrizione articolo (stringa, facoltativo) - descrizione dell'articolo
* itemCategory (stringa, facoltativo) - la categoria dell'elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Rimuovere gli acquisti carrello evento
Questo evento deve essere utilizzato quando l'utente rimuove un elemento al carrello.

Parametri:
* evento (stringa, obbligatorio) - "removeshopcart"
* elemento (stringa, obbligatorio) - identificatore univoco dell'elemento
* itemName (stringa, facoltativo) - il nome dell'elemento
* Descrizione articolo (stringa, facoltativo) - descrizione dell'articolo
* itemCategory (stringa, facoltativo) - la categoria dell'elemento
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Eventi di acquisto
Questo evento deve essere utilizzato quando l'utente ha acquistato il suo carrello.

Parametri:
* evento (stringa) - "acquistare"
* gli elementi (acquistati []) - matrice contenente una voce per ogni elemento è stato acquistato.<br><br>
Formato acquistato:
    * elemento (stringa) - identificatore univoco dell'elemento.
    * contare (int o stringa) - numero di elementi che sono stati acquistati.
    * prezzo (margine di flessibilità o stringa) - campo facoltativo - prezzo dell'articolo.

Nell'esempio seguente viene mostrata l'acquisto di 3 elementi (33, 34, 35), due con tutti i campi inseriti (elementi, conteggio, prezzo) e l'altra (articolo 34) senza un prezzo.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Evento di accesso utente
Raccolta ML consigli evento Azure consente di creare e utilizzare i cookie per identificare gli eventi che è stato acquistato dallo stesso browser. Per migliorare i risultati di modello che consigli ML Azure consente di impostare di identificazione univoco utente che sostituirà l'utilizzo di cookie.

Questo evento deve essere utilizzato dopo l'accesso utente al sito.

Parametri:
* evento (stringa) - "userlogin"
* utente (stringa) - identificazione univoco dell'utente.
        <script>
  Se (typeof AzureMLRecommendationsEvent = = "non definito") {AzureMLRecommendationsEvent = [];} AzureMLRecommendationsEvent.push ({evento: "userlogin" utente: "ABCD10AA"});      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. utilizzare consigli tramite JavaScript
Il codice che utilizza il suggerimento viene attivato da un evento JavaScript da una pagina Web del cliente. La risposta recommendation include l'ID di elementi consigliati, i nomi e le relative restrizioni. È consigliabile usare questa opzione solo per una visualizzazione elenco di elementi consigliati: gestione più complessa (ad esempio l'aggiunta di metadati dell'elemento) deve essere eseguita sull'integrazione di lato server.

###<a name="41-consume-recommendations"></a>4.1 utilizzare consigli
Per utilizzare consigli che è necessario includono le librerie JavaScript necessarie nella pagina e di chiamare AzureMLRecommendationsStart. Nella sezione 2.

Per utilizzare i suggerimenti per uno o più elementi è necessario chiamare il metodo: AzureMLRecommendationsGetI2IRecommendation.

Parametri:
* elementi (matrice di stringhe) - uno o più elementi per ottenere suggerimenti per. Se si utilizza una compilazione delle rate delle imposte è possibile impostare qui un solo elemento.
* numberOfResults (int) - numero di risultati desiderati.
* includeMetadata (boolean, facoltativo) - se è impostata su "true" indica che il campo di metadati deve essere compilato nel risultato.
* Funzione - una funzione che consente di gestire i suggerimenti di elaborazione restituito. I dati vengono restituiti come matrice di:
    * Id univoco articolo - elemento
    * name - nome (se sono presenti nel catalogo)
    * punteggio - valutazione recommendation
    * metadati - una stringa che rappresenta i metadati dell'elemento

Esempio: Il codice seguente richiede 8 consigli per l'elemento "64f6eb0d-947a-4c18-a16c-888da9e228ba" (e non specificando includeMetadata - in modo implicito dichiara di metadati non siano necessario), quindi concatenare i risultati in un buffer.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 
