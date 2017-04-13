<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Beeline | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Beeline."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-beeline"></a>Esercitazione: Integrazione di Azure Active Directory con Beeline

In questa esercitazione si imparerà a integrare Beeline con Azure Active Directory (Azure Active Directory).

Integrazione di Beeline con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Beeline
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Beeline (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Beeline, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Beeline single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Beeline dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-beeline-from-the-gallery"></a>Aggiunta di Beeline dalla raccolta
Per configurare l'integrazione di Beeline in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Beeline dalla raccolta.

**Per aggiungere Beeline dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Beeline**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_01.png)

7. Nel riquadro dei risultati, selezionare **Beeline**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con Beeline in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare sia l'utente corrispondente in Beeline a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Beeline deve essere stabilito.
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Beeline.

Per configurare e testare Azure Active Directory single sign-on con Beeline, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un Beeline test utente](#creating-an-beeline-test-user)** - che equivalente di Britta Simon nel Beeline collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione Beeline.

L'applicazione Beeline prevede asserzioni SAML in un formato specifico. Lavorare con il team Beeline prima di tutto per identificare l'identificatore di utente corrette verrà mappata nell'applicazione. Dedicare, le indicazioni del team Beeline sull'attributo che si desidera utilizzare per questo mapping. Si consiglia di utilizzare l'attributo **"NameIdentifier"** come ID utente. È possibile gestire il valore dell'attributo dalla scheda **"Atrribute"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. Di seguito è abbiamo mappati Richiedi nameidentifier con l'attributo **userprincipalname** che fornisce l'ID utente univoco, che verrà inviata all'applicazione Beeline in ogni risposta SAML esito negativo.

![Configurare Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_07.png) 


**Per configurare Azure Active Directory single sign-on con Beeline, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Beeline** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

     ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al Beeline** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti:.

    ![Configurare Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_04.png) 


    un. Nella casella di testo **identificatore** , digitare l'URL utilizzato dagli utenti per accesso all'applicazione Beeline utilizzando il modello seguente:`https://projects.beeline.net/<instance name>`

    b. L'URL di risposta digitare l'URL nel modello di esempio: `https://projects.beeline.net/<instance name>/SSO_External.ashx` o`https://projects.beeline.net/<company name>/SSO_External.ashx`


4. Nella pagina **Configura il single sign-on in Beeline** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_05.png) 

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5.  Per ottenere SSO configurato per l'applicazione, contatto team di supporto Beeline e viene spiegato come per configurare Single Sign-on. Si noti che è necessario inviare posta elettronica e allegare il file di metadati scaricati e offrono l'ID di entità e accesso singolo all'URL del servizio.
  
6. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-beeline-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-an-beeline-test-user"></a>Creazione di un utente di test Beeline

In questa sezione, si crea un utente chiamato Britta Simon in Beeline. Applicazione di beeline necessario tutti gli utenti di eseguire il provisioning nell'applicazione prima di eseguire Single Sign-On. Fare in modo lavoro con il cliente Beeline associazione per eseguire il provisioning di tutti gli utenti nell'applicazione di supporto. 


> [AZURE.NOTE] Se è necessario creare manualmente un utente o batch di utenti, è necessario contattare il team di supporto Beeline.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua Beeline.

![Assegnare utente][200] 

**Per assegnare Britta Simon Beeline, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Beeline**.

    ![Configurare Single Sign-On](./media/active-directory-saas-beeline-tutorial/tutorial_beeline_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
Quando si fa clic sul riquadro Beeline nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Beeline.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-beeline-tutorial/tutorial_general_205.png
