<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con HR2day da Merces | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e HR2day da Merces."
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


# <a name="tutorial-azure-active-directory-integration-with-hr2day-by-merces"></a>Esercitazione: Integrazione di Azure Active Directory con HR2day da Merces

L'obiettivo di questa esercitazione è illustrano come integrare HR2day da Merces con Azure Active Directory (Azure Active Directory).  
Integrazione di HR2day da Merces con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a HR2day da Merces
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a HR2day da Merces (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con HR2day da Merces, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- HR2day da Merces single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di HR2day da Merces dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-hr2day-by-merces-from-the-gallery"></a>Aggiunta di HR2day da Merces dalla raccolta
Per configurare l'integrazione di HR2day da Merces in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito HR2day da Merces dalla raccolta.

**Per aggiungere HR2day da Merces dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
 
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **HR2day da Merces**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_01.png)

7. Nel riquadro dei risultati, selezionare **HR2day da Merces**e quindi fare clic su **completa** per aggiungere l'applicazione.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con HR2day da Merces in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in HR2day da Merces a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente HR2day da Merces deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in HR2day da Merces.

Per configurare e testare Azure Active Directory single sign-on con HR2day da Merces, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un HR2day da Merces test utente](#creating-a-hr2day-by-merces-test-user)** - che equivalente di Britta Simon nell'HR2day da Merces collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a HR2day da Merces con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

Il HR2day applicando Merces prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di attributi token SAML. Schermata seguente è illustrato un esempio per l'oggetto. 

![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_00.png) 

Prima di poter configurare asserzione SAML, è necessario contattare il team di supporto HR2day tramite [servicedesk@merces.nl](mailto:servicedesk@merces.nl) e richiedere il valore dell'attributo identificatore univoco per il tenant. È necessario questo valore per completare la procedura descritta nella sezione.


**Per configurare Azure Active Directory single sign-on con HR2day da Merces, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **HR2day da Merces** , nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** . 

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_06.png) 

2. Per aggiungere i mapping attributo obbligatorio, procedere come segue, eseguire le operazioni seguenti: 

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_07.png) 


    un. Fare clic su **Aggiungi attributo utente**.

    b. Nella casella di testo **Nome attributo** , digitare **"ATTR_LOGINCLAIM"**.

    c. Nell'elenco **Valore dell'attributo** selezionare **join ()**. 

    d. Nell'elenco **stringa1** , selezionare **User.mail**. 

    e. Nella casella di testo **stringa2** , digitare l' **identificatore univoco** fornite dal team di HR2day. 

    f. Nella casella di testo **separatore** , digitare **@**.

    g. Fare clic su **completa**.

  
3. Fare clic su **Applica modifiche**.


1. Nel menu nella parte superiore, fare clic su **Guida introduttiva** per aprire la finestra di **Avvio rapido** .

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_general_08.png) 



1. Fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al HR2day da Merces** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue: 

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_04.png) 


    un. Nella casella di testo su URL di accesso, digitare l'URL utilizzato dagli utenti al sign-on per il HR2day dall'applicazione Merces utilizzando il modello seguente: **"https://\<nome tenant\>.force.com/\<nome dell'istanza\>"**.

    b. Fare clic su **Avanti**.

4. Nella pagina **Configura il single sign-on in HR2day da Merces** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5. Per ottenere SSO configurato per l'applicazione, contattare il HR2day dal team di supporto Merces tramite [servicedesk@merces.nl](emailTo:servicedesk@merces.nl) e allegare il file di certificato scaricata la posta elettronica. Anche fornire le SAML Single Sign-on URL, l'accesso all'URL e l'URL emittente in modo che possono essere configurati per l'integrazione di Single Sign-on.


> [AZURE.NOTE] Menzionare, al team Merces che questa integrazione necessario ID entità sia impostato su questo modello **https://hr2day.force.com/INSTANCENAME**



6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hr2day-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-hr2day-by-merces-test-user"></a>Creazione di un HR2day da Merces test utente

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in HR2day da Merces. Lavorare con HR2day dal team di supporto Merces per aggiungere gli utenti nella finestra account HR2day. 


> [AZURE.NOTE]Se è necessario creare manualmente un utente, è necessario contattare il HR2day dal team di supporto Merces.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua HR2day da Merces.

![Assegnare utente][200] 

**Per assegnare Britta Simon HR2day da Merces, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **HR2day da Merces**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hr2day-tutorial/tutorial_hr2day_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si sceglie la HR2day dal riquadro Merces nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on per il HR2day applicando Merces.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hr2day-tutorial/tutorial_general_205.png
