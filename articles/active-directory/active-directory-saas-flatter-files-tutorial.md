<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory file esaminarlo | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e i file esaminarlo."
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


# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Esercitazione: Integrazione di Azure Active Directory file lineare

L'obiettivo di questa esercitazione è illustrano come integrare i file esaminarlo con Azure Active Directory (Azure Active Directory).  
Integrazione di file esaminarlo con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso ai file lineare 
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a file esaminarlo (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure Active Directory

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con file lineare, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un file esaminarlo single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di file più lineare dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-flatter-files-from-the-gallery"></a>Aggiunta di file più lineare dalla raccolta
Per configurare l'integrazione dei file esaminarlo in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito esaminarlo file dalla raccolta.

**Per aggiungere più lineare file dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **File esaminarlo**.


    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_01.png)

7. Nel riquadro dei risultati, selezionare **File esaminarlo**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_500.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on file esaminarlo in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nei file larga a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nei file esaminarlo deve essere stabilito.  
Assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nei file esaminarlo stabilita la relazione di collegamento.
 
Per configurare e testare Azure Active Directory single sign-on esaminarlo file, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un file esaminarlo testare utente](#creating-a-halogen-software-test-user)** - al file sono presenti un equivalente di Britta Simon esaminarlo che è collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale classico di Azure Active Directory e configurare il single sign-on esaminarlo file nell'applicazione in uso. Come parte di questa procedura, è necessario creare un file di certificato codificato base 64. Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

Per configurare single sign-on per esaminarlo file, è necessario un dominio registrato. Se non si dispone di un dominio registrato ancora, contatto i file esaminarlo supportano team tramite [support@flatterfiles.com](mailto:support@flatterfiles.com).  



**Per configurare Azure Active Directory single sign-on con file esaminarlo, procedere come segue:**

1. Nel portale classico di Azure Active Directory, nella pagina **File esaminarlo** applicazione integrazione fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al file esaminarlo** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
 
    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_02.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_03.png) 

    > [AZURE.NOTE] File lineare utilizza lo stesso URL di accesso Single Sign-on per tutti i clienti: [https://www.flatterfiles.com/site/login/sso/](https://www.flatterfiles.com/site/login/sso/).
.
 
 
4. Nella pagina **Configura il single sign-on esaminarlo file** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_04.png)  

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


1. Accesso a un'applicazione di file più lineare come amministratore.

2. Fare clic su Dashboard. 

    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  



2. Fare clic su **Impostazioni**e quindi eseguire i passaggi seguenti nella scheda **della società** : 

    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  

    un. Selezionare **Usa SAML 2.0 per l'autenticazione**.

    b. Fare clic su **Configura SAML**.



2. Nella finestra di dialogo **Configurazione SAML** , procedere come segue: 

    ![Configurare Single Sign-On](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  

    un. Nella casella di testo dominio digitare il nome del dominio registrato.

    > [AZURE.NOTE] Se non si dispone di un dominio registrato ancora, contatto i file esaminarlo supportano team tramite [support@flatterfiles.com](mailto:support@flatterfiles.com).
    
    b. Nel Azure portale classica, in Configura single sign-on nella finestra di dialogo file esaminarlo, copt URL del servizio servizio Single Sign-On e quindi incollarla nella casella di testo URL del Provider di identità.

    c.  Creare un file **con codifica base 64** del certificato scaricata.  

    >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    d.  Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato Provider di identità FlatterFiles** .

    e. Fare clic su **Aggiorna**.

6. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_05.png)  

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_06.png) 
 
    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_08.png) 
  
    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

  
 
### <a name="creating-a-flatter-files-test-user"></a>Creazione di un utente di test file lineare

L'obiettivo di questa sezione consiste nel creare un utente denominato Britta Simon nei file esaminarlo.

**Per creare un utente chiamato Britta Simon esaminarlo dei file, procedere come segue:**

1. Accedere al sito aziendale **File esaminarlo** come amministratore.

2. Nel riquadro di spostamento a sinistra, fare clic su **Impostazioni**e quindi fare clic su utenti **scheda**.

    ![Cfreate un utente di file più lineare](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Fare clic su **Aggiungi utente**. 

4. Nella finestra di dialogo **Aggiungi utente** , procedere come segue:

    ![Cfreate un utente di file più lineare](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    un. Nella casella di testo **nome** digitare **Sandro**.

    b. Nella casella di testo **Cognome** digitare **Ezio**. 

    c. Nella casella di testo **Indirizzo di posta elettronica** digitare l'indirizzo di posta elettronica di Sandro nel portale di classica Azure.

    d. Fare clic su **Invia**.   


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure mediante la concessione di accesso ai file esaminarlo.

![Assegnare utente][200] 

**Per assegnare Britta Simon ai file più lineare, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **File esaminarlo**.

    ![Assegnare utente](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_11.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro file esaminarlo nel Pannello di accesso, dovrebbe ottenere automaticamente effettuato l'accesso-on per l'applicazione di file più lineare.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_205.png






