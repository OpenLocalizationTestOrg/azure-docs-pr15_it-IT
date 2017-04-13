<properties 
    pageTitle="Modificare il livello di livello e le prestazioni del servizio di un database di SQL Azure con PowerShell | Microsoft Azure" 
    description="Modificare il livello di servizio e livello di prestazioni di un database di SQL Azure viene illustrato come ridimensionare il database SQL verso l'alto o verso il basso con PowerShell. Modificare il livello di prezzo di un database di SQL Azure con PowerShell." 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>Modificare il livello e prestazioni livello di servizio (prezzi livello) di un database SQL con PowerShell


> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-scale-up.md)
- [**PowerShell**](sql-database-scale-up-powershell.md)


Livelli di servizio e livelli di prestazioni descrivono le caratteristiche e le risorse disponibili per il database SQL e possono essere aggiornati come le esigenze di apportare modifiche all'applicazione. Per informazioni dettagliate, vedere [Livelli di servizio](sql-database-service-tiers.md).

Si noti che la modifica del livello di servizio e/o il livello di prestazioni di un database crea una replica del database originale al nuovo livello di prestazioni e poi passa connessioni alla replica. Dati non andranno persi durante questo processo ma durante il breve attimo quando si passa la replica sono disabilitate connessioni al database, quindi alcune operazioni in volo possono essere riportati. Questa finestra varia, ma è in Media in 4 secondi e in oltre il 99% dei casi è inferiore a 30 secondi. Molto raramente, soprattutto se sono presenti grandi sono disabilitati i numeri delle transazioni in volo in connessioni momento, questa finestra può contenere più.  

La durata dell'intero processo scalabilità dipende dal livello service e dimensione del database prima e dopo la modifica. Ad esempio un database di 250 GB in fase di modifica, da o all'interno di un livello di servizio Standard, deve completare 6 ore. Per un database delle stesse dimensioni in fase di modifica dei livelli di prestazioni all'interno del livello di servizio Premium, è necessario completare all'interno di 3 ore.


- Per eseguire il downgrade un database, il database deve essere inferiore alle dimensioni massime consentite del livello di servizio di destinazione. 
- Quando si aggiorna un database con [Replica geografico](sql-database-geo-replication-portal.md) abilitato, è necessario aggiornare prima di tutto i database secondari al livello di prestazioni desiderato prima dell'aggiornamento del database primario.
- Quando il downgrade da un livello di servizio Premium, è prima necessario terminare tutte le relazioni di replica geografico. È possibile eseguire i passaggi descritti nell'argomento [recuperare da un'interruzione del servizio](sql-database-disaster-recovery.md) per interrompere il processo di replica tra principale e i database secondari attivi.
- Le offerte di servizi di ripristino sono diverse per i vari livelli di servizio. Se si downgrade si potrebbero perdere la possibilità di ripristinare in un punto nel tempo o hanno un periodo di conservazione backup inferiore. Per ulteriori informazioni, vedere [Backup del Database SQL Azure e ripristino](sql-database-business-continuity.md).
- Le nuove proprietà per il database non vengono applicate finché non sono state completate le modifiche.



**Per completare in questo articolo è necessario quanto segue:**

- Un abbonamento Azure. Se è necessario un abbonamento a Azure, è sufficiente fare clic su **ACCOUNT gratuito** nella parte superiore della pagina e quindi tornare alla fine di questo articolo.
- Un database di SQL Azure. Se non si dispone di un database SQL, creare una seguendo i passaggi descritti in questo articolo: [creare il primo Database di SQL Azure](sql-database-get-started.md).
- Azure PowerShell.


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]



## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>Modificare il livello di livello e le prestazioni di servizio del database SQL

Eseguire [Set-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx) cmdlet e set **-RequestedServiceObjectiveName** a livello di prestazioni del livello prezzo desiderato, ad esempio *S0*, *S1*, *S2*, *S3*, *P1*, *P2*,...

```
$ResourceGroupName = "resourceGroupName"
    
$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

  

   


## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>Script di PowerShell di esempio per modificare il livello del servizio di livello e le prestazioni del database SQL

Sostituire ```{variables}``` con i valori (non includere le parentesi graffe).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"
    
$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"
    
$ServerName = "{server}"
$DatabaseName = "{database}"
    
$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"
    
Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId
    
Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```
        


## <a name="next-steps"></a>Passaggi successivi

- [Ridimensionare e in](sql-database-elastic-scale-get-started.md)
- [Connettere e query in un database SQL di SQL Server Management Studio](sql-database-connect-query-ssms.md)
- [Esportare un database SQL Azure](sql-database-export-powershell.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Panoramica di continuità aziendale](sql-database-business-continuity.md)
- [Documentazione di Database SQL](http://azure.microsoft.com/documentation/services/sql-database/)
- [Cmdlet di Database SQL azure] (http://msdn.microsoft.com/library/azure/mt574084https :/ / msdn.microsoft.com/library/azure/mt619433(v=azure.300\).aspx.aspx)