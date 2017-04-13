<properties
   pageTitle="Come individuare origini dati | Microsoft Azure"
   description="Articolo procedure evidenziazione come individuare i dati registrati risorse con il catalogo dati di Azure, tra cui la ricerca e filtro e tramite l'evidenziazione dei funzionalità del portale del catalogo dati di Azure."
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
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="how-to-discover-data-sources"></a>Come individuare origini dati

## <a name="introduction"></a>Introduzione
**Catalogo dati di Microsoft Azure** è un servizio cloud completamente gestita che funge da un sistema di registrazione e di sistema di individuazione per le origini dati dell'organizzazione. In altre parole, **Catalogo dati di Azure** è basato sui consentire agli utenti alla scoperta di comprensione e utilizzare le origini dati e alle organizzazioni di risoluzione per ottenere il massimo dai dati esistenti. Una volta registrata un'origine dati con **Il catalogo dati di Azure**, i metadati verrà indicizzato dal servizio, in modo che gli utenti possono cercare facilmente per individuare i dati che necessari.

## <a name="searching-and-filtering"></a>Ricerca e filtro

Individuazione nel **Catalogo dati di Azure** utilizza due metodi principali: la ricerca e filtro.

La ricerca è progettata per essere intuitivo e potente: per impostazione predefinita, i termini di ricerca vengono confrontati con le proprietà del catalogo, incluse le annotazioni fornito dall'utente.

Il filtro è progettato per completare la ricerca. Gli utenti possono selezionare caratteristiche specifiche, ad esempio esperti, tipo di origine dati, il tipo di oggetto e tag, per visualizzare solo le risorse dati corrispondente e per limitare i risultati della ricerca a corrispondenti anche risorse.

Utilizzando una combinazione di ricerca e filtro, gli utenti passare rapidamente le origini dati che sono state registrate con il **Catalogo dati di Azure** per individuare le origini dati che siano necessari.

## <a name="search-syntax"></a>Sintassi di ricerca

Anche se la ricerca di testo libero predefinito è semplice e intuitivo, gli utenti possono anche usare la sintassi di ricerca **Del catalogo dati di Azure**del un maggiore controllo sui risultati della ricerca. Ricerca in **Catalogo dati di Azure** supporta le tecniche seguenti:

| Tecnica                 | Utilizzo                                                                                                                                     | Esempio                                                   |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Ricerca di base              | Ricerca di base con uno o più termini di ricerca. Risultati sono tutte le risorse che corrispondono a una proprietà con una o più delle condizioni specificate. | dati delle vendite                                                |
| Definizione dell'ambito di proprietà          | Solo restituire le origini dati nel punto in cui il termine di ricerca viene trovata una corrispondenza con la proprietà specificata                                                   | nome: finanze                                              |
| Operatori booleani         | Ampliare o restringere la ricerca utilizzando operazioni booleane                                                                                     | Nota non aziendale                                     |
| Raggruppamento con parentesi | Utilizzare le parentesi a parti di gruppo della query per ottenere l'isolamento logico, in particolare in combinazione con operatori booleani              | nome: finanze e (tag: T1 OR tag: T2) |
| Operatori di confronto      | Utilizzare i confronti ad eccezione di uguaglianza per le proprietà con tipi di dati numerici e di data                                                | modifiedTime > "11/05/2014"                                 |

Per ulteriori informazioni sulla ricerca in **Catalogo dati di Azure** , vedere [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## <a name="hit-highlighting"></a>Evidenziazione dei risultati
Quando si visualizzano i risultati della ricerca, le proprietà visualizzate corrispondenti ai termini di ricerca specificati: ad esempio dati bene nome, descrizione e tag: verranno evidenziate per rendere più semplice identificare il motivo per cui una risorsa di dati specificato vengono restituita da una determinata ricerca.

> [AZURE.NOTE] Gli utenti possono attivare passaggi evidenziazione disattivato se lo si desidera, utilizzare l'opzione "Evidenziazione" nel portale di **Catalogo dati di Azure** .

Quando si visualizzano i risultati della ricerca, potrebbe non sempre essere evidente il motivo per cui una risorsa di dati è inclusa, neanche a fronte di evidenziazione abilitato. Perché vengono eseguita la ricerca di tutte le proprietà per impostazione predefinita, una risorsa di dati può essere restituita a causa di una corrispondenza su una proprietà del livello di colonna. E perché più utenti possono aggiungere note alle risorse di dati registrato con le proprie tag e le descrizioni, non tutti i metadati potrebbero essere visualizzati nell'elenco dei risultati della ricerca.

Riquadro visualizzazione predefinita, ogni riquadro visualizzato nei risultati della ricerca include un'icona "corrisponde a termine di ricerca Visualizza", che consente all'utente per visualizzare rapidamente il numero di corrispondenze e la relativa posizione e per accedervi se lo si desidera.

 ![Evidenziazione dei risultati e trova le corrispondenze ricerca nel portale di catalogo dati di Azure](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Riepilogo
Registrazione di un'origine dati con **Il catalogo dati di Azure** semplifica l'origine dati individuare e comprendere copiando strutturali e descrittivo i metadati dall'origine dati al servizio di catalogo. Una volta registrata un'origine dati, gli utenti di individuare l'utilizzo di filtri e ricerca all'interno del portale di **Catalogo dati di Azure** .

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva a catalogo dati di Azure](data-catalog-get-started.md) esercitazione per informazioni dettagliate su come individuare origini dati.
