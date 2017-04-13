<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con NetSuite | Microsoft Azure"
    description="Ecco come utilizzare NetSuite con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#<a name="tutorial-how-to-integrate-netsuite-with-azure-active-directory"></a>Esercitazione: Come integrare NetSuite con Azure Active Directory

In questa esercitazione illustra come connettere l'ambiente NetSuite di Azure Active Directory (Azure Active Directory). Si apprenderà come configurare single sign-on per NetSuite, come abilitare il provisioning automatico dell'utente e come assegnare agli utenti di accedere a NetSuite. 

##<a name="prerequisites"></a>Prerequisiti

1. Per accedere a Azure Active Directory tramite il [portale classica Azure](https://manage.windowsazure.com), avere un abbonamento valido a Azure.

2. È necessario avere accesso amministrativo a un abbonamento [NetSuite](http://www.netsuite.com/portal/home.shtml) . È possibile utilizzare un account di valutazione gratuito per dei servizi.

##<a name="step-1-add-netsuite-to-your-directory"></a>Passaggio 1: Aggiungere NetSuite alla Directory

1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Nel riquadro di spostamento sinistro, selezionare Active Directory.][0]

2. Nell'elenco di **Directory** , selezionare la directory che si desidera aggiungere NetSuite a.

3. Fare clic su **applicazioni** nel menu superiore.

    ![Fare clic su applicazioni.][1]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]

6. Nella **casella Cerca**digitare **NetSuite**. Selezionare **NetSuite** tra i risultati, quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Aggiungere NetSuite.][4]

7. Verrà visualizzata la pagina Guida introduttiva per NetSuite:

    ![Pagina Guida introduttiva di NetSuite in Azure Active Directory][5]

##<a name="step-2-enable-single-sign-on"></a>Passaggio 2: Abilitare Single Sign-On

1. In Azure Active Directory, nella pagina Guida introduttiva per NetSuite, fare clic sul pulsante **Configura il single sign-on** .

    ![Configura single sign-on pulsante][6]

2. Verrà aperta una finestra di dialogo e verrà visualizzata una schermata che chiede "Specificare come gli utenti di accedere al NetSuite?" Selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Selezionare Azure Active Directory Single Sign-On][7]

    > [AZURE.NOTE] Per altre informazioni sulle diverse single sign-on opzioni, [fare clic qui](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)

3. Nella pagina **Configura impostazioni di App** per il campo di **URL di risposta** , digitare l'URL di tenant NetSuite utilizzando uno dei formati seguenti:
    - `https://<tenant-name>.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.netsuite.com/saml2/acs`
    - `https://<tenant-name>.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs`
    - `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    ![Digitare l'URL tenant][8]

4. Nella pagina **Configura il single sign-on in NetSuite** fare clic su **Download dei metadati**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Scaricare i metadati.][9]

5. Aprire una nuova scheda nel browser e accedere a sito della società Netsuite come amministratore.

6. Nella barra degli strumenti nella parte superiore della pagina, fare clic su **Impostazioni**e quindi fare clic su **Gestione configurazione**.

    ![Passare a Gestione guidata installazione][10]

7. Selezionare **l'integrazione**dall'elenco **Attività di configurazione** .

    ![Andare all'integrazione][11]

8. Nella sezione **Gestione autenticazione** , fare clic su **SAML Single Sign-on**.

    ![Passare a SAML Single Sign-on][12]

9. Nella pagina **Configurazione SAML** , procedere come segue:

    - Azure Active Directory, copiare il valore di **URL di accesso remoto** e incollarla nel campo **Pagina di accesso del Provider di identità** NetSuite.

        ![Pagina di configurazione SAML.][13]

    - In NetSuite, selezionare **Il metodo di autenticazione principale**.

    - Per il campo etichettato **Metadati del Provider di identità SAMLV2**, selezionare **Carica File di metadati IDP**. Fare clic su **Sfoglia** per caricare il file di metadati che è stato scaricato nel passaggio 4 #.

        ![Caricare i metadati][16]

    - Fare clic su **Invia**.

9. In Azure Active Directory, selezionare la casella di controllo Conferma configurazione single sign-on per abilitare il certificato caricati in NetSuite. Fare clic su **Avanti**.

    ![Selezionare la casella di controllo conferma][14]

10. Nella pagina finale della finestra di dialogo, digitare un indirizzo di posta elettronica se si desidera ricevere notifiche tramite posta elettronica per gli errori e avvisi relativi al mantenimento di questa configurazione single sign-on. 

    ![Digitare l'indirizzo di posta elettronica.][15]

11. Fare clic su **completa** per chiudere la finestra di dialogo. Fare clic su **attributi** nella parte superiore della pagina.

    ![Fare clic su attributi.][17]

12. Fare clic su **Aggiungi attributo utente**.

    ![Fare clic su Aggiungi attributo utente.][18]

13. Per il campo **Nome attributo** , digitare nella `account`. Per il campo **Valore dell'attributo** , digitare l'ID NetSuite account. Di seguito sono riportate le istruzioni su come trovare l'ID account:

    ![Aggiungere il proprio ID Account di NetSuite.][19]

    - In NetSuite, scegliere **il programma di installazione** dal menu di spostamento superiore.
    - Quindi fare clic su nella sezione **Attività di configurazione** del menu di spostamento sinistra, selezionare la sezione **integrazione** e fare clic su **Preferenze di servizi Web**.
    - Copiare l'ID Account NetSuite e incollarlo sul campo **Valore dell'attributo** in Azure Active Directory.

        ![Ottenere il numero dell'account][20]

14. In Azure Active Directory, fare clic su **completa** per completare l'aggiunta dell'attributo SAML. Quindi fare clic su **Apply Changes** nel menu nella parte inferiore.

    ![Salvare le modifiche.][21]

15. Prima che gli utenti possono eseguire il single sign-on in NetSuite, vengano deve essere assegnati le autorizzazioni appropriate nel NetSuite. Seguire le istruzioni seguenti per assegnare le autorizzazioni.

    - Menu barra di spostamento superiore fare clic su **Impostazioni**, quindi fare clic su **Gestione configurazione**.

        ![Passare a Gestione guidata installazione][10]

    - Nel menu di spostamento sinistro, selezionare **Gli utenti/ruoli**, quindi scegliere **Gestione ruoli**.

        ![Passare a gestire i ruoli][22]

    - Fare clic su **nuovo ruolo**.

    - Digitare un **nome** per ruolo nuovo e selezionare la casella di controllo **Single Sign-On solo** .

        ![Assegnare un nome nuovo ruolo.][23]

    - Fare clic su **Salva**.

    - Nel menu nella parte superiore, fare clic su **autorizzazioni**. Fare clic su **Imposta**.

        ![Passare a autorizzazioni][24]

    - Selezionare **Imposta la Giorgio Single Sign-on**e quindi fare clic su **Aggiungi**.

    - Fare clic su **Salva**.

    - Menu barra di spostamento superiore fare clic su **Impostazioni**, quindi fare clic su **Gestione configurazione**.

        ![Passare a Gestione guidata installazione][10]

    - Nel menu di spostamento sinistro, selezionare **Gli utenti/ruoli**, quindi fare clic su **Gestisci utenti**.

        ![Passare a gestire gli utenti][25]

    - Selezionare un utente di prova. Fare clic su **Modifica**.

        ![Passare a gestire gli utenti][26]

    - Nella finestra di dialogo ruoli, selezionare il ruolo è stato creato e fare clic su **Aggiungi**.

        ![Passare a gestire gli utenti][27]

    - Fare clic su **Salva**.

19. Per verificare la configurazione, vedere la sezione successiva intitolata [Assegnare agli utenti di NetSuite](#step-4-assign-users-to-netsuite).

##<a name="step-3-enable-automated-user-provisioning"></a>Passaggio 3: Abilitare il Provisioning automatico dell'utente

> [AZURE.NOTE] Per impostazione predefinita, gli utenti di provisioning verranno aggiunto alla filiale principale dell'ambiente NetSuite.

1. Azure Active Directory, nella pagina Guida introduttiva per NetSuite, scegliere **Configura il provisioning dell'utente**.

    ![Configurare il provisioning dell'utente][28]

2. Nella finestra di dialogo visualizzata digitare le credenziali di amministratore per NetSuite, quindi fare clic su **Avanti**.

    ![Digitare le credenziali di amministratore NetSuite.][29]

3. Nella pagina di conferma, digitare l'indirizzo di posta elettronica per ricevere notifiche relative a errori di provisioning.

    ![Per confermare.][30]

4. Fare clic su **completa** per chiudere la finestra di dialogo.

##<a name="step-4-assign-users-to-netsuite"></a>Passaggio 4: Assegnare utenti ai NetSuite

1. Per verificare la configurazione, iniziare a creare un nuovo account di prova nella directory.

2. Nella pagina NetSuite avvio rapido, fare clic sul pulsante **Assegna utenti** .

    ![Fare clic su Assegna utenti][31]

3. Selezionare l'utente test e fare clic sul pulsante **assegnare** nella parte inferiore dello schermo:

 - Se non è attiva il provisioning automatico dell'utente, verrà visualizzato il seguente messaggio di conferma:

        ![Confirm the assignment.][32]

 - Se è stata attivata di provisioning automatico dell'utente, verrà visualizzato un messaggio per definire il tipo di ruolo l'utente deve disporre di NetSuite. Gli utenti appena provisioning dovrebbero essere visualizzate nel proprio ambiente NetSuite dopo alcuni minuti.

4. Per verificare le impostazioni single sign-on, aprire il pannello di accesso in [https://myapps.microsoft.com](https://myapps.microsoft.com/), accedere con l'account di prova e fare clic su **NetSuite**.

##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-netsuite-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-netsuite-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-netsuite-tutorial/add-app.png
[3]: ./media/active-directory-saas-netsuite-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-netsuite-tutorial/add-netsuite.png
[5]: ./media/active-directory-saas-netsuite-tutorial/quick-start-netsuite.png
[6]: ./media/active-directory-saas-netsuite-tutorial/config-sso.png
[7]: ./media/active-directory-saas-netsuite-tutorial/sso-netsuite.png
[8]: ./media/active-directory-saas-netsuite-tutorial/sso-config-netsuite.png
[9]: ./media/active-directory-saas-netsuite-tutorial/config-sso-netsuite.png
[10]: ./media/active-directory-saas-netsuite-tutorial/ns-setup.png
[11]: ./media/active-directory-saas-netsuite-tutorial/ns-integration.png
[12]: ./media/active-directory-saas-netsuite-tutorial/ns-saml.png
[13]: ./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png
[14]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-confirm.png
[15]: ./media/active-directory-saas-netsuite-tutorial/sso-email.png
[16]: ./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png
[17]: ./media/active-directory-saas-netsuite-tutorial/ns-attributes.png
[18]: ./media/active-directory-saas-netsuite-tutorial/ns-add-attribute.png
[19]: ./media/active-directory-saas-netsuite-tutorial/ns-add-account.png
[20]: ./media/active-directory-saas-netsuite-tutorial/ns-account-id.png
[21]: ./media/active-directory-saas-netsuite-tutorial/ns-save-saml.png
[22]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-roles.png
[23]: ./media/active-directory-saas-netsuite-tutorial/ns-new-role.png
[24]: ./media/active-directory-saas-netsuite-tutorial/ns-sso.png
[25]: ./media/active-directory-saas-netsuite-tutorial/ns-manage-users.png
[26]: ./media/active-directory-saas-netsuite-tutorial/ns-edit-user.png
[27]: ./media/active-directory-saas-netsuite-tutorial/ns-add-role.png
[28]: ./media/active-directory-saas-netsuite-tutorial/netsuite-provisioning.png
[29]: ./media/active-directory-saas-netsuite-tutorial/ns-creds.png
[30]: ./media/active-directory-saas-netsuite-tutorial/ns-confirm.png
[31]: ./media/active-directory-saas-netsuite-tutorial/assign-users.png
[32]: ./media/active-directory-saas-netsuite-tutorial/assign-confirm.png
