
<properties
    pageTitle="Eseguire la migrazione a servizi Cognitive Azure consigli API da DataMarket raccomandazioni API | Microsoft Azure"
    description="Azure apprendimento consigli - la migrazione al servizio cognitive consigli"
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
    ms.date="09/01/2016"
    ms.author="luisca"/>


# <a name="migrate-to-azure-cognitive-services-recommendations-api-from-the-datamarket-recommendations-api"></a>Eseguire la migrazione a servizi Cognitive Azure consigli API da DataMarket raccomandazioni API
In questo articolo viene illustrato come eseguire la migrazione da [Microsoft DataMarket consigli API](https://datamarket.azure.com/dataset/amla/recommendations) all' [API di suggerimenti di Microsoft Azure Cognitive dei servizi](https://www.microsoft.com/cognitive-services/en-us/recommendations-api).

L'API di consigli DataMarket verrà interrotto accettare nuovi clienti il 31 dicembre 2016 e verrà eliminato il 28 febbraio 2017.

## <a name="how-do-i-start-using-the-azure-cognitive-services-recommendations-api"></a>Come si inizia a usare l'API di consigli servizi Cognitive Azure?

Per eseguire la migrazione all'API consigli servizi Cognitive, eseguire le operazioni seguenti:

1.  Se si dispone già di un abbonamento Azure, [iscriversi](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/Recommendations/pricingtier/S1) per uno. 

1.  Istruzioni dettagliate tra la [Guida introduttiva](cognitive-services-recommendations-quick-start.md) per iscriversi a Cognitive API di suggerimenti di servizi e a livello di programmazione. 

1.  Visualizzare la [pagina di destinazione Cognitive servizi consigli API](https://www.microsoft.com/cognitive-services/en-us/recommendations-api) per informazioni sul servizio e trovare la documentazione.

## <a name="i-used-the-recommendations-ui-to-build-my-models-is-there-a-similar-tool-for-the-cognitive-services-recommendations-api"></a>Ho utilizzato l'interfaccia utente suggerimenti per creare i modelli. È disponibile uno strumento simile per l'API di consigli servizi Cognitive?

Assolutamente! L'URL per la nuova [Interfaccia utente consigli](http://recommendations-portal.azurewebsites.net/) è http://recommendations-portal.azurewebsites.net. 

>[AZURE.NOTE] Le credenziali DataMarket non funzionano qui. Iscriversi a un abbonamento nel portale di Azure e ottenere la chiave Account necessari per utilizzare la nuova [Interfaccia utente di suggerimenti](http://recommendations-portal.azurewebsites.net/).
Se non si dispone di un tasto di Account, vedere Passaggio 1 della [Guida introduttiva](cognitive-services-recommendations-quick-start.md).

##<a name="is-the-new-api-format-the-same-as-the-datamarket-recommendations-api"></a>Corrisponde al nuovo formato di API API consigli DataMarket?

L'API supporta gli stessi scenari e flussi di processo come tali scenari supportati nella versione DataMarket, ma l'interfaccia API effettivo è stato aggiornato per essere conformi alle [Linee guida API REST di Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Le API sono più coerente e lavoro con strumenti migliorati che supportano Swagger.

Per comprendere ognuna delle API, estrarre [explorer API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3db).
Utilizzare *provare* pulsante per provare una chiamata API. Il formato di file utilizzata dall'API consigli (catalogo e l'utilizzo di file) non è cambiata in modo che è possibile continuare a usare stesso file e/o infrastruttura creata per generare i file.

##<a name="what-are-some-new-features-in-the-cognitive-services-recommendations-api"></a>Quali sono alcune nuove caratteristiche dell'API consigli Services Cognitive?

Negli ultimi due mesi, ha rilasciato nuove funzionalità per l'API di consigli Cognitive servizi:
-   Consigli dell'interfaccia utente per la formazione e verifica dei modelli di senza dover scrivere una sola riga di codice
-   Batch di classificazione per consentire di migliaia di suggerimenti contemporaneamente
-   Creare metriche supporto per la qualità dei modelli di suggerimenti di query
-   Supporto per le regole aziendali
-   Possibilità di enumerare e scaricare i file di utilizzo e catalogo
-   Supporto per la compilazione di classificazione per la qualità delle caratteristiche di elementi in un modello di suggerimenti di query
-   Nuova possibilità di cercare un prodotto nel catalogo

## <a name="when-does-microsoft-stop-supporting-the-datamarket-recommendations-api"></a>Quando Microsoft interrompere che supporta l'API di consigli DataMarket?

[API consigli su DataMarket](https://datamarket.azure.com/dataset/amla/recommendations) non accetta nuovi clienti dopo il 31 dicembre 2016 e il 28 febbraio 2017 verrà rimossa completamente. 

## <a name="what-if-i-dont-have-the-data-that-i-need-to-recreate-my-models-in-the-cognitive-services-recommendations-api"></a>Cosa fare se non si dispone i dati che è necessario ricreare i modelli dell'API consigli Services Cognitive?

Si desidera rendere più semplice possibile questo passaggio dell'utente. Possiamo consentono a spostare i vecchi modelli dall'account DataMarket alla nuova sottoscrizione Azure Cognitive servizi consigli API. Contatta [mlapi@microsoft.com](mailto://mlapi@microsoft.com). Si verrà chiesto di fornire i DataMarket ID dell'abbonamento e l'ID di abbonamento a servizi Cognitive prima è associare i modelli con il nuovo account.
