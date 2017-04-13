<properties
   pageTitle="Eseguire la migrazione dei dati in SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per la migrazione dei dati nell'archivio di dati di SQL Azure per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="migrate-your-data"></a>Eseguire la migrazione dei dati
In SQL Data Warehouse con gli strumenti di diversi, è possono spostare dati da origini diverse.  Copia alimentatore automatico, SSIS e bcp possono essere utilizzati per tale scopo. Tuttavia, come la quantità di aumento dei dati da considerare suddividere il processo di migrazione di dati nei passaggi. Mette a disposizione i l'opportunità di ottimizzare istruzioni dettagliate per prestazioni e per adattabilità garantire una migrazione di dati smussate.

Prima di tutto, in questo articolo illustra gli scenari di migrazione semplice di copia alimentatore automatico, SSIS e bcp. Quindi approfondire in come è possibile ottimizzare la migrazione.

## <a name="azure-data-factory-adf-copy"></a>Azure copia di dati Factory (alimentatore automatico)
[Copia alimentatore automatico][] fa parte di [Azure Data Factory][]. È possibile usare copia alimentatore automatico per esportare i dati in file flat memorizzati nello spazio di archiviazione locale, ai file flat remoti contenute in archiviazione blob Azure o direttamente nell'archivio di dati di SQL.

Se i dati si avvia in file flat, quindi è necessario innanzitutto trasferire a blob Azure dello spazio di archiviazione prima di avviare un carico in SQL Data Warehouse. Una volta i dati vengono trasferiti in archiviazione blob Azure è possibile usare di nuovo [Alimentatore automatico copia][] per inserire i dati nell'archivio di dati SQL.

PolyBase vengono forniti anche un'opzione di prestazioni elevate per il caricamento dei dati. Tuttavia, che significa tramite due strumenti invece che con uno. Se è necessario ottenere prestazioni ottimali, usare PolyBase. Se si desidera un'esperienza unico strumento (e i dati non sono enorme) alimentatore automatico è la risposta.

> [AZURE.NOTE] PolyBase richiede i file di dati in UTF-8. Si tratta predefinito della copia di alimentatore automatico codifica in modo niente da modificare. Si tratta di promemoria a non modificare il comportamento predefinito della copia di alimentatore automatico.

Visitare su anche l'articolo seguente per alcuni interessanti [esempi alimentatore automatico][].

## <a name="integration-services"></a>Servizi di integrazione ##
Integration Services (SSIS) è uno strumento flessibile ed estrarre trasformazione e caricamento (ETL) che supporta flussi di lavoro complessi, la trasformazione dei dati e le opzioni di caricamento di dati diversi. Utilizzare SSIS per il trasferimento di dati di Azure o come parte di una migrazione più ampia.

> [AZURE.NOTE] SSIS è possibile esportare in UTF-8 senza l'ordine dei byte nel file. Per configurare questa operazione è necessario utilizzare il componente di colonna derivato per convertire i dati di carattere nel flusso di dati da utilizzare la tabella codici 65001 UTF-8. Dopo la conversione delle colonne, scrivere i dati alla scheda destinazione file flat garantire che 65001 è stato selezionato come codici per il file.

SSIS si connette a SQL Data Warehouse così come connette a una distribuzione di SQL Server. Tuttavia, le connessioni saranno necessario utilizzare una gestione connessione ADO.NET. È necessario prestare attenzione per configurare il "utilizzo inserimento di massa quando disponibili" impostazione per ottimizzare la produttività. Vedere l'articolo [ADO.NET adattatore di destinazione][] per altre informazioni su questa proprietà

> [AZURE.NOTE] Connessione a dati di SQL Azure Warehouse tramite OLEDB non è supportata.

Inoltre, è sempre la possibilità che un pacchetto può avere esito negativo dovuto a problemi di rete o la limitazione. Progettazione pacchetti in modo che è possibile riprendere al momento dell'errore, senza ripetizione lavorare completate prima dell'errore.

Per ulteriori informazioni, consultare la [documentazione SSIS][].

## <a name="bcp"></a>bcp
bcp è un'utilità della riga di comando è progettata per file flat importazione / esportazione dati. Alcuni trasformazione può essere eseguita durante l'esportazione di dati. Per eseguire trasformazioni semplice utilizzano una query per selezionare e trasformare i dati. Dopo aver esportato, i file flat possono essere caricati direttamente nella destinazione il database di SQL Data Warehouse.

> [AZURE.NOTE] Può essere utile anche per racchiudere le trasformazioni usate durante l'esportazione dei dati in una visualizzazione nel sistema di origine. In questo modo che la logica viene mantenuta e il processo è ripetibile.

Di seguito sono riportati i vantaggi del bcp:

- Semplicità. comandi bcp sono semplici da creare ed eseguire
- Processo di caricamento nuovamente avviabile. Una volta esportato il carico può essere eseguita qualsiasi numero di volte

Limitazioni del bcp sono:

- bcp funziona con tabulari solo file flat. Non funziona con i file, ad esempio xml o JSON
- bcp non supporta l'esportazione in UTF-8. Ciò potrebbe impedire utilizzando PolyBase bcp esportata dati
- Le funzionalità di trasformazione dati sono limitate a solo la fase di esportazione e sono semplici natura
- bcp non è stato adattato affidabile per durante il caricamento di dati tramite internet. Qualsiasi instabilità di rete potrebbe causare un errore di caricamento.
- bcp si basa sullo schema di essere presenti nel database di destinazione prima il carico

Per ulteriori informazioni, vedere [utilizzare bcp per caricare i dati nell'archivio di dati SQL][].

## <a name="optimizing-data-migration"></a>Ottimizzare la migrazione dei dati
Un processo di migrazione di dati SQLDW può essere suddivise in modo efficace in tre passaggi distinti:

1. Esportazione dei dati di origine
2. Trasferimento di dati di Azure
3. Caricare il database di destinazione SQLDW

È possibile ottimizzare singolarmente istruzioni dettagliate per creare un processo di migrazione affidabile, nuovamente avviabile e flessibile da ottimizzare le prestazioni in ogni fase.

## <a name="optimizing-data-load"></a>Caricamento di ottimizzazione dei dati
Esamina elencate in ordine inverso per qualche minuto; il modo più veloce per caricare i dati consiste nell'utilizzare PolyBase. Ottimizzazione per un processo di caricamento PolyBase inserisce i prerequisiti per i passaggi precedenti consentire è consigliabile esaminare iniziali. Sono:

1. Codifica dei file di dati
2. Formato di file di dati
3. Percorso del file di dati

### <a name="encoding"></a>Codifica
PolyBase richiede file di dati per la codifica UTF-8. Questo errore indica che quando si esportano i dati deve essere conforme a questo requisito. Se i dati contengono solo base caratteri ASCII (ASCII non estesi) quindi questi mappa direttamente lo standard UTF-8 e si non è necessario preoccuparsi la codifica. Tuttavia, i dati contengono caratteri speciali quali umlaut, accenti o simboli o i dati supporti lingue alfabeto latino non sarà necessario verificare che i file di esportazione siano correttamente codifica UTF-8.

> [AZURE.NOTE] bcp non supporta l'esportazione dei dati in UTF-8. Di conseguenza l'opzione ottimale consiste nell'utilizzare servizi di integrazione o copia alimentatore automatico per l'esportazione dei dati. È opportuno sottolineare che il contrassegno dell'ordine di UTF-8 byte (DB) non è necessaria nel file di dati.

Qualsiasi file codificati con UTF-16 dovranno essere mantenuti invariati ***precedente*** per il trasferimento di dati.

### <a name="format-of-data-files"></a>Formato di file di dati
PolyBase impone un terminatore di riga fissa di \n o una nuova riga. I file di dati devono essere conforme a questo standard. Non ci sono restrizioni sui terminatori colonna o stringa.

È necessario definire ogni colonna nel file come parte della tabella esterna in PolyBase. Assicurarsi che tutte le colonne esportate sono necessari e che i tipi di essere conforme allo standard richiesti.

Per fare riferimento al [eseguire la migrazione dello schema] articolo per informazioni su tipi di dati supportati.

### <a name="location-of-data-files"></a>Percorso del file di dati
SQL Data Warehouse utilizza PolyBase per caricare dati dallo spazio di archiviazione Blob Azure in modo esclusivo. Di conseguenza, i dati è necessario innanzitutto trasferiti in archiviazione blob.

## <a name="optimizing-data-transfer"></a>Trasferimento di ottimizzazione dei dati
Una delle parti più lente migrazione dei dati è il trasferimento dei dati di Azure. Non solo la larghezza di banda di rete è possibile un problema ma anche l'affidabilità di rete può compromettere seriamente l'avanzamento. Per impostazione predefinita la migrazione dei dati di Azure le possibilità di errori di trasferimento che si verificano sono adeguatamente probabile è tramite internet. Tuttavia, questi errori possono richiedere dati da inviare nuovamente in tutto o in parte.

Per fortuna sono disponibili diverse opzioni per migliorare la velocità e l'adattabilità di questa procedura:

### <a name="expressroute"></a>[ExpressRoute][]
È consigliabile valutare la possibilità di utilizzo [ExpressRoute][] per accelerare il trasferimento. [ExpressRoute][] consente la connessione privata in Azure in modo che la connessione non va tramite internet. Si tratta non essendo obbligatoria. Tuttavia, miglioreranno velocità quando l'inserimento di dati di Azure da un locale o le strutture di posizione condivisa.

Vantaggi dell'uso delle [ExpressRoute][] sono:

1. Maggiore affidabilità
2. Aumentare la velocità di rete
3. Inferiore latenza di rete
4. maggiore sicurezza della rete

[ExpressRoute][] è utile per un numero di scenari. non solo la migrazione.

Sei interessato? Per ulteriori informazioni e prezzi, visitare la [documentazione ExpressRoute][].

### <a name="azure-import-and-export-service"></a>Azure importazione / esportazione servizio
Azure importazione / esportazione servizio è un processo di trasferimento dati progettato di grandi dimensioni (GB +) per grandi (TB + +) trasferimenti di dati in Azure. Si tratti di scrivere i dati dischi e distribuirle a un centro di dati di Azure. Il contenuto del disco vengono caricato in archiviazione BLOB per proprio conto.

Una panoramica del processo di esportazione importazione è il seguente:

1. Configurare un contenitore di archiviazione Blob Azure per ricevere i dati
2. Esportare i dati in un archivio locale
2. Copiare i dati in unità disco rigido SATA II/III 3,5 lo strumento [Azure Importa/Esporta]
3. Creare un processo di importazione con Azure importazione / esportazione servizio i file del diario prodotti dal [Azure importazione/esportazione strumento]
4. Spedire il nuovo centro di dati di Azure nominati dischi
5. Trasferimento dei dati per il contenitore di archiviazione Blob Azure
6. Caricare i dati in SQLDW con PolyBase

### <a name="azcopy-utility"></a>Utilità di [AZCopy][]
L'utilità di [AZCopy][] è un ottimo strumento per ottenere i dati trasferiti in archiviazione BLOB. È progettato per piccole (MB + +) per trasferimenti di dati di dimensioni molto grandi (GB + +). [AZCopy] è stata progettata anche per fornire buona velocità flessibile il trasferimento di dati di Azure, quindi è la scelta ideale per il passaggio di trasferimento di dati. Una volta trasferiti è possibile caricare i dati utilizzando PolyBase nell'archivio di dati SQL. È anche possibile incorporare AZCopy i pacchetti SSIS tramite un'attività "Esegui processo".

Per utilizzare AZCopy è innanzitutto necessario scaricarlo e installarlo. Esiste una [versione di produzione][] e una [versione di anteprima][] disponibile.

Per caricare un file dal file system è necessario un comando simile a quella riportata di seguito:

```
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt
```

Potrebbe essere un riepilogo di processo di alto livello:

1. Configurare un contenitore di blob Azure dello spazio di archiviazione per ricevere i dati
2. Esportare i dati in un archivio locale
3. AZCopy i dati nel contenitore di archiviazione Blob Azure
4. Caricare i dati in SQL Data Warehouse utilizzando PolyBase

Completa documentazione disponibile: [AZCopy][].

## <a name="optimizing-data-export"></a>Ottimizzazione esportazione dei dati
Oltre a garantire che l'esportazione è conforme ai requisiti disposti dalla PolyBase è anche possibile cercare ottimizzare l'esportazione dei dati per migliorare ulteriormente il processo.

> [AZURE.NOTE] Come PolyBase richiede i dati siano in formato UTF-8 che difficilmente utilizzare bcp per eseguire l'esportazione dei dati. bcp non supporta l'output dei file di dati in UTF-8. SSIS o copia alimentatore automatico sono molto più adatta per eseguire questo tipo di esportazione dei dati.

### <a name="data-compression"></a>Compressione dei dati
PolyBase possibile leggere i dati compressi gzip. Se è possibile comprimere i dati per i file gzip si ridurrà la quantità di dati viene inseriti in rete.

### <a name="multiple-files"></a>Più file
Suddividere le tabelle di grandi dimensioni in più file non solo consente di migliorare la velocità di esportazione, può essere utile anche con re-startability trasferimento e la gestibilità complessiva dei dati di una sola volta sull'archiviazione blob Azure. Molte caratteristiche pile di PolyBase presente che verrà letto tutti i file all'interno di una cartella e viene considerato una tabella. È pertanto consigliabile isolare i file per ogni tabella nella relativa cartella.

PolyBase supporta anche la funzione nota come "scorrimento cartella ricorsiva". È possibile utilizzare questa caratteristica per ottimizzare l'organizzazione dei dati esportati per migliorare la gestione dei dati.

Per ulteriori informazioni sul caricamento di dati con PolyBase, vedere [Utilizzare PolyBase per caricare i dati nell'archivio di dati SQL][].


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla migrazione, vedere [eseguire la migrazione della soluzione nell'archivio di dati SQL][].
Per ulteriori suggerimenti di sviluppo, vedere [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[AZCopy]: ../storage/storage-use-azcopy.md
[Copia alimentatore automatico]: ../data-factory/data-factory-data-movement-activities.md 
[Esempi di alimentatore automatico]: ../data-factory/data-factory-samples.md
[ADF Copy examples]: ../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md
[Eseguire la migrazione della soluzione nell'archivio di dati SQL]: sql-data-warehouse-overview-migrate.md
[SQL Data Warehouse development overview]: sql-data-warehouse-overview-develop.md
[Utilizzare bcp per caricare dati nell'archivio di dati SQL]: sql-data-warehouse-load-with-bcp.md
[Utilizzare PolyBase per caricare dati nell'archivio di dati SQL]: sql-data-warehouse-get-started-load-with-polybase.md


<!--MSDN references-->

<!--Other Web references-->
[Dati di Azure Factory]: http://azure.microsoft.com/services/data-factory/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[Documentazione ExpressRoute]: http://azure.microsoft.com/documentation/services/expressroute/

[versione di produzione]: http://aka.ms/downloadazcopy/
[versione di anteprima]: http://aka.ms/downloadazcopypr/
[Scheda destinazione ADO.NET]: https://msdn.microsoft.com/library/bb934041.aspx
[Documentazione SSIS]: https://msdn.microsoft.com/library/ms141026.aspx
