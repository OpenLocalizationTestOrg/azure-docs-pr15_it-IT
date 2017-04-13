<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Concur | Microsoft Azure" 
    description="Ecco come utilizzare Concur con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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

#<a name="tutorial-azure-active-directory-integration-with-concur"></a>Esercitazione: Integrazione di Azure Active Directory con Concur  


L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Concur.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant Concur

Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni per Concur
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-concur-tutorial/IC769766.png "Scenario:")

>[AZURE.NOTE] La configurazione del proprio abbonamento Concur per Single Sign-on federati tramite SAML è un'attività separata, è necessario contattare Concur da eseguire.

##<a name="enabling-the-application-integration-for-concur"></a>Attivare l'integrazione delle applicazioni per Concur

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per Concur.

###<a name="to-enable-the-application-integration-for-concur-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per Concur, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-concur-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco **Directory** , selezionare la directory per cui vuole attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-concur-tutorial/IC700994.png "Applicazioni")

4.  Per aprire la **Raccolta di applicazione**, fare clic su **Aggiungi un'App**e quindi fare clic su **Aggiungi un'applicazione per la propria organizzazione da utilizzare**.

    ![Operazioni da eseguire?] (./media/active-directory-saas-concur-tutorial/IC700995.png "Operazioni da eseguire?")

5.  Nella **casella Cerca**digitare **Concur**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-concur-tutorial/IC721727.png "Raccolta dell'applicazione")

6.  Nel riquadro dei risultati, selezionare **Concur**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Contribuire] (./media/active-directory-saas-concur-tutorial/IC721728.png "Contribuire")
##<a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a Concur con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

>[AZURE.NOTE] La configurazione del proprio abbonamento Concur per Single Sign-on federati tramite SAML è un'attività separata, è necessario contattare Concur da eseguire.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Concur **, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-concur-tutorial/IC769767.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Concur** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-concur-tutorial/IC769768.png "Configurare il single sign-on")

3.  Nella pagina **Configura App URL** nella casella di testo **Contribuire URL di accesso** , digitare l'URL di accesso del tenant concur e quindi fare clic su **Avanti**: 

    ![Configurare l'accesso URL] (./media/active-directory-saas-concur-tutorial/IC769769.png "Configurare l'accesso URL")

4.  Nella pagina **Configura il single sign-on in Concur** , procedere come segue.

    ![Configurare l'accesso URL] (./media/active-directory-saas-concur-tutorial/IC769770.png "Configurare l'accesso URL")

    1.  Fare clic su Scarica i metadati e quindi safe il file di dati nel computer in uso.
    2.  Contattare il team di supporto Concur per configurare Single Sign-on per il tenant.
    3.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .  

    >[AZURE.NOTE] La configurazione del proprio abbonamento Concur per Single Sign-on federati tramite SAML è un'attività separata, è necessario contattare Concur da eseguire.

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per Concur.

Per abilitare l'app nel servizio di spesa, ci deve essere corretta configurazione e uso di un profilo di amministratore del servizio Web. Non è sufficiente aggiungere il ruolo di amministratore WS al proprio profilo di amministratore esistente utilizzato per T & E funzioni amministrative.

Contribuire consulenti o l'amministratore di client deve creare un profilo di amministratore del servizio Web distinti e l'amministratore di Client dovrà utilizzare il profilo per le funzioni di amministratore dei servizi Web (ad esempio attivare App). Questi profili devono essere mantenuti distinti dal client profilo dell'amministratore giornaliero T & E amministrazione (il profilo di amministratore T & E necessario non è assegnato il ruolo di WSAdmin).

Quando si crea il profilo da utilizzare per abilitare l'app, immettere il nome dell'amministratore client nei campi profilo utente. Si assegna la proprietà del profilo. Dopo aver creato i profili, il client dovrà accedere con il profilo fare clic sul pulsante "*abilitare*" per un'App Partner all'interno del menu di servizi Web.

Per i motivi seguenti, questa azione non deve essere eseguita con il profilo che utilizzano per l'amministrazione T & E normale.

1.  Il cliente deve essere quello che fa clic su "*Sì*" nella finestra di dialogo che viene visualizzata dopo l'attivazione di un'app. Questo fare clic su accetta che il cliente è disposto per l'applicazione di Partner accedere ai dati, in modo che si o il Partner non è possibile fare clic sul pulsante Sì.
2.  Se un amministratore di client che ha abilitato la un'app utilizzando l'amministratore T & E profilo lascia l'azienda (risultante nel profilo viene disattivato), installata nessuna App attivata utilizzando che profilo non funzioneranno fino a quando non è abilitata l'app con un altro profilo WS amministratore attivo. Questo è il motivo per cui si deve per creare distinti WS amministrazione profili.
3.  Se un amministratore lascia l'azienda, il nome associato al profilo WS amministratore può essere modificato all'amministratore di sostituzione se lo si desidera senza influire sul che App abilitato perché il profilo deve essere disattivato

###<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Accesso al tenant di **Concur** .

2.  Dal menu **amministrazione** selezionare **Servizi Web**.

    ![Concur tenant] (./media/active-directory-saas-concur-tutorial/IC721729.png "Concur tenant")

3.  Sul lato sinistro dal riquadro **Servizi Web** , selezionare **Attiva applicazione Partner**.

    ![Consentire l'applicazione Partner] (./media/active-directory-saas-concur-tutorial/IC721730.png "Consentire l'applicazione Partner")

4.  Dall'elenco **Abilitare applicazione** selezionare **Azure Active Directory**e quindi fare clic su **Attiva**.

    ![Microsoft Azure Active Directory] (./media/active-directory-saas-concur-tutorial/IC721731.png "Microsoft Azure Active Directory")

5.  Fare clic su **Sì** per chiudere la finestra di dialogo di **Conferma azione** .

    ![Confermare] (./media/active-directory-saas-concur-tutorial/IC721732.png "Confermare")

6.  Nel portale di Azure classico selezionare **Concur** dall'elenco delle applicazioni per aprire la pagina della finestra di dialogo **Concur** .

7.  Per aprire la pagina di finestra di dialogo **Configura il Provisioning dell'utente** , fare clic su **Configura il provisioning dell'utente**.

8.  Immettere il nome utente e la password dell'amministratore Concur e quindi fare clic su **Avanti**.

9.  Per completare la configurazione, nella pagina di **Conferma** , fare clic sul pulsante di **completamento** .

È ora possibile creare un account di prova, attendere 10 minuti e verificare che l'account è stata sincronizzata con Concur.
##<a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-concur-perform-the-following-steps"></a>Per assegnare gli utenti a Concur, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Concur **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-concur-tutorial/IC769771.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-concur-tutorial/IC767830.png "Sì")

È ora necessario attendere 10 minuti e verificare che l'account è stata sincronizzata con Concur.

Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso. Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
