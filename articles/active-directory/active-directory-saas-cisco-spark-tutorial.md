<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con i Cisco | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e i Cisco."
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
    ms.date="10/20/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Esercitazione: Integrazione di Azure Active Directory con i Cisco

In questa esercitazione si imparerà a integrare i Cisco con Azure Active Directory (Azure Active Directory).

Integrazione di motori Cisco con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Cisco motori
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Cisco motori (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con i Cisco, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un **Ad Cisco** single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing. Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di motori Cisco dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-cisco-spark-from-the-gallery"></a>Aggiunta di motori Cisco dalla raccolta
Per configurare l'integrazione di motori Cisco in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito ad Cisco dalla raccolta.

**Per aggiungere i Cisco dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella di ricerca, digitare **I Cisco**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_01.png)

7. Nel riquadro dei risultati, selezionare **I Cisco**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con i Cisco in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente in Cisco motori a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Cisco motori deve essere stabilito.
Assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in ad Cisco stabilita la relazione di collegamento. Per configurare e testare Azure Active Directory single sign-on con i Cisco, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un ad Cisco testare utente](#creating-a-cisco-spark-test-user)** - che equivalente di Britta Simon nel motori Cisco collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on Cisco motori nell'applicazione in uso.

Applicazione di motori Cisco prevede asserzioni SAML contenga attributi specifici. Configurare gli attributi seguenti per questa applicazione. È possibile gestire i valori degli attributi dalla scheda **"Atrributes"** dell'applicazione. Schermata seguente è illustrato un esempio per l'oggetto.

![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_03.png) 

**Per configurare Azure Active Directory single sign-on con i Cisco, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Cisco motori** , nel menu nella parte superiore, fare clic su **attributi**.
     
    ![Configurare Single Sign-On][5]


2. Nella finestra di dialogo **attributi token SAML** , procedere come segue:

    un. Fare clic su **Aggiungi attributo utente** per aprire la finestra di dialogo **Aggiungi utente Attribure** .

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_05.png)
    
    b. Nella casella di testo **Nome attributo** digitare **uid**.
    
    c. Nell'elenco **Valore dell'attributo** , selezionare **user.userprincipal**.
    
    d. Fare clic su **completa**. Quindi **Applicare le modifiche** nella parte inferiore della pagina.

3. Nel menu nella parte superiore, fare clic su **Quick Start**.

    ![Configurare Single Sign-On][6]

4. Nel portale classico, nella pagina integrazione applicazione **Motori Cisco** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][7] 

5. Nella pagina **specificare come agli utenti di effettuare l'accesso ad Cisco** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_06.png)

6. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_07.png)


    un. Nella casella di testo su URL di accesso, digitare un URL utilizzando il modello seguente: `https://web.ciscospark.com/#/signin`.

    b. Fare clic su **Avanti**.


7. Nella pagina **Configura il single sign-on in motori Cisco** fare clic su **Download dei metadati**e quindi salvare il file nel computer in uso.

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_09.png)

8. Accedere a [Gestione della collaborazione di Cisco Cloud](https://admin.ciscospark.com/) con le credenziali di amministratore completo.
9. Selezionare **Impostazioni** e nella sezione **autenticazione** , fare clic su **Modifica**.

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)

10. Selezionare **integrazione un provider di identità di terze parti 3o. (Avanzate)** e passare alla schermata successiva.
11. Fare clic su **Scarica File di metadati** e salvare il file nel computer in uso.
12. Nella pagina **Importa Idp metadati** trascinare e rilasciare il file di metadati di Azure Active Directory nella pagina o utilizzare l'opzione file di browser per individuare e caricare il file di metadati di Azure Active Directory. Selezionare **Richiedi certificato firmato da un'autorità di certificazione nei metadati (più sicura)** , quindi fare clic su **Avanti**. 

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

13. Selezionare **Test Single Sign-on connessione**e quando si apre una nuova scheda del browser, eseguire l'autenticazione con Azure Active Directory effettuando l'accesso.
14. Tornare alla scheda **Gestione della collaborazione di Cisco Cloud** browser. Se il test ha esito positivo, selezionare **questa è stata stabilita. Abilitare l'opzione Single Sign-On** e fare clic su **Avanti**.

7. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

8. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
    
    ![Azure Active Directory Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.
    
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:
 
    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-a-cisco-spark-test-user"></a>Creazione di un utente prova ad Cisco

In questa sezione, si crea un utente chiamato Britta Simon in motori Cisco. In questa sezione, si crea un utente chiamato Britta Simon in motori Cisco.

1. Passare alla [Gestione della collaborazione di Cisco Cloud](https://admin.ciscospark.com/) con le credenziali di amministratore completo.
2. Fare clic su **utenti** e **gestire gli utenti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Nella finestra **Gestione utente** , selezionare **manualmente aggiungere o modificare utenti** e fare clic su **Avanti**.
4. Selezionare **nomi e indirizzi di posta elettronica**. Compilare quindi la casella di testo come procedere:

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    un. Nella casella di testo **nome** digitare **Sandro**

    b. Nella casella di testo **Cognome** digitare **Ezio**

    c. Nella casella di testo **indirizzo di posta elettronica** , digitare**britta.simon@contoso.com**

5. Fare clic sul segno più per aggiungere Britta Simon. Scegliere **Avanti**.
6. Nella finestra **Aggiungi servizi per gli utenti** , fare clic su **Salva** , quindi **Fine**.

### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua Cisco motori.

![Assegnare utente][200] 

**Per assegnare Britta Simon motori Cisco, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **I Cisco**.

    ![Configurare Single Sign-On](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_14.png) 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di tutti gli utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test il single sign-on

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Cisco motori nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on all'applicazione Cisco motori.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_205.png
