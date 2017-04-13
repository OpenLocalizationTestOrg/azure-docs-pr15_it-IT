<properties
    pageTitle="Risolvere i problemi di backup e ripristino con Database di SQL Azure"
    description="Informazioni su come ripristinare un database cloud da errori e interruzioni con backup e repliche nel Database di SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="restore-a-database-to-a-previous-point-in-time-restore-a-deleted-database-or-recover-from-a-data-center-outage"></a>Ripristinare un database in un punto precedente nel tempo, ripristinare un database eliminato o recuperare da un'interruzione del centro di dati

Database SQL di mantiene repliche del database in modo che è possibile recuperare da interruzioni ed errori dell'utente. Opzioni disponibili dipendono dal livello di servizio di database e opzioni che scelte. Vedere la [Panoramica di continuità aziendale](sql-database-business-continuity.md) per informazioni dettagliate e considerazioni di progettazione.

## <a name="to-restore-a-database-to-a-previous-point-in-time"></a>Per ripristinare un database su un punto precedente nel tempo
1.  Nel [Portale di Azure](https://azure.microsoft.com/), fare clic su **database SQL**.
2.  Selezionare il database nell'elenco e quindi fare clic su **Ripristina**.
3.  Digitare un nuovo nome per il database, scegliere la data e l'ora da ripristinare e quindi fare clic su **Crea.**
4.  Apportare le modifiche di app in base alle esigenze fare riferimento il nuovo database. Vedere [ripristinare un database a una data e ora](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="to-restore-an-accidentally-deleted-database"></a>Per ripristinare un database eliminato per errore
1.  Nel [Portale di Azure](https://azure.microsoft.com/), fare clic su **SQL Server**.
2.  Selezionare il server ospitati database dall'elenco.
3.  Nella e Server, scorrere verso il basso e fare clic su **database eliminati**.
4.  Selezionare il database da ripristinare e quindi fare clic su **Crea**.
5.  Apportare le modifiche di app in base alle esigenze fare riferimento il nuovo database. Vedere [ripristinare un database eliminato](sql-database-recovery-using-backups.md#deleted-database-restore).

## <a name="to-recover-from-a-regional-datacenter-outage"></a>Per ripristinare un'interruzione del data center locale
I database Standard e Premium, se è stato configurato dei secondari replicato geografico, è possibile ripristinare tramite dei secondari. In questo modo la possibilità di ripristinare un database con una meno possibilità di perdita di dati. Per informazioni dettagliate, vedere [ripristinare un database di SQL Azure con backup automatico](sql-database-disaster-recovery.md) .

Azure vengono forniti anche i backup di tutti i database in un paese diverso (una copia di backup ridondanti geografico). È possibile creare un nuovo database da questi backup denominato geografico Ripristina, ma è probabile che si sarà un rallentamento dati se ci si basa su questo metodo solo.

**Per ripristinare un database utilizzando geografico Ripristina:**

- Nel [Portale di Azure](https://azure.microsoft.com/), fare clic su **Nuovo**, fare clic su **dati e lo spazio di archiviazione**, fare clic su **Database SQL**e quindi selezionare **copia di Backup** come origine del database. Per informazioni dettagliate, vedere [ripristinare un database SQL Azure da un'interruzione](sql-database-disaster-recovery.md) .