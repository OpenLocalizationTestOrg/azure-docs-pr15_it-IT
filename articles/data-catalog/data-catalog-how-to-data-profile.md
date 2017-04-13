<properties
    pageTitle="Come origine dati del profilo di dati"
    description="Articolo procedure evidenziazione come includere i profili dei dati di livello di tabella e di colonna durante la registrazione delle origini dati nel catalogo dati di Azure e come usare i profili dei dati per comprendere le origini dati."
    services="data-catalog"
    documentationCenter=""
    authors="spelluru"
    manager="NA"
    editor=""
    tags=""/>
<tags
    ms.service="data-catalog"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-catalog"
    ms.date="09/13/2016"
    ms.author="spelluru"/>

# <a name="data-profile-data-sources"></a>Origini dati di profilo

## <a name="introduction"></a>Introduzione

**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestita che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. In altre parole, **Catalogo dati di Azure** è basato sui consentire agli utenti alla scoperta di comprensione e utilizzare le origini dati e alle organizzazioni di risoluzione per ottenere il massimo dai dati esistenti. Quando viene registrata un'origine dati con **Il catalogo dati di Azure**, i metadati viene copiato e indicizzato dal servizio, ma la storia non finisce qui.

La funzionalità **Di analisi dei dati** del **Catalogo dati di Azure** esamina i dati dalle origini dati supportate nel catalogo e consente di raccogliere statistiche e informazioni relative a tali dati. È facile da includere un profilo di risorse di dati. Quando si registra una risorsa di dati, selezionare **Includi dati profilo** nello strumento di registrazione dell'origine dati.

## <a name="what-is-data-profiling"></a>Che cos'è l'analisi dei dati

Dati di analisi esamina i dati nell'origine dati da registrare e raccoglie le statistiche e informazioni relative a tali dati. Durante l'individuazione di origine dati, queste statistiche consentono di determinare se i dati per risolvere i problemi aziendali.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Origini dati seguenti supportano l'analisi dei dati:

- Visualizzazioni e tabelle di SQL Server (inclusi i database di SQL Azure e Azure SQL Data Warehouse)
- Visualizzazioni e tabelle oracle
- Teradata tabelle e viste
- Tabelle di hive

Tra i profili dei dati quando registrazione delle risorse dati consente agli utenti di rispondere alle domande sulle origini dati, tra cui:

-   Può essere utilizzato per risolvere il problema aziendale?
-   I dati sono conformi alle norme specifici o motivi?
-   Quali sono alcune delle alterazioni dell'origine dati?
-   Informazioni sui possibili problemi di integrare tali dati dell'applicazione?

> [AZURE.NOTE] È anche possibile aggiungere documentazione di un bene per descrivere come dati potrebbero essere integrati in un'applicazione. Scopri [come origini dati del documento](data-catalog-how-to-documentation.md).


<a name="howto"/>
## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Come includere un profilo di dati durante la registrazione di un'origine dati

È facile da includere un profilo dell'origine dati. Quando si registra un'origine dati, nel riquadro **oggetti per la registrazione** dello strumento di registrazione di origine dati, selezionare **Includi dati profilo**.

![](media\data-catalog-data-profile\data-catalog-register-profile.png)

Per ulteriori informazioni su come registrare le origini dati, vedere [come registrare le origini dati](data-catalog-how-to-register.md) e [iniziare a utilizzare il catalogo dati di Azure](data-catalog-get-started.md).


## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Applicazione del filtro a risorse di dati che includono i profili dei dati
Consentono di scoprire le risorse dati che includono un profilo di dati, è possibile includere `has:tableDataProfiles` o `has:columnsDataProfiles` in uno dei termini di ricerca.

> [AZURE.NOTE] Selezionare **Includi dati profilo** nello strumento di registrazione dell'origine dati include tabella e le informazioni sul profilo di livello di colonna. Tuttavia, l'API di catalogo dati consente risorse di dati per la registrazione con un solo set di informazioni sul profilo inclusi.

## <a name="viewing-data-profile-information"></a>Informazioni sul profilo di una visualizzazione dati

Dopo aver trovato un'origine dati adatta con un profilo, è possibile visualizzare i dettagli del profilo di dati. Per visualizzare il profilo dei dati, selezionare una risorsa di dati e scegliere **Profilo dei dati** nella finestra portale catalogo dati.

![](media\data-catalog-data-profile\data-catalog-view.png)

Un profilo di dati nel **Catalogo dati di Azure** Mostra tabella e colonna profilo informazioni inclusi:

### <a name="object-data-profile"></a>Profilo dei dati oggetto

-   Numero di righe
-   Dimensioni della tabella
-   Quando l'oggetto dell'ultimo aggiornamento

### <a name="column-data-profile"></a>Profilo dei dati di colonna

- Tipo di dati di colonna
- Numero di valori distinti
- Numero di righe con valori NULL
- Valore minimo, massimo, Media e deviazione standard i valori di colonna

## <a name="summary"></a>Riepilogo
Analisi dei dati sono disponibili statistiche e informazioni sulle risorse dati registrati per determinare l'idoneità dei dati per risolvere i problemi aziendali. Oltre a inserire le note e la documentazione di origini dati, i profili dei dati può concedere ad utenti approfondita dei dati.


## <a name="see-also"></a>Vedere anche
-   [Come registrare le origini dati](data-catalog-how-to-register.md)
-   [Guida introduttiva a catalogo dati di Azure](data-catalog-get-started.md)
