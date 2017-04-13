<properties
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con DocuSign | Microsoft Azure"
    description="Informazioni su come configurare single sign-on tra Azure Active Directory e DocuSign."
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


# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Esercitazione: Integrazione di Azure Active Directory con DocuSign

L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e DocuSign.
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

- Abbonamento valido a Azure
- Un tenant DocuSign



Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1. [Attivare l'integrazione delle applicazioni per DocuSign](#enabling-the-application-integration-for-docusign) 


2. [Configurare il single sign-on](#configuring-single-sign-on) 


3. [Configurazione del provisioning degli account](#configuring-account-provisioning) 


4. [Assegnazione di utenti](#assigning-users) 

    ![Configurare il single sign-on][0]
 

## <a name="enabling-the-application-integration-for-docusign"></a>Attivare l'integrazione delle applicazioni per DocuSign

L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni per DocuSign.

### <a name="to-enable-the-application-integration-for-docusign-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni per DocuSign, procedere come segue:

1. Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Configurare il single sign-on][1]

2. Nell'elenco di Directory, selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3. Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Configurare il single sign-on][2]

4. Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Applicazioni][3]

5. In elementi di cui si desidera eseguire finestra di dialogo, fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Configurare il single sign-on][4]


6. Nella casella Cerca digitare **DocuSign**.

    ![Configurare il single sign-on][5]

7. Nel riquadro dei risultati, selezionare **DocuSign**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Configurare il single sign-on][6]


## <a name="configuring-single-sign-on"></a>Configurare il single sign-on

L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione a DocuSign con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.


### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1. Nel portale di classica Azure, nella pagina **DocuSign integrazione delle applicazioni** fare clic su **Configura il single sign-on** per aprire la finestra di dialogo Configura Single Sign-On.

    ![Configurare il single sign-on][7]

2. Nella pagina **specificare come gli utenti di accedere al DocuSign** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su Avanti.

    ![Configurare il single sign-on][8]

3. Nella pagina **Configura impostazioni di App** , procedere come segue:

    ![Configurare il single sign-on][61]

    un. Nella casella di testo **URL di accesso** digitare `https://account.docusign.com/*`.  

    b. Nella casella di testo **identificatore** , digitare `https://account.docusign.com/*`.  
   
    c. Fare clic su **Avanti**. 


    > [AZURE.TIP] L'accesso in URL e i valori degli identificatori sono solo segnaposto. Istruzioni per la procedura recuperare i valori effettivi per l'ambiente sono trattate più avanti in questo argomento.
 

4. Nella pagina **Configura il single sign-on in DocuSign** fare clic su **Scarica certificato**e quindi salvare il file di certificato in locale nel computer in uso.

    ![Configurare il single sign-on][10]


5. In una finestra del browser web diverso, accedere al **portale di amministrazione di DocuSign** come amministratore.


6. Nel menu di spostamento a sinistra, fare clic su **domini**.

    ![Configurare il single sign-on][51]

7. Nel riquadro destro fare clic su **Richiedi dominio**.

    ![Configurare il single sign-on][52]

8. Nella finestra di dialogo **Richiedi un dominio** , nella casella di testo **Nome di dominio** , digitare il dominio della società e quindi fare clic su **Richiedi**. Verificare che si verifica il dominio e lo stato attivo.

    ![Configurare il single sign-on][53]

9. Nel menu a sinistra, fare clic su **Provider di identità**  

    ![Configurare il single sign-on][54]

10. Nel riquadro destro fare clic su **Aggiungi Provider di identità**. 
    
    ![Configurare il single sign-on][55]

11. Nella pagina **Impostazioni del Provider di identità** , procedere come segue:

    ![Configurare il single sign-on][56]


    un. Nella casella di testo **nome** digitare un nome univoco per la configurazione. Non usare spazi.

    b. Nel portale di classica Azure, copiare l'URL emittente e quindi incollarla nella casella di testo **Emittente Provider di identità** .

    c. Nel portale di classica Azure, copiare l' **URL di accesso remoto**e quindi incollarla nella casella di testo **URL di accesso del Provider di identità** .

    d. Nel portale di classica Azure, copiare l' **URL di disconnessione remoto**e quindi incollarla nella casella di testo **URL disconnessione del Provider di identità** .

    e. Selezionare **Accesso uso richiesta**.

    f. Come **inviare uso richiesta**, selezionare **POST**.

    g. Come **inviare una richiesta di disconnessione da**, selezionare **POST**. 


12. Nella sezione **Mappatura attributi personalizzati** , scegliere il campo che si desidera eseguire il mapping con Azure Active Directory richiesta. In questo esempio, Richiedi **emailaddress** mappato con il valore di **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Questo è il nome di richiesta di rimborso predefinito da Azure Active Directory per domanda di posta elettronica. 

    > [AZURE.NOTE] Consente di eseguire il mapping all'utente da Azure Active Directory di mapping utente Docusign appropriato **ID utente** . Selezionare il campo appropriato e immettere il valore appropriato in base alle impostazioni dell'organizzazione.

    ![Configurare il single sign-on][57]

13. Nella sezione **Certificato di Provider di identità** fare clic su **Aggiungi certificato**e quindi caricare il certificato scaricato dal portale classica di Azure Active Directory.   

    ![Configurare il single sign-on][58]

14. Fare clic su **Salva**.

15. Nella sezione **Provider di identità** , fare clic su **Azioni**e quindi fare clic sui **punti finali**.   

    ![Configurare il single sign-on][59]



10. Nel portale di classica Azure, tornare alla pagina **Configura impostazioni di App** . 

16. Nel **portale di amministrazione di DocuSign**, nella sezione **Visualizzazione SAML 2.0 endpoint** eseguire, la procedura seguente:

    ![Configurare il single sign-on][60]

    un. Copiare l' **URL del servizio di emittente Provider**e quindi incollarla nella casella di testo **identificatore** nel portale di classica Azure.

    b. Copiare l' **URL di accesso di Provider di servizi**e quindi incollare la casella di testo di **Accesso in URL** nel portale di classica Azure.

    c.  Fare clic su **Chiudi**  


10. Nel portale di classica Azure, fare clic su **Avanti**. 


15. Nel portale di classica Azure, selezionare la **conferma della configurazione Single sign-on**e quindi fare clic su **Avanti**.

    ![Applicazioni][14]

10. Nella pagina di **Conferma Single sign-on** , fare clic su **completa**.

    ![Applicazioni][15]
 

## <a name="configuring-account-provisioning"></a>Configurazione del provisioning degli account

L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per DocuSign dell'utente.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1. Nel **portale classica Azure**, nella pagina **DocuSign integrazione delle applicazioni** fare clic su **Configura account provisioning** per aprire la finestra di dialogo Configura il Provisioning dell'utente.

    ![Configurazione del provisioning degli account][30]

2. Nella pagina **impostazioni e le credenziali di amministratore** per consentire di provisioning automatico dell'utente, specificare le credenziali di un account DocuSign con diritti sufficienti e quindi fare clic su **Avanti**. 

    ![Configurazione del provisioning degli account][31]

3. Nella finestra di dialogo **Verifica connessione** fare clic su **Start test**e su una prova ha esito positivo, fare clic su **Avanti**.

    ![Configurazione del provisioning degli account][32]

3. Nella pagina di **Conferma** , fare clic su **completa**.

    ![Configurazione del provisioning degli account][33]
 

## <a name="assigning-users"></a>Assegnazione di utenti

Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

### <a name="to-assign-users-to-docusign-perform-the-following-steps"></a>Per assegnare gli utenti a DocuSign, procedere come segue:

1. Nel **portale classica Azure**, creare un account di prova.

2. Nella pagina **DocuSign integrazione delle applicazioni** fare clic su **assegnare agli utenti**.

    ![Assegnazione di utenti][40]
 

3. Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Assegnazione di utenti][41]


È ora necessario attendere 10 minuti e verificare che l'account è stata sincronizzata con DocuSign.

Come primo passaggio di verifica, è possibile verificare lo stato di provisioning, facendo clic su Dashboard d nella pagina di integrazione DocuSign applicazione nel portale di classica Azure.

![Assegnazione di utenti][42]

Un utente completato il provisioning di ciclo è indicato da uno stato correlato:

![Assegnazione di utenti][43]


Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso.

Per ulteriori informazioni sul pannello di accesso, vedere Introduzione al pannello di accesso.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_06.png

[14]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_18.png

[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png