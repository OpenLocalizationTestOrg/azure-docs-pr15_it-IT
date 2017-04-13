<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Vice | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e vice."
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
    ms.date="09/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Esercitazione: Integrazione di Azure Active Directory con Vice

L'obiettivo di questa esercitazione è illustrano come integrare Vice con Azure Active Directory (Azure Active Directory).

Integrazione Vice con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Vice
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Vice (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Vice, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Una parte single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Vice dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-deputy-from-the-gallery"></a>Aggiunta di Vice dalla raccolta
Per configurare l'integrazione di Vice in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Vice dalla raccolta.

**Per aggiungere Vice dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Vice**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_01.png)

7. Nel riquadro risultati selezionare **Vice**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con Vice in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nella parte a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Vice deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come valore di **nome utente** nella parte.

Per configurare e testare Azure Active Directory single sign-on con Vice, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un agente di test utente](#creating-a-deputy-test-user)** - che equivalente di Britta Simon nel Vice collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione Vice.

**Per configurare Azure Active Directory single sign-on con Vice, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Vice** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al vice** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_03.png)

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , se si desidera configurare l'applicazione in **IDP avviata modalità**, eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_04.png)

    un. Nella casella di testo **identificatore** , digitare un URL utilizzando il modello seguente: `https://<your-subdomain>.<region>.deputy.com`.

    b. Nella casella di testo **URL di risposta** , digitare un URL utilizzando il modello seguente: `https://<your-subdomain>.<region>.deputy.com/exec/devapp/samlacs`.

    c. Fare clic su **Avanti**.

4. Se si desidera configurare l'applicazione in **SP avviata modalità** nella pagina **Configura impostazioni di App** finestra di dialogo, quindi fare clic su **"Mostra avanzate impostazioni (facoltativo)"** e quindi immettere l' **URL in accesso** e fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_05.png)

    un. Nella casella di testo **Su URL di accesso** , digitare un URL utilizzando il modello seguente: `https://<your-subdomain>.<region>.deputy.com`.

    b. Fare clic su **Avanti**.

    > [AZURE.NOTE] Vice area suffisso è opitional o deve utilizzare uno di questi piani: au | ND | dell'Unione europea | come | la | messa a fuoco automatica | un | inviato au | inviato ND | dell'Unione europea dell'organizzazione | dell'organizzazione-come | la Business | messa a fuoco automatica dell'organizzazione | un oggetto dell'organizzazione

5. Nella pagina **Configura il single sign-on in Vice** eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_06.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    
6. Passare all'URL seguente: https://(your-subdomain).deputy.com/exec/config/system_config. Passare alle **Impostazioni di protezione** e fare clic su **Modifica**.

    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_004.png)

7. Nel portale di classica Azure, nel configura il single sign-on nella pagina Vice, copiare l'URL di Single Sign-on SAML. 

8. In questa pagina di **Impostazioni di sicurezza** , eseguire sotto passaggi.

    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_005.png)

    un. Consentire **l'accesso social networking**.

    b. Aprire il certificato di base 64 codificato nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **OpenSSL certificato** .

    c. Nella casella di testo URL Single Sign-on Giorgio, digitare`https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Nella casella di testo URL Single Sign-on Giorgio, sostituire `<your subdomain>` con il sottodominio.

    e. Nella casella di testo URL Single Sign-on Giorgio, sostituire `<saml sso url>` con l'URL di Single Sign-on SAML copiato dal portale di classica Azure.

    f. Fare clic su **Salva impostazioni**.

9. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_09.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_05.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_06.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_07.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-deputy-tutorial/create_aaduser_08.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-deputy-test-user"></a>Creazione di un utente di test Vice

Per consentire agli utenti di Azure Active Directory per l'accesso a Vice, è necessario essere effettuato il provisioning in parte. In caso di Vice, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere come amministratore sito parte della società.

2.  Nel riquadro di spostamento superiore fare clic su **persone**.

    ![Persone] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_001.png "Persone")

3.  Fare clic sul pulsante **Aggiungi persone** e fare clic su **Aggiungi una sola persona**.

    ![Aggiunta di utenti] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_002.png "Aggiunta di utenti")

4.  Eseguire le operazioni seguenti e fare clic su **Salva e invitare**.

    ![I nuovi utenti] (./media/active-directory-saas-deputy-tutorial/tutorial_deputy_003.png "I nuovi utenti")

    un. Nella casella di testo **nome** digitare **Sandro** ed **Ezio**.  

    b. Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di un account Azure Active Directory che si desidera eseguire il provisioning.

    c. Nella casella di testo **in misura** , digitare il nome bussniess.

    d. Fare clic su **Salva e invitare** .

    >[AZURE.NOTE]Il titolare di account AAD riceverà un messaggio di posta elettronica e visitare un collegamento per confermare il proprio account diventa attiva. È possibile utilizzare qualsiasi altro Vice utente account strumenti di creazione o API fornite dal Vice agli account utente di provisioning AAD.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua parte.
    
![Assegnare utente][200]

**Per assegnare Britta Simon Vice, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **Vice**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-deputy-tutorial/tutorial_deputy_50.png)

3. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.
    
    ![Assegnare utente][205]

### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro Vice nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Vice.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-deputy-tutorial/tutorial_general_205.png
