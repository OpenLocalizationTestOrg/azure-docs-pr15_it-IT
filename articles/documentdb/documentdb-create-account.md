<properties
    pageTitle="Come creare un account DocumentDB | Microsoft Azure"
    description="Creare un database NoSQL con Azure DocumentDB. Seguire queste istruzioni per creare un account DocumentDB e iniziare a creare il database di NoSQL rapidamente su scala globale eccezionali." 
    keywords="creare un database"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-create-a-documentdb-nosql-account-using-the-azure-portal"></a>Come creare un account DocumentDB NoSQL tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](documentdb-create-account.md)
- [CLI Azure e Azure Manager delle risorse](documentdb-automation-resource-manager-cli.md)

Per creare un database con Microsoft Azure DocumentDB, è necessario:

- Ha un account Azure. Se non è già disponibile, è possibile ottenere un [account Azure gratuito](https://azure.microsoft.com/free) . 
- Creare un account DocumentDB.  

È possibile creare un account DocumentDB tramite il portale di Azure, modelli di gestione risorse Azure o Azure interfaccia riga di comando (CLI). In questo articolo viene illustrato come creare un account DocumentDB tramite il portale di Azure. Per creare un account tramite Gestione risorse di Azure o CLI Azure, vedere [creazione di account di automatizzare DocumentDB del database](documentdb-automation-resource-manager-cli.md).

Si ha DocumentDB? Guardare [questo](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) video quattro minuto da Scott Hanselman per informazioni su come completare le attività più comuni nel portale online.

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  In Jumpbar, fare clic su **Nuovo**, fare clic su **database**e quindi fare clic su **DocumentDB (NoSQL)**. 

    ![Schermata del portale di Azure, evidenziazione altri servizi e DocumentDB (NoSQL)](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-1.png)  

3. Nella finestra e **nuovo account** , specificare la configurazione desiderata per l'account DocumentDB.

    ![Schermata della stessa e nuovo DocumentDB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-2.png)

    - Nella casella **ID** immettere un nome per identificare il conto DocumentDB.  Quando l' **ID** viene convalidata, nella casella **ID** viene visualizzato un segno di spunta verde. Il valore di **ID** diventa il nome host all'interno di URI. L' **ID** può contenere solo lettere minuscole lettere, numeri e i '-' caratteri e deve essere compreso tra 3 e 50 caratteri. Si noti che *documents.azure.com* viene aggiunto al nome dell'endpoint che si sceglie, il cui risultato diventa l'endpoint di account DocumentDB.

    - Nella casella **NoSQL API** selezionare il modello di programmazione da usare:
        - **DocumentDB**: l'API DocumentDB disponibile tramite .NET, Java, Node, Python JavaScript [SDK](documentdb-sdk-dotnet.md), nonché HTTP [resto](https://msdn.microsoft.com/library/azure/dn781481.aspx)e offre l'accesso a tutte le funzionalità di DocumentDB. 
       
        - **MongoDB**: DocumentDB offre anche [protocollo a livello di supporto](documentdb-protocol-mongodb.md) per **MongoDB** API. Quando si sceglie l'opzione MongoDB API, è possibile utilizzare SDK MongoDB esistente e [Strumenti](documentdb-mongodb-mongochef.md) per comunicare con DocumentDB. È possibile [spostare](documentdb-import-data.md) le app MongoDB esistente per utilizzare DocumentDB, con [Nessuna modifica codice necessaria](documentdb-connect-mongodb-account.md)e vantaggi offerti da un database completamente gestito come un servizio, con scala illimitata, replica globale e altre funzionalità.

    - Per l' **abbonamento**, selezionare l'abbonamento Azure che si desidera utilizzare per l'account DocumentDB. Se l'account ha un solo abbonamento, tale account sia selezionata per impostazione predefinita.

    - Nel **Gruppo di risorse**, selezionare o creare un gruppo di risorse per l'account DocumentDB.  Per impostazione predefinita, viene creato un nuovo gruppo di risorse. Per ulteriori informazioni, vedere [tramite il portale di Azure per gestire le risorse Azure](../articles/azure-portal/resource-group-portal.md).

    - Consente di specificare l'area geografica in cui pubblicare l'account DocumentDB **posizione** . 

4.  Dopo aver configurate le nuove opzioni account DocumentDB, fare clic su **Crea**. Per controllare lo stato della distribuzione, controllare l'hub di notifiche.  

    ![Creare rapidamente database - schermata dell'hub le notifiche, con la creazione di account DocumentDB](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-4.png)  

    ![Schermata dell'hub le notifiche, che mostra che l'account DocumentDB è stato creato correttamente e distribuito in un gruppo di risorse - notifica di creazione di database in linea](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-5.png)

5.  Dopo la creazione di account DocumentDB, è pronto per l'uso con le impostazioni predefinite. La coerenza predefinita dell'account DocumentDB è impostata su **sessione**.  È possibile regolare la coerenza predefinito, fare clic su **Uniformità predefinita** nel menu della risorsa. Per ulteriori informazioni sui livelli di coerenza offerti dai DocumentDB, vedere [livelli di coerenza in DocumentDB](documentdb-consistency-levels.md).

    ![Schermata della stessa e gruppo di risorse - iniziare lo sviluppo di applicazioni](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-6.png)  

    ![Schermata della stessa e livello di coerenza - coerenza sessione](./media/documentdb-create-account/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un account DocumentDB, il passaggio successivo consiste nel creare una raccolta di DocumentDB e database. 

È possibile creare un nuovo insieme e un database utilizzando una delle operazioni seguenti:

- Il portale di Azure, come descritto in [creare un insieme di DocumentDB tramite il portale di Azure](documentdb-create-collection.md).
- Esercitazioni globale, che includono dati di esempio: [.NET](documentdb-get-started.md), [.NET MVC](documentdb-dotnet-application.md), [Java](documentdb-java-application.md), [Node](documentdb-nodejs-application.md)o [Python](documentdb-python-application.md).
- [.NET](documentdb-dotnet-samples.md#database-examples), [Node](documentdb-nodejs-samples.md#database-examples)o [Python](documentdb-python-samples.md#database-examples) codice di esempio disponibile in GitHub.
- Il [.NET](documentdb-sdk-dotnet.md), [Node](documentdb-sdk-node.md), [Java](documentdb-sdk-java.md), [Python](documentdb-sdk-python.md)e [resto](https://msdn.microsoft.com/library/azure/mt489072.aspx) SDK.

Dopo aver creato il database e insieme, è necessario [aggiungere documenti](documentdb-view-json-document-explorer.md) a raccolte.

Dopo avere documenti in una raccolta, è possibile utilizzare [DocumentDB SQL](documentdb-sql-query.md) per [eseguire query](documentdb-sql-query.md#executing-queries) in base ai propri documenti. È possibile eseguire query utilizzando [Query Esplora](documentdb-query-collections-query-explorer.md) il portale, l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx)o uno dei [SDK](documentdb-sdk-dotnet.md).

### <a name="learn-more"></a>Ulteriori informazioni

Per ulteriori informazioni su DocumentDB, esplorare queste risorse:

-   [Percorso formativo per DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/)
-   [Concetti e modello di risorse gerarchico DocumentDB](documentdb-resources.md)
