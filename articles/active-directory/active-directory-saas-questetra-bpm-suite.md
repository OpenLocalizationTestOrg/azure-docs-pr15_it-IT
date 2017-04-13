<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con famiglia di prodotti BPM Questetra | Microsoft Aure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e Questetra BPM famiglia di prodotti."
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
    ms.date="10/28/2016"
    ms.author="jeedes"/>


# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Esercitazione: Integrazione di Azure Active Directory con Questetra BPM famiglia

L'obiettivo di questa esercitazione è illustrano come integrare Questetra BPM Suite con Azure Active Directory (Azure Active Directory).  
Integrazione di famiglia di prodotti BPM Questetra con Azure Active Directory offre i seguenti vantaggi: 

- È possibile controllare in Azure Active Directory chi ha accesso alla famiglia di prodotti BPM Questetra 
- È possibile consentire agli utenti di automaticamente ottenere firmato-on alla famiglia di prodotti BPM Questetra (Single Sign-On) con il proprio account Azure Active Directory
- È possibile gestire gli account in una posizione centrale - portale classica di Azure

Se si desiderano ulteriori dettagli sull'integrazione di app SaaS con Azure Active Directory, vedere [che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Prerequisiti 

Per configurare l'integrazione di Azure Active Directory con Questetra BPM Suite, è necessario quanto segue:

- Una sottoscrizione di Azure Active Directory
- Una [Famiglia di prodotti BPM Questetra](https://senbon-imadegawa-988.questetra.net/) single sign-abbonamento abilitato


> [AZURE.NOTE] Per testare la procedura descritta in questa esercitazione, è consigliabile non usare un ambiente di produzione.


Per verificare la procedura descritta in questa esercitazione, è necessario seguire questi suggerimenti:

- Non utilizzare l'ambiente di produzione, a meno che non è necessario.
- Se non si dispone di un ambiente di valutazione di Azure Active Directory, è possibile ottenere un mese di una versione di valutazione [qui](https://azure.microsoft.com/pricing/free-trial/). 

 
## <a name="scenario-description"></a>Descrizione dello scenario
L'obiettivo di questa esercitazione è che consentono di verificare Azure Active Directory single sign-on in un ambiente di testing.  
Lo scenario illustrato in questa esercitazione è costituito da tre blocchi predefiniti principali:

1. Aggiunta di famiglia di prodotti BPM Questetra dalla raccolta 
2. Configurazione e verifica Azure Active Directory single sign-on


## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Aggiunta di famiglia di prodotti BPM Questetra dalla raccolta
Per configurare l'integrazione di famiglia di prodotti BPM Questetra Azure Active Directory, è necessario aggiungere l'elenco di App SaaS gestito Questetra BPM Suite dalla raccolta.

**Per aggiungere Questetra BPM Suite dalla raccolta, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**. 

    ![Active Directory][1]

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Applicazioni][4]

6. Nella casella Cerca digitare **Questetra BPM famiglia di prodotti**.

    ![Applicazioni][5]

7. Nel riquadro dei risultati, selezionare **Questetra BPM famiglia di prodotti**e quindi fare clic su **completa** per aggiungere l'applicazione.



##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configurazione e verifica Azure Active Directory single sign-on
L'obiettivo di questa sezione è illustrato come configurare e testare Azure Active Directory single sign-on con Suite BPM Questetra in base a un utente di test denominato "Ezio Sandro".

Single sign-on per l'uso, è necessario indicare che cos'è l'utente corrispondente nella famiglia di prodotti BPM Questetra a un utente in Active Directory Azure Azure Active Directory. In altre parole, una relazione di collegamento tra un utente di Azure Active Directory e il relativo utente nella famiglia di prodotti BPM Questetra deve essere stabilito.  
Assegnando il valore di **nome utente** in Active Directory Azure come valore di **nome utente** nella famiglia di prodotti BPM Questetra stabilita la relazione di collegamento.
 
Per configurare e testare Azure Active Directory single sign-on con Questetra BPM Suite, è necessario completare i blocchi predefiniti seguenti:

1. **[Configurazione di Azure Active Directory Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - per consentire agli utenti di utilizzare questa caratteristica.
2. **[Creazione di un annuncio Azure testare utente](#creating-an-azure-ad-test-user)** - verificare Azure Active Directory single sign-on con Britta Simon.
4. **[Creazione di una famiglia di prodotti BPM Questetra test utente](#creating-a-questetra-bpm-suite-test-user)** - disporre di un equivalente di Britta Simon nella famiglia di prodotti BPM Questetra collegato per la rappresentazione di Azure Active Directory di lei.
5. **[Assegnazione di Azure AD testare utente](#assigning-the-azure-ad-test-user)** - abilitare Britta Simon a utilizzare Azure Active Directory single sign-on.
5. **[Test Single Sign-On](#testing-single-sign-on)** - per verificare se funziona la configurazione.

### <a name="configuring-azure-ad-single-sign-on"></a>Configurazione di Azure Active Directory Single Sign-On

L'obiettivo di questa sezione è per attivare Azure Active Directory single sign-on nel portale di classica Azure e configurare il single sign-on Questetra BPM Suite nell'applicazione in uso.

**Per configurare Azure Active Directory single sign-on con Questetra BPM Suite, procedere come segue:**

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Questetra BPM Suite** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare Single Sign-On][8]

2. Nella pagina **specificare come agli utenti di eseguire l'accesso alla famiglia di prodotti BPM Questetra** selezionare **Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Azure Active Directory Single Sign-On][9]


3. In una finestra del browser web diverso, accedere a sito della società **Questetra BPM Suite** come amministratore.

4. Nel menu nella parte superiore, fare clic su **Impostazioni di sistema**. 

    ![Azure Active Directory Single Sign-On][10]

5. Per aprire la pagina **SingleSignOnSAML** , fare clic su **Single Sign-on SAML ()**. 

    ![Azure Active Directory Single Sign-On][11]


6. Nel portale di classica Azure, nella pagina di finestra di dialogo **Configura impostazioni di App** , procedere come segue: 

    ![Configurare le impostazioni dell'App][13]
 
    un. Via si sito aziendale **Questetra BPM famiglia di prodotti** , nella sezione informazioni di SP copiare l' **URL ACS**e quindi incollarla nella casella di testo **Su URL di accesso** .

    b. Via si sito aziendale **Questetra BPM famiglia di prodotti** , nella sezione informazioni di SP copiare l' **ID di entità**e quindi incollarla nella casella di testo **URL emittente** .

    c. Via si sito aziendale **Questetra BPM famiglia di prodotti** , nella sezione informazioni di SP copiare l' **URL ACS**e quindi incollarla nella casella di testo **URL di risposta** .

    d. Fare clic su **Avanti**.

 
7. Nella pagina **Configura il single sign-on Questetra BPM Suite** fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare Single Sign-On][14]


8. Nel sito della società **Questetra BPM Suite** , procedere come segue: 

    ![Configurare Single Sign-On][15]

    un. Selezionare **Attiva Single Sign-On**.
     
    b. Nel portale di classica Azure, copiare il valore di **URL emittente** e quindi incollarla nella casella di testo **ID entità** .

    c. Nel portale di classica Azure, copiare il valore di **URL del servizio Single Sign-On** e quindi incollarla nella casella di testo **URL della pagina di accesso** .

    d. Nel portale di classica Azure, copiare il valore **Singolo URL del servizio di Sign-Out** e quindi incollarla nella casella di testo **URL della pagina disconnessione** .

    e. Nella casella di testo **formato NameID** digitare **urn: oasis: nomi: tc: SAML:1.1:nameid-formato: emailAddress**.


    f. Creare un file codificato base 64 del certificato scaricata. 

    >[AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

    g. Aprire il certificato codificato base 64 nel blocco note, copiare negli Appunti il contenuto di esso e quindi incollarlo nella casella di testo **certificato di convalida** . 

    h. Fare clic su **Salva**.


9. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **Avanti**. 

    ![Che cos'è Azure AD Connect][17]


10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.  

    ![Che cos'è Azure AD Connect][18]




### <a name="creating-an-azure-ad-test-user"></a>Creazione di un utente di test di Azure Active Directory
L'obiettivo di questa sezione consiste nel creare un utente di prova nel portale di classica Azure chiamato Britta Simon.

**Per creare un utente di prova in Azure Active Directory, procedere come segue:**

1. Nel **portale classica Azure**, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Creare l'utente test Azure Active Directory][100] 

2. Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per visualizzare l'elenco di utenti, nel menu nella parte superiore, fare clic su **utenti**.

    ![Creare l'utente test Azure Active Directory][101] 

4. Per aprire la finestra di dialogo **Aggiungi utente** , nella barra degli strumenti nella parte inferiore, fare clic su **Aggiungi utente**. 

    ![Creare l'utente test Azure Active Directory][102] 

5. Nella scheda **inviare commenti e suggerimenti su questo utente** , procedere come segue:

    ![Creare l'utente test Azure Active Directory][103]
 
    un. Come **Tipo di utente**, selezionare **nuovo utente nell'organizzazione**.
  
    b. Nella casella nome utente di **casella di testo**digitare **BrittaSimon**.

    c. Fare clic su Avanti.

6.  Nella pagina di finestra di dialogo del **Profilo utente** , procedere come segue: 

    ![Creare l'utente test Azure Active Directory][104] 
  
    un. Nella casella di testo **nome** digitare **Sandro**. 
 
    b. Nella casella **Cognome** di testo digitare **Ezio**.

    c. Nella casella di testo **Nome visualizzato** digitare **Sandro**.

    d. Nell'elenco **ruolo** selezionare **utente**.

    e. Fare clic su **Avanti**.

7. Nella pagina **Guida password temporanea** fare clic su **Crea**.

    ![Creare l'utente test Azure Active Directory][105]  

8. Nella pagina **Guida password temporanea** procedere come segue:

    ![Creare l'utente test Azure Active Directory][106]   
  
    un. Prendere nota del valore della **Nuova Password**.
  
    b. Fare clic su **completa**.   
  
 
### <a name="creating-a-questetra-bpm-suite-test-user"></a>Creazione di un utente di test Questetra BPM famiglia

L'obiettivo di questa sezione consiste nel creare un utente chiamato Britta Simon nella famiglia di prodotti BPM Questetra.

**Per creare un utente chiamato Britta Simon Questetra BPM Suite, procedere come segue:**

1.  Accesso al sito della società Questetra BPM Suite come amministratore.
2.  Passare a **impostazioni di sistema > elenco utenti > Nuovo utente**. 
3.  Nella finestra di dialogo Nuovo utente, eseguire la procedura seguente: 

    ![Creare l'utente di test][300] 

    un. Nella casella di testo **nome** , digitare nome utente di Sandro in Azure Active Directory.

    b. Nella casella di testo **messaggio di posta elettronica** , digitare nome utente di Sandro in Azure Active Directory.

    c. Nella casella di testo **Password** digitare una password.

4.  Fare clic su **Aggiungi nuovo utente**.



### <a name="assigning-the-azure-ad-test-user"></a>L'assegnazione all'utente di test di Azure Active Directory

L'obiettivo di questa sezione è l'attivazione di Britta Simon a usare il single sign-on Azure concedendo utente l'accesso alla famiglia di prodotti BPM Questetra.

![Che cos'è Azure AD Connect][200]

**Per assegnare Britta Simon alla famiglia di prodotti BPM Questetra, procedere come segue:**

1. Nel portale di Azure classico per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Che cos'è Azure AD Connect][201]

2. Nell'elenco delle applicazioni, selezionare **Il gruppo di BPM Questetra**.

    ![Che cos'è Azure AD Connect][205]

1. Nel menu nella parte superiore, fare clic su **utenti**.

    ![Che cos'è Azure AD Connect][202]

1. Nell'elenco di utenti, selezionare **Sandro**.

    ![Che cos'è Azure AD Connect][203]

2. Nella barra degli strumenti nella parte inferiore, fare clic su **Assegna**.

    ![Che cos'è Azure AD Connect][204]



### <a name="testing-single-sign-on"></a>Test Single Sign-On

L'obiettivo di questa sezione è per verificare la Azure Active Directory single sign-on configurazione mediante il pannello di accesso.  
Quando si fa clic sul riquadro Questetra BPM Suite nel Pannello di accesso, è necessario ottenere automaticamente effettuato l'accesso-on all'applicazione Questetra BPM Suite.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_04.png
[5]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_01.png


[8]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_06.png
[9]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_02.png
[10]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_04.png
[12]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_05.png
[13]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_06.png
[14]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_07.png
[15]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_08.png
[16]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_09.png
[17]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_10.png
[18]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_08.png


[100]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_09.png 
[101]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_10.png 
[102]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_11.png 
[103]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_12.png 
[104]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_13.png 
[105]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_14.png 
[106]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_15.png 


[200]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_16.png 
[201]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_17.png 
[202]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_18.png
[203]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_19.png
[204]: ./media/active-directory-saas-questetra-bpm-suite/tutorial_general_20.png
[205]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_12.png

[300]: ./media/active-directory-saas-questetra-bpm-suite/questera_bpm_suite_11.png 