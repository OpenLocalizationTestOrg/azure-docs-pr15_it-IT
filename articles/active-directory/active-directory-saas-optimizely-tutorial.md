<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Optimizely | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Optimizely."
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
    ms.date="09/11/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Esercitazione: Integrazione di Azure Active Directory con Optimizely

In questa esercitazione si imparerà a integrare Optimizely con Azure Active Directory (Azure Active Directory).

Integrazione di Optimizely con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Optimizely
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Optimizely (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Optimizely, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un **Optimizely** single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Optimizely dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-optimizely-from-the-gallery"></a>Aggiunta di Optimizely dalla raccolta
Per configurare l'integrazione di Optimizely in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Optimizely dalla raccolta.

**Per aggiungere Optimizely dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Optimizely**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_01.png)

7. Nel riquadro dei risultati, selezionare **Optimizely**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con Optimizely in base a un utente di test denominato "Ezio Sandro".

Per single sign-on per l'uso, è necessario indicare sia l'utente corrispondente in Optimizely a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Optimizely deve essere stabilito.
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Optimizely.

Per configurare e testare Azure Active Directory single sign-on con Optimizely, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un Optimizely test utente](#creating-an-optimizely-test-user)** - che equivalente di Britta Simon nell'Optimizely collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on Optimizely nell'applicazione in uso.

Applicazione Optimizely prevede asserzioni SAML per contenere un attributo denominato "messaggio di posta elettronica". Il valore di "email" deve essere un messaggio di posta elettronica Optimizely riconosciuto che può essere autenticato da Azure Active Directory. Configurare la domanda "posta elettronica" di questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrributes"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. 


![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_03.png) 


**Per configurare Azure Active Directory single sign-on con Optimizely, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Optimizely** , nel menu nella parte superiore, fare clic su **attributi**.
     
    ![Configurare Single Sign-On][5]

2. Nella finestra di dialogo attributi token SAML, aggiungere l'attributo "posta elettronica".

    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi l'attributo utente** . 
    
    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_05.png)

    b. Nella casella di testo **Nome attributo** , digitare il nome dell'attributo "e-mail".

    c. Nell'elenco **Valore dell'attributo** , selezionare il valore dell'attributo "userprincipalname" o su un valore che contiene un messaggio di posta elettronica riconosciuto da Azure Active Directory e Optimizely.

    d. Fare clic su **completa**.
3. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On][6]
4. Nel portale classico, nella pagina integrazione applicazione **Optimizely** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][7] 

5. Nella pagina **specificare come gli utenti di accedere al Optimizely** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_06.png)

6. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue: 

    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)


    un. Nella casella di testo **Su URL di accesso** , digitare:`https://app.optimizely.net/contoso`

    b. Nella casella di testo **identificatore** , digitare:`urn:auth0:optimizely:contoso`

    c. Fare clic su **Avanti**. 


    > [AZURE.NOTE] I valori per **l'Accesso in URL** e **identificatore** sono solo i segnaposto per i valori effettivi. È possibile trovare istruzioni per aquiring i valori effettivi da Optimizely più avanti in questa esercitazione.

7. Nella pagina **Configura il single sign-on in Optimizely** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_08.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Copiare l' **URL del servizio Single Sign-On**.

8. Per ottenere SSO configurato per l'applicazione, contattare il responsabile di Optimizely clienti e fornire le informazioni seguenti:

    - Il certificato scaricato 
    - L'URL del servizio Single Sign-On
 
    In risposta alla posta elettronica, Optimizely consente l'accesso in URL (SP avviata SSO) e i valori degli identificatori (ID entità di Provider del servizio).

9. Tornare alla pagina di finestra di dialogo **Configura impostazioni di App** e quindi eseguire le operazioni seguenti:

    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_07.png)

    un. Nella casella di testo **Su URL di accesso** digitare l' **URL di Single Sign-on avviata SP** fornito dall'Optimizely.

    b. Nella casella di testo **identificatore** , digitare l' **ID di entità Provider di servizi** forniti da Optimizely.

    c. Fare clic su **Avanti**.

10. Nella pagina **Configura il single sign-on in Optimizely** , procedere come segue:
    
    ![Azure Active Directory Single Sign-On][10]

    un. Selezionare la conferma di configurazione del servizio single sign-on.

    b. Fare clic su **Avanti**.

11. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]

12. In una nuova finestra del browser, accesso all'applicazione Optimizely.
13. Fare clic su account nome nell'angolo superiore destro e quindi **Impostazioni Account**.

    ![Azure Active Directory Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

14. Nella scheda Account, selezionare la casella **Abilita Single Sign-on** in Single Sign-On nella sezione **Overview** .

    ![Azure Active Directory Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.
Nell'elenco di utenti, selezionare **Sandro**.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-an-optimizely-test-user"></a>Creazione di un utente di test Optimizely

In questa sezione, si crea un utente chiamato Britta Simon in Optimizely.

1. Nella home page, selezionare la scheda **collaboratori**
2. Fare clic su **Nuovo collaboratore** per aggiungere un nuovo collaboratore al progetto.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3.  Immettere l'indirizzo di posta elettronica e assegnarle un ruolo. Fare clic su **Invita**.


    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Quest'ultimo riceverà un invito di posta elettronica. Usando l'indirizzo di posta elettronica. è necessario accedere a Optimizely.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua Optimizely.

![Assegnare utente][200] 

**Per assegnare Britta Simon Optimizely, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Optimizely**.

    ![Configurare Single Sign-On](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di tutti gli utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Optimizely nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Optimizely.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-optimizely-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_205.png
