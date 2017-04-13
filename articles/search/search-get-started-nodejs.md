<properties
    pageTitle="Guida introduttiva di Azure ricerca in NodeJS | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Scorrere la creazione di un'applicazione di ricerca in un servizio di ricerca cloud ospitato su Azure utilizzando NodeJS come il linguaggio di programmazione."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-nodejs"></a>Guida introduttiva di Azure ricerca in NodeJS
> [AZURE.SELECTOR]
- [Portale](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Informazioni su come creare un'applicazione di ricerca NodeJS personalizzata che usa la ricerca di Azure per l'esperienza di ricerca. In questa esercitazione utilizza l' [API REST servizio di ricerca di Azure](https://msdn.microsoft.com/library/dn798935.aspx) per creare gli oggetti e operazioni in questo esercizio.

È stato usato [NodeJS](https://nodejs.org) e NPM, [Sublime 3 di testo](http://www.sublimetext.com/3)e Windows PowerShell in Windows 8.1 per sviluppare e testare il codice.

Per eseguire questo esempio, è necessario disporre di un servizio di ricerca di Azure, è possibile accedere nel [Portale di Azure](https://portal.azure.com). Per istruzioni dettagliate, vedere [creare un servizio di ricerca di Azure nel portale](search-create-service-portal.md) .

## <a name="about-the-data"></a>Informazioni su come i dati

Questa applicazione di esempio utilizzati i dati i [Servizi geologiche negli Stati Uniti (soltanto)](http://geonames.usgs.gov/domestic/download_data.htm), filtrata in stato di Rhode isola di ridurre le dimensioni del set di dati. Si userà questi dati per creare un'applicazione di ricerca che restituisce edifici punti di riferimento, ad esempio scuole e ospedali, nonché geologiche caratteristiche quali flussi e laghi Summit.

In questa applicazione, il programma **DataIndexer** consente di creare e carica dell'indice utilizzando un costrutto [indicizzatore](https://msdn.microsoft.com/library/azure/dn798918.aspx) recupero il set di dati soltanto filtrato da un Database di SQL Azure pubblico. Le credenziali e connessione all'origine dati online informazioni nel codice del programma. È necessaria alcuna ulteriore configurazione.

> [AZURE.NOTE] È applicato un filtro in questo set di dati per rimanere nel limite di 10.000 documenti del livello prezzo gratuito. Se si utilizza il livello standard, non si applica questo limite. Per informazioni dettagliate sulla capacità per ogni livello prezzo, vedere [limiti del servizio di ricerca](search-limits-quotas-capacity.md).


<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Trovare il nome del servizio e la chiave dell'api del servizio di ricerca di Azure

Dopo aver creato il servizio, tornare al portale per ottenere l'URL o `api-key`. Connessioni al servizio di ricerca è necessario disporre entrambi l'URL e un `api-key` per eseguire l'autenticazione la chiamata.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella barra di spostamento, fare clic su **servizio di ricerca** per elencare tutti i servizi di ricerca di Azure viene completato il provisioning per l'abbonamento.
3. Selezionare il servizio che si desidera utilizzare.
4. Nel dashboard di servizio, si noterà riquadri le informazioni essenziali, oltre che l'icona per l'accesso alle chiavi amministratore.

    ![][3]

5. Copiare l'URL del servizio, un tasto di amministratore e una chiave di query. È necessario tutti e tre in un secondo momento, quando vengono aggiunti al file config.js.

## <a name="download-the-sample-files"></a>Scaricare i file di esempio

Utilizzare uno dei metodi seguenti per scaricare il campione.

1. Passare a [AzureSearchNodeJSIndexerDemo](https://github.com/AzureSearch/AzureSearchNodeJSIndexerDemo).
2. Fare clic su **Scarica ZIP**, salvare il file con estensione zip e quindi estrarre tutti i file che contiene.

Tutte le modifiche successive e istruzioni fase saranno per i file in questa cartella.


## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Aggiornare il config.js. con l'URL del servizio di ricerca e la chiave dell'api

Mediante l'URL e la chiave dell'api copiato in precedenza, specificare l'URL, chiave di amministrazione e chiave di query nel file di configurazione.

Tasti di amministrazione concedono il controllo completo sulle operazioni di servizio, incluse la creazione o eliminazione di un indice e il caricamento di documenti. Tasti di query sono invece per le operazioni di sola lettura, in genere utilizzate dalle applicazioni client che eseguono la connessione alla ricerca di Azure.

In questo esempio abbiamo includere la chiave di query per rafforzare la procedura consigliata come utilizzare la chiave di query nelle applicazioni client.

La schermata seguente mostra **config.js** aperto in un testo editor, con le voci pertinenti delimitate in modo da visualizzare la posizione in cui aggiornare il file con i valori che sono validi per il servizio di ricerca.

![][5]


## <a name="host-a-runtime-environment-for-the-sample"></a>Ospitato da un ambiente di runtime per il campione

L'esempio richiede un server HTTP, è possibile installare a livello globale tramite npm.

Utilizzare una finestra di PowerShell per i comandi seguenti.

1. Passare alla cartella che contiene il file **package.json** .
2. Tipo di `npm install`.
2. Tipo di `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Creare l'indice ed eseguire l'applicazione

1. Tipo di `npm run indexDocuments`.
2. Tipo di `npm run build`.
3. Tipo di `npm run start_server`.
4. Indirizzare il browser`http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Cercare dati soltanto

Il set di dati soltanto include record relativi a stati del Rhode isola. Se si fa clic su **ricerca** su una casella di ricerca vuoto, si otterrà superiore 50 voci, che rappresenta il valore predefinito.

Immettere un termine di ricerca per ottenere il motore di ricerca è necessario passare. Provare a immettere un nome internazionali. "Roger Williams" è stato il primo gestore isola Rhode. Numerosi parco, edifici e scuole vengono denominate in quest'ultimo.

![][9]

È inoltre possibile tentare uno di questi termini:

- Pawtucket
- Pembroke
- oca + capo


## <a name="next-steps"></a>Passaggi successivi

Verrà visualizzata la prima esercitazione Azure ricerca in base a NodeJS e il set di dati soltanto. Nel tempo, è possibile estendere questa esercitazione per illustrare le funzionalità di ricerca che è consigliabile utilizzare le soluzioni personalizzate.

Se si dispone già di alcuni concetti di base di ricerca di Azure, è possibile utilizzare in questo esempio prefissati per tentare di suggesters (query di ricerca incrementale o completamento automatico), filtri e spostamento in base a facet. È anche possibile migliorare alla pagina di risultati della ricerca aggiungendo conteggi e il batch di documenti in modo che gli utenti possono spostarsi tra i risultati.

Nuovi utenti di Azure ricerca? È consigliabile tentando altre esercitazioni per sviluppare capire cosa è possibile creare. Visitare la [pagina della documentazione](https://azure.microsoft.com/documentation/services/search/) per trovare altre risorse. Visualizzare i collegamenti nella di [Video ed esercitazioni elenchi](search-video-demo-tutorial-list.md) per accedere a ulteriori informazioni.

<!--Image references-->
[1]: ./media/search-get-started-nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-nodejs/AzSearch-NodeJS-configjs.png
[9]: ./media/search-get-started-nodejs/rogerwilliamsschool.png
