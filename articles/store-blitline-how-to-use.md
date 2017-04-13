<properties 
    pageTitle="Come usare Blitline come immagine di elaborazione - Azure caratteristica Guida" 
    description="Informazioni su come usare il servizio Blitline per elaborare le immagini all'interno di un'applicazione Azure." 
    services="" 
    documentationCenter=".net" 
    authors="blitline-dev" 
    manager="jason@blitline.com" 
    editor="jason@blitline.com"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2014" 
    ms.author="support@blitline.com"/>
# <a name="how-to-use-blitline-with-azure-and-azure-storage"></a>Come usare Blitline con Azure e lo spazio di archiviazione di Azure

Questa guida viene illustrato come accedere ai servizi Blitline e su come inviare processi a Blitline.

## <a name="what-is-blitline"></a>Che cos'è Blitline?

Blitline è un'immagine basata su cloud servizio che consente l'elaborazione di immagini a livello aziendale una frazione del prezzo da costi per creare il database di elaborazione.

Il fatto è che l'elaborazione di immagini sia stata eseguita in modo continuativo, in genere ricreato novo per ogni sito Web. Abbiamo realizzare perché abbiamo sviluppato loro un milione di volte troppo. Un giorno che deciso che probabilmente è ora è sufficiente farlo per tutti gli utenti. È in grado di eseguire questa operazione, per fare in modo veloce ed efficiente e salvare tutti aziendale nel frattempo.

Per ulteriori informazioni, vedere [http://www.blitline.com](http://www.blitline.com).

## <a name="what-blitline-is-not"></a>Cosa Blitline non è...

Per chiarire ciò che cos'è utile per Blitline, è spesso più semplice identificare Blitline non operazioni prima di iniziare.

- Blitline non ha widget HTML per caricare le immagini. È necessario disporre le immagini disponibili pubblicamente o con autorizzazioni limitate disponibili per Blitline raggiungere.

- Blitline eseguire live elaborazione delle immagini, ad esempio Aviary.com

- Blitline non accettare il caricamento di immagini, non è possibile inserire le immagini a Blitline direttamente. È necessario inviarli per lo spazio di archiviazione di Azure o altre posizioni Blitline supporta e quindi indicare Blitline operazioni ottenerli.

- Blitline market parallela ed eseguire elaborazioni icona del. Ovvero è necessario fornire un postback_url ed è possibile sapere quando abbiamo terminato elaborazione.

## <a name="create-a-blitline-account"></a>Creare un account Blitline

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a name="how-to-create-a-blitline-job"></a>Come creare un processo Blitline

Blitline utilizza JSON per definire le azioni che si desidera impostare un'immagine. Questo JSON è costituito da alcuni campi semplici.

Nell'esempio più semplice è il seguente:

        json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Di seguito sono JSON che verrà eseguita un'immagine "src" "... boys.jpeg" e quindi ridimensionare l'immagine a 240 x 140.

ID applicazione è un elemento che è possibile trovare nella scheda **Informazioni di connessione** o **GESTISCI** in Azure. È l'identificatore segreta che consente di eseguire i processi in Blitline.

Il parametro "Salva" identifica informazioni in cui si desidera inserire l'immagine, una volta abbiamo elaborato. In questo caso, non definito uno. Se non è stata definita alcuna ubicazione Blitline verrà archiviata in locale (e temporaneamente) in una posizione sul cloud univoco. Sarà possibile ottenere tale posizione da JSON restituito da Blitline quando si effettua la Blitline. L'identificatore "immagini" è necessario e viene restituito all'utente quando per identificare questa particolare Salva immagine.

È possibile trovare ulteriori informazioni sulle *funzioni* è supportato qui: <http://www.blitline.com/docs/functions>

È inoltre possibile trovare la documentazione relativa alle opzioni del processo: <http://www.blitline.com/docs/api>

Dopo avere inserito le JSON è sufficiente è **POST** per`http://api.blitline.com/job`

Si otterrà JSON nuovamente che ha un aspetto simile al seguente:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


In questo modo si Blitline ha ricevuto la richiesta, ha inserito nella coda di elaborazione e quando è stata completata l'immagine sarà disponibile in: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR\_APP\_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a name="how-to-save-an-image-to-your-azure-storage-account"></a>Come salvare un'immagine al proprio account di archiviazione Azure

Se si dispone di un account di archiviazione Azure, è possibile impostare facilmente Blitline push immagini elaborate nel contenitore di Azure. Aggiungendo un "azure_destination" si definiscono la posizione e le autorizzazioni per Blitline a.

Ecco un esempio:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Inserendo i valori di CAPITALIZED con uno personalizzato, è possibile inviare questo JSON a http://api.blitline.com/job e verrà elaborata con un filtro Sfocatura e quindi inserito alla destinazione Azure immagine "src".

###<a name="please-note"></a>Si noti che:

Le SA devono contenere l'intero url SA, incluso il nome del file di destinazione.

Esempio:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


È inoltre possibile leggere la versione più recente di documenti archiviazione Azure del Blitline [qui](http://www.blitline.com/docs/azure_storage).


## <a name="next-steps"></a>Passaggi successivi

Visitare blitline.com per ulteriori informazioni su tutte le altre funzionalità:

* API Endpoint Blitline documenti <http://www.blitline.com/docs/api>
* Funzioni Blitline API <http://www.blitline.com/docs/functions>
* Esempi di Blitline API <http://www.blitline.com/docs/examples>
* Terza parte di una raccolta Nuget <http://nuget.org/packages/Blitline.Net>
