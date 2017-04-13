<properties
    pageTitle="Guida introduttiva: Machine Learning consigli API | Microsoft Azure"
    description="Azure apprendimento consigli - Guida introduttiva"
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
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guida introduttiva per l'API di consigli servizi cognitiva

Questo documento descrive come a integrata del servizio o dell'applicazione di utilizzo dell' [API consigli](http://go.microsoft.com/fwlink/?LinkId=759710).
È possibile trovare altre informazioni sull'API consigli e altri servizi Cognitive [qui](http://go.microsoft.com/fwlink/?LinkId=759709). In questa guida è inoltre possibile trovare il [Riferimento all'API consigli](http://go.microsoft.com/fwlink/?LinkId=759348) utili.


<a name="Overview"></a>
## <a name="general-overview"></a>Informazioni generali

In questo documento è una Guida dettagliata. Il nostro obiettivo è per scorrere i passaggi necessari per formare un modello e per scegliere le risorse che consentono di utilizzare il modello all'ambiente di produzione.

Questo esercizio richiederà circa 30 minuti.

Per utilizzare [API raccomandazioni](http://go.microsoft.com/fwlink/?LinkId=759710), è necessario eseguire le operazioni seguenti:

1. Creare un modello, un modello è un contenitore di dati di utilizzo, catalogo dati e il modello di recommendation.
1. Importazione del catalogo dati, un catalogo contengono informazioni sugli elementi dei metadati.
1. Importa dati di utilizzo - dati di utilizzo possono essere caricati in uno dei 2 modi (o entrambe):
  -  Per caricare un file che contiene i dati di utilizzo.
  -  Invio di eventi di acquisizione di dati.
  In genere si carica un file di utilizzo per poter creare un modello di recommendation iniziale (avvio) e usarla fino a quando il sistema raccoglie abbastanza dati utilizzando il formato di acquisizione di dati.
1. Creazione di un modello di recommendation: questa è un'operazione asincrona in cui il sistema di recommendation accetta tutti i dati di utilizzo e viene creato un modello di recommendation. Questa operazione può richiedere alcuni minuti o alcune ore a seconda delle dimensioni dei dati e i parametri di configurazione di compilazione. Durante la compilazione di trigger, verrà visualizzato un ID di compilazione. Utilizzarla per verificare quando è terminato il processo di compilazione prima di iniziare a utilizzare i suggerimenti.
1. Utilizzare consigli - ottenere suggerimenti per un elemento specifico o un elenco di elementi.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Iniziamo!

Avviare la creazione di un modello di suggerimenti. Quindi è necessario una Guida su come utilizzare i risultati generati dal modello di suggerimenti di power su un sito di e-commerce.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Attività 1 - firma per l'API consigli ####

In questa attività si sarà iscriversi al servizio API di suggerimenti e creare un modello di suggerimenti.

1. Accedere al **Portale di Azure** [http://portal.azure.com/](http://portal.azure.com/) e accedere usando l'account Azure.

1.  Fare clic su **+ Nuovo**.

1. Selezionare l'opzione di **Business Intelligence** .

1. Selezionare il prodotto **Cognitive API dei servizi** .
Questo prodotto sarà possibile avviare un abbonamento per uno dei servizi cognitivi API (nominale, testo Analitica, problemi alla vista Computer e così via). Oggi è dedicata API consigli.

1. Nella pagina di destinazione Cognitive API dei servizi, immettere il **nome dell'Account** per l'abbonamento consigli. (Per istanza: "MyRecommendations"). Questo nome non deve avere spazi al suo interno.

1. **Tipo di API**, selezionare **consigli**.

1. Nel **livello prezzi**, è possibile selezionare un piano. È possibile selezionare il livello di **gratuito** per le transazioni 10.000/mese. Si tratta di un piano di gratuita, pertanto è un'ottima soluzione per avviare tentando il sistema. Quando si passa alla produzione, è consigliabile prendere in considerazione il volume di richiesta e modificherà il tipo di piano. (Nota: È consentito un solo abbonamento gratuito livello alla volta)

1. Selezionare un **Gruppo di risorse**o crearne uno nuovo se non è già disponibile.

1. È possibile modificare altri elementi nella finestra di dialogo Crea. Si noti che il provider di risorse oggi è supportato solo dai centri dati negli Stati Uniti.

1. Dopo aver con le selezioni, fare clic su **Crea**.

1. Attendere alcuni minuti per la risorsa per la distribuzione.
Una volta distribuito, è possibile accedere alla sezione **chiavi** e **l'Impostazioni** in cui verrà fornito una chiave primaria e secondaria da utilizzare l'API.  Copiare la chiave primaria come sarà necessario durante la creazione di un modello di prima.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Attività 2 - ha importare i dati? ####

API consigli verranno fornite informazioni dal catalogo e sotto controllo le transazioni per fornire consigli sui prodotti ottimale. Che indica che è necessario feed di dati validi sui prodotti (desiderata per questo un file di **catalogo** ) e un set di transazioni sufficiente per poter trovare interessanti i modelli di consumo (desiderata per l' **utilizzo**).

1. In genere si preferisce query database transazioni per questi tipi di informazioni.
Nel caso non è loro comodo, sono disponibili alcuni dati di esempio è basato su Microsoft Store transazione dati.

 È possibile scaricare i dati da [qui](http://aka.ms/RecoSampleData). Copiare e decomprimere il MsStoreData.Zip in una cartella nel computer locale.

 > **Nota:** Il codice di esempio che verranno scaricare ed eseguire attività 3 è i dati di esempio già incorporati all'interno, ossia in modo che questa attività è facoltativa.  Nonostante ciò, questa attività è possibile scaricare più realistici set di dati e consentono di comprendere l'input all'API consigli migliore.

1.  Ora esaminiamo il file di catalogo. Passare al percorso in cui sono stati copiati i dati.
 Aprire il file di catalogo nel **blocco note**.

 Si noterà che il file catalogo è molto semplice. Ha il seguente formato`<itemid>,<item name>,<product category>`

 >  AAA-04294, DwnLd Online E34 OfficeLangPack 32/64 2013, Office <br>
 > AAA-04303, DwnLd Online e OfficeLangPack 32/64 2013, Office  <br>
 > C9F 00168, KRUSELL Kiruna capovolgere copertina per Nokia Lumia 635 - caratterizzati, accessori

 Si noti che un file di catalogo può essere molto più completo, ad esempio possibile aggiungere i metadati sui prodotti (desiderata per queste *caratteristiche elemento*). Si deve vedere la sezione [formato catalogo](http://go.microsoft.com/fwlink/?LinkID=760716) nel riferimento API per ulteriori informazioni sul formato del catalogo.

1. Di seguito, eseguire la stessa operazione con i dati di utilizzo. Si noterà che la data di utilizzo del formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C acquisto, 297833400, 2015/08/04T11:02:50, 0003BFFDD4C2118D acquisto, 297833300, 2015/08/04T11:02:40, 00030000D16C4237 acquisto, 297833300, 2015/08/04T11:02:37, 0003BFFDD4C20B63 acquisto, 297833400, 2015/08/04T11:02:12, 00037FFEC8567FB8 acquisto, 297833400, 2015/08/04T11:02:04, acquisto

Si noti che i primi tre elementi sono obbligatori. Il tipo di evento è facoltativo. È possibile estrarre il [formato uso](http://go.microsoft.com/fwlink/?LinkID=760712) per ulteriori informazioni su questo argomento.

 > **La quantità di dati è necessario?**
 <p>
Tutto dipende i dati di utilizzo. Il sistema impara quando si acquistano elementi diversi utenti. Per alcune Build come delle rate delle imposte, è importante sapere quali elementi vengono acquistati nelle stesse transazioni. (Si chiama questo *CO-occorrenze*). È buona norma essere maggior parte degli elementi in 20 transazioni o altre informazioni, in modo che se si dispone di 10.000 elementi del catalogo, sarebbe consigliabile avere almeno 20 volte il numero delle transazioni o circa 200.000.
Una volta anche in questo caso generale. Sarà necessario acquisire familiarità con i dati.
</p>

<a name="Ex1Task3"></a>
####Attività 3: creazione di un modello di consigli####

Ora che si possiede un account e si dispone di dati, creare il primo modello.

In questa attività si utilizzerà l'applicazione di esempio per creare il primo modello.

1. Prima di tutto dovrebbe essere presente del [Riferimento all'API consigli](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Scaricare l' [Applicazione di esempio](http://go.microsoft.com/fwlink/?LinkID=759344) in una cartella locale.

1. Aprire Visual Studio la soluzione **RecommendationsSample.sln** nella cartella **c#** .

1. Aprire il file **SampleApp.cs** . Nota che nel file i passaggi seguenti:
 + Creare un modello
 + Aggiungere un file di catalogo
 + Aggiungere un file di utilizzo
 + Impostare un trigger una compilazione per il modello
 + Ottenere un suggerimento in base a una coppia di elementi
<p></p>

1. Sostituire il valore per il campo **AccountKey** con la chiave da attività 1.

1. Passaggio attraverso la soluzione e verrà visualizzato come viene creato un modello.

1. Provare a sostituire i file di catalogo e l'uso scaricato per creare un nuovo modello per il sito Microsoft Store o per consigli Rubrica. È necessario modificare anche il nome del modello e gli elementi per cui si richiede consigli.

1. Quando viene creato il modello, prendere nota dell' **ID del modello** come sarà necessario quando vengono richiesti i consigli nel proprio ambiente di produzione.

>  Ulteriori informazioni sulle generazione tipi e su come calcolare la qualità della build [qui](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Inserimento di un modello di produzione! ###

Dopo avere appreso come creare un modello e utilizzare i suggerimenti, il passaggio successivo è per mettere in produzione nel sito Web, applicazioni mobili o integrare il sistema CRM o ERP.
Naturalmente, ciascuna di esse essere diverso. Poiché l'API consigli richiesto come un servizio web, dovrebbe essere possibile chiamare con facilità da uno qualsiasi di questi ambienti diversi.

**Importanti:**  Se si vuole visualizzare consigli da un client pubblico (ad esempio, il sito e-commerce), creare un server proxy per fornire i suggerimenti. Questo è importante, in modo che la chiave dell'API non viene esposto su entità (potenzialmente non attendibile) esterne.

Di seguito sono riportati alcuni suggerimenti delle posizioni in cui è possibile utilizzare i suggerimenti:

### <a name="product-page"></a>Pagina del prodotto

**Elemento consigli**
<p>Se il modello è configurato sui dati di acquisto, sarà possibile del cliente per *individuare i prodotti che si possono essere di interesse per gli utenti che hanno acquistato l'oggetto di origine*.</p>
<p>Se il modello è configurato in fare clic su dati, sarà possibile del cliente per *individuare i prodotti in genere visitati dagli utenti che hanno visitato la voce di origine*. Questo tipo di modello può restituire elementi simili.</p>

**Spesso acquistato insieme consigli**
<p>Impossibile esperti A domande acquistato insieme compilazione, è possibile ottenere set di elementi che potranno essere acquistato insieme a questo elemento.</p>

### <a name="check-out-page"></a>Estrazione pagina

**Elemento consigli**
<p>Un consigli modello possono essere necessari come input un elenco di elementi. In modo che è possibile passare tutti gli elementi nel carrello come input per ottenere suggerimenti.
In questo caso, il modello verrà consigli per l'interesse assegnato tutti gli elementi nel carrello.
</p>

### <a name="landing-page"></a>Pagina di destinazione

**Consigli utente**
<p>
Un consigli modello può accettare come input l'id utente.  La cronologia delle transazioni per l'utente verrà utilizzato per fornire i consigli personalizzati per l'utente specificato.
</p>

Consultare la [Documentazione di consigli elemento visualizzato](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>Che cos'è successiva?
Congratulazioni se è stata questo estrema! Per informazioni su che altre è possibile visitare [Riferimento all'API di suggerimenti](http://go.microsoft.com/fwlink/?LinkId=759348) per eventuali domande, consigliabile contattamlapi@microsoft.com
