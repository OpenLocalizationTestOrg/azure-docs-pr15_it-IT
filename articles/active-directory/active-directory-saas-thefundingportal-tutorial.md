<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il portale di fondi | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e il portale di fondi."
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
    ms.date="09/02/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-the-funding-portal"></a>Esercitazione: Integrazione di Azure Active Directory con il portale di fondi

In questa esercitazione si imparerà a integrare il portale di fondi con Azure Active Directory (Azure Active Directory).

Integrazione di portale di fondi con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso al portale di fondi
- È possibile consentire agli utenti di automaticamente ottenere firmato-on per il portale di fondi (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con il portale di fondi, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un **Portale di fondi** single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di portale di fondi dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-the-funding-portal-from-the-gallery"></a>Aggiunta di portale di fondi dalla raccolta
Per configurare l'integrazione del portale di fondi di Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito nel portale di fondi dalla raccolta.

**Per aggiungere il portale di fondi dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca, digitare **Il portale di fondi**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_01.png)

7. Nel riquadro dei risultati, selezionare **Il portale di fondi**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con il portale fondi in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente nel portale di fondi a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nel portale di fondi deve essere stabilito.
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nel portale di fondi.

Per configurare e testare Azure Active Directory single sign-on con il portale di fondi, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un portale di fondi testare utente](#creating-a-the-funding-portal-test-user)** - abbiano equivalente di Britta Simon nel portale di fondi collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on nell'applicazione nel portale di fondi.

L'applicazione di fondi portale prevede asserzioni SAML per contenere un attributo denominato "externalId1". Il valore di "externalId1" deve essere un studentID riconosciuto. Configurare la domanda "externalId1" di questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrributes"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto.

![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_03.png) 

**Per configurare Azure Active Directory single sign-on con il portale di fondi, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Portale di fondi** , nel menu nella parte superiore, fare clic su **attributi**.
     
    ![Configurare Single Sign-On][5]

2. Nella finestra di dialogo attributi token SAML, aggiungere l'attributo "externalId1".

    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi l'attributo utente** . 
    
    ![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_05.png)

    b. Nella casella di testo **Nome attributo** , digitare il nome attributo "externalId1".

    c. Nell'elenco **Valore dell'attributo** , selezionare l'attributo che si desidera utilizzare per l'implementazione. Ad esempio, se sono stati archiviati valore StudentID nel ExtensionAttribute1, quindi selezionare user.extensionattribute1.

    d. Fare clic su **completa**. Quindi fare clic su **Apply Changes**.

1. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On][6]

2. Nel portale classico, nella pagina integrazione applicazione **Portale di fondi** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][7] 

3. Nella pagina **specificare come gli utenti di accedere al portale di fondi** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_06.png)

4. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue: 

    ![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_07.png)


    un. Nella casella di testo su URL di accesso, digitare un URL utilizzando il modello seguente: `https://<subdomain>.regenteducation.net/`.

    b. Fare clic su **Avanti**.

5. Nella pagina **Configura il single sign-on al portale di fondi** fare clic su **Download dei metadati**e quindi salvare il file nel computer in uso.

    ![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_08.png)

6. Per ottenere SSO configurato per l'applicazione, contattare il supporto tecnico nel portale di fondi. Sono aiuterà con il canale appropriato per configurare Single Sign-on. Si noti che è necessario inviare posta elettronica e allegare scaricati file di metadati info@regenteducation.com.

7. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

8. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-thefundingportal-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-the-funding-portal-test-user"></a>Creazione di un utente di test nel portale di fondi

In questa sezione, si crea un utente chiamato Britta Simon nel portale di fondi. Se non si conosce come aggiungere Britta Simon nel portale di fondi, funzionano con il team di supporto nel portale di fondi per aggiungere l'utente prova e attivare Single Sign-on. Contattare in info@regenteducation.com.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo utente l'accesso al portale di fondi.

![Assegnare utente][200] 

**Per assegnare Britta Simon al portale di fondi, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Il portale di fondi**.

    ![Configurare Single Sign-On](./media/active-directory-saas-thefundingportal-tutorial/tutorial_thefundingportal_09.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di tutti gli utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro nel portale di fondi nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione nel portale di fondi.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-thefundingportal-tutorial/tutorial_general_205.png
