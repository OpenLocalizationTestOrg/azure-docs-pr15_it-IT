<properties
    pageTitle="Creare un'App di logica | Microsoft Azure"
    description="Informazioni su come creare un'App di logica di connessione servizi SaaS"
    authors="jeffhollan"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="create-a-new-logic-app-connecting-saas-services"></a>Creare una nuova app logica la connessione di servizi SaaS

In questo argomento viene illustrato come, in pochi minuti per iniziare con [Azure logica App](app-service-logic-what-are-logic-apps.md). Verrà esaminato un flusso di lavoro semplice che consente di inviare TWEET interessanti alla posta elettronica.

Per utilizzare questo scenario, è necessario:

- Una sottoscrizione di Azure
- Un account Twitter
- Un Outlook.com o ospitato cassetta postale di Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Creare una nuova app logica di posta elettronica si TWEET

1. Nel [dashboard portale Azure](https://portal.azure.com), selezionare **Nuovo**. 
2. Nella barra di ricerca, cercare "logica app" e quindi selezionare **App logica**. È anche possibile selezionare **Nuovo** **Web + Mobile**e selezionare **App logica**. 
3. Immettere un nome per l'app logica, selezionare un gruppo di risorse e selezionare **Crea**.  Se si seleziona **Aggiungi a Dashboard** app logica si apriranno automaticamente dopo la distribuzione.  
4. Dopo aver aperto l'app logica per la prima volta che è possibile selezionare da un modello per iniziare.  Per il momento fare clic su **App logica vuota** per questo creare da zero. 
1. Il primo elemento che è necessario creare è il trigger.  Questo è un evento in cui verrà avviata l'app logica.  Cercare **twitter** nella casella di ricerca condizione di attivazione e selezionarla.
7. Ora immettere in un termine di ricerca per attivare in.  La **frequenza** e **intervallo** determinerà con quale frequenza controllerà l'app logica per TWEET nuovo (e ritorno tutti TWEET durante che durata).
    ![Ricerca di Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Selezionare il pulsante **nuova azione** e quindi scegliere **Aggiungi un'azione** o **aggiungere una condizione**
6. Quando si seleziona **Aggiungi un'azione**, è possibile eseguire ricerche i [connettori disponibili](../connectors/apis-list.md) per scegliere un'azione. Ad esempio, è possibile selezionare **Outlook.com - inviare posta elettronica** per inviare posta elettronica da un indirizzo di outlook.com:  
    ![Azioni](./media/app-service-logic-create-a-logic-app/actions.png)

7. A questo punto è necessario compilare i parametri per la posta elettronica desiderato:  ![parametri](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Infine, è possibile selezionare **Salva** per rendere l'app logica live.

## <a name="manage-your-logic-app-after-creation"></a>Gestire l'app logica dopo la creazione

A questo punto l'app logica sia in esecuzione. Verificherà periodicamente per tweet con il termine di ricerca immesso. Quando viene rilevato un tweet corrispondente, invia è un messaggio di posta elettronica. Infine, si verrà vedere come disabilitare l'app, oppure come procede.

1. Accedere al [portale di Azure](https://portal.azure.com)

1. Fare clic su **Sfoglia** sul lato sinistro dello schermo e selezionare **App logica**.

2. Fare clic su nuova app di logica appena creata per visualizzare lo stato corrente e informazioni generali.

3. Per modificare la nuova app logica, fare clic su **Modifica**.

5. Per disattivare l'app, fare clic su **Disattiva** nella barra dei comandi.

1. Visualizzazione cronologia di esecuzione e trigger per monitorare durante l'esecuzione di un'applicazione di logica.  È possibile fare clic su **Aggiorna** per visualizzare i dati più recenti.

In meno di 5 minuti è stato possibile installare un'app di logica semplice in esecuzione nel cloud. Per ulteriori informazioni sull'uso delle funzionalità logica App, vedere [usare le funzionalità di app logica]. Per conoscere le definizioni di logica App, vedere [creare definizioni di logica di App](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utilizzo delle funzionalità di app logica]: app-service-logic-create-a-logic-app.md
