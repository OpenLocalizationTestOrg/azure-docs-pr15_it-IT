<properties
    pageTitle="Importare dati di ricerca di Azure usando indicizzatori nel portale di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Utilizzare l'importazione guidata dati di Azure ricerca nel portale di Azure ai dati di ricerca per indicizzazione dallo spazio di archiviazione Blob Azure, archiviazione tabella, Database SQL e SQL Server in macchine virtuali di Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importare dati tramite il portale di ricerca di Azure

Portale di Azure fornisce una procedura guidata **Importa dati** nel dashboard di ricerca di Azure per il caricamento dei dati in un indice. 

  ![Importare i dati della barra dei comandi][1]

Internamente, la procedura guidata Configura e richiama un *indicizzatore*automatizzare diversi passaggi del processo di indicizzazione: 

- Connettersi a un'origine dati esterna in attuale abbonamento Azure
- Genera automaticamente uno schema di indice in base alla struttura di dati di origine
- Creare documenti basati su un set di righe recuperati dall'origine dati
- Caricare documenti all'indice nel servizio di ricerca

È possibile provare il flusso di lavoro con dati di esempio in DocumentDB. Per ulteriori informazioni, visitare [Guida introduttiva di Azure ricerca nel portale di Azure](search-get-started-portal.md) .

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Origini dati supportate per l'importazione guidata dati

La procedura guidata Importazione dati supporta le origini dati seguenti: 

- Database SQL Azure
- Dati relazionali di SQL Server in macchine Virtuali un Azure
- DocumentDB Azure
- Archiviazione Blob Azure (nella versione di anteprima)
- Archivio tabelle Azure (nella versione di anteprima)

Un set di dati bidimensionali è un input necessario. È possibile importare solo da una singola tabella, una vista di database o una struttura di dati equivalenti. È necessario creare la struttura di dati prima di eseguire la procedura guidata.

Si noti che alcuni gli indicizzatori sono ancora in anteprima, ovvero che la definizione dell'indicizzatore è supportata dalla versione di anteprima dell'API. Per ulteriori informazioni e collegamenti, vedere [Panoramica dell'indicizzatore](search-indexer-overview.md) .

## <a name="connect-to-your-data"></a>Connettersi ai dati

1. Accedere al [Portale di Azure](https://portal.azure.com) e dashboard aperto del servizio. È possibile fare clic su **servizi di ricerca** nella barra di spostamento per mostrare i servizi esistenti in abbonamento corrente. 

2. Fare clic su **Importa dati** della barra dei comandi ad Apri diapositiva e l'Importa dati.  

3. Fare clic su **connettersi ai dati** per specificare una definizione di origine dati utilizzata da un indicizzatore. Per le origini dati interne all'abbonamento, la procedura guidata in genere vengono rilevate e leggere le informazioni di connessione, ridurre i requisiti di configurazione complessivi.

| | |
|--------|------------|
|**Origine dati esistente** | Se si dispongono già di indicizzatori definiti nel servizio di ricerca, è possibile selezionare una definizione di origine dati esistente per l'importazione di un altro.|
|**Database SQL Azure** | Nome del servizio, le credenziali per l'utente del database con autorizzazione di lettura e un nome di database può specificato nella pagina o tramite una stringa di connessione ADO.NET. Scegliere l'opzione stringa di connessione per visualizzare o personalizzare le proprietà. <br/><br/>Nella pagina è necessario specificare la tabella o la visualizzazione che contiene il set di righe. Questa opzione viene visualizzata dopo avere stabilito la connessione, assegnando un elenco a discesa in modo che è possibile effettuare una selezione.|
|**SQL Server in macchine Virtuali di Azure** | Specificare un nome completo del servizio, l'ID utente e password e database come una stringa di connessione. Per utilizzare questa origine dati, è necessario avere già installato un certificato nell'archivio locale per la crittografia la connessione. <br/><br/>Nella pagina è necessario specificare la tabella o la visualizzazione che contiene il set di righe. Questa opzione viene visualizzata dopo avere stabilito la connessione, assegnando un elenco a discesa in modo che è possibile effettuare una selezione.
|**DocumentDB** |Requisiti di includono l'account, database e insieme. Tutti i documenti nella raccolta verranno inclusi nell'indice analitico. È possibile definire una query per unire o filtrare il set di righe o per rilevare i documenti modificati per le operazioni di aggiornamento dei dati successive.|
|**Archiviazione Blob Azure** | Requisiti di includono l'account di archiviazione e un contenitore. Facoltativamente, se i nomi di blob seguono una convenzione di denominazione virtuale per scopi di raggruppamento, è possibile specificare la parte di directory virtuale del nome del come una cartella all'interno del contenitore. Per ulteriori informazioni, vedere [L'indicizzazione archiviazione Blob (preview)](search-howto-indexing-azure-blob-storage.md) . |
|**Archivio tabelle Azure** | Requisiti di includono l'account di archiviazione e un nome di tabella. Facoltativamente, è possibile specificare una query per recuperare un sottoinsieme delle tabelle. Per ulteriori informazioni, vedere [L'indicizzazione archiviazione tabella (preview)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Personalizzare l'indice di destinazione

Un indice preliminare viene in genere dedotta dal set di dati. Aggiungere, modificare o eliminare i campi per completare lo schema. Inoltre, è possibile impostare gli attributi livello di campo per determinare i comportamenti ricerca successiva.

1. **Indice di destinazione Personalizza**, specificare il nome e una **chiave** utilizzata per identificare in modo univoco ogni documento. Il tasto deve essere una stringa. Se i valori dei campi sono inclusi gli spazi o trattini assicurarsi di impostare le opzioni avanzate in **importare i dati** come eliminare il controllo di convalida per questi caratteri.

2. Rivedere e modificare i campi rimanenti. Tipo e il nome di campo vengono in genere compilati automaticamente. È possibile modificare il tipo di dati.

3. Impostare gli attributi di indice per ogni campo:

 - Recuperabili restituisce il campo nei risultati della ricerca.
 - Possibile filtrare consente il campo venga fatto riferimento nelle espressioni di filtro.
 - Ordinabile consente il campo da utilizzare in un ordinamento.
 - Facetable consente il campo per l'esplorazione in base a facet.
 - Ricerca full-text consente che supportano le ricerche.
  
4. Se si desidera specificare un analizzatore lingua a livello di campo, fare clic sulla scheda **analisi** . Solo analisi del linguaggio può essere specificati al momento. Utilizzo di un analizzatore personalizzato o un analizzatore non lingua come parola chiave, il motivo e così via, richiederà codice.

 - Fare clic su **ricercabile** per designare ricerca full-text sul campo e abilitare l'elenco a discesa Analyzer.
 - Scegliere analizzatore desiderato. Per informazioni dettagliate, vedere [creare un indice per i documenti in più lingue](search-language-support.md) .

5. Fare clic su **Suggester** per attivare i suggerimenti di query ricerca incrementale in campi selezionati.


## <a name="import-your-data"></a>Importare i dati

1. **Importare i propri dati**, specificare un nome per l'indicizzatore. Tenere presente che il prodotto della procedura guidata Importazione dati è un indicizzatore. In seguito, se si vuole visualizzarlo o modificarlo, è necessario selezionarlo dal portale di anziché da ripetere la procedura guidata. 

2. Specificare la pianificazione, si basa sul internazionali fuso orario in cui il servizio viene eseguito il provisioning.

3. Impostare le opzioni avanzate per specificare le soglie se l'indicizzazione continuare se un documento viene interrotta. Inoltre, è possibile specificare se i campi **chiave** può contenere spazi e le barre.  

## <a name="edit-an-existing-indexer"></a>Modificare un indicizzatore esistente

In dashboard del servizio, fare doppio clic sul riquadro dell'indicizzatore alla diapositiva un elenco di tutti gli indicizzatori creato per l'abbonamento. Fare doppio clic su uno dei indicizzatori per eseguire, modificare o eliminarla. È possibile sostituire l'indice con uno esistente, modificare l'origine dati e impostare le opzioni per le soglie di errore durante l'indicizzazione.

## <a name="edit-an-existing-index"></a>Modificare un indice esistente

Risultati di ricerca di Azure, strutturali aggiornamenti a un indice richiederà rigenerazione dell'indice, che include l'eliminazione dell'indice, ricreare l'indice e ricaricare i dati. Gli aggiornamenti strutturali includono la modifica di un tipo di dati e la ridenominazione o eliminazione di un campo.

Le modifiche che non è richiesta una ricostruzione includono l'aggiunta di un nuovo campo punteggio profili, modifica suggesters o la modifica di analisi di lingua di modifica. Per ulteriori informazioni, vedere [Aggiorna indice](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Passaggio successivo

Esaminare questi collegamenti per ulteriori informazioni:

- [L'indicizzazione di Database SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [L'indicizzazione DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Archiviazione Blob indicizzazione (preview)](search-howto-indexing-azure-blob-storage.md)
- [Servizio di archiviazione tabelle indicizzazione (preview)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

