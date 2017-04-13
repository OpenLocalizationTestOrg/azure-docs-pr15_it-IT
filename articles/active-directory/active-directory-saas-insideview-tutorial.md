<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con InsideView | Microsoft Azure" 
    description="Ecco come utilizzare InsideView con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-insideview"></a>Esercitazione: Integrazione di Azure Active Directory con InsideView
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e InsideView.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant InsideView
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a InsideView sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di InsideView (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per InsideView
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-insideview-tutorial/IC794128.png "Scenario:")
##<a name="enabling-the-application-integration-for-insideview"></a>Attivare l'integrazione delle applicazioni per InsideView
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per InsideView.

###<a name="to-enable-the-application-integration-for-insideview-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per InsideView, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-insideview-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-insideview-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-insideview-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **InsideView**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-insideview-tutorial/IC794129.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **InsideView**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![InsideView] (./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a InsideView con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **InsideView** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-insideview-tutorial/IC794131.png "Configurare l'accesso singolo")

2.  Nella pagina **specificare come gli utenti di accedere al InsideView** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-insideview-tutorial/IC794132.png "Configurare l'accesso singolo")

3.  Nella pagina **Configura App URL** nella casella di testo **URL di risposta InsideView** digitare l'URL di Single Sign-on InsideView (ad esempio: `https://my.insideview.com/iv/<STS Name>/login.iv`), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-insideview-tutorial/IC794133.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in InsideView** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare l'accesso singolo] (./media/active-directory-saas-insideview-tutorial/IC794134.png "Configurare l'accesso singolo")

5.  In una finestra del browser web diverso, accedere a sito della società InsideView come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **amministratore**, **Le impostazioni di ADFS**e quindi fare clic su **Aggiungi SAML**.

    ![SAML Single Sign-On impostazioni] (./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign-On impostazioni")

7.  Nella sezione **aggiungere un nuovo SAML** , procedere come segue:

    ![Aggiungere un nuovo SAML] (./media/active-directory-saas-insideview-tutorial/IC794136.png "Aggiungere un nuovo SAML")

    1.  Nella casella di testo **Nome servizio token di sicurezza** , digitare un nome per la configurazione.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in InsideView** copiare il valore di **Endpoint avviata Provider di servizi (SP)** e quindi incollarla nella casella di testo **SamlP/WS-Fed Unsolicated EndPoint** .
    3.  Creare un file **con codifica base 64** del certificato scaricata.
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato servizio token di sicurezza**
    5.  Nella casella di testo **Mapping Id utente Crm** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    6.  Nella casella di testo **Mapping di posta elettronica Crm** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Nella casella di testo **Mapping nome Crm** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    8.  Nella casella di testo **Cognome Crm Mapping** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    9.  Fare clic su **Salva**.

8.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare l'accesso singolo] (./media/active-directory-saas-insideview-tutorial/IC794137.png "Configurare l'accesso singolo")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a InsideView, è necessario essere effettuato il provisioning in InsideView.  
In caso di InsideView, il provisioning è un'attività manuale.
  
Per ottenere gli utenti o i contatti creati in InsideView, contattare il responsabile di successo dei clienti o inviare messaggi di posta elettronica**support@insideview.com**

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro InsideView utente account strumenti di creazione o API fornite dall'InsideView provisioning degli account utente di Azure Active Directory.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-insideview-perform-the-following-steps"></a>Per assegnare gli utenti a InsideView, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **InsideView **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-insideview-tutorial/IC794138.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-insideview-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).