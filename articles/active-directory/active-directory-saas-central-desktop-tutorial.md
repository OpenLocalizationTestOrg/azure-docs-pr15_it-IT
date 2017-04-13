<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Desktop centrale | Microsoft Azure" 
    description="Informazioni su come utilizzare Desktop centrale con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Esercitazione: Integrazione di Azure Active Directory con Desktop centrale

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Desktop centrale. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un accesso singolo desktop centrale abbonamento abilitato / desktop centrale del tenant

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazioni per Desktop centrale
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario:")
##<a name="enabling-the-application-integration-for-central-desktop"></a>Attivare l'integrazione di applicazioni per Desktop centrale

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per Desktop centrale.

###<a name="to-enable-the-application-integration-for-central-desktop-perform-the-following-steps"></a>Per attivare l'integrazione di applicazioni per Desktop centrale, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Desktop centrale**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **Desktop centrale**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Desktop centrale] (./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Desktop centrale")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione centrale desktop con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di Desktop centrale.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).



###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina di integrazione dell'applicazione **Desktop centrale** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Desktop centrale** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**: 

    -   Nella casella di testo **Centrale Desktop URL di accesso** , digitare l'URL del tenant Desktop centrale (ad esempio: *http://contoso.centraldesktop.com*).
    -   Nella casella di testo URL di risposta Desktop centrale, digitare l'URL di AssertionConsumerService Desktop centrale (ad esempio: https://contoso.centraldesktop.com/saml2-assertion.php).

    >[AZURE.NOTE] È possibile ottenere il valore dai metadati desktop centrali (ad esempio: *http://contoso.centraldesktop.com*).

    ![Configurare URL di app] (./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on desktop centrale** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurare il single sign-on")

5.  Accedere al tenant di **Desktop centrale** .

6.  Passare a **Impostazioni**, fare clic su **Avanzate**e quindi fare clic su **Single Sign-On**.

    ![Installazione: avanzate] (./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Installazione: avanzate")

7.  Pagina **Accesso singolo in impostazioni** procedere come segue:

    ![Single Sign-On impostazioni] (./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign-On impostazioni")

    1.  Selezionare **Abilita SAML v2 Single Sign-On**.
    2.  Nel portale di classica Azure, nella pagina **Configura il single sign-on desktop centrale** , copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **URL Single Sign-on** .
    3.  Nel portale di classica Azure, nella pagina **Configura il single sign-on desktop centrale** , copiare il valore di **URL di accesso remoto** e quindi incollarla nella casella di testo **URL di accesso Single Sign-on** .
    4.  Nel portale di classica Azure, nella pagina **Configura il single sign-on desktop centrale** , copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **URL disconnessione Single Sign-on** .

8.  Nella sezione **Metodo di verifica della firma messaggio** , procedere come segue:

    ![Metodo di verifica della firma messaggio] (./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Metodo di verifica della firma messaggio")

    1.  Selezionare **certificato**.
    2.  Dall'elenco di **Certificati di Single Sign-on** , selezionare **SHA256 RSH**.
    3.  Creare un file di testo del certificato scaricata, copiare il contenuto del file di testo e quindi incollarla nel campo **Certificato Single Sign-on** .  

        >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    4.  Selezionare **Visualizza un collegamento alla pagina di accesso SAMLv2**.

9.  Fare clic su **Aggiorna**.

10. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per gli utenti AAD in grado di accedere, è necessario essere effettuato il provisioning per l'applicazione Desktop centrale. In questa sezione viene descritto come creare account utente AAD nell'applicazione Desktop centrale.

###<a name="to-provision-user-accounts-to-central-desktop"></a>Provisioning degli account utente centrale desktop:

1.  Accedere al tenant di Desktop centrale.

2.  Passare a **persone \> membri interni**.

3.  Fare clic su **Aggiungi membri interni**.

    ![Persone] (./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Persone")

4.  Nella casella di testo **Indirizzo di posta elettronica di nuovi membri** , digitare un account AAD cui effettuare il provisioning e quindi fare clic su **Avanti**.

    ![Indirizzi di posta elettronica di nuovi membri] (./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Indirizzi di posta elettronica di nuovi membri")

5.  Fare clic su **membri aggiungere interno**.

    ![Aggiungere membri interni] (./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Aggiungere membri interni")

    >[AZURE.NOTE] Gli utenti che sono stati aggiunti verranno visualizzato un messaggio di posta elettronica contenente un collegamento di conferma che dovranno fare clic per attivare l'account.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro Desktop centrale utente Creazione strumenti account o API fornite dal Desktop centrale agli account utente di provisioning AAD

##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-central-desktop-perform-the-following-steps"></a>Per assegnare gli utenti desktop centrale, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina di integrazione dell'applicazione **Desktop centrale** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sì")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
