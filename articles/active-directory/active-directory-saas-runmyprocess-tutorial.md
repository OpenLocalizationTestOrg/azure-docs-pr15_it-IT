<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con RunMyProcess | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e RunMyProcess."
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
    ms.date="10/21/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Esercitazione: Integrazione di Azure Active Directory con RunMyProcess

L'obiettivo di questa esercitazione è illustrano come integrare RunMyProcess con Azure Active Directory (Azure Active Directory).

Integrazione di RunMyProcess con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a RunMyProcess
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a RunMyProcess (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con RunMyProcess, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un RunMyProcess single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di RunMyProcess dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-runmyprocess-from-the-gallery"></a>Aggiunta di RunMyProcess dalla raccolta
Per configurare l'integrazione di RunMyProcess in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito RunMyProcess dalla raccolta.

**Per aggiungere RunMyProcess dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **RunMyProcess**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_01.png)

7. Nel riquadro dei risultati, selezionare **RunMyProcess**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con RunMyProcess in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, Azure Active Directory è necessario conoscere sia l'utente corrispondente in RunMyProcess a un utente in Active Directory Azure è. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente RunMyProcess deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in RunMyProcess.

Per configurare e testare Azure Active Directory single sign-on con RunMyProcess, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un RunMyProcess test utente](#creating-a-runmyprocess-test-user)** - che equivalente di Britta Simon nel RunMyProcess collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.


### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione RunMyProcess.

**Per configurare Azure Active Directory single sign-on con RunMyProcess, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **RunMyProcess** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al RunMyProcess** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare un URL utilizzando il modello seguente: `https://live.runmyprocess.com/live/<tenant id>`.
        
    b. Fare clic su **Avanti**

    > [AZURE.NOTE] Si noti che è necessario aggiornare il valore con l'accesso in URL effettivo. Per ottenere questo valore, contattare il team di supporto RunMyProcess tramite <mailto:support@runmyprocess.com>.
 
4. Nella pagina **Configura il single sign-on in RunMyProcess** fare clic su **Scarica certificato** e quindi salvare il file nel computer in uso:

    ![Configurare Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_05.png)

5. In una finestra del browser web diverso, accesso al tenant di RunMyProcess come amministratore.

6. Nel riquadro di spostamento sinistra, fare clic su **Account** e selezionare **la configurazione**.

    ![Configurare Single Sign-On sul lato App](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_001.png)

7. Passare alla sezione **metodo di autenticazione** e sotto i passaggi da eseguire:

    ![Configurare Single Sign-On sul lato App](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    un. Come **metodo**, selezionare **Single Sign-On con Samlv2**.

    b. In **Single Sign-on reindirizzare** casella di testo immettere il valore **dell'URL di Single Sign-on SAML** dalla configurazione guidata di Azure Active Directory applicazione.

    c. In **disconnessione reindirizzare** casella di testo immettere il valore **dell'URL del servizio Sign-Out singola** dalla configurazione guidata di Azure Active Directory applicazione.

    d. In **Nome Id formato** casella di testo inserire il valore di **Nome identificatore Format** dalla configurazione guidata di Azure Active Directory applicazione.

    e. Copiare il contenuto del file di certificato scaricato e quindi incollarla nella casella di testo **certificato** . 

    f. Fare clic sull'icona **Salva** .
    
8. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

9. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_01.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_02.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_03.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , eseguire le operazioni seguenti:  ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_04.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , eseguire le operazioni seguenti: ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_05.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_06.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-runmyprocess-tutorial/create_aaduser_07.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

### <a name="creating-a-runmyprocess-test-user"></a>Creazione di un utente di test RunMyProcess
  
Per consentire agli utenti di Azure Active Directory per l'accesso a RunMyProcess, è necessario essere effettuato il provisioning in RunMyProcess. In caso di RunMyProcess, il provisioning è un'attività manuale.

#### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere al sito aziendale RunMyProcess come amministratore.

2.  Fare clic su **Account** e selezionare **gli utenti** nel riquadro di spostamento sinistro, quindi fare clic su **Nuovo utente**.

    ![I nuovi utenti] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_003.png "I nuovi utenti")

3.  Nella sezione **Impostazioni utente** , procedere come segue:

    ![Profilo] (./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profilo")

    un. Digitare il **nome** e il **messaggio di posta elettronica** di un account valido AAD che si desidera eseguire il provisioning nelle caselle di testo correlati.

    b. Selezionare una **lingua IDE**, una **lingua** e un **profilo**.

    c. Selezionare **Invia messaggio di creazione di account per uno specifico utente**.

    d. Fare clic su **Salva**.

    >[AZURE.NOTE] È possibile utilizzare qualsiasi altro RunMyProcess utente account strumenti di creazione o API fornite dal RunMyProcess effettuare il provisioning di Azure Active Directory gli account utente.
    

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua RunMyProcess.

![Assegnare utente][200] 

**Per assegnare Britta Simon RunMyProcess, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **RunMyProcess**.

    ![Configurare Single Sign-On](./media/active-directory-saas-runmyprocess-tutorial/tutorial_runmyprocess_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. sulla barra nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro RunMyProcess nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione RunMyProcess.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-runmyprocess-tutorial/tutorial_general_205.png
