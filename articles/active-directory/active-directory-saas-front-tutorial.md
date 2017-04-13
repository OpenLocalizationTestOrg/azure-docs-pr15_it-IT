<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con anteriore | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e primo piano."
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
    ms.date="10/24/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-front"></a>Esercitazione: Integrazione di Azure Active Directory primo piano

L'obiettivo di questa esercitazione è illustrano come integrare anteriore con Azure Active Directory (Azure Active Directory).

Integrazione di primo piano con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso in primo piano
- È possibile consentire agli utenti di automaticamente ottenere firmato-on in primo piano (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory di primo piano, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Front single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di primo piano dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-front-from-the-gallery"></a>Aggiunta di primo piano dalla raccolta
Per configurare l'integrazione di primo piano in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito anteriore dalla raccolta.

**Per aggiungere anteriore dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **anteriore**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/tutorial_front_01.png)

7. Nel riquadro risultati selezionare **anteriore**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-front-tutorial/tutorial_front_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on di primo piano in base a un utente di test denominato "Britta Simon".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in primo piano a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e in primo piano il relativo utente deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in primo piano.

Per configurare e testare Azure Active Directory single sign-on con primo piano, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un Front testare utente](#creating-a-front-test-user)** - disporre di un equivalente di Britta Simon davanti collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione di primo piano.

**Per configurare Azure Active Directory single sign-on con anteriore, procedere come segue:**

1. Nel portale classico, sulla **prima** pagina di integrazione di applicazione, fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **da come si desidera agli utenti di eseguire l'accesso in primo piano** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_03.png)

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , se si desidera configurare l'applicazione in **IDP avviata modalità**, eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_04.png)

    un. Nella casella di testo **identificatore** , digitare un URL utilizzando il modello seguente:`https://<company name>.frontapp.com`

    b. Nella casella di testo **URL di risposta** , digitare un URL utilizzando il modello seguente:`https://<company name>.frontapp.com/sso/saml/callback`

    c. Fare clic su **Avanti**

4. Se si desidera configurare l'applicazione in **SP avviata modalità** nella pagina **Configura impostazioni di App** finestra di dialogo, quindi fare clic su **"Mostra avanzate impostazioni (facoltativo)"** e quindi immettere l' **URL in accesso** e fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_05.png)

    un. Nella casella di testo **Su URL di accesso** , digitare un URL utilizzando il modello seguente:`https://<company name>.frontapp.com`

    b. Fare clic su **Avanti**

    > [AZURE.NOTE] Si noti che queste non sono valori reali. È necessario aggiornare questi valori con il segno su URL, identificatore e l'URL di risposta effettivo. Per ottenere questi valori, è possibile per informazioni dettagliate, vedere **passaggio 12** o contattare anteriore tramite [support@frontapp.com](emailTo:support@frontapp.com).

5. Nella pagina **Configura il single sign-on in primo piano** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_06.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.

6. Accesso al tenant di primo piano come amministratore.

7. Passare a **Impostazioni (icona cog nella parte inferiore dell'intestazione laterale sinistra) > Preferenze**.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_000.png)

8. Fare clic sul collegamento **Single Sign-On** .

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_001.png)

9. Selezionare **SAML** nella casella di riepilogo del **Single Sign-On**.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_002.png)

10. **Punto di ingresso** casella di testo immettere il valore **dell'URL del servizio Single Sign-on** dalla configurazione guidata di Azure Active Directory applicazione.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_003.png)

11. Copiare il contenuto del file di certificato scaricato e quindi incollarla nella casella di testo **certificato di firma** .

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_004.png)

12. Verificare che questi URL corrispondano alla configurazione nel passaggio 3.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-front-tutorial/tutorial_front_005.png)

13. Fare clic su **Salva** .

14. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

15. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_09.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_05.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_06.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_07.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-front-tutorial/create_aaduser_08.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-front-test-user"></a>Creazione di un utente di test di primo piano

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon Front.Please lavoro con il team di supporto di primo piano per aggiungere gli utenti nella finestra account di primo piano.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure mediante la concessione di accesso in primo piano.
    
![Assegnare utente][200]

**Per assegnare Britta Simon in primo piano, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **anteriore**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-front-tutorial/tutorial_front_50.png)

1. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare utente][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.
    
    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro di primo piano nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on per l'applicazione di primo piano.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-front-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-front-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-front-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-front-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-front-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-front-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-front-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-front-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-front-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-front-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-front-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-front-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-front-tutorial/tutorial_general_205.png
