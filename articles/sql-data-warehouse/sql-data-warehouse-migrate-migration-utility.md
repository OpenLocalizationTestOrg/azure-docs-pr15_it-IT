<properties
   pageTitle="Eseguire la migrazione: Data Warehouse utilità di migrazione | Microsoft Azure"
   description="Eseguire la migrazione nell'archivio di dati SQL."
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
   ms.date="08/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# <a name="data-warehouse-migration-utility-preview"></a>Utilità di migrazione Warehouse dati (Preview)

> [AZURE.SELECTOR]
- [Scaricare l'utilità di migrazione][]

L'utilità di migrazione di dati Warehouse è uno strumento progettato per eseguire la migrazione di schema e i dati di SQL Server e Database di SQL Azure nell'archivio di dati di SQL Azure. Durante la migrazione di schema, lo strumento cartine automaticamente lo schema corrispondente dall'origine alla destinazione. Dopo lo schema è stata eseguita la migrazione, gli strumenti, è possibile spostare i dati con gli script generati automaticamente.

Oltre la migrazione di dati e lo schema, questo strumento offre la possibilità di generare i report di compatibilità che riepilogano incompatibilità tra le varianti di origine e di destinazione che impediscono la migrazione semplificata.

## <a name="get-started"></a>Guida introduttiva
Come prerequisito per l'installazione, è necessario l'utilità della riga di comando BCP per l'esecuzione di script di migrazione e Office per visualizzare il report compatibilità. Quando si avvia l'eseguibile viene scaricato verrà richiesto l'accettazione di un contratto di licenza standard verrà installato lo strumento.

Inoltre, per eseguire la migrazione di Utiliy, sarà necessario quello seguendo le autorizzazioni per il database che si sta cercando di eseguire la migrazione: DATABASE creare, modificare qualsiasi DATABASE o VIEW ANY DEFINITION.

### <a name="launching-the-tool-and-connecting"></a>Avviare lo strumento e la connessione
Avviare lo strumento facendo clic sull'icona del desktop che appare post installazione. All'apertura lo strumento, verrà chiesto di una pagina di connessione iniziale in cui è possibile scegliere l'origine e destinazione per lo strumento di migrazione. In questa fase, SQL Server e Database di SQL Azure è supportato come origini e SQL Data Warehouse come destinazione. Dopo aver selezionato questa verrà chiesto di connettersi al server di origine specificando nome del server e autenticazione e quindi facendo clic su "Connetti".

Dopo l'autenticazione, lo strumento viene visualizzato un elenco di database che sono presenti nel server che si è connessi a. È possibile iniziare la migrazione selezionando un database che si desidera eseguire la migrazione e quindi facendo clic su 'Esegui migrazione selezionata'.

## <a name="migration-report"></a>Report di migrazione
Selezionare 'Verifica compatibilità Database' nello strumento di generare un report di riepilogo tutte le incompatibilità nel database che richiesto per eseguire la migrazione. Un elenco più ampio di alcune delle funzionalità di SQL Server che non sono presenti in SQL Data Warehouse sono disponibili nella [documentazione di migrazione][]. Dopo il report viene generato sarà possibile salvare e aprire il report in Excel.

Si noti che per la generazione lo schema di migrazione, la maggior parte dei problemi identificati come "Oggetto" verrà regolata per consentire la migrazione immediata dei dati. Rivedere le modifiche per assicurarsi che non si desidera apportare modifiche aggiuntive prima di applicare lo schema.

## <a name="migrate-schema"></a>Eseguire la migrazione di schema

Dopo la connessione, selezionare 'Eseguire la migrazione di Schema' verrà generato un script di migrazione dello schema per le tabelle selezionate. Porte questo script la struttura della tabella di dati incompatibile mappe tipi ai moduli più compatibile e create schema e le credenziali di sicurezza, se viene indicato dall'utente in impostazioni di migrazione. Questo codice può essere eseguito rispetto all'istanza di SQL Data Warehouse target salvato in un file, copiato negli Appunti o modificati anche in linea prima di eseguire altre azioni.  

Come in precedenza, quando la migrazione schema esaminare la migrazione delle modifiche apportate che lo strumento ha reso per garantire che è completamente comprendere.  

## <a name="migrate-data"></a>Eseguire la migrazione dei dati

Facendo clic sull'opzione 'Eseguire la migrazione di dati' è possibile generare script BCP che consente di spostare i dati prima di tutto file flat sul server, e quindi direttamente in un Data Warehouse SQL. È consigliabile questo processo per lo spostamento piccole quantità di dati e, come tentativi non sono predefiniti ed errori possono verificarsi se si verifica una perdita della connessione di rete. Per eseguire questo strumento, è necessario avere installato l'utilità della riga di comando BCP e lo schema per i dati già sono stato creato.

Dopo avere immesso i parametri indicati è sufficiente fare clic su Esegui migrazione e verrà generato un set di due pacchetti al percorso specificato. Eseguire il file di esportazione per esportare i dati dell'origine di migrazione in file flat ed eseguire il file di importazione per importare i dati nell'archivio di dati SQL.

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata eseguita la migrazione di alcuni dati, vedere come [sviluppare][].

<!--Image references-->

<!--Article references-->
[documentazione di migrazione]: sql-data-warehouse-overview-migrate.md
[sviluppare]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Scaricare l'utilità di migrazione]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip
