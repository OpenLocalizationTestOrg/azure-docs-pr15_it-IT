<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Cherwell | Microsoft Azure" 
    description="Ecco come utilizzare Cherwell con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="10/14/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-cherwell"></a>Esercitazione: Integrazione di Azure Active Directory con Cherwell

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Cherwell. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Cherwell single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Cherwell sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Cherwell (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Cherwell
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-cherwell-tutorial/IC798988.png "Scenario:")
##<a name="enabling-the-application-integration-for-cherwell"></a>Attivare l'integrazione delle applicazioni per Cherwell

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Cherwell.

###<a name="to-enable-the-application-integration-for-cherwell-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Cherwell, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-cherwell-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-cherwell-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-cherwell-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Cherwell**.

    ![Cherwell] (./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")

7.  Nel riquadro dei risultati, selezionare **Cherwell**e quindi fare clic su **completa** per aggiungere l'applicazione.
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

    ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Cherwell con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Cherwell** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798990.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Cherwell** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798991.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-cherwell-tutorial/IC798992.png "Configurare URL di App")

    un.  Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere al **Cherwell** (ad esempio: *https://\<il nome della società\>.cherwellondemand.com/cherwellclient*).

    b.  Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in Cherwell** , procedere come segue:

    ![Configurare Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798993.png "Configurare Single Sign-On")

    un.  Fare clic su **Scarica certificato**e quindi salvare il certificato in locale nel computer in uso.

    b.  Copiare l' **URL del Provider di identità**.

    c.  Copiare l' **URL del servizio Single Sign-On**.

    d.  Fare clic su **Avanti**.

5.  Inviare il certificato scaricato, l' **URL del Provider di identità** e l' **URL del servizio Single Sign-On** per il team di supporto Cherwell.

    >[AZURE.NOTE] Il team di supporto Cherwell deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-cherwell-tutorial/IC798994.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a Cherwell, è necessario essere effettuato il provisioning in Cherwell.  
Nel caso Cherwell, è necessario che gli account utente creati dal team di supporto Cherwell.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Cherwell utente account strumenti di creazione o API fornite dal Cherwell effettuare il provisioning di Azure Active Directory gli account utente.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-cherwell-perform-the-following-steps"></a>Per assegnare gli utenti a Cherwell, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Cherwell** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-cherwell-tutorial/IC798995.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-cherwell-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
