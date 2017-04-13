<properties
    pageTitle="API DocumentDB Node & SDK | Microsoft Azure"
    description="Tutte le informazioni di API Node SDK e date di rilascio, pensionistico date e le modifiche apportate tra ogni versione di SDK Node DocumentDB."
    services="documentdb"
    documentationCenter="nodejs"
    authors="rnagpal"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="rnagpal"/>

# <a name="documentdb-apis-and-sdks"></a>API DocumentDB e SDK

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [RESTO](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

##<a name="documentdb-nodejs-api-and-sdk"></a>SDK e API DocumentDB Node

<table>
<tr><td>**Scaricare SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>
<tr><td>**Documentazione dell'API**</td><td>[Documentazione di riferimento Node API](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>
<tr><td>**Istruzioni di installazione SDK**</td><td>[Istruzioni per l'installazione](http://azure.github.io/azure-documentdb-node/)</td></tr>
<tr><td>**Contribuiscono a SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>
<tr><td>**Esempi**</td><td>[Esempi di codice Node](documentdb-nodejs-samples.md)</td></tr>
<tr><td>**Esercitazione introduttiva Get**</td><td>[Guida introduttiva a SDK Node](documentdb-nodejs-get-started.md)</td></tr>
<tr><td>**Esercitazione app Web**</td><td>[Creare un'applicazione web Node utilizzando DocumentDB](documentdb-nodejs-application.md)</td></tr>
<tr><td>**Piattaforme supportate corrente**</td><td>[V0.10 Node](https://nodejs.org/en/blog/release/v0.10.0/)<br/>[V0.12 Node](https://nodejs.org/en/blog/release/v0.12.0/)<br/>[V4.2.0 Node](https://nodejs.org/en/blog/release/v4.2.0/)</td></tr>
</table></br>

##<a name="release-notes"></a>Note sulla versione

###<a name="1.10.0"/>1.10.0</a>

- Aggiunta del supporto per cross query parallele partizione.
- Aggiunta del supporto per le query superiore/ORDER BY per raccolte partizionate.

###<a name="1.9.0"/>1.9.0</a>

- Supporto dei criteri di Riprova aggiunto per le richieste limitate. (Richieste accelerate venga generata un'eccezione troppo grande tasso richiesta, il codice di errore 429). Per impostazione predefinita, DocumentDB tentativi nove ore per ogni richiesta quando viene rilevato il codice di errore 429, rispettare il tempo di retryAfter nell'intestazione della risposta. Un intervallo di tempo fisso Riprova ora impostare come parte della proprietà RetryOptions sull'oggetto ConnectionPolicy se si vuole ignorare l'ora di retryAfter restituita dal server tra i tentativi. DocumentDB ora attesa per un massimo di 30 secondi per ogni richiesta che viene limitata (indipendentemente dal numero di tentativi) e restituisce la risposta con il codice di errore 429. Questa volta può anche essere esegue l'override nella proprietà RetryOptions ConnectionPolicy oggetto.

- DocumentDB viene ora restituita x-ms-velocità--numero di tentativi e x-ms-throttle-retry-wait-time-ms come intestazioni di risposta in ogni richiesta per indicare la limitazione Riprova conta. numeri e l'ora cummulative la richiesta di attesa tra i tentativi.

- È stata aggiunta la classe RetryOptions, esposizione della proprietà RetryOptions sulla classe ConnectionPolicy che può essere utilizzata per ignorare alcune delle opzioni Riprova predefinito.

###<a name="1.8.0"/>1.8.0</a>

 - Il supporto per gli account di database più aree.

###<a name="1.7.0"/>1.7.0</a>

- Il supporto per volta a Live(TTL) caratteristica per i documenti.

###<a name="1.6.0"/>1.6.0</a>

- Implementato [raccolte partizionate](documentdb-partition-data.md) e [livelli di prestazioni definite dall'utente](documentdb-performance-levels.md).

###<a name="1.5.6"/>1.5.6</a>

- Bug RangePartitionResolver.resolveForRead corretto nel punto in cui non è stato restituisce collegamenti a causa di un concatena errata dei risultati.

###<a name="1.5.5"/>1.5.5</a>

- Fisso hashParitionResolver resolveForRead(): quando nessun tasto partizione fornito è stata eccezioni, anziché la restituzione di un elenco di tutti i collegamenti registrati.

###<a name="1.5.4"/>1.5.4</a>

- Risoluzioni emettere [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - agente HTTPS dedicato: evitare di modificare l'agente globale ai fini della DocumentDB. Utilizzare un agente dedicato per tutte le richieste di raccolta.

###<a name="1.5.3"/>1.5.3</a>

- Risoluzioni problemi [81 #](https://github.com/Azure/azure-documentdb-node/issues/81) - correttamente quadratino trattini ID di elementi multimediali.

###<a name="1.5.2"/>1.5.2</a>

- Risoluzioni problemi [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter comunicare ascoltatore una perdita di avviso.

###<a name="1.5.1"/>1.5.1</a>

- Risoluzioni problemi [92 #](https://github.com/Azure/azure-documentdb-node/issues/90) - Rinomina cartella Hash eseguire l'hashing per sistemi tra maiuscole e minuscole.

### <a name="1.5.0"/>1.5.0</a>

- Supporto di implementare sharding aggiungendo hash & intervallo sistemi di risoluzione partizione.

### <a name="1.4.0"/>1.4.0</a>

- Implementare Upsert. I nuovi metodi upsertXXX su documentClient.

### <a name="1.3.0"/>1.3.0</a>

- Saltare per visualizzare i numeri di versione in allineamento con altri SDK.

### <a name="1.2.2"/>1.2.2</a>

- Domande di divisione garantisce spaziale all'archivio di nuovo.
- Aggiornare il file di pacchetto per npm del Registro di sistema.

### <a name="1.2.1"/>1.2.1</a>

- Implementa ID Routing basato sul.
- Consente di correggere problema [49 #](https://github.com/Azure/azure-documentdb-node/issues/49) - corrente proprietà in conflitto con metodo Current ().

### <a name="1.2.0"/>1.2.0</a>

- Aggiunta del supporto per geospaziali indice.
- Convalida proprietà id per tutte le risorse. ID per le risorse non può contenere?, /, #, #47; & #47; caratteri o terminare con uno spazio.
- Aggiunge nuovi intestazione "indice trasformazione lo stato di avanzamento" ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>

- Implementato V2 criterio di indicizzazione.

### <a name="1.0.3"/>1.0.3</a>

- Problema [#40] (https://github.com/Azure/azure-documentdb-node/issues/40) - implementata eslint e noioso configurazioni di base e le potenzialità SDK.

### <a name="1.0.2"/>1.0.2</a>

- Problema [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - spaziale grado non include l'intestazione con errore.

### <a name="1.0.1"/>1.0.1</a>

- Implementato possibilità di query per i conflitti aggiungendo readConflicts, readConflictAsync e queryConflicts.
- Documentazione API aggiornata.
- Problema [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync errore.

### <a name="1.0.0"/>1.0.0</a>

- GA SDK.

## <a name="release--retirement-dates"></a>Rilascio e ritiro dei date
Microsoft fornirà notifica almeno **12 mesi** prima ritirare un SDK per ridurre la transizione a una versione più recente/supportati.

Nuove caratteristiche e funzionalità e le ottimizzazioni vengono aggiunti solo a SDK corrente, pertanto è consigliabile sempre eseguire l'aggiornamento alla versione più recente SDK il prima possibile.

Qualsiasi richiesta di DocumentDB utilizzando un SDK ritirati verrà rifiutato dal servizio.

> [AZURE.WARNING]
Tutte le versioni di Azure DocumentDB SDK per Node prima versione **1.0.0** verranno ritirate su **29 febbraio 2016**.

<br/>

| Versione | Data di rilascio | Data pensionistico
| ---     | ---          | ---
| [1.10.0](#1.10.0) | 03 ottobre 2016 |---
| [1.9.0](#1.9.0) | 07 luglio 2016 |---
| [1.8.0](#1.8.0) | 14 giugno 2016 |---
| [1.7.0](#1.7.0) | 26 aprile 2016 |---
| [1.6.0](#1.6.0) | 29 marzo 2016 |---
| [1.5.6](#1.5.6) | 08 marzo 2016 |---
| [1.5.5](#1.5.5) | 02 febbraio 2016 |---
| [1.5.4](#1.5.4) | 01 febbraio 2016 |---
| [1.5.2](#1.5.2) | 26 gennaio 2016 |---
| [1.5.2](#1.5.2) | 22 gennaio 2016 |---
| [1.5.1](#1.5.1) | 4 gennaio 2016 |---
| [1.5.0](#1.5.0) | 31 dicembre 2015 |---
| [1.4.0](#1.4.0) | 06 ottobre 2015 |---
| [1.3.0](#1.3.0) | 06 ottobre 2015 |---
| [1.2.2](#1.2.2) | 10 settembre 2015 |---
| [1.2.1](#1.2.1) | 15 agosto 2015 |---
| [1.2.0](#1.2.0) | 05 agosto 2015 |---
| [1.1.0](#1.1.0) | 09 luglio 2015 |---
| [1.0.3](#1.0.3) | 04 giugno 2015 |---
| [1.0.2](#1.0.2) | 23 maggio 2015 |---
| [1.0.1](#1.0.1) | 15 maggio 2015 |---
| [1.0.0](#1.0.0) | 08 aprile 2015 |---
| 0.9.4-prerelease | 06 aprile 2015 | 29 febbraio 2016
| 0.9.3-prerelease | 14 gennaio 2015 | 29 febbraio 2016
| 0.9.2-prerelease | 18 dicembre 2014 | 29 febbraio 2016
| 0.9.1-prerelease | 22 agosto 2014 | 29 febbraio 2016
| 0.9.0-prerelease | 21 agosto 2014 | 29 febbraio 2016


## <a name="faq"></a>Domande frequenti
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni su DocumentDB, vedere [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) servizio pagina.
