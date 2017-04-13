<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Wikispaces | Microsoft Azure" 
    description="Informazioni su come utilizzare Wikispaces con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-wikispaces"></a>Esercitazione: Integrazione di Azure Active Directory con Wikispaces
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Wikispaces.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un Wikispaces single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Wikispaces sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Wikispaces (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Wikispaces
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Sceanrio] (./media/active-directory-saas-wikispaces-tutorial/IC787182.png "Sceanrio")

##<a name="enabling-the-application-integration-for-wikispaces"></a>Attivare l'integrazione delle applicazioni per Wikispaces
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Wikispaces.

###<a name="to-enable-the-application-integration-for-wikispaces-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Wikispaces, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-wikispaces-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-wikispaces-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-wikispaces-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-wikispaces-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Wikispaces**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-wikispaces-tutorial/IC787186.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Wikispaces**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Wikispaces] (./media/active-directory-saas-wikispaces-tutorial/IC787187.png "Wikispaces")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Wikispaces con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Wikispaces** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787188.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al Wikispaces** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787189.png "Configurare Single Sign-On")

3.  Nella pagina **Configura App URL** nella casella di testo **Wikispaces su URL di accesso** , digitare l'URL utilizzando il modello seguente "*http://company.wikispaces.net*" e quindi fare clic su **Avanti**.

    ![Configurare URL di App] (./media/active-directory-saas-wikispaces-tutorial/IC787190.png "Configurare URL di App")

4.  Nella pagina **Configura il single sign-on in Wikispaces** fare clic su **Scarica metadati**e quindi salvare il file di metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787191.png "Configurare Single Sign-On")

5.  Inviare la metadatafile al team di supporto Wikispaces.

    >[AZURE.NOTE] La configurazione single sign-on deve essere eseguita dal team di supporto Wikispaces. Si riceve una notifica non appena la configurazione è stata completata.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-wikispaces-tutorial/IC787192.png "Configurare Single Sign-On")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Wikispaces, è necessario essere effettuato il provisioning in Wikispaces.  
In caso di Wikispaces, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale **Wikispaces** come amministratore.

2.  Passare ai **membri**.

    ![Membri] (./media/active-directory-saas-wikispaces-tutorial/IC787193.png "Membri")

3.  Fare clic su **Invita persone**.

    ![Invitare altre persone] (./media/active-directory-saas-wikispaces-tutorial/IC787194.png "Invitare altre persone")

4.  Nella sezione **Invita altri utenti** , procedere come segue:

    ![Invitare altre persone] (./media/active-directory-saas-wikispaces-tutorial/IC787208.png "Invitare altre persone")

    1.  Digitare i **nomi utente o indirizzo di posta elettronica** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Invia**.  

        >[AZURE.NOTE] Il titolare di account Azure Active Directory riceve un messaggio di posta elettronica incluso un collegamento per confermare l'account diventa attiva.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Wikispaces utente account strumenti di creazione o API fornite dal Wikispaces agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-wikispaces-perform-the-following-steps"></a>Per assegnare gli utenti a Wikispaces, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Wikispaces **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-wikispaces-tutorial/IC787195.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-wikispaces-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).