<properties 
    pageTitle="Incrementare account DocumentDB S1 | Microsoft Azure" 
    description="Vantaggi offerti aumentare la velocità effettiva nell'account DocumentDB S1 apportando alcune semplici modifiche nel portale di Azure." 
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/25/2016" 
    ms.author="mimig"/>

# <a name="supercharge-your-documentdb-account"></a>Incrementare account DocumentDB

Seguire questa procedura per usufruire di aumentare la velocità effettiva per il proprio account Azure DocumentDB S1. Con il minimo senza costi aggiuntivi, è possibile aumentare la velocità di S1 account esistente da 250 [RU/s](documentdb-request-units.md) a 400 RU/s o più!  

> [AZURE.VIDEO changedocumentdbcollectionperformance]

## <a name="change-to-user-defined-performance-in-the-azure-portal"></a>Modificare le prestazioni definite dall'utente nel portale di Azure

1. Nel browser passare al [**portale di Azure**](https://portal.azure.com). 
2. Fare clic su **Sfoglia** -> **DocumentDB (NoSQL)**, quindi selezionare l'account DocumentDB da modificare.   
3. Nell'obiettivo di **database** , selezionare il database da modificare e quindi selezionare la raccolta con il livello di prezzi S1 e il **Database** .

      ![Schermata della stessa e Database con una raccolta di S1](./media/documentdb-supercharge-your-account/documentdb-change-performance-S1.png)

4. In e **l'insieme** , fare clic su **altro**e quindi fare clic su **Impostazioni**.   
5. In e **l'Impostazioni** , fare clic su **Livello prezzi** e viene visualizzato il valore stimato costo mensile per ciascun piano. In e lo **scegliere il livello dei prezzi** , fare clic su **Standard**e quindi fare clic su **Seleziona** per salvare la modifica.

      ![Schermata delle impostazioni DocumentDB e scegliere i prezzi blade di livello](./media/documentdb-supercharge-your-account/documentdb-change-performance.png)

6. Di nuovo in e **l'Impostazioni** **Prezzi livello** viene modificato in **Standard** e viene visualizzata la **velocità (RU/s)** con un valore predefinito di 400. Fare clic su **OK** per salvare le modifiche. 

    > [AZURE.NOTE] È possibile impostare la velocità tra 400 e 10.000 [richiedere unità](../articles/documentdb/documentdb-request-units.md)computer (RU/s). **Prezzi riepilogo** nella parte inferiore della pagina viene aggiornato automaticamente con una stima del costo mensile.
    
    ![Schermata della stessa e impostazioni che mostra la posizione in cui modificare il valore di produttività](./media/documentdb-supercharge-your-account/documentdb-change-performance-set-thoughput.png)

8. Nuovo in e il **Database** , è possibile verificare le velocità della raccolta. 

    ![Schermata della stessa e Database con insieme modificato](./media/documentdb-supercharge-your-account/documentdb-change-performance-confirmation.png)

Per ulteriori informazioni sulle modifiche relativi alla velocità definite dall'utente e predefiniti, vedere il post del blog [DocumentDB: tutto quello è necessario conoscere utilizzando le nuove opzioni prezzi](https://azure.microsoft.com/blog/documentdb-use-the-new-pricing-options-on-your-existing-collections/).

## <a name="next-steps"></a>Passaggi successivi

Se si determina che è necessario maggiore velocità (maggiore di 10.000 RU/s) o altro spazio di archiviazione (maggiore di 10GB) è possibile creare una raccolta partizionata. Per creare una raccolta partizionata, vedere [creare una raccolta](documentdb-create-collection.md).