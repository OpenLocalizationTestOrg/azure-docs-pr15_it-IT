<properties 
   pageTitle="Comprendere le prestazioni di Database SQL Azure | Microsoft Azure" 
   description="Il Database di SQL Azure sono disponibili gli strumenti di prestazioni per identificare più aree che è possono migliorare le prestazioni delle query corrente." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Comprendere le prestazioni di Database SQL

Database SQL Azure sono disponibili gli strumenti di prestazioni per identificare e migliorare le prestazioni dei database fornendo consigli e azioni regolazione intelligente. 

1. Selezionare il database nel [Portale di Azure](http://portal.azure.com) e fare clic su **tutte le impostazioni** > **prestazioni **  >  **Panoramica** per aprire la pagina **delle prestazioni** . 


2. Fare clic su **suggerimenti** per la [Preparazione di Database SQL](#sql-database-advisor)di aprire e fare clic su **query** per aprire [Comprendere le prestazioni di Query](#query-performance-insight).

    ![Visualizzare le prestazioni](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Cenni preliminari sulle prestazioni

Fare clic su **Panoramica** o nel riquadro **delle prestazioni** per passare a dashboard prestazioni per il database. Questa visualizzazione viene fornito un riepilogo delle prestazioni del database e consente di ottimizzazione e risoluzione dei problemi di prestazioni. 

![Prestazioni](./media/sql-database-performance/performance.png)

- Riquadro **suggerimenti** fornisce un riepilogo dei suggerimenti per il database di ottimizzazione (consigli primi 3 vengono visualizzati se esistono più). Fare clic su riquadro consente di accedere alla **Preparazione di Database SQL**. 
- Il riquadro **attività regolazione** offre un riepilogo in corso e completate ottimizzazione delle azioni per il database, fornisce una breve descrizione nella cronologia di ottimizzazione delle attività. Fare clic su riquadro consente di accedere alla visualizzazione della cronologia regolazione completo per il database.
- Il riquadro **regolazione automatica** è illustrata la configurazione regolazione automatica per il database (le azioni di ottimizzazione vengono configurate automaticamente da applicare al database). Fare clic su riquadro, verrà visualizzata la finestra di dialogo Configurazione di automazione.
- Il riquadro **query di Database** viene visualizzato il riepilogo delle prestazioni di query per il database (complessiva DTU superiore e l'uso risorse query). Fare clic su riquadro consente di accedere a **Comprendere le prestazioni di Query**.



## <a name="sql-database-advisor"></a>Preparazione di Database SQL


[Preparazione di Database SQL](sql-database-advisor.md) fornisce indicazioni di ottimizzazione intelligente che consentono di migliorare le prestazioni del database. 

- Consigli su cui l'indicizzazione per creare o eliminare (e un'opzione per applicare i suggerimenti di indice automaticamente senza interazione dell'utente e il ripristino automaticamente suggerimenti che hanno un impatto negativo sulle prestazioni).
- Consigli quando sono identificati i problemi di schema del database.
- Consigli per la query possono trarre vantaggio da query con parametri.




## <a name="query-performance-insight"></a>Comprendere le prestazioni di query

[Comprendere le prestazioni di query](sql-database-query-performance.md) consente di ridurre le attività di risoluzione dei problemi di prestazioni del database fornendo:

- Approfondita il consumo di risorse (DTU) il database. 
- CPU superiore utilizzo di query, che può essere ottimizzata potenzialmente per migliorare le prestazioni. 
- La possibilità di drill-down dei dettagli di una query. 


## <a name="additional-resources"></a>Risorse aggiuntive

- [Indicazioni sulle prestazioni di Database SQL Azure per singoli database](sql-database-performance-guidance.md)
- [Quando utilizzare un pool di database flessibile](sql-database-elastic-pool-guidance.md)