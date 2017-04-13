<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con BetterWorks | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e BetterWorks."
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


# <a name="tutorial-azure-active-directory-integration-with-betterworks"></a>Esercitazione: Integrazione di Azure Active Directory con BetterWorks

L'obiettivo di questa esercitazione è illustrano come integrare BetterWorks con Azure Active Directory (Azure Active Directory).

Integrazione di BetterWorks con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a BetterWorks
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a BetterWorks (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con BetterWorks, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un BetterWorks single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di BetterWorks dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-betterworks-from-the-gallery"></a>Aggiunta di BetterWorks dalla raccolta
Per configurare l'integrazione di BetterWorks in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito BetterWorks dalla raccolta.

**Per aggiungere BetterWorks dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **BetterWorks**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_01.png)

7. Nel riquadro dei risultati, selezionare **BetterWorks**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con BetterWorks in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in BetterWorks a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente BetterWorks deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in BetterWorks.

Per configurare e testare Azure Active Directory single sign-on con BetterWorks, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un BetterWorks test utente](#creating-a-betterworks-test-user)** - che equivalente di Britta Simon nel BetterWorks collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on BetterWorks nell'applicazione in uso.

Applicazione BetterWorks prevede asserzioni SAML in un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori degli attributi dalla scheda "**Atrribute**" dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. 

![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_06.png)

**Per configurare Azure Active Directory single sign-on con BetterWorks, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **BetterWorks** , nel menu nella parte superiore, fare clic su **attributi**.

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_07.png)

2. Nella finestra di dialogo **attributi token SAML** per ogni riga nella tabella riportata di seguito, procedere come segue:
    

  	| Nome attributo | Valore dell'attributo |
  	| --- | --- |    
  	| saml_token | bd189cf6-1701-11e6-8f90-d26992eca2a5 |

    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi utente Attribure** .

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_12.png)
    
    b. Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.
    
    c. Nell'elenco **Valore dell'attributo** digitare l'ID di token saml visualizzata per tale riga.
    
    d. Fare clic su **completa**

3. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_11.png) 

4. Nella pagina **specificare come gli utenti di accedere al BetterWorks** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_03.png)

5. Nella pagina, finestra di dialogo **Configura impostazioni di App** se si desidera configurare l'applicazione in **IDP avviata modalità**, procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_04.png)


    un. Nella casella di testo **identificatore** , digitare l'URL nel modello di seguito:`https://app.betterworks.com/saml2/metadata/`


    b. Nella casella di testo **URL di risposta** , digitare l'URL nel modello di seguito:`https://app.betterworks.com/saml2/acs/`


    c. Fare clic su **Avanti**

6. Nella pagina finestra di dialogo **Configura impostazioni di App** se si desidera configurare l'applicazione in **SP avviata modalità**, eseguire nella procedura seguente:

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_10.png)

    un.  Selezionare **Mostra avanzate impostazioni (facoltativo)**.



    b. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione BetterWorks utilizzando il modello seguente:`https://app.betterworks.com`

    b. Fare clic su **Avanti**.

7. Nella pagina **Configura il single sign-on in BetterWorks** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_05.png)

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.

8. Per ottenere SSO configurato per l'applicazione, contattare il team di supporto BetterWorks tramite <mailto:support@betterworks.com>. Allegare il file di metadati scaricati e condividerlo con team BetterWorks configurare Single Sign-on sul lato "i".

9. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_09.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_05.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_06.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_07.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-betterworks-tutorial/create_aaduser_08.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-betterworks-test-user"></a>Creazione di un utente di test BetterWorks

In questa sezione, si crea un utente chiamato Britta Simon in BetterWorks. 

Rivolgersi al team di supporto BetterWorks tramite <mailto:support@betterworks.com> per aggiungere gli utenti della piattaforma BetterWorks.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua BetterWorks.
    
   ![Assegnare utente][200]

**Per assegnare Britta Simon BetterWorks, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **BetterWorks**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-betterworks-tutorial/tutorial_betterworks_50.png)

1. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare utente][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.
    
    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro BetterWorks nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione BetterWorks.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-betterworks-tutorial/tutorial_general_205.png
