<properties
    pageTitle="Creazione di un modello con UI Recommnendations | Microsoft Azure"
    description="Azure consigli di apprendimento - creazione di un modello con l'interfaccia utente consigli"
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
    ms.date="10/11/2016"
    ms.author="luisca"/>

# <a name="building-a-model-with-the-recommendations-ui"></a>Creazione di un modello con l'interfaccia utente consigli

In questo documento è una Guida dettagliata. Il nostro obiettivo è per scorrere i passaggi necessari per formare un modello mediante l' [Interfaccia utente di suggerimenti](https://recommendations-portal.azurewebsites.net/).
Nell'esercizio consente di comprendere il processo di creazione di un modello prima di eseguire a livello di programmazione. Inoltre consente di acquisire familiarità con l'interfaccia utente, che è utile quando si iniziano a usare il servizio.

Questo esercizio dura circa 30 minuti.

<a name="Step1"></a>
## <a name="step-1---sign-in-to-the-recommendations-ui"></a>Passaggio 1 - accesso per i suggerimenti dell'interfaccia utente ##

1. Se non è già stato fatto, è necessario [iscrizione](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) per un nuovo abbonamento [API consigli](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) . È possibile iscriversi per il servizio in **Azure** [all'http://portal.azure.com/](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) ed effettuare l'accesso con il proprio account Azure. Vengono fornite istruzioni dettagliate sul processo di iscrizione nel *passaggio 1* della [Guida introduttiva](cognitive-services-recommendations-quick-start.md) 

1. Dopo aver ottenuto una **chiave** per la sottoscrizione API raccomandazioni, passare [all'Interfaccia utente di suggerimenti](https://recommendations-portal.azurewebsites.net/). 

1. Accedere al portale di immettendo la chiave nella casella **Chiave Account** e quindi fare clic sul pulsante di **accesso** .

    ![Consigli dell'interfaccia utente: Accesso nella finestra di dialogo.][reco_signin]


<a name="Step2"></a>
## <a name="step-2---lets-gather-some-training-data"></a>Passaggio 2 - seguito raccogliere alcuni dati di formazione ##

Prima di poter creare una compilazione, il motore deve disporre di due informazioni: un file di catalogo e una serie di file di utilizzo. 

Il file di catalogo contiene informazioni sugli elementi offrono al cliente. Un file di utilizzo contiene informazioni sulla modalità di utilizzo degli elementi oppure le transazioni dall'azienda.

In genere si preferisce query un database di archiviazione per questi tipi di informazioni. Oggi, vengono fornite alcuni dati di esempio automaticamente in modo che si imparerà a utilizzare l'API consigli.

È possibile scaricare i dati da [http://aka.ms/RecoSampleData](http://aka.ms/RecoSampleData). Copiare e decomprimere il file **Books.Zip** in una cartella nel computer locale. Ad esempio **c:\data**.

Informazioni dettagliate sullo schema del catalogo e l'utilizzo dei file disponibili nell'articolo [la raccolta di dati per formare il modello](cognitive-services-recommendations-collecting-data.md) .
 
Per questa esercitazione, che verranno lavorare con un file di piccole dimensioni in modo che non è necessario molto attendere troppo a lungo corsi di formazione. Se si vuole provare a un file più realistico, abbiamo anche **MsStoreData.zip** contenente le transazioni di esempio da Microsoft Store nella [stessa posizione](http://aka.ms/RecoSampleData).

<a name="Step3"></a>
## <a name="step-3---create-a-project-and-upload-catalog-and-usage-data"></a>Passaggio 3: creare un progetto e caricare catalogo e l'uso dei dati ##

Durante l'accesso all' [Interfaccia utente di suggerimenti](https://recommendations-portal.azurewebsites.net/), viene visualizzata la pagina di progetti. Se è stato creato in precedenza tutti i progetti, verrà visualizzato in seguito.
Un progetto (noto anche come *un modello* nel riferimento API) è un contenitore per i catalogo e l'uso di dati. È possibile creare diversi *Compila* all'interno del progetto. Si verrà descritto il processo nei passaggi successivi.

1. Per creare un nuovo progetto, digitare il nome nella casella di testo (un elemento come funzioni "MyFirstModel") e fare clic su **Aggiungi progetto**.
 
    ![Consigli dell'interfaccia utente: Pagina di progetti.][reco_projects]

1. Una volta creato il progetto, fare clic sul pulsante **Sfoglia per File** nella sezione **aggiungere un File di catalogo** . Caricare il catalogo ottenuto nel passaggio 2. Se è stato salvato in *c:\data*, è necessario passare a tale cartella.

    ![Consigli dell'interfaccia utente: Aggiunta di dati a un progetto.][reco_firstmodel]

1. Dopo il catalogo viene caricato, fare clic sul pulsante **Sfoglia per File** nella sezione **Aggiungi file l'utilizzo** . Aggiungere il file usage_large.txt.

> **Cosa fare se dispone di un file di grandi dimensioni dati di utilizzo?**
>
> È possibile caricare solo i file di utilizzo inferiori a 200 MB. Nonostante ciò, il sistema può contenere fino a 2 GB patrimonio dei dati delle transazioni, se necessario, è possibile caricare più di un file.
> Non è necessario che tutti i dati per generare un buon modello, non è la dimensione di dati che è importante, ma la qualità dei dati. Vengono in genere per visualizzare dati di utilizzo in cui la maggior parte delle transazioni presenti solo pochi elementi popolari ed esiste "leggermente segnalare" per altri elementi.

<a name="Step4"></a>
## <a name="step-4---lets-do-some-training"></a>Passaggio 4 - possibile eseguire alcune formazione! ##

Ora che è stato caricato il catalogo e i dati di utilizzo, sono pronti per impostare il modulo in modo che possono leggere modelli dai dati.

1.  Fare clic sul pulsante **Nuova compilazione** .

1.  Selezionare **consigli** come tipo di compilazione. Si noti che sono supportate la classificazione di compilazione e anche tipi di compilazione di un rate delle imposte (spesso acquistato insieme).

    ![Consigli dell'interfaccia utente: Creare una finestra di dialogo.][reco_build_dialog.png]


    Una compilazione delle rate delle imposte consente di identificare i modelli per i prodotti che vengono in genere acquistato/utilizzati nella stessa transazione.
    Creare una classificazione viene usato per identificare le funzionalità di interesse. 
    Non è possibile analizzare molto profondità delle rate delle imposte o classificazione Compila per questa esercitazione, ma se si è interessati è necessario estrarre dei [tipi di compilazione e pagina della documentazione di qualità del modello](cognitive-services-recommendations-buildtypes.md).

1. Fare clic sul pulsante dei **generatori** . Se si usa i dati di libri, il processo di compilazione necessari alcuni minuti. Richiede più tempo nel set di dati più grande.

<a name="Step5"></a>
## <a name="step-5---lets-find-out-what-the-machine-learned"></a>Passaggio 5 - seguito scoprire quanto appreso computer! ##

Una volta completata la compilazione, si noterà una nuova compilazione nell'elenco Build. Questa compilazione è possibile cercare consigli elemento e utente.

1. Una volta completata la compilazione, fare clic su **punteggio**. In questo modo è possibile riprodurre con il modello e vedere quali elementi sono consigliati.

    ![Consigli dell'interfaccia utente: Pulsante punteggio][reco_score_button]

1. Selezionare un elemento per vedere quali elementi vengono restituiti come suggerimenti per l'elemento. Si noti che se non sono sufficienti transazioni prevedere un suggerimento per uno specifico elemento, il sistema non restituirà qualsiasi consigli per l'elemento.  Se per qualche motivo si dispone di un elemento che non restituisce Nessuna raccomandazioni, provare a punteggio altri elementi.

<a name="Step6"></a>
## <a name="step-6---next-steps"></a>Passaggio 6 - passaggi successivi ##
Congratulazioni! si dispone qualificato un modello e quindi se si ha consigli da tale modello.  L'interfaccia utente consigli è uno strumento utile che consente di visualizzare lo stato dei progetti e crea. 

Dopo aver creato un modello, è consigliabile per informazioni su come eseguire la procedura indicata sopra a livello di programmazione. Per informazioni su come chiamare l'API a livello di programmazione, invita tutti è possibile controllare il [Riferimento all'API consigli](http://go.microsoft.com/fwlink/?LinkId=759348) e scaricare l' [Applicazione di esempio consigli](http://go.microsoft.com/fwlink/?LinkID=759344).


[reco_signin]:../media/cognitive-services/reco_signin.PNG
[reco_projects]:../media/cognitive-services/reco_projects.PNG
[reco_firstmodel]:../media/cognitive-services/reco_firstmodel.png
[reco_build_dialog.png]:../media/cognitive-services/reco_build_dialog.png
[reco_score_button]:../media/cognitive-services/reco_score_button.png
