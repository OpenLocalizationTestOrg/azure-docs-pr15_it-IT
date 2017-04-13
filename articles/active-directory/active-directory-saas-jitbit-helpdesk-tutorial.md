<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk | Microsoft Azure" 
    description="Informazioni su come usare Jitbit Helpdesk con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Esercitazione: Integrazione di Azure Active Directory con Jitbit Helpdesk
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Jitbit Helpdesk.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Jitbit Helpdesk
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Jitbit Helpdesk sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Jitbit Helpdesk (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Jitbit Helpdesk
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario:")
##<a name="enabling-the-application-integration-for-jitbit-helpdesk"></a>Attivare l'integrazione delle applicazioni per Jitbit Helpdesk
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Jitbit Helpdesk.

###<a name="to-enable-the-application-integration-for-jitbit-helpdesk-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Jitbit Helpdesk, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Help desk Jitbit**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Help desk Jitbit**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![JitBit] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Jitbit Helpdesk con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML. .  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

>[AZURE.IMPORTANT] Per configurare il single sign-on nel tenant di Jitbit Helpdesk, è necessario prima di tutto contattare il supporto tecnico Jitbit Helpdesk per abilitare questa funzionalità.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina **Dell'help desk Jitbit** applicazione integrazione fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al supporto tecnico Jitbit** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** nella casella di testo **Jitbit help desk URL di accesso** , digitare l'URL utilizzando il modello seguente "https://*\<nome tenant\>. Jitbit.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in Jitbit Helpdesk** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato localmente come **c:\\Jitbit Helpdesk.cer**.

    ![Configurare il single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Jitbit Helpdesk come amministratore.

6.  Nella barra degli strumenti nella parte superiore, fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Amministrazione")

7.  Fare clic su **Impostazioni generali**.

    ![Le autorizzazioni, società e utenti] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Le autorizzazioni, società e utenti")

8.  Nella sezione configurazione **impostazioni di autenticazione** , procedere come segue:

    ![Impostazioni di autenticazione] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Impostazioni di autenticazione")

    1.  Selezionare **Abilita SAML 2.0 singolo accesso** dall'accesso aggiuntivo con Single Sign-On (SSO) **OneLogin**.
    2.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in help desk Jitbit** copiare il valore di **Provider di servizi (SP) avviata endpoint** e quindi incollarla nella casella di testo **URL EndPoint** .
    3.  Creare un file **con codifica base 64** del certificato scaricata.
        
        >[AZURE.TIP]Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Aprire il certificato codificato base 64, copiare il contenuto che ne negli Appunti e incollarlo alla casella di testo **Certificato x. 509**
    5.  Fare clic su **Salva modifiche**.

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Per consentire agli utenti di Azure Active Directory per l'accesso a Jitbit Helpdesk, è necessario essere effettuato il provisioning in Jitbit Helpdesk.  
Nel caso dei Jitbit Helpdesk, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **Help desk Jitbit** .

2.  Nel menu nella parte superiore, fare clic su **amministrazione**.

    ![Amministrazione] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Amministrazione")

3.  Fare clic su **utenti, società e autorizzazioni**.

    ![Le autorizzazioni, società e utenti] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Le autorizzazioni, società e utenti")

4.  Fare clic su **Aggiungi utente**.

    ![Aggiungi utente.] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Aggiungi utente.")

5.  Nella sezione creare digitare i dati dell'account Azure Active Directory si desidera eseguire il provisioning nelle caselle di testo seguenti: **nome utente**, **messaggio di posta elettronica**, **nome**e **Cognome**

    ![Creare] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Creare")

6.  Fare clic su **Crea**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Jitbit Helpdesk utente account strumenti di creazione o API fornite dal Jitbit Helpdesk agli account utente di provisioning AAD.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-jitbit-helpdesk-perform-the-following-steps"></a>Per assegnare gli utenti a Jitbit Helpdesk, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina **Dell'help desk Jitbit **applicazione integrazione fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).