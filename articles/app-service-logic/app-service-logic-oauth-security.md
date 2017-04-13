<properties
    pageTitle="Sicurezza OAUTH di SaaS connettori e le API App | Azure"
    description="Informazioni sulla protezione OAUTH i connettori e App API nel servizio di App Azure; architettura di microservices. SaaS"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Informazioni sulla sicurezza delle OAUTH SaaS connettori

>[AZURE.NOTE] Questa versione dell'articolo si applica alla versione di logica App 2014-12-01-anteprima dello schema.

Molti del Software come connettori un servizio, ad esempio Facebook, Twitter, DropBox e così via obbligatoria l'autenticazione tramite il protocollo OAUTH.  Quando si utilizzano i connettori SaaS dalle App logica, sono disponibili un'esperienza utente semplificata nel punto in cui si fa clic su "Autorizzazione" nella finestra di progettazione logica app. Quando si **autorizzazione**, viene chiesto di accedere in (se non è già) e fornire consenso dell'utente a connettersi al servizio SaaS per proprio conto. Dopo aver fornire consenso e autorizzare, le app di logica può quindi accedere questi servizi SaaS.

## <a name="create-your-own-saas-app"></a>Creare il proprio app SaaS
Questa funzionalità semplificate è possibile perché è precedentemente creati e registrata l'applicazione di questi servizi SaaS.  In alcuni casi può essere necessario eseguire la registrazione e usare l'applicazione.  È necessario, ad esempio, quando si desidera utilizzare questi connettori SaaS nelle applicazioni personalizzate. In questo esempio viene utilizzato il connettore DropBox, ma il processo è analogo a tutti i connettori che si basano su OAUTH.

Anche nel contesto della logica di App, è possibile utilizzare un'applicazione invece di usare l'applicazione predefinita che sono disponibili. Se il pulsante "Autorizzazione" non riesce a connettersi, è possibile provare a creare il proprio app. Di seguito sono elencati i passaggi per il connettore Twitter:

1. Aprire il connettore Twitter nel portale di Azure anteprima. Passare a **esplorare** > **API App**. Selezionare il connettore Twitter:  
    ![][1]

2. Selezionare **Impostazioni** > **autenticazione**:  
    ![][2]

3. Copiare il valore di **Reindirizzamento URI** :  
    ![][3]

4. Passare a [Twitter](http://apps.twitter.com) e **creare una nuova App**. Nella proprietà **URL Callback** incollare il valore di **Reindirizzamento URI** copiato il connettore Twitter: ![][4]  
5. Dopo avere creato l'app Twitter, selezionare **chiave e token di accesso**. Copiare questi valori.
6. Nelle impostazioni di autenticazione del connettore Twitter, incollare i valori nelle proprietà **ID Client** e **Segreto del Client** :   
    ![][5]  
7. Salvare le impostazioni di connessione.  

A questo punto, dovrebbe essere possibile utilizzare il connettore da Apps logica. Quando si utilizza il connettore da logica Apps, utilizza l'applicazione anziché come applicazione predefinita.  

> [AZURE.NOTE] Se si è autorizzati un'app in precedenza, è necessario riautorizzare l'app.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png
