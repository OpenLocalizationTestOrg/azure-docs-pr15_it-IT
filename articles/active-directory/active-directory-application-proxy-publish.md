<properties
    pageTitle="Pubblicare App con Azure Active Directory applicazione Proxy | Microsoft Azure"
    description="Pubblicare le applicazioni in locale nel cloud con Azure Active Directory applicazione Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Pubblicare le applicazioni che utilizzano Proxy di applicazione Azure Active Directory

Azure Active Directory applicazione Proxy consentono di supportare gli utenti remoti pubblicando applicazioni locale per essere accessibili tramite internet. Da questo punto è già [attivato Proxy di applicazione nel portale di classica Azure](active-directory-application-proxy-enable.md). In questo articolo sono illustrati i passaggi per la pubblicazione di applicazioni in eseguono nella rete locale e forniscono l'accesso remoto sicuro dall'esterno della rete. Al termine di questo articolo è pronto per la configurazione dell'applicazione con informazioni personalizzate o requisiti di sicurezza.

> [AZURE.NOTE] Proxy applicazione è una caratteristica è disponibile solo se è stato aggiornato a premio o edizione di base di Azure Active Directory. Per ulteriori informazioni, vedere [edizioni di Azure Active Directory](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Pubblicare un'applicazione con la procedura guidata

1. Accedere come amministratore nel [portale classica Azure](https://manage.windowsazure.com/).
2. Passare a Active Directory e selezionare la directory è stato abilitato il Proxy dell'applicazione.

    ![Active Directory: icona](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Fare clic sulla scheda **applicazioni** e quindi fare clic sul pulsante **Aggiungi** nella parte inferiore dello schermo

    ![Aggiunta dell'applicazione](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Selezionare **pubblica un'applicazione che sarà accessibile dall'esterno alla rete aziendale**.

    ![Pubblicare un'applicazione che sarà accessibile dall'esterno alla rete aziendale](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Specificare le informazioni seguenti sull'applicazione:

    - **Nome**: il nome descrittivo per l'applicazione. Deve essere univoco all'interno della directory.
    - **URL interno**: l'indirizzo dell'applicazione Proxy Connector viene usata per accedere all'applicazione all'interno della rete privata. È possibile fornire un percorso specifico nel server di back-end per pubblicare, mentre il resto del server è non pubblicato. In questo modo, è possibile pubblicare siti diversi nello stesso server e assegnare ognuno di essi alle regole il proprio nome e l'accesso.

        > [AZURE.TIP] Se si pubblica un percorso, assicurarsi che includa tutte le immagini necessarie, script e i fogli di stile per l'applicazione. Ad esempio, se l'app in https://yourapp/app e utilizza le immagini che si trova in https://yourapp/media, è consigliabile pubblicare https://yourapp/ come percorso.

    - **Metodo di autenticazione preliminare**: come Proxy di applicazione verifica gli utenti prima di consentire l'accesso all'applicazione. Scegliere una delle opzioni dal menu a discesa.

        - Azure Active Directory: Proxy applicazione reindirizza agli utenti di accedere con Azure Active Directory, che consente di autenticare le autorizzazioni per la directory e l'applicazione.
        - Pass-through: Gli utenti non sono necessario eseguire l'autenticazione per accedere all'applicazione.

    ![Proprietà delle applicazioni](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Per completare la procedura guidata, fare clic sul segno di spunta nella parte inferiore dello schermo. L'applicazione a questo punto è definita in Azure Active Directory.


## <a name="assign-users-and-groups-to-the-application"></a>Assegnare utenti e gruppi all'applicazione

Affinché agli utenti di accedere all'applicazione pubblicato, è necessario assegnarle singolarmente o in gruppi. (Ricordarsi di assegnarsi access troppo). È necessario che ogni utente dispone di una licenza per Azure base o versione successiva. È possibile assegnare licenze singolarmente o ai gruppi. Per ulteriori informazioni, vedere [assegnazione di utenti a un'applicazione](active-directory-applications-guiding-developers-assigning-users.md) . 

Per le applicazioni che richiedono preliminare, si concedono le autorizzazioni per usare l'app. Per le applicazioni che non richiedono preliminare, gli utenti possono ancora essere assegnati l'app in modo che venga visualizzato nell'elenco di applicazioni, ad esempio App personali.

1. Dopo aver completato la procedura guidata aggiungere App, viene visualizzata la pagina avvio rapido per l'applicazione. Per gestire chi ha accesso all'app, selezionare **utenti e gruppi**.

    ![Guida introduttiva di applicazione Proxy assegnare agli utenti - schermata](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Cercare gruppi specifici nella directory o Mostra tutti gli utenti. Per visualizzare i risultati della ricerca, fare clic sul segno di spunta.

    ![Ricerca di gruppi o utenti - schermata](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Selezionare ogni utente o gruppo che si desidera assegnare per questa app e fare clic su **Assegna**. Viene chiesto di confermare l'azione.

> [AZURE.NOTE] Per le applicazioni di Windows integrata, è possibile assegnare solo gli utenti e gruppi sincronizzati da Active Directory locale. Gli utenti che accedere con un account Microsoft e gli utenti guest non assegnati per le applicazioni pubblicate con Proxy di Azure Active Directory dell'applicazione. Assicurarsi che gli utenti accedere con le credenziali che fanno parte dello stesso dominio app che viene pubblicato.

## <a name="test-your-published-application"></a>Testare l'applicazione pubblicata

Dopo aver pubblicato l'applicazione, è possibile verificare i posizionandosi l'URL è stato pubblicato. Verificare che sia accessibile, che visualizzi correttamente e che tutto funzioni come previsto. Se si riscontrano problemi o messaggio di errore, provare a [Guida alla risoluzione dei](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurare l'applicazione

È possibile modificare App pubblicato o impostare le opzioni avanzate nella pagina Configura. In questa pagina, è possibile personalizzare l'app per la modifica del nome o il caricamento di un logo. È anche possibile gestire le regole di accesso ad esempio il metodo preliminare o l'autenticazione a più fattori.

![Configurazione avanzata](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Dopo la pubblicazione di applicazioni tramite Proxy applicazione di Azure Active Directory, vengono visualizzati nell'elenco delle applicazioni in Azure Active Directory e gestirli sono.

Se si disabilitino servizi Proxy applicazione dopo averlo applicazioni, non sono più accessibile dall'esterno della rete privata. Questa operazione non viene eliminato le applicazioni.

Per visualizzare un'applicazione e assicurarsi che sia accessibile, fare doppio clic sul nome dell'applicazione. Se il servizio Proxy di applicazione è disabilitato e l'applicazione non è disponibile, nella parte superiore dello schermo viene visualizzato un messaggio di avviso.

Per eliminare un'applicazione, selezionare un'applicazione nell'elenco e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- [Pubblicare le applicazioni che utilizzano il nome di dominio](active-directory-application-proxy-custom-domains.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Abilitare l'accesso condizionale](active-directory-application-proxy-conditional-access.md)
- [Utilizzo di applicazioni sulle attestazioni](active-directory-application-proxy-claims-aware-apps.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
