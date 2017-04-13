<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con 8 x 8 virtuale Office | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e 8 x 8 ufficio virtuale."
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


# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Esercitazione: Integrazione di Azure Active Directory con 8 x 8 virtuale Office

L'obiettivo di questa esercitazione è illustrano come integrare Office virtuale 8 x 8 con Azure Active Directory (Azure Active Directory).

Integrazione di 8 x 8 ufficio virtuale con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Office virtuale 8 x 8
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Office virtuale 8 x 8 (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Office virtuale 8 x 8, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un 8 x 8 ufficio virtuale single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di testare Microsoft Azure Active Directory Single Sign-On in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di 8 x 8 virtuale Office dalla raccolta
2. Configurazione e verifica Microsoft Azure Active Directory Single Sign-On


## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Aggiunta di 8 x 8 virtuale Office dalla raccolta
Per configurare l'integrazione di Office virtuale 8 x 8 Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito 8 x 8 virtuale Office dalla raccolta.

**Per aggiungere 8 x 8 virtuale Office dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.
    
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **8 x 8 virtuale Office**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_01.png)
7. Nel riquadro dei risultati, selezionare **8 x 8 virtuale Office**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Selezionare l'app nella raccolta](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_0001.png)


##  <a name="configuring-and-testing-microsoft-azure-ad-single-sign-on"></a>Configurazione e verifica Microsoft Azure Active Directory Single Sign-On
L'obiettivo di questa sezione è illustrato come configurare e testare Microsoft Azure Active Directory Single Sign-On con 8 x 8 che ufficio virtuale in base a un utente di test denominato "Britta Simon".

Per single sign-on per l'uso, è necessario indicare quali l'utente corrispondente in ufficio virtuale a un utente in Active Directory Azure è 8 x 8 Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente 8 x 8 ufficio virtuale deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Office virtuale 8 x 8.

Per configurare e testare Microsoft Azure Active Directory Single Sign-On con 8 x 8 virtuale Office, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Microsoft Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Microsoft Azure Active Directory Single Sign-On con Britta Simon.
3. **[Creazione di un utente di prova Office virtuale 8 x 8](#creating-a-8x8-virtual-office-test-user)** - che equivalente di Britta Simon nell'Office virtuale 8 x 8 collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Microsoft Azure Active Directory Single Sign-On.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-microsoft-azure-ad-single-sign-on"></a>Configurazione di Microsoft Azure Active Directory Single Sign-On

In questa sezione, abilitare Microsoft Azure Active Directory Single Sign-On nel portale di classica e configurare il single sign-on nell'applicazione di Office virtuale 8 x 8.

**Per configurare Microsoft Azure Active Directory Single Sign-On con 8 x 8 virtuale Office, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Office virtuale 8 x 8** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **da come si desidera agli utenti di accedere a Office virtuale 8 x 8** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_04.png)

    un. Nella casella di testo **URL di risposta** , digitare:`https://sso.8x8.com/saml2`

    b. Fare clic su **Avanti**

4. Nella pagina **configurare single sign-on 8 x 8 virtuale Office** , eseguire le operazioni seguenti e fare clic su **Avanti**:

    ![Configurare Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_05.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.

5. Accesso al tenant di Office virtuale 8 x 8 come amministratore.
6. Selezionare **gestione di Account Office virtuale** nel riquadro di applicazione.

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

7. Selezionare account **commerciale** per gestire e fare clic su **Accedi** .

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

8. Fare clic sulla scheda **account** nell'elenco a menu.

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

9. Fare clic su **Single Sign-On** nell'elenco degli account.

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

10. Selezionare **Signle Sign On** in metodo di autenticazione e fare clic su **SAML**.

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

11. Copiare **Single Sign-on SAML URL**, **Single Sign-all'URL del servizio** ed **emittente** Azure AD effettuare **l'accesso URL**, **eseguire la disconnessione URL** e **URL emittente** in ufficio virtuale 8 x 8. 

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    ![Configurare App lato](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_007.png)

12. Fare clic su pulsante **Browser** per caricare il certificato di cui è stato scaricato da Azure Active Directory.

13. Fare clic sul pulsante **Salva** .

14. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][10]

15. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Azure Active Directory Single Sign-On][11]



### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica chiamato Britta Simon.
    
![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_09.png)

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_03.png)

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_04.png)

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_05.png)

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_06.png)

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_07.png)

8. Nella pagina **Guida password temporanea** procedere come segue:
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-8x8virtualoffice-tutorial/create_aaduser_08.png)

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-8x8-virtual-office-test-user"></a>Creazione di un utente di prova Office virtuale 8 x 8

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon 8 x 8 virtuale Office. 8 x 8 ufficio virtuale sono supportati in fase di provisioning, che per impostazione predefinita abilitato.

Non esiste alcun elemento azione dell'utente in questa sezione. Verrà creato un nuovo utente durante il tentativo di accedere a Office virtuale 8 x 8 se non esiste ancora. 

> [AZURE.NOTE] Se è necessario creare manualmente un utente, è necessario contattare il team di supporto di Office virtuale 8 x 8.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo utente l'accesso a Office virtuale 8 x 8.
    
![Assegnare utente][200]

**Per assegnare Britta Simon a 8 x 8 virtuale Office, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201]

2. Nell'elenco delle applicazioni, selezionare **8 x 8 virtuale Office**.

    ![Configurare Single Sign-On](./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_50.png)

3. Nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Microsoft Azure Active Directory Single Sign-On configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Office virtuale 8 x 8 nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on nelle applicazioni di Office virtuale 8 x 8.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-8x8virtualoffice-tutorial/tutorial_general_205.png
