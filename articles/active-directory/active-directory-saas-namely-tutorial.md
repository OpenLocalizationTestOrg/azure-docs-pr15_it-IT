<properties
    pageTitle="Esercitazione: Azure Active Directory integrazione vale a dire | Microsoft Azure"
    description="Informazioni su come configurare il single sign-on tra Azure Active Directory e vale a dire."
    services="active-directory"
    documentationCenter=""
    authors="jeevansd"
    manager="prasannas"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-namely"></a>Esercitazione: Azure Active Directory integrazione vale a dire

L'obiettivo di questa esercitazione è illustrano come integrare vale a dire con Azure Active Directory (Azure Active Directory).

Integrazione vale a dire con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi è autorizzato ad accedere a vale a dire 
- È possibile abilitare gli utenti ottenere automaticamente la firma a vale a dire (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare Azure AD integrazione con vale a dire, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un vale a dire single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing. 

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di vale a dire dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-namely-from-the-gallery"></a>Aggiunta di vale a dire dalla raccolta
Per configurare l'integrazione di vale a dire in Azure Active Directory, è necessario aggiungere vale a dire dalla raccolta per l'elenco di App SaaS gestito.

**Per aggiungere vale a dire dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **vale a dire**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/tutorial_namely_01.png)

7. Nel riquadro dei risultati, **vale a dire**selezionare e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/tutorial_namely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con vale a dire in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in vale a dire a un utente in Active Directory Azure Azure Active Directory. In altre parole, vale a dire una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente in è opportuno stabilire.

È stata stabilita la relazione di collegamento assegnando vale a dire il valore di **nome utente** in Azure Active Directory come il valore di **nome utente** in.
 
Per configurare e testare Azure Active Directory single sign-on con vale a dire, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[La creazione di un test vale a dire utente](#creating-a-namely-test-user)** - disporre di un equivalente di Britta Simon in vale a dire che è collegato per la rappresentazione di Azure Active Directory del proprio.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on nel vale a dire applicazione. 




**Per configurare Azure Active Directory single sign-on con vale a dire, procedere come segue:**

1. Nel portale di classica Azure, della pagina **vale a dire** applicazione integrazione, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come agli utenti di accedere a, vale a dire** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
 
    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti:.

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti di accedere al vale a dire applicazione (ad esempio: *https://fabrikam.Namely.com/*).

    b. Fare clic su **Avanti**.
 
 
4. Nella pagina **configurare single sign-on in vale a dire** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


1. In un'altra finestra del browser, accedere al sito di società vale a dire come amministratore.

1. Nella barra degli strumenti nella parte superiore, fare clic su **società**.

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_06.png) 

1. Fare clic sulla scheda **Impostazioni** .

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_07.png) 


1. Fare clic su **SAML**.

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_08.png) 


1. Nella pagina **Impostazioni SAML** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_09.png) 

    un. Fare clic su **Attiva SAML**. 

    b. Nel portale di classica Azure, nella pagina **configurare single sign-on in vale a dire** copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **url sillaba DDO del provider di identità** . 

    c. Aprire il certificato scaricato nel blocco note, copiare il contenuto e quindi incollarla nella casella di testo **certificato di provider di identità** .    

    d. Fare clic su **Salva**.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]


**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_09.png)  

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_05.png)  

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_06.png) 
 
    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-namely-tutorial/create_aaduser_08.png) 
  
    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

  
 
### <a name="creating-a-namely-test-user"></a>Creazione di un test vale a dire utente

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in, vale a dire.

**Per creare un utente chiamato Britta Simon in, vale a dire, procedere come segue:**

1. Sign-on per il sito di società vale a dire come amministratore.

1. Nella barra degli strumenti nella parte superiore, fare clic su **persone**.

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_10.png) 

1. Fare clic sulla scheda **Directory** .

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_11.png) 

1. Fare clic su **Aggiungi nuovo utente**.



1. Nella finestra di dialogo **Aggiungi nuovo utente** , procedere come segue:

    un. Nella casella di testo **nome** digitare **Sandro**.

    b. Nella casella di testo **Cognome** digitare **Ezio**.

    c. Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di Sandro nel portale di classica Azure.

    d. Fare clic su **Salva**.





### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo accesso a, vale a dire.

![Assegnare utente][200] 

**Per assegnare Britta Simon vale a dire, eseguire i passaggi seguenti:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **vale a dire**.

    ![Configurare Single Sign-On](./media/active-directory-saas-namely-tutorial/tutorial_namely_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic il riquadro vale a dire nel Pannello di accesso, è necessario ottenere automaticamente firmato-on per l'applicazione vale a dire.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-namely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-namely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-namely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-namely-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-namely-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-namely-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-namely-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-namely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-namely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-namely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-namely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-namely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-namely-tutorial/tutorial_general_205.png






