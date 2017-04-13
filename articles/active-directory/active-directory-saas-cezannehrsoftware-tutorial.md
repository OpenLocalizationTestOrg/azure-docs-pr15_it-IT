<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con un Software per HR Cezanne | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Cezanne HR Software."
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
    ms.date="10/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Esercitazione: Integrazione di Azure Active Directory Cezanne HR Software

L'obiettivo di questa esercitazione è illustrano come integrare Cezanne HR Software con Azure Active Directory (Azure Active Directory).

Integrazione Cezanne HR Software con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Cezanne HR Software
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Cezanne HR Software (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Cezanne HR Software, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Software di risorse Umane Cezanne single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Software HR Cezanne dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Aggiunta di Software HR Cezanne dalla raccolta
Per configurare l'integrazione del Software HR Cezanne Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Cezanne HR Software dalla raccolta.

**Per aggiungere Cezanne HR Software dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.
    
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Cezanne HR Software**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_01.png)

7. Nel riquadro risultati selezionare **Cezanne HR Software**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_0001.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con un Software per HR Cezanne in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nel Software HR Cezanne a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Cezanne HR Software deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** Cezanne HR software.

Per configurare e testare Azure Active Directory single sign-on con Cezanne HR Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un Software di risorse Umane Cezanne test utente](#creating-a-cezanne-hr-software-test-user)** - necessario equivalente di Britta Simon Cezanne HR software collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on Cezanne HR dell'applicazione in uso.

**Per configurare Azure Active Directory single sign-on con Cezanne HR Software, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Cezanne HR Software** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al Software HR Cezanne** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_03.png)

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_04.png)

    un. Nella casella di testo **Su URL di accesso** , digitare un URL utilizzando il modello seguente: `https://w3.cezanneondemand.com/cezannehr/-/<tenant id>`.

    b. Nella casella di testo **URL di risposta** , digitare un URL utilizzando il modello seguente: `https://w3.cezanneondemand.com:443/CezanneOnDemand/-/<tenant id>/Saml/samlp`.

    c. Fare clic su **Avanti**

    > [AZURE.NOTE] Si noti che è necessario aggiornare i valori con il segno su URL effettivo e l'URL di risposta. Per ottenere questi valori, contattare il team di supporto Cezanne HR Software tramite <mailto:info@cezannehr.com>.

4. Nella pagina **Configura il single sign-on Cezanne HR software** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_05.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.

5. In una finestra del browser web diverso, accesso al tenant di Software HR Cezanne come amministratore.

6. Nel riquadro di spostamento sinistra, fare clic su **Configurazione di sistema**. Passare alle **impostazioni di sicurezza**. Quindi passare alla **Configurazione Single Sign-On**.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

7. In **Consenti agli utenti di accedere usando il servizio Single Sign-On (SSO) seguenti** pannello selezionare la casella **SAML 2.0** e selezionare l'opzione **Configurazione avanzata** accanto a esso.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

8. Fare clic su **Aggiungi nuovo** pulsante.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

9. Nella sezione **Provider di identità SAML 2.0** , procedere come segue.

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    un. Immettere il nome del Provider di identità come il **Nome visualizzato**.

    b. **Identificatore entità** casella di testo immettere il valore **dell'ID entità** dalla configurazione guidata di Azure Active Directory applicazione.

    c. Modificare l' **associazione SAML** "Post".

    d. **Endpoint del servizio Token di sicurezza** casella di testo immettere il valore **dell'URL del servizio Single Sign-on** dalla configurazione guidata di Azure Active Directory applicazione.

    e. Immettere 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' **nome attributo ID utente**.

    f. Fare clic sull'icona **Carica** per caricare il certificato scaricato da Azure Active Directory.

    g. Fare clic sul pulsante **Ok** . 

10. Fare clic su **Salva** .

    ![Configurare lato Single Sign-On in App](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

11. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

12. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_09.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_05.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_06.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella di testo **Cognome** digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_07.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cezannehrsoftware-tutorial/create_aaduser_08.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-cezanne-hr-software-test-user"></a>Creazione di un utente di test Cezanne HR Software

Per consentire agli utenti di Azure Active Directory per l'accesso a Cezanne HR Software, è necessario essere effettuato il provisioning in Cezanne HR Software. Nel caso dei Cezanne HR Software, il provisioning è un'attività manuale.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>Per eseguire il provisioning di un account utente, procedere come segue:

1.  Accedere come amministratore sito della società Cezanne HR Software.

2.  Nel riquadro di spostamento sinistra, fare clic su **Configurazione di sistema**. Passare a **gestire gli utenti**. Individuare quindi **Aggiungi nuovo utente**.

    ![I nuovi utenti] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "I nuovi utenti")

3.  Nella sezione **Dettagli utente** eseguire sotto passaggi:

    ![I nuovi utenti] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "I nuovi utenti")

    un. **Utenti interni** come.

    b. Nella casella di testo **nome** digitare **Sandro**.  

    c. Nella casella di testo **Cognome** digitare **Ezio**.

    d. Nella casella di testo di **posta elettronica** digitare l'indirizzo di posta elettronica dell'account Britta Simon.

4.  Nella sezione **Informazioni Account** eseguire sotto passaggi:

    ![I nuovi utenti] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "I nuovi utenti")

    un. Nella casella di testo **nome utente** digitare l'indirizzo di posta elettronica di Britta Simon.

    b. Nella casella di testo **Password** digitare la password dell'account Britta Simon.

    c. Selezionare **HR Professional** come **ruolo di sicurezza**.

    d. Fare clic su **OK**.

5. Passare alla scheda **Single Sign-On** e selezionare **Aggiungi nuovo** nell'area **SAML 2.0 identificatori** .

    ![Utente] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "Utente")

6. Scegliere il Provider di identità per il **Provider di identità** e nella casella di testo **dell'ID utente**, immettere l'indirizzo di posta elettronica dell'account Britta Simon.

    ![Utente] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "Utente")
    
7. Fare clic su **Salva** .

    ![Utente] (./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "Utente")


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua Cezanne HR Software.
    
![Assegnare utente][200]

**Per assegnare Britta Simon Cezanne HR Software, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **Cezanne HR Software**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_50.png)

3. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.
    
    ![Assegnare utente][205]

### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.
 
Quando si fa clic sul riquadro Cezanne HR Software nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on a un'applicazione Software di risorse Umane Cezanne.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cezannehrsoftware-tutorial/tutorial_general_205.png
