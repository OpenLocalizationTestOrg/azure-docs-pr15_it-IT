<properties
   pageTitle="Versioni di API di ricerca di Azure | Microsoft Azure | API di ricerca"
   description="Criteri di versione API REST di Azure ricerca e la libreria client in .NET SDK."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versioni di API di ricerca di Azure

Ricerca Azure transita fuori degli aggiornamenti delle caratteristiche a intervalli regolari. In alcuni casi, ma non sempre questi aggiornamenti richiedono di pubblicare una nuova versione dell'API per mantenere la compatibilità con le versioni precedenti. Pubblicazione di una nuova versione consente di controllare quando e come si integrano gli aggiornamenti del servizio di ricerca nel codice.

In genere, si tenta di pubblicare nuove versioni solo quando necessario, poiché può comportare alcune attività per aggiornare il codice per l'utilizzo di una nuova versione dell'API. Si verrà pubblicare una nuova versione solo se è necessario modificare alcuni aspetti dell'API in modo che le interruzioni di compatibilità con le versioni precedenti. Questo problema può verificarsi a causa di correzioni alle funzionalità esistenti o a causa di nuove caratteristiche che cambiano superficie API esistente.

Abbiamo seguire la stessa regola SDK aggiornamenti. Azure ricerca SDK segue le regole di [controllo delle versioni semantic](http://semver.org/) , ovvero la versione ha tre parti: principale, secondario e generare numero (ad esempio 1.1.0). Abbiamo sarà disponibile una nuova versione principale di SDK solo in caso di modifiche che interrompere la compatibilità con le versioni precedenti. Aggiornamenti caratteristica unificatori è aumenterà la versione secondaria e per correzioni di bug è aumenterà solo la versione build.

##<a name="snapshot-of-current-versions"></a>Snapshot delle versioni correnti 

Sotto è uno snapshot delle versioni corrente di tutte le interfacce di programmazione per la ricerca di Azure. Roadmap e altri dettagli disponibili nelle sezioni successive di questo documento.

Interfacce|Versione più recente|Stato
----------|-------------------------|------
[.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|Disponibile in genere rilasciato febbraio 2016
[Anteprima SDK .NET](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|anteprima 2.0|Anteprima, rilasciato agosto 2016
[API REST di servizio](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|In genere disponibili
[Anteprima di API REST di servizio](search-api-2015-02-28-preview.md)|Anteprima 28 di 02 2015|Anteprima
[Gestione API REST](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|In genere disponibili

Per le API REST, tra cui la `api-version` a ogni chiamata è necessario. Consente di utilizzare una versione specifica, ad esempio un'anteprima dell'API facilmente. Nell'esempio seguente viene illustrato come la `api-version` viene specificato:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Anche se ogni richiesta ha un `api-version`, è consigliabile utilizzare la stessa versione per tutte le richieste di API. Si tratta soprattutto quando le nuove versioni API introducono attributi o operazioni che non vengono riconosciute in versioni precedenti. Combinazione versioni API può avere conseguenze impreviste ed evitare.
> 
L'API REST di servizio e API REST gestione viene creata una versione indipendente tra loro. Le analogie tra i numeri di versione sono CO-danni incidentali.

Generalmente disponibile (o GA) API possono essere usate in produzione e sono soggetti a contratti di servizio Azure. Le versioni di anteprima hanno sperimentazione caratteristiche che non vengono migrati sempre a una versione GA. **È consigliabile contro utilizzando l'anteprima API nelle applicazioni di produzione.**

##<a name="sdk-version-roadmap"></a>Guida di orientamento alla versione SDK

Ogni versione di .NET SDK è destinato a una determinata versione dell'API REST di servizio. Caratteristiche sono riportate API REST prima di tutto e quindi implementate in SDK.

.NET SDK è ora disponibile in genere e lavoro è già in corso nella versione successiva. Nella tabella seguente è simile direttamente alle versioni future di SDK in modo da avere un'idea delle quali è impegni prossimi.

Versione di .NET SDK|Versione API REST|Caratteristiche|GRECO
----------------|----------------|--------|---
1.1|2015-02-28|Nella sintassi della query Lucene|Febbraio 2016
anteprima 2.0|Anteprima 28 di 02 2015|Analisi personalizzati, Blob Azure e indicizzatori tabella, mapping dei campi, ETag|Agosto 2016
2. x|Nuova versione dell'API GA|Diverso da quello di anteprima 2.0|Iniziali Q4 2016

##<a name="about-preview-and-generally-available-versions"></a>Informazioni sulle versioni di anteprima e generalmente disponibile

Ricerca Azure sempre pre-rilascia sperimentazione funzionalità tramite l'API REST prima di tutto, quindi tramite versioni non definitive di .NET SDK.

Funzionalità di anteprima non necessariamente essere eseguita la migrazione a una versione GA. Mentre in caratteristiche in una versione GA sono considerati stabile e in genere modificata ad eccezione della miglioramenti e correzioni di compatibilità small, Anteprima funzionalità sono disponibili per il testing e sperimentazione, con l'obiettivo di raccogliere commenti e suggerimenti sulle funzionalità progettazione e implementazione. 

Tuttavia, poiché la funzionalità Anteprima è soggetti a modifiche, è consigliabile contro la scrittura di codice di produzione che entrerà in una relazione in versioni precedenti. Se si utilizza una versione precedente di anteprima, è consigliabile eseguire la migrazione alla versione (GA) generalmente disponibile. 

.NET SDK: indicazioni per la migrazione di codice, vedere [aggiornare .NET SDK](search-dotnet-sdk-migration.md).

Disponibilità generale significa che Azure ricerca si trova sotto il contratto di servizio (contratto di servizio). Il contratto di servizio sono disponibili in [Azure ricerca i contratti di servizio](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

