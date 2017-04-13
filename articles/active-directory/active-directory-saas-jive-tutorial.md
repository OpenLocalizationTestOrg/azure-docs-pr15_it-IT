<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Jive | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Jive."
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-jive"></a>Esercitazione: Integrazione di Azure Active Directory con Jive

In questa esercitazione si imparerà a integrare Jive con Azure Active Directory (Azure Active Directory).

Integrazione di Jive con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Jive
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Jive (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Jive, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Jive single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Jive dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-jive-from-the-gallery"></a>Aggiunta di Jive dalla raccolta
Per configurare l'integrazione di Jive in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Jive dalla raccolta.

**Per aggiungere Jive dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]
2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Jive**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. Nel riquadro dei risultati, selezionare **Jive**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con Jive in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare sia l'utente corrispondente in Jive a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Jive deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Jive.

Per configurare e testare Azure Active Directory single sign-on con Jive, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un Jive test utente](#creating-a-jive-test-user)** - che equivalente di Britta Simon nel Jive collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Configurare il provisioning dell'utente](#configuring-user-provisioning)** - struttura come abilitare il provisioning dell'utente dell'utente Active Directory conti a Jive.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
6. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on Jive nell'applicazione in uso.

**Per configurare Azure Active Directory single sign-on con Jive, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Jive** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **da come si desidera agli utenti di accedere al Jive** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione Jive utilizzando il modello seguente: **https://\<nome cliente\>. jivecustom.com**.
    
    b. Fare clic su **Avanti**
 
4. Nella pagina **Configura il single sign-on in Jive** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5. Accesso al tenant di Jive come amministratore.

6. Nel menu nella parte superiore, fare clic su "**Saml**".

    ![Configurare Single Sign-On sul lato App](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

    un. Selezionare la scheda **Genaral** **abilitata** .

    b. Fare clic sul pulsante "**Salva tutte le impostazioni di saml**".

7. Passare alla scheda "**Idp metadati**".

    ![Configurare Single Sign-On sul lato App](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

    un. Copiare il contenuto del file XML di metadati scaricati e quindi incollarla nella casella di testo **dei metadati del Provider di identità (IDP)** .

    b. Fare clic sul pulsante "**Salva tutte le impostazioni di saml**". 

8. Passare alla scheda "**Mapping attributo utente**".

    ![Configurare Single Sign-On sul lato App](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

    un. Nella casella di testo **messaggio di posta elettronica** , copiare e incollare il nome dell'attributo del valore di **posta elettronica** .

    b. Nella casella di testo **nome** , copiare e incollare il nome dell'attributo del valore **givenname** .

    c. Nella casella di testo **Cognome** , copiare e incollare il nome dell'attributo del valore **Cognome** .
    
9. Nel portale di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
![Azure Active Directory Single Sign-On][10]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
  ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , eseguire le operazioni seguenti:  ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , eseguire le operazioni seguenti: ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



###<a name="creating-a-jive-test-user"></a>Creazione di un utente di test Jive

In questa sezione, si crea un utente chiamato Britta Simon in Jive. Lavorare con il team di supporto Jive per aggiungere gli utenti della piattaforma Jive.


###<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per Jive dell'utente.  
Come parte di questa procedura, verrà chiesto di fornire un token di sicurezza utente che è necessario richiedere a Jive.com.
  
Schermata seguente è illustrato un esempio della finestra di dialogo correlato in Azure Active Directory:

![Configurare il Provisioning dell'utente] (./media/active-directory-saas-jive-tutorial/IC698794.png "Configurare il Provisioning dell'utente")

####<a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1.  Nel portale di gestione di Azure, nella pagina integrazione applicazione **Jive** , fare clic su **Configura il provisioning dell'utente** per aprire la finestra di dialogo **Configura il Provisioning dell'utente** .

2.  Nella pagina **Immettere le credenziali Jive per abilitare il provisioning automatico degli utenti** , specificare le impostazioni di configurazione seguenti:

    1.  Nella casella di testo **Nome utente amministratore Jive** , digitare un nome di account Jive con il profilo di **Amministratore di sistema** in Jive.com assegnate.

    2.  Nella casella di testo **Password dell'amministratore Jive** , digitare la password per l'account.

    3.  Nella casella di testo **Jive Tenant URL** digitare l'URL di tenant Jive.

        >[AZURE.NOTE]L'URL di tenant Jive è utilizzato dall'organizzazione di accedere a Jive.  
        In genere, l'URL è nel formato seguente: **www.\< organizzazione\>. jive.com**.

    4.  Fare clic su **convalida** per verificare la configurazione.

    5.  Fare clic sul pulsante **Avanti** per aprire la pagina di **Conferma** .

3.  Nella pagina di **Conferma** , fare clic sul segno di spunta per salvare la configurazione.
  
È ora possibile creare un account di prova, attendere 10 minuti e verificare che l'account è stata sincronizzata con Jive.com.




### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua Jive.

![Assegnare utente][200] 

**Per assegnare Britta Simon Jive, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Jive**.

    ![Configurare Single Sign-On](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test Single Sign-On

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Jive nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Jive.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png
