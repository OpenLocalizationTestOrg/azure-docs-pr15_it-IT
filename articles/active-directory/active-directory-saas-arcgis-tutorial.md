<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ArcGIS | Microsoft Azure" 
    description="Ecco come utilizzare ArcGIS con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-arcgis"></a>Esercitazione: Integrazione di Azure Active Directory con ArcGIS

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e ArcGIS. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un ArcGIS single sign-on abilitato abbonamento

Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a ArcGIS sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di ArcGIS (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per ArcGIS
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario:")
##<a name="enabling-the-application-integration-for-arcgis"></a>Attivare l'integrazione delle applicazioni per ArcGIS

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per ArcGIS.

###<a name="to-enable-the-application-integration-for-arcgis-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per ArcGIS, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-arcgis-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-arcgis-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **ArcGIS**.

    ![Raccolta Applcation] (./media/active-directory-saas-arcgis-tutorial/IC784736.png "Raccolta Applcation")

7.  Nel riquadro dei risultati, selezionare **ArcGIS**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ArcGIS] (./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ArcGIS con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **ArcGIS** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al ArcGIS** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **ArcGIS URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere con il modello "*https://company.maps.arcgis.com*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in ArcGIS** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configurare Single Sign-On")

5.  In una finestra del browser web diverso, accedere a sito della società ArcGIS come amministratore.

6.  Fare clic su **Modifica impostazioni**.

    ![Modificare le impostazioni] (./media/active-directory-saas-arcgis-tutorial/IC784742.png "Modificare le impostazioni")

7.  Fare clic su **sicurezza**.

    ![Sicurezza] (./media/active-directory-saas-arcgis-tutorial/IC784743.png "Sicurezza")

8.  In **Account di accesso dell'organizzazione**, fare clic su **Imposta Provider di identità**.

    ![Account di accesso dell'organizzazione] (./media/active-directory-saas-arcgis-tutorial/IC784744.png "Account di accesso dell'organizzazione")

9.  Nella pagina configurazione **Provider di identità di gruppo** , procedere come segue:

    ![Impostare il Provider di identità] (./media/active-directory-saas-arcgis-tutorial/IC784745.png "Impostare il Provider di identità")

    1.  Nella casella di testo Nome digitare il nome dell'organizzazione.
    2.  Per **che i metadati per il Provider di identità dell'organizzazione verranno forniti con**, selezionare **Un File**.
    3.  Per caricare il file di metadati scaricato, fare clic su **Scegli file**.
    4.  Fare clic su **Imposta Provider di identità**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a ArcGIS, è necessario essere effettuato il provisioning in ArcGIS.  
In caso di ArcGIS, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accedere al tenant di **ArcGIS** .

2.  Fare clic su **Invita membri**.

    ![Invitare i membri] (./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invitare i membri")

3.  Selezionare **Aggiungi membri automaticamente senza inviare un messaggio di posta elettronica**e quindi fare clic su **Avanti**.

    ![Aggiungere membri automaticamente] (./media/active-directory-saas-arcgis-tutorial/IC784748.png "Aggiungere membri automaticamente")

4.  Nella pagina di finestra di dialogo **membri** , procedere come segue:

    ![Aggiungere e revisione] (./media/active-directory-saas-arcgis-tutorial/IC784749.png "Aggiungere e revisione")

    1.  Immettere il **nome**, **Cognome** e **posta elettronica** di un account valido AAD che si desidera eseguire il provisioning.
    2.  Fare clic su **Aggiungi e rivedere**.

5.  Esaminare i dati immessi e quindi fare clic su **Aggiungi membri**.

    ![Aggiungi membro] (./media/active-directory-saas-arcgis-tutorial/IC784750.png "Aggiungi membro")

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro ArcGIS utente account strumenti di creazione o API fornite dall'ArcGIS agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-arcgis-perform-the-following-steps"></a>Per assegnare gli utenti a ArcGIS, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **ArcGIS **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-arcgis-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
