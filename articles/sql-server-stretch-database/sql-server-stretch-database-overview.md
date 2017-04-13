<properties
    pageTitle="Allontanare Panoramica Database | Microsoft Azure"
    description="Informazioni su come Database zoom esegue la migrazione dei dati freddo trasparente e sicuro nel cloud di Microsoft Azure."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/27/2016"
    ms.author="douglasl"/>

# <a name="stretch-database-overview"></a>Allontanare Panoramica di Database

Estensione Database esegue la migrazione dei dati freddo trasparente e sicuro nel cloud di Microsoft Azure.

Se si desidera iniziare subito con estensione Database, vedere [Introduzione eseguendo il Database attivare zoom guidata](sql-server-stretch-database-wizard.md).

## <a name="what-are-the-benefits-of-stretch-database"></a>Quali sono i vantaggi del Database di zoom?
Estensione Database offre i seguenti vantaggi:

### <a name="provides-cost-effective-availability-for-cold-data"></a>Fornisce costo\-efficace della disponibilità per i dati freddo
Allontanare caldi e freddo transazioni dati in modo dinamico da SQL Server a Microsoft Azure con Database di SQL Server zoom. A differenza di archiviazione dati fredda in genere, i dati siano sempre online e disponibile per query. È possibile fornire più sequenze temporali di conservazione dei dati senza interrompere banca per le tabelle di grandi dimensioni come cronologia degli ordini cliente. Vantaggi offerti dal costo di Azure anziché il ridimensionamento costosa su\-locali dello spazio di archiviazione. Si sceglie il livello dei prezzi e configurare le impostazioni nel portale di Azure per mantenere il controllo dei costi. Scalare verso l'alto o verso il basso in base alle esigenze. Visitare [Il prezzo di Database di SQL Server zoom](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/) pagina per informazioni dettagliate.

### <a name="doesnt-require-changes-to-queries-or-applications"></a>Non richiede modifiche alle query o applicazioni
Accedere ai dati di SQL Server diretta indipendentemente sulla\-locali o viene estesa nel cloud.  Impostare il criterio che determina quale i dati vengono archiviati e SQL Server gestisce lo spostamento dei dati in background. L'intera tabella è sempre online e query. E, Database di zoom non richiede modifiche apportate alle query esistenti o applicazioni, la posizione dei dati è completamente trasparente all'applicazione.

### <a name="streamlines-on-premises-data-maintenance"></a>Consente di ottimizzare in\-locali la gestione dei dati
Ridurre in\-locali manutenzione e lo spazio di archiviazione per i dati. Backup per la su\-locale dati eseguito più velocemente e di fine all'interno della finestra di manutenzione. L'esecuzione di backup per la parte cloud i dati vengono eseguite automaticamente. Il su\-esigenze di archiviazione locale sono ridotte. Spazio di archiviazione Azure può essere 80% meno costosa rispetto all'aggiunta sulla\-locali SSD.

### <a name="keeps-your-data-secure-even-during-migration"></a>I dati rimangono sicura anche durante la migrazione
Sfrutteranno tranquillità durante il ridimensionamento delle applicazioni più importanti in modo sicuro nel cloud. Sempre crittografate SQL Server consente la crittografia per i dati di animazione. Riga livello di sicurezza (RLS) e altre funzionalità di sicurezza avanzate di SQL Server funziona anche con Database di zoom per proteggere i dati.

## <a name="what-does-stretch-database-do"></a>Cosa fare zoom Database?
Dopo aver attivato il Database di zoom per un'istanza di SQL Server, database e almeno una tabella, Database zoom silenziosamente inizia a eseguire la migrazione dei dati freddo in Azure.

-   Se si archiviano freddo dati in un'altra tabella, è possibile eseguire la migrazione l'intera tabella.

-   Se la tabella contiene dati caldo e freddo, è possibile specificare una funzione di filtro per selezionare le righe per eseguire la migrazione.

**Non è necessario modificare le query esistenti e le applicazioni client.** Hai ancora accedere facilmente a dati locali e remoti, anche durante la migrazione dei dati. Esiste una piccola quantità di latenza per le query remote, ma si verifica solo questa latenza quando si esegue una query di dati freddo.

**Che zoom Database garantisce che nessun dati andranno persi** in caso di errore durante la migrazione. Dispone anche di ritentare per gestire i problemi di connessione che possono verificarsi durante la migrazione. Una visualizzazione di gestione dinamica fornisce lo stato della migrazione.

**È possibile mettere in pausa la migrazione dei dati** per la risoluzione dei problemi nel server locale o per ottimizzare la larghezza di banda di rete disponibili.

![Allontanare Panoramica di database][StretchOverviewImage1]

## <a name="is-stretch-database-for-you"></a>È estesa Database dell'utente?
Se è possibile apportare le seguenti istruzioni, zoom Database può aiutare a soddisfare i requisiti e risolvere i problemi.

|Se si è un decisionale|Se si è un amministratore di database|
|------------------------------|-------------------|
|È necessario mantenere i dati delle transazioni una lunga esperienza.|Le dimensioni delle tabelle riceveranno fuori controllo.|
|A volte è necessario eseguire query sui dati freddo.|Gli utenti si supponga che desiderano l'accesso ai dati freddo, ma solo raramente usino.|
|Si dispone di App, incluse le applicazioni meno recenti, che non desidero aggiornare.|È necessario mantenere l'acquisto e aggiungere altro spazio di archiviazione.|
|Come trovare un modo per risparmiare sullo spazio di archiviazione?|Non è possibile eseguire il backup o ripristino di tali tabelle di grandi dimensioni all'interno il contratto di servizio.|

## <a name="what-kind-of-databases-and-tables-are-candidates-for-stretch-database"></a>Quali tipi di database e le tabelle sono candidati per Database zoom?
Estendere i siti di destinazione transazione di database con grandi quantità di dati fredda, in genere memorizzati in un numero limitato di tabelle. Le tabelle possono contenere più di un miliardi di righe.

Se si utilizza la caratteristica temporale tabella di SQL Server 2016, usare il Database di zoom per eseguire la migrazione interamente o parzialmente tabella della cronologia associati ai costi\-efficace dello spazio di archiviazione di Azure. Per ulteriori informazioni, vedere [Gestire criteri di conservazione dei dati cronologici in tabelle temporali versione del sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Utilizzare zoom Database Advisor, una caratteristica di SQL Server 2016 Preparazione aggiornamento, per identificare i database e le tabelle per Database di zoom. Per ulteriori informazioni, vedere [identificare database e le tabelle per Database di zoom](sql-server-stretch-database-identify-databases.md). Per ulteriori informazioni sui potenziali problemi di blocco, vedere [limitazioni per i Database di zoom](sql-server-stretch-database-limitations.md).

## <a name="test-drive-stretch-database"></a>Prova Database zoom
**Prova Database zoom con database di esempio AdventureWorks.** Per ottenere il database di esempio AdventureWorks, scaricare almeno il file di database e il file di script ed esempi da [qui](https://www.microsoft.com/download/details.aspx?id=49502). Dopo avere ripristinato il database di esempio in un'istanza di SQL Server 2016, decomprimere il file esempi e aprire il file esempi DB zoom dalla cartella DB zoom. Eseguire gli script in questo file per verificare lo spazio utilizzato per i dati prima e dopo l'abilitazione zoom Database, per verificare l'avanzamento della migrazione dei dati e per verificare che è possibile continuare a eseguire query sui dati esistenti e inserire nuovi dati durante e dopo la migrazione dei dati.

## <a name="next-step"></a>Passaggio successivo
**Identificare i database e le tabelle che sono candidati per il Database di zoom.** Scaricare Preparazione aggiornamento a SQL Server 2016 ed eseguire zoom Database Advisor per identificare i database e le tabelle che sono candidati per il Database di zoom. Estensione di Database proporrà identifica anche problemi di blocco. Per ulteriori informazioni, vedere [identificare database e le tabelle per Database di zoom](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png
