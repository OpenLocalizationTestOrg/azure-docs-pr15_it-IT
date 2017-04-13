<properties 
    pageTitle="Configurare la replica di geografico Active per Database di SQL Azure con PowerShell | Microsoft Azure" 
    description="Configurare la replica di geografico Active per Database di SQL Azure con PowerShell" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
   ms.workload="NA"
    ms.date="07/14/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-powershell"></a>Configurare la replica geografico per Database SQL Azure con PowerShell

> [AZURE.SELECTOR]
- [Panoramica](sql-database-geo-replication-overview.md)
- [Portale di Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

In questo articolo viene illustrato come configurare la replica di geografico Active per Database SQL con PowerShell.

Per avviare failover tramite PowerShell, vedere [avviare caso di errore pianificato o non pianificato per il Database di SQL Azure con PowerShell](sql-database-geo-replication-failover-powershell.md).

>[AZURE.NOTE] Replica geografico attiva (leggibile dei secondari) è ora disponibile per tutti i database in tutti i livelli di servizio. In aprile 2017 ritiro il tipo secondario non leggibile e database non leggibile esistenti verranno aggiornati automaticamente a database secondari leggibili.



Per configurare la replica geografico attivo tramite PowerShell, è necessario le operazioni seguenti:

- Un abbonamento Azure. 
- Un database SQL Azure - il database principale che si desidera replicare.
- Azure PowerShell 1.0 o versione successiva. È possibile scaricare e installare i moduli di Azure PowerShell di esempio [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a name="configure-your-credentials-and-select-your-subscription"></a>Configurare le credenziali e selezionare l'abbonamento

Prima di tutto è necessario stabilire l'accesso al proprio account Azure così avviare PowerShell ed eseguire il cmdlet seguente. Nella schermata di accesso immettere lo stesso messaggio di posta elettronica e password utilizzati per accedere al portale di Azure.


    Login-AzureRmAccount

Dopo aver completato l'accesso si vedrà alcune informazioni sullo schermo che include l'Id è stato effettuato l'accesso con e le sottoscrizioni Azure a che è possibile accedere.


### <a name="select-your-azure-subscription"></a>Selezionare l'abbonamento Azure

Per selezionare l'abbonamento è necessario l'abbonamento ID. È possibile copiare la sottoscrizione Id dalle informazioni visualizzate nel passaggio precedente, o se si hanno più abbonamenti e servono informazioni più dettagliate è possibile eseguire il cmdlet **Get-AzureRmSubscription** e copiare le informazioni desiderate abbonamento set di risultati. Il seguente cmdlet utilizza l'Id di abbonamento per impostare l'abbonamento corrente:

    Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

Una volta eseguita **Selezionare AzureRmSubscription** si torna a di PowerShell prompt.


## <a name="add-secondary-database"></a>Aggiungere database secondario


La procedura seguente crea un nuovo database secondario in una relazione di replica geografico.  
  
Per abilitare secondario è necessario essere il proprietario della sottoscrizione o comproprietario. 

È possibile utilizzare il cmdlet **New-AzureRmSqlDatabaseSecondary** per aggiungere un database secondario in un server partner a un database locale nel server a cui si è connessi (database primario). 

Questo cmdlet sostituisce **AzureSqlDatabaseCopy Start** con il parametro **– IsContinuous** .  Verrà visualizzato un oggetto **AzureRmSqlDatabaseSecondary** che può essere utilizzato da altri cmdlet per identificare chiaramente un collegamento di replica specifico. Questo cmdlet restituirà quando il database secondario viene creato e completamente definito. A seconda delle dimensioni del database può essere necessaria dai minuti a ore.

Il database replicato nel server secondario avrà lo stesso nome del database nel server primario e avrà, per impostazione predefinita, lo stesso livello di servizio. Il database secondario può essere leggibile o non letti e può essere un solo database o un database flessibile. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md)e [AzureRMSqlDatabaseSecondary di nuovo](https://msdn.microsoft.com/library/mt603689.aspx) .
Dopo il secondario creato, definito dati verranno avviata la replica dal database primario per il nuovo database secondario. La procedura seguente viene descritto come eseguire questa operazione con PowerShell per creare database secondari non leggibile e facilitarne la lettura, con un solo database o un database flessibile.

Se il database partner esiste già (ad esempio, in seguito terminare una relazione geografico replica precedente) non viene eseguito il comando.



### <a name="add-a-non-readable-secondary-single-database"></a>Aggiungere un secondario non leggibile (solo database)

Il comando seguente crea secondario non leggibile del database "mydb" del server "srv2" nel gruppo risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### <a name="add-readable-secondary-single-database"></a>Aggiungere secondario leggibile (solo database)

Il comando seguente crea secondario leggibile del database "mydb" del server "srv2" nel gruppo risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### <a name="add-a-non-readable-secondary-elastic-database"></a>Aggiungere un secondario non leggibile (database flessibile)

Il comando seguente crea secondario non leggibile del database "mydb" nel pool di database flessibile denominato "ElasticPool1" del server "srv2" nel gruppo risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### <a name="add-a-readable-secondary-elastic-database"></a>Aggiungere un secondario leggibile (database flessibile)

Il comando seguente crea secondario leggibile del database "mydb" nel pool di database flessibile denominato "ElasticPool1" del server "srv2" nel gruppo risorse "rg2":

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## <a name="remove-secondary-database"></a>Rimuovere database secondario

Utilizzare il cmdlet **Rimuovi AzureRmSqlDatabaseSecondary** terminare definitivamente la relazione di replica tra un database secondario e il relativo principale. Dopo la chiusura di relazione, il database secondario diventa un database di sola lettura. Se la connessione al database secondario è interrotta il comando ha avuto esito positivo ma secondaria diventerà in lettura / scrittura dopo il ripristino di integrazione applicativa. Per ulteriori informazioni, vedere [Livelli di servizio](sql-database-service-tiers.md)e [Rimuovi AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) .

Questo cmdlet consente di sostituire Interrompi AzureSqlDatabaseCopy per la replica. 

La rimozione è equivalente di una chiusura forzata che consente di rimuovere il collegamento di replica di secondario ex come database autonomo che non è completamente replicato prima della chiusura. Tutti i dati di collegamento verranno eliminati ex primario e secondario, del primo se o quando sono disponibili. Questo cmdlet restituirà quando viene rimosso il collegamento di replica. 


Per rimuovere secondario, gli utenti dovrebbero avere accesso scrittura ai database primari e secondari in base alle RBAC. Controllo dell'accesso basato sui ruoli per informazioni dettagliate, vedere.

Le operazioni seguenti per rimuovere il collegamento di replica di database con nome "Miodb" per server "srv2" del gruppo di risorse "rg2". 

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorare l'integrità e la configurazione della replica geografico

Monitoraggio attività includono il monitoraggio dell'integrità della replica di dati e il monitoraggio della configurazione di replica geografico.  

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx) possono utilizzati per recuperare le informazioni relative ai collegamenti di replica inoltrare visibili nella visualizzazione sys.geo_replication_links catalogo.

Il comando seguente recupera stato il collegamento tra il database primario "mydb" e quello secondario nel server "srv2" del gruppo di risorse "rg2".

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulle Active geografico replica, vedere - [Replica di geografico Active](sql-database-geo-replication-overview.md)
- Per una panoramica di continuità aziendale e gli scenari, vedere [Panoramica di continuità aziendale](sql-database-business-continuity.md)

