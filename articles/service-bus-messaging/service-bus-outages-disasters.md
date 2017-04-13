<properties 
    pageTitle="Isolanti applicazioni di servizio Bus contro interruzioni e guasti | Microsoft Azure"
    description="Vengono descritte le tecniche è possibile utilizzare la protezione delle applicazioni rispetto a un'interruzione del servizio Bus potenziali."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/02/2016"
    ms.author="sethm" />

# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Procedure consigliate per isolanti applicazioni rispetto a interruzioni Bus di servizio e di emergenza

Applicazioni critiche devono funzionare continuamente, anche in presenza di emergenza o interruzioni non pianificate. In questo argomento vengono descritte le tecniche è possibile utilizzare la protezione delle applicazioni di servizio Bus in base a un potenziale interruzione del servizio o emergenza.

Un'interruzione del servizio è definito come la temporanea indisponibilità del Bus di servizio Azure. Il periodo di inattività può influire sulla alcuni componenti di Bus di servizio, ad esempio un archivio di messaggistica o anche nell'intero Data Center. Dopo che è stato risolto il problema, Bus di servizio nuovamente disponibile. In genere, un'interruzione non comporta la perdita di messaggi o altri dati. Esempio di errore di un componente è la mancata disponibilità di un particolare archivio messaggistica. Un esempio di un'interruzione del livello di Data Center è un errore di power del Data Center del o un parametro di rete difettoso Data Center. Un'interruzione può durare da alcuni minuti in pochi giorni.

Emergenza è definita come la perdita permanente di un'unità in scala Bus di servizio o Data Center. Data Center del possono o non vengano disponibili nuovamente. In genere emergenza comporta perdita di alcuni o tutti i messaggi o altri dati. Esempi di emergenza sono fire, saturazione o terremoto.

## <a name="current-architecture"></a>Architettura corrente

Servizio Bus utilizza più archivi di messaggistica per archiviare i messaggi inviati a code o argomenti. Non suddiviso coda o un argomento è assegnata a un archivio di SMS. Se questo archivio messaggistica non è disponibile, tutte le operazioni nella coda o argomento avrà esito negativo.

Tutte le entità messaggistica di Bus di servizio (code, argomenti, inoltro) si trovano in uno spazio dei nomi del servizio, è associato un Data Center. Servizio Bus non consente la replica geografico automatica dei dati, né consente uno spazio dei nomi su più Data Center.

## <a name="protecting-against-acs-outages"></a>Protezione contro interruzioni ACS

Se si utilizza le credenziali ACS e ACS non è più disponibile, client non è più possono ottenere i token. Clienti che hanno un token al momento che ACS è inattivo è possono continuare a usare servizio Bus finché i token scadono. La durata di token predefinito è 3 ore.

Per evitare interruzioni ACS, utilizzare i token di firma di Access condiviso (SA). In questo caso, il client autenticazione direttamente con Bus di servizio eseguendo l'accesso a un token di self-minted con una chiave segreta. Le chiamate a ACS non sono più necessari. Per ulteriori informazioni sui token SA, vedere [Bus di servizio di autenticazione][].

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Protezione delle code e argomenti contro messaggistica errori store

Non suddiviso coda o un argomento è assegnata a un archivio di SMS. Se questo archivio messaggistica non è disponibile, tutte le operazioni nella coda o argomento avrà esito negativo. Una coda partizionata mano, costituita da più frammenti. Ogni frammento verrà archiviato in un archivio di messaggistica diverso. Quando un messaggio viene inviato a una coda partizionata o argomento, Bus di servizio assegna il messaggio a uno dei frammenti. Se non è disponibile nell'archivio messaggi corrispondente, Bus di servizio scrivere il messaggio differenti frammenti, se possibile. Per ulteriori informazioni sulle entità partizionata, vedere [partizioni messaggistica entità][].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Protezione contro interruzioni del Data Center o di emergenza

Per consentire caso di errore tra due Data Center, è possibile creare uno spazio dei nomi del servizio Bus di servizio in ogni Data Center. Ad esempio, la spazio dei nomi di servizio servizio Bus **contosoPrimary.servicebus.windows.net** eventualmente presenti nell'area negli Stati Uniti nord/centrale e **contosoSecondary.servicebus.windows.net** eventualmente presenti nell'area Sud/centrale IT. Se un Bus di servizio di messaggistica entità deve rimanere accessibile in presenza di un'interruzione del Data Center, è possibile creare tale entità in entrambi gli spazi dei nomi.

Per ulteriori informazioni, vedere la sezione "Errore del servizio Bus all'interno di un Data Center Azure" [asincroni modelli di messaggistica][]e disponibilità.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protezione dei punti finali di inoltro in base a Data Center interruzioni o guasti

La replica geografico di endpoint di inoltro consente a un servizio esposti un endpoint di inoltro per essere raggiungibile in presenza di interruzioni Bus di servizio. Per ottenere la replica geografico, il servizio è necessario creare due punti finali di inoltro in diversi spazi dei nomi. Gli spazi dei nomi devono essere inclusi nei data center diverso e i due punti finali devono avere un nome diversi. Ad esempio, un endpoint principale è raggiungibili in **contosoPrimary.servicebus.windows.net/myPrimaryService**, mentre è raggiungibili equivalente secondario in **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Per entrambi gli endpoint rilevati quindi il servizio e un client può richiamare il servizio tramite estremità. Un'applicazione client in modo casuale sceglie uno dell'inoltro come endpoint principale e invio di una richiesta al punto finale attivo. Se l'operazione non riesce con un codice di errore, questo errore indica che l'endpoint di inoltro non è disponibile. L'applicazione viene aperto un canale per l'endpoint di backup ed emette di nuovo la richiesta. A questo punto attivo e gli endpoint backup invertire i ruoli: l'applicazione client vengono tenuti in considerazione la precedente attivo endpoint di nuovo endpoint di backup e il vecchio backup endpoint di nuovo endpoint attivo. Se entrambe le operazioni errori di invio, i ruoli di due entità rimangono invariati e viene restituito un errore.

Nell'esempio [geografico replica con i servizi Bus inoltro dei messaggi][] viene illustrato come replicare inoltro.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Protezione delle code e argomenti contro interruzioni del Data Center o di emergenza

Per ottenere flessibilità rispetto interruzioni del Data Center quando tramite negoziate messaggistica, Bus di servizio supporta due approcci: replica *Attiva* e *passiva* . Per ogni approccio se una determinata coda o l'argomento devono rimanere accessibile in presenza di un'interruzione del Data Center, è possibile crearlo in entrambi gli spazi dei nomi. Entrambe le entità possono avere lo stesso nome. Ad esempio una coda principale è raggiungibili in **contosoPrimary.servicebus.windows.net/myQueue**, mentre è raggiungibili equivalente secondario in **contosoSecondary.servicebus.windows.net/myQueue**.

Se l'applicazione non richiede permanente comunicazioni mittente al destinatario, l'applicazione possibile implementare coda permanente lato client per evitare la perdita di messaggi e per impedire che il mittente da eventuali errori temporanei Bus di servizio.

## <a name="active-replication"></a>Attiva replica

La replica di Active vengono utilizzate entità in entrambi gli spazi dei nomi per ogni operazione. Qualsiasi client che invia un messaggio inviato due copie dello stesso messaggio. La prima copia viene inviata a entità primaria (ad esempio **contosoPrimary.servicebus.windows.net/sales**) e la seconda copia del messaggio viene inviata l'entità secondaria (ad esempio **contosoSecondary.servicebus.windows.net/sales**).

Un client riceve messaggi da entrambe le code. Il ricevitore elabora prima copia di un messaggio e la seconda copia viene eliminata. Per eliminare i messaggi duplicati, il mittente è necessario contrassegnare ogni messaggio con un identificatore univoco. Con lo stesso identificatore, è necessario tag entrambe le copie del messaggio. È possibile utilizzare la proprietà [BrokeredMessage.MessageId][] o [BrokeredMessage.Label][] o una proprietà personalizzata per contrassegnare il messaggio. Il ricevitore è necessario gestire un elenco dei messaggi che ha già ricevuto.

Nell'esempio [geografico replica con i messaggi del servizio Bus negoziate][] viene attivi della replica della messaggistica entità.

> [AZURE.NOTE] L'approccio replica di active raddoppia il numero di operazioni, pertanto questo approccio può comportare costo superiore.

## <a name="passive-replication"></a>Replica passiva

Nel caso gratuiti guasto replica passiva utilizza solo uno dei due entità messaggistica. Un client invia il messaggio per l'entità attivo. In caso di esito sull'entità attivo con un codice di errore che indica la Data Center che ospita l'entità active potrebbero non essere disponibili, il client invia una copia del messaggio per l'entità di backup. A questo punto attivo e quelle di backup invertire i ruoli: client mittente considera precedente entità attivi per la nuova entità di backup e l'entità di backup precedente è la nuova entità attiva. Se entrambe le operazioni errori di invio, i ruoli di due entità rimangono invariati e viene restituito un errore.

Un client riceve messaggi da entrambe le code. Perché è probabile che il destinatario riceverà due copie dello stesso messaggio, il destinatario è necessario eliminare i messaggi duplicati. È possibile eliminare i duplicati nello stesso modo come descritto per la replica attiva.

In generale, replica passiva è più economica di replica di active in quanto nella maggior parte dei casi viene eseguita solo un'operazione. Latenza, velocità e costo monetario sono identici per lo scenario non replicato.

Quando si usa replica passiva, negli scenari seguenti messaggi possono essere persi o ricevuti due volte:

-   **Ritardo messaggi o perdita**: si presuppone che il mittente m1 un messaggio inviato alla coda principale e quindi coda diventa disponibile prima che il destinatario riceva m1. Il mittente invia un m2 messaggio successivo nella coda secondaria. Se coda principale è temporaneamente non disponibile, il destinatario riceverà m1 dopo coda nuovamente disponibile. In caso di problemi, il destinatario non è mai che venga visualizzato m1.

-   **Duplicare ricezione**: presuppongono che il mittente invia un messaggio m a coda principale. Servizio Bus correttamente elabora m, ma non riesce a inviare una risposta. Al termine dell'operazione di invio, il mittente invia una copia di m alla coda secondaria. Se il destinatario in grado di ricevere la prima copia m prima coda primaria non è più disponibile, il destinatario riceverà entrambe le copie di m circa nello stesso momento. Se il destinatario non è in grado di ricevere la prima copia m prima coda primaria non è più disponibile, il destinatario inizialmente riceve la seconda copia di m, ma quando coda principale diventa disponibile riceve una seconda copia m.

Nell'esempio [geografico replica con i messaggi del servizio Bus negoziate][] viene passivo della replica della messaggistica entità.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul ripristino di emergenza, vedere gli articoli seguenti:

- [Continuità aziendale di Database SQL Azure][]
- [Indicazioni tecniche sulla resilienza Azure][]

  [Autenticazione Bus del servizio]: service-bus-authentication-and-authorization.md
  [Partizionata entità messaggistica]: service-bus-partitioning.md
  [Modelli di messaggistica asincroni e disponibilità]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
  [La replica geografico con Bus di servizio di inoltro dei messaggi]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [La replica geografico con Bus di servizio negoziate messaggi]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Continuità aziendale di Database SQL Azure]: ../sql-database/sql-database-business-continuity.md
  [Indicazioni tecniche sulla resilienza Azure]: ../resiliency/resiliency-technical-guidance.md
