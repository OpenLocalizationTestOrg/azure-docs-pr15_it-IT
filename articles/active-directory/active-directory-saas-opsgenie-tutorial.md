<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con OpsGenie | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e OpsGenie."
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
    ms.date="10/07/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Esercitazione: Integrazione di Azure Active Directory con OpsGenie

L'obiettivo di questa esercitazione è illustrano come integrare OpsGenie con Azure Active Directory (Azure Active Directory).

Integrazione di OpsGenie con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a OpsGenie
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a OpsGenie (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con OpsGenie, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un OpsGenie single sign-on abilitato abbonamento


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing. 

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di OpsGenie dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-opsgenie-from-the-gallery"></a>Aggiunta di OpsGenie dalla raccolta
Per configurare l'integrazione di OpsGenie in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito OpsGenie dalla raccolta.

**Per aggiungere OpsGenie dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **OpsGenie**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_01.png)

7. Nel riquadro dei risultati, selezionare **OpsGenie**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con OpsGenie in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare l'utente corrispondente in OpsGenie a un utente in Azure Active Directory Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente OpsGenie deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in OpsGenie.

Per configurare e testare Azure Active Directory single sign-on con OpsGenie, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un OpsGenie test utente](#creating-a-opsgenie-test-user)** - che equivalente di Britta Simon nell'OpsGenie collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di Azure classica e configurare il single sign-on OpsGenie nell'applicazione in uso.



**Per configurare Azure Active Directory single sign-on con OpsGenie, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **OpsGenie** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al OpsGenie** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_04.png) 


    un. Nella casella di testo su URL di accesso, digitare l'URL utilizzato da agli utenti di accesso all'applicazione OpsGenie utilizzando il modello seguente: **"https://app.opsgenie.com/auth/login"**.

    > [AZURE.NOTE] Contattare il [team di supporto OpsGenie](mailto:support@opsgenie.com) se è necessario l'accesso in URL.

    b. Fare clic su **Avanti**.


4. Nella pagina **Configura il single sign-on in OpsGenie** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso. È necessario questo certificato e gli URL dei metadati (ID entità, Single Sign-on URL di accesso e accedere all'URL) per configurare Single Sign-on sul lato OpsGenie.

    b. Fare clic su **Avanti**.


5. Aprire un'altra istanza del browser e quindi log aggiuntivo per OpsGenie come amministratore.

6. Fare clic su **Impostazioni**e quindi fare clic sulla scheda **Single Sign-On** .
 
    ![OpsGenie Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_06.png) 

7. Per abilitare Single Sign-on, selezionare **attivato**.

    ![Impostazioni OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_07.png) 
   
8. Nella sezione **Provider** , fare clic sulla scheda **Azure Active Directory** .

    ![Impostazioni OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_08.png) 
    
9. Nella pagina di finestra di dialogo di Azure Active Directory, procedere come segue:
 
    ![Impostazioni OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_09.png) 

    un. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in OpsGenie** copiare il valore di **Accesso singolo su URL del servizio** e quindi incollarla nella casella di testo **SAML 2.0 Endpoint** .

    b. Creare un file codificato base 64 del certificato scaricata.      
    
    > [AZURE.NOTE] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](https://www.youtube.com/watch?v=PlgrzUZ-Y1o&feature=youtu.be).

    c. Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarla nella casella di testo **500 certificato** .

    d. Fare clic su **Salva modifiche**.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.



![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-opsgenie-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-opsgenie-test-user"></a>Creazione di un utente di test OpsGenie

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in OpsGenie. 

1.  In una finestra del browser web, accedere al tenant OpsGenie come amministratore.

2.  Passare all'elenco di utenti, fare clic su **utente** nel riquadro sinistro.
   
    ![Impostazioni OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_10.png) 

3.  Fare clic su "**Aggiungi utente**".

3.  Nella finestra di dialogo **Aggiungi utente** , procedere come segue:

    ![Impostazioni OpsGenie](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_11.png) 

    un. Nella casella di testo **messaggio di posta elettronica** digitare l'indirizzo di posta elettronica di Sandro di Azure Active Directory.

    b. Nella casella di testo **Nome completo** digitare **Sandro**.

    c. Fare clic su **Salva**. 

Sandro verrà visualizzato un messaggio di posta elettronica con le istruzioni per configurare il suo profilo.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua OpsGenie.

![Assegnare utente][200] 

**Per assegnare Britta Simon OpsGenie, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
 
    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **OpsGenie**.

    ![Configurare Single Sign-On](./media/active-directory-saas-opsgenie-tutorial/tutorial_opsgenie_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro OpsGenie nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione OpsGenie.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-opsgenie-tutorial/tutorial_general_205.png
