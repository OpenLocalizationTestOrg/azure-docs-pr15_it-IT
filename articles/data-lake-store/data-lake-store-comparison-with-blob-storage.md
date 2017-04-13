<properties
   pageTitle="Confronto di Azure dati Lake Store con Azure lo spazio di archiviazione Blob | Microsoft Azure"
   description="Confronto di Azure dati Lake Store con Azure lo spazio di archiviazione Blob"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/15/2016"
   ms.author="nitinme"/>

# <a name="comparing-azure-data-lake-store-and-azure-blob-storage"></a>Confronto tra Azure dati Lake archivio e archiviazione Blob Azure

La tabella in questo articolo sono riepilogate le differenze tra Azure dati Lake archivio e archiviazione Blob Azure lungo alcuni aspetti grande dell'elaborazione dei dati. Archiviazione Blob Azure è un generiche archivio scalable oggetto che deve essere una vasta gamma di scenari di spazio di archiviazione. Archivio Lake dati di Azure è un repository hyper scala ottimizzate per carichi di lavoro di grandi dimensioni dati analitica.

|    | Archivio Lake dati di Azure  | Archiviazione Blob Azure  |
|----|-----------------------|--------------------|
| Scopo  | Spazio di archiviazione ottimizzata per carichi di lavoro di grandi dimensioni dati analitica                                                                          | Oggetto di interesse generale archiviare per una vasta gamma di scenari di spazio di archiviazione                                                                                |
| Use case                                   | Batch interattivi, streaming analitica e dati di apprendimento computer come file di registro, IoT dati, fare clic su flussi, grandi set di dati | Qualsiasi tipo di dati testo o binario, ad esempio applicazione indietro fine, dati di backup, lo spazio di archiviazione di elementi multimediali per i dati di scopo di flusso e generale |
| Concetti fondamentali                                | Account di archivio di dati Lake contiene cartelle, che a sua volta contiene i dati archiviati come file | Account di archiviazione sono contenitori, che a sua volta dati in forma di BLOB |
| Struttura | File system gerarchico                                                                                                    | Archivio di oggetti con spazio dei nomi piatto  |
| API   | API REST su HTTPS | API REST su HTTP/HTTPS                                                                                                                            |
| API lato server                             | [API REST compatibile con WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx)                                                                                                 | [Archiviazione Blob Azure API REST](https://msdn.microsoft.com/library/azure/dd135733.aspx)                                                                                                                         |
| File Hadoop sistema Client                   | Sì                                                                                                                         | Sì                                                                                                                                                 |
| Operazioni sui dati - autenticazione            | In base a [identità di Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md) | In base a informazioni riservate condivisi - [Account i tasti di scelta](../storage/storage-create-storage-account.md#manage-your-storage-account) e [Condiviso firma tasti di scelta](../storage/storage-dotnet-shared-access-signature-part-1.md).                                                                       |
| Operazioni sui dati - protocollo di autenticazione     | OAuth 2.0. Chiamate devono contenere un JWT valido (JSON Web Token) rilasciato da Azure Active Directory                     | Codice di autenticazione messaggi basato su hash (HMAC). Chiamate devono contenere un hash SHA-256 codifica base 64 su una parte della richiesta HTTP. |
| Operazioni sui dati - autorizzazioni               | Controllo dell'accesso POSIX gli elenchi.  ACL in base a identità di Azure Active Directory possono essere impostati come livello di file e cartelle. | Per l'autorizzazione a livello di account: utilizzare i [Tasti di scelta Account](../storage/storage-create-storage-account.md#manage-your-storage-account)<br>Per account, contenitore o autorizzazione blob - Usa [Condiviso firma tasti di scelta](../storage/storage-dotnet-shared-access-signature-part-1.md) |
| Operazioni sui dati - verifica                    | Disponibile. Vedere [qui](data-lake-store-diagnostic-logs.md) per informazioni.                                                                                                                   | Disponibile                                                                                                                                           |
| Dati di crittografia inattivi                     | Trasparente, sul lato Server (disponibile a breve)<ul><li>Con i tasti servizio gestito</li><li>Con i tasti gestiti cliente in Azure KeyVault</li></ul>| <ul><li>Trasparente, sul lato Server</li> <ul><li>Con i tasti servizio gestito</li><li>Con i tasti gestiti cliente in Azure KeyVault (disponibile a breve)</li></ul><li>Crittografia lato client</li></ul> |
| Operazioni di gestione (ad esempio, creare Account) | [Controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-what-is.md) (RBAC) fornita da Azure per la gestione di account                                                                       | [Controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-what-is.md) (RBAC) fornita da Azure per la gestione di account                                                                                               |
| SDK per sviluppatori                              | .NET, Java, Node                                                                                                         | .NET, Java, Python, Node, C++, trascrizione                                                                                                              |
| Prestazioni Analitica              | Prestazioni ottimizzate per carichi di lavoro in parallelo analitica. Alta velocità e IOPS.                                           | Non è ottimizzato per carichi di lavoro analitica                                                                                                               |
| Limiti di dimensioni                                 | Nessun limiti per le dimensioni di account, le dimensioni del file o il numero di file                                                                   | Limiti specifici documentazione [qui](../azure-subscription-service-limits.md#storage-limits)                                                                                                                     |
| Ridondanza geografico                              | In locale ridondanti (più copie di dati in un'area di Azure)                                                             | In locale ridondanti (LRS), globalmente ridondanti (GRS), accesso in lettura globalmente ridondanti (RA-GRS). Vedere [di seguito](../storage/storage-redundancy.md) per ulteriori informazioni |
| Stato del servizio                               | Anteprima pubblica                                                                                                              | In genere disponibili                                                                                                                                 |
| Disponibilità internazionali  | Vedere [qui](https://azure.microsoft.com/regions/#services)| Vedere [qui](https://azure.microsoft.com/regions/#services) |
| Prezzo                                       |     Vedere [prezzi](https://azure.microsoft.com/pricing/details/data-lake-store/)| Vedere [prezzi](https://azure.microsoft.com/pricing/details/storage/) |

### <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'archivio Lake dati di Azure](data-lake-store-overview.md)
- [Guida introduttiva a Lake archivio](data-lake-store-get-started-portal.md)



