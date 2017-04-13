## <a name="azure-storage-linked-service"></a>Spazio di archiviazione Azure collegato servizio

**Lo spazio di archiviazione di Azure collegato servizio** consente di collegare un account di archiviazione Azure a una factory di Azure dati utilizzando la **chiave account**. In questo modo factory dati accesso globale per l'archiviazione di Azure. La tabella seguente contiene una descrizione per gli elementi JSON specifici per il servizio di archiviazione di Azure collegato.

| Proprietà | Descrizione | Obbligatorio |
| :-------- | :----------- | :-------- |
| tipo | Impostare la proprietà tipo: **AzureStorage** | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi a archiviazione Azure per la proprietà connectionString. | Sì |

Per un archivio di Azure, vedere l'articolo seguente per passaggi per la visualizzazione/copiare la chiave account: [visualizzazione, copia e lo spazio di archiviazione rigenera i tasti di scelta](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

**Esempio:**  
  
    {  
        "name": "StorageLinkedService",  
        "properties": {  
            "type": "AzureStorage",  
            "typeProperties": {  
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
            }  
        }  
    }  


## <a name="azure-storage-sas-linked-service"></a>Spazio di archiviazione Azure SA collegato servizio  
Una firma di accesso condiviso (SA) consente di delega per l'accesso alle risorse nell'account di archiviazione. Questo errore indica che è possibile concedere che autorizzazioni agli oggetti nel proprio account di archiviazione un client limitate per un determinato periodo di tempo e con un determinato set di autorizzazioni, senza che sia necessario condividere i tasti di scelta account. Le associazioni di sicurezza è un URI che includa nei relativi parametri query tutte le informazioni necessarie per l'accesso a una risorsa di archiviazione autenticato. Per accedere alle risorse di archiviazione con le associazioni di protezione, il client deve solo passare al metodo o costruttore appropriato le associazioni di protezione. Per informazioni dettagliate sulle SA, vedere [condiviso firme Access: informazioni sul modello di SA](../articles/storage/storage-dotnet-shared-access-signature-part-1.md)
  
Il servizio di Azure archiviazione SA collegato consente di collegare un Account di archiviazione Azure a una factory di Azure dati utilizzando una firma di Access condiviso (SA). In questo modo la factory di dati con l'accesso con restrizioni/associate alla durata alle risorse tutte/specifiche (blob/contenitore) nell'archivio. La tabella seguente contiene una descrizione per gli elementi JSON specifici di servizio sa di spazio di archiviazione di Azure collegato. 

| Proprietà | Descrizione | Obbligatorio |
| :-------- | :----------- | :-------- |
| tipo | Impostare la proprietà tipo: **AzureStorageSas**  | Sì |
| sasUri | Specificare condiviso URI firma accesso alle risorse di archiviazione di Azure ad esempio blob, contenitore o tabella. Vedere le note di seguito per informazioni dettagliate. | Sì | 


**Esempio:**
  
    {  
        "name": "StorageSasLinkedService",  
        "properties": {  
            "type": "AzureStorageSas",  
            "typeProperties": {  
                "sasUri": "<storageUri>?<sasToken>"   
            }  
        }  
    }  

Quando si crea un **URI SA**, prendere in considerazione le operazioni seguenti:  

- Azure Data Factory supporta solo **Sa servizio**, non sa Account. Per informazioni su questi due tipi, vedere [Tipi di firme di Access condiviso](../articles/storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) .
- Necessità di **autorizzazioni** di lettura/scrittura appropriate deve essere impostato su oggetti in base a come verrà utilizzato il servizio collegato (lettura, scrittura, lettura/scrittura) nella factory dati.
- **Ora di scadenza** deve essere impostata in modo appropriato. Assicurarsi che l'accesso agli oggetti di archiviazione Azure non scada mai all'interno del periodo attivo della pipeline.
- URI devono essere creati nel contenitore/blob destra o a livello di tabella in base alle necessità. Uri SA su blob Azure consente al servizio dati Factory accedere a quel particolare blob. Uri sa a un contenitore di blob Azure consente al servizio dati Factory scorrere BLOB in tale contenitore. Se è necessario fornire l'accesso più o meno gli oggetti in un secondo momento o aggiornare URI SAS, ricordarsi di aggiornare il servizio collegato con il nuovo URI.   
