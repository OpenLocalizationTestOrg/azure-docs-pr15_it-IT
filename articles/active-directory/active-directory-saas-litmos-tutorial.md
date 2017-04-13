<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Litmos | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Litmos."
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


# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Esercitazione: Integrazione di Azure Active Directory con Litmos

L'obiettivo di questa esercitazione è illustrano come integrare Litmos con Azure Active Directory (Azure Active Directory).  
Integrazione di Litmos con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso a Litmos 
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Litmos (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - Azure Active Directory 

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con Litmos, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Litmos single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Lo scenario illustrato in questa esercitazione è costituito da tre blocchi predefiniti principali:

1. Aggiunta di Litmos dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-litmos-from-the-gallery"></a>Aggiunta di Litmos dalla raccolta
Per configurare l'integrazione di Litmos in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Litmos dalla raccolta.

**Per aggiungere Litmos dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Litmos**.

    ![Applicazioni][5]

7. Nel riquadro dei risultati, selezionare **Litmos**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Applicazioni][500]


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con Litmos in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente in Litmos a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Litmos deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** in Litmos.
 
Per configurare e testare Azure Active Directory single sign-on con Litmos, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un Litmos test utente](#creating-a-halogen-software-test-user)** - che equivalente di Britta Simon nel Litmos collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale classico di Azure Active Directory e configurare il single sign-on Litmos nell'applicazione in uso.  
Come parte di questa procedura, è necessario creare un file di certificato codificato base 64.  
Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

Nell'ambito della configurazione, è necessario personalizzare gli **Attributi di Token SAML** per l'applicazione Litmos.  

![Azure Active Directory Single Sign-On][17] 

**Per configurare Azure Active Directory single sign-on con Litmos, procedere come segue:**

1. Nel portale classico di Azure Active Directory, nella pagina integrazione applicazione **Litmos** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al Litmos** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.
 
    ![Azure Active Directory Single Sign-On][7] 


1. Accesso al sito della società Litmos (ad esempio: *https://azureapptest.litmos.com/account/Login*) come amministratore.

    ![Azure Active Directory Single Sign-On][21] 


1. Nella barra di spostamento sul lato sinistro fare clic su **account**.

    ![Azure Active Directory Single Sign-On][22] 


1. Fare clic sulla scheda **integrazioni** .

    ![Azure Active Directory Single Sign-On][23] 


1. Nella scheda **integrazioni** , scorrere fino a **3 ° integrazioni di terze parti**e quindi fare clic sulla scheda **SAML 2.0** .

    ![Azure Active Directory Single Sign-On][24] 

1. Copiare il valore in **è il SAML endoiint per litmos:**.

    ![Azure Active Directory Single Sign-On][26] 


3. Nel portale di classica Azure, nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Azure Active Directory Single Sign-On][8] 
 
    un. Nella casella di testo **identificatore** , digitare l'URL utilizzato dagli utenti per accesso all'applicazione Litmos (ad esempio: *https://azureapptest.litmos.com/account/Login*).
     
    b. Nella casella di testo **URL di risposta** , incollare il valore copiato dall'applicazione Litmos nel passaggio precedente.

    c. Fare clic su **Avanti**.
 
4. Nella pagina **Configura il single sign-on in Litmos** , procedere come segue:

    ![Azure Active Directory Single Sign-On][2] 

    un. Fare clic su Scarica certificato e quindi salvare il file nel computer in uso.


1. Nell'applicazione **Litmos** , procedere come segue:

    ![Azure Active Directory Single Sign-On][25] 

    un. Fare clic su **Attiva SAML**.

    b. Creare un file **con codifica base 64** del certificato scaricata.  

    >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o)

    c. Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto che ne e quindi incollarlo alla casella di testo **Certificato x. 509 SAML** .

    d. Fare clic su **Salva modifiche**.


6. Nel portale classico di Azure Active Directory, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Azure Active Directory Single Sign-On][10]

7. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
  
    ![Azure Active Directory Single Sign-On][11]


20. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** . 

    ![Configurare Single Sign-On][12]


24. Nella finestra di dialogo **Aggiungi l'attributo utente** , procedere come segue: 

    ![Configurare Single Sign-On][14]

  	| Nome attributo | Valore dell'attributo |
  	| ---            | ---             |
  	| Posta elettronica          | User.Mail       |
  	| Nome      | User.givenName  |
  	| Cognome       | User.surname    |

    Per ogni riga di dati nella tabella precedente, procedere come segue:
   
    un. Fare clic su **Aggiungi attributo utente**. 

    ![Configurare Single Sign-On][15]


    un. Nella casella di testo **Nome attributo** , digitare il **Nome dell'attributo** indicato per tale riga.

    b. Selezionare il **Valore dell'attributo** visualizzato per tale riga.

    c. Fare clic su **completa** per chiudere la finestra di dialogo **Aggiungi l'attributo utente** .


25. Fare clic su **Applica modifiche**. 

    ![Configurare Single Sign-On][16]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.  

![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale di Azure clasic**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_09.png)  

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_05.png)  

    un. Come **Tipo di utente**, selezionare **nuovo utente nell'organizzazione**.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_06.png) 
 
    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-litmos-tutorial/create_aaduser_08.png) 
  
    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   

  
 
### <a name="creating-a-litmos-test-user"></a>Creazione di un utente di test Litmos

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon in Litmos.  
L'applicazione Litmos supporta solo in fase di provisioning. In questo campo, un account utente viene creato automaticamente se necessario durante un tentativo di accedere all'applicazione mediante il pannello di accesso.

**Per creare un utente chiamato Britta Simon in Litmos, procedere come segue:**


1. Accesso al sito della società Litmos (ad esempio: *https://azureapptest.litmos.com/account/Login*) come amministratore.

    ![Azure Active Directory Single Sign-On][21] 


1. Nella barra di spostamento sul lato sinistro fare clic su **account**.

    ![Azure Active Directory Single Sign-On][22] 


1. Fare clic sulla scheda **integrazioni** .

    ![Azure Active Directory Single Sign-On][23] 


1. Nella scheda **integrazioni** , scorrere fino a **3 ° integrazioni di terze parti**e quindi fare clic sulla scheda **SAML 2.0** .

    ![Azure Active Directory Single Sign-On][24] 

1. Selezionare **Genera automaticamente gli utenti:**.

    ![Azure Active Directory Single Sign-On][27] 


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo la sua Litmos.

![Assegnare utente][200] 

**Per assegnare Britta Simon Litmos, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Litmos**.

    ![Assegnare utente][202] 

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203] 

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro Litmos nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Litmos.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_01.png
[500]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_02.png

[6]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_03.png
[8]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_04.png
[9]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_05.png
[10]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_07.png
[12]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_80.png
[13]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[14]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_82.png
[15]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_81.png
[16]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_19.png
[17]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_67.png


[20]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_100.png
[21]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_66.png

[200]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_68.png
[203]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-litmos-tutorial/tutorial_general_205.png


[400]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_400.png
[401]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_401.png
[402]: ./media/active-directory-saas-litmos-tutorial/tutorial_litmos_402.png





