<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ImageRelay | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e ImageRelay."
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
    ms.date="08/15/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-imagerelay"></a>Esercitazione: Integrazione di Azure Active Directory con ImageRelay

L'obiettivo di questa esercitazione è illustrano come integrare ImageRelay con Azure Active Directory (Azure Active Directory).  
Integrazione di ImageRelay con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a ImageRelay
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a ImageRelay (single sign-on) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con ImageRelay, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un ImageRelay single sign-on abilitato abbonamento


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di ImageRelay dalla raccolta

2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-imagerelay-from-the-gallery"></a>Aggiunta di ImageRelay dalla raccolta
Per configurare l'integrazione di ImageRelay in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito ImageRelay dalla raccolta.

**Per aggiungere ImageRelay dalla raccolta, procedere come segue:**

1. Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **ImageRelay**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_01.png)

7. Nel riquadro dei risultati, selezionare **ImageRelay**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con ImageRelay in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, Azure Active Directory richiede un account utente che rappresenta l'utente correlato in ImageRelay.  In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente ImageRelay deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in ImageRelay.

Per configurare e testare Azure Active Directory single sign-on con ImageRelay, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un ImageRelay test utente](#creating-a-userecho-test-user)** - che equivalente di Britta Simon nell'ImageRelay collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on ImageRelay nell'applicazione in uso.


**Per configurare Azure Active Directory single sign-on con ImageRelay, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **ImageRelay** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

     ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al ImageRelay** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

     ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per accedere all'applicazione ImageRelay (ad esempio: *https://fabrikam.ImageRelay.com/*).

    b. Fare clic su **Avanti**.

4. Nella pagina **Configura il single sign-on in ImageRelay** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.

5. In un'altra finestra del browser, accedere al sito aziendale ImageRelay come amministratore.

1. Nella barra degli strumenti nella parte superiore, fare clic sul carico di lavoro di **utenti e autorizzazioni** .

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_06.png) 

1. Fare clic su **Crea nuova autorizzazione**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_08.png) 

1. **Accesso singolo in impostazioni** del carico di lavoro di Excel, selezionare la casella di controllo **questo gruppo possono solo accesso aggiuntivo tramite Single Sign-On** e quindi fare clic su **Salva**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_09.png) 

1. Passare a **Impostazioni Account**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_10.png) 

1. Passare al carico di lavoro di **Accesso in impostazioni servizio Single** .

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_11.png)

1. Nella finestra di dialogo **Impostazioni SAML** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_12.png)

    un. Nel portale di classica Azure, copiare l' **URL del servizio Single Sign-On**e quindi incollarla nella casella di testo **URL di accesso** .


    b. Nel portale di classica Azure, copiare l' **URL del servizio Sign-Out singolo**e quindi incollarla nella casella di testo **URL disconnessione** .

    c. Come **Formato del nome Id**, selezionare **urn: oasis: nomi: tc: SAML:1.1:nameid-formato: emailAddress**.

    
    d. **Opzioni di rilegatura per le richieste dal Provider di servizi (immagine inoltro)**, selezionare **Associazione POST**.
   

    e. In **x. 509 certificato**, fare clic su **Aggiorna certificato**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Aprire il certificato scaricato nel blocco note, copiare il contenuto e quindi incollarla nella casella di testo di certificato x. 509.
  
    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. Nella sezione **JIT il Provisioning dell'utente** , selezionare il **Provisioning di abilitare JIT dell'utente**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Selezionare il gruppo di autorizzazioni (ad esempio, di **Base di Single Sign-on**) che è possibile accedere solo tramite il single sign-on.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_20.png)

    si. Fare clic su **Salva**.

6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.

    ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-imagerelay-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-imagerelay-test-user"></a>Creazione di un utente di test ImageRelay

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in ImageRelay.

**Per creare un utente chiamato Britta Simon in ImageRelay, procedere come segue:**

1. Accesso al sito della società ImageRelay come amministratore.

1. Passare a **utenti e autorizzazioni** e selezionare **Crea utente Single Sign-on**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_21.png) 

1. Immettere il **messaggio di posta elettronica**, **nome**, **Cognome** e **della società** dell'utente che si desidera eseguire il provisioning e selezionare il gruppo autorizzazione (ad esempio Single Sign-on di base), ovvero il gruppo che può accedere solo tramite il single sign-on.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_22.png) 

1. Fare clic su **Crea**.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua ImageRelay.

![Assegnare utente][200] 

**Per assegnare Britta Simon ImageRelay, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **ImageRelay**.

    ![Configurare Single Sign-On](./media/active-directory-saas-imagerelay-tutorial/tutorial_imagerelay_23.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro ImageRelay nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione ImageRelay.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-imagerelay-tutorial/tutorial_general_205.png
