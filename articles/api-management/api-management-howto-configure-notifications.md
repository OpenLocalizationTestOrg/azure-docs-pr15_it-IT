<properties 
    pageTitle="Come configurare le notifiche e i modelli di posta elettronica in Azure API Management" 
    description="Informazioni su come configurare le notifiche e modelli di Azure API Management di posta elettronica." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Come configurare le notifiche e i modelli di posta elettronica in Azure API Management

API Gestione offre la possibilità di configurare le notifiche per eventi specifici e configurare i modelli di posta elettronica utilizzati per comunicare con gli amministratori e sviluppatori di un'istanza di gestione delle API. In questo argomento viene illustrato come configurare le notifiche per eventi disponibili e viene fornita una panoramica di la configurazione dei modelli di posta elettronica usati per questi eventi.

## <a name="publisher-notifications"> </a>Configurare le notifiche di publisher

Per configurare le notifiche, fare clic su **Gestisci** nel portale classica di Azure per il servizio di gestione dell'API. Verrà visualizzato il portale di gestione delle API publisher.

![Portale di Publisher][api-management-management-console]

>Se non è ancora stato creato un'istanza di servizio di gestione delle API, vedere [creare un'istanza di servizio di gestione delle API][] nell'esercitazione [Introduzione Azure API Management][] .

Fare clic su **notifiche** dal menu **Gestione API** a sinistra per visualizzare le notifiche disponibili.

![Notifiche di Publisher][api-management-publisher-notifications]

L'elenco degli eventi seguente può essere configurato per le notifiche.

-   **Le richieste di sottoscrizione (che richiedono l'approvazione)** - i destinatari di posta elettronica specificato e gli utenti riceveranno notifiche tramite posta elettronica relative alle richieste di abbonamento per i prodotti di API che richiedono l'approvazione.
-   **Nuovi abbonamenti** - i destinatari di posta elettronica specificato e gli utenti riceveranno notifiche tramite posta elettronica sui nuovi abbonamenti prodotto API.
-   **Le richieste di raccolta dell'applicazione** - i destinatari di posta elettronica specificato e gli utenti riceveranno notifiche tramite posta elettronica quando presentate nuovo nella raccolta di applicazione.
-   **Ccn** - i destinatari di posta elettronica specificato e gli utenti riceveranno posta elettronica in copia per conoscenza nascosta tutti i messaggi di posta elettronica inviati agli sviluppatori.
-   **Nuovo numero o un commento** - i destinatari di posta elettronica specificato e gli utenti riceveranno notifiche tramite posta elettronica quando un nuovo problema o commento viene inviato nel portale per sviluppatori.
-   **Messaggio di chiusura degli account** - i destinatari di posta elettronica specificato e gli utenti riceveranno notifiche tramite posta elettronica alla chiusura di un account.
-   **Limite di quota della sottoscrizione Approaching** - i seguenti destinatari di posta elettronica e gli utenti riceveranno notifiche tramite posta elettronica quando l'uso di abbonamento si avvicina quota utilizzo.

Per ogni evento, è possibile specificare i destinatari di posta elettronica mediante la casella di testo indirizzo di posta elettronica o è possibile selezionare gli utenti da un elenco.

Per specificare gli indirizzi di posta elettronica per ricevere una notifica, immetterli nella casella di testo indirizzo di posta elettronica. Se si dispongono di più indirizzi di posta elettronica, separarli con una virgola.

![Destinatari di notifica][api-management-email-addresses]

Per specificare gli utenti per ricevere una notifica, fare clic su **aggiungere un destinatario**, selezionare la casella accanto agli utenti di ricevere una notifica e fare clic su **OK**.

>Si noti che solo gli amministratori vengono visualizzati nell'elenco.

Dopo aver configurato i destinatari di notifica, fare clic su **Salva** per applicare i destinatari notifica aggiornati.

>Se passa a un'altra scheda **Publisher notifiche** nel portale di publisher notifica l'esistenza di se sono presenti non salvate le modifiche.

## <a name="email-templates"> </a>Configurare modelli di posta elettronica

Gestione delle API sono disponibili modelli di posta elettronica per i messaggi di posta elettronica inviati nel corso di amministrazione e tramite il servizio. Modelli di posta elettronica seguenti sono disponibili.

-   Raccolta invio di richieste approvata
-   Lettera di saluto per sviluppatori
-   Limite di quota sviluppo se ci si avvicina notifica
-   Invitare utenti
-   Nuovo commento aggiunto a un problema
-   Nuovo problema ricevuto
-   Nuova sottoscrizione attivato
-   Conferma dell'iscrizione rinnovato
-   Richiesta di sottoscrizione rifiuta
-   Ricevuta una richiesta di abbonamento

Questi modelli possono essere modificati come desiderato.

Per visualizzare e configurare i modelli di posta elettronica per l'istanza di gestione delle API, fare clic su **notifiche** dal menu **Gestione API** sul lato sinistro e selezionare la scheda **Modelli di posta elettronica** .

![Modelli di posta elettronica][api-management-email-templates]

Per visualizzare o modificare un modello specifico, selezionare dall'elenco a discesa **modelli** .

![Elenco di modelli di posta elettronica][api-management-email-templates-list]

Ogni modello di messaggio di posta elettronica, un oggetto in formato testo normale e una definizione del corpo in formato HTML. Ogni elemento può essere personalizzato come desiderato.

![Editor dei modelli di posta elettronica][api-management-email-template]

L'elenco dei **parametri** contiene un elenco dei parametri, che, quando inserito un oggetto o nel corpo, sarà sostituito il valore designato quando il messaggio viene inviato. Per inserire un parametro, posizionare il cursore nel punto in cui si desidera che il parametro per l'invio e fare clic sulla freccia a sinistra del nome del parametro.

Fare clic su **Anteprima** o **inviare un test** per vedere come messaggio di posta elettronica verrà ricerca o inviare un messaggio di posta elettronica di prova.

>Si noti che i parametri non vengono sostituiti con i valori effettivi durante l'anteprima o l'invio di un test.
>
>Per salvare le modifiche al modello di messaggio di posta elettronica, fare clic su **Salva**o per annullare le modifiche fare clic su **Annulla**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Guida introduttiva di Azure API Management]: api-management-get-started.md
[Creare un'istanza di servizio di gestione delle API]: api-management-get-started.md#create-service-instance