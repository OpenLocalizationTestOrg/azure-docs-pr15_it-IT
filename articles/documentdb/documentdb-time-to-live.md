<properties
   pageTitle="Scadenza dati con il periodo di validità DocumentDB | Microsoft Azure"
   description="Con TTL, Microsoft Azure DocumentDB offre la possibilità di documenti automaticamente eliminati dal sistema dopo un periodo di tempo."
   services="documentdb"
   documentationCenter=""
   keywords="periodo di validità"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Scadenza dati in raccolte DocumentDB automaticamente con il periodo di validità

Applicazioni consentono di creare e archiviare grandi quantità di dati. Alcuni di questi dati, ad esempio computer generato evento dati, i registri e utente sessione di informazioni sono utile solo per un periodo di tempo limitato. Una volta assume la forma dei dati in eccedenza in base alle esigenze dell'applicazione quando è possibile eliminare i dati e ridurre le esigenze di archiviazione di un'applicazione.

Con "time to live" o TTL, Microsoft Azure DocumentDB offre la possibilità di documenti automaticamente eliminati dal database dopo un periodo di tempo. La durata predefinita possibile impostare il livello di raccolta e sovrascritti su singoli documenti. Una volta TTL impostato come predefinito raccolta o un livello di documento, DocumentDB rimuoverà automaticamente i documenti esistenti dopo il periodo di tempo, espresso in secondi, poiché dell'ultima modifica.

Il periodo di validità DocumentDB utilizza un offset rispetto a quando il documento è stato modificato. A tale scopo che viene utilizzato il campo _ts che è presente in ogni documento. Il campo _ts è un timestamp periodo stile unix che rappresenta la data e l'ora. Il campo _ts viene aggiornato ogni volta che si modifica un documento. 

## <a name="ttl-behavior"></a>Comportamento TTL

La caratteristica TTL dipende dalle proprietà TTL due livelli - livello di raccolta e il livello di documento. I valori vengono configurati nelle secondi e vengono gestiti come delta rispetto _ts che il documento è stato Data ultima modifica.

 1.  DefaultTTL per la raccolta
  * Se manca o è impostato su null, i documenti non vengono eliminati automaticamente.
  
  * Se è presente e il valore "-1" = infinito-documenti senza scadenza per impostazione predefinita
  
  * Se e il valore è un numero ("n")-documenti scadono "n" secondi dopo l'ultima modifica

 2.  TTL per i documenti: 
  * Proprietà è applicabile solo se è presente per la raccolta padre DefaultTTL.
  
  * Sostituisce il valore DefaultTTL per la raccolta padre.

Non appena il documento è scaduto (ttl + _ts > = ora corrente del server), il documento è contrassegnato come "scaduto". Alcuna operazione non sarà autorizzata a tali documenti dopo la scadenza e verranno esclusi dai risultati di una query eseguita. I documenti vengono eliminati fisica nel sistema e vengono eliminati in background in base alle esigenze in un secondo momento. Non si utilizza qualsiasi [Richiesta unità (destinatari)](documentdb-request-units.md) dal budget insieme.

Logica sopra può essere visualizzata in matrice seguente:

|       | DefaultTTL mancanti o non è impostata sulla raccolta | DefaultTTL = -1 nella raccolta | DefaultTTL = "n" nella raccolta|
| ------------- |:-------------|:-------------|:-------------|
| TTL mancanti nel documento| Nessun elemento da sostituire a livello di documento poiché il documento e raccolta non dispongono del concetto TTL. | Non contiene documenti nella raccolta scadrà. | I documenti nella raccolta scadrà quando allo scadere dell'intervallo n. |
| TTL = -1 nel documento | Niente per ignorare il livello di documento dopo la raccolta non definisce la proprietà DefaultTTL che è possibile ignorare un documento. TTL su un documento è non interpretato dal sistema. | Non contiene documenti nella raccolta scadrà. | Il documento con TTL =-1 nella raccolta mai scadrà. Tutti gli altri documenti scadrà dopo intervallo "n". |
|  TTL = n nel documento | Niente per ignorare il livello di documento. TTL a un documento in non interpretato dal sistema. | Il documento con TTL = n scadrà dopo n intervallo, in secondi. Altri documenti verranno ereditano intervallo-1 e scadenza. | Il documento con TTL = n scadrà dopo n intervallo, in secondi. Altri documenti verranno applicate intervallo "n" dalla raccolta. |


## <a name="configuring-ttl"></a>Configurazione TTL

Per impostazione predefinita, il periodo di validità è disattivato per impostazione predefinita in tutte le raccolte di DocumentDB e in tutti i documenti.

## <a name="enabling-ttl"></a>Attivazione TTL

Per abilitare TTL in una raccolta o i documenti all'interno di una raccolta, è necessario impostare la proprietà DefaultTTL di un insieme di -1 o un numero positivo diverso da zero. Se il DefaultTTL su-1, che da tutti i documenti nella raccolta verranno live tempi lunghissimi predefinito, ma il DocumentDB servizio necessario monitorare questa raccolta per i documenti che hanno eseguito l'override di questa impostazione predefinita.

## <a name="configuring-default-ttl-on-a-collection"></a>Configurazione TTL predefinito in una raccolta

In grado di configurare una durata predefinita a un livello di raccolta. 

Per impostare il valore TTL in una raccolta, è necessario specificare un numero positivo diverso da zero che indica il periodo, espresso in secondi, la scadenza tutti i documenti nella raccolta dopo l'ultimo timestamp modificato del documento (_ts).

In alternativa, è possibile impostare il valore predefinito -1, che indica che tutti i documenti inseriti alla raccolta verranno live tempo indefinito per impostazione predefinita.

## <a name="setting-ttl-on-a-document"></a>Impostazione TTL su un documento

Oltre a impostare un valore TTL predefinito in una raccolta è possibile impostare TTL specifica un livello di documento. In questo sostituirà l'impostazione predefinita della raccolta.

Per impostare il valore TTL su un documento, è necessario specificare un numero positivo diverso da zero che indica il periodo, espresso in secondi, in modo che scadano il documento dopo l'ultimo timestamp modificato del documento (_ts).

Per impostare l'offset di scadenza, impostare il campo TTL nel documento.

Se un documento non ha TTL un campo, verrà applicata l'impostazione predefinita della raccolta.

Se TTL è disattivato a livello di raccolta, il campo TTL nel documento verrà ignorato fino a TTL è stato attivato nuovamente la raccolta.


## <a name="extending-ttl-on-an-existing-document"></a>Estensione TTL su un documento esistente

È possibile reimpostare il valore TTL su un documento, eseguire qualsiasi operazione di scrittura per il documento. Questa operazione consente di impostare il _ts per l'ora corrente e conto alla rovescia per la scadenza di documento, l'impostazione ttl, inizierà nuovamente.

Se si desidera modificare il valore ttl di un documento, è possibile aggiornare il campo come è possibile eseguire con qualsiasi altro campo che è possibile impostare.


## <a name="removing-ttl-from-a-document"></a>Rimozione TTL da un documento

Se un valore TTL è stato impostato su un documento e non si desidera più che scadenza del documento, quindi è possibile recuperare il documento, rimuovere il campo TTL e sostituire il documento nel server.

Quando il campo TTL viene rimosso dal documento, verrà applicato il valore predefinito della raccolta.

Per evitare che un documento da in scadenza e non eredita dalla raccolta è quindi necessario impostare il valore TTL -1.


## <a name="disabling-ttl"></a>Disabilitazione TTL

Per disabilitare TTL interamente in una raccolta e arrestare il processo in background la ricerca di proprietà DefaultTTL raccolta devono essere eliminate i documenti scaduti.

Eliminazione di questa proprietà è diversa dall'impostazione su -1. Impostazione di 1 indica che i nuovi documenti aggiunti alla raccolta verrà live tempi lunghissimi ma è possibile ignorare questo specifico documenti nella raccolta.

Rimozione di questa proprietà completamente dalla raccolta significa che non contiene documenti scadrà, anche se sono disponibili i documenti che hanno sostituita in modo esplicito predefinito precedente.


## <a name="faq"></a>Domande frequenti

**Cosa verrà TTL costo me?**

Non esiste costi aggiuntivi per l'impostazione di un valore TTL su un documento.

**Quanto tempo sarà di eliminare il documento al termine è il valore TTL?**

I documenti verranno contrassegnati come non disponibili, non appena il documento è scaduto (ttl + _ts > = ora corrente del server). Alcuna operazione non sarà autorizzata a tali documenti dopo la scadenza e verranno esclusi dai risultati di una query eseguita. I documenti vengono eliminati fisica dal sistema in background. Questa operazione non utilizza qualsiasi destinatari dal budget dell'insieme.

**Se sono necessari un periodo di tempo per eliminare i documenti, cercherà verso la quota (e fattura) fino a ottenere eliminati?**

No, quando il documento è scaduto non verrà non fatturato per l'archiviazione dei documenti e le dimensioni dei documenti non verranno applicate verso la quota di archiviazione per una raccolta.

**TTL su un documento avranno un impatto sugli addebiti RU?**

No, si verificherà alcun effetto sugli addebiti RU per le operazioni eseguite su qualsiasi documento all'interno di DocumentDB.

**Sarà l'eliminazione del impatto documenti sulla velocità che è stato effettuato il provisioning nella raccolta personale?**

No, le risposte alle richieste contro la raccolta riceveranno priorità esecuzione del processo di sfondo per eliminare i documenti. Aggiunta di TTL a qualsiasi documento non influisce seguente.

**Alla scadenza di un documento, la durata rimarranno nella raccolta fino all'eliminazione?**

Non appena il documento scade non saranno accessibile. L'ora esatta un documento rimarrà nella raccolta prima che in realtà da eliminare è deterministico e verrà impostata in quando il processo in background è in grado di eliminare il documento.

**I documenti scaduti verrà eliminati in tutti i nodi o se è "alla fine consistent?"**

Il documento sarà disponibile contemporaneamente in tutti i nodi e in tutti i paesi.

**Esiste un costo RU per TTL Monitoraggio attività in background?**

No, non esiste alcun costo RU per questa.

**Con quale frequenza vengono controllate le scadenze TTL?**

Controllare le scadenze TTL non accade in background. Quando si risponde a una richiesta di servizio back-end verrà eseguire i controlli all'interno del testo ed escludere i documenti scaduti. L'eliminazione del documento fisico è il processo solo in modo asincrono esecuzione in background. Specificare la frequenza di questo processo è destinatari disponibili nella raccolta.

**La caratteristica TTL vengono applicate solo alle interi documenti oppure posso scadere valori di proprietà singolo documento?**

TTL riguarda l'intero documento. Se si preferisce scadere solo una parte di un documento, quindi è consigliabile estrarre la parte del documento principale in a un documento "collegato" diverso e quindi utilizzare TTL sul documento estratto.

**La caratteristica TTL esistono requisiti di indicizzazione specifici?**

Sì. La raccolta deve essere [indicizzazione criterio impostato](documentdb-indexing-policies.md) su Lazy o coerenti. Si tenta di installare DefaultTTL in una raccolta con l'indicizzazione è impostata su Nessuno verrà generato un errore, così come si tenta di disattivare l'indicizzazione in una raccolta che contiene un DefaultTTL già impostato.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure DocumentDB, fare riferimento alla pagina [*documentazione*](https://azure.microsoft.com/documentation/services/documentdb/) del servizio.




