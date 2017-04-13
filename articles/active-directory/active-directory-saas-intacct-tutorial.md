<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Intacct | Microsoft Azure" 
    description="Ecco come utilizzare Intacct con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-intacct"></a>Esercitazione: Integrazione di Azure Active Directory con Intacct
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Intacct.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Intacct
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Intacct sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Intacct (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Intacct
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario:")
##<a name="enabling-the-application-integration-for-intacct"></a>Attivare l'integrazione delle applicazioni per Intacct
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Intacct.

###<a name="to-enable-the-application-integration-for-intacct-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Intacct, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-intacct-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-intacct-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-intacct-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Intacct**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-intacct-tutorial/IC790031.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Intacct**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Intacct] (./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Intacct con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Intacct** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Intacct** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Intacct su URL di accesso** , digitare l'URL utilizzando il modello seguente "*https://Intacct.com/company*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Intacct** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società Intacct come amministratore.

6.  Fare clic sulla scheda **della società** e quindi fare clic su **Informazioni società**.

    ![Società] (./media/active-directory-saas-intacct-tutorial/IC790037.png "Società")

7.  Fare clic sulla scheda **protezione** e quindi fare clic su **Modifica**.

    ![Sicurezza] (./media/active-directory-saas-intacct-tutorial/IC790038.png "Sicurezza")

8.  Nella sezione **Single sign-on (SSO)** , procedere come segue:

    ![Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign-On")

    1.  Selezionare **Attiva single sign in**.
    2.  Come **tipo di provider di identità**, selezionare **SAML 2.0**.
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Intacct** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **URL emittente** .
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Intacct** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    5.  Creare un file **con codifica base 64** del certificato scaricata.
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    6.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **certificato**
    7.  Fare clic su **Salva**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Intacct, è necessario essere effettuato il provisioning in Intacct.  
In caso di Intacct, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Intacct** .

2.  Fare clic sulla scheda **della società** e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-intacct-tutorial/IC790041.png "Utenti")

3.  Fare clic sulla scheda **Aggiungi**

    ![Aggiungere] (./media/active-directory-saas-intacct-tutorial/IC790042.png "Aggiungere")

4.  Nella sezione **Informazioni utente** , procedere come segue:

    ![Informazioni sugli utenti] (./media/active-directory-saas-intacct-tutorial/IC790043.png "Informazioni sugli utenti")

    1.  Digitare l' **ID utente**, il **Cognome**, **nome**, l' **indirizzo di posta elettronica**, il **titolo** e il **telefono** di un account Azure Active Directory che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Selezionare i **privilegi di amministratore** di un account Azure Active Directory che si desidera eseguire il provisioning.
    3.  Fare clic su **Salva**.
        
        >[AZURE.NOTE] Il titolare di account AAD riceverà un messaggio di posta elettronica e visitare un collegamento per confermare il proprio account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Intacct utente account strumenti di creazione o API fornite dall'Intacct agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-intacct-perform-the-following-steps"></a>Per assegnare gli utenti a Intacct, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Intacct **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-intacct-tutorial/IC790044.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-intacct-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).