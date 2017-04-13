<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS) | Microsoft Azure"
    description="Informazioni su come utilizzare i servizi Web di Amazon (AWS) con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro!"
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
    ms.date="09/01/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-amazon-web-service-aws"></a>Esercitazione: Integrazione di Azure Active Directory con Amazon Web Service (AWS)

L'obiettivo di questa esercitazione è illustrano come integrare servizi Web di Amazon (AWS) con Azure Active Directory (Azure Active Directory).  
Integrazione del servizio Web di Amazon (AWS) con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso al servizio Web di Amazon (AWS) 
- È possibile consentire agli utenti di automaticamente ottenere firmato-on per Amazon Web Service (AWS) (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con Amazon Web Service (AWS), è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un servizio Web di Amazon (AWS) single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Lo scenario illustrato in questa esercitazione è costituito da tre blocchi predefiniti principali:

1. Aggiunta di servizi Web di Amazon (AWS) dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-amazon-web-service-aws-from-the-gallery"></a>Aggiunta di servizi Web di Amazon (AWS) dalla raccolta
Per configurare l'integrazione del servizio Web di Amazon (AWS) Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito servizio Web di Amazon (AWS) dalla raccolta.

### <a name="to-add-amazon-web-service-aws-from-the-gallery-perform-the-following-steps"></a>Per aggiungere servizi Web di Amazon (AWS) dalla raccolta, procedere come segue:

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1] 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore. 
   
    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina. 
   
    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**. 
   
    ![Applicazioni][4]

6. Nella casella Cerca digitare **Servizio Web di Amazon (AWS)**.
   
    ![Applicazioni][5]

7. Nel riquadro dei risultati selezionare **Servizio Web di Amazon (AWS)**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Applicazioni][6]



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è sufficiente visualizzare come configurare e testare Azure Active Directory single sign-on con Amazon Web Service (AWS) in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nel servizio Web di Amazon (AWS) a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nel servizio Web di Amazon (AWS) deve essere stabilito.  
È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nel servizio Web di Amazon (AWS).
 
Per configurare e testare Azure Active Directory il single sign-on con Amazon Web Service (AWS), è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di un servizio Web di Amazon (AWS) testare utente](#creating-a-halogen-software-test-user)** - necessario equivalente di Britta Simon Amazon Web Service (AWS) che è collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-single-sign-on"></a>Configurazione di Azure Active Directory Single Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on nell'applicazione di servizio Web di Amazon (AWS).  
L'applicazione di servizio Web di Amazon (AWS) prevede asserzioni SAML in un formato specifico, che è necessario aggiungere mapping degli attributi personalizzati per la configurazione di **attributi token saml** . Schermata seguente è illustrato un esempio per l'oggetto.


![Configurare Single Sign-On][27]

**Per configurare Azure Active Directory single sign-on con Amazon Web Service (AWS), procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Del servizio Web di Amazon (AWS)** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][7]

2. Nella pagina **specificare come agli utenti di accedere al servizio Web di Amazon (AWS)** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On][8]

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , fare clic su Avanti. 

    ![Configurare le impostazioni dell'App][9]
 
4. Nella pagina **Configura il single sign-on al servizio Web di Amazon (AWS)** fare clic su **Scarica metadati**e quindi salvare il file di metadati in locale nel computer in uso.

    ![Configurare Single Sign-On][10]

5. In una nuova finestra del browser, accesso al sito della società di servizi Web di Amazon (AWS) come amministratore.

6. Fare clic su **Home Console**.

    ![Configurare Single Sign-On][11]

7. Fare clic su **gestione delle identità e Access**. 

    ![Configurare Single Sign-On][12]

8. Fare clic su **Provider di identità**e quindi fare clic su **Crea Provider**. 

    ![Configurare Single Sign-On][13]

9. Nella pagina di finestra di dialogo **Configura Provider** , procedere come segue: 

    ![Configurare Single Sign-On][14]

     un. Come **Tipo di Provider**, selezionare **SAML**.

     b. Nella casella di testo **Nome Provider** , digitare un nome di provider (ad esempio: *sughero non trattati*).

     c. Per caricare il file di metadati scaricato, fare clic su **Scegli File**.

     d. Fare clic su **passaggio successivo**.


10. Nella pagina di finestra di dialogo **Verificare informazioni relative al Provider** , fare clic su **Crea**. 

    ![Configurare Single Sign-On][15]

11. Fare clic su **ruoli**e quindi fare clic su **Crea nuovo ruolo**. 

    ![Configurare Single Sign-On][16]

12. Nella finestra di dialogo **Impostazione del nome del ruolo** , procedere come segue: 

    ![Configurare Single Sign-On][17]

    un. Nella casella di testo **Nome ruolo** , digitare un nome di ruolo (ad esempio: *TestUser*).

    b. Fare clic su **passaggio successivo**.

13. Nella finestra di dialogo **Selezione tipo di ruolo** , procedere come segue: 

    ![Configurare Single Sign-On][18]

    un. Selezionare **ruolo per l'accesso di Provider di identità**.

    b. Nella sezione **accesso Grant Web Single Sign-On (WebSSO) per i provider di SAML** fare clic su **Seleziona**.


14. Nella finestra di dialogo **Stabilire attendibile** , procedere come segue:  

    ![Configurare Single Sign-On][19]
     
     un. Come provider di SAML, selezionare il provider SAML creati previousley (ad esempio: *sughero non trattati*) 

     b. Fare clic su **passaggio successivo**.


15. Nella finestra di dialogo **Verificare il ruolo di protezione** , fare clic su **Passaggio successivo**. 

    ![Configurare Single Sign-On][32]


16. Nella finestra di dialogo **Criteri allegare** , fare clic su **Passaggio successivo**.  

    ![Configurare Single Sign-On][33]


17. Nella finestra di dialogo **revisione** , procedere come segue:   

    ![Configurare Single Sign-On][34]

     un. Copiare il valore di **Informazioni di ruolo** .

     b. Copiare il valore di informazioni **Entità attendibili** .

     c. Fare clic su **Crea ruolo**. 

18. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.

    ![Che cos'è Azure AD Connect][20]

19. Nella pagina di **Conferma Single sign-on** , fare clic su **completa** per chiudere la finestra di dialogo **Configura il single sign-on** .

    ![Che cos'è Azure AD Connect][22]


20. Nel menu nella parte superiore, fare clic su **attributi** per aprire la finestra di dialogo **Attributi Token SAML** . 

    ![Configurare Single Sign-On][21]

21. Fare clic su **Aggiungi attributo utente**. 

    ![Configurare Single Sign-On][23]

22. Nella finestra di dialogo Aggiungi l'attributo utente, eseguire i passaggi seguenti. 

    ![Configurare Single Sign-On][24] 

    un. Nella casella di testo **Nome attributo** digitare **https://aws.amazon.com/SAML/Attributes/Role**.

    b. Nella casella di testo **Valore dell'attributo** , digitare **[valore informazioni ruolo], [valore attendibili informazioni entità]**.

    >[AZURE.TIP] Questi sono i valori copiati dalla finestra di dialogo revisione dopo aver creato il proprio ruolo. 

    c. Fare clic su **completa** per chiudere la finestra di dialogo **Aggiungi l'attributo utente** .

23. Fare clic su **Aggiungi attributo utente**. 

    ![Configurare Single Sign-On][23]


24. Nella finestra di dialogo Aggiungi l'attributo utente, eseguire i passaggi seguenti. 

    ![Configurare Single Sign-On][25]


     un. Nella casella di testo **Nome attributo** digitare **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. Nella casella di testo **Valore dell'attributo** , digitare o selezionare **user.userprincipalname** dall'elenco a discesa.
     
    ![Configurare Single Sign-On][35]
    

     c. Fare clic su **completa** per chiudere la finestra di dialogo **Aggiungi l'attributo utente** .


25. Fare clic su **Applica modifiche**. 

    ![Configurare Single Sign-On][26]





### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory

L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png) 
 
4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png) 

  1. Come tipo di utente, selezionare nuovo utente nell'organizzazione.
  2. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.
  3. Fare clic su Avanti.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nel **Cognome** txtbox, tipo, **Ezio**.
  
    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.
  
    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png) 
 
8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.
  
    b. Fare clic su **completa**.   
  
 
### <a name="creating-a-amazon-web-service-aws-test-user"></a>Creazione di un utente di test del servizio Web di Amazon (AWS)

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon nel servizio Web di Amazon (AWS).

### <a name="to-create-a-user-called-britta-simon-in-amazon-web-service-aws-perform-the-following-steps"></a>Per creare un utente chiamato Britta Simon nel servizio Web di Amazon (AWS), procedere come segue:

1. Accedere al sito della società di **Servizi Web di Amazon (AWS)** come amministratore.

2. Fare clic sull'icona **Home Console** . 

    ![Configurare Single Sign-On][11]

3. Fare clic su identità e gestione di accesso. 

    ![Configurare Single Sign-On][28]

4. Nel Dashboard, fare clic su utenti e quindi fare clic su Creazione di nuovi utenti. 

    ![Configurare Single Sign-On][29]

5. Nella finestra di dialogo Crea utente, procedere come segue: 

    ![Configurare Single Sign-On][30]

     un. Nelle caselle di testo **Immettere i nomi utente** digitare il nome utente Brita Simon (userprincipalname) in Azure Active Directory.

     b. Fare clic su **Crea**.




### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo utente l'accesso al servizio Web di Amazon (AWS).

![Assegnare utente][31]

**Per assegnare Britta Simon CloudPassage, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][26]

2. Nell'elenco delle applicazioni, selezionare **Servizio Web di Amazon (AWS)**.

    ![Assegnare utente][27]

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][25]

1. Nell'elenco di utenti, selezionare **Sandro**.

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][29]

### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro del servizio Web di Amazon (AWS) nel Pannello di accesso, dovrebbe ottenere automaticamente firmato-on per l'applicazione di servizio Web di Amazon (AWS).


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service/user_attributes_01.png






















