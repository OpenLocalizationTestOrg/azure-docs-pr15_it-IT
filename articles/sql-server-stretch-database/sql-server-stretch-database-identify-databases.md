<properties
    pageTitle="Identificare i database e le tabelle per Database zoom eseguendo zoom Database Advisor | Microsoft Azure"
    description="Informazioni su come identificare i database e le tabelle che sono candidati per il Database di zoom."
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
    ms.topic="article"
    ms.date="06/14/2016"
    ms.author="douglasl"/>

# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Identificare i database e le tabelle per Database zoom eseguendo Advisor Database zoom

Per identificare i database e le tabelle che sono candidati per Database zoom, scaricare Preparazione aggiornamento a SQL Server 2016 ed eseguire Advisor Database zoom. Estensione di Database proporrà identifica anche problemi di blocco.

## <a name="download-and-install-upgrade-advisor"></a>Scaricare e installare l'aggiornamento Advisor
Scaricare e installare l'aggiornamento Advisor da [qui](http://go.microsoft.com/fwlink/?LinkID=613421). Questo strumento non è incluso nel supporto di installazione di SQL Server.

## <a name="run-the-stretch-database-advisor"></a>Eseguire la preparazione di Database esteso

1.  Eseguire l'aggiornamento Advisor.

2.  Selezionare **gli scenari**e quindi selezionare **Esegui zoom DATABASE ADVISOR**.

3.  Scegliere **Seleziona database per analizzare**e **l'Eseguire zoom Database Advisor** .

4.  Immettere e **Selezionare database** il o selezionare il nome del server e le informazioni di autenticazione. Fare clic su **Connetti**.

5.  Viene visualizzato un elenco dei database nel server selezionato. Selezionare i database che si desidera analizzare. Fare clic su **Seleziona**.

6.  Scegliere **Esegui**e **l'Eseguire zoom Database Advisor** .  Viene eseguita l'analisi.

## <a name="review-the-results"></a>Esaminare i risultati

1.  Al termine dell'analisi, su e il **database analizzati** , selezionare uno dei database che l'analisi per visualizzare e il **risultati dell'analisi** .

    E il **risultati dell'analisi** Elenca consigliate tabelle del database selezionato che soddisfano i criteri di recommendation predefinito.

2.  Nell'elenco delle tabelle in e il **risultati dell'analisi** , selezionare una delle tabelle consigliate per la visualizzazione e il **risultati della tabella** .

    Se problemi gravi, e il **risultati tabella** sono elencati i problemi di blocchi per la tabella selezionata. Per informazioni su blocchi problemi rilevati da Advisor Database zoom, vedere [limitazioni per i Database di zoom](sql-server-stretch-database-limitations.md).

3.  Nell'elenco dei problemi di blocco nella e **risultati della tabella** , selezionare uno dei problemi per visualizzare altre informazioni sul problema selezionato e propone attenuazione. Implementare la procedura di attenuazione consigliato se si desidera configurare la tabella selezionata per Database di zoom.

## <a name="next-step"></a>Passaggio successivo
Abilitare il Database esteso.

-   Per abilitare l'estensione Database in un **database**, vedere [Attivare zoom Database per un database](sql-server-stretch-database-enable-database.md).

-   Per attivare zoom Database in un'altra **tabella**, quando zoom è già attivato nel database, vedere [Attivare zoom Database per una tabella](sql-server-stretch-database-enable-table.md).

## <a name="see-also"></a>Vedere anche

[Limitazioni per Database esteso](sql-server-stretch-database-limitations.md)

[Abilitare il Database di zoom per un database](sql-server-stretch-database-enable-database.md)

[Abilitare il Database di zoom per una tabella](sql-server-stretch-database-enable-table.md)

[Tutti gli argomenti per il servizio di Database di zoom Server SQL Azure](sql-server-stretch-database-index-all-articles.md)
