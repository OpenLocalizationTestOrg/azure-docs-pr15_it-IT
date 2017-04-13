<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con O.C. Sarà Tonon - AppreciateHub | Microsoft Azure"
    description="Informazioni su come configurare il single sign-on tra Azure Active Directory e O.C. Sarà Tonon - AppreciateHub."
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


# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>Esercitazione: Integrazione di Azure Active Directory con O.C. Sarà Tonon - AppreciateHub

L'obiettivo di questa esercitazione è illustrato come integrare O.C. Sarà Tonon - AppreciateHub con Azure Active Directory (Azure AD).  
Integrazione O.C. Sarà Tonon - AppreciateHub con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso a O.C. Sarà Tonon - AppreciateHub 
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a O.C. Sarà Tonon - AppreciateHub (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con O.C. Sarà Tonon - AppreciateHub, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- UN O.C. Sarà Tonon - AppreciateHub single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Lo scenario illustrato in questa esercitazione è costituito da tre blocchi predefiniti principali:

1. Aggiunta di O.C. Sarà Tonon - AppreciateHub dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>Aggiunta di O.C. Sarà Tonon - AppreciateHub dalla raccolta
Per configurare l'integrazione di O.C. Sarà Tonon - AppreciateHub in Azure Active Directory, è necessario aggiungere O.C. Sarà Tonon - AppreciateHub dalla raccolta per l'elenco di App SaaS gestito.

**Per aggiungere sarà Tonon O.C. - AppreciateHub dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1] 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2] 

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3] 

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4] 

6. Nella casella Cerca digitare **Sarà Tonon O.C. - AppreciateHub**.

    ![Applicazioni][5] 

7. Nel riquadro dei risultati selezionare **Sarà Tonon O.C. - AppreciateHub**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Applicazioni][25] 




##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on

L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con O.C. Sarà Tonon - AppreciateHub in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario sapere quali l'utente corrispondente in O.C. Azure Active Directory Sarà Tonon - AppreciateHub a un utente in Active Directory Azure è. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente O.C. Sarà Tonon - AppreciateHub deve essere stabilito.  
Stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in O.C. Sarà Tonon - AppreciateHub.
 
Per configurare e testare Azure Active Directory single sign-on con O.C. Sarà Tonon - AppreciateHub, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un sarà Tonon O.C. - AppreciateHub test utente](#creating-a-halogen-software-test-user)** - che equivalente di Britta Simon nell'O.C. Sarà Tonon - AppreciateHub collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e per configurare il single sign-on il O.C. Sarà Tonon - AppreciateHub applicazione.


**Per configurare Azure Active Directory single sign-on con sarà Tonon O.C. - AppreciateHub, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Sarà Tonon O.C. - AppreciateHub** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6]

2. Nella pagina **specificare come gli utenti di accedere al sarà Tonon O.C. - AppreciateHub** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][7]

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare le impostazioni dell'App][8]
 
     un. Aprire il file di metadati tramite il collegamento seguente: [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata).

     b. Individuare il nodo **md:AssertionConsumerService** . 

     c. Copiare il valore dell'attributo **posizione** . 

     ![Configurare le impostazioni dell'App][12]
     
     d. Nella casella di testo **URL in accesso** superato il valore è ottenuto nel passaggio precedente.

     > [AZURE.NOTE] Nel caso di problemi di expiriencing ottenere l'URL di risposta dal file di metadati, contattare il O.C. Sarà Tonon - team di supporto AppreciateHub tramite [sso@octanner.com](mailto:sso@octanner.com).

     e. Fare clic su **Avanti**.
 
4. Nella pagina **Configura il single sign-on in sarà Tonon O.C. - AppreciateHub** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Che cos'è Azure AD Connect][9]

5. Contattare il O.C. Sarà Tonon - team di supporto AppreciateHub tramite xyz, fornire il file di metadati e li informarlo che consentono Single Sign-on dell'utente.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Che cos'è Azure AD Connect][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Che cos'è Azure AD Connect][11]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png) 
  
    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

  
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>Creazione di un O.C. Sarà Tonon - AppreciateHub test utente

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in O.C. Sarà Tonon - AppreciateHub.

**Per creare un utente chiamato Britta Simon in sarà Tonon O.C. - AppreciateHub, procedere come segue:**

1. Chiedere al team di supporto sarà Tonon OC per creare un utente con conflitto come nameID attributo lo stesso valore di nome utente del Britta Simon in Azure Active Directory.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione Britta Simon usare il single sign-on Azure concedendo la sua O.C. Sarà Tonon - AppreciateHub.

![Assegnare utente][200]

**Per assegnare Britta Simon sarà Tonon O.C. - AppreciateHub, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **Sarà Tonon O.C. - AppreciateHub**.

    ![Assegnare utente][202]

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic il O.C. Sarà Tonon - riquadro AppreciateHub nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on per il O.C. Sarà Tonon - AppreciateHub applicazione.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png






