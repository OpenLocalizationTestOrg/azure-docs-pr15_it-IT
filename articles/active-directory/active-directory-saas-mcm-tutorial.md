<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con MCM | Microsoft Azure" 
    description="Ecco come utilizzare MCM con Azure Active Directory per consentire il single sign-on, il provisioning automatico e altro." 
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
    ms.date="08/30/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-mcm"></a>Esercitazione: Integrazione di Azure Active Directory con MCM
  
L'obiettivo di questa esercitazione è illustrano come integrare MCM con Azure Active Directory (Azure Active Directory).

Integrazione di MCM con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a MCM
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a MCM (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con MCM, è necessario quanto segue:

- Abbonamento valido a Azure
- Un MCM single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di MCM dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on

## <a name="adding-mcm-from-the-gallery"></a>Aggiunta di MCM dalla raccolta
Per configurare l'integrazione di MCM in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito MCM dalla raccolta.

**Per aggiungere MCM dalla raccolta, procedere come segue:**

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-mcm-tutorial/tutorial_general_01.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-mcm-tutorial/tutorial_general_02.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-mcm-tutorial/tutorial_general_03.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-mcm-tutorial/tutorial_general_04.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **MCM**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_01.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **MCM**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![MCM] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_001.png "MCM")

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con MCM in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in MCM a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente MCM deve essere stabilito.

Assegnando il valore di **nome utente** in Active Directory Azure come valore di **nome utente** nella MCM stabilita la relazione di collegamento.

Per configurare e testare Azure Active Directory single sign-on con MCM, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un MCM testare utente](#creating-a-mcm-test-user)** - che equivalente di Britta Simon nel MCM collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on
  
In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione MCM.

**Per configurare Azure Active Directory single sign-on con MCM, procedere come segue:**

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **MCM** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-mcm-tutorial/tutorial_general_05.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al MCM** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Microsoft Azure Active Directory Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_03.png "Microsoft Azure Active Directory Single Sign-On")

3.  Nella pagina di finestra di dialogo Configura impostazioni di App, procedere come segue:

    ![Configurare URL di App] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_04.png "Configurare URL di App")

    un. Nella casella di testo **Su URL di accesso** , digitare: `https://myaba.co.uk/client-access/<company name>/saml.php`.
    
    b. Fare clic su **Avanti**

4.  Nella pagina **Configura il single sign-on in MCM** fare clic su **Scarica metadati**e quindi salvare il file di certificato nel computer in uso.

    ![Configurare Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_05.png "Configurare Single Sign-On")

5. Per ottenere SSO configurato per l'applicazione, rivolgersi al team di supporto MCM. Allegare il file di metadati scaricati e condividerlo con team MCM configurare Single Sign-on sul lato "i".

6.  Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_06.png "Configurare Single Sign-On")

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.

    ![Configurare Single Sign-On] (./media/active-directory-saas-mcm-tutorial/tutorial_mcm_07.png "Configurare Single Sign-On")


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory

L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_00.png)

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_01.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_02.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_03.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_04.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_05.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_06.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-mcm-tutorial/create_aaduser_07.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

###<a name="creating-a-mcm-test-user"></a>Creazione di un utente di test MCM
  
In questa sezione, si crea un utente chiamato Britta Simon in MCM. Lavorare con il team di supporto MCM per aggiungere gli utenti della piattaforma MCM.

>[AZURE.NOTE]È possibile utilizzare qualsiasi altro MCM utente account strumenti di creazione o API fornite dal MCM agli account utente di provisioning AAD.


###<a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory
  
L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua MCM.
    
![Assegnare agli utenti] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_00.png "Assegnare agli utenti")

**Per assegnare Britta Simon MCM, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Assegnare agli utenti] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_01.png "Assegnare agli utenti")

2. Nell'elenco delle applicazioni, selezionare **MCM**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-mcm-tutorial/tutorial_mcm_08.png)

1. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare agli utenti] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_02.png "Assegnare agli utenti")

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.
    
    ![Assegnare agli utenti] (./media/active-directory-saas-mcm-tutorial/assign_aaduser_03.png "Assegnare agli utenti")


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro MCM nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione MCM.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)