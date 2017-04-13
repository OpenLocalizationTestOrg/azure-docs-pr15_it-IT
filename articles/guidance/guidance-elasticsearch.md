
<properties
   pageTitle="Elasticsearch indicazioni Azure | Microsoft Azure"
   description="Elasticsearch indicazioni Azure."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="bennage"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="masashin"/>

# <a name="elasticsearch-on-azure-guidance"></a>Elasticsearch indicazioni Azure 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Elasticsearch è un motore di ricerca Apri origine scalabilità e un database. È utile per situazioni che richiedono il rilevamento delle informazioni contenute in grande set di dati e analisi rapida. Questa guida esamina alcuni aspetti da considerare quando si progetta un cluster di Elasticsearch, con l'obiettivo di modi per ottimizzare e testare la configurazione.

> [AZURE.NOTE]Questa guida presuppone familiarità base con Elasticsearch. Per ulteriori informazioni, visitare il [sito Web Elasticsearch](https://www.elastic.co/products/elasticsearch). 

- **[Elasticsearch in esecuzione in Azure][]** fornisce una breve introduzione alla struttura generale del Elasticsearch e viene descritto come implementare un cluster di Elasticsearch con Azure. 
- **[Prestazioni di acquisizione di dati di regolazione per Elasticsearch su Azure][]** viene descritta la distribuzione di un cluster di Elasticsearch e fornisce un'analisi approfondita delle diverse opzioni di configurazione da considerare quando si prevede un valore elevato di acquisizione di dati.
- **[Aggregazione di ottimizzazione dei dati e alle prestazioni delle query per Elasticsearch su Azure][]** fornisce un'analisi approfondita delle opzioni da considerare quando decidere come ottimizzare il sistema per le prestazioni di query e ricerca.
- **[Configurazione di adattabilità e il ripristino in Elasticsearch in Azure][]** descrive alcune importanti caratteristiche di un cluster di Elasticsearch che consente di ridurre al minimo le possibilità di perdita di dati e gli orari di recupero dati "Extended".
- **[Creazione di un ambiente di testing delle prestazioni per Elasticsearch su Azure][]** viene descritto come configurare un ambiente di testing l'acquisizione di dati delle prestazioni e carichi di lavoro di query in un cluster di Elasticsearch. 
- **[Implementare un JMeter test piano per Elasticsearch][]** sono riepilogate in esecuzione test implementato utilizzando piani di test JMeter insieme linguaggio codice incorporato come test JUnit per l'esecuzione di attività, ad esempio il caricamento di dati nel cluster Elasticsearch.
- **[Distribuzione di una prova di JMeter JUnit per la verifica delle prestazioni Elasticsearch][]** viene descritto come creare e utilizzare una prova di JUnit che può generare e caricare dati in un cluster di Elasticsearch. Si tratta di un approccio altamente flessibile per caricare il test in grado di generare grandi quantità di dati di test senza a seconda di file di dati esterni. 
- **[Esecuzione di test di adattabilità Elasticsearch automatizzati][]** vengono riepilogate le modalità eseguire i test sulla resilienza utilizzati in questa serie. 
- **[Esecuzione di test di prestazioni Elasticsearch automatizzati][]** vengono riepilogate le modalità eseguire il test utilizzati in questa serie.


[Elasticsearch in esecuzione in Azure]: guidance-elasticsearch-running-on-azure.md
[Ottimizzazione delle prestazioni di acquisizione di dati per Elasticsearch su Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[La creazione di un ambiente di Testing per Elasticsearch su Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementazione di un piano di Test JMeter per Elasticsearch]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Distribuzione di una prova di JMeter JUnit per il test delle prestazioni Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ottimizzazione aggregazione dei dati e alle prestazioni delle Query per Elasticsearch su Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[La configurazione adattabilità e ripristino su Elasticsearch su Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Esecuzione di test di adattabilità Elasticsearch automatica]: guidance-elasticsearch-running-automated-resilience-tests.md
[Esecuzione di test Elasticsearch automatica]: guidance-elasticsearch-running-automated-performance-tests.md
