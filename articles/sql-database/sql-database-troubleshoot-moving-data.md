<properties
    pageTitle="Spostare i database tra server, tra le sottoscrizioni ed e disconnettersi Azure."
    description="Azioni rapide per copiare, spostare, quindi eseguire la migrazione dei dati e database nel Database di SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Spostare i database tra server, tra le sottoscrizioni ed e uscire da Azure

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]
##<a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Per spostare un database su un server diverso nella stessa sottoscrizione
- Nel [Portale di Azure](https://portal.azure.com), fare clic su **database SQL**, selezionare un database dall'elenco e quindi fare clic su **Copia**. Per ulteriori informazioni, vedere [copiare un database SQL Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Per spostare un database tra abbonamenti
- Nel [Portale di Azure](https://portal.azure.com), fare clic su **SQL Server** e quindi selezionare il server che ospita i database dall'elenco. Fare clic su **Sposta**e quindi selezionare le risorse da spostare e abbonamento per spostare.

## <a name="to-migrate-a-sql-database-into-azure"></a>Eseguire la migrazione di un database SQL in Azure
- Determinare la compatibilità del database e quindi selezionare il metodo di migrazione destra in base alle proprie esigenze. Seguire le istruzioni e opzioni per [la migrazione di un database SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Per creare una copia di un database per l'utilizzo all'esterno di Azure
- [Esportare un file BACPAC.](sql-database-export.md)
