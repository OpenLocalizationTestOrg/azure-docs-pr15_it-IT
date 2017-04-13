<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con informazioni su Lavagna | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e informazioni su Lavagna."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn"></a>Esercitazione: Integrazione di Azure Active Directory con informazioni su Lavagna

In questa esercitazione si imparerà a integrare informazioni su lavagna con Azure Active Directory (Azure Active Directory).

Integrazione di informazioni su lavagna con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a informazioni su Lavagna
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a informazioni su Lavagna (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con informazioni su Lavagna, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Una piattaforma Cloud informazioni su Lavagna single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di informazioni su Lavagna dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-blackboard-learn-from-the-gallery"></a>Aggiunta di informazioni su Lavagna dalla raccolta
Per configurare l'integrazione di informazioni su Lavagna in Azure Active Directory, è necessario aggiungere informazioni su Lavagna dalla raccolta per l'elenco di App SaaS gestito.

**Per aggiungere informazioni su Lavagna nella raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]
2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Informazioni su Lavagna**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_01.png)

7. Nel riquadro dei risultati, selezionare **Informazioni su Lavagna**e quindi fare clic su **completa** per aggiungere l'applicazione.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_06.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con informazioni su Lavagna in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente nella Lavagna informazioni a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente in informazioni su Lavagna deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in informazioni su Lavagna.

Per configurare e testare Azure Active Directory single sign-on con informazioni su Lavagna, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di una lavagna informazioni su Verifica utente](#creating-a-blackboard-learn-test-user)** - che equivalente di Britta Simon nel Lavagna informazioni che è collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione informazioni su Lavagna.

Lavagna informazioni applicazione prevede asserzioni SAML in un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrribute"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. 

![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_51.png) 

**Per configurare Azure Active Directory single sign-on con informazioni su Lavagna, procedere come segue:**


1. Nel portale di classica Azure, della pagina **Informazioni su Lavagna** applicazione integrazione, nel menu nella parte superiore, fare clic su **attributi**.

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_80.png) 


1. Nella finestra di dialogo **attributi token SAML** per ogni riga nella tabella riportata di seguito, eseguire le operazioni seguenti: È stata mappare Userprincipalname come l'attributo di utente univoco, ma è possibile mappare sul valore appropriato che consentono di identificare l'utente nell'organizzazione e che esegue il mapping a Lavagna informazioni campo username.

  	| Nome attributo | Valore dell'attributo |
  	| --- | --- |    
  	| urn:oid:1.3.6.1.4.1.5923.1.1.1.6  | User.userPrincipalName |
   
 
    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi utente Attribure** .

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_81.png) 


    b. Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.

    c. Nell'elenco **Valore dell'attributo** selsect il valore dell'attributo visualizzato per tale riga.

    d. Fare clic su **completa**.  

2. Nel portale classico, nella pagina integrazione applicazione **Informazioni su Lavagna** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

3. Nella pagina **specificare come agli utenti di effettuare l'accesso a informazioni su Lavagna** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_03.png) 

4. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione Lavagna informazioni utilizzando il modello seguente: **https://\<società prezzi nome\>.blackboard.com/**
    
    b. Fare clic su **Avanti**
 
5. Nella pagina **Configura il single sign-on in informazioni su Lavagna** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_05.png)

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


6. Per ottenere SSO configurato per l'applicazione, contattare il team di supporto lavagna informazioni e fornire le operazioni seguenti:

    • Metadati scaricati


7. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

8. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , eseguire le operazioni seguenti:  ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , eseguire le operazioni seguenti: ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-blackboard-learn-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-an-blackboard-learn-test-user"></a>Creazione di un utente di test informazioni su Lavagna

In questa sezione, si crea un utente chiamato Britta Simon in informazioni su Lavagna. 

Lavagna informazioni applicazione supporta solo per il provisioning utente ora. Assicurarsi di aver configurato le attestazioni come descritto nella sezione ** [Configurazione Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)**

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure come consentire l'accesso a informazioni su Lavagna.

![Assegnare utente][200] 

**Per assegnare Britta Simon per informazioni su Lavagna, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Informazioni su Lavagna**.

    ![Configurare Single Sign-On](./media/active-directory-saas-blackboard-learn-tutorial/tutorial_blackboardlearn_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test Single Sign-On

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Lavagna informazioni supporto applicazioni quando si fa clic sul riquadro informazioni su Lavagna nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso all'applicazione informazioni su Lavagna.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-blackboard-learn-tutorial/tutorial_general_205.png
