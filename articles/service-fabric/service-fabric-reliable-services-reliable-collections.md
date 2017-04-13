<properties
   pageTitle="Raccolte affidabile | Microsoft Azure"
   description="Tessuti servizio informazioni sullo stato servizi forniscono affidabile raccolte che consentono di scrivere applicazioni cloud altamente disponibile, scalable e latenza ridotta."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/18/2016"
   ms.author="mcoskun"/>

# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Introduzione alle raccolte affidabile in servizi di informazioni sullo stato di Azure servizio tessuti

Raccolte affidabile consentono di scrivere applicazioni cloud altamente disponibile, scalable e bassa latenza, come se si scrive applicazioni per computer singolo. Le classi dello spazio dei nomi **Microsoft.ServiceFabric.Data.Collections** forniscono un set di raccolte di casella che automaticamente lo stato di disponibilità. Gli sviluppatori devono programmare solo alle API insieme affidabile e attendere che venga raccolte affidabile gestire lo stato replicato e locale.

La differenza fondamentale tra raccolte affidabile e altre tecnologie di disponibilità (ad esempio Redis, il servizio di tabelle Azure e servizio Azure coda) è che lo stato viene mantenuto in locale in istanza del servizio mentre vengono applicate anche disponibilità. Questo significa che:

- Tutte le operazioni lettura sono locali, che genera bassa latenza e legge ad alta velocità.
- Il numero minimo di rete IOs, creando così bassa latenza è soggetta a tutte le operazioni scrittura e scrive ad alta velocità.

![Immagine dell'andamento delle raccolte.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Raccolte affidabile possono essere considerate alla naturale evoluzione le classi **Collections** : un nuovo set di raccolte che sono state progettate per le applicazioni cloud e su più computer senza aumentare la complessità per gli sviluppatori. Di conseguenza, raccolte affidabile sono:

- Replicato: Modifiche dello stato verranno replicate per disponibilità.
- Mantenuti: Dati vengono mantenuti su disco per resistenza interruzioni di grandi dimensioni (ad esempio un Data Center interruzioni).
- Asincrono: API sono asincrone per garantire che thread non vengano bloccati quando incorrere IO.
- Transazioni: API utilizzano l'estrazione delle transazioni in modo che è possibile gestire più raccolte affidabile all'interno di un servizio facilmente.

Raccolte affidabile forniscono coerenza sicura garantisce all'esterno della casella per semplificare il motivi sullo stato applicazione.
La coerenza sicura si ottiene assicurando transazione commit fine solo dopo l'intera transazione è stata registrata in una base maggior parte delle repliche, tra cui primario.
Per ottenere la coerenza meno sicuri, applicazioni possono accettare al client/richiedente prima che venga restituito il commit asincrono.

Le API raccolte affidabile sono evoluzione di raccolte simultanee API (disponibile dello spazio dei nomi **System.Collections.Concurrent** ):

- Asincrono: Restituisce un'attività poiché, a differenza di raccolte simultanee, le operazioni sono replicate e mantenute.
- Non parametri out: Usa `ConditionalValue<T>` per restituire un valore bool e un valore invece di parametri out. `ConditionalValue<T>`è ad esempio `Nullable<T>` , ma non richiede T a una struttura.
- Transazioni: Utilizza un oggetto transazione per consentire all'utente di azioni di gruppo in più raccolte affidabile in una transazione.

Oggi, **Microsoft.ServiceFabric.Data.Collections** contiene due raccolte:

- [Dizionario affidabile](https://msdn.microsoft.com/library/azure/dn971511.aspx): rappresenta un insieme di replicato, transazione e asincrono di coppie di parole chiave/valore. Analogamente a **ConcurrentDictionary**, la chiave e il valore può essere di qualsiasi tipo.
- [Coda affidabile](https://msdn.microsoft.com/library/azure/dn971527.aspx): rappresenta una replicato, transazioni e asincrono strict first in First Out (FIFO) coda. Analogamente a **ConcurrentQueue**, che può essere di qualsiasi tipo.

## <a name="isolation-levels"></a>Livelli di isolamento
Livello di isolamento definisce il grado in cui la transazione deve essere isolata dalle modifiche apportate da altre transazioni.
Esistono due livelli di isolamento che sono supportati in raccolte affidabile:

- **Lettura ripetibile**: consente di specificare che le istruzioni non è possibile leggere i dati che sono stati modificati ma non ancora eseguito il commit da altre transazioni e che nessun'altra transazione possono modificare dati che sono stato letto dalla transazione corrente fino al termine della transazione corrente. Per ulteriori informazioni, vedere [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Snapshot**: Specifica che i dati letti da qualsiasi istruzione in una transazione saranno la versione coerente a livello di dati presenti all'inizio della transazione.
La transazione può riconoscere solo le modifiche ai dati che sono state salvate prima dell'inizio della transazione.
Apportate ai dati da altre transazioni dopo l'inizio della transazione corrente non sono visibili agli istruzioni esecuzione nella transazione corrente.
L'effetto è come se le istruzioni di una transazione ottenere uno snapshot dei dati salvati come esistenti all'inizio della transazione.
Snapshot sono coerenti in raccolte affidabile.
Per ulteriori informazioni, vedere [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Raccolte affidabile scegliere automaticamente il livello di isolamento da utilizzare per una determinata operazione di lettura a seconda dell'operazione e il ruolo della replica al momento della creazione della transazione.
Di seguito è riportato nella tabella che descrive le impostazioni predefinite livello di isolamento per le operazioni dizionario affidabile e coda.

| Operazione \ ruolo      | Principale          | Secondario        |
| --------------------- | :--------------- | :--------------- |
| Entità di sola lettura    | Lettura ripetibile  | Snapshot         |
| Enumerazione \ conteggio   | Snapshot         | Snapshot         |

>[AZURE.NOTE] Esempi comuni per le operazioni di entità singola sono `IReliableDictionary.TryGetValueAsync`, `IReliableQueue.TryPeekAsync`.

Supporto per il dizionario affidabile e coda affidabile scrive di lettura.
In altre parole, qualsiasi scrittura all'interno di una transazione saranno visibile per la lettura seguente che appartiene alla stessa transazione.

## <a name="locking"></a>Blocco
In raccolte affidabile, tutte le transazioni sono in due fasi: transazione non rilascia blocchi acquisiti fino a quando la transazione termina con un'interruzione o commit.

Dizionario affidabile utilizza blocco per tutte le operazioni di singola entità a livello di riga.
Coda affidabile favorisce la concorrenza per proprietà FIFO transazione strict.
Coda affidabile utilizza blocchi a livello di operazione che consente una sola transazione con `TryPeekAsync` e/o `TryDequeueAsync` e una sola transazione con `EnqueueAsync` alla volta.
Si noti che per mantenere FIFO, se un `TryPeekAsync` o `TryDequeueAsync` mai osserva che coda affidabile non contiene alcun dato, anche Blocca `EnqueueAsync`.

Scrivere operazioni hanno sempre blocchi esclusivi.
Per le operazioni di lettura, il blocco dipende da due fattori.
Qualsiasi lettura operazioni eseguite mediante isolamento dello Snapshot sono blocco gratuito.
Qualsiasi operazione Repeatable Read per impostazione predefinita consente di accedere blocchi condivisi.
Tuttavia, per qualsiasi operazione di lettura che supporta Repeatable Read, l'utente può chiedere di un blocco di aggiornamento anziché il blocco condiviso.
Un blocco di aggiornamento è un blocco asimmetrico utilizzato per evitare che una forma comune di blocco critico che si verifica quando più transazioni bloccare le risorse potenziali aggiornamenti in un secondo momento.

Seguito, è possibile trovare la matrice di compatibilità blocco:

| Richiedere \ abbia concesso o meno | Nessuno         | Condiviso       | Aggiornamento      | Esclusivo    |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Condiviso            | Alcun conflitto  | Nessun conflitto  | Conflitto    | Conflitto     |
| Aggiornamento            | Alcun conflitto  | Nessun conflitto  | Conflitto    | Conflitto     |
| Esclusivo         | Nessun conflitto  | Conflitto     | Conflitto    | Conflitto     |

Si noti che un argomento di timeout nelle API raccolte affidabile viene utilizzato per il rilevamento blocco critico.
Ad esempio, due transazioni (T1 e T2) siano tentando di leggere e aggiornare K1.
È possibile che vengano blocco critico, poiché entrambi alla fine di blocco condiviso.
In questo caso, una o entrambe le operazioni scadrà.

Si noti che lo scenario di blocco critico sopra un buon esempio su come un blocco di aggiornamento può impedire a blocchi critici.

## <a name="persistence-model"></a>Modello di persistenza
Il responsabile stato affidabile e raccolte affidabile seguire un modello di persistenza chiamato Log e verifica.
Questo è un modello in cui ogni modifica dello stato è registrato su disco e applicata solo in memoria.
Lo stato di completamento viene mantenuto solo occasionalmente (noto anche Punto di controllo).
Il vantaggio è che delta vengono convertiti in sola scrittura sequenziali su disco per migliorare le prestazioni.

Per comprendere meglio il modello di Log e verifica, prima di tutto esaminiamo lo scenario infinito disco.
Il responsabile stato affidabile registra ogni operazione prima che viene replicato.
In questo modo affidabile insieme da applicare solo l'operazione di memoria.
Poiché i registri vengono mantenuti, anche quando la replica ha esito negativo e deve essere riavviato, il responsabile stato affidabile include le informazioni necessarie nei registri per la riproduzione di tutte le operazioni di che replica di perse.
Mentre il disco è infinito, i record del log non è mai necessario rimuovere e raccolta affidabile devono essere gestiti solo lo stato in memoria.

Ora esaminiamo lo scenario disco limitata.
Come si accumulano record di log, il responsabile stato affidabile eseguiranno spazio su disco.
Prima che in tal caso, è necessario che il responsabile stato affidabile troncare il registro per creare spazio per i record più recenti.
Richiederà insiemi di affidabile verifica lo stato in memoria su disco.
È responsabilità le raccolte affidabile per mantenere il proprio stato fino a quel momento.
Una volta raccolte affidabile completare i punti di controllo, il responsabile stato affidabile possibile troncare il log per liberare spazio su disco.
In questo modo, quando la replica deve essere riavviato raccolte affidabile verrà ripristinato il relativo stato checkpoint e il responsabile stato affidabile verrà recuperare e riprodurre tutte le modifiche di stato che si sono verificati il punto di controllo.

>[AZURE.NOTE] Aggiungere un altro valore di arresto è che consente di migliorare le prestazioni di ripristino nella maggior parte dei casi.
In questo modo punti di controllo contengono solo le versioni più recenti.

## <a name="recommendations"></a>Consigli

- Non modificare un oggetto di tipo personalizzato restituito da operazioni di lettura (ad esempio, `TryPeekAsync` o `TryGetValueAsync`). Raccolte affidabile, come raccolte simultanee, restituiscono un riferimento agli oggetti e non una copia.
- Eseguire l'oggetto restituito di un tipo personalizzato di copia completa prima di apportare modifiche. Poiché le strutture e tipi incorporati vengono passati in base al valore, non è necessario eseguire una copia completa su di essi.
- Non utilizzare `TimeSpan.MaxValue` per timeout. Timeout deve essere utilizzato per rilevare blocchi critici.
- Dopo che è stato eseguito il commit, interrotta o eliminato, non usare una transazione.
- Non utilizzare enumerazione di fuori dell'ambito della transazione in che è stato creato.
- Non creare una transazione all'interno di un'altra transazione `using` istruzione poiché può causare blocchi critici.
- Assicurarsi che il `IComparable<TKey>` implementazione sia corretto. Il sistema assume dipendenza seguente per l'unione di punti di controllo.
- Consente di blocco di aggiornamento durante la lettura di un elemento con un intento aggiornarlo per evitare che una determinata categoria di blocchi critici.
- Valutare la possibilità di utilizzo del backup e ripristinare le funzionalità per il ripristino di emergenza.
- Evitare di combinare operazioni singola entità ed entità multiple (ad esempio `GetCountAsync`, `CreateEnumerableAsync`) nella stessa transazione a causa di diversi livelli di isolamento.

Ecco alcuni aspetti da tenere presenti:

- Il timeout predefinito è 4 secondi per tutte le API insieme affidabile. Maggior parte degli utenti non eseguire l'override seguente.
- Token di annullamento predefinito è `CancellationToken.None` in tutte le API raccolte affidabile.
- Il parametro di tipo di chiave (*TKey*) per un dizionario affidabile necessario implementare correttamente `GetHashCode()` e `Equals()`. Le chiavi devono essere modificate.
- Per ottenere disponibilità per gli insiemi di file affidabile, ogni servizio deve avere almeno una destinazione e replica minimo impostare le dimensioni di 3.
- Le operazioni di lettura nel database secondario potrebbero essere letti versioni che non sono base approvata.
Questo errore indica che una versione dei dati che vengono letti da un singolo database secondario potrebbe essere avanzamento false.
Naturalmente, lettura dalle principale è sempre stabile: non possa mai essere falso avanzamento.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva a servizi affidabile](service-fabric-reliable-services-quick-start.md)
- [Utilizzo di raccolte affidabile](service-fabric-work-with-reliable-collections.md)
- [Affidabile delle notifiche di servizi](service-fabric-reliable-services-notifications.md)
- [Servizi affidabili di backup e ripristino (ripristino di emergenza)](service-fabric-reliable-services-backup-restore.md)
- [Configurazione di gestione dello stato affidabile](service-fabric-reliable-services-configuration.md)
- [Introduzione a servizi API Web tessuti del servizio](service-fabric-reliable-services-communication-webapi.md)
- [Utilizzo dei servizi affidabile modello di programmazione avanzato](service-fabric-reliable-services-advanced-usage.md)
- [Guida di riferimento per sviluppatori per raccolte affidabile](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
