<properties
    pageTitle="Routing ed espressioni Tag"
    description="Questo argomento viene illustrato il routing e tag espressioni per hub notifica Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="routing-and-tag-expressions"></a>Espressioni di routing e tag

##<a name="overview"></a>Panoramica

Le espressioni tag consentono di distribuire set specifici di dispositivi o le registrazioni in particolare, quando si invia una notifica push tramite hub di notifica.


## <a name="targeting-specific-registrations"></a>Assegnazione di gruppi specifiche registrazioni

L'unico modo per destinazione notifica specifica registrazioni consiste nell'associare tag con loro, quindi assegnare i tag. Come illustrato nella [Gestione di registrazione](notification-hubs-push-notification-registration-management.md), per ricevere notifiche push un'app deve registrare un punto di dispositivo in un hub di notifica. Dopo aver creata una registrazione di un hub di notifica, back-end applicazione consente l'invio delle notifiche push in.
Back-end applicazione è possibile scegliere le registrazioni di destinazione con una notifica specifica nei modi seguenti:

1. **Trasmettere**: tutte le registrazioni nell'hub notifica riceveranno la notifica.
2. **Tag**: tutte le registrazioni che contengono il tag specificato riceveranno la notifica.
3. **Espressione contrassegno**: tutte le registrazioni il cui set di tag corrispondono all'espressione specificata riceveranno la notifica.

## <a name="tags"></a>Tag

Un tag che può essere qualsiasi stringa, un massimo di 120 caratteri, che contiene caratteri alfanumerici e i seguenti caratteri non alfanumerici: '_', ‘@’, '#', '. ',':', '-'. Nell'esempio seguente viene illustrata un'applicazione da cui è possibile ricevere notifiche di tipo avviso popup informazioni sui gruppi di musica specifico. In questo scenario, un modo semplice per indirizzare notifiche è alle registrazioni etichetta con contrassegni che rappresentano le bande diverse, come illustrato nella figura seguente.

![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags.png)

Nell'immagine seguente, il messaggio con tag **Beatles** raggiunge solo i tablet registrato con il tag **Beatles**.

Per ulteriori informazioni sulla creazione di registrazioni per categorie, vedere [Gestione di registrazione](notification-hubs-push-notification-registration-management.md).

È possibile inviare notifiche ai tag utilizzando le notifiche invio dei `Microsoft.Azure.NotificationHubs.NotificationHubClient` classe in [Microsoft Azure notifica hub](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) SDK. È anche possibile utilizzare Node o le API REST di notifiche Push.  Ecco un esempio di utilizzo di SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You requested a Beatles notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Beatles");

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You requested a Wailers notification</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, "Wailers");




Tag non può essere già stato eseguito il provisioning e può fare riferimento a più sui concetti di app specifici. Ad esempio, gli utenti di questa applicazione di esempio possono commentare bande e desidera ricevere toasts, non solo per i commenti nel loro bande preferite, ma anche per tutti i commenti da amici, indipendentemente dalla banda in cui sono commenti. L'immagine seguente mostra un esempio di questo scenario:



![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags2.png)

Nell'immagine seguente Alice è interessata negli aggiornamenti per la Beatles e Davide è interessati gli aggiornamenti per la Wailers. Roberto sarà interessato nei commenti di Charlie e Charlie nella interessato il Wailers. Quando viene inviata una notifica per commento di Charlie il Beatles, Alice e Davide ricevuto dal destinatario.

Mentre è possibile codificare preoccupazioni più tag (ad esempio, "band_Beatles" o "follows_Charlie"), tag sono stringhe semplici e non alle proprietà con valori. Una registrazione corrisponde solo alla presenza o meno di un tag specifico.

Per un'esercitazione dettagliata completo su come usare i contrassegni per l'invio a gruppi di interesse, vedere [Ultimi aggiornamenti](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md).


## <a name="using-tags-to-target-users"></a>Uso di tag agli utenti di destinazione

Un altro modo per usare i contrassegni è identificare tutti i dispositivi di un utente specifico. Le registrazioni possono essere contrassegnate con un tag che contiene un id utente, come illustrato nella figura seguente:


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags3.png)

Nell'immagine seguente uid:Alice messaggio tagged raggiunge tutti uid:Alice tag registrazioni; di conseguenza, tutti i dispositivi di Alice.


##<a name="tag-expressions"></a>Espressioni di tag

Esistono casi in cui ha una notifica a un set di registrazioni identificato non per un singolo contrassegno, ma da un'espressione booleana tag di destinazione.

Valutare la possibilità di un'applicazione di sport che invia un promemoria per tutti gli utenti Boston sui gioco tra il Sox rosso e Cardinals. Se l'applicazione client registra tag sugli interessi team e posizione, la notifica deve essere destinata a tutti gli utenti Boston interessati Sox rosso o il Cardinals. Questa condizione può essere espressa con espressioni booleane seguenti:

    (follows_RedSox || follows_Cardinals) && location_Boston


![](./media/notification-hubs-routing-tag-expressions/notification-hubs-tags4.png)

Tag espressioni possono contenere tutti gli operatori booleani, ad esempio AND (& &), o (|) e non (!). Contengono tra parentesi. Tag le espressioni sono limitate a 20 tag se contengono solo ORs; in caso contrario sono limitati ai 6 tag.

Ecco un esempio per l'invio di notifiche con le espressioni di tag mediante il SDK.


    Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;

    String userTag = "(location_Boston && !follows_Cardinals)"; 

    // Windows 8.1 / Windows Phone 8.1
    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

    // Windows 10
    toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
    "You want info on the Red Socks</text></binding></visual></toast>";
    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
