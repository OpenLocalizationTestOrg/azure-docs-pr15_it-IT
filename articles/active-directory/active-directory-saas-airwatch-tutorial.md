<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con AirWatch | Microsoft Azure" 
    description="Ecco come utilizzare AirWatch con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Esercitazione: Integrazione di Azure Active Directory con AirWatch

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e AirWatch.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un AirWatch single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a AirWatch sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di AirWatch (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per AirWatch
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791913.png "AirWatch")
##<a name="enabling-the-application-integration-for-airwatch"></a>Attivare l'integrazione delle applicazioni per AirWatch

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per AirWatch.

###<a name="to-enable-the-application-integration-for-airwatch-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per AirWatch, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-airwatch-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-airwatch-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-airwatch-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-airwatch-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **AirWatch**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-airwatch-tutorial/IC791914.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **AirWatch**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![AirWatch] (./media/active-directory-saas-airwatch-tutorial/IC791915.png "AirWatch")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a AirWatch con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **AirWatch** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791916.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al AirWatch** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791917.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **AirWatch su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione AirWatch (ad esempio: "*https:// companycode.awmdm.com/AirWatch/Login?gid=companycode*"), quindi scegliere **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-airwatch-tutorial/IC791918.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in AirWatch** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791919.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società AirWatch come amministratore.

6.  Nel riquadro di spostamento sinistra, fare clic su **account**e quindi fare clic su **amministratori**.

    ![Amministratori] (./media/active-directory-saas-airwatch-tutorial/IC791920.png "Amministratori")

7.  Espandere il menu **Impostazioni** e quindi fare clic su **Servizi Directory**.

    ![Impostazioni] (./media/active-directory-saas-airwatch-tutorial/IC791921.png "Impostazioni")

8.  Fare clic sulla scheda **utente** nel campo di testo **Nome distinto di Base** , digitare il nome di dominio e quindi fare clic su **Salva**.

    ![Utente] (./media/active-directory-saas-airwatch-tutorial/IC791922.png "Utente")

9.  Fare clic sulla scheda **Server** .

    ![Server] (./media/active-directory-saas-airwatch-tutorial/IC791923.png "Server")

10. Eseguire i passaggi seguenti:

    ![Caricare] (./media/active-directory-saas-airwatch-tutorial/IC791924.png "Caricare")

    1.  Come **Tipo di elenco**, selezionare **Nessuno**.
    2.  Selezionare **Usa SAML per l'autenticazione**.
    3.  Per caricare il certificato scaricato, fare clic su **Carica**.

11. Nella sezione **Richiedi** , procedere come segue:

    ![Richiedere] (./media/active-directory-saas-airwatch-tutorial/IC791925.png "Richiedere")

    1.  Come **Tipo di associazione richiesta**, selezionare **POST**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Airwatch** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **Identità Provider singola su URL di accesso** .
    3.  Come **Formato NameID**, selezionare **l'Indirizzo di posta elettronica**.
    4.  Fare clic su **Salva**.

12. Fare di nuovo clic sulla scheda **utente** .

    ![Utente] (./media/active-directory-saas-airwatch-tutorial/IC791926.png "Utente")

13. Nella sezione **attributo** , procedere come segue:

    ![Attributo] (./media/active-directory-saas-airwatch-tutorial/IC791927.png "Attributo")

    1.  Nella casella di testo **Identificatore di oggetto** , digitare **http://schemas.microsoft.com/identity/claims/objectidentifier**.
    2.  Nella casella di testo **nome utente** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    3.  Nella casella di testo **Nome visualizzato** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    4.  Nella casella di testo **nome** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
    5.  Nella casella di testo **Cognome** digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.
    6.  Nella casella di testo **messaggio di posta elettronica** , digitare **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.
    7.  Fare clic su **Salva**.

14. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-airwatch-tutorial/IC791928.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a AirWatch, è necessario essere effettuato il provisioning in AirWatch.  
In caso di AirWatch, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **AirWatch** come amministratore.

2.  Nel riquadro di spostamento sul lato sinistro, fare clic su **account**e quindi fare clic su **utenti**.

    ![Utenti] (./media/active-directory-saas-airwatch-tutorial/IC791929.png "Utenti")

3.  Nel menu a **utenti** , fare clic su **Visualizzazione elenco**e quindi fare clic su **Aggiungi \> Aggiungi utente**.

    ![Aggiunta utente] (./media/active-directory-saas-airwatch-tutorial/IC791930.png "Aggiunta utente")

4.  Nella finestra di dialogo **Aggiungi / Modifica utente** , procedere come segue:

    ![Aggiunta utente] (./media/active-directory-saas-airwatch-tutorial/IC791931.png "Aggiunta utente")

    1.  Digitare **il nome utente**, **Password**, **Conferma Password**, **nome**, **Cognome**, **Indirizzo di posta elettronica** di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro AirWatch utente account strumenti di creazione o API fornite dall'AirWatch agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-airwatch-perform-the-following-steps"></a>Per assegnare gli utenti a AirWatch, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **AirWatch **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-airwatch-tutorial/IC791932.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-airwatch-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
