<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con totalmente Cloud | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e totalmente Cloud."
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
    ms.date="10/04/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Esercitazione: Integrazione di Azure Active Directory con totalmente Cloud

In questa esercitazione si imparerà a integrare totalmente Cloud con Azure Active Directory (Azure Active Directory).

Integrazione totalmente Cloud con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso totalmente cloud
- È possibile consentire agli utenti di automaticamente ottenere firmato-on totalmente cloud (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con totalmente Cloud, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Cloud totalmente single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Cloud totalmente dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-lifesize-cloud-from-the-gallery"></a>Aggiunta di Cloud totalmente dalla raccolta
Per configurare l'integrazione del Cloud totalmente Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Cloud totalmente dalla raccolta.

**Per aggiungere Cloud totalmente dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]
2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Totalmente Cloud**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)

7. Nel riquadro dei risultati selezionare **Totalmente Cloud**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e test Azure Active Directory single sign-on con totalmente Cloud in base a un utente di test denominato "Britta Simon".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente nel Cloud totalmente a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente totalmente Cloud deve essere stabilito.

Assegnando il valore di **nome utente** in Active Directory Azure come il valore di **nome utente** nel Cloud totalmente stabilita la relazione di collegamento.

Per configurare e testare Azure Active Directory single sign-on con totalmente Cloud, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di una nuvola totalmente testare utente](#creating-a-lifesize-cloud-test-user)** - abbiano equivalente di Britta Simon nel Cloud totalmente collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on nell'applicazione totalmente Cloud.


**Per configurare Azure Active Directory single sign-on con totalmente Cloud, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Totalmente Cloud** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come gli utenti di accedere al Cloud totalmente** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso all'applicazione Cloud totalmente utilizzando il modello seguente: **https://login.lifesizecloud.com/ls/?acs**.
    
    b. Fare clic su **Avanti**
 
4. Nella pagina **Configura il single sign-on al Cloud totalmente** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)

    un. Fare clic su **Scarica certificato**e quindi salvare il file nel computer in uso.

    b. Fare clic su **Avanti**.


5. Per ottenere SSO configurato per l'applicazione accesso nell'applicazione totalmente Cloud con privilegi di amministratore.

6. Nell'angolo superiore destro fare clic sul proprio nome e quindi fare clic su **Impostazioni avanzate**

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

7. In impostazioni avanzate adesso clic sul collegamento della **Configurazione di Single Sign-on** . Viene visualizzata la pagina di configurazione di Single Sign-on per l'istanza.

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

8. Configurare i valori seguenti nell'interfaccia utente di configurazione Single Sign-on.    

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    • Copiare il valore dell'URL emittente da Azure Active Directory e incollare il testo nella casella di testo **Emittente Provider di identità** .

    • Copiare il valore dell'URL di accesso remoto da Azure Active Directory e incollare il testo nella casella di testo **URL di accesso** .

    • Aprire il certificato scaricato nel blocco note e copiare il contenuto del certificato, escluse le linee di inizio e fine certificato, incollare il nella casella di testo **Certificato x. 509** .

    • Nel mapping SAML attributo per la casella di testo **nome** immettere il valore come **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    • Nel mapping degli attributi di SAML **Il cognome** della casella di testo immettere il valore come **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**

    • Nel mapping SAML attributo per la casella di testo del **messaggio di posta elettronica** immettere il valore come **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

9. Per verificare la configurazione è possibile fare clic sul pulsante **Test** .

    > [AZURE.NOTE] Per il test ha esito positivo è necessario completare la configurazione guidata in Azure Active Directory e offrono accesso a utenti o gruppi che possono eseguire il test.
    
10. Attivare il Single Sign-on selezionando sul pulsante **Abilita Single Sign-on** .

11. A questo punto fare clic sul pulsante **Aggiorna** in modo che tutte le impostazioni vengono salvate. Verrà generato il valore di RelayState. Copiare il valore di RelayState generato nella casella di testo. È necessario questo valore nei passaggi successivi.

12. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

13. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]

14. A questo punto di accesso nel portale di gestione Azure **https://portal.azure.com** utilizzando le credenziali di amministratore

15. Fare clic sul collegamento di **Altri servizi** nel riquadro di spostamento a sinistra
    
    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)

16. Ricerca di Azure Active Directory e fare clic sul collegamento **Azure Active Directory**
    
    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)

17. Sono disponibili tutte le applicazioni di SaaS sotto il pulsante di **Applicazioni aziendali** .

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)

18. Fare clic sul collegamento di **Tutte le applicazioni** nella e successivo
    
    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)

19. Cercare applicazione totalmente per il quale si desidera impostare la RelayState. 
    
    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)

20. Fare clic su in e il **servizio Single sign-on** collegamento

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)

21. Verranno visualizzati la casella di controllo **Mostra avanzate impostazioni URL** . Fare clic sulla casella di controllo.
    
    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
    
22. Configurare RelayState per l'applicazione, viene visualizzato nella pagina di configurazione totalmente applicazione Single Sign-on. 

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)

23. Salvare le impostazioni.

### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , eseguire le operazioni seguenti:  ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , eseguire le operazioni seguenti: ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   



### <a name="creating-an-lifesize-cloud-test-user"></a>Creazione di un utente di test totalmente Cloud

In questa sezione, si crea un utente chiamato Britta Simon totalmente cloud. Cloud totalmente supportano il provisioning automatico degli utenti. Dopo l'autenticazione in Azure Active Directory, l'utente eseguire automaticamente il provisioning dell'applicazione. 


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua totalmente Cloud.

![Assegnare utente][200] 

**Per assegnare Britta Simon totalmente cloud, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Totalmente Cloud**.

    ![Configurare Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


### <a name="testing-single-sign-on"></a>Test Single Sign-On

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro totalmente Cloud nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione totalmente Cloud.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png
