<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con posta elettronica Zoho | Microsoft Azure" 
    description="Informazioni su come utilizzare Zoho Mail con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro!." 
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
    ms.date="09/09/2016" 
    ms.author="markvi" />

#<a name="tutorial-azure-active-directory-integration-with-zoho-mail"></a>Esercitazione: Integrazione di Azure Active Directory con Zoho Mail
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Zoho posta.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di posta elettronica Zoho
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a posta elettronica Zoho sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di posta elettronica Zoho (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazione per la posta Zoho
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-zoho-mail-tutorial/IC789600.png "Scenario:")

##<a name="enabling-the-application-integration-for-zoho-mail"></a>Attivare l'integrazione di applicazione per la posta Zoho
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazione per la posta Zoho.

###<a name="to-enable-the-application-integration-for-zoho-mail-perform-the-following-steps"></a>Per attivare l'integrazione di applicazione per la posta Zoho, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-zoho-mail-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-zoho-mail-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-zoho-mail-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-zoho-mail-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Zoho Mail**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-zoho-mail-tutorial/IC789601.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Posta Zoho**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Stampa Zoho] (./media/active-directory-saas-zoho-mail-tutorial/IC789602.png "Stampa Zoho")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione Zoho telefonica con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Zoho posta** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789603.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come agli utenti di effettuare l'accesso a posta elettronica Zoho** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789604.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-zoho-mail-tutorial/IC789605.png "Configurare URL di App")

    un. Nella casella di testo **Zoho posta accesso su URL** digitare l'URL utilizzando il modello seguente:`http://<company name>.ZohoMail.com`

    b. Fare clic su **Avanti**.


4.  Nella pagina **Configura il single sign-on Zoho messaggio** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789606.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere come amministratore nel sito della società Zoho posta.

6.  Passare al **Pannello di controllo**.

    ![Pannello di controllo] (./media/active-directory-saas-zoho-mail-tutorial/IC789607.png "Pannello di controllo")

7.  Fare clic sulla scheda **Autenticazione SAML** .

    ![Autenticazione SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789608.png "Autenticazione SAML")

8.  Nella sezione **Dettagli relativi all'autenticazione SAML** , procedere come segue:

    ![Dettagli relativi all'autenticazione SAML] (./media/active-directory-saas-zoho-mail-tutorial/IC789609.png "Dettagli relativi all'autenticazione SAML")

    1.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Zoho messaggio** copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso** .
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Zoho messaggio** copiare il valore di **URL disconnessione remoto** e quindi incollarla nella casella di testo **URL disconnessione** .
    3.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on Zoho messaggio** copiare il valore **Cambia Password URL** e quindi incollarla nella casella di testo **URL Password di modifica** .
    4.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    5.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **PublicKey** .
    6.  Come **algoritmo**, selezionare **RSA**.
    7.  Fare clic su **OK**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-zoho-mail-tutorial/IC789610.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory accedere alla posta elettronica Zoho, è necessario essere effettuato il provisioning alla posta elettronica con Zoho.  
In caso di posta elettronica Zoho, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Posta Zoho** come amministratore.

2.  Passare a **Pannello di controllo \> posta e documenti**.

3.  Passare a **Dettagli utente \> aggiungere utente**.

    ![Aggiunta utente] (./media/active-directory-saas-zoho-mail-tutorial/IC789611.png "Aggiunta utente")

4.  Nella finestra di dialogo **Aggiungi utenti** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-zoho-mail-tutorial/IC789612.png "Aggiunta utente")

    1.  Digitare il **nome**, **Cognome**, **ID di posta elettronica**e **Password** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **OK**.  

        >[AZURE.NOTE] Il titolare di account Azure Active Directory verrà visualizzato un messaggio di posta elettronica con un collegamento di confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Zoho utente account creazione strumenti Mail o API fornite tramite posta Zoho agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-zoho-mail-perform-the-following-steps"></a>Per assegnare gli utenti per la posta Zoho, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Zoho posta **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-zoho-mail-tutorial/IC789613.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-zoho-mail-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).