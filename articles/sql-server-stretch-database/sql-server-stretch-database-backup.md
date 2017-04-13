<properties
    pageTitle="Eseguire il backup database abilitato zoom | Microsoft Azure"
    description="Informazioni su come eseguire il backup zoom\-abilitato database."
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
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Backup database abilitato zoom

Il backup del database consentono di recuperare uno dei numerosi tipi di errori, errori e guasti.  

-   È necessario eseguire il backup l'estensione\-abilitato database di SQL Server.  

-   Microsoft Azure backup automaticamente i dati remoti zoom Database è eseguita la migrazione da SQL Server in Azure.  

>    [AZURE.NOTE] Copia di backup è solo una parte di una disponibilità completa e soluzione di continuità aziendale. Per ulteriori informazioni sulla disponibilità, vedere [Le soluzioni di disponibilità elevato](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Il backup dei dati di SQL Server  

Per eseguire il backup l'estensione\-abilitato database SQL Server, è possibile continuare a utilizzare i metodi di backup di SQL Server in uso. Per ulteriori informazioni, vedere [eseguire il backup e ripristino del database di SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

Backup di un database SQL Server abilitato zoom contenere solo i dati locali e dati idoneo per la migrazione in corrispondenza del punto nel tempo quando viene eseguito il backup. \(Dati idonei sono dati che non è ancora stati eseguiti, ma verranno migrati in Azure in base alle impostazioni di migrazione delle tabelle.\) Nota come backup **dei riferimenti** e non include i dati già eseguita la migrazione di Azure.  

## <a name="back-up-your-remote-azure-data"></a>Il backup dei dati di Azure remoti   

Microsoft Azure backup automaticamente i dati remoti zoom Database è eseguita la migrazione da SQL Server in Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure consente di ridurre il rischio di perdere dati con backup automatico  
Il servizio di Database di SQL Server zoom in Azure consente di proteggere i database remoti con snapshot di archiviazione automatica almeno ogni 8 ore. Mantiene ogni snapshot per 7 giorni fornire un intervallo di punti di ripristino possibili.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure consente di ridurre il rischio di perdere dati con geografico\-ridondanza  
Il backup del database Azure sono archiviate nel geografico\-archiviazione Azure ridondanti (RA\-GRS) e sono pertanto geografico\-ridondanti per impostazione predefinita. Geografico\-lo spazio di archiviazione ridondante replica i dati in un'area secondaria centinaia di miglia fuori area principale. In aree principali e secondarie, i dati viene replicati ogni tre volte, in domini guasto separata e aggiornamento di domini. In questo modo che i dati sono permanenti anche in caso di un'interruzione di internazionali completa o un danno che esegue il rendering di una delle aree Azure non è disponibile.

### <a name="stretchRPO"></a>Database estensione riduce il rischio di perdita di dati per i dati di Azure mantenendo temporaneamente righe migrate
Dopo l'estensione Database esegue la migrazione delle righe idonee da SQL Server in Azure, mantiene le righe nella tabella di gestione temporanea per un numero minimo di 8 ore. Se si consentono di ripristinare una copia di backup del database Azure, Database Zoom utilizza le righe salvate nella tabella di gestione temporanea di riconciliare SQL Server e database Azure.

Dopo avere ripristinato una copia di backup dei dati di Azure, è necessario eseguire la stored procedure [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) per riconnettersi l'estensione\-database di SQL Server per il database remoto Azure abilitato. Quando si esegue **sys.sp_rda_reauthorize_db**, Database zoom Riconcilia automaticamente i database Azure e SQL Server.

Per aumentare il numero di ore di dati migrati zoom Database mantiene temporaneamente nella tabella di gestione temporanea, eseguire la stored procedure [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) e specificare un numero di ore maggiori di 8. Per decidere la quantità di dati per la conservazione, considerare i fattori seguenti:
-   Frequenza di backup automatici di Azure (almeno ogni 8 ore).
-   Il tempo necessario dopo un problema per individuare i problemi e per decidere di ripristinare una copia di backup.
-   La durata dell'operazione di ripristino Azure.

> [AZURE.NOTE] Aumentare la quantità di dati estesa Database mantiene temporaneamente nella tabella di gestione temporanea aumenta la quantità di spazio in SQL Server.

Per verificare se il numero di ore di dati estesa Database mantiene attualmente temporaneamente nella tabella di gestione temporanea, eseguire la stored procedure [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Vedere anche

[Gestire e risolvere i problemi di Database di zoom](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Eseguire il backup e ripristino del database di SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
