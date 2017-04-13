<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Qlik senso Enterprise | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Qlik senso Enterprise."
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
    ms.date="08/31/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-qlik-sense-enterprise"></a>Esercitazione: Integrazione di Azure Active Directory con Qlik senso Enterprise

In questa esercitazione si imparerà a integrare Qlik senso Enterprise con Azure Active Directory (Azure Active Directory).

Integrazione Qlik senso Enterprise con Azure Active Directory offre i seguenti vantaggi:

- È possibile controllare in Azure Active Directory chi ha accesso a Qlik senso Enterprise
- È possibile consentire agli utenti di automaticamente ottenere effettuato l'accesso a Qlik senso Enterprise (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti

Per configurare l'integrazione di Azure Active Directory con Qlik senso Enterprise, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Un Qlik senso Enterprise single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Descrizione dello scenario
In questa esercitazione si testare Azure Active Directory il single sign-on in un ambiente di testing.

Scenario illustrato in questa esercitazione è composto da due blocchi predefiniti principali:

1. Aggiunta di Qlik senso Enterprise dalla raccolta
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Aggiunta di Qlik senso Enterprise dalla raccolta
Per configurare l'integrazione di Qlik senso Enterprise in Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Qlik senso Enterprise dalla raccolta.

**Per aggiungere Qlik senso Enterprise dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory][1]
2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Qlik senso Enterprise**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Nel riquadro dei risultati, selezionare **Qlik senso Enterprise**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
In questa sezione, configurare e testare Azure Active Directory single sign-on con Enterprise senso Qlik in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario riconosce l'utente corrispondente in Qlik senso Enterprise a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente Qlik senso Enterprise deve essere stabilito.

È stata stabilita la relazione di collegamento assegnando il valore di **nome utente** in Active Directory Azure come valore di **nome utente** nella Qlik senso Enterprise.

Per configurare e testare Azure Active Directory single sign-on con Qlik senso Enterprise, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
3. **[Creazione di un'organizzazione senso Qlik test utente](#creating-a-qliksense-enterprise-test-user)** - che equivalente di Britta Simon nell'Enterprise senso Qlik collegato per la rappresentazione di Azure Active Directory di lei.
4. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione Azure Active Directory single sign-on

In questa sezione, attivare Azure Active Directory single sign-on nel portale di classica e configurare il single sign-on applicazione Qlik senso aziendale.


**Per configurare Azure Active Directory single sign-on con Qlik senso Enterprise, procedere come segue:**

1. Nel portale classico, nella pagina integrazione applicazione **Qlik senso Enterprise** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .
     
    ![Configurare Single Sign-On][6] 

2. Nella pagina **specificare come agli utenti di accedere alla versione Enterprise senso Qlik** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png) 

3. Nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png) 

    un. Nella casella di testo **Su URL di accesso** , digitare l'URL utilizzato da agli utenti di accesso a un'applicazione Enterprise senso Qlik utilizzando il modello seguente: **https://\<Qlik senso completamente completo Hostname\>: 443 / < prefisso Proxy virtuale\>/samlauthn/**.
    
    > [AZURE.NOTE] Si noti che una barra finale alla fine di questo URI.  È necessario.

    b. Fare clic su **Avanti**
 
4. Nella pagina **Configura il single sign-on in Qlik senso Enterprise** , procedere come segue:

    ![Configurare Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    un. Fare clic su **Scarica metadati**e quindi salvare il file nel computer in uso.  Prepararsi a modificare il file di metadati prima di caricare nel server Qlik senso.

    b. Fare clic su **Avanti**.

5. Preparare il file XML di metadati federazione in modo che è possibile caricare che al server Qlik senso.

    > [AZURE.NOTE] Prima di caricare i metadati IdP al server Qlik senso, il file deve essere modificati per rimuovere le informazioni per garantire il corretto funzionamento tra Azure Active Directory e senso Qlik server.

    ![QlikSense][qs24]

    un. Aprire il file FederationMetadata scaricato da Azure in un editor di testo.

    b. Cercare il valore **RoleDescriptor**.  Si verificherà quattro voci (due coppie di apertura e chiusura tag).

    c. Eliminare tra i tag RoleDescriptor e tutte le informazioni dal file.

    d. Salvare il file e mantenerlo vicini più avanti in questo documento.

6. Passare a Qlik senso Qlik Management Console (QMC) come utente che è possibile creare configurazioni proxy virtuale.

7. In QMC, fare clic sulla voce di menu Proxy virtuale.

    ![QlikSense][qs6] 

8. Nella parte inferiore dello schermo, fare clic su Crea nuovo pulsante.

    ![QlikSense][qs7]

9. Verrà visualizzata la schermata di modifica proxy virtuale.  Sul lato destro dello schermo è un menu delle opzioni di configurazione visibile.

    ![QlikSense][qs9]

10. Con l'opzione di menu identificazione selezionata, immettere le informazioni di identificazione per la configurazione del proxy virtuale Azure.

    ![QlikSense][qs8]  
    
    un. Il campo Descrizione è un nome descrittivo per la configurazione del proxy virtuale.  Immettere un valore per una descrizione.
    
    b. Il campo prefisso identifica l'endpoint proxy virtuale per la connessione a senso Qlik con Azure Active Directory Single Sign-On.  Immettere un nome univoco prefisso per questo proxy virtuale.

    c. Timeout di inattività sessione (minuti) è il timeout per le connessioni tramite questo proxy virtuale.

    d. Il nome di intestazione cookie sessione è il nome di cookie archiviazione l'identificatore di sessione per la sessione senso Qlik che l'utente riceve dopo l'autenticazione.  Questo nome deve essere univoco.

11. Fare clic sull'opzione di menu autenticazione per rendere più visibili.  Viene visualizzata la schermata di autenticazione.

    ![QlikSense][qs10]

    un. **Modalità di accesso anonimo** a discesa determina se gli utenti anonimi possono accedere senso Qlik attraverso il proxy virtuale.  L'opzione predefinita non è nessun utente anonimo.

    b. **Metodo di autenticazione** a discesa determina che lo schema di autenticazione del proxy virtuale utilizzerà.  Selezionare SAML dall'elenco a discesa.  Di conseguenza verranno visualizzate ulteriori opzioni.

    c. Nel **campo URI host SAML**input che utenti hostname entrerà per accedere a Qlik senso tramite questo proxy virtuale SAML.  Il nome host è l'uri del server Qlik senso.

    d. Nella casella **ID entità SAML**immettere lo stesso valore immesso per il campo URI SAML host.

    e. I **metadati SAML IdP** è il file modificato in precedenza nella sezione **Modifica metadati federazione dalla configurazione di Azure Active Directory** .  Rimuovere le informazioni per garantire il corretto funzionamento tra Azure Active Directory **prima di caricare i metadati IdP, il file deve essere modificati** e senso Qlik server.  **Fare riferimento alle istruzioni sopra se il file non è stato ancora da modificare.**  Se il file è stato modificato fare clic sul pulsante Sfoglia e selezionare il file di metadati modificata per caricare la configurazione del proxy virtuale.

    f. Immettere il nome dell'attributo o riferimento allo schema per l'attributo SAML che rappresenta l' **ID utente** Azure Active Directory verrà inviato al server Qlik senso.  Informazioni di riferimento schema sono disponibile nella configurazione dell'applicazione Azure schermate post.  Per utilizzare l'attributo name, **immettere http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

    g. Immettere il valore della **directory utente** che verranno collegati agli utenti quando eseguono l'autenticazione per server Qlik senso tramite Azure Active Directory.  Valori hardcoded devono essere racchiuso tra **parentesi quadre []**.  Per utilizzare un attributo inviato asserzione SAML di Azure Active Directory, immettere il nome dell'attributo questo testo casella **senza** racchiusa tra parentesi quadre.

    h. **Algoritmo di firma SAML** imposta per la configurazione del proxy virtuale di firma del certificato di provider (in questo caso senso Qlik server) del servizio.  Se senso Qlik server utilizza un certificato attendibile generato utilizzando Microsoft Enhanced RSA e crittografia AES, impostare l'algoritmo di firma SAML **SHA-256**.

    si. Sezione del mapping attributo SAML consente di attributi aggiuntivi come gruppi devono essere inviati Qlik senso per l'utilizzo di regole di protezione.

12. Fare clic sulla voce di menu che si desidera mostrare di bilanciamento del carico.  Viene visualizzata la schermata di bilanciamento del carico.

    ![QlikSense][qs11]

13. Fare clic su Aggiungi nuovo server nodo, nodo selezionare motore o nodi senso Qlik verranno inviato di sessioni per scopi di bilanciamento del carico e fare clic sul pulsante Aggiungi.

    ![QlikSense][qs12]

14. Fare clic sull'opzione di menu Avanzate per rendere più visibili. Viene visualizzata la schermata avanzata.

    ![QlikSense][qs13]

    un. L'elenco di bianco Host identifica nomi host accettati durante la connessione al server Qlik senso.  **Immettere il nome host utenti verranno specificato durante la connessione al server Qlik senso.** Il nome host è lo stesso valore di uri host SAML senza il https://.

15. Fare clic sul pulsante Applica.

    ![QlikSense][qs14]

16. Fare clic su OK per accettare il messaggio di avviso che indica i proxy collegati al proxy di virtuale verranno riavviati.

    ![QlikSense][qs15]

17. Sul lato destro dello schermo, viene visualizzato il menu di elementi associati.  Fare clic sull'opzione di menu proxy.

    ![QlikSense][qs16]

18. Viene visualizzata la schermata proxy.  Fare clic sul pulsante collegamento nella parte inferiore per collegare un proxy al proxy di virtuale.

    ![QlikSense][qs17]

19. Selezionare il nodo proxy che supportano questa connessione virtuale proxy e fare clic sul pulsante di collegamento.  Dopo aver collegato, il proxy sarà elencato in proxy associato.

    ![QlikSense][qs18]
    ![QlikSense][qs19]

20. Dopo circa 5 a 10 secondi, verrà visualizzato il messaggio di aggiornamento QMC.  Fare clic sul pulsante Aggiorna QMC.

    ![QlikSense][qs20]

21. Quando viene aggiornato il QMC, fare clic sulla voce di menu proxy virtuale. La nuova voce proxy virtuale SAML viene elencata nella tabella sullo schermo.  Singolo clic sulla voce proxy virtuale.

    ![QlikSense][qs51]

22. Nella parte inferiore dello schermo, il pulsante di metadati scaricare SP attiverà.  Fare clic sul pulsante di metadati scaricare SP per salvare i metadati per un file.

    ![QlikSense][qs52]

23. Aprire il file di metadati sp.  Osservare la voce **entityID** e la voce **AssertionConsumerService** .  Questi valori sono uguali per l' **identificatore** e l' **URL di accesso** nella configurazione dell'applicazione Azure Active Directory. Se non corrispondono è necessario sostituirli nella configurazione guidata di Azure Active Directory App.

    ![QlikSense][qs53]

24. Nel portale classico, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**.
    
    ![Azure Active Directory Single Sign-On][10]

25. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  
 
    ![Azure Active Directory Single Sign-On][11]


### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
In questa sezione, si crea un utente di test nel portale di classica chiamato Britta Simon.


![Creazione di Azure Active Directory utente][20]

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png) 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png) 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png) 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , eseguire le operazioni seguenti:  ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png) 

    un. Come tipo di utente, selezionare nuovo utente nell'organizzazione.

    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su **Avanti**.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , eseguire le operazioni seguenti: ![la creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png) 

    un. Nella casella di testo **nome** digitare **Sandro**.  

    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png) 

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creazione di un utente di test di Azure Active Directory](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png) 

    un. Prendere nota del valore della **Nuova Password**.

    b. Fare clic su **completa**.   


### <a name="creating-an-qlik-sense-enterprise-test-user"></a>Creazione di un utente di test Qlik senso Enterprise

In questa sezione, si crea un utente chiamato Britta Simon Qlik senso organizzazione. Lavorare con il team di supporto dell'organizzazione senso Qlik per aggiungere gli utenti della piattaforma Qlik senso Enterprise.


### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

In questa sezione, viene attivata Britta Simon usare il single sign-on Azure concedendo la sua Qlik senso Enterprise.

![Assegnare utente][200] 

**Per assegnare Britta Simon Qlik senso Enterprise, procedere come segue:**

1. Nel portale classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Assegnare utente][201] 

2. Nell'elenco delle applicazioni, selezionare **Qlik senso Enterprise**.

    ![Configurare Single Sign-On](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png) 

3. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Assegnare utente][203]

4. Nell'elenco di utenti, selezionare **Sandro**.

5. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Assegnare utente][205]


## <a name="testing-single-sign-on"></a>Test single sign-On

In questa sezione è testare Azure Active Directory single sign-on configurazione mediante il pannello di accesso.

Quando si fa clic sul riquadro Qlik senso Enterprise nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Enterprise senso Qlik.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png