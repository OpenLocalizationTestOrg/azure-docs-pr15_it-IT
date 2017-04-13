<properties
pageTitle="OneDrive per le aziende | Microsoft Azure"
description="Creare App logica con il servizio di Azure App. Connettersi a OneDrive for Business per gestire i file. È possibile eseguire diverse operazioni, ad esempio il caricamento, aggiornare, ottenere ed eliminare i file."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-onedrive-for-business-connector"></a>Introduzione a OneDrive for Business connector

Connettersi a OneDrive for Business per gestire i file. È possibile eseguire diverse operazioni, ad esempio il caricamento, aggiornare, ottenere ed eliminare i file.

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2015-08-01-anteprima dello schema. 

È possibile iniziare a creare un'app logica a questo punto, vedere [creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Trigger e azioni

OneDrive for Business connector può essere utilizzato come un'azione. ha trigger. Tutti i connettori supportano dati nei formati XML e JSON. 

 OneDrive for Business connector è le seguenti azioni e/o trigger disponibili:

### <a name="onedrive-for-business-actions"></a>OneDrive per le azioni di Business
È possibile eseguire queste azioni:

|Azione|Descrizione|
|--- | ---|
|[GetFileMetadata](connectors-create-api-onedriveforbusiness.md#getfilemetadata)|Recupera i metadati di un file in OneDrive for Business con id|
|[UpdateFile](connectors-create-api-onedriveforbusiness.md#updatefile)|Aggiornare un file in OneDrive for Business|
|[DeleteFile](connectors-create-api-onedriveforbusiness.md#deletefile)|Elimina un file da OneDrive for Business|
|[GetFileMetadataByPath](connectors-create-api-onedriveforbusiness.md#getfilemetadatabypath)|Recupera i metadati di un file in OneDrive for Business usando percorso|
|[GetFileContentByPath](connectors-create-api-onedriveforbusiness.md#getfilecontentbypath)|Recupera contenuto di un file in OneDrive for Business usando percorso|
|[GetFileContent](connectors-create-api-onedriveforbusiness.md#getfilecontent)|Recupera contenuto di un file in OneDrive for Business con id|
|[CreateFile](connectors-create-api-onedriveforbusiness.md#createfile)|Consente di caricare un file di OneDrive for Business|
|[CopyFile](connectors-create-api-onedriveforbusiness.md#copyfile)|Copia di un file in OneDrive for Business|
|[ListFolder](connectors-create-api-onedriveforbusiness.md#listfolder)|Elenco di file in un OneDrive per cartella commerciale|
|[ListRootFolder](connectors-create-api-onedriveforbusiness.md#listrootfolder)|Elenco di file in OneDrive per la cartella principale Business|
|[ExtractFolderV2](connectors-create-api-onedriveforbusiness.md#extractfolderv2)|Estrae una cartella di onedrive for Business|
### <a name="onedrive-for-business-triggers"></a>Attiva OneDrive for Business
È possibile intercettare questi eventi:

|Trigger | Descrizione|
|--- | ---|
|Quando viene creato un file|Attiva un flusso quando viene creato un nuovo file in una cartella di OneDrive for Business|
|Quando si modifica un file|Attiva un flusso quando si modifica un file in una cartella di OneDrive for Business|


## <a name="create-a-connection-to-onedrive-for-business"></a>Creare una connessione a OneDrive for Business
Per creare App logica con OneDrive for Business, è necessario creare una **connessione** quindi fornire i dettagli per le proprietà seguenti: 

|Proprietà| Obbligatorio|Descrizione|
| ---|---|---|
|Token|Sì|Fornire OneDrive for Business credenziali|
Dopo aver creato la connessione, è possibile utilizzare per eseguire le azioni e attendere trigger descritto in questo articolo. 

>[AZURE.INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]

>[AZURE.TIP] È possibile usare la connessione in altre applicazioni di logica.

## <a name="reference-for-onedrive-for-business"></a>Guida di riferimento per OneDrive for Business
Si applica alla versione: 1.0

## <a name="getfilemetadata"></a>GetFileMetadata
Ottenere i metadati di file con id: recupera i metadati di un file in OneDrive for Business con id 

```GET: /datasets/default/files/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Specificare il file|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="updatefile"></a>UpdateFile
File di aggiornamento: gli aggiornamenti di un file in OneDrive for Business 

```PUT: /datasets/default/files/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Specificare il file da aggiornare|
|corpo| |Sì|corpo|Nessuno|Contenuto del file da aggiornare in OneDrive for Business|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="deletefile"></a>DeleteFile
Eliminare file: elimina un file da OneDrive for Business 

```DELETE: /datasets/default/files/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Specificare il file da eliminare|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getfilemetadatabypath"></a>GetFileMetadataByPath
Ottenere i metadati di file con percorso: recupera i metadati di un file in OneDrive for Business usando percorso 

```GET: /datasets/default/GetFileByPath``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|percorso|stringa|Sì|query|Nessuno|Percorso univoco per il file in OneDrive for Business|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getfilecontentbypath"></a>GetFileContentByPath
Ottenere contenuto del file con percorso: recupera contenuto di un file in OneDrive for Business usando percorso 

```GET: /datasets/default/GetFileContentByPath``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|percorso|stringa|Sì|query|Nessuno|Percorso univoco per il file in OneDrive for Business|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="getfilecontent"></a>GetFileContent
Ottenere contenuto del file con id: recupera contenuto di un file in OneDrive for Business con id 

```GET: /datasets/default/files/{id}/content``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Specificare il file|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="createfile"></a>CreateFile
Creare file: consente di caricare un file di OneDrive for Business 

```POST: /datasets/default/files``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|folderPath|stringa|Sì|query|Nessuno|Percorso della cartella per caricare il file in OneDrive for Business|
|nome|stringa|Sì|query|Nessuno|Nome del file da creare in OneDrive for Business|
|corpo| |Sì|corpo|Nessuno|Contenuto del file da caricare in OneDrive for Business|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="copyfile"></a>CopyFile
File di copia: copia di un file in OneDrive for Business 

```POST: /datasets/default/copyFile``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|origine|stringa|Sì|query|Nessuno|URL di file di origine|
|destinazione|stringa|Sì|query|Nessuno|Percorso del file di destinazione in OneDrive for Business, tra cui nome file di destinazione|
|sovrascrivere|valore booleano|No|query|FALSO|Sovrascrive il file di destinazione se è impostata su "true"|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="onnewfile"></a>OnNewFile
Quando viene creato un file: attiva un flusso quando viene creato un nuovo file in una cartella di OneDrive for Business 

```GET: /datasets/default/triggers/onnewfile``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID cartella|stringa|Sì|query|Nessuno|Specificare una cartella|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="onupdatedfile"></a>OnUpdatedFile
Quando si modifica un file: attiva un flusso quando si modifica un file in una cartella di OneDrive for Business 

```GET: /datasets/default/triggers/onupdatedfile``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID cartella|stringa|Sì|query|Nessuno|Specificare una cartella|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="listfolder"></a>ListFolder
Elencare i file nella cartella: elenchi di file in un OneDrive per cartella commerciale 

```GET: /datasets/default/folders/{id}``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|ID|stringa|Sì|percorso|Nessuno|Specificare la cartella|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="listrootfolder"></a>ListRootFolder
Cartella radice di elenco: Elenca i file in OneDrive per la cartella principale Business 

```GET: /datasets/default/folders``` 

Non sono presenti parametri per la chiamata
#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="extractfolderv2"></a>ExtractFolderV2
Estrarre cartella: estrae una cartella di onedrive for Business 

```POST: /datasets/default/extractFolderV2``` 

| Nome| Tipo di dati|Obbligatorio|Disponibile In|Valore predefinito|Descrizione|
| ---|---|---|---|---|---|
|origine|stringa|Sì|query|Nessuno|Percorso del file di archivio|
|destinazione|stringa|Sì|query|Nessuno|Percorso di OneDrive for Business per estrarre il contenuto di archivio|
|sovrascrivere|valore booleano|No|query|FALSO|Sovrascrive i file di destinazione se è impostata su "true"|

#### <a name="response"></a>Risposta

|Nome|Descrizione|
|---|---|
|200|Ok|
|impostazione predefinita|Operazione non è riuscita.|


## <a name="object-definitions"></a>Definizioni di oggetti 

### <a name="datasetsmetadata"></a>DataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|tabulare|non è definito|No |
|BLOB|non è definito|No |



### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|origine|stringa|No |
|displayName|stringa|No |
|codificando gli|stringa|No |
|tableDisplayName|stringa|No |
|tablePluralName|stringa|No |



### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|origine|stringa|No |
|displayName|stringa|No |
|codificando gli|stringa|No |



### <a name="blobmetadata"></a>BlobMetadata


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|
|ID|stringa|No |
|Nome|stringa|No |
|DisplayName|stringa|No |
|Percorso|stringa|No |
|LastModified|stringa|No |
|Dimensioni|numero intero|No |
|MediaType|stringa|No |
|IsFolder|valore booleano|No |
|ETag|stringa|No |
|FileLocator|stringa|No |



### <a name="object"></a>Oggetto


| Nome della proprietà | Tipo di dati | Obbligatorio |
|---|---|---|


## <a name="next-steps"></a>Passaggi successivi
[Creare un'app di logica](../app-service-logic/app-service-logic-create-a-logic-app.md)