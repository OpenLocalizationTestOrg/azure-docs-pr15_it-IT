<properties
    pageTitle="Aggiungere la casella di connessione alle App logica | Microsoft Azure"
    description="Panoramica del connettore casella con i parametri di API REST"
    services=""
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

# <a name="get-started-with-the-box-connector"></a>Iniziare a utilizzare il connettore casella
Connettersi alla casella e creare file, Elimina file e altro ancora. 

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema.

Le caselle, è possibile:

- Definire il flusso di business in base ai dati che viene visualizzato nella finestra. 
- Utilizzare trigger quando un file viene creato o aggiornato.
- Usare le azioni che copiare un file, eliminare un file e altro ancora. Queste azioni ottenere una risposta e quindi verificare l'output per le altre azioni. Quando viene modificato un file nella casella, ad esempio, è possibile utilizzare il file e inviarlo tramite posta elettronica con Office 365.

Per aggiungere un'operazione logica App, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni
Casella include i trigger e le azioni seguenti.

| Trigger | Azioni|
| --- | --- |
|<ul><li>Quando viene creato un file</li><li>Quando si modifica un file</li></ul> | <ul><li>Creare file</li><li>Quando viene creato un file</li><li>Copiare file</li><li>Eliminare file</li><li>Estrarre archivio cartella</li><li>Ottenere contenuto del file con id</li><li>Ottenere contenuto del file con percorso</li><li>Ottenere i metadati di file con id</li><li>Ottenere i metadati di file con percorso</li><li>File di aggiornamento</li><li>Quando si modifica un file</li></ul>

Tutti i connettori supportano dati nei formati XML e JSON.

## <a name="create-a-connection-to-box"></a>Creare una connessione alla casella
Quando si aggiunge il connettore alle App logica, è necessario autorizzare App logica a cui connettersi la casella.

>[AZURE.INCLUDE [Steps to create a connection to box](../../includes/connectors-create-api-box.md)]

Dopo aver creato la connessione, immettere le proprietà delle caselle. Il **riferimento all'API REST** in questo argomento sono descritte le proprietà.

>[AZURE.TIP] È possibile utilizzare la stessa connessione casella in altre applicazioni di logica.

## <a name="swagger-rest-api-reference"></a>Guida di riferimento all'API REST swagger
Si applica alla versione: 1.0.

### <a name="create-file"></a>Creare file
Carica un file nella casella.  
```POST: /datasets/default/files```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|stringa|Sì|query|Nessuno |Percorso della cartella da caricare il file in casella|
|nome|stringa|Sì|query|Nessuno |Nome del file da creare nella casella|
|corpo|String(Binary) |Sì|corpo|Nessuno |Contenuto del file da caricare in casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="when-a-file-is-created"></a>Quando viene creato un file
Attiva un flusso quando viene creato un nuovo file in una cartella di casella.  
```GET: /datasets/default/triggers/onnewfile```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID cartella|stringa|Sì|query|Nessuno |Identificatore univoco della cartella nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="copy-file"></a>Copiare file
Copia di un file alla casella.  
```POST: /datasets/default/copyFile```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|origine|stringa|Sì|query|Nessuno |URL di file di origine|
|destinazione|stringa|Sì|query| Nessuno|Percorso di file di destinazione nella casella, tra cui nome file di destinazione|
|sovrascrivere|valore booleano|No|query| Nessuno|Sovrascrive il file di destinazione se è impostata su "true"|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="delete-file"></a>Eliminare file
Elimina un file dalla casella.  
```DELETE: /datasets/default/files/{id}```


| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno |Identificatore univoco del file da eliminare dalla casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="extract-archive-to-folder"></a>Estrarre archivio cartella
Estrae un file di archivio in una cartella nella casella (esempio: zip).  
```POST: /datasets/default/extractFolderV2```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|origine|stringa|Sì|query| |Percorso del file di archivio|
|destinazione|stringa|Sì|query| |Percorso nella casella per estrarre il contenuto di archivio|
|sovrascrivere|valore booleano|No|query| |Sovrascrive i file di destinazione se è impostata su "true"|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-file-content-using-id"></a>Ottenere contenuto del file con id
Recupera il contenuto del file dalla casella con id.  
```GET: /datasets/default/files/{id}/content```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno |Identificatore univoco del file nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-file-content-using-path"></a>Ottenere contenuto del file con percorso
Recupera il contenuto del file dalla casella utilizzando percorso.  
```GET: /datasets/default/GetFileContentByPath```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|percorso|stringa|Sì|query|Nessuno |Percorso univoco per il file nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-file-metadata-using-id"></a>Ottenere i metadati di file con id
Recupera i metadati dei file dalla casella utilizzando l'id di file.  
```GET: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso| Nessuno|Identificatore univoco del file nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="get-file-metadata-using-path"></a>Ottenere i metadati di file con percorso
Recupera i metadati dei file dalla casella utilizzando percorso.  
```GET: /datasets/default/GetFileByPath```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|percorso|stringa|Sì|query|Nessuno |Percorso univoco per il file nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="update-file"></a>File di aggiornamento
Aggiorna un file nella casella.  
```PUT: /datasets/default/files/{id}```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso| Nessuno|Identificatore univoco del file da aggiornare nella casella|
|corpo|String(Binary) |Sì|corpo|Nessuno |Contenuto del file da aggiornare nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


### <a name="when-a-file-is-modified"></a>Quando si modifica un file
Attiva un flusso quando si modifica un file in una cartella di casella.  
```GET: /datasets/default/triggers/onupdatedfile```

| Nome|Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID cartella|stringa|Sì|query|Nessuno |Identificatore univoco della cartella nella casella|

#### <a name="response"></a>Risposta
|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti

#### <a name="datasetsmetadata"></a>DataSetsMetadata

|Nome della proprietà | Tipo di dati | Obbligatorio|
|---|---|---|
|tabulare|non è definito|No|
|BLOB|non è definito|No|

#### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|origine|stringa|No|
|displayName|stringa|No|
|codificando gli|stringa|No|
|tableDisplayName|stringa|No|
|tablePluralName|stringa|No|

#### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|origine|stringa|No|
|displayName|stringa|No|
|codificando gli|stringa|No|

#### <a name="blobmetadata"></a>BlobMetadata

|Nome della proprietà | Tipo di dati |Obbligatorio|
|---|---|---|
|ID|stringa|No|
|Nome|stringa|No|
|DisplayName|stringa|No|
|Percorso|stringa|No|
|LastModified|stringa|No|
|Dimensioni|numero intero|No|
|MediaType|stringa|No|
|IsFolder|valore booleano|No|
|ETag|stringa|No|
|FileLocator|stringa|No|

## <a name="next-steps"></a>Passaggi successivi

[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).
