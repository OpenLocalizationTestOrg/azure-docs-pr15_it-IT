<properties 
    pageTitle="L'indicizzazione automatica nella DocumentDB | Microsoft Azure" 
    description="Informazioni sulle funzioni di indicizzazione automatici nella DocumentDB Azure." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="mimig" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="arramac"/>
    
# <a name="automatic-indexing-in-azure-documentdb"></a>L'indicizzazione nella Azure DocumentDB automatico

Questo articolo è stato estratto da paper ["indipendente dal Schema indicizzazione con Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) , che verrà visualizzato alla [Conferenza interno 41a nel database di grandi dimensioni molto](http://www.vldb.org/2015/) tra agosto 31-4 settembre 2015, e una presentazione come indicizzazione works in Azure DocumentDB. 

Dopo aver letto questa operazione, verrà rispondono alle domande seguenti:

- Come DocumentDB dedurre lo schema da un documento JSON?
- Come DocumentDB creare un indice in tutti i documenti diversi?
- Come DocumentDB eseguire indicizzazione automatica scala?

##<a id="HowDocumentDBIndexingWorks"></a>Funzionamento dell'indicizzazione DocumentDB

[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) è uno scopo del database gratuiti schema progettato per JSON. Non aspettarsi o richiedere le definizioni di schema o indice secondario indicizzare i dati in scala. In questo modo è possibile definire e sui modelli di dati dell'applicazione tramite DocumentDB di scorrere rapidamente. Mentre si aggiungono i documenti a una raccolta, DocumentDB indicizza automaticamente tutte le proprietà del documento in modo che siano disponibili per la query. L'indicizzazione automatica consente di archiviare i documenti che appartengono a schemi completamente non autorizzati senza preoccuparsi di schemi o gli indici secondari.

Con l'obiettivo per eliminare la mancata corrispondenza tra il database e i modelli di programmazione di applicazioni, DocumentDB sfruttare la semplicità di JSON e l'assenza di una specifica dello schema. Non fa distinzione tra relativi ai documenti e consente di documenti all'interno di un insieme di DocumentDB per variare nello schema, oltre ai valori specifica istanza. A differenza di altri database documento motore di database di DocumentDB opera direttamente al livello di grammatica JSON, rimanente indipendente dal concetto di uno schema di documento e sfocatura bordo tra i valori di struttura e istanza di documenti. Questo, a sua volta, consente alle indice automaticamente i documenti senza schema o gli indici secondari.

L'indicizzazione in DocumentDB consente di sfruttare il fatto che grammatica JSON consente di essere **rappresentato come alberi di**documenti. Per un documento JSON essere rappresentato come una struttura ad albero, è necessario creare che parents il resto dei nodi effettivi del documento di sotto di un nodo principale fittizio. Ogni etichetta inclusi gli indici di matrice in un documento JSON diventa un nodo della struttura. La figura seguente illustra un esempio di documento JSON e rappresentazione albero corrispondente.

>[AZURE.NOTE] Poiché JSON sia Auto descrittivo vale a dire ogni documento include lo schema (metadati) e i dati, ad esempio `{"locationId": 5, "city": "Moscow"}` , vengono visualizzate sono presenti due proprietà `locationId` e `city`, e che abbiano un valore numerico e stringa valori di proprietà. DocumentDB è in grado di dedurre lo schema dei documenti ed eseguirne l'indicizzazione quando vengono inseriti o sostituiti, senza dover mai definire schemi o gli indici secondari.


**Documenti JSON come alberi di:**

![Come alberi di documenti](media/documentdb-indexing/DocumentsAsTrees.png)

Ad esempio, nell'esempio riportato sopra:

- La proprietà JSON `{"headquarters": "Belgium"}` proprietà nell'esempio precedente corrisponde al percorso/headquarters/Belgio.
- Matrice JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corrisponde ai percorsi `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

Con l'indicizzazione, (1) automatico ogni percorso tra la struttura del documento verrà indicizzato (a meno che lo sviluppatore è configurato in modo esplicito i criteri di indicizzazione per escludere alcuni motivi percorso). (2) ogni aggiornamento di un documento in una raccolta di DocumentDB dei clienti potenziali per l'aggiornamento della struttura dell'indice (ad esempio cause aggiunta o rimozione dei nodi). Uno dei requisiti principali dell'indicizzazione automatica dei documenti è per assicurarsi che il costo per l'indice e un documento con annidamento struttura della query, ad esempio 10 livelli, è uguale a quello di un documento JSON flat composta da coppie di parole chiave valore un solo livello completa. Di conseguenza una rappresentazione di percorso normalizzato è la base su cui vengono creati entrambi sottosistemi automatici di indicizzazione e query.

Un implicazioni importanti di trattare entrambi i valori di schema e istanza in modo uniforme in termini di percorsi è che, in modo logico come singoli documenti, un indice di due documenti visualizzati che tiene un mapping tra percorsi e gli ID documento contenente tale percorso possono anche essere rappresentati come una struttura. DocumentDB Usa questo fatto per creare una struttura di indice che viene creata dall'unione di tutte le strutture che rappresenta i singoli documenti all'interno della raccolta. La struttura dell'indice in raccolte DocumentDB aumenta nel tempo con i nuovi documenti ottenere aggiunto o aggiornati alla raccolta.


**Indice DocumentDB come una struttura:**

![Indice in una struttura](media/documentdb-indexing/IndexAsTree.png)

Nonostante siano gratuiti schema, SQL e JavaScript del DocumentDB query lingue fornire previsioni relazionale e filtri, lo spostamento gerarchico nei documenti, operazioni spaziale e chiamata di funzioni definite dall'utente interamente scritta in JavaScript. Fase di esecuzione query del DocumentDB in grado di supportare le query, poiché può operare direttamente su questo rappresentazione della struttura di indice dei dati.

Il criterio di indicizzazione predefinito automaticamente indicizza tutte le proprietà di tutti i documenti e fornisce query coerente (che indica l'indice viene aggiornato in modo sincrono con la scrittura di documento). In che modo DocumentDB supporta coerenti aggiornamenti per la struttura dell'indice scala? Blocco gratuita e log strutturati tecniche di manutenzione indice DocumentDB utilizza scrittura ottimizzato. Questo errore indica che DocumentDB supportino un volume velocità di scrittura rapida mentre si ancora la gestione delle query coerenti. 

DocumentDB indicizzazione è progettato per migliorare l'efficienza dello spazio di archiviazione e di gestire più affitto. Per l'efficacia dei costi, il sovraccarico di spazio di archiviazione su disco dell'indice è bassa e prevedibili. Gli aggiornamenti dell'indice vengono eseguiti anche superare il preventivo delle risorse di sistema allocata per DocumentDB insieme.

##<a name="NextSteps"></a>Passaggi successivi
- Scaricare ["indipendente dal Schema indicizzazione con Azure DocumentDB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), da presentare alla conferenza interno 41a database molto grandi, agosto 31-4 settembre 2015.
- [Query con DocumentDB SQL](documentdb-sql-query.md)
- Informazioni su come personalizzare l'indice DocumentDB [qui](documentdb-indexing-policies.md)
 
