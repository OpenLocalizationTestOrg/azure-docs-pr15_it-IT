<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con ADP eTime | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory ed eTime ADP."
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
    ms.date="10/17/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-adp-etime"></a>Esercitazione: Integrazione di Azure Active Directory con ADP eTime

L'obiettivo di questa esercitazione è illustrano come integrare ADP eTime con Azure Active Directory (Azure Active Directory).  
Integrazione ADP eTime con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a eTime ADP
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a eTime ADP (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure


Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con eTime ADP, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un progetto ADP eTime single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di eTime ADP dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-adp-etime-from-the-gallery"></a>Aggiunta di eTime ADP dalla raccolta
Per configurare l'integrazione di eTime ADP in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito ADP eTime dalla raccolta.

**Per aggiungere ADP eTime dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **eTime ADP**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_01.png)

7. Nel riquadro dei risultati selezionare **ADP eTime**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_06.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con eTime ADP in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in eTime ADP a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente ADP eTime deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come valore di **nome utente** nella eTime ADP.

Per configurare e testare Azure Active Directory single sign-on con eTime ADP, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un eTime ADP testare utente](#creating-a-adpetime-test-user)** - disporre di un equivalente di Britta Simon in eTime ADP collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on ADP eTime nell'applicazione in uso.

L'applicazione di eTime ADP prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di attributi token SAML. Schermata seguente è illustrato un esempio per l'oggetto. Nome attestazione viene mantenuto **"PersonImmutableID"** e il valore di cui è stato mappato a ExtensionAttribute2 che contiene il campo ID dipendente dell'utente. Di seguito non verrà eseguito il primo mapping utente Azure Active Directory per eTime ADP per il campo ID dipendente, ma è possibile mappare questo a un valore diverso anche in base alle impostazioni applicazione. Fare in modo lavoro con team eTime ADP prima di utilizzare l'identificatore corretto di un utente e mappare tale valore con la richiesta di **"PersonImmutableID"** .  

![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_02.png) 

È possibile configurare l'asserzione SAML, è necessario contattare il team di supporto eTime ADP e richiedere il valore dell'attributo identificatore univoco per il tenant. È necessario questo valore per configurare attestazione personalizzata per l'applicazione.


**Per configurare Azure Active Directory single sign-on con eTime ADP, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **ADP eTime** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al progetto ADP eTime** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti:.

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_04.png) 


    un. Nella casella di testo **URL di risposta** , digitare l'URL utilizzato dagli utenti per accesso all'applicazione di eTime ADP utilizzando il modello seguente: `https://<server name>.adp.com/affwebservices/public/saml2assertionconsumer`.

    b. Fare clic su **Avanti**.

4. Nella pagina **Configura il single sign-on in eTime ADP** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_05.png) 

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5. Per utilizzare Single Sign-on configurato per l'applicazione, contattare il team di supporto eTime ADP e inviare tramite posta elettronica di Connetti scaricati file di metadati, in modo che possono essere configurati per l'integrazione di Single Sign-on.

    > [AZURE.NOTE] Dopo il team di **progetto ADP eTime** configurato l'istanza, ottenere il valore di **RelayState** da essi. Seguire il sotto indicati i passaggi per configurarlo. Dopo la configurazione è possibile verificare l'integrazione. Pertanto si nota che si tratta di configurazione importanti per l'integrazione di applicazioni per l'uso.

6. Per configurare il valore di RelayState in Azure Active Directory, procedere come segue: 
    
    un. Accesso al [Portale di gestione Azure](https://portal.azure.com) come amministratore.

    b. Nel riquadro di spostamento sinistra, fare clic su **Altri servizi**. 
    
    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_07.png)

    c. Nella casella di testo **Cerca** digitare **Azure Active Directory**e quindi fare clic sul collegamento correlato.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_08.png)

    d. Fare clic su **applicazioni aziendali**.

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_09.png)

    e. Nella sezione **Gestisci** , fare clic su **Tutte le applicazioni**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_10.png)

    f. Nella casella di testo **Cerca** digitare **ADP eTime**e quindi fare clic sul collegamento correlato. 
    
    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_11.png)

    g. Nella sezione **Gestisci** , fare clic su **servizio Single sign-on**.

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_12.png)

    h. Selezionare **Mostra avanzate impostazioni URL**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_13.png)
    
    si. Nella casella di testo **Dello stato di inoltro** , digitare un valore utilizzando i motivi seguenti:
    
    - Ambiente di produzione:`https://fed.adp.com/saml/fedlanding.html?<id>` 
    - Ambiente di gestione temporanea:`https://fed-stag.adp.com/saml/fedlanding.html?PORTAL`

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_14.png)

    j. Salvare le impostazioni.

7. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

8. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  
Nell'elenco di utenti, selezionare **Sandro**.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_05.png) 

    un. Come **Tipo di utente**, selezionare **nuovo utente nell'organizzazione**.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-adpetime-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-adp-etime-test-user"></a>Creazione di un utente di test eTime ADP

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in eTime ADP. Lavorare con team di supporto eTime ADP per aggiungere gli utenti nella finestra account eTime ADP. 


> [AZURE.NOTE]Se è necessario creare manualmente un utente, è necessario contattare il team di supporto eTime ADP.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo l'accesso a eTime ADP.

![Assegnare utente][200] 

**Per assegnare Britta Simon eTime ADP, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **eTime ADP**.

    ![Configurare Single Sign-On](./media/active-directory-saas-adpetime-tutorial/tutorial_adpetime_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro eTime ADP nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione eTime ADP.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-adpetime-tutorial/tutorial_general_205.png
