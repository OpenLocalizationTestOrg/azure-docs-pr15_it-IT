<properties
    pageTitle="Esercitazione: Azure Active Directory integrazione Tableau Online | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Tableau Online."
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
    ms.date="10/18/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Esercitazione: Azure Active Directory integrazione Tableau Online

In questa esercitazione si imparerà a integrare Tableau Online con Azure Active Directory (Azure Active Directory).

Integrazione Tableau Online con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Tableau Online
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Tableau Online (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Tableau Online, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un **Tableau Online** single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Tableau Online dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-tableau-online-from-the-gallery"></a>Aggiunta di Tableau Online dalla raccolta
Per configurare l'integrazione di Tableau Online in Azure Active Directory, è necessario aggiungere Tableau Online dalla raccolta per l'elenco di App SaaS gestito.

**Per aggiungere Tableau Online dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Tableau Online**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_01.png)

7. Nel riquadro dei risultati, selezionare **Tableau Online**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con Tableau Online in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente in Tableau Online a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente in Tableau Online deve essere stabilito.
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Tableau Online.

Per configurare e testare Azure Active Directory single sign-on con Tableau Online, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di una linea di Tableau test utente](#creating-a-Tableau-Online-test-user)** - che equivalente di Britta Simon nel Tableau Online collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on nell'applicazione Tableau Online.

**Per configurare Azure Active Directory single sign-on con Tableau Online, procedere come segue:**

1. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On][6]
2. Nel portale classico, nella pagina di integrazione di applicazione **Tableau Online** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][7] 

3. Nella pagina **specificare come gli utenti di accedere al Tableau Online** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_06.png)

4. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue: 

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_07.png)


    un. Nella casella di testo su URL di accesso, digitare un URL utilizzando il modello seguente:`https://sso.online.tableau.com`

    c. Fare clic su **Avanti**.

5. Nella pagina **Configura il single sign-on Tableau online** fare clic su **Download dei metadati**e quindi salvare il file nel computer in uso.

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_08.png)

6. Selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]
8. In una nuova finestra del browser, accesso all'applicazione Tableau Online. Passare a **Impostazioni** e quindi **l'autenticazione**

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)

9. Nella sezione **Tipi di autenticazione** . Selezionare la casella di controllo **Single sign-on con SAML** per abilitare SAML.

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

10. Scorrere verso il basso fino a **importare file di metadati in Tableau Online** sezione.  Fare clic su Sfoglia e importare il file di metadati scaricato da Azure Active Directory. Quindi fare clic su **Applica**.

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

11. Nella sezione **asserzioni corrispondenza** , inserire il nome di asserzione Provider di identità corrispondente per indirizzo di posta elettronica, nome e cognome. Per informazioni da Azure Active Directory:

    un. Tornare a Azure Active Directory. Nel portale di classica Azure, della pagina di integrazione di applicazione **Tableau Online** , nel menu nella parte superiore, fare clic su **attributi**. Copiare il nome per i valori: userprincipalname, givenname e il cognome.
     
    ![Azure Active Directory Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)

    b. Passare all'applicazione Tableau Online, quindi impostare la sezione **Attributi Online Tableau** i seguenti:
    
    -  Posta elettronica: **posta elettronica** o **userprincipalname**
    -  Nome: **givenname**
    -  Cognome: **Cognome**

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-tableau-online-test-user"></a>Creazione di un utente di test Tableau Online

In questa sezione, si crea un utente chiamato Britta Simon in Tableau Online.

1. Nella **Tableau Online**fare clic su **Impostazioni** e quindi sezione **autenticazione** . Scorrere fino alla sezione **Seleziona utenti** . Fare clic su **aggiungere utenti** e quindi **Immettere gli indirizzi di posta elettronica**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Selezionare **Aggiungi utenti per single sign-on (SSO) l'autenticazione**. Nella casella di testo **Immettere gli indirizzi di posta elettronica** aggiungerebritta.simon@contoso.com

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)

3.  Fare clic su **Crea**.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo utente l'accesso a Tableau Online.

![Assegnare utente][200] 

**Per assegnare Britta Simon Tableau Online, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

3. Nell'elenco delle applicazioni, selezionare **Tableau Online**.

    ![Configurare Single Sign-On](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_50.png) 

4. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

5. Nell'elenco di tutti gli utenti, selezionare **Sandro**.

6. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Tableau Online nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Tableau Online.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_205.png
