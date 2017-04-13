<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting | Microsoft Azure" 
    description="Informazioni su come utilizzare Citrix GoToMeeting con Azure Active Directory per abilitare il single sign-on, automatizzato il provisioning e altro." 
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
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>Esercitazione: Integrazione di Azure Active Directory con Citrix GoToMeeting  
Si applica a: Azure

>[AZURE.TIP]Per i commenti, fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=522412).

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Citrix GoToMeeting. Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Citrix GoToMeeting

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Citrix GoToMeeting
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Configurazione] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configurazione")



##<a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>Attivare l'integrazione delle applicazioni per Citrix GoToMeeting

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Citrix GoToMeeting.

###<a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Citrix GoToMeeting, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione dalla raccolta] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Aggiungere un'applicazione dalla raccolta")

6.  Nella **casella Cerca**digitare **Citrix GoToMeeting**.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7.  Nel riquadro dei risultati selezionare **Citrix GoToMeeting**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Citrix GoToMeeting] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Citrix GoToMeeting con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  
Come parte di questa procedura, è necessario caricare un certificato codificato base 64 al tenant di Citrix GoToMeeting.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nella pagina integrazione applicazione **Citrix GoToMeeting** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura via accesso singolo** .

    ![Attivare single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Attivare single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Citrix GoToMeeting** selezionare **Microsoft Azure Active Directory Single Sign-On**.

    ![Configurare il single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configurare il single sign-on")


3. Nella pagina **Configura impostazioni di App** , fare clic su **Avanti**. 

    ![Attivare single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Attivare single sign-on")

4.  Nella pagina **Configura il single sign-on in Citrix GoToMeeting** fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configurare il single sign-on")

5.  In una nuova finestra del browser, accedere a centro di organizzazione Citrix ([https://account.citrixonline.com/organization/administration/](https://account.citrixonline.com/organization/administration/)).

6. Fare clic sulla scheda **Provider di identità** e quindi eseguire le operazioni seguenti:  

    ![Configurazione SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "Configurazione SAML")

    un. Selezionare **manuale**

    
    b. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Citrix GoToMeeting** copiare il valore di **Accesso URL della pagina** e quindi incollarla nella casella di testo **URL della pagina di accesso** . 

    
    c. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Citrix GoToMeeting** copiare il valore di **Sign-Out URL della pagina** e quindi incollarla nella casella di testo **URL della pagina disconnessione** .

    
    d. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Citrix GoToMeeting** copiare il valore di **ID entità** e quindi incollarla nella casella di testo **ID entità Provider di identità** .

   
    e. Per caricare il certificato scaricato, fare clic su **Carica certificato**.

    
    f. Fare clic su **Salva**.

6.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configurare il single sign-on")


7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.

    ![Configurazione SAML] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "Configurazione SAML")





##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per Citrix GoToMeeting.

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Citrix GoToMeeting** , fare clic su **Configura il provisioning dell'utente** per aprire la finestra di dialogo **Configura il Provisioning dell'utente** .

    ![Configura il provisioning dell'utente] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configura il provisioning dell'utente")

2.  Nella pagina **impostazioni e le credenziali di amministratore** , procedere come segue:

    ![Configura il provisioning dell'utente] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configura il provisioning dell'utente")

    un. Nella casella di testo **Nome utente dell'amministratore GoToMeeting Citrix** , digitare il nome utente dell'amministratore.

    
    b. Nella casella **Password di amministratore GoToMeeting Citrix** di testo, la password dell'amministratore.

    
    c. Fare clic su **Avanti**.

3.  Nella pagina di **Conferma** , fare clic sul segno di spunta per salvare la configurazione.

4.  Fare clic sul pulsante **convalida** per verificare la configurazione.


##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>Per assegnare gli utenti a Citrix GoToMeeting, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Citrix GoToMeeting** , fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Sì")

È ora necessario attendere 10 minuti e verificare che l'account è stato sincronizzato in Dropbox per le aziende.

Come primo passaggio di verifica, è possibile verificare lo stato di provisioning, facendo clic su Dashboard d nella pagina di integrazione **Citrix GoToMeeting** applicazione nel portale di classica Azure.

![Dashboard] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

Un utente completato il provisioning di ciclo è indicato da uno stato correlato:

![Stato di integrazione] (./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Stato di integrazione")

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso.

Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](https://msdn.microsoft.com/library/dn308586).
