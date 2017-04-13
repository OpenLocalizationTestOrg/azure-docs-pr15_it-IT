<properties
    pageTitle="Aggiungere Microsoft Translator nelle App logica | Microsoft Azure"
    description="Panoramica del connettore Microsoft Translator con i parametri di API REST"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Iniziare a utilizzare il connettore Microsoft Translator
Connettersi a Microsoft Translator per tradurre il testo, rileva una lingua e altro ancora. Con Microsoft Translator, è possibile: 

- Definire il flusso di business in base ai dati che viene visualizzato da Microsoft Translator. 
- Usare azioni per tradurre il testo, rileva una lingua e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Ad esempio, quando viene creato un nuovo file in Dropbox, è possibile tradurre il testo nel file in un'altra lingua utilizzando Microsoft Translator.

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Microsoft Translator include le azioni seguenti. Non esistono alcun trigger.

Trigger | Azioni
--- | ---
Nessuno | <ul><li>Rileva lingua</li><li>Sintesi vocale da testo</li><li>Traduci testo</li><li>Ottenere lingue</li><li>Ottenere lingue sintesi e riconoscimento vocale</li></ul>

Tutti i connettori supportano dati nei formati XML e JSON.


## <a name="create-a-connection-to-microsoft-translator"></a>Creare una connessione a Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Guida di riferimento all'API REST swagger
Si applica alla versione: 1.0.

### <a name="detect-language"></a>Rileva lingua    
Rileva lingua di origine del dato testo.  
```GET: /Detect```

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|stringa|Sì|query|Nessuno |Testo la cui lingua compariranno|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="text-to-speech"></a>Sintesi vocale da testo    
Converte un determinato testo in sintesi e riconoscimento vocale come flusso audio in formato wave.  
```GET: /Speak```

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|stringa|Sì|query|Nessuno |Testo da convertire|
|lingua|stringa|Sì|query|Nessuno |Codice di lingua per generare sintesi e riconoscimento vocale (esempio: ' en-us')|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="translate-text"></a>Traduci testo    
Converte il testo in una lingua specificata utilizzando Microsoft Translator.  
```GET: /Translate```

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|query|stringa|Sì|query|Nessuno |Testo da tradurre|
|languageTo|stringa|Sì|query| Nessuno|Codice di lingua di destinazione (esempio: "fr")|
|languageFrom|stringa|No|query|Nessuno |Lingua di origine. Se non viene specificato, Microsoft Translator tenterà di rilevamento automatico. (esempio: en)|
|categoria|stringa|No|query|generale |Categoria di traduzione (impostazione predefinita: "generale")|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-languages"></a>Ottenere lingue    
Recupera tutte le lingue che supporta Microsoft Translator.  
```GET: /TranslatableLanguages```

Non sono presenti parametri per la chiamata. 

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-speech-languages"></a>Ottenere lingue sintesi e riconoscimento vocale    
Recupera le lingue disponibili per la sintesi vocale.  
```GET: /SpeakLanguages``` 

Non sono presenti parametri per la chiamata.

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|

## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Lingua: modello lingua per le lingue convertibile Microsoft Translator

|Nome della proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|Codice|stringa|No|
|Nome|stringa|No|


## <a name="next-steps"></a>Passaggi successivi

[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Tornare all' [elenco di API](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
