
<properties
    pageTitle="API DocumentDB Java & SDK | Microsoft Azure"
    description="Tutte le informazioni di API Java SDK e date di rilascio, pensionistico date e le modifiche apportate tra ogni versione di SDK linguaggio DocumentDB."
    services="documentdb"
    documentationCenter="java"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## <a name="documentdb-java-api-and-sdk"></a>SDK e API DocumentDB Java

<table>
<tr><td>**Download SDK**</td><td>[Maven](http://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)</td></tr>
<tr><td>**Documentazione dell'API**</td><td>[Documentazione di riferimento API Java](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**Contribuiscono a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**Guida introduttiva**</td><td>[Guida introduttiva a SDK Java](documentdb-java-application.md)</td></tr>
<tr><td>**Runtime supportati corrente**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Note sulla versione

### <a name="a-name191191httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb191"></a><a name="1.9.1"/>[1.9.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.1)

  - Aggiunta del supporto per il livello di coerenza BoundedStaleness.
  - Aggiunta del supporto per collegamento diretto per le operazioni CRUD per raccolte partizionate.
  - Correzione di un problema di query su un database con SQL.
  - Correzione di un problema nella cache della sessione nel punto in cui i token di sessione può essere impostato in modo non corretto.

### <a name="a-name190190httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb190"></a><a name="1.9.0"/>[1.9.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.9.0)

  - Aggiunta del supporto per cross query parallele partizione.
  - Aggiunta del supporto per le query superiore/ORDER BY per raccolte partizionate.
  - Aggiunta del supporto per la coerenza sicura.
  - Aggiunta del supporto per le richieste di nomi in base a quando si utilizza la connettività diretta.
  - Corretta rendere ActivityId rimanere coerente in tutti i tentativi di richiesta.
  - Correzione di un problema correlato alla cache di sessione quando ricreazione di una raccolta con lo stesso nome.
  - Aggiunte poligono e LineString DataTypes durante la specifica raccolta indicizzazione criteri per le query spaziale recinzione geografico.
  - Problemi risolti con linguaggio Doc per Java 1.8.

### <a name="a-name181181httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb181"></a><a name="1.8.1"/>[1.8.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.1)
  - Correzione di un problema in PartitionKeyDefinitionMap per memorizzare nella cache raccolte singola partizione e non recupero aggiuntiva suddividere le richieste di codice.
  - Correzione di un problema tentativo non quando viene fornito un valore di chiave partizione non corretti.

### <a name="a-name180180httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb180"></a><a name="1.8.0"/>[1.8.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.8.0)
  - Il supporto per gli account di database più aree.
  - Aggiunta del supporto per un tentativo automatico alle richieste accelerate con le opzioni per personalizzare il numero massimo di tentativi e il tempo di attesa massimo di tentativi.  Vedere RetryOptions e ConnectionPolicy.getRetryOptions().
  - Codice di partizione personalizzato basato su IPartitionResolver obsoleto. Utilizzare raccolte partizionate per costi più elevati lo spazio di archiviazione e la velocità di trasmissione.

### <a name="a-name171171httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb171"></a><a name="1.7.1"/>[1.7.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.1)
- Riprova aggiunto il supporto di criteri per la limitazione.  

### <a name="a-name170170httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb170"></a><a name="1.7.0"/>[1.7.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.7.0)
- Maggiore tempo di assistenza in tempo reale (TTL) per i documenti.

### <a name="a-name160160httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb160"></a><a name="1.6.0"/>[1.6.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.6.0)
- Implementato [raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definite dall'utente](documentdb-performance-levels.md).

### <a name="a-name151151httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb151"></a><a name="1.5.1"/>[1.5.1](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.1)
- Correzione di un problema in HashPartitionResolver generare valori hash in littleEndian siano coerenti con altri SDK.

### <a name="a-name150150httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb150"></a><a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- Aggiungi Hash & intervallo partizione sistemi di risoluzione per assistenza con le applicazioni di sharding su più partizioni.

### <a name="a-name140140httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb140"></a><a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- Implementare Upsert. Nuovi metodi upsertXXX aggiunti per supportare funzionalità Upsert.
- Implementare il Routing ID in base a. Nessuna modifica API pubblica, tutte le revisioni interne.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
- Versione saltata per anticipare numero di versione allineamento con altri SDK

### <a name="a-name120120httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb120"></a><a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- Supporta geospaziali indice
- Convalida proprietà id per tutte le risorse. ID per le risorse non possono contenere ?, /, # \, caratteri o terminare con uno spazio.
- Aggiunge nuovi intestazione "indice trasformazione lo stato di avanzamento" ResourceResponse.

### <a name="a-name110110httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb110"></a><a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- I criteri di indicizzazione V2 implementati

### <a name="a-name100100httpmvnrepositorycomartifactcommicrosoftazureazure-documentdb100"></a><a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## <a name="release--retirement-dates"></a>Rilascio e ritiro dei date
Microsoft fornirà notifica almeno **12 mesi** prima ritirare un SDK per ridurre la transizione a una versione più recente/supportati.

Nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunti solo a SDK corrente, pertanto è consigliabile sempre eseguire l'aggiornamento alla versione più recente SDK il prima possibile.

Qualsiasi richiesta di DocumentDB utilizzando un SDK ritirati verrà rifiutato dal servizio.

> [AZURE.WARNING]
Tutte le versioni di Azure DocumentDB SDK per Java prima versione **1.0.0** verranno ritirate su **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data pensionistico
| ---     | ---          | ---
| [1.9.1](#1.9.1) | 28 ottobre 2016 |---
| [1.9.0](#1.9.0) | 03 ottobre 2016 |---
| [1.8.1](#1.8.1) | 30 giugno 2016 |---
| [1.8.0](#1.8.0) | 14 giugno 2016 |---
| [1.7.1](#1.7.1) | 30 aprile 2016 |---
| [1.7.0](#1.7.0) | 27 aprile 2016 |---
| [1.6.0](#1.6.0) | 29 marzo 2016 |---
| [1.5.1](#1.5.1) | 31 dicembre 2015 |---
| [1.5.0](#1.5.0) | 04 dicembre 2015 |---
| [1.4.0](#1.4.0) | 05 ottobre 2015 |---
| [1.3.0](#1.3.0) | 05 ottobre 2015 |---
| [1.2.0](#1.2.0) | 05 agosto 2015 |---
| [1.1.0](#1.1.0) | 09 luglio 2015 |---
| [1.0.1](#1.0.1) | 12 maggio 2015 |---
| [1.0.0](#1.0.0) | 07 aprile 2015 |---
| 0.9.5-prelease | 09 marzo 2015 | 29 febbraio 2016
| 0.9.4-prelease | 17 febbraio 2015 | 29 febbraio 2016
| 0.9.3-prelease | 13 gennaio 2015 | 29 febbraio 2016
| 0.9.2-prelease | 19 dicembre 2014 | 29 febbraio 2016
| 0.9.1-prelease | 19 dicembre 2014 | 29 febbraio 2016
| 0.9.0-prelease | 10 dicembre 2014 | 29 febbraio 2016

## <a name="faq"></a>Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni su DocumentDB, vedere [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) servizio pagina.
