<properties
    pageTitle="Che cos'è ricerca Azure | Microsoft Azure | Servizio di ricerca cloud ospitato"
    description="Ricerca Azure è un servizio di ricerca completamente gestito ospitato cloud. Informazioni su altre funzionalità in questa panoramica delle funzionalità."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="what-is-azure-search"></a>Che cos'è Azure ricerca?

Ricerca Azure è una soluzione di ricerca come servizio cloud che delega server e gestione dell'infrastruttura a Microsoft, lasciando visibili un servizio pronte per l'uso che è possibile inserire dati con i dati e quindi utilizzare per aggiungere Cerca nel web o applicazione per dispositivi mobili. Ricerca Azure consente di aggiungere facilmente affidabili funzionalità di ricerca alle applicazioni mediante una semplice [API REST](https://msdn.microsoft.com/library/azure/dn798935.aspx) o [.NET SDK](search-howto-dotnet-sdk.md) senza gestione dell'infrastruttura di ricerca o può diventare un esperto nella ricerca.

## <a name="give-your-users-a-powerful-search-experience"></a>Consentire agli utenti un'esperienza di ricerca potenti

Possibile formulare **potenti query** utilizzando la [sintassi della query semplice](https://msdn.microsoft.com/library/azure/dn798920.aspx), che offre gli operatori logici, gli operatori di ricerca di una frase, operatori suffisso, operatori di precedenza. Inoltre, la [sintassi della query Lucene](https://msdn.microsoft.com/library/azure/mt589323.aspx) possibile abilitare la ricerca sfocata, ricerca, ottimizzare termini e le espressioni regolari. Ricerca Azure supporta anche analisi lessicale personalizzati per consentire all'applicazione di gestire le query di ricerca complessa con corrispondenza-versione fonetica ed espressioni regolari.

**Supporto lingua** è [incluso per 56 lingue diverse](https://msdn.microsoft.com/library/azure/dn879793.aspx). Usa Lucene analisi e analisi di Microsoft (affinamento in base a anni del linguaggio naturale di elaborazione in Office e Bing), ricerca Azure consente di analizzare testo nella casella di ricerca dell'applicazione per gestire in maniera intelligente linguistica specifiche della lingua inclusi aggettivali, sesso, sostantivi plurali irregolari (ad esempio "mouse" e "mouse"), word di annullare la composizione, ritorno a capo (per le lingue senza spazi) e così via.

**Suggerimenti per la ricerca** può essere abilitata per query di ricerca incrementale e barre di ricerca di completamento automatico. [Vengono suggeriti i documenti effettiva dell'indice di](https://msdn.microsoft.com/library/azure/dn798936.aspx) come utenti immettere ricerca parziale di input.

**Evidenziazione dei risultati** [consente](https://msdn.microsoft.com/library/azure/dn798927.aspx) agli utenti di visualizzare il frammento di testo in ogni risultato che contiene le corrispondenze per le query. È possibile scegliere i campi che restituiscono frammenti evidenziati.

**Spostamento in base a facet** facilmente viene aggiunta alla pagina dei risultati di ricerca con Azure ricerca. Usa [solo un singolo parametro di query](https://msdn.microsoft.com/library/azure/dn798927.aspx), ricerca Azure restituirà tutte le informazioni necessarie per creare un'esperienza di ricerca in base a facet nell'interfaccia utente dell'applicazione per consentire agli utenti di drill-down e filtrare i risultati di ricerca (ad esempio filtro elementi catalogo prezzo intervallo o marchio).

Supporto **geografico spaziale** consente di elaborare in maniera intelligente, filtrare e visualizzare aree geografiche. Ricerca Azure consente agli utenti di esplorare i dati in base a prossimità di un risultato di ricerca in una posizione specificata o in base a una specifica località geografica. Questo video viene spiegato come funziona: [Channel 9: i dati geospaziali e ricerca di Azure](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data).

**Filtri** possono essere utilizzati per facilmente incorporare in base a facet spostamento dell'interfaccia utente dell'applicazione, migliorare la formulazione di query e filtro in base a su specificato dall'utente o sviluppo-criteri. Creare filtri potenti utilizzando la [sintassi di OData](https://msdn.microsoft.com/library/azure/dn798921.aspx).

## <a name="empower-your-developers-with-an-easy-to-use-service"></a>Consentire agli sviluppatori con un servizio di semplice utilizzo

**Disponibilità** garantisce un'esperienza di servizio di ricerca estremamente affidabile. Quando ridimensionate correttamente, [ricerca Azure offre un contratto di servizio 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Non **completamente gestiti** come soluzione-to-end, ricerca Azure richiede assolutamente gestione dell'infrastruttura. Il servizio può essere facilmente personalizzato per le proprie esigenze ridimensionando in due dimensioni di gestire ulteriore spazio di archiviazione di documenti, superiore carichi di query o entrambe.

**Integrazione dei dati** di utilizzo [degli indicizzatori](https://msdn.microsoft.com/library/azure/dn946891.aspx) consente Azure ricerca per indicizzazione automaticamente il Database di SQL Azure, DocumentDB Azure o [archiviazione Blob Azure](search-howto-indexing-azure-blob-storage.md) per sincronizzare il contenuto dell'indice di ricerca con l'archivio dati primario.

**Identificazione del documento** è disponibile (attualmente nella versione di anteprima) [per leggere e indicizzare formati di file principali](search-howto-indexing-azure-blob-storage.md) inclusi Microsoft Office, nonché documenti PDF e HTML.

**Analitica il traffico di ricerca** sono [facilmente raccolti e analizzati](search-traffic-analytics.md) sbloccare approfondimenti da quali utenti stanno scrivendo nella casella di ricerca.

**Punteggio semplice** è dei principali vantaggi della ricerca di Azure. [I profili di classificazione](https://msdn.microsoft.com/library/azure/dn798928.aspx) vengono utilizzate per consentire alle organizzazioni di pertinenza modello come funzione di valori nei documenti stessi. Ad esempio, è consigliabile prodotti più recenti o scontato prodotti per alto nei risultati della ricerca. È anche possibile creare profili punteggio uso di tag per il punteggio personalizzate in base alle preferenze di ricerca dei clienti sono stati registrati e archiviati separatamente.

**Ordinamento** è disponibile per più campi tramite lo schema di indice e quindi attivata o disattivata in fase di query con un solo parametro di ricerca.

**Spostamento** e la limitazione per i risultati della ricerca è un'operazione [semplice con quella del controllo ottimizzata](search-pagination-page-layout.md) che ricerca Azure offre sopra i risultati della ricerca.  

**Soluzioni di ricerca** consente di inviare query relative a tutti gli indici destra dal portale di Azure dell'account in modo che sia possibile testare le query e perfezionare i profili punteggio.

## <a name="how-it-works"></a>Come funziona

### <a name="1-provision-service"></a>1. servizio di provisioning
È possibile ruotare di un servizio di ricerca di Azure tramite il [Portale di Azure](https://portal.azure.com/) o l' [API di gestione delle risorse di Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx).

A seconda di come si configura il servizio di ricerca, si utilizzeranno scegliere il livello gratuito del servizio condiviso con altri server di sottoscrizione di ricerca di Azure o una [pagato livello](https://azure.microsoft.com/pricing/details/search/) che riserva risorse da utilizzare solo dal servizio. Durante il provisioning del servizio, è anche possibile selezionare l'area dell'interfaccia di dati che ospita il servizio.

A seconda di quale livello di servizio scelto, è possibile ridimensionare il servizio in due dimensioni: 1) aggiungere repliche per migliorare le capacità di gestire carichi di query piena e 2) aggiungere partizioni per aggiungere spazio di archiviazione per più documenti. Gestendo separatamente velocità di query e lo spazio di archiviazione di documenti, è possibile personalizzare il servizio di ricerca in base alle esigenze.

### <a name="2-create-index"></a>2. creare l'indice
Prima del servizio di ricerca di Azure, è possibile caricare il contenuto, è necessario definire un indice di ricerca di Azure. Un indice è simile a una tabella di database contenente i dati che può accettare le query di ricerca. Definire lo schema di indice per eseguire il mapping alla struttura dei documenti che si desidera eseguire una ricerca, simili ai campi in un database.

Lo schema di tali indici può essere creato nel portale di Azure o a livello di programmazione [utilizzando .NET SDK](search-howto-dotnet-sdk.md) o [API REST](https://msdn.microsoft.com/library/azure/dn798941.aspx). Una volta definita l'indice, è possibile caricare quindi i dati al servizio di ricerca di Azure in cui è successivamente di indicizzazione.

### <a name="3-index-data"></a>3. i dati dell'indice
Dopo aver definito i campi e gli attributi dell'indice, si è pronti per caricare il contenuto in corrispondenza dell'indice. È possibile utilizzare un modello push o pull di caricare i dati per l'indice.

Il modello pull viene fornito tramite indicizzatori che possono essere configurati per su richiesta o aggiornamenti pianificati (vedere [le operazioni dell'indicizzatore (Azure ricerca servizio all'API REST)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), che consente di acquisire facilmente dati e modifica dei dati da un DocumentDB di Azure, Database SQL Azure, archiviazione Blob Azure o SQL Server ospitato in una macchina virtuale Azure.

Il modello push viene fornito tramite il SDK o API REST utilizzate per l'invio di documenti aggiornati a un indice. È possibile inserire i dati da qualsiasi set di dati utilizzando il formato JSON. Vedere [aggiungere, aggiornare, o eliminare documenti](https://msdn.microsoft.com/library/azure/dn798930.aspx) o [come utilizzare .NET SDK)](search-howto-dotnet-sdk.md) per indicazioni sul caricamento dei dati.

### <a name="4-search"></a>4. ricerca di
Dopo avere compilato l'indice di ricerca di Azure, è possibile ora [eseguire query di ricerca](https://msdn.microsoft.com/library/azure/dn798927.aspx) per l'endpoint del servizio di richieste HTTP semplice con l'API REST o .NET SDK.

## <a name="try-it-now-for-free"></a>Prova (gratuitamente!)
È possibile provare Azure ricerca oggi stesso! Se si dispone già di un account Azure, è possibile [disposizione un servizio gratuito livello](search-create-service-portal.md).

Se non si dispone di un account Azure che è possibile provare una sessione gratuita, 60 minuti senza iscriversi necessari. Accedere al [Servizio di App Azure provare](http://go.microsoft.com/fwlink/p/?LinkId=618214) e selezionare "Web App". Quindi selezionare il modello "ASP.NET + Azure Search" per iniziare.
