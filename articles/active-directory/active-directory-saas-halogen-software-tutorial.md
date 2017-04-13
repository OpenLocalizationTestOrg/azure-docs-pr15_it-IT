<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con il Software alogeni"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e alogeno Software."
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
    ms.date="10/10/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-halogen-software"></a>Esercitazione: Integrazione di Azure Active Directory con il Software alogeni

L'obiettivo di questa esercitazione è illustrano come integrare alogeno Software con Azure Active Directory (Azure Active Directory).

Integrazione di Software alogeno con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso a Software alogeni 
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Software alogeno (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con alogeno Software, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Software di alogeno single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing. 

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Software alogeno dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-halogen-software-from-the-gallery"></a>Aggiunta di Software alogeno dalla raccolta
Per configurare l'integrazione del Software alogeno Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito alogeno Software dalla raccolta.

**Per aggiungere Software alogeno dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. 

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **software alogeno**.

    ![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **Alogeno Software**e quindi fare clic su **completa** per aggiungere l'applicazione.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con un Software per alogeno in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nel Software alogeno a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nel Software alogeno deve essere stabilito.

Assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nel Software alogeno stabilita la relazione di collegamento.
 
Per configurare e testare Azure Active Directory single sign-on con alogeno Software, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un Software di alogeno testare utente](#creating-a-halogen-software-test-user)** - necessario equivalente di Britta Simon nel Software alogeno collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurazione di Azure Active Directory Single Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on alogeno dell'applicazione in uso.


**Per configurare Azure Active Directory single sign-on con un Software alogeno, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Alogeno Software** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][8]

2. Nella pagina **specificare come gli utenti di accedere al Software alogeno** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][9]

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti:  ![configurare le impostazioni dell'App][10]
 
     un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato dagli utenti per eseguire l'accesso a un'applicazione Software di alogeno utilizzando il seguente criterio: *https://global.hgncloud.com/fabrikam/welcome.jsp*

     b. Fare clic su **Avanti**.
 
4. Nella pagina **Configura il single sign-on alogeno software** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.
    
    ![Che cos'è Azure AD Connect][11]

5. In una nuova finestra del browser, accesso a un'applicazione **Software di alogeno** come amministratore.

6. Fare clic sulla scheda **Opzioni** . 

    ![Che cos'è Azure AD Connect][12]


7. Nel riquadro di spostamento sinistra, fare clic su **Configurazione SAML**. 

    ![Che cos'è Azure AD Connect][13]

8. Nella pagina **Configurazione SAML** , eseguire le operazioni seguenti:  ![novità Azure AD Connect][14]

    un. Come **Identificatore univoco**, selezionare **NameID**.

    b. Come **Univoci mappe identificatore per**selezionare **il nome utente**.

    c. Per caricare il file di metadati scaricato, fare clic su **Sfoglia** per selezionare il file, quindi scegliere **Carica File**.

    d. Per testare la configurazione, fare clic su **Esegui Test**. 

    > [AZURE.NOTE] È necessario attendere per il messaggio "*il SAML test è stato completo. Chiudere la finestra*". Chiudere la finestra del browser aperta. La casella di controllo **Abilita SAML** è disponibile solo se il test è stato completato.

    e. Selezionare **Abilita SAML**.
    
    f. Fare clic su **Salva modifiche**. 


9. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** . 

    ![Che cos'è Azure AD Connect][15]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Che cos'è Azure AD Connect][16]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Che cos'è Azure AD Connect][100] 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Che cos'è Azure AD Connect][101] 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Che cos'è Azure AD Connect][102] 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Che cos'è Azure AD Connect][103] 
 
    un. Come **Tipo di utente**, selezionare **nuovo utente nell'organizzazione**.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su Avanti.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Che cos'è Azure AD Connect][104] 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nel **Cognome** txtbox, tipo, **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Che cos'è Azure AD Connect][105]  

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Che cos'è Azure AD Connect][106]   

    un. Prendere nota del valore della **Nuova Password**.
    b. Fare clic su **completa**.   
  
 
### <a name="creating-a-halogen-software-test-user"></a>Creazione di un utente di test alogeno Software

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon alogeno software.

**Per creare un utente chiamato Britta Simon alogeno software, procedere come segue:**

1. Accedere a un'applicazione **Software di alogeno** come amministratore.

2. Fare clic sulla scheda **Centro utenti** e quindi fare clic su **Crea utente**.

    ![Che cos'è Azure AD Connect][300]  

3. Nella pagina di finestra di dialogo **Nuovo utente** , procedere come segue:

    ![Che cos'è Azure AD Connect][301]

    un. Nella casella di testo **nome** digitare **Sandro**. 
  
    b. Nella casella di testo **Cognome** digitare **Ezio**.
  
    c. Nella casella di testo **nome utente** , digitare **nome utente Brita Ezio nel portale di classica Azure**.
  
    d. Nella casella di testo **Password** digitare una password per Sandro.
  
    e. Fare clic su **Salva**.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua alogeno Software.

![Che cos'è Azure AD Connect][200]

**Per assegnare Britta Simon a Software alogeno, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Che cos'è Azure AD Connect][201]

2. Nell'elenco delle applicazioni, selezionare **Alogeno Software**.

    ![Che cos'è Azure AD Connect][202]

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Che cos'è Azure AD Connect][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

    ![Che cos'è Azure AD Connect][204]

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Che cos'è Azure AD Connect][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro alogeno Software nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on a un'applicazione Software di alogeno.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png 
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png 
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png 
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png 
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png 
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png 
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png 
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png 
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png 
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png