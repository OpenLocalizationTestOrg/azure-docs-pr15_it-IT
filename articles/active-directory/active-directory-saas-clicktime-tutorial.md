<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ClickTime | Microsoft Azure" 
    description="Ecco come utilizzare ClickTime con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
    services="active-directory" 
    authors="jeevansd"
    documentationCenter="na" 
    manager="femila" />
<tags
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Esercitazione: Integrazione di Azure Active Directory con ClickTime

In questa esercitazione si imparerà a integrare ClickTime con Azure Active Directory (Azure Active Directory).

Integrazione di ClickTime con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a ClickTime
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a ClickTime (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con ClickTime, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un ClickTime single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di ClickTime dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on

##<a name="adding-clicktime-from-the-gallery"></a>Aggiunta di ClickTime dalla raccolta

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per ClickTime.

###<a name="to-enable-the-application-integration-for-clicktime-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per ClickTime, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-clicktime-tutorial/tic700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-clicktime-tutorial/tic700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-clicktime-tutorial/tic749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-clicktime-tutorial/tic749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **ClickTime**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-clicktime-tutorial/tic777275.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **ClickTime**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![ClickTime] (./media/active-directory-saas-clicktime-tutorial/tic777276.png "ClickTime")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con ClickTime in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare sia l'utente corrispondente in ClickTime a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente ClickTime deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in ClickTime.

Per configurare e testare Azure Active Directory single sign-on con ClickTime, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un ClickTime test utente](#creating-a-clicktime-test-user)** - che equivalente di Britta Simon nel ClickTime collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a ClickTime con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.  


>[AZURE.IMPORTANT] Per configurare il single sign-on nel tenant di ClickTime, è necessario prima di tutto contattare il supporto tecnico ClickTime per abilitare questa funzionalità.

**Per configurare Azure Active Directory single sign-on con ClickTime, procedere come segue:**

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **ClickTime** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Attivare single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777277.png "Attivare single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al ClickTime** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777278.png "Configurare il single sign-on")

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tic777286.png) 

    un. Nella casella di testo **IdentifierL** , digitare l'URL utilizzando il modello seguente: **https://app.clicktime.com/sp/**
    
    b. Nella casella di testo **URL di risposta** , digitare l'URL utilizzando il modello seguente: **https://app.clicktime.com/Login/**

    c. Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in ClickTime** per scaricare il certificato, fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777279.png "Configurare il single sign-on")

4.  In una finestra del browser web diverso, accedere a sito della società ClickTime come amministratore.

5.  Nella barra degli strumenti nella parte superiore, fare clic su **Preferenze**e quindi fare clic su **Impostazioni di sicurezza**.

6.  Nella sezione configurazione **Single Sign-On preferenze** , procedere come segue:

    ![Impostazioni di sicurezza] (./media/active-directory-saas-clicktime-tutorial/tic777280.png "Impostazioni di sicurezza")

    un.  Selezionare **Consenti** accesso aggiuntivo con **Azure Active Directory**Single Sign-On (SSO).
    
    b.  Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in ClickTime** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **Endpoint Provider di identità** .

    c.  Aprire il certificato codificato base 64 nel **blocco note**, copiare il contenuto e quindi incollarla nella casella di testo **Certificato x. 509** .
    
    d.  Fare clic su **Salva**.

7.  Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-clicktime-tutorial/tic777281.png "Configurare il single sign-on")

##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente

Per consentire agli utenti di Azure Active Directory per l'accesso a ClickTime, è necessario essere effettuato il provisioning in ClickTime.  
In caso di ClickTime, il provisioning è un'attività manuale.

###<a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al tenant di **ClickTime** .

2.  Nella barra degli strumenti nella parte superiore, fare clic su **società**e quindi fare clic su **persone**.

    ![Persone] (./media/active-directory-saas-clicktime-tutorial/tic777282.png "Persone")

3.  Fare clic su **Aggiungi utente**.

    ![Aggiungere una persona] (./media/active-directory-saas-clicktime-tutorial/tic777283.png "Aggiungere una persona")

4.  Nella sezione nuova persona, procedere come segue:

    ![Persone] (./media/active-directory-saas-clicktime-tutorial/tic777284.png "Persone")

    un.  Nella casella di testo **indirizzo di posta elettronica** digitare l'indirizzo di posta elettronica del proprio account Azure Active Directory.
    
    b.  Nella casella di testo **nome e cognome** , digitare il nome del proprio account Azure Active Directory.  

    >[AZURE.NOTE] Se si desidera, è possibile impostare proprietà aggiuntive del nuovo oggetto di persona.

    c.  Fare clic su **Salva**.

>[AZURE.NOTE] È possibile utilizzare qualsiasi altro ClickTime utente account strumenti di creazione o API fornite dal ClickTime provisioning degli account utente di Azure Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua ClickTime.

![Assegnare utente][200]

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

**Per assegnare Britta Simon ClickTime, eseguire le operazioni seguenti**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **ClickTime**.

    ![Configurare Single Sign-On](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]

## <a name="testing-single-sign-on"></a>Test il single sign-on
In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro ClickTime nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione ClickTime.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png
[205]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_205.png