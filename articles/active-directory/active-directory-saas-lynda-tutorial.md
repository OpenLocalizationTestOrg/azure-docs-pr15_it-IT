<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Lynda.com | Microsoft Azure" 
    description="Ecco come utilizzare Lynda.com con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-lyndacom"></a>Esercitazione: Integrazione di Azure Active Directory con Lynda.com
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Lynda.com.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Lynda.com
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Lynda.com sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Lynda.com (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Lynda.com
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-lynda-tutorial/IC781046.png "Scenario:")
##<a name="enabling-the-application-integration-for-lyndacom"></a>Attivare l'integrazione delle applicazioni per Lynda.com
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Lynda.com.

###<a name="to-enable-the-application-integration-for-lyndacom-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Lynda.com, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-lynda-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-lynda-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-lynda-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-lynda-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Lynda.com**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-lynda-tutorial/IC777524.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Lynda.com**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Lynda.com] (./media/active-directory-saas-lynda-tutorial/IC777525.png "Lynda.com")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Lynda.com con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

>[AZURE.IMPORTANT]Per configurare il single sign-on nel tenant di Lynda.com, è necessario prima di tutto contattare il supporto tecnico Lynda.com per abilitare questa funzionalità.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Lynda.com** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777526.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Lynda.com** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777527.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Lynda.com URL di accesso** , digitare l'URL di tenant Lynda.com (ad esempio: *https://shib.lynda.com/Shibboleth.sso/InCommon?providerId=https://sts.windows-ppe.net/6247032d-9415-403c-b72b-277e3fb6f2c8/&target= https://shib.lynda.com/InCommon*), quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-lynda-tutorial/IC781047.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in Lynda.com** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777529.png "Configurare il single sign-on")

5.  Inviare il file di metadati scaricati al team di supporto Lynda.com. Il team di supporto Lynda.com indica la configurazione di Single Sign-On dell'utente.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-lynda-tutorial/IC777530.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning Lynda.com dell'utente.  
Quando un utente assegnato tenta di accedere a Lynda.com mediante il pannello di accesso, Lynda.com verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Lynda.com.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Lynda.com utente account strumenti di creazione o API fornite dal Lynda.com agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-lyndacom-perform-the-following-steps"></a>Per assegnare gli utenti a Lynda.com, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Lynda.com **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-lynda-tutorial/IC777531.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-lynda-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).