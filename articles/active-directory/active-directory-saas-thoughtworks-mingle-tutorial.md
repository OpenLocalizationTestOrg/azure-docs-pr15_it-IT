<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Thoughtworks combinare | Microsoft Azure" 
    description="Informazioni su come usare Thoughtworks combinare con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/11/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>Esercitazione: Integrazione di Azure Active Directory con Thoughtworks combinare
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e combinare Thoughtworks.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Thoughtworks combinare
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per combinare Thoughtworks
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario:")

##<a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>Attivare l'integrazione delle applicazioni per combinare Thoughtworks
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per combinare Thoughtworks.

###<a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per combinare Thoughtworks, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **thoughtworks combinare**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Thoughtworks tra**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Combinare Thoughtworks] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Combinare Thoughtworks")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione tra Thoughtworks con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato Thoughtworks tra.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Thoughtworks combinare **, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Thoughtworks combinare** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Thoughtworks tra Tenant URL** digitare l'URL utilizzando il modello seguente "*http://company.mingle.thoughtworks.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Thoughtworks combinare** fare clic su Download metadati e quindi salvarlo nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configurare il single sign-on")

5.  Accedere al sito aziendale **Thoughtworks combinare** come amministratore.

6.  Fare clic sulla scheda **amministratore** e quindi fare clic su **Configurazione Single Sign-on**.

    ![Configurazione di Single Sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "Configurazione di Single Sign-on")

7.  Nella sezione **Configurazione Single Sign-on** , procedere come segue:

    ![Configurazione di Single Sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "Configurazione di Single Sign-on")

    1.  Per caricare il file di metadati, fare clic su **Scegli file**.
    2.  Fare clic su **Salva modifiche**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per gli utenti AAD in grado di accedere, è necessario essere effettuato il provisioning all'applicazione Thoughtworks combinare utilizzando i relativi nomi utente di Azure Active Directory.  
Per combinare Thoughtworks, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al sito aziendale Thoughtworks combinare come amministratore.

2.  Fare clic su **profilo**.

    ![Il primo progetto] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Il primo progetto")

3.  Fare clic sulla scheda **amministratore** e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Utenti")

4.  Fare clic su **nuovo utente**.

    ![I nuovi utenti] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "I nuovi utenti")

5.  Nella pagina di finestra di dialogo **Nuovo utente** , procedere come segue:

    ![I nuovi utenti] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "I nuovi utenti")

    1.  Digitare il **nome di accesso**, **nome visualizzato**, **scegliere password**e **Conferma password** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Come **tipo di utente**, selezionare **l'utente completa**.
    3.  Fare clic su **creare il profilo**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Thoughtworks combinare utente account strumenti di creazione o API forniti da combinare Thoughtworks agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>Per assegnare gli utenti tra Thoughtworks, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Thoughtworks combinare** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
