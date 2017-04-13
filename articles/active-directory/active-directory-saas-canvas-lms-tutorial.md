<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con area LMS | Microsoft Azure" 
    description="Informazioni su come utilizzare LMS area di disegno con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>Esercitazione: Integrazione di Azure Active Directory con area LMS

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e area di disegno.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di area di disegno

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a area di lavoro sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di area di lavoro (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per area di disegno
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "Scenario:")
##<a name="enabling-the-application-integration-for-canvas"></a>Attivare l'integrazione delle applicazioni per area di disegno

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per area di disegno.

###<a name="to-enable-the-application-integration-for-canvas-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per area di lavoro, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **area di disegno**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **l'area**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Area di disegno] (./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Area di disegno")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione all'area di disegno con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Per configurare single sign-on per area di lavoro è necessario recuperare un valore da un certificato.  
Se non ha familiarità con questa procedura, informazioni [su come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **area di disegno** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "Configurare il single sign-on")

2.  Nella pagina **specificare come agli utenti di accedere all'area di disegno** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** nella casella di testo **Area URL di accesso** , digitare l'URL utilizzando il seguente criterio `https://<tenant-name>.instructure.com`e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on all'area di disegno** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito area di disegno della società.

6.  Passare a **corsi \> gestite account \> Microsoft**.

    ![Area di disegno] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Area di disegno")

7.  Nel riquadro di spostamento a sinistra, selezionare **l'autenticazione**e quindi fare clic su **Aggiungi nuovo SAML Config**.

    ![Autenticazione] (./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "Autenticazione")

8.  Nella pagina corrente integrazione, procedere come segue:

    ![Integrazione corrente] (./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Integrazione corrente")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on all'area di lavoro** copiare il valore di **ID entità** e quindi incollarla nella casella di testo **ID entità IdP** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on all'area di lavoro** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **Log su URL** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on all'area di lavoro** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **Log all'URL** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on all'area di lavoro** copiare il valore **Cambia Password URL** e quindi incollarla nella casella di testo **Collegamento Cambia Password** .
    5.  Copiare il valore di **identificazione personale** il certificato esportato e quindi incollarla nella casella di testo **ID digitale di certificato** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come recuperare il valore di identificazione personale del certificato](http://youtu.be/YKQF266SAxI)

    6.  Nell'elenco **Degli attributi di accesso** selezionare **NameID**.
    7.  Nell'elenco **Formato identificatore** selezionare **emailAddress**.
    8.  Fare clic su **Salva impostazioni di autenticazione**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory accedere all'area di lavoro, è necessario essere effettuato il provisioning nell'area di disegno.  
In caso di area di lavoro, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **area di disegno** .

2.  Passare a **corsi \> gestite account \> Microsoft**.

    ![Area di disegno] (./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Area di disegno")

3.  Fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "Utenti")

4.  Fare clic su **Aggiungi nuovo utente**.

    ![Utenti] (./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "Utenti")

5.  Nella pagina Aggiungi un nuovo utente finestra di dialogo, procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "Aggiunta utente")

    1.  Nella casella di testo **Nome e cognome** , digitare il nome dell'utente.
    2.  Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica dell'utente.
    3.  Nella casella di testo di **accesso** , digitare l'indirizzo di posta elettronica di Azure Active Directory dell'utente.
    4.  Selezionare **l'utente sulla creazione di account di posta elettronica**.
    5.  Fare clic su **Aggiungi utente**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro disegno utente account strumenti di creazione o API fornite dall'area di disegno per gli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-canvas-perform-the-following-steps"></a>Per assegnare gli utenti all'area di disegno, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **area di disegno **, fare clic su **assegnare agli utenti**.

    ![Assegnazione di utenti] (./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assegnazione di utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
