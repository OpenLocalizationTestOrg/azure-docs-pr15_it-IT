<properties
    pageTitle="Creare un indice di ricerca di Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Che cos'è un indice di ricerca di Azure e come viene utilizzata?"
    services="search"
    manager="jhubbard"
    documentationCenter=""
    authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="create-an-azure-search-index"></a>Creare un indice di ricerca di Azure
> [AZURE.SELECTOR]
- [Panoramica](search-what-is-an-index.md)
- [Portale](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [RESTO](search-create-index-rest-api.md)

## <a name="what-is-an-index"></a>Che cos'è un indice?

Un *indice* è un archivio persistente di *documenti* e altri costrutti utilizzati da un servizio di ricerca di Azure. Un documento è un'unica dei dati che supportano le ricerche nell'indice. Ad esempio, un rivenditore di e-commerce potrebbe avere un documento per ogni elemento che vendono, un'organizzazione news disponga di un documento per ogni articolo e così via. Mappatura di questi concetti a più intuitivo equivalenti di database: un *indice* è livello concettuale simile a una *tabella*e *i documenti* sono approssimativamente alle *righe* in una tabella.

Quando si aggiungono/caricamento di documenti e inviano le query di ricerca alla ricerca di Azure, si inviare le richieste in un indice specifico nel servizio di ricerca.

## <a name="field-types-and-attributes-in-an-azure-search-index"></a>Tipi di campo e attributi in un indice di ricerca di Azure

Come si definisce lo schema, è necessario specificare il nome, tipo e attributi di ogni campo dell'indice di. Il tipo di campo Classifica i dati memorizzati in questo campo. Attributi sono impostati su singoli campi per specificare come viene utilizzato il campo. Consente di enumerare le tabelle seguenti i tipi e attributi che è possibile specificare.


### <a name="field-types"></a>Tipi di campo
|Tipo|Descrizione|
|------------|-----------|
|*Edm.String*|Testo che facoltativamente può essere suddiviso in token per la ricerca full-text (ritorno a capo, derivanti e così via).|
|*Collection(EDM.String)*|Elenco di stringhe che facoltativamente può essere suddiviso in token per la ricerca full-text. Illimitato teorico superiore al numero di elementi in una raccolta, ma il limite massimo di 16 MB di dimensione payload si applica alle raccolte.|
|*Edm.Boolean*|Contiene valori VERO/FALSO.|
|*Edm.Int32*|valori di a 32 bit.|
|*Edm.Int64*|valori intero a 64 bit.|
|*EDM*|Dati numerici e precisione doppia.|
|*DateTimeOffset*|I valori di ora rappresentati nel formato di OData V4 di data (ad esempio `yyyy-MM-ddTHH:mm:ss.fffZ` o `yyyy-MM-ddTHH:mm:ss.fff[+/-]HH:mm`).|
|*Edm.GeographyPoint*|Punto che rappresenta un'area geografica sul globo.|

È possibile trovare informazioni più dettagliate sulle [tipi di dati su MSDN supportati della ricerca di Azure](https://msdn.microsoft.com/library/azure/dn798938.aspx).



### <a name="field-attributes"></a>Attributi dei campi
|Attributo|Descrizione|
|------------|-----------|
|*Chiave*|Stringa che fornisce l'ID univoco di ogni documento utilizzato per cercare documenti. Ogni indice deve avere una chiave. Un solo campo può essere la chiave e il relativo tipo deve essere impostato su Edm.String.|
|*Recuperabili*|Specifica se un campo può essere restituito un risultato di ricerca.|
|*Possibile filtrare*|Consente il campo da utilizzare nelle query di filtro.|
|*Ordinabile*|Consente di ordinare i risultati di ricerca utilizzando questo campo una query.|
|*Facetable*|Consente a un campo da utilizzare in una struttura di [spostamento in base a facet](search-faceted-navigation.md) per il filtro automatico diretto utente. In genere campi contenenti valori che si ripetono che è possibile utilizzare per raggruppare più documenti (ad esempio, più documenti che rientrano in un unico marchio o di servizi) funzionano in modo ottimale facet.|
|*Che supportano le ricerche*|Contrassegna il campo come full-text che supportano le ricerche.|

È possibile trovare informazioni più dettagliate sulle della ricerca di Azure [attributi indice su MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx).



## <a name="guidance-for-defining-an-index-schema"></a>Indicazioni per la definizione di uno schema di indice

Quando si progetta l'indice, richiedere del tempo in fase di pianificazione di pensare a ogni decisione. È importante tenere la ricerca utente esperienza e le esigenze aziendali presenti quando si progetta l'indice di ogni campo deve essere assegnata gli [attributi appropriati](https://msdn.microsoft.com/library/azure/dn798941.aspx). La modifica di un indice dopo la distribuzione comporta la ricostruzione e ricaricare i dati.


Se i requisiti di archiviazione cambiano nel tempo, è possibile aumentare o diminuire la capacità aggiungendo o rimuovendo partizioni. Per informazioni dettagliate, vedere [gestire il servizio di ricerca in Azure](search-manage.md) o [Servizi limiti](search-limits-quotas-capacity.md).
