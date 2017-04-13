<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Salesforce | Microsoft Azure"
    description="Ecco come utilizzare Salesforce con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro."
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

#<a name="tutorial-how-to-integrate-salesforce-with-azure-active-directory"></a>Esercitazione: Come integrare Salesforce con Azure Active Directory

In questa esercitazione illustra come connettere l'ambiente di Salesforce di Azure Active Directory. Si apprenderà come configurare single sign-on per Salesforce, come abilitare il provisioning automatico dell'utente e come assegnare agli utenti di accedere a Salesforce.

##<a name="prerequisites"></a>Prerequisiti

1. Per accedere a Azure Active Directory tramite il [portale classica Azure](https://manage.windowsazure.com), avere un abbonamento valido a Azure.

2. È necessario avere un tenant valido in [Salesforce.com](https://www.salesforce.com/).

> [AZURE.IMPORTANT] Se si utilizza un account di **prova** Salesforce.com, sarà in grado di configurare il provisioning automatico dell'utente. Valutazione account non dispone dell'accesso all'API necessario abilitato fino a cui sono stati acquistati.
> 
> È possibile aggirare questa limitazione utilizzando un [account sviluppatore gratuito](https://developer.salesforce.com/signup) per completare questa esercitazione.

Se si utilizza un ambiente Salesforce Sandbox, vedere l' [esercitazione integrazione Salesforce Sandbox](https://go.microsoft.com/fwLink/?LinkID=521879).

##<a name="video-tutorials"></a>Esercitazioni video

Seguire questa esercitazione utilizzando i video riportata di seguito.

**Esercitazione video parte uno: come abilitare Single Sign-On**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**Esercitazione video seconda parte: Come automatizzare il Provisioning dell'utente**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##<a name="step-1-add-salesforce-to-your-directory"></a>Passaggio 1: Aggiungere Salesforce alla directory

1. Nel [portale classica Azure](https://manage.windowsazure.com), nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Nel riquadro di spostamento sinistro, selezionare Active Directory.][0]

2. Nell'elenco di **Directory** , selezionare la directory che si desidera aggiungere Salesforce a.

3. Fare clic su **applicazioni** nel menu superiore.

    ![Fare clic su applicazioni.][1]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Fare clic su Aggiungi per aggiungere una nuova applicazione.][2]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Fare clic su Aggiungi un'applicazione dalla raccolta.][3]

6. Nella **casella Cerca**digitare **Salesforce**. Selezionare **Salesforce** tra i risultati, quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Aggiungere Salesforce.][4]

7. Verrà visualizzata la pagina Guida introduttiva per Salesforce:

    ![Pagina Guida introduttiva di Salesforce in Azure Active Directory][5]

##<a name="step-2-enable-single-sign-on"></a>Passaggio 2: Abilitare il single sign-on

1. Prima di configurare il single sign-on, è necessario configurare e distribuire un dominio personalizzato per l'ambiente di Salesforce. Per istruzioni su come eseguire questa operazione, vedere [Impostare un nome di dominio](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US).

2. Nella pagina Guida introduttiva di Salesforce in Azure Active Directory, fare clic sul pulsante **Configura il single sign-on** .

    ![Configura single sign-on pulsante][6]

3. Verrà aperta una finestra di dialogo e verrà visualizzata una schermata che chiede "Specificare come gli utenti di accedere al Salesforce?" Selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Selezionare Azure Active Directory Single Sign-On][7]

    > [AZURE.NOTE] Per altre informazioni sulle diverse single sign-on opzioni, [fare clic qui](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

4. Nella pagina **Configura impostazioni di App** compilare l' **Accesso in URL** digitare l'URL di dominio Salesforce nel formato seguente:
 - Account dell'organizzazione:`https://<domain>.my.salesforce.com`
 - Account sviluppatore:`https://<domain>-dev-ed.my.salesforce.com` 

    ![Digitare l'URL di accesso][8]

5. Nella pagina **Configura il single sign-on in Salesforce** fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Scaricare certificato][9]

6. Aprire una nuova scheda nel browser e accedere a proprio account di amministratore di Salesforce.

7. Sotto il riquadro di spostamento di **amministratore** , fare clic su **Controlli di protezione** per espandere la sezione correlata. Fare clic su **Impostazioni servizio Single Sign-On**.

    ![Fare clic su impostazioni Single Sign-On in controlli di sicurezza][10]

8. Nella pagina **Impostazioni servizio Single Sign-On** , fare clic sul pulsante **Modifica** .

    ![Fare clic sul pulsante Modifica][11]

    > [AZURE.NOTE] Se non è possibile attivare le impostazioni di Single Sign-On per l'account di Salesforce, potrebbe essere necessario contattare il supporto di Salesforce per installare la funzionalità abilitata per l'utente.

9. Selezionare **SAML abilitato**e quindi fare clic su **Salva**.

    ![Selezionare SAML abilitata][12]

10. Per configurare il SAML single sign-on impostazioni, fare clic su **Nuovo**.

    ![Selezionare SAML abilitata][13]

11. Nella pagina **SAML Single Sign-On impostazione modifica** verificare le configurazioni seguenti:

    ![Screenshot che mostra le configurazioni da considerare][14]

 - Per il campo **nome** digitare un nome descrittivo per la configurazione. Fornire un valore per **nome** inserire automaticamente il **Nome dell'API** di testo.

 - In Azure Active Directory, copiare il valore **dell'URL emittente** e quindi incollarla nel campo **emittente** Salesforce.

 - Nella **casella di testo Id entità**, digitare il nome di dominio Salesforce utilizzando il modello seguente:
     - Account dell'organizzazione:`https://<domain>.my.salesforce.com`
     - Account sviluppatore:`https://<domain>-dev-ed.my.salesforce.com`

 - Fare clic su **Sfoglia** o **Selezionare il File** per aprire la finestra di dialogo **Selezionare il File da caricare** , selezionare il certificato di Salesforce e quindi fare clic su **Apri** per caricare il certificato.

 - Per **Tipo di identità SAML**selezionare **asserzione contiene nome salesforce.com dell'utente**.

 - **Posizione di identità SAML**, seleziona **l'identità nell'elemento NameIdentifier dell'istruzione oggetto**

 - In Azure Active Directory, copiare il valore di **URL di accesso remoto** e quindi incollarla nel campo **URL di accesso del Provider di identità** Salesforce.

 - Per i **Servizi avviati richiedere associazione Provider**, selezionare **Reindirizzamento HTTP**.

 - Infine, fare clic su **Salva** per applicare le SAML single sign-on impostazioni.

12. Nel riquadro di spostamento sinistra di Salesforce, fare clic su **Gestione dei domini** per espandere la sezione correlata e quindi fare clic su **Risorse del dominio**.

    ![Fare clic su nel dominio personalizzato][15]

13. Scorrere fino alla sezione **Configurazione di autenticazione** e fare clic sul pulsante **Modifica** .

    ![Fare clic sul pulsante Modifica][16]

14. Nella sezione **Servizio di autenticazione** selezionare il nome della configurazione SAML SSO e quindi fare clic su **Salva**.

    ![Selezionare la configurazione di Single Sign-on][17]

    > [AZURE.NOTE] Se è selezionato più di un servizio di autenticazione, quindi quando gli utenti tentano di avviare servizio single sign-on all'ambiente di Salesforce vengano verrà richiesto di selezionare il servizio di autenticazione desiderano per l'accesso. Se non si vuole a tale scopo, è necessario **lasciare tutti gli altri servizi di autenticazione deselezionate**.

15. In Azure Active Directory, selezionare la casella di controllo Conferma configurazione single sign-on per abilitare il certificato caricati in Salesforce. Fare clic su **Avanti**.

    ![Selezionare la casella di controllo conferma][18]

16. Nella pagina finale della finestra di dialogo, digitare un indirizzo di posta elettronica se si desidera ricevere notifiche tramite posta elettronica per gli errori e avvisi relativi al mantenimento di questa configurazione single sign-on. 

    ![Digitare l'indirizzo di posta elettronica.][19]

17. Fare clic su **completa** per chiudere la finestra di dialogo. Per verificare la configurazione, vedere la sezione successiva intitolata [Assegnare agli utenti di Salesforce](#step-4-assign-users-to-salesforce).

##<a name="step-3-enable-automated-user-provisioning"></a>Passaggio 3: Abilitare il provisioning automatico dell'utente

1. Nella pagina di Azure Active Directory Guida introduttiva per Salesforce, fare clic sul pulsante **Configura il provisioning dell'utente** .

    ![Fare clic sul pulsante configurare il Provisioning dell'utente][20]

2. Nella finestra di dialogo **Configura il provisioning dell'utente** , digitare il nome utente amministratore Salesforce e la password.

    ![Digitare il nome utente amministratore o la password][21]

    > [AZURE.NOTE] Se si sta configurando un ambiente di produzione, la procedura consigliata consiste nel creare un nuovo account di amministratore in Salesforce specificamente per questo passaggio. Questo account deve disporre il profilo di **Amministratore di sistema** assegnato a tale Salesforce.

3. Per ottenere la sicurezza di Salesforce token, aprire una nuova scheda ed effettuare l'accesso nello stesso account di amministratore di Salesforce. Nell'angolo superiore destro della pagina, fare clic sul proprio nome e quindi fare clic su **Impostazioni personali**.

    ![Fare clic sul proprio nome, quindi fare clic su impostazioni personali][22]

4. Nel riquadro di spostamento sinistra, fare clic su **personale** per espandere la sezione correlata e quindi fare clic su **Reimposta Token protezione personale**.

    ![Fare clic sul proprio nome, quindi fare clic su impostazioni personali][23]

5. Nella pagina **Reimposta Token sicurezza personale** , fare clic sul pulsante **Reimposta Token di sicurezza** .

    ![Leggere gli avvisi.][24]

6. Selezionare la cartella Posta in arrivo associato a questo account di amministratore. Cercare un messaggio di posta elettronica da Salesforce.com che contiene il nuovo token di sicurezza.

7. Copiare il token, passare alla finestra di Azure Active Directory e incollarla nel campo **Token di sicurezza utente** . Fare clic su **Avanti**.

    ![Incollare nel token di sicurezza][25]

8. Nella pagina di conferma, è possibile ricevere notifiche tramite posta elettronica per quando si verificano errori di provisioning. Fare clic su **completa** per chiudere la finestra di dialogo.

    ![Digitare l'indirizzo di posta elettronica per ricevere notifiche][26]

##<a name="step-4-assign-users-to-salesforce"></a>Passaggio 4: Assegnare agli utenti di Salesforce

1. Per verificare la configurazione, iniziare creando un nuovo account di prova nella directory.

2. Nella pagina Salesforce avvio rapido, fare clic sul pulsante **Assegna utenti** .

    ![Fare clic su Assegna utenti][27]

3. Selezionare l'utente test e fare clic sul pulsante **assegnare** nella parte inferiore dello schermo:

 - Se non è attiva il provisioning automatico dell'utente, verrà visualizzato il seguente messaggio di conferma:

        ![Confirm the assignment.][28]

 - Se è stata attivata di provisioning automatico dell'utente, verrà visualizzato un messaggio per definire il tipo di profilo di Salesforce l'utente deve disporre. Gli utenti appena provisioning dovrebbero essere visualizzate nel proprio ambiente Salesforce dopo alcuni minuti.

        ![Confirm the assignment.][29]

        > [AZURE.IMPORTANT] Se provisioning in un ambiente di **sviluppo** Salesforce, si avrà un numero limitato di licenze disponibili per ogni profilo. Di conseguenza, è preferibile il provisioning degli utenti per il profilo **Chatter gratuito utente** che ha 4,999 licenze disponibili.

4. Per verificare le impostazioni single sign-on, aprire il pannello di accesso in [https://myapps.microsoft.com](https://myapps.microsoft.com/), quindi accedere con l'account di prova e fare clic su **Salesforce**.

##<a name="related-articles"></a>Articoli correlati

- [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
- [Elenco di esercitazioni su come integrare SaaS App](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png
