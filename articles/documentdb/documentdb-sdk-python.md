<properties 
    pageTitle="API DocumentDB Python e SDK | Microsoft Azure" 
    description="Tutte le informazioni di Python API SDK e date di rilascio, pensionistico date e le modifiche apportate tra ogni versione di SDK Python DocumentDB." 
    services="documentdb" 
    documentationCenter="python" 
    authors="rnagpal" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="09/29/2016" 
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-python-api-and-sdk"></a>SDK e API DocumentDB Python

<table>
<tr><td>**Scaricare SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**Documentazione dell'API**</td><td>[Documentazione di riferimento API Python](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>
<tr><td>**Istruzioni di installazione SDK**</td><td>[Istruzioni relative all'installazione Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**Contribuiscono a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**Guida introduttiva**</td><td>[Guida introduttiva a SDK Python](documentdb-python-application.md)</td></tr>
<tr><td>**Piattaforme supportate corrente**</td><td>[Python 2.7](https://www.python.org/downloads/) e [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name200200httpspypipythonorgpypipydocumentdb200"></a><a name="2.0.0"/>[2.0.0](https://pypi.python.org/pypi/pydocumentdb/2.0.0)
- Aggiunta del supporto per Python 3.5.
- Aggiunta del supporto per il pool di connessioni tramite un modulo di richieste.
- Aggiunta del supporto per la coerenza sessione.
- Aggiunta del supporto per le query superiore/ORDERBY per raccolte partizionate.


### <a name="a-name190190httpspypipythonorgpypipydocumentdb190"></a><a name="1.9.0"/>[1.9.0](https://pypi.python.org/pypi/pydocumentdb/1.9.0)
- Supporto dei criteri di Riprova aggiunto per le richieste limitate. (Richieste accelerate venga generata un'eccezione troppo grande tasso richiesta, il codice di errore 429). Per impostazione predefinita, DocumentDB tentativi nove ore per ogni richiesta quando viene rilevato il codice di errore 429, rispettare il tempo di retryAfter nell'intestazione della risposta. Un intervallo di tempo fisso Riprova ora impostare come parte della proprietà RetryOptions sull'oggetto ConnectionPolicy se si vuole ignorare l'ora di retryAfter restituita dal server tra i tentativi. DocumentDB ora attesa per un massimo di 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questa volta può anche essere esegue l'override nella proprietà RetryOptions ConnectionPolicy oggetto.

- DocumentDB viene ora restituita x-ms-velocità--numero di tentativi e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare la limitazione Riprova conta. numeri e l'ora cummulative la richiesta di attesa tra i tentativi.

- Rimossa la classe RetryPolicy e la proprietà corrispondente (retry_policy) esposta nella classe document_client e invece introdotta una classe RetryOptions esposizione della proprietà RetryOptions nella classe ConnectionPolicy che può essere utilizzato per ignorare alcune delle opzioni Riprova predefinito.

### <a name="a-name180180httpspypipythonorgpypipydocumentdb180"></a><a name="1.8.0"/>[1.8.0](https://pypi.python.org/pypi/pydocumentdb/1.8.0)
  - Il supporto per gli account di database più aree.

### <a name="a-name170170httpspypipythonorgpypipydocumentdb170"></a><a name="1.7.0"/>[1.7.0](https://pypi.python.org/pypi/pydocumentdb/1.7.0)
- Il supporto per volta a Live(TTL) caratteristica per i documenti.

### <a name="a-name161161httpspypipythonorgpypipydocumentdb161"></a><a name="1.6.1"/>[1.6.1](https://pypi.python.org/pypi/pydocumentdb/1.6.1)
- Correzioni di bug correlate al server lato partizione per consentire i caratteri speciali nel percorso partitionkey.

### <a name="a-name160160httpspypipythonorgpypipydocumentdb160"></a><a name="1.6.0"/>[1.6.0](https://pypi.python.org/pypi/pydocumentdb/1.6.0)
- Implementato [raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definite dall'utente](documentdb-performance-levels.md). 

### <a name="a-name150150httpspypipythonorgpypipydocumentdb150"></a><a name="1.5.0"/>[1.5.0](https://pypi.python.org/pypi/pydocumentdb/1.5.0)
- Aggiungi Hash & intervallo partizione sistemi di risoluzione per assistenza con le applicazioni di sharding su più partizioni.

### <a name="a-name142142httpspypipythonorgpypipydocumentdb142"></a><a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- Implementare Upsert. Nuovi metodi UpsertXXX aggiunti per supportare funzionalità Upsert.
- Implementare il Routing ID in base a. Nessuna modifica API pubblica, tutte le revisioni interne.

### <a name="a-name120120httpspypipythonorgpypipydocumentdb120"></a><a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- Indice geospaziali supporta.
- Convalida proprietà id per tutte le risorse. ID per le risorse non possono contenere ?, /, # \, caratteri o terminare con uno spazio.
- Aggiunge nuovi intestazione "indice trasformazione lo stato di avanzamento" ResourceResponse.

### <a name="a-name110110httpspypipythonorgpypipydocumentdb110"></a><a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- Implementato V2 criterio di indicizzazione.

### <a name="a-name101101httpspypipythonorgpypipydocumentdb101"></a><a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- Supporta la connessione proxy.

### <a name="a-name100100httpspypipythonorgpypipydocumentdb100"></a><a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK.

## <a name="release--retirement-dates"></a>Date di rilascio e ritiro dei
Microsoft fornirà notifica almeno **12 mesi** prima ritirare un SDK per ridurre la transizione a una versione più recente/supportati.

Nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunti solo a SDK corrente, pertanto è consigliabile sempre eseguire l'aggiornamento alla versione più recente SDK il prima possibile. 

Qualsiasi richiesta di DocumentDB utilizzando un SDK ritirati verrà rifiutato dal servizio.

> [AZURE.WARNING]
Tutte le versioni di Azure DocumentDB SDK per Python prima versione **1.0.0** verranno ritirate su **29 febbraio 2016**. 

<br/>

| Versione | Data di rilascio | Data pensionistico 
| ---     | ---          | ---
| [2.0.0](#2.0.0) | 29 settembre 2016 |---
| [1.9.0](#1.9.0) | 07 luglio 2016 |---
| [1.8.0](#1.8.0) | 14 giugno 2016 |---
| [1.7.0](#1.7.0) | 26 aprile 2016 |---
| [1.6.1](#1.6.1) | 08 aprile 2016 |---
| [1.6.0](#1.6.0) | 29 marzo 2016 |---
| [1.5.0](#1.5.0) | 03 gennaio 2016 |---
| [1.4.2](#1.4.2) | 06 ottobre 2015 |---
| [1.4.1](#1.4.1) | 06 ottobre 2015 |---
| [1.2.0](#1.2.0) | 06 agosto 2015 |---
| [1.1.0](#1.1.0) | 09 luglio 2015 |---
| [1.0.1](#1.0.1) | 25 maggio 2015 |---
| [1.0.0](#1.0.0) | 07 aprile 2015 |---
| 0.9.4-prelease | 14 gennaio 2015 | 29 febbraio 2016
| 0.9.3-prelease | 09 dicembre 2014 | 29 febbraio 2016
| 0.9.2-prelease | 25 novembre 2014 | 29 febbraio 2016
| 0.9.1-prelease | 23 settembre 2014 | 29 febbraio 2016
| 0.9.0-prelease | 21 agosto 2014 | 29 febbraio 2016

## <a name="faq"></a>Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni su DocumentDB, vedere [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) servizio pagina. 
