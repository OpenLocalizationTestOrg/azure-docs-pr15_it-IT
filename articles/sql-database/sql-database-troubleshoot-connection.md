<properties
    pageTitle="Database sul server non è attualmente disponibile, connettersi al Database SQL | Microsoft Azure"
    description="Risoluzione dei problemi del database nel server non è attualmente disponibile errore quando un'applicazione si connette al Database SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="database sul server non è attualmente disponibile, connettersi al database sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Errore "Database sul server non è attualmente disponibile" quando ci si connette al database sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando un'applicazione si connette a un database SQL Azure, viene visualizzato il messaggio di errore seguente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Questo messaggio di errore è in genere temporaneo (breve).

Questo errore si verifica quando il database Azure viene spostato (o riconfigurare) e l'applicazione perde la connessione al database SQL. SQL database riconfigurazione eventi si verificano a causa di un evento pianificato (ad esempio, un aggiornamento software) o un evento non pianificato (ad esempio, un arresto anomalo del processo o il bilanciamento del carico). La maggior parte delle riconfigurazione sono in genere breve e devono essere completate in meno di 60 secondi al massimo. Tuttavia, questi eventi in alcuni casi possono impiegare più tempo alla fine, ad esempio quando una transazione di grandi dimensioni provoca un ripristino lunga.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Procedura per risolvere i problemi di connettività instabile
1.  Controllare il [Dashboard del servizio di Microsoft Azure](https://azure.microsoft.com/status) per i problemi noti dei che si sono verificati durante il periodo durante il quale gli errori rilevati dall'applicazione.
2. Le applicazioni di connettersi a un servizio cloud, ad esempio Database SQL Azure devono aspettarsi eventi riconfigurazione periodici e implementare riprovare logica per gestire questi errori anziché come gli errori dell'applicazione con la visualizzazione agli utenti. Esaminare la sezione [errori temporanei](sql-database-connectivity-issues.md) e le procedure consigliate e orientamenti [SQL Database Development Overview](sql-database-develop-overview.md) per ulteriori informazioni e generale Riprova strategie di progettazione. Vedere gli esempi di codice in [Raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md) per informazioni specifiche.
3.  Quando un database si avvicina i limiti delle risorse, può sembrare un problema temporaneo connettività. Vedere [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4.  Se i problemi di connettività continuano, o se la durata per cui l'applicazione rileva l'errore supera i 60 secondi oppure se risulta più occorrenze dell'errore in un determinato giorno, file una richiesta di supporto Azure selezionando **Ottenere supporto** nel sito di [Supporto di Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Passaggi successivi
- Se si riceve un errore diverso, valutare il [messaggio di errore](sql-database-develop-error-messages.md) per indicazioni sulla causa.
- Se il problema non persistente, visitare le indicazioni per la [risoluzione dei problemi comuni](sql-database-troubleshoot-common-connection-issues.md)problemi di connessione al Database SQL Azure.
