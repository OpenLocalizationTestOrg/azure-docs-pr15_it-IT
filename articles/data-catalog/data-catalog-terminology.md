<properties
   pageTitle="Terminologia di catalogo dati Azure | Microsoft Azure"
   description="In questo articolo viene fornita un'introduzione ai concetti e termini usati nella documentazione del catalogo dati di Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-terminology"></a>Azure terminologia di catalogo dati

## <a name="catalog"></a>Catalogo

Catalogo dei dati di Azure è un archivio dei metadati basato sul cloud dati origini dati sia le attività possono essere registrate. Il catalogo funge da una posizione di archiviazione centrale per i metadati strutturali estratti dalle origini dati e per i metadati descrittivo aggiunti dagli utenti.

## <a name="data-source"></a>Origine dati

Un'origine dati è un sistema o un contenitore per la gestione di dati. Esempi di database di SQL Server, database Oracle, database di SQL Server Analysis Services (tabulari o multidimensionali) e i server di SQL Server Reporting Services.

## <a name="data-asset"></a>Risorse di dati

Attività di dati sono contenuti all'interno di origini dati che possono essere registrate con il catalogo. Esempi di tabelle di SQL Server e le visualizzazioni, tabelle Oracle e visualizzazioni, SQL Server Analysis Services misure, dimensioni e indicatori KPI e report di SQL Server Reporting Services.

## <a name="data-asset-location"></a>Ubicazione dei dati

Il catalogo contiene la posizione di un'origine dati o risorse di dati, possono essere usate per connettersi all'origine tramite un'applicazione client. Il formato e i dettagli della posizione variano in base al tipo di origine dati. Ad esempio una tabella di SQL Server può essere identificata in base al nome di quattro parti: il nome del server, nome del database, nome dello schema, nome dell'oggetto: mentre un Report di SQL Server Reporting Services possono essere identificati tramite l'URL.

## <a name="structural-metadata"></a>Metadati strutturali

Metadati strutturali sono i metadati estratti da un'origine dati che viene descritta la struttura di un bene di dati. Sono inclusi il percorso di risorse, il relativo nome oggetto e tipo e altre caratteristiche specifiche del tipo. Ad esempio, i metadati strutturali per tabelle e viste includono i nomi e i tipi di dati per le colonne dell'oggetto.

## <a name="descriptive-metadata"></a>Metadati descrittivi

Metadati descrittivi sono basato su metadati che descrive lo scopo intento di un bene di dati. In genere aggiunto metadati descrittivi dagli utenti del catalogo nel portale di catalogo dati di Azure, ma anche possibile verranno estratte dall'origine dati durante la registrazione. Ad esempio lo strumento di registrazione del catalogo dati di Azure verrà estratte da descrizioni della proprietà descrizione nel SQL Server Analysis Services e SQL Server Reporting Services e da [ms_description proprietà estesa](https://technet.microsoft.com/library/ms190243.aspx) nei database di SQL Server, se queste proprietà sono state inserite i valori.

## <a name="request-access"></a>Richiedere l'accesso

Metadati descrittivi del bene dati possono includere informazioni su come richiedere l'accesso a risorse di dati dell'origine dati. Queste informazioni viene visualizzate la posizione di risorse dei dati e possono includere uno o più delle opzioni seguenti:

- Indirizzo di posta elettronica dell'utente o team responsabile per concedere l'accesso all'origine dati.
- L'URL del processo di documentazione che gli utenti devono seguire per accedere all'origine dati.
- URL di un'identità e accessi strumento di gestione (ad esempio Microsoft identità Manager) che può essere utilizzato per accedere all'origine dati.
- Voce di testo libero che descrive come gli utenti possono accedere all'origine dati.

## <a name="preview"></a>Anteprima

Un'anteprima nel catalogo dati di Azure è uno snapshot di un massimo di 20 record che possono essere estratti dall'origine dati durante la registrazione e archiviate nel catalogo con i metadati di risorse di dati. L'anteprima consente di utenti alla scoperta di un bene dati comprendono meglio la funzione e lo scopo. Visualizzare i dati di esempio in altre parole, può essere più efficace rispetto a visualizzare solo i nomi delle colonne e i tipi di dati.
Le anteprime sono supportate solo per tabelle e viste e devono essere selezionate in modo esplicito dall'utente durante la registrazione.

## <a name="data-profile"></a>Profilo dei dati

Un profilo di dati nel catalogo dati di Azure è uno snapshot del livello di tabella e colonna metadati relativi a una risorsa dati registrati che può essere estratti dall'origine dati durante la registrazione e archiviata nel catalogo con i metadati di risorse di dati. Il profilo dei dati può aiutare gli utenti alla scoperta di un bene dati comprendono meglio la funzione e lo scopo. Analogamente alle anteprime, i profili dei dati deve essere esplicitamente selezionate dall'utente durante la registrazione.

> [AZURE.NOTE] Estrazione di un profilo di dati può essere un'operazione costosa per tabelle di grandi dimensioni e le visualizzazioni e aumenti significativamente il tempo necessario per registrare un'origine dati.

## <a name="user-perspective"></a>Punto di vista utente

Nel catalogo dati di Azure, qualsiasi utente può fornire metadati descrittivo per una risorsa dati registrati. Ogni utente dispone di un punto di vista distinto nei dati e il suo utilizzo. Ad esempio, l'amministratore responsabile di un server può fornire i dettagli dei relativi contratto servizio () o windows backup. un amministratore dei dati può fornire collegamenti alla documentazione per l'azienda elabora dati supporta; e gli analisti possono fornire una descrizione in termini che sono più importanti per gli altri analisti e che può essere più significative per gli utenti che necessitano per individuare e comprendere i dati.

Ognuna di queste prospettive sono importanti per sé e con il catalogo dati di Azure ogni utente può fornire le informazioni che sono significative, mentre tutti gli utenti possono usare queste informazioni per comprendere i dati e allo scopo.

## <a name="expert"></a>Esperto

Un esperto è un utente che è stato individuato con un punto di vista "esperto" informato per una risorsa di dati. Tutti gli utenti possono aggiungere se stessi o un altro utente come un esperto per una risorsa. Viene elencato come un esperto non suggerisce privilegi aggiuntivi nel catalogo dati di Azure. consente agli utenti di individuare facilmente tali prospettive che potrebbe essere utili durante l'analisi dei metadati descrittivo di una risorsa.

## <a name="owner"></a>Proprietario

Un proprietario è un utente che dispone di privilegi aggiuntivi per la gestione di una risorsa di dati nel catalogo dati di Azure. Gli utenti possono diventare proprietari dei beni dati registrato e proprietari aggiungere altri utenti come comproprietari. Per ulteriori informazioni, vedere [come gestire le risorse dati](data-catalog-how-to-manage.md)  
> [AZURE.NOTE] Gestione e la proprietà sono disponibili solo nel Standard Edition di Azure catalogo dati.

## <a name="registration"></a>Registrazione

Registrazione comporta la estrazione dei metadati bene dati da un'origine dati e la copia al servizio Catalogo dati di Azure. Risorse di dati che sono state registrate possono essere annotati e individuati.

## <a name="see-also"></a>Vedere anche

- [Che cos'è il catalogo dati di Azure?](data-catalog-what-is-data-catalog.md) -In questo articolo viene fornita una panoramica del servizio Catalogo dati di Azure, il valore fornito e gli scenari che supporta.

- [Guida introduttiva a catalogo dati di Azure](data-catalog-get-started.md) : questo articolo fornisce un'esercitazione-to-end che illustra come utilizzare il catalogo dati di Azure per l'individuazione di origine dati.  
