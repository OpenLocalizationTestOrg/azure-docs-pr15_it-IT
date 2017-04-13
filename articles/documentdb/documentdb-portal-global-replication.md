<properties
    pageTitle="Replica di database globale DocumentDB | Microsoft Azure"
    description="Informazioni su come gestire la replica globale del proprio account DocumentDB tramite il portale Azure."
    services="documentdb"
    keywords="database globale, replica"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Come eseguire la replica di database globale DocumentDB tramite il portale di Azure

Informazioni su come usare il portale di Azure replicare i dati in più aree geografiche per la disponibilità globale di dati di Azure DocumentDB.

Per informazioni sul database come globale replica funziona in DocumentDB, vedere [dati Distribuisci globale con DocumentDB](documentdb-distribute-data-globally.md). Per informazioni sull'esecuzione di replica di database globale a livello di programmazione, vedere [sviluppo di applicazioni con gli account DocumentDB più aree](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribuzione globale dei database DocumentDB è generalmente disponibile e attivata automaticamente per tutti gli account DocumentDB appena creati. Stiamo lavorando per abilitare la distribuzione globale per tutti gli account esistenti, ma nel frattempo, se si desidera distribuzione globale abilitata nel proprio account, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e si verrà attivarla per l'ora.

## <a id="addregion"></a>Aggiungere aree del database globale

DocumentDB è disponibile nella maggior parte delle [aree geografiche Azure] [azureregions]. Dopo aver selezionato il livello di coerenza predefinito per il proprio account di database, è possibile associare una o più aree (a seconda della scelta effettuata delle esigenze di distribuzione globali e livello coerenza predefinito).

1. Nel [portale di Azure](https://portal.azure.com/), in Jumpbar, fare clic su **Account DocumentDB**.
2. In e il **DocumentDB Account** selezionare l'account di database per la modifica.
3. In e il conto, fare clic su **replicare globalmente i dati** dal menu di scelta.
4. In e il **replicare i dati a livello globale** , selezionare le aree geografiche per aggiungere o rimuovere e quindi fare clic su **Salva**. Esiste un costo per l'aggiunta di aree, vedere [prezzi pagina](https://azure.microsoft.com/pricing/details/documentdb/) o l'articolo [dati Distribuisci globalmente con DocumentDB](documentdb-distribute-data-globally.md) per ulteriori informazioni.

    ![Fare clic su aree della mappa per aggiungerli o rimuoverli][1]

### <a name="selecting-global-database-regions"></a>Selezionando le aree del database globale

Quando si configurano due o più aree, è consigliabile che aree siano selezionate in coppie area descritte in base alla [Business continuità e ripristino di emergenza (BCDR): aree accoppiati Azure]  [ bcdr] articolo.

In particolare, durante la configurazione per più paesi, assicurarsi di selezionare lo stesso numero di aree (+ /-1 per pagine pari/dispari) provenienti da ognuna delle colonne area accoppiate. Ad esempio, se si desidera distribuire in quattro aree degli Stati Uniti, selezionare due aree US dalla colonna a sinistra e due da destra. E quindi le operazioni seguenti sarà un insieme appropriato: ovest degli Stati Uniti, Stati Uniti orientali, North Central US e Sud Central US.

Questa guida è importante seguire quando solo due aree sono configurate per ripristino di emergenza. Per più di due aree, seguire questa guida è buona norma, ma non critiche come alcune delle aree selezionate rispettare questa associazione.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Passaggi successivi

Informazioni su come gestire la coerenza del proprio account globalmente replicato leggendo [livelli la coerenza in DocumentDB](documentdb-consistency-levels.md).

Per informazioni sul database come globale replica funziona in DocumentDB, vedere [dati Distribuisci globalmente con DocumentDB](documentdb-distribute-data-globally.md). Per informazioni su replica a livello di programmazione di dati in più aree, vedere [sviluppo di applicazioni con gli account DocumentDB più aree](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/
