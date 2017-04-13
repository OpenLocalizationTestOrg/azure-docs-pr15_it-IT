<properties 
    pageTitle="Inoltro automatico Bus di servizio di messaggistica entità | Microsoft Azure"
    description="Informazioni su come catena una coda o l'abbonamento a una coda o argomento."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="chaining-service-bus-entities-with-auto-forwarding"></a>Concatenazione entità Bus di servizio con inoltro automatico

La caratteristica di *inoltro automatico* consente di concatenare una coda di sottoscrizione o a una coda o un argomento a cui fa parte dello stesso spazio dei nomi. Quando l'inoltro automatico è attivato, Bus di servizio automaticamente rimuove messaggi che vengono inseriti nella coda prima o l'abbonamento (origine) e li inserisce nella seconda coda o argomento (destinazione). Si noti che è comunque possibile inviare un messaggio direttamente alle entità di destinazione. Non è inoltre possibile collegare una coda secondaria, ad esempio una coda di messaggi non recapitabili, a una coda o argomento.

## <a name="using-auto-forwarding"></a>Utilizzo dell'inoltro automatico

È possibile abilitare inoltro automatico impostando la proprietà [QueueDescription.ForwardTo][] o [SubscriptionDescription.ForwardTo][] le sugli oggetti [QueueDescription][] o [SubscriptionDescription][] per l'origine, come illustrato nell'esempio seguente.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

L'entità di destinazione deve trovarsi durante la creazione di entità di origine. Se l'entità di destinazione non esiste, Bus di servizio restituisce un'eccezione quando viene richiesto di creare l'entità di origine.

È possibile utilizzare l'inoltro automatico per scalare un argomento specifico. Servizio Bus limita il [numero di sottoscrizioni a un determinato argomento](service-bus-quotas.md) a 2.000. Si possono contenere altri abbonamenti mediante la creazione di argomenti di secondo livello. Si noti che anche se non è associato il da limitazione Bus di servizio al numero di abbonamenti, aggiunta di un secondo livello degli argomenti relativi a migliorare la velocità complessiva dell'argomento di.

![Scenario di inoltro automatico][0]

È anche possibile utilizzare inoltro automatico per separare i mittenti dei messaggi dai destinatari. Si consideri ad esempio un sistema ERP costituita da tre moduli: ordine di elaborazione, gestione dell'inventario e gestione delle relazioni clienti. Ognuno di questi moduli genera messaggi che vengono inseriti in coda in un argomento corrispondente. Alice e Roberto è venditore che interessano in tutti i messaggi relativi ai propri clienti. Per ricevere i messaggi, Alice e Roberto crea una coda personale e una sottoscrizione su ognuno degli argomenti ERP che inoltrare automaticamente tutti i messaggi di posta coda.

![Scenario di inoltro automatico][1]

Se Alice va in ferie, la sua coda personale, anziché l'argomento ERP, riempie verso l'alto. In questo scenario, perché un addetto alle vendite non ha ricevuto tutti i messaggi, nessuno degli argomenti ERP mai raggiungere la quota.

## <a name="auto-forwarding-considerations"></a>Considerazioni sulla inoltro automatico

Se l'entità di destinazione ha accumulato tanti messaggi e supera la quota o entità di destinazione è disattivato, entità di origine consente di aggiungere i messaggi di [coda inattiva](service-bus-dead-letter-queues.md) fino a quando non è presente spazio nella destinazione (o l'entità viene riattivata). I messaggi continuerà a live nella coda inattiva, quindi è necessario ricevere ed elaborate da coda inattiva in modo esplicito.

Quando concatenazione insieme singoli argomenti per ottenere un argomento composto con numero di sottoscrizioni, si consiglia di disporre di un numero limitato di sottoscrizioni dell'argomento di primo livello e molti sugli argomenti di secondo livello. Ad esempio, un argomento di primo livello con 20 abbonamenti, ognuno di essi concatenati a un argomento di secondo livello con 200 abbonamenti, consente di velocità maggiore rispetto a un argomento di primo livello con 200 abbonamenti, ogni concatenati a un argomento di secondo livello con 20 sottoscrizioni.

Servizio Bus effetti un'unica operazione per ogni messaggio inoltrato. Ad esempio, inviando un messaggio a un argomento con 20 abbonamenti, ognuno di essi configurato per l'inoltro automatico dei messaggi a una coda o argomento viene effettuata come 21 operazioni se tutte le sottoscrizioni di primo livello ricevono una copia del messaggio.

Per creare una sottoscrizione appartenente a una coda o un argomento, l'autore della sottoscrizione deve avere **Gestisci** autorizzazioni nel sito di origine e l'entità di destinazione. Invio di messaggi all'argomento di origine sufficiente **inviare** le autorizzazioni per l'argomento di origine.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sull'inoltro automatico, vedere gli argomenti di riferimento seguenti:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Per ulteriori informazioni sui miglioramenti delle prestazioni Bus di servizio, vedere [partizionati messaggistica entità][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Partizionata entità messaggistica]: service-bus-partitioning.md