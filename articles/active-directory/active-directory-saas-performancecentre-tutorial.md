<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con PerformanceCentre | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e PerformanceCentre."
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
    ms.date="09/26/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Esercitazione: Integrazione di Azure Active Directory con PerformanceCentre

L'obiettivo di questa esercitazione è illustrano come integrare PerformanceCentre con Azure Active Directory (Azure Active Directory).  
Integrazione di PerformanceCentre con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso a PerformanceCentre 
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a PerformanceCentre (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure Active Directory

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con PerformanceCentre, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un PerformanceCentre single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Lo scenario illustrato in questa esercitazione è costituito da tre blocchi predefiniti principali:

1. Aggiunta di PerformanceCentre dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-performancecentre-from-the-gallery"></a>Aggiunta di PerformanceCentre dalla raccolta
Per configurare l'integrazione di PerformanceCentre in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito PerformanceCentre dalla raccolta.

**Per aggiungere PerformanceCentre dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **PerformanceCentre**.
 
    ![Applicazioni][5]

7. Nel riquadro dei risultati, selezionare **PerformanceCentre**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Applicazioni][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con PerformanceCentre in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in PerformanceCentre a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente PerformanceCentre deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in PerformanceCentre.
 
Per configurare e testare Azure Active Directory single sign-on con PerformanceCentre, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un PerformanceCentre test utente](#creating-a-halogen-software-test-user)** - che equivalente di Britta Simon nel PerformanceCentre collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale classico di Azure Active Directory e configurare il single sign-on PerformanceCentre nell'applicazione in uso.

**Per configurare Azure Active Directory single sign-on con PerformanceCentre, procedere come segue:**

1. Nel portale classico di Azure Active Directory, nella pagina integrazione applicazione **PerformanceCentre** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al PerformanceCentre** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][7] 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Azure Active Directory Single Sign-On][8] 
 
     un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso al sito PerformanceCentre (ad esempio: *http://companyname.performancecentre.com/saml/SSO*).
 
     b. Fare clic su **Avanti**.
 
4. Nella pagina **Configura il single sign-on in PerformanceCentre** , procedere come segue:

    ![Azure Active Directory Single Sign-On][9] 

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.



1. Accesso al sito della società **PerformanceCentre** come amministratore.

2. Nella scheda sul lato sinistro fare clic su **Configura**.

    ![Azure Active Directory Single Sign-On][10]

2. Nella scheda sul lato sinistro, fare clic su **varie**e quindi fare clic su **Single Sign-On**.

    ![Azure Active Directory Single Sign-On][11]

2. Come **protocollo**, selezionare **SAML**.

    ![Azure Active Directory Single Sign-On][12]

2. Aprire il file di metadati scaricati in blocco note, copiare il contenuto, incollarlo nella casella di testo **Metadati del Provider di identità** e quindi fare clic su **Salva**.

    ![Azure Active Directory Single Sign-On][13]

2. Verificare che i valori per **l'URL della Base entità** e **ID entità** siano corretti.

    ![Azure Active Directory Single Sign-On][14]


6. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Azure Active Directory Single Sign-On][15]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][16]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_09.png)  

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_05.png)  

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_06.png) 
 
    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-performancecentre-tutorial/create_aaduser_08.png) 
  
    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

  
 
### <a name="creating-a-performancecentre-test-user"></a>Creazione di un utente di test PerformanceCentre

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in PerformanceCentre.

**Per creare un utente chiamato Britta Simon in PerformanceCentre, procedere come segue:**

1. Accedere al sito aziendale PerformanceCentre come amministratore.

2. Nel menu a sinistra, fare clic su **Interrelate**e quindi fare clic su **Crea partecipante**.

    ![Creare utente][400]

4. Nella finestra di dialogo **Interrelate - creare partecipante** , procedere come segue:

    ![Creare utente][401]

    un. Digitare gli attributi necessari per Britta Simon nelle caselle di testo correlati.
    
    > [AZURE.IMPORTANT] Attributo di nome utente di Sandro in PerformanceCentre deve essere lo stesso nome utente in Active Directory Azure.


    b. Selezionare **Amministratore dei Client** come **scegliere ruolo**. 

    c. Fare clic su **Salva**.   


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua PerformanceCentre.

![Assegnare utente][200] 

**Per assegnare Britta Simon PerformanceCentre, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **PerformanceCentre**.

    ![Assegnare utente][202]

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro PerformanceCentre nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione PerformanceCentre.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_01.png
[500]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_02.png

[6]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_03.png
[8]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_04.png
[9]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_05.png
[10]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_10.png
[15]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_06.png
[16]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_07.png


[20]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_50.png
[203]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_12.png
[402]: ./media/active-directory-saas-performancecentre-tutorial/tutorial_performancecentre_402.png



