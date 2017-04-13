<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con HackerOne | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e HackerOne."
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
    ms.date="09/29/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Esercitazione: Integrazione di Azure Active Directory con HackerOne

In questa esercitazione si integrare HackerOne con Azure Active Directory (Azure Active Directory).

Integrazione di HackerOne con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a HackerOne
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a HackerOne (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con HackerOne, è necessario quanto segue:

- Una sottoscrizione di Azure
- Un HackerOne single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione, configurare e testare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di HackerOne dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-hackerone-from-the-gallery"></a>Aggiunta di HackerOne dalla raccolta
Per integrare HackerOne in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito HackerOne dalla raccolta.

**Per aggiungere HackerOne dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

![Applicazioni][4]

6. Nella casella Cerca digitare **HackerOne**.

![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_01.png)

7. Nel riquadro dei risultati, selezionare **HackerOne**e quindi fare clic su **completa** per aggiungere l'applicazione.


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
Successivamente, configurare e testare Azure Active Directory single sign-on con HackerOne in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in HackerOne a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente HackerOne deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in HackerOne.

Per configurare e testare Azure Active Directory single sign-on con HackerOne, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un HackerOne test utente](#creating-a-hackerone-test-user)** - che equivalente di Britta Simon nel certificare collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

Successivamente, si attiva Azure Active Directory il single sign-on nel portale di classica e configurare il single sign-on HackerOne nell'applicazione in uso.

Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

**Per configurare Azure Active Directory single sign-on con HackerOne, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **HackerOne** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al HackerOne** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_03.png) 

3. Nella pagina finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_04.png) 


    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione HackerOne utilizzando il modello seguente: **"https://hackerone.com/\<il nome della società\>/authentication"**. 

    b. Contattare il team di supporto HackerOne tramite [support@hackerone.com](mailto:support@hackerone.com) per ottenere l'URL tenant se non si conosce.

    c. Nella casella di testo **identificatore** , digitare l'URL del tenant. 

    d. Fare clic su **Avanti**.


4. Nella pagina **Configura il single sign-on in HackerOne** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_05.png) 

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


1. Accesso al tenant di HackerOne come amministratore.

1. Nel menu nella parte superiore, fare clic su **Impostazioni**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_001.png) 

1. Passare a "**autenticazione**" e fare clic su "**aggiungere SAML impostazioni**".

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_003.png) 


1. Nella finestra di dialogo **Impostazioni SAML** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_004.png) 

    un. Nella casella di testo **Dominio di posta elettronica** , digitare un dominio registrato.

    b. Nel portale di classica Azure, copiare l' **URL del servizio Single Sign-On**e quindi incollarla nella casella di testo singola accesso su URL.

    c. Creare un file **con codifica base 64** del certificato scaricata.  

       >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)
    
    d. Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto di esso e quindi incollarlo nel **X509 certificato** casella di testo.

    e. Fare clic su **Salva**


1. Nella finestra di dialogo Impostazioni di autenticazione, procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_005.png) 

    un. Fare clic su **Esegui test**.

    b. Se il valore di **stato** del campo è uguale al **ultimo test stato: creato**, contattare il team di supporto HackerOne tramite [support@hackerone.com](mailto:support@hackerone.com) per richiedere un'analisi della configurazione.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory

Creare quindi un utente di prova nel portale di classica chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova sicura RECAPITARE in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hackerone-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   


### <a name="creating-a-hackerone-test-user"></a>Creazione di un utente di test HackerOne

Successivamente, si crea un utente chiamato Britta Simon in HackerOne. HackerOne supporta il provisioning in entrata, che è attivata per impostazione predefinita.

Non esiste alcun elemento azione dell'utente in questa sezione. Quando si accede a HackerOne, se non esiste ancora viene creato un nuovo utente. [Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on).

> [AZURE.NOTE] Se è necessario creare manualmente un utente, è necessario contattare il team di supporto certificare.




### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

Successivamente, si abilita Britta Simon usare il single sign-on Azure concedendo la sua HackerOne.

![Assegnare utente][200] 

**Per assegnare Britta Simon HackerOne, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **HackerOne**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hackerone-tutorial/tutorial_hackerone_50.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

Infine, si verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro HackerOne nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione HackerOne.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hackerone-tutorial/tutorial_general_205.png