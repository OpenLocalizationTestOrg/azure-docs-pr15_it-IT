<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con FM: sistemi | Microsoft Azure" 
    description="Informazioni su come usare FM: sistemi con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-fm-systems"></a>Esercitazione: Integrazione di Azure Active Directory con FM: sistemi
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e FM:Systems.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un FM:Systems single sign-on abilitato abbonamento
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a FM:Systems sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di FM:Systems (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per FM:Systems
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-fm-systems-tutorial/IC795899.png "Scenario:")
##<a name="enabling-the-application-integration-for-fmsystems"></a>Attivare l'integrazione delle applicazioni per FM:Systems
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per FM:Systems.

###<a name="to-enable-the-application-integration-for-fmsystems-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per FM:Systems, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-fm-systems-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-fm-systems-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-fm-systems-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-fm-systems-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **FM:Systems**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-fm-systems-tutorial/IC795900.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **FM:Systems**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![FM: sistemi] (./media/active-directory-saas-fm-systems-tutorial/IC800213.png "FM: sistemi")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti autenticati a FM:Systems con il proprio account Azure Active Directory utilizzando la federazione in base al protocollo SAML.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **FM:Systems** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC790810.png "Configurare Single Sign-On")

2.  Nella pagina **specificare come gli utenti di accedere al FM:Systems** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795901.png "Configurare Single Sign-On")

3.  Nella pagina **Configura URL App** , procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-fm-systems-tutorial/IC795902.png "Configurare URL di App")

    1.  Nella casella di testo **FM:Systems su URL di accesso** , digitare **l'URL di risposta** di FM:Systems (ad esempio: *https://dpr.fmshosted.com/fminteract/ConsumerService2.aspx*).  

        >[AZURE.WARNING] È possibile ottenere questo valore dal FM: il team di supporto di sistemi.

    2.  Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in FM:Systems** per scaricare i metadati, fare clic su **Scarica metadati**e quindi salvare i metadati nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795903.png "Configurare Single Sign-On")

5.  Inviare il file scaricato basato su metadati per il FM: il team di supporto di sistemi.

    >[AZURE.NOTE] Il FM: Il team di supporto di sistemi deve eseguire la configurazione di Single Sign-on effettiva.
Si riceve una notifica quando Single Sign-on è stata attivata per l'abbonamento.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On] (./media/active-directory-saas-fm-systems-tutorial/IC795904.png "Configurare Single Sign-On")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a FM:Systems, è necessario essere effettuato il provisioning in FM:Systems.  
In caso di FM:Systems, il provisioning è un'attività manuale.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  In una finestra del browser accedere sito della società FM:Systems come amministratore.

2.  Passare a **amministrazione del sistema \> Gestisci protezione \> utenti \> elenco utente**.

    ![Amministrazione del sistema] (./media/active-directory-saas-fm-systems-tutorial/IC795905.png "Amministrazione del sistema")

3.  Fare clic su **Crea nuovo utente**.

    ![Creare i nuovi utenti] (./media/active-directory-saas-fm-systems-tutorial/IC795906.png "Creare i nuovi utenti")

4.  Nella sezione **Crea utente** , procedere come segue:

    ![Creare utente] (./media/active-directory-saas-fm-systems-tutorial/IC795907.png "Creare utente")

    1.  Digitare il nome utente, la password e conferma, l'indirizzo di posta elettronica e l'ID dipendente di un account Azure Active Directory valido che si desidera eseguire il provisioning nelle caselle di testo correlati.
    2.  Fare clic su **Avanti**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro FM:Systems utente account strumenti di creazione o API fornite dal FM:Systems agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-fmsystems-perform-the-following-steps"></a>Per assegnare gli utenti a FM:Systems, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **FM:Systems **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-fm-systems-tutorial/IC795908.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-fm-systems-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).