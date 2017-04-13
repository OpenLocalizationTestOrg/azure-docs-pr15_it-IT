<properties
    pageTitle="Configurare un nome di dominio personalizzato per un'app web nel servizio di App Azure che utilizza il traffico Manager per il bilanciamento del carico."
    description="Usare un nome di dominio personalizzato per un un'app web nel servizio di App Azure che include il traffico Manager per il bilanciamento del carico."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Configurazione di un nome di dominio personalizzato per un'app web nel servizio di App Azure con il traffico Manager

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[AZURE.INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

In questo articolo fornisce istruzioni generiche per l'uso di un nome di dominio personalizzato con il servizio di App Azure che utilizzano il traffico Gestione bilanciamento del carico.

[AZURE.INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>
## <a name="understanding-dns-records"></a>Informazioni sui record DNS

[AZURE.INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>
## <a name="configure-your-web-apps-for-standard-mode"></a>Configurare le applicazioni web per la modalità standard

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>
## <a name="add-a-dns-record-for-your-custom-domain"></a>Aggiungere un record DNS per il dominio personalizzato

> [AZURE.NOTE] Se è stato acquistato dominio tramite Azure App servizio Web App passare alla procedura seguente e fare riferimento al passaggio finale dell'articolo [Acquistare dominio per le applicazioni Web](custom-dns-web-site-buydomains-web-app.md) .

Per associare il dominio personalizzato a un'app web nel servizio App Azure, è necessario aggiungere una nuova voce nella tabella DNS per il dominio personalizzato mediante strumenti disponibili tramite il registrar di dominio è stato acquistato il nome del dominio. Utilizzare la procedura seguente per individuare e utilizzare gli strumenti DNS.

1. Accedere al proprio account presso il registrar e cercare una pagina per la gestione dei record DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**. Spesso possibile trovare un collegamento a questa pagina visualizzano le informazioni dell'account e quindi cercare un collegamento, ad esempio **My domains**.

1. Dopo aver individuato la pagina di gestione del nome del dominio, cercare un collegamento che consente di modificare i record DNS. Potrebbero essere elencato come un **file di zona**, **I record DNS**, o un collegamento di configurazione **Avanzate** .

    * La pagina è probabile che avrà alcuni record già stato creato, ad esempio un'associazione di voce '**@**'o'\*' con una pagina "parcheggio dominio". Può inoltre contenere i record per comuni sottodomini, ad esempio **www**.
    * La pagina verrà menzionare **record CNAME**o fornire un elenco a discesa per selezionare un tipo di record. Potrebbe inoltre altri record, ad esempio **un record** e **record MX**. In alcuni casi, verranno chiamati record CNAME per altri nomi, ad esempio un **Alias Record**.
    * La pagina avrà anche i campi che consentono di **mappa** a un **nome Host** o il **nome di dominio** a un altro nome di dominio.

1. Anche se le specifiche di ogni registrar variano, in genere si esegue il mapping *dal* nome di dominio personalizzato (ad esempio **contoso.com**,) *per* il traffico Manager nome di dominio (**contoso.trafficmanager.net**) utilizzato per un'app web.

    > [AZURE.NOTE] In alternativa, se un record è già in uso ed è necessario associare preventivamente le app, è possibile creare un ulteriore record CNAME. Ad esempio, per associare in modo preemptive **www.contoso.com** a un'applicazione web, creare un record CNAME da **awverify.www** a **contoso.trafficmanager.net**. È quindi possibile aggiungere "www.contoso.com" all'App Web senza modificare il record CNAME "www". Per ulteriori informazioni, vedere [creare record DNS per un'app web in un dominio personalizzato][CREATEDNS].

1. Dopo avere finito di aggiungere o modificare i record DNS presso il registrar, salvare le modifiche.

<a name="enabledomain"></a>
## <a name="enable-traffic-manager"></a>Consentire il traffico Manager

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori Node](/develop/nodejs/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
