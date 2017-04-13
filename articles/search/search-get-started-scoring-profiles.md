<properties 
    pageTitle="Come utilizzare il punteggio dei profili di ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato" 
    description="Ottimizzare le ricerche classificazione tramite i profili di classificazione di ricerca di Azure, un servizio di ricerca cloud ospitato in Microsoft Azure." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Utilizzo dei profili punteggio risultati di ricerca di Azure

I profili punteggio sono una funzionalità di Microsoft Azure ricerca personalizzabile il calcolo dei punteggi di ricerca, che influenzano classificazione gli elementi in un elenco dei risultati di ricerca. È possibile pensare di punteggio profili come un modo per pertinenza modello, per ottimizzare gli elementi che soddisfano i criteri predefiniti. Si supponga ad esempio, che l'applicazione è un sito di prenotazione albergo online. Per migliorare la `location` di campo, le ricerche che includono un termine come Seattle genereranno punteggi maggiori per gli elementi che hanno Seattle nel `location` campo. Si noti che è possibile avere più di un profilo punteggio o nessuno, se l'impostazione predefinita il punteggio è sufficiente per l'applicazione.

Per informazioni su come sperimentare punteggio profili, è possibile scaricare un'applicazione di esempio che utilizza profili punteggio per modificare l'ordine di classificazione dei risultati della ricerca. Il campione è un'applicazione di console – non molto realistica per lo sviluppo di applicazioni, ma utile tuttavia come uno strumento di apprendimento. 

L'applicazione di esempio viene punteggio comportamenti utilizzando dati fittizi, denominati la `musicstoreindex`. La semplicità di applicazione di esempio è semplice modificare punteggio profili e alle query e quindi verificare gli effetti immediati rango ordine quando si esegue il programma.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Prerequisiti

L'applicazione di esempio è scritto in c# utilizzando Visual Studio 2013. Prova gratuita [Visual Studio 2013 Express edition](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) se non si dispone già di una copia di Visual Studio.

È necessario un abbonamento a Azure e un servizio di ricerca di Azure per completare l'esercitazione. Per informazioni sulla configurazione del servizio, vedere [creare un servizio di ricerca nel portale](search-create-service-portal.md) .

[AZURE. INCLUDERE [è necessario un account Azure per completare l'esercitazione:](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio

Passare a [Azure ricerca punteggio profili Demo](https://azuresearchscoringprofiles.codeplex.com/) su codeplex per scaricare l'applicazione di esempio descritta in questa esercitazione.

Nella scheda codice sorgente fare clic su **Download** per ottenere un file zip della soluzione. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Modifica app

1. Dopo avere estratto i file, aprire la soluzione in Visual Studio per modificare il file di configurazione.
1. In Esplora soluzioni fare doppio clic su **App**. Questo file specifica l'endpoint del servizio e un `api-key` utilizzato per autenticare la richiesta. È possibile ottenere questi valori dal portale di classica.
1. Accedere al [portale di Azure](https://portal.azure.com).
1. Passare al dashboard di servizio per la ricerca di Azure.
1. Fare clic sul riquadro **proprietà** per copiare l'URL del servizio
1. Fare clic sul riquadro di **tasti** per copiare la `api-key`.

Dopo aver terminato l'aggiunta dell'URL e `api-key` a App, le impostazioni dell'applicazione dovrebbero risultare analoga alla seguente:

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Esplorare l'applicazione

Si è quasi pronti per creare ed eseguire l'app, ma prima di procedere, esaminare i file JSON utilizzati per creare e inserire l'indice.

**Schema.JSON** definisce l'indice, inclusi i profili punteggio sono evidenziati in questa demo. Si noti che lo schema definisce tutti i campi utilizzati nell'indice, inclusi i campi che non supporta ricerche, ad esempio `margin`, che è possibile utilizzare un punteggio profilo. Sintassi di profilo di punteggio descritte in [Aggiungi un punteggio profilo a un indice di ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Data1-3.json** fornisce i dati, 246 album in pochi generi. I dati sono una combinazione di informazioni effettive album e artisti, con i campi fittizi come `price` e `margin` usato per illustrare le operazioni di ricerca. I file di dati sono conformi all'indice e vengono caricati nel servizio di ricerca di Azure. Dopo i dati vengono caricati e indicizzati, è possibile eseguire query su esso.

**Program.cs** esegue le operazioni seguenti:

- Apre una finestra console.

- Si connette alla ricerca di Azure tramite l'URL del servizio e `api-key`.

- Elimina il `musicstoreindex` se esistente.

- Crea un nuovo `musicstoreindex` utilizzando il file schema.json.

- Inserisce l'indice, utilizzando i file di dati.

- L'indice di utilizzo delle quattro query una query. Si noti che i profili di punteggio vengono specificati come un parametro di query. Tutte le query di ricerca per lo stesso termine 'meglio'. La prima query viene punteggio predefinito. Tre query rimanente utilizzare un punteggio profilo.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Creare ed eseguire l'applicazione

Per escludere la connettività o i problemi di riferimento assembly, creare ed eseguire l'applicazione per assicurarsi che non esistono problemi di scoprire prima. Verrà visualizzata un'applicazione console aperta in background. Tutte le quattro query vengono eseguite in sequenza senza sospensione. In molti sistemi, l'intero programma viene eseguito in 15 secondi. Se l'applicazione console include un messaggio che indica "completata. Premere INVIO per continuare", il programma è stato completato correttamente. 

Per confrontare query verrà eseguita, è possibile Segna, copia, Incolla i risultati della query dalla console e incollarli in un file di Excel. 

La figura seguente mostra i risultati del primo tre query side-by-side. Tutte le query di usare lo stesso termine di ricerca migliori, che viene visualizzata in numerosi titoli degli album.

   ![][10]

La prima query utilizza il punteggio predefinito. Poiché il termine di ricerca viene visualizzata solo in titoli degli album e non viene specificato alcun altri criteri, gli articoli con "meglio" nel titolo dell'album vengono restituiti nell'ordine in cui il servizio di ricerca individuati. 

La seconda query utilizza un profilo punteggio, ma si noti che il profilo non ha alcun effetto. I risultati sono uguali a quelle della prima query. Ciò avviene perché il profilo punteggio aumenta un campo ('perimetro') non pertinenti alla query. Al termine di ricerca 'procedure' non esiste in qualsiasi campo "perimetro" di un documento. Quando un profilo punteggio non ha alcun effetto, i risultati sono diverso da quello di punteggio predefinito.  

La terza query è l'evidenza prima di punteggio impatto profilo. Al termine di ricerca è ancora migliore, in modo stiamo lavorando con lo stesso set di album, ma poiché il profilo punteggio sono disponibili altri criteri che aumenta 'valutazione' e 'aggiornato ultimo' alcuni elementi sono azionati superiore nell'elenco.

La figura seguente mostra la query quarta e ultima aumentata di "margine". Il campo "margine" non ricerche e non può essere restituito nei risultati della ricerca. Il valore di "margine" è stato aggiunto manualmente al foglio di calcolo per illustrare il punto che gli elementi con i margini superiori visualizzate più in alto nell'elenco dei risultati della ricerca. 

   ![][9]

Ora che hanno sperimenta punteggio profili, provare a cambiare il programma da utilizzare nella sintassi della query diversa punteggio dei profili o dati più completi. Ulteriori informazioni sui collegamenti nella sezione successiva.

<a id="next-steps"></a>
## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui profili di classificazione. Per informazioni dettagliate, vedere [aggiungere un profilo punteggio a un indice di ricerca di Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) .

Ulteriori informazioni sui parametri di query e sintassi di ricerca. Per informazioni dettagliate, vedere [Ricerca documenti (API REST di Azure ricerca)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

È necessario passo indietro e ulteriori informazioni sulla creazione di un indice? [Guardare questo video](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) per comprendere le nozioni di base.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 