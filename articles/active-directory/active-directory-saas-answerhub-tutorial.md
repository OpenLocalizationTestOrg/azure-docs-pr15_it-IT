<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con AnswerHub | Microsoft Azure" 
    description="Ecco come utilizzare AnswerHub con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Esercitazione: Integrazione di Azure Active Directory con AnswerHub

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software).  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a AnswerHub sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di AnswerHub (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per AnswerHub
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario:")

##<a name="enabling-the-application-integration-for-answerhub"></a>Attivare l'integrazione delle applicazioni per AnswerHub

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per AnswerHub.

###<a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per AnswerHub, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-answerhub-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-answerhub-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **AnswerHub**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-answerhub-tutorial/IC785166.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **AnswerHub**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![AnswerHub] (./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a AnswerHub con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **AnswerHub** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al AnswerHub** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **AnswerHub URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://company.answerhub.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in AnswerHub** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società AnswerHub come amministratore.
    >[AZURE.NOTE] Se occorre assistenza nella configurazione AnswerHub, contattare il [team di supporto dell'AnswerHub](mailto:success@answerhub.com. ).








6.  Passare alla pagina di **amministrazione**.

7.  Fare clic sulla scheda **utenti e gruppi** .

8.  Nel riquadro di spostamento sul lato sinistro, nella sezione **Impostazioni di social networking** , fare clic su **Imposta SAML**.

9.  Fare clic sulla scheda **IDP Config** .

10. Nella scheda **Configurazione IDP** , procedere come segue:

    ![Configurazione SAML] (./media/active-directory-saas-answerhub-tutorial/IC785172.png "Configurazione SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in AnswerHub** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso IDP** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in AnswerHub** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione IDP** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in AnswerHub** copiare il valore di **Nome identificatore di formato** e quindi incollarla nella casella di testo **IDP nome identificatore formato** .
    4.  Fare clic su **certificati e le chiavi**.

11. Nella scheda certificati e le chiavi, procedere come segue:

    ![Chiavi e certificati] (./media/active-directory-saas-answerhub-tutorial/IC785173.png "Chiavi e certificati")

    1.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    2.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **IDP chiave pubblica (x 509 formato)** .
    3.  Fare clic su **Salva**.

12. Nella scheda **Configurazione IDP** fare clic su **Salva**.

13. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a AnswerHub, è necessario essere effettuato il provisioning in AnswerHub.  
In caso di AnswerHub, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale **AnswerHub** come amministratore.

2.  Passare alla pagina di **amministrazione**.

3.  Fare clic sulla scheda **utenti e gruppi** .

4.  Nel riquadro di spostamento sul lato sinistro, nella sezione **Gestisci utenti** fare clic su **creare o importare gli utenti**.

    ![Utenti e gruppi] (./media/active-directory-saas-answerhub-tutorial/IC785175.png "Utenti e gruppi")

5.  Digitare **l'indirizzo di posta elettronica**, **nome utente** e la **Password** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati e quindi fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro AnswerHub utente account strumenti di creazione o API fornite dall'AnswerHub agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>Per assegnare gli utenti a AnswerHub, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **AnswerHub **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-answerhub-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
