<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Hightail | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Hightail."
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


# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Esercitazione: Integrazione di Azure Active Directory con Hightail

L'obiettivo di questa esercitazione è illustrano come integrare Hightail con Azure Active Directory (Azure Active Directory).

Integrazione di Hightail con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Hightail
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Hightail (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Hightail, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Hightail single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing. 

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Hightail dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-hightail-from-the-gallery"></a>Aggiunta di Hightail dalla raccolta
Per configurare l'integrazione di Hightail in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Hightail dalla raccolta.

**Per aggiungere Hightail dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 
 
    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Hightail**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_01.png)

7. Nel riquadro dei risultati, selezionare **Hightail**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con Hightail in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in Hightail a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Hightail deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Hightail.

Per configurare e testare Azure Active Directory single sign-on con Hightail, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un Hightail test utente](#creating-a-hightail-test-user)** - che equivalente di Britta Simon nell'Hightail collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on Hightail nell'applicazione in uso.

Hightail asserzioni SAML prevista dall'applicazione in un formato specifico. Configurare le attestazioni seguenti per questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrribute"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto. 

![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_51.png) 

**Per configurare Azure Active Directory single sign-on con Hightail, procedere come segue:**


1. Nel portale di classica Azure, nella pagina integrazione applicazione **Hightail** , nel menu nella parte superiore, fare clic su **attributi**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_general_81.png) 


1. Nella finestra di dialogo **attributi token SAML** per ogni riga nella tabella riportata di seguito, procedere come segue:

  	| Nome attributo | Valore dell'attributo |
  	| --- | --- |    
  	| Nome | User.givenName |
  	| Cognome  | User.surname |
  	| Posta elettronica | User.Mail |
  	| Identità utente | User.Mail |

    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi utente Attribure** .

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_general_82.png) 


    b. Nella casella di testo **Nome attributo** , digitare il nome attributo visualizzato per tale riga.

    c. Nell'elenco **Valore dell'attributo** selsect il valore dell'attributo visualizzato per tale riga.

    d. Fare clic su **completa**.  
    



1. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_general_83.png)  



2. Nella pagina **specificare come gli utenti di accedere al Hightail** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_03.png) 


3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , se si desidera configurare l'applicazione in **IDP avviata modalità**, eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_04.png) 



    un. Nella casella di testo **URL di risposta** , digitare l'URL nel modello di esempio: **"https://www.hightail.com/samlLogin?phi_action=app/samlLogin & sottoazione = handleSamlResponse"**

    b. Fare clic su **Avanti**

4. Se si desidera configurare l'applicazione in **SP avviata modalità** nella pagina **Configura impostazioni di App** finestra di dialogo, quindi fare clic su **"Mostra avanzate impostazioni (facoltativo)"** e quindi immettere l' **URL in accesso** e fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_06.png) 

    un. Nella casella di testo su URL di accesso, digitare l'URL utilizzato da agli utenti di accesso all'applicazione Hightail utilizzando il modello seguente: **https://www.hightail.com/loginSSO**. Questa è la pagina di accesso comune per tutti i clienti che desiderano utilizzare Single Sign-on.

    b. Fare clic su **Avanti**

5. Nella pagina **Configura il single sign-on in Hightail** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_05.png) 


    un. Fare clic su **Scarica certificato**e quindi salvare il file di certificato codificato base 64 nel computer in uso.

    b. Fare clic su **Avanti**.

    > [AZURE.NOTE] Prima di configurare il Single Sign-On in Hightail app, vedere elenco bianco del dominio di posta elettronica con Hightail del team in modo che tutti gli utenti che usano tale dominio possono sfruttare le funzionalità di Single Sign-On.

6. Per ottenere SSO configurato per l'applicazione, è necessario sign-on per il tenant Hightail come amministratore.

    un. Nel menu nella parte superiore, fare clic sulla scheda **Account** e selezionare **Configura SAML**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_001.png) 


    b. Selezionare la casella di controllo di **Abilitare autenticazione SAML**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_002.png) 

    c. Aprire il certificato codificato base 64 nel blocco note, copiare il contenuto che ne negli Appunti e lo si incolla alla casella di testo **SAML Token certificato di firma** .

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_003.png) 


    d. Copia Hightail URL di accesso remoto da Azure Active Directory all' **autorità SAML (Provider di identità)** .

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_005.png)
    
    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_004.png)

    e. Se si desidera configurare l'applicazione in **IDP avviata modalità** selezionare **"Provider di identità (IdP) avviata log in"**. Se **SP avviata modalità** selezionare **"Provider di servizi (SP) avviata log in"**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_006.png)

    f. Copiare l'URL di consumer SAML per l'istanza e incollarla nella casella di testo **URL di risposta** , come illustrato nel passaggio 4. 

    g. Fare clic su **Salva**.



6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**. 
 
    ![Azure Active Directory Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

Nell'elenco di utenti, selezionare **Sandro**.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_03.png) 


4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_05.png) 

    un. Come **Tipo di utente**, selezionare **nuovo utente nell'organizzazione**.

    b. Nella casella di testo **Nome utente** digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_07.png) 


8. Nella pagina **Guida password temporanea** procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-hightail-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-hightail-test-user"></a>Creazione di un utente di test Hightail

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Hightail. 

Non esiste alcun elemento azione dell'utente in questa sezione. Hightail supporta il provisioning dell'utente nel tempo in base a reclami personalizzati. Se sono state configurate le attestazioni personalizzate come illustrato nella sezione **[Configurazione Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** sopra, verrà creato automaticamente un utente dell'applicazione che non esiste ancora. 

> [AZURE.NOTE] Se è necessario creare manualmente un utente, è necessario contattare il team di supporto Hightail tramite [support@hightail.com](mailto:support@hightail.com).


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua Hightail.

![Assegnare utente][200] 

**Per assegnare Britta Simon Hightail, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
 
    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Hightail**.

    ![Configurare Single Sign-On](./media/active-directory-saas-hightail-tutorial/tutorial_hightail_50.png) 


1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Hightail nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Hightail.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-hightail-tutorial/tutorial_general_205.png
