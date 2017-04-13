<properties 
    pageTitle="Richiedere l'unità di DocumentDB | Microsoft Azure" 
    description="Informazioni sulle procedure comprendere e specificare la stima dei requisiti di unità di richiesta di DocumentDB." 
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="06/29/2016" 
    ms.author="syamk"/>

#<a name="request-units-in-documentdb"></a>Richiedere l'unità di DocumentDB
È ora disponibile: DocumentDB [Calcolatrice unità richiesta](https://www.documentdb.com/capacityplanner). Altre informazioni nel [Estimating richiede la velocità](documentdb-request-units.md#estimating-throughput-needs).

![Calcolatrice velocità][5]

##<a name="introduction"></a>Introduzione
In questo articolo viene fornita una panoramica di unità di richiesta di [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/). 

Dopo aver letto in questo articolo, sarà possibile rispondere alle domande seguenti:  

-   Quali sono richiesta unità e richiesta in base alle tariffe?
-   Come è possibile specificare la capacità di unità richiesta per una raccolta?
-   Come è stimare che unità di richiesta dell'applicazione in uso deve?
-   Cosa succede se si supera la capacità di unità richiesta per una raccolta?


##<a name="request-units-and-request-charges"></a>Richiesta di unità e richiesta in base alle tariffe
DocumentDB offre prestazioni prevedibili e veloce tramite *la prenotazione di* risorse per soddisfare che esigenze di velocità dell'applicazione.  Poiché applicazione caricare e accedere a motivi cambiare nel tempo, DocumentDB consente di aumentare o ridurre la quantità di velocità riservato disponibili per l'applicazione.

Con DocumentDB, velocità riservato è specificato come unità di richiesta di elaborazione al secondo.  È possibile pensare di unità richiesta come valuta effettiva, in base al quale si *prenotare* una quantità di garantito unità richiesta disponibili per l'applicazione in base al secondo.  Ogni operazione DocumentDB - la scrittura di un documento, eseguire una query, l'aggiornamento di un documento - implica l'uso della CPU e memoria IOPS.  Ogni operazione comporta, ovvero una *richiesta addebito*, espresso in *unità richiesta*.  Informazioni sui fattori che impatto in base alle tariffe unità di richiesta, insieme a requisiti di velocità dell'applicazione, consente di eseguire l'applicazione come costo in modo efficace possibile. 

##<a name="specifying-request-unit-capacity"></a>Specificando capacità unità richiesta
Quando si crea un insieme di DocumentDB, specificare il numero di unità richiesta al secondo (destinatari) per riservato per la raccolta.  Dopo aver creata la raccolta, l'allocazione completo di destinatari specificato è riservato dell'insieme.  Ogni raccolta è dedicato e isolato le caratteristiche di velocità.  

È importante tenere presente che DocumentDB opera su un modello di prenotazione. vale a dire vengono addebitate per il periodo di velocità *riservato* per la raccolta, indipendentemente dalla quantità di in cui la produttività è attivamente *utilizzato*.  Tenere presente, tuttavia, che come carico dell'applicazione, dati e modifica di modelli di utilizzo di che possibile adattare su e la quantità riservare destinatari tramite SDK DocumentDB o tramite il [Portale di Azure](https://portal.azure.com).  Per ulteriori informazioni sulla velocità scala su e giù, vedere [livelli di prestazioni DocumentDB](documentdb-performance-levels.md).

##<a name="request-unit-considerations"></a>Considerazioni sull'unità richiesta
Per determinare il numero di unità di richiesta per prenotare per la raccolta di DocumentDB, è importante considerare le seguenti variabili:

- **Dimensioni del documento**. Come le dimensioni di documento aumentano le unità utilizzate per leggere o scrivere che i dati anche aumenterà.
- **Numero di proprietà di documenti**. Presupponendo che l'indicizzazione predefinita di tutte le proprietà, le unità utilizzate per scrivere un documento ne aumenterà mentre aumenta il numero di proprietà.
- **La coerenza dei dati**. Quando si usa livelli la coerenza dei dati di sicuro o delimitata obsolescenza, unità aggiuntive verranno utilizzate per leggere i documenti.
- **Le proprietà indicizzate**. Un criterio di indice in ogni raccolta determina quali proprietà siano indicizzati per impostazione predefinita. È possibile ridurre il consumo di unità richiesta limitando il numero di proprietà indicizzate o abilitare l'indicizzazione lenta.
- **Documento di indicizzazione**. Per impostazione predefinita che verrà indicizzato automaticamente ogni documento, si dovrà utilizzare meno unità richiesta se non si desidera indicizzare alcuni dei documenti.
- **Modelli di query**. La complessità di una query influenza il numero di unità richiedere vengono utilizzato per un'operazione. Il numero di predicati, natura predicati, previsioni, numero di funzioni definite dall'utente e la dimensione del set di dati di origine tutti influenzare il costo di operazioni di query.
- **L'uso di script**.  Come per le query, stored procedure e trigger utilizzare unità richiesta in base alla complessità delle operazioni eseguite. Quando si sviluppa l'applicazione, esaminare l'intestazione addebito richiesta per comprendere meglio come ogni operazione è costituita richiesta unità capacità.

##<a name="estimating-throughput-needs"></a>Stima velocità esigenze
Un'unità richiesta è una misura normalizzata della richiesta l'elaborazione di costo. Un'unità singola richiesta rappresenta la capacità di elaborazione necessaria per la lettura, tramite collegamento automatico o id, un singolo documento JSON 1KB composta da 10 valori di proprietà univoche (esclusi proprietà-sistema). Una richiesta per creare (insert), sostituire o eliminare lo stesso documento utilizzerà ulteriori elaborazioni dal servizio e di conseguenza più unità di richiesta.   

> [AZURE.NOTE] Linea di base dell'unità di 1 richiesta di un documento di 1KB corrisponde a un accesso semplice a tramite collegamento automatico o l'id del documento.

###<a name="use-the-request-unit-calculator"></a>Utilizzare la calcolatrice unità richiesta
Per consentire ai clienti fitta ottimizzare le stime di velocità, esiste una basata sul web [Calcolatrice unità richiesta](https://www.documentdb.com/capacityplanner) per calcolare i requisiti di unità richiesta per le operazioni tipiche, tra cui:

- Documento Crea (scrittura)
- Lettura di documenti
- Elimina documento
- Aggiornamenti del documento

Lo strumento include anche il supporto per la stima delle esigenze di archiviazione dei dati in base ai documenti di esempio specificati.

Tramite lo strumento è molto semplice:

1. Caricare uno o più documenti JSON representative.

    ![Caricare documenti in calcolatrice unità richiesta][2]

2. Per stimare i requisiti di spazio di archiviazione dei dati, immettere il numero totale di documenti che si prevede di archiviare.

3. Immettere il numero di documento creare, leggere, aggiornare ed eliminare le operazioni necessarie (in base al secondo). Per valutare in base alle tariffe unità richiesta documento operazioni di aggiornamento, caricare una copia del documento esempio dal passaggio 1 che include gli aggiornamenti di campo tipico.  Ad esempio, se aggiornamenti del documento in genere modificano due proprietà denominate lastLogin e userVisits, quindi è sufficiente copiare il documento di esempio, aggiornare i valori per queste due proprietà e caricare il documento copiato.

    ![Immettere i requisiti di velocità della Calcolatrice di unità richiesta][3]

4. Fare clic su calcolare ed esaminare i risultati.

    ![Richiedere i risultati della Calcolatrice di unità][4]

>[AZURE.NOTE]Se si dispone di tipi di documenti che variano in modo significativo in termini di dimensioni e il numero di proprietà indicizzate, quindi caricare un campione di ogni *tipo* di documento tipico allo strumento e quindi calcolare i risultati.

###<a name="use-the-documentdb-request-charge-response-header"></a>Utilizzare l'intestazione di risposta addebito richiesta DocumentDB
Ogni risposta dal servizio DocumentDB include un'intestazione personalizzata (x ms-richiesta gratuito) che contiene le unità di richiesta utilizzate per la richiesta. Questa intestazione è possibile accedere tramite SDK DocumentDB. In .NET SDK RequestCharge è una proprietà dell'oggetto ResourceResponse.  Per le query, Esplora aree di Query DocumentDB nel portale di Azure vengono fornite informazioni di addebito richiesta per query.

![Esaminare le spese RU in Esplora aree di Query][1]

Con questo valore in considerazione, un metodo per la stima la quantità di velocità riservato necessarie per l'applicazione è possibile registrare la spesa unitaria richiesta associata all'esecuzione di operazioni tipiche in base a un documento representative utilizzato dall'applicazione e quindi la stima il numero di operazioni si prevede di eseguire un secondo.  Assicurarsi di misurare e includere query tipiche e l'utilizzo di script DocumentDB anche.

>[AZURE.NOTE]Se si dispone di tipi di documenti che variano in modo significativo in termini di dimensioni e il numero di proprietà indicizzate, quindi registrare le spese di unità di richiesta di operazione applicabile associate a ogni *tipo* di documento tipico.

Per esempio:

1. Registrare la spesa unitaria richiesta creazione (inserimento) di un documento tipico. 
2. Registrare le spese di unità di richiesta di lettura di un documento tipico.
3. Registrare le spese di unità di richiesta di aggiornamento di un documento tipico.
3. Registrare le spese di unità di richiesta di query comuni, tipico documento.
4. Registrare la spesa unitaria di richiesta di tutti gli script personalizzati (stored procedure, trigger, funzioni definite dall'utente) utilizzata dall'applicazione
5. Calcolare le unità richiesta necessari specificate il numero stimato di operazioni che si prevede di eseguire per eseguire un secondo.

##<a name="a-request-unit-estimation-example"></a>Un esempio di valutazione di unità richiesta
Valutare la possibilità di documento ~ 1KB seguente:

    {
     "id": "08259",
    "description": "Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX",
    "tags": [
        {
        "name": "cereals ready-to-eat"
        },
        {
        "name": "kellogg"
        },
        {
        "name": "kellogg's crispix"
        }
    ],
    "version": 1,
    "commonName": "Includes USDA Commodity B855",
    "manufacturerName": "Kellogg, Co.",
    "isFromSurvey": false,
    "foodGroup": "Breakfast Cereals",
    "nutrients": [
        {
        "id": "262",
        "description": "Caffeine",
        "nutritionValue": 0,
        "units": "mg"
        },
        {
        "id": "307",
        "description": "Sodium, Na",
        "nutritionValue": 611,
        "units": "mg"
        },
        {
        "id": "309",
        "description": "Zinc, Zn",
        "nutritionValue": 5.2,
        "units": "mg"
        }
    ],
    "servings": [
        {
        "amount": 1,
        "description": "cup (1 NLEA serving)",
        "weightInGrams": 29
        }
    ]
    }

>[AZURE.NOTE]I documenti sono minimizzati in DocumentDB, in modo che il sistema calcolato dimensioni del documento precedente sono leggermente inferiore a 1KB.


Nella tabella seguente mostra richiesta approssimativa spese di unità per le operazioni tipiche questo documento (la spesa unitaria richiesta approssimativa presuppone che il livello di coerenza account sia impostato su "Sessione" e che tutti i documenti vengono indicizzati automaticamente):

Operazione|Richiedere spesa unitaria 
---|---
Creazione di documenti|~ 15 RU 
Documento di lettura|~ 1 RU
Documento di query in base all'id|~2.5 RU

Inoltre, questa tabella mostra richiesta approssimativa spese di unità per le query tipiche utilizzate nell'applicazione:

Query|Richiedere spesa unitaria|# dei documenti restituiti
---|---|--- 
Selezionare gli alimenti per id|~2.5 RU|1 
Selezionare gli alimenti dal produttore|~ 7 RU|7
Selezionare il gruppo di alimenti e ordine di peso|~ 70 RU|100
Selezionare gli 10 alimenti superiore in un gruppo di cibo|~ 10 RU|10

>[AZURE.NOTE]In base alle tariffe RU variano in base al numero di documenti restituiti.

Con queste informazioni, è possibile valutare i requisiti di RU per questa applicazione dato il numero di query si prevede che al secondo e operazioni:

Operazione/Query|Numero stimato al secondo|Destinatari necessari 
---|---|--- 
Creazione di documenti|10|150 
Documento di lettura|100|100 
Selezionare gli alimenti dal produttore|25|175 
Selezionare il gruppo di alimenti|10|700 
Selezionare primi 10|15|Totale 150|155|1275

In questo caso, si prevede un requisito di velocità media del 1,275 RU/s.  Arrotondamento fino a 100 più vicino, è necessario effettuare il provisioning di 1.300 RU/s per la raccolta dell'applicazione.

##<a id="RequestRateTooLarge"></a>Superamento dei limiti di velocità riservato
Tenere presente che il consumo di unità richiesta viene valutato come un tasso al secondo. Per le applicazioni che superano il tasso di unità di provisioning richiesta per una raccolta, le richieste a tale raccolta saranno filtrate fino a quando il tasso inserisce sotto il livello di riservato. Quando si verifica un accelerazione, il server in modo preemptive terminare la richiesta con RequestRateTooLargeException (codice di stato HTTP 429) e restituire l'intestazione x-ms-Riprova-dopo-ms che indica la quantità di tempo in millisecondi, che l'utente deve attendere prima di eseguire nuovamente la richiesta.

    HTTP Status 429
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

Se si usano le query .NET Client SDK e LINQ, quindi la maggior parte dei casi che non è mai necessario di occuparsi di questa eccezione, come la versione corrente di .NET Client SDK intercetta in modo implicito questa risposta, rispetta specificato server Riprova dopo intestazione e la richiesta. A meno che l'account è utilizzata contemporaneamente da più client, Riprova successiva avrà esito positivo.

Se si dispone di più client, in modo cumulativo operativo sopra la frequenza di richiesta, il comportamento di Riprova predefinito non è sufficiente e il client genererà DocumentClientException con codice di stato 429 all'applicazione. In casi come questo, provare a gestione comportamento Riprova e logica di errore dell'applicazione routine di gestione degli o aumento della velocità riservata per la raccolta.

##<a name="next-steps"></a>Passaggi successivi

Per altre informazioni riservate produttività con Azure DocumentDB database, esplorare queste risorse:
 
- [DocumentDB prezzi](https://azure.microsoft.com/pricing/details/documentdb/)
- [Gestione delle capacità DocumentDB](documentdb-manage.md) 
- [Modellazione dei dati in DocumentDB](documentdb-modeling-data.md)
- [Livelli di prestazioni DocumentDB](documentdb-partition-data.md)

Per ulteriori informazioni su DocumentDB, vedere la [documentazione](https://azure.microsoft.com/documentation/services/documentdb/)di Azure DocumentDB. 

Per iniziare con scala e delle prestazioni con DocumentDB, vedere [prestazioni e scala test con Azure DocumentDB](documentdb-performance-testing.md).


[1]: ./media/documentdb-request-units/queryexplorer.png 
[2]: ./media/documentdb-request-units/RUEstimatorUpload.png
[3]: ./media/documentdb-request-units/RUEstimatorDocuments.png
[4]: ./media/documentdb-request-units/RUEstimatorResults.png
[5]: ./media/documentdb-request-units/RUCalculator2.png
