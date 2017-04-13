<properties
    pageTitle="Come eseguire la migrazione di logica App schema versione 2015-08-01-Preview | Servizio di Microsoft Azure App"
    description="È possibile migrare facilmente le app logica all'ultima versione di schema. Seguire la procedura seguente."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Come eseguire la migrazione di logica App schema versione 2015-08-01-Preview

Per spostare le app logica esistente al nuovo schema, eseguire le operazioni seguenti:  
1. Aprire l'app di logica nel portale di Azure  
2. Fare clic su Aggiorna Schema:

 ![Icona API][step1]   
La pagina Schema di aggiornamento consente di visualizzare e fornisce un collegamento a un documento che forniscono informazioni dettagliate sui miglioramenti nel nuovo schema: ![API icona][step2]

>[AZURE.NOTE] Quando si seleziona **Schema di aggiornamento**, automaticamente eseguire le operazioni di migrazione e prevedere l'output di codice. È possibile utilizzare per aggiornare la definizione di una, tuttavia, assicurarsi che si consiglia di attenersi ad esempio quelli illustrati nella sezione **procedure consigliate** .

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Procedure consigliate per la migrazione delle applicazioni di logica all'ultima versione di schema:  

- Copiare lo script migrato in una nuova App logica: non sovrascrivere il vecchio uno fino a quando non aver completato l'attività di test e confermata migrati app funziona come previsto.
- Testare la logica app **prima** dell'applicazione pratica di produzione
- Al termine del processo di migrazione, avviare l'aggiornamento delle applicazioni di logica per usare l' [API gestite](./apis-list.md) laddove possibile. Ad esempio, è possibile iniziare a usare v2 Dropbox, deposte si usano v1 DropBox.


## <a name="whats-next"></a>Che cos'è successiva
-  [Informazioni su come eseguire manualmente la migrazione delle applicazioni di logica](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png






