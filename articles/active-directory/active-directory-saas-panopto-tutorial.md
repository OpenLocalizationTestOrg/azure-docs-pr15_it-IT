<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Panopto | Microsoft Azure" 
    description="Ecco come utilizzare Panopto con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-panopto"></a>Esercitazione: Integrazione di Azure Active Directory con Panopto
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Panopto.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Panopto
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a Panopto sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di Panopto (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Panopto
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-panopto-tutorial/IC777665.png "Scenario:")
##<a name="enabling-the-application-integration-for-panopto"></a>Attivare l'integrazione delle applicazioni per Panopto
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Panopto.

###<a name="to-enable-the-application-integration-for-panopto-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Panopto, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-panopto-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-panopto-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-panopto-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-panopto-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Panopto**.

    ![Raccolta Appkication] (./media/active-directory-saas-panopto-tutorial/IC777666.png "Raccolta Appkication")

7.  Nel riquadro dei risultati, selezionare **Panopto**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Panopto] (./media/active-directory-saas-panopto-tutorial/IC782936.png "Panopto")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Panopto con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Panopto** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777667.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Panopto** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777668.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Panopto URL di accesso** , digitare l'URL utilizzando il seguente criterio "https://*\<nome tenant\>. Panopto.com*", quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-panopto-tutorial/IC777528.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on in Panopto** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777669.png "Configurare il single sign-on")

5.  In una finestra del browser web diverso, accedere a sito della società Panopto come amministratore.

6.  Nella barra degli strumenti sulla sinistra, fare clic su **sistema**e quindi fare clic su **Provider di identità**.

    ![Sistema] (./media/active-directory-saas-panopto-tutorial/IC777670.png "Sistema")

7.  Fare clic su **Aggiungi Provider**.

    ![Provider di identità] (./media/active-directory-saas-panopto-tutorial/IC777671.png "Provider di identità")

8.  Nella sezione provider SAML, procedere come segue:

    ![Configurazione di SaaS] (./media/active-directory-saas-panopto-tutorial/IC777672.png "Configurazione di SaaS")

    1.  Nell'elenco **Tipo di Provider** , selezionare **SAML20**
    2.  Nella casella di testo **Nome dell'istanza** , digitare un nome per l'istanza.
    3.  Nella casella di testo **Descrizione** digitare una breve descrizione.
    4.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Panopto** copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **emittente** .
    5.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Panopto** copiare il valore di **SAML Single Sign-on URL** e quindi incollarla nella casella di testo **Rimbalzo Url della pagina** .
    6.  Creare un file **con codifica base 64** del certificato scaricata.  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    7.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Chiave pubblica**
    8.  Fare clic su **Salva**.
        ![Salva] (./media/active-directory-saas-panopto-tutorial/IC777673.png "Salva")

9.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-panopto-tutorial/IC777674.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
Non esiste alcun elemento azione per la configurazione di provisioning a Panopto dell'utente.  
Quando un utente assegnato tenta di accedere a Panopto mediante il pannello di accesso, Panopto verifica l'esistenza dell'utente.  
Se non vi è alcun account utente ancora disponibile, verrà creato automaticamente da Panopto.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro Panopto utente account strumenti di creazione o API fornite dal Panopto provisioning degli account utente di Azure Active Directory.

##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-panopto-perform-the-following-steps"></a>Per assegnare gli utenti a Panopto, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Panopto **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-panopto-tutorial/IC777675.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-panopto-tutorial/IC767830.png "Sì")
  
Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).