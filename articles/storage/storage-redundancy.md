<properties
    pageTitle="Replica di spazio di archiviazione Azure | Microsoft Azure"
    description="Dati nel proprio account di archiviazione di Microsoft Azure viene replicati per la durata e disponibilità. Opzioni di replica includono lo spazio di archiviazione in locale ridondante (LRS), lo spazio di archiviazione zona ridondanti (ZRS), lo spazio di archiviazione ridondanti geografico (GRS) e accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="tamram"/>

# <a name="azure-storage-replication"></a>Azure replica di spazio di archiviazione

I dati nel proprio account di archiviazione di Microsoft Azure viene sempre replicati per garantire la durata e disponibilità. La replica copia i dati all'interno dello stesso data center, o in un secondo data center, in base all'opzione replica scelto. La replica consente di proteggere i dati e mantiene l'applicazione i tempi in caso di errori hardware temporaneo. Se i dati sono replicati in un secondo data center, che anche consente di proteggere i dati da un errore irreversibile nella posizione principale.

La replica garantisce che l'account di archiviazione soddisfi il [Livello di servizio contratto (contratto di servizio) per l'archiviazione](https://azure.microsoft.com/support/legal/sla/storage/) anche in caso di errori. Vedere che il contratto di servizio per informazioni sull'archiviazione di Azure garanzie per la durata e la disponibilità. 

Quando si crea un account di archiviazione, è possibile selezionare una delle seguenti opzioni di replica:  

- [Spazio di archiviazione in locale ridondante (LRS)](#locally-redundant-storage)
- [Spazio di archiviazione zona ridondanti (ZRS)](#zone-redundant-storage)
- [Spazio di archiviazione ridondanti geografico (GRS)](#geo-redundant-storage)
- [Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)](#read-access-geo-redundant-storage)

Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS) è l'opzione predefinita quando si crea un nuovo account di archiviazione.

Nella tabella seguente fornisce una breve panoramica delle differenze tra LRS, ZRS, GRS e RA GRS, mentre nelle sezioni successive indirizzo ogni tipo di replica in modo più dettagliato.

| Strategia di replica                                                               | LRS | ZRS | GRS | GRS RA |
|:----------------------------------------------------------------------------------|:---|:---|:---|:------|
| Dati vengono replicati in più Data Center.                                     | No  | Sì | Sì | Sì    |
| Leggere i dati dalla posizione secondaria oltre che da posizione principale. | No  | No  | No  | Sì    |
| Numero di copie dei dati gestiti in nodi separati.                             | 3   | 3   | 6   | 6      |

Per informazioni sui prezzi delle opzioni di ridondanza diverso, vedere [Prezzi lo spazio di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

>[AZURE.NOTE] Spazio di archiviazione Premium supporta l'archiviazione solo localmente ridondanti (LRS). Per informazioni sull'archiviazione Premium, vedere [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](storage-premium-storage.md).

## <a name="locally-redundant-storage"></a>Spazio di archiviazione in locale ridondante

Spazio di archiviazione in locale ridondante (LRS) replica i dati tre volte all'interno di un'unità in scala dello spazio di archiviazione che è ospitato in un Data Center nell'area in cui è stato creato l'account di archiviazione. Una richiesta di scrittura restituisce correttamente solo dopo che sono stati scritti per ogni tre replica. Questi tre ogni repliche, in domini di guasto separata e l'aggiornamento all'interno di unità in scala uno spazio di archiviazione. 

Un'unità in scala dello spazio di archiviazione è un insieme di rack di nodi di archiviazione. Un dominio (FD) è un gruppo di nodi che rappresentano un'unità fisica di errore e possono essere considerati nodi appartenenti allo stesso rack fisico. Un aggiornamento del dominio (UD) è un gruppo di nodi aggiornabili durante il processo di un aggiornamento del servizio (implementazione). Le tre repliche sono distribuite in UDs e FDs all'interno di un'unità di scala di archiviazione per assicurarsi che i dati siano disponibili anche se errore hardware influisce su un singolo rack o quando si aggiornano i nodi durante una distribuzione. 

LRS è il costo più bassi e offre durata almeno rispetto alle altre opzioni. In caso di emergenza livello Data Center (fire, saturazione e così via) ogni tre replica potrebbe essere persi o irreversibile. Per ridurre il rischio geografico ridondanti dello spazio di archiviazione (GRS) è consigliato per la maggior parte delle applicazioni.

Lo spazio di archiviazione in locale ridondante essere comunque utile in alcuni scenari: 

- Fornisce più alto al massimo la larghezza di banda delle opzioni di replica di archiviazione Azure.

- Se l'applicazione archivia dati che possono essere facilmente ricostruzione, è possibile scegliere per LRS.

- Alcune applicazioni sono limitati ai dati solo all'interno di un paese a causa di requisiti di governance dei dati. Un'area accoppiata potrebbe essere in un altro paese; Per informazioni su coppie di area geografica, vedere [aree Azure](https://azure.microsoft.com/regions/) .

## <a name="zone-redundant-storage"></a>Spazio di archiviazione zona ridondanti

Spazio di archiviazione zona ridondanti (ZRS) replica i dati in modo asincrono tra i Data Center all'interno di una o due aree oltre l'archiviazione tre repliche simile a LRS, in modo da fornire durata superiore a LRS. Dati archiviati in ZRS sono permanenti anche se il data center principale è disponibile o irreversibile.
I clienti che prevede di utilizzare ZRS devono essere presente che: 

- ZRS è disponibile solo per BLOB blocco nell'account di archiviazione generale ed è supportato solo nelle versioni di servizio di archiviazione 2014-02-14 e versioni successive. 

- Poiché la replica asincrona implica un ritardo, in caso di emergenza locale è possibile che le modifiche che non ancora replicate nel server secondario andranno perse se i dati non possono essere recuperati dal server primario.

- La replica potrebbe non essere disponibile fino a Microsoft avvia il controllo a quello secondario.

- Account ZRS non può essere convertito in un secondo momento LRS o GRS. Analogamente, un account esistente LRS o GRS non può essere convertito a un account ZRS.

- Per gli account ZRS non sono metriche o la funzionalità di registrazione. 

## <a name="geo-redundant-storage"></a>Spazio di archiviazione geografico ridondanti

Spazio di archiviazione ridondanti geografico (GRS) replica i dati in un'area secondaria centinaia di miglia fuori area principale. Se l'account di archiviazione ha GRS abilitata, i dati sono permanenti anche in caso di un'interruzione di internazionali completa o un danno in cui non è reversibile area principale.

Per un account di archiviazione con GRS abilitato, un aggiornamento prima di tutto si impegna a dell'area principale, in cui viene replicato tre volte. Quindi l'aggiornamento viene replicato in modo asincrono all'area secondario, in cui viene anche replicato tre volte. 

Con GRS aree principali e secondarie gestire repliche domini separati guasto ed eseguire l'aggiornamento di domini all'interno di un'unità in scala dello spazio di archiviazione come descritto con LRS.

Considerazioni:

- Poiché la replica asincrona implica un ritardo, in caso di emergenza internazionali è possibile che le modifiche che non ancora replicate all'area secondario andranno perse se i dati non possono essere recuperati dall'area principale.

- La replica non è disponibile solo se Microsoft avvia il controllo a area secondario.

- Se un'applicazione desidera leggere dall'area di secondario l'utente deve abilitare RA GRS. 

Quando si crea un account di archiviazione, selezionare l'area principale per l'account. L'area secondario viene determinato in base all'area principale e non può essere modificato. Nella tabella seguente mostra le associazioni area primario e secondario.

| Principale             | Secondario           |
|---------------------|---------------------|
| America del Nord centrale USA    | Sud centrale USA    |
| Sud centrale USA    | America del Nord centrale USA    |
| Stati Uniti orientali             | Usa ovest             |
| Usa ovest             | Stati Uniti orientali             |
| Stati Uniti orientali 2           | Centrale USA          |
| Centrale USA          | Stati Uniti orientali 2           |
| Europa Nord America        | Europa occidentale         |
| Europa occidentale         | Europa Nord America        |
| Asia sudorientale     | Asia orientale           |
| Asia orientale           | Asia sudorientale     |
| Cina orientale          | Cina Nord America         |
| Cina Nord America         | Cina orientale          |
| Giappone est          | Giappone ovest          |
| Giappone ovest          | Giappone est          |
| Brasile sud        | Sud centrale USA    |
| Australia orientale      | Australia sudorientale |
| Australia sudorientale | Australia orientale      |
| Sud India         | India centrale       |
| India centrale       | Sud India         |
| Stati Uniti Gov Iowa         | Stati Uniti Gov Virginia     |
| Stati Uniti Gov Virginia     | Stati Uniti Gov Iowa         |
| Canada centrale      | Canada orientale          |
| Canada orientale         | Canada centrale      |
| Regno Unito ovest             | Sud Regno Unito            |
| Sud Regno Unito            | Regno Unito ovest             |
| Germania centrale     | Germania nord-est   |
| Germania nord-est   | Germania centrale     |
| Usa ovest 2           | Centrale USA ovest     |
| Centrale USA ovest     | Usa ovest 2           |

Per informazioni aggiornate sui supportati da Azure, vedere [Le aree di Azure](https://azure.microsoft.com/regions/).
 
## <a name="read-access-geo-redundant-storage"></a>Spazio di archiviazione ridondanti geografico di accesso in lettura

Accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS) ingrandisce disponibilità per il proprio account di archiviazione, fornendo accesso in sola lettura per i dati nel percorso secondario, oltre a replica tra due aree forniti da GRS. 

Quando si Abilita accesso in sola lettura per i dati presenti nell'area secondario, sono disponibili in un endpoint secondario, oltre all'endpoint principale per l'account di archiviazione dei dati. L'endpoint secondario è simile all'endpoint principale, ma aggiunge il suffisso `–secondary` al nome dell'account. Ad esempio, se l'endpoint primario per il servizio Blob `myaccount.blob.core.windows.net`, quindi l'endpoint secondario `myaccount-secondary.blob.core.windows.net`. I tasti di scelta per l'account di archiviazione sono gli stessi per entrambi gli endpoint primari e secondari.

Considerazioni:

- Per gestire quale endpoint interagisce con quando si usa RA GRS l'applicazione. 

- RA GRS è destinato ai fini della disponibilità. Per indicazioni scalabilità, esaminare l' [elenco di controllo prestazioni](storage-performance-checklist.md).

## <a name="next-steps"></a>Passaggi successivi

- [Spazio di archiviazione Azure prezzi](https://azure.microsoft.com/pricing/details/storage/)
- [Informazioni sugli account di archiviazione Azure](storage-create-storage-account.md)
- [Prestazioni e scalabilità archiviazione Azure](storage-scalability-targets.md)
- [Opzioni di ridondanza di archiviazione Microsoft Azure e accesso in lettura geografico ridondante dello spazio di archiviazione](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)  
- [Carta SOSP - archiviazione Azure: Un disponibilità servizio di archiviazione Cloud con coerenza sicuro](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)  
