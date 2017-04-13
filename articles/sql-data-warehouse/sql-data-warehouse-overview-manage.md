<properties
   pageTitle="Gestire i database SQL di Azure Data warehouse | Microsoft Azure"
   description="Panoramica della gestione dei database di SQL Data Warehouse. Include gli strumenti di gestione DWUs e prestazioni ottimali scalabilità, risoluzione dei problemi relativi alle prestazioni delle query, la definizione di criteri di sicurezza efficace e si ripristina un database da danneggiamento dei dati o da un'interruzione internazionali."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="barbkess;sonyama;"/>

# <a name="manage-databases-in-azure-sql-data-warehouse"></a>Gestire i database in Data Warehouse di SQL Azure

SQL Data Warehouse consente di automatizzare molti aspetti della gestione dei database. Ad esempio, per ridimensionare le prestazioni è sufficiente regolare e pagare il livello ottimale delle risorse di elaborazione e quindi SQL Data Warehouse eseguire automaticamente tutte le operazioni di scalabilità e proporzioni dei caratteri indietro. 

Senza dubbio desiderata monitorare il carico di lavoro per identificare le proprie esigenze prestazioni come risolvere i problemi di query con esecuzione prolungata. Sarà anche necessario eseguire alcune attività di sicurezza per gestire le autorizzazioni per utenti e gli account di accesso.

Questa sezione copre questi aspetti della gestione SQL Data Warehouse.

- Strumenti di gestione
- Elaborazione di scala
- Sospendere e riprendere
- Prestazioni ottimali
- Monitoraggio delle query
- Sicurezza
- Eseguire il backup e ripristino

## <a name="management-tools"></a>Strumenti di gestione

È possibile utilizzare una varietà di strumenti per gestire i database SQL Data warehouse. Come si gestiscono i database, verrà sviluppato Preferenze strumento per ogni tipo di attività da eseguire.

### <a name="azure-portal"></a>Portale di Azure
Il [portale di Azure][] è un portale basato sul web in cui è possibile creare, aggiornare ed eliminare database e monitorare le risorse del database. Questo strumento è ideale se si ha ancora esperienza nel con Azure, la gestione di un numero limitato di database di data warehouse o necessario eseguire rapidamente.

Per iniziare a utilizzare il portale di Azure, vedere [creare un Data Warehouse SQL (portal Azure)][].

### <a name="sql-server-data-tools-in-visual-studio"></a>SQL Server Data Tools in Visual Studio
[SQL Server Data Tools][] (SSDT) in Visual Studio consente di connettersi, gestire e sviluppare i database. Se si è uno sviluppatore di applicazioni familiarità con Visual Studio o in altri ambienti di sviluppo integrato (IDE), provare a utilizzare SSDT in Visual Studio.

SSDT include Esplora di oggetto SQL Server che consente di visualizzare, connettersi ed eseguire gli script nei database di SQL Data Warehouse. Per connettere rapidamente SQL Data Warehouse, è possibile è sufficiente fare clic sul pulsante **Apri in Visual Studio** nella barra dei comandi quando si visualizzano i dettagli di database nel portale classica di Azure.  

Per iniziare a utilizzare SSDT in Visual Studio, vedere [Query Azure SQL Data Warehouse con Visual Studio][].

### <a name="command-line-tools"></a>Strumenti della riga di comando
Gli strumenti della riga di comando sono ideali per automatizzare i carichi di lavoro.  PowerShell e sqlcmd in due modi interessanti per automatizzare i processi.  È consigliabile questi strumenti per la gestione di un numero elevato di server logici e distribuire le modifiche delle risorse in un ambiente di produzione, come le attività che possono essere inserite e quindi automatico.

### <a name="dynamic-management-views"></a>Viste a gestione dinamica 

Viste sono la consegna e Pane della gestione SQL Data Warehouse. Quasi tutte le informazioni che replica nel portale si basa su viste. Per visualizzare un elenco delle viste Warehouse di dati SQL, vedere [visualizzazioni di sistema SQL Data Warehouse][].

Per iniziare, vedere [connettersi e query con sqlcmd][]e [creare un database (PowerShell)][].

## <a name="scale-compute"></a>Elaborazione di scala

In SQL Data Warehouse, è possibile ridimensionare rapidamente le prestazioni out o indietro di aumentare o diminuire le risorse di calcolo della larghezza di banda i/o CPU e memoria. Per ridimensionare le prestazioni, è sufficiente è regolare il numero di unità di magazzino dati (DWUs) allocata SQL Data Warehouse al database. SQL Data Warehouse rapidamente apporta la modifica e gestisce tutte le modifiche sottostante al software o hardware.

Per ulteriori informazioni sul ridimensionamento DWUs, vedere [prestazioni scala][].

##  <a name="pause-and-resume"></a>Sospendere e riprendere

Per salvare i costi, è possibile interrompere e riprendere risorse elaborazione su richiesta. Ad esempio, se non è possibile utilizzare il database durante la notte e nei fine settimana, è possibile sospendere durante le ore e riprendere durante la giornata. Non addebitati DWUs mentre il database è sospeso.

Per ulteriori informazioni, vedere [sospendere il calcolo][]e [calcolare curriculum][].

## <a name="performance-best-practices"></a>Prestazioni ottimali

Quando iniziare una nuova tecnologia, scoprono suggerimenti che funzionano migliore destra dall'inizio consente di risparmiare molto tempo.  Sono disponibili procedure consigliate in molti degli nostro argomenti.

Per molti un riepilogo delle considerazioni più importanti per lo sviluppo il carico di lavoro, vedere [Procedure consigliate Warehouse dati SQL][].

## <a name="query-monitoring"></a>Monitoraggio delle query

In alcuni casi esecuzione di una query troppo lunga, ma non si è certi di quale è la causa del problema. SQL Data Warehouse sono gestione dinamica viste che è possibile utilizzare per determinare quali query sta impiegando troppo tempo. 

Per trovare query con esecuzione prolungata, vedere [monitorare il carico di lavoro utilizzando viste][].

## <a name="security"></a>Sicurezza

Per mantenere un sistema protetto, è necessario essere sull'avviso ed evitare qualsiasi tipo di accesso non autorizzato. Per assicurarsi che le regole del firewall sono attive in modo che solo autorizzati gli indirizzi IP è possono connettere un sistema di protezione. È necessario che l'autenticazione corretta delle credenziali utente. Dopo che un utente è connesso al database, l'utente deve contenere solo le autorizzazioni per eseguire un numero minimo di azioni. Per proteggere i dati, è possibile utilizzare la crittografia. È anche importante avere il controllo e verifica in modo che è possibile rivedere gli eventi in qualsiasi attività sospetto.

Per informazioni sulla gestione della protezione, testa sopra per [informazioni generali sulla protezione][].

## <a name="backup-and-restore"></a>Eseguire il backup e ripristino

Backps affidabile dei dati è essenziale per ogni database di produzione. SQL Data Warehouse consente di mantenere i dati attendibili eseguendo il backup dei database attiva automaticamente a intervalli regolari. Questi backup consentono di recuperare da scenari in cui è stato danneggiato i dati o eliminato accidentalmente i dati o database.  Per la pianificazione di backup dei dati, criteri di conservazione e su come ripristinare un database, vedere [ripristinare da snapshot][].

## <a name="next-steps"></a>Passaggi successivi
Utilizzo di buona progettazione del database principi renderà più semplice da gestire i database di SQL Data Warehouse. Per informazioni, testa sopra alla pagina [Panoramica di sviluppo][].

<!--Image references-->

<!--Article references-->
[Creare un Data Warehouse SQL (Portal Azure)]: sql-data-warehouse-get-started-provision.md
[Creare un database (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Query SQL Azure Data Warehouse con Visual Studio]: sql-data-warehouse-query-visual-studio.md
[Connettersi ed eseguire query con sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Panoramica di sviluppo]: sql-data-warehouse-overview-develop.md
[Monitorare il carico di lavoro utilizzando viste]: sql-data-warehouse-manage-monitor.md
[Calcolo pausa]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Ripristinare da snapshot]: sql-data-warehouse-restore-database-overview.md
[Calcolo curriculum]: sql-data-warehouse-manage-compute-overview.md#resume-compute-performance-bk
[Prestazioni di scala]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Cenni preliminari sulla sicurezza]: sql-data-warehouse-overview-manage-security.md
[Procedure consigliate Warehouse dati SQL]: sql-data-warehouse-best-practices.md
[Visualizzazioni di sistema SQL Data Warehouse]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portale di Azure]: http://portal.azure.com/
