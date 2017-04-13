
<properties
    pageTitle="Ottenere suggerimenti in batch: lavorare formazione consigli API | Microsoft Azure"
    description="Azure apprendimento consigli - ottenere suggerimenti in batch"
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
    ms.date="08/17/2016"
    ms.author="luisca"/>

# <a name="get-recommendations-in-batches"></a>Suggerimenti in batch

>[AZURE.NOTE] Ottenere suggerimenti in batch è più complessa Guida consigli uno alla volta. Controllare le API per informazioni su come ottenere suggerimenti per una singola richiesta:

> [Consigli per elemento](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br>
> [Consigli per elemento utente](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> Punteggio batch funziona solo per build che sono stati creati dopo il 21 luglio 2016.


Ci sono situazioni in cui è necessario ottenere suggerimenti per più di un elemento alla volta. Ad esempio, può essere utile nella creazione di una cache consigli o persino analisi dei tipi di suggerimenti che si desidera ottenere.

Operazioni punteggio batch, vengono chiamati, sono operazioni asincrone. È necessario inviare la richiesta, attendere il termine dell'operazione e quindi raccogliere i risultati.  

In modo più preciso, ecco i passaggi da seguire:

1.  Creare un contenitore di archiviazione Azure se non è già disponibile.
2.  Caricare un file di input che descrive ogni delle richieste di recommendation a archiviazione Blob Azure.
3.  Avviare il processo batch punteggio.
4.  Attendere il termine dell'operazione asincrono.
5.  Al termine dell'operazione, raccogliere i risultati dallo spazio di archiviazione Blob.

Verrà ora esaminato ciascuno di questi passaggi.

## <a name="create-a-storage-container-if-you-dont-have-one-already"></a>Creare un contenitore di archiviazione se non è già disponibile

Accedere al [portale di Azure](https://portal.azure.com) e creare un nuovo account di archiviazione se non è già disponibile. A tale scopo, passare al **Nuovo** > **dati** + **lo spazio di archiviazione** > **Account di archiviazione**.

Dopo aver ottenuto un account di archiviazione, è necessario creare i contenitori di blob nel punto in cui memorizzare l'input e output dell'esecuzione batch.

Caricare un file di input che descrive ogni il suggerimento richiede a archiviazione Blob - verrà chiamato input.json file qui.
Dopo aver ottenuto un contenitore, è necessario caricare un file che descrive ogni delle richieste che è necessario eseguire dal servizio di suggerimenti.

Un batch può essere eseguita solo un tipo di richiesta da una compilazione specifica. Viene illustrato come definire queste informazioni nella sezione successiva. Per ora, si supponga che si svolgeranno consigli elemento da una compilazione specifica. File di input contiene quindi le informazioni di input (in questo caso, gli elementi di partenza) per ognuna delle richieste.

Questo è un esempio di file input.json l'aspetto:

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }

Come si può notare, il file è un file JSON, in ognuna delle richieste ha le informazioni necessarie per inviare una richiesta di suggerimenti. Creare un file JSON simile per le richieste che è necessario soddisfare e copiarlo nel contenitore di sezioni appena creata nell'archiviazione Blob.

## <a name="kick-start-the-batch-job"></a>Avviare il processo batch

Il passaggio successivo consiste per inviare un nuovo processo batch. Per ulteriori informazioni, selezionare il [riferimento all'API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/).

Nel corpo della richiesta dell'API per definire la posizione in cui l'input, output e i file di errore necessario archiviare. È anche necessario definire le credenziali necessarie accedere a queste posizioni. Inoltre, è necessario specificare alcuni parametri che riguardano l'intero batch (il tipo di suggerimenti per richiedere la compilazione o un modello da utilizzare, il numero di risultati per la chiamata e così via).

Questo è un esempio dell'aspetto del corpo della richiesta:

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

Ecco alcuni aspetti importanti da tenere presente:

-   Al momento **authenticationType** deve sempre essere impostato su **PublicOrSas**.

-   È necessario ottenere un token di firma di Access condiviso (SA) per consentire l'API di suggerimenti leggere e scrivere da/verso l'account di archiviazione Blob. Ulteriori informazioni su come generare i token SA disponibili nella [pagina API consigli](../storage/storage-dotnet-shared-access-signature-part-1.md).

-   Solo **NOMEAPI** attualmente supportata sono **ItemRecommend**, che viene utilizzato per consigli elemento a altro. Il batch non supportano attualmente raccomandazioni per elemento utente.

## <a name="wait-for-the-asynchronous-operation-to-finish"></a>Attendere il termine dell'operazione asincrono

Quando si avvia l'operazione in blocco, la risposta restituisce l'intestazione percorso operazione che fornisce le informazioni necessarie tenere traccia dell'operazione.
Per rilevare l'operazione, utilizzare l' [API di stato operazione recuperare]( https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da)esattamente come per individuare l'operazione di un'operazione di compilazione.

## <a name="get-the-results"></a>Ottenere i risultati

Al termine dell'operazione, presupponendo che nessun errore, è possibile raccogliere i risultati dall'output nell'archiviazione Blob.

Nell'esempio seguente mostra come potrebbe apparire l'output. In questo esempio mostra i risultati per un batch con solo due richieste (per brevità).

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## <a name="learn-about-the-limitations"></a>Informazioni sulle limitazioni

-   È possibile chiamare solo una procedura batch per abbonamento alla volta.
-   Un file di input del processo batch non può essere più di 2 MB.
