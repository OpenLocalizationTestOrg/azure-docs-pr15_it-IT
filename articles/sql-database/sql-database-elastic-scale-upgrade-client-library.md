< proprietà
    
    pageTitle="Upgrade to the latest elastic database client library | Microsoft Azure" 
    description="Upgrade apps and libraries using Nuget" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove" />

# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Aggiornare un'app per utilizzare la libreria di client più recente di database flessibile

Sono disponibili tramite l'interfaccia di gestione di NuGetPackage in Visual Studio NuGetand nuove versioni della [libreria client Database flessibile](sql-database-elastic-database-client-library.md) . Aggiornamenti contengono correzioni di bug e supporto tecnico per le nuove funzionalità della libreria client.

**Per la versione più recente:** Passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Rigenerare l'applicazione con la nuova raccolta, nonché modificare i metadati condiviso mappa Manager esistenti archiviati nei database di SQL Azure per supportare nuove funzionalità.

Eseguire le operazioni seguenti nell'ordine garantisce che le versioni precedenti della libreria client non sono più presente nell'ambiente quando vengono aggiornati oggetti metadati, vale a dire che gli oggetti metadati della versione precedente non verranno creati dopo l'aggiornamento.   

## <a name="upgrade-steps"></a>Passaggi per l'aggiornamento

**1. aggiornare le applicazioni.** In Visual Studio, download e l'ultima versione di una raccolta di client fanno riferimento a tutti i progetti di sviluppo che utilizzano la libreria; quindi ricostruire e distribuire. 

 * Nella soluzione Visual Studio, selezionare **Strumenti** --> **Gestione pacchetti NuGet** -->  **Gestisci pacchetti NuGet per soluzione**. 
 * (Visual Studio 2013) Nel riquadro sinistro selezionare **gli aggiornamenti**e quindi selezionare il pulsante **Aggiorna** nel pacchetto **Azure SQL Database flessibile scala libreria Client** che verrà visualizzato nella finestra.
 * (Visual Studio 2015) Casella filtro di **eseguire l'aggiornamento disponibile**. Selezionare il pacchetto da aggiornare e fare clic sul pulsante **Aggiorna** .
    
 
 * Compilare e distribuire. 

**2. aggiornamento degli script.** Se si utilizza gli script di **PowerShell** per gestire shards, [scaricare la nuova versione di libreria](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) e copiare nella directory da cui eseguire gli script. 

**3. eseguire l'aggiornamento del servizio di stampa unione di divisione.** Se si usa lo strumento di unione Dividi database flessibile riorganizzare i dati sharded, [scaricare e installare la versione più recente dello strumento](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). Dettagliate passaggi per l'aggiornamento per il servizio sono disponibili [qui](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. aggiornare i database condiviso mappa Manager**. Aggiornare i metadati che supportano le mappe condiviso nel Database di SQL Azure.  Esistono due modi è possibile eseguire questa operazione, utilizzando PowerShell o c#. Entrambe le opzioni sono illustrate di seguito.

***Opzione 1: Aggiornare i metadati tramite PowerShell***

1. Scaricare l'ultima versione utilità della riga di comando per NuGet da [qui](http://nuget.org/nuget.exe) e salvare in una cartella. 

2. Aprire un prompt dei comandi, passare alla stessa cartella e il comando:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`

3. Passare alla sottocartella contenente la nuova versione DLL client che semplicemente scaricato, ad esempio:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`

4. Scaricare scriptlet di aggiornamento client database flessibile dall' [Interfaccia di Script](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)e salvarlo nella stessa cartella contenente la DLL.

5. Da una cartella, eseguire "PowerShell.\upgrade.ps1" dal prompt dei comandi e seguire le istruzioni visualizzate.
 
***Opzione 2: Aggiornare i metadati utilizzando c#***

In alternativa, creare un'applicazione di Visual Studio che apre il ShardMapManager, scorre shards tutti ed esegue l'aggiornamento dei metadati chiamando i metodi [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) e [UpgradeGlobalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) come in questo esempio: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 
    
    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Queste tecniche per gli aggiornamenti di metadati possono essere applicate più volte senza problemi. Ad esempio, se una versione precedente di client crea inavvertitamente un condiviso dopo l'aggiornamento è stato ancora, è possibile eseguire l'aggiornamento nuovamente tra tutti shards per assicurarsi che la versione più recente di metadati sia presenta in tutta l'infrastruttura. 

**Nota:**  Nuove versioni della libreria client pubblicato alla data continuare a lavorare con versioni precedenti di metadati condiviso mappa Manager nel database di SQL Azure e viceversa.   Tuttavia, per usufruire di alcune delle nuove funzionalità nel client più recenti, metadati devono essere aggiornato.   Si noti che gli aggiornamenti di metadati non avranno effetto dati utente o dati specifici delle applicazioni, solo gli oggetti creati e utilizzati dal Manager mappa condiviso.  E le applicazioni continuano a funzionare attraverso la sequenza di aggiornamento descritta in precedenza. 

## <a name="elastic-database-client-version-history"></a>Cronologia delle versioni client database flessibile 

Per cronologia delle versioni, passare a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]  


<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png
 