<properties
    pageTitle="Raccolta di dati per formare il modello: lavorare formazione consigli API | Microsoft Azure"
    description="Computer Azure formazione consigli - raccolta di dati per formare un modello"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="luisca"/>

#  <a name="collecting-data-to-train-your-model"></a>La raccolta di dati per formare un modello #

API consigli le apprende da sotto controllo le transazioni passate per trovare quello che devono essere elementi consigliati per un utente specifico.

Dopo aver creato un modello, sarà necessario fornire due informazione prima di eseguire qualsiasi formazione: un file del catalogo e dati di utilizzo.

>   Se si dispone non già fatto, è consigliabile completare la [Guida introduttiva](cognitive-services-recommendations-quick-start.md).


## <a name="catalog-data"></a>Catalogo dati ##

### <a name="catalog-file-format"></a>Formato di file di catalogo ###

Il file di catalogo contiene informazioni sugli elementi offrono al cliente.
I dati del catalogo devono seguire nel formato seguente:

- Senza funzionalità-`<Item Id>,<Item Name>,<Item Category>[,<Description>]`

- Con funzionalità-`<Item Id>,<Item Name>,<Item Category>,[<Description>],<Features list>`

#### <a name="sample-rows-in-a-catalog-file"></a>Righe di esempio in un File di catalogo

Senza caratteristiche:

    AAA04294,Office Language Pack Online DwnLd,Office
    AAA04303,Minecraft Download Game,Games
    C9F00168,Kiruna Flip Cover,Accessories

Con caratteristiche:

    AAA04294,Office Language Pack Online DwnLd,Office,, softwaretype=productivity, compatibility=Windows
    BAB04303,Minecraft DwnLd,Games, softwaretype=gaming,, compatibility=iOS, agegroup=all
    C9F00168,Kiruna Flip Cover,Accessories, compatibility=lumia,, hardwaretype=mobile

#### <a name="format-details"></a>Dettagli sul formato

| Nome | Obbligatorio | Tipo |  Descrizione |
|:---|:---|:---|:---|
| Id elemento |Sì | [A-z], [a-z], [0-9], [_] & #40; Carattere di sottolineatura & #41; [-] & #40; trattino & #41;<br> La lunghezza massima consentita: 50 | Identificatore univoco di un elemento. |
| Nome elemento | Sì | Qualsiasi carattere alfanumerico<br> La lunghezza massima consentita: 255 | Nome elemento. |
| Categoria articolo | Sì | Qualsiasi carattere alfanumerico <br> La lunghezza massima consentita: 255 | Categoria a cui appartiene l'articolo (ad esempio cucina libri, serie...); può essere vuoto. |
| Descrizione | No, a meno che non sono caratteristiche presentare (ma può essere vuoto) | Qualsiasi carattere alfanumerico <br> La lunghezza massima consentita: 4000 | Descrizione di questo articolo. |
| Elenco delle caratteristiche | No | Qualsiasi carattere alfanumerico <br> La lunghezza massima consentita: 4000; Numero massimo di caratteristiche: 20 | Elenco delimitato da virgole del nome della caratteristica = valore funzionalità che può essere utilizzati per migliorare recommendation modello.|

#### <a name="uploading-a-catalog-file"></a>Caricare un file di catalogo

Esaminare il [riferimento all'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e1) per caricare un file di catalogo.  

Si noti che il contenuto del file di catalogo deve essere passato come corpo della richiesta.

Se si carica più file di catalogo allo stesso modello con più chiamate, si verranno inserito solo i nuovi elementi catalogo. Gli elementi esistenti rimangono con i valori originali. Con questo metodo non è possibile aggiornare i dati del catalogo.

>   Nota: Le dimensioni massime dei file sono 200MB.
>   Il numero massimo di elementi del catalogo supportato è 100.000 voci.


## <a name="why-add-features-to-the-catalog"></a>Perché aggiungere caratteristiche al catalogo?

Il motore consigli crea un modello di statistico indica quali elementi sono probabilmente esprime apprezzamento o acquistato da un cliente. Quando si dispone di un nuovo prodotto non è mai state interazione non è possibile creare un modello in occorrenze condivisa solo. Supponiamo che si avvia che offre un nuovo "violino dei bambini" nell'archivio, dal momento che tale violino non mai stato venduto prima non è possibile stabilire quali altri elementi di consigliare con tale violino.

Nonostante ciò, se il motore di comunicare informazioni che violino (ad esempio è uno strumento musicale, per gli elementi figlio età 7 a 10, non un violino costosa e così via), quindi il motore di informazioni da altri prodotti con caratteristiche analoghe. Ad esempio, sono venduti del violino in passato e in genere le persone che acquistano violins comandi sono particolarmente utili per acquistare musica classica CD e supporti musica foglio.  Il sistema di individuare le connessioni tra le caratteristiche e fornire consigli in base alle mentre il nuovo violino ha piccola l'uso.

Caratteristiche vengono importate come parte dei dati di catalogo e quindi il rango (o la priorità della funzionalità del modello) è associata al termine di una compilazione rango. Funzione rango possibile cambiano in base al modello di dati di utilizzo e il tipo di elementi. Ma per l'uso coerenti/elementi, il rango deve avere solo le piccole fluttuazioni. Rango delle caratteristiche è un numero non negativo. Il numero 0 indica che la caratteristica non è stata classificata (si verifica se si richiama questa API prima del completamento della prima compilazione rango). La data in cui è stato attribuito il rango si chiama la validità punteggio.


###<a name="features-are-categorical"></a>Caratteristiche sono Categorical

Questo significa che è necessario creare funzionalità simili a una categoria. Ad esempio prezzo = 9.34 non è una caratteristica per categoria. Mano, una caratteristica come priceRange = Under5Dollars è una caratteristica per categoria. Un altro errore comune consiste nell'usare il nome dell'elemento come una caratteristica. Questa operazione può provocare il nome di un elemento che deve essere univoco in modo che non sarebbe descrivono una categoria. Assicurarsi che le caratteristiche rappresentano le categorie di elementi.


###<a name="how-manywhich-features-should-i-use"></a>Caratteristiche come molti/che è utile usare?


Infine i consigli creare supporta la creazione di un modello con un massimo di 20 caratteristiche. È possibile assegnare più di 20 caratteristiche degli elementi del catalogo, ma ci si aspettava di eseguire una compilazione di classificazione e selezionare solo le caratteristiche situati elevato. Una caratteristica con una classificazione di 2.0 o più è una caratteristica molto utile usare!. 


###<a name="when-are-features-actually-used"></a>Quando sono caratteristiche effettivamente utilizzate?

Caratteristiche vengono utilizzate dal modello quando non è sufficiente dati transazione da fornire consigli informazioni di transazione. In modo caratteristiche avrà il massimo impatto sulla "freddo"elementi – gli elementi con alcuni transazioni. Se tutti gli elementi sono sufficienti informazioni transazione non può essere necessario migliorare il modello con caratteristiche.


###<a name="using-product-features"></a>Utilizzo delle funzionalità del prodotto

Per usare le funzionalità durante la compilazione che è necessario:

1. Verificare che il catalogo include caratteristiche quando si carica.

2. Impostare un trigger genera una classificazione. Questa operazione verrà eseguita l'analisi su priorità/priorità delle caratteristiche.

3. Impostare un trigger una compilazione consigli, parametri di compilazione impostando le operazioni seguenti: impostare useFeaturesInModel su true, allowColdItemPlacement su true, e modelingFeatureList deve essere impostata su nell'elenco di valori separati da virgola di caratteristiche che si desidera utilizzare per migliorare il modello. Per ulteriori informazioni, vedere [consigli creare parametri di tipo](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d0) .





## <a name="usage-data"></a>Dati di utilizzo ##
Un file di utilizzo contiene informazioni sulla modalità di utilizzo degli elementi oppure le transazioni dall'azienda.

#### <a name="usage-format-details"></a>Dettagli sul formato di utilizzo
File CSV (dei valori separati da virgola) in ogni riga in un file di utilizzo rappresenta un'interazione tra un utente e un elemento è un file di utilizzo. Ogni riga è formattato come indicato di seguito:<br>
`<User Id>,<Item Id>,<Time>,[<Event>]`



| Nome  | Obbligatorio | Tipo | Descrizione
|-------|------------|------|---------------
|Id utente|         Sì|[A-z], [a-z], [0-9], [_] & #40; Carattere di sottolineatura & #41; [-] & #40; trattino & #41;<br> La lunghezza massima consentita: 255 |Identificatore univoco dell'utente.
|Id elemento|Sì|[A-z], [a-z], [0-9], [& #95;] & #40; Carattere di sottolineatura & #41; [-] & #40; trattino & #41;<br> La lunghezza massima consentita: 50|Identificatore univoco di un elemento.
|Ora|Sì|Data in formato: aaaa/MM/GGOhh.mm.ss (ad esempio 2013/06/20T10:00:00)|Ora di dati.
|Evento|No | Una delle operazioni seguenti:<br>Fare clic su •<br>• RecommendationClick<br>• AddShopCart<br>• RemoveShopCart<br>• Acquisto| Tipo di transazione. |

#### <a name="sample-rows-in-a-usage-file"></a>Righe di esempio in un File di utilizzo

    00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase
    0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase
    0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase
    00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase
    0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase
    00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

#### <a name="uploading-a-usage-file"></a>Caricare un file di utilizzo

Esaminare il [riferimento all'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f316efeda5650db055a3e2) per caricare file con l'uso.
Si noti che è necessario passare il contenuto del file di utilizzo come corpo della chiamata HTTP.

>  Nota:

>  Dimensioni massime dei file: 200MB. Può caricare più file di utilizzo.

>  È necessario caricare un file di catalogo prima di iniziare ad aggiungere dati di utilizzo per il modello. Solo gli elementi nel file di catalogo da utilizzare durante la fase di formazione. Tutti gli altri elementi verranno ignorati.

## <a name="how-much-data-do-you-need"></a>La quantità di dati è necessario?

La qualità del modello dipende molto la qualità e la quantità di dati.
Il sistema viene a conoscenza quando si acquistano elementi diversi utenti (desiderata per questo CO-occorrenze). Per le generazioni delle rate delle imposte, è importante sapere quali elementi vengono acquistati nelle stesse transazioni. 

È buona norma essere maggior parte degli elementi in 20 transazioni o altre informazioni, in modo che se si dispone di 10.000 elementi del catalogo, sarebbe consigliabile avere almeno 20 volte il numero delle transazioni o circa 200.000. Una volta anche in questo caso generale. Sarà necessario acquisire familiarità con i dati.

Dopo aver creato un modello, è possibile eseguire una [valutazione offline](cognitive-services-recommendations-buildtypes.md) per controllare come e il modello è che può essere eseguita.
