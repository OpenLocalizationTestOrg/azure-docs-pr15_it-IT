<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il portale di gestione Cloud di Microsoft Azure | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e il portale di gestione Cloud di Microsoft Azure."
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
    ms.date="08/16/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Esercitazione: Integrazione di Azure Active Directory con il portale di gestione Cloud di Microsoft Azure

L'obiettivo di questa esercitazione è illustrano come integrare il portale di gestione Cloud di Microsoft Azure con Azure Active Directory (Azure Active Directory).  
Integrazione di portale di gestione Cloud di Microsoft Azure con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso al portale di gestione Cloud di Microsoft Azure
- È possibile consentire agli utenti di automaticamente ottenere firmato-on al portale di gestione Cloud di Microsoft Azure (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure


Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con il portale di gestione Cloud di Microsoft Azure, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un portale di gestione Cloud di Microsoft Azure single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di portale di gestione Cloud di Microsoft Azure dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Aggiunta di portale di gestione Cloud di Microsoft Azure dalla raccolta
Per configurare l'integrazione del portale di gestione Cloud di Microsoft Azure Active Directory Azure, è necessario aggiungere l'elenco di App SaaS gestito portale di gestione Cloud di Microsoft Azure dalla raccolta.

**Per aggiungere il portale di gestione Cloud di Microsoft Azure dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca, digitare **Il portale di gestione Cloud di Microsoft Azure**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_01.png)

7. Nel riquadro dei risultati, selezionare **Il portale di gestione Cloud di Microsoft Azure**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con il portale di gestione Cloud di Microsoft Azure in base a un utente di test denominato "Britta Simon".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nel portale di gestione Cloud di Microsoft Azure a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nel portale di gestione Cloud di Microsoft Azure deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nel portale di gestione Cloud di Microsoft Azure.

Per configurare e testare Azure Active Directory single sign-on con il portale di gestione Cloud di Microsoft Azure, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un portale di gestione Cloud di Microsoft Azure testare utente](#creating-a-newsignature-test-user)** - disporre di un equivalente di Britta Simon nel portale di gestione Cloud di Microsoft Azure collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on nel portale di gestione Cloud per applicazione di Microsoft Azure.



**Per configurare Azure Active Directory single sign-on con il portale di gestione Cloud di Microsoft Azure, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Portale di gestione Cloud di Microsoft Azure** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al portale di gestione Cloud di Microsoft Azure** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti:.

    ![Configurare Single Sign-On](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso al portale di gestione Cloud per applicazioni di Microsoft Azure utilizzando il modello seguente:`https://portal.igcm.com/<instance name>`

    b. Fare clic su **Avanti**.


4. Nella pagina **Configura il single sign-on al portale di gestione Cloud di Microsoft Azure** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5. Per ottenere SSO configurato per l'applicazione, contattare il portale di gestione Cloud per team di supporto Microsoft Azure [jczernuszka@newsignature.com](mailTo:jczernuszka@newsignature.com) e il file di certificato scaricato Allega di posta elettronica. Anche specificare l'URL di emittente, SAML Single Sign-on URL e accesso singolo all'URL del servizio in modo che possono essere configurati per l'integrazione di Single Sign-on.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-newsignature-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Creazione di un portale di gestione Cloud di Microsoft Azure test

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon nel portale di gestione Cloud di Microsoft Azure. Usare il portale di gestione Cloud per il team di supporto Microsoft Azure per aggiungere gli utenti nel portale di gestione Cloud per account di Microsoft Azure. 


> [AZURE.NOTE]Se è necessario creare manualmente un utente, è necessario contattare il portale di gestione di Cloud per il team di supporto Microsoft Azure.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo utente l'accesso al portale di gestione Cloud di Microsoft Azure.

![Assegnare utente][200] 

**Per assegnare Britta Simon al portale di gestione Cloud di Microsoft Azure, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Il portale di gestione Cloud di Microsoft Azure**.

    ![Configurare Single Sign-On](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si sceglie il portale di gestione Cloud per il riquadro di Microsoft Azure nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on al portale di gestione Cloud per applicazione di Microsoft Azure.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_205.png
