<properties 
    pageTitle="Esercitazione: Integrazione di Azure Active Directory con Dropbox per le aziende | Microsoft Azure" 
    description="Informazioni su come utilizzare Dropbox for Business con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="08/16/2016" 
    ms.author="jeedes" />

#<a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Esercitazione: Integrazione di Azure Active Directory con Dropbox for Business
  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e Dropbox per le aziende.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di test in Dropbox for Business
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory è stato assegnato a Dropbox per Business sarà in grado di eseguire l'accesso singolo all'applicazione la Dropbox for Business società sito (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione delle applicazioni dropbox for Business
2.  Configurare il single sign-on
3.  Configurare il provisioning dell'utente
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769508.png "Scenario:")



##<a name="enabling-the-application-integration-for-dropbox-for-business"></a>Attivare l'integrazione delle applicazioni dropbox for Business
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione delle applicazioni dropbox for Business.

###<a name="to-enable-the-application-integration-for-dropbox-for-business-perform-the-following-steps"></a>Per attivare l'integrazione delle applicazioni dropbox for Business, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **Dropbox per le aziende**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701010.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati selezionare **Dropbox for Business**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Dropbox for Business] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC701011.png "Dropbox for Business")

##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione in Dropbox for Business con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML.

Come parte di questa procedura, è necessario caricare un certificato codificato base 64 il Dropbox per tenant Business. Se non ha familiarità con questa procedura, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **Dropbox for Business** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749323.png "Configurare il single sign-on")

2.  Nella pagina **specificare come gli utenti di accedere al Dropbox for Business** selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749327.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** , procedere come segue:

    un. Sign-on per la raccolta per tenant business. 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769509.png "Configurare il single sign-on")

    b. Nel riquadro di spostamento sul lato sinistro fare clic su **Console di amministrazione**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769510.png "Configurare il single sign-on")

    c. Nella **Console di amministrazione**, fare clic su **autenticazione** nel riquadro di spostamento sinistro. 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769511.png "Configurare il single sign-on")

    d. Nella sezione **servizio Single sign-on** , selezionare **Attiva il single sign-on**e quindi fare clic su **altro** per espandere la sezione.  

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769512.png "Configurare il single sign-on")

    e. Copiare l'URL accanto agli **utenti possano accedere immettendo l'indirizzo di posta elettronica o possono accedere direttamente**. 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769513.png "Configurare il single sign-on")

    f. Nel portale di Azure classico incollare l'URL nella casella di testo a URL **effettuarlo DropBox per le aziende** . 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769514.png "Configurare il single sign-on")  



4. Nella pagina **Configura il single sign-on in Dropbox for Business** , fare clic su **Scarica certificato**e quindi salvare il file di certificato nel computer in uso.  

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769515.png "Configurare il single sign-on")


5. Nel Dropbox per tenant Business, nella sezione **servizio Single sign-on** della pagina di **autenticazione** , procedere come segue: 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769516.png "Configurare il single sign-on")

    un. Fare clic su **obbligatorio**.

    b. Nel portale di classica Azure, nella pagina finestra di dialogo **Configura il single sign-on in Dropbox for Business** copiare il valore di **accesso URL della pagina** e quindi incollarla nella casella di testo **URL l'accesso** .


    c. Creare un file **con codifica Base 64** del certificato scaricata. 

    > [AZURE.TIP] Per ulteriori informazioni, vedere [come convertire un certificato binario in un file di testo](http://youtu.be/PlgrzUZ-Y1o).


    d. Fare clic sul pulsante **"Scegli certificato"** e quindi individuare il **file di certificato codificato base 64**.


    e. Fare clic su pulsante **"Salva le modifiche"** per completare la configurazione del DropBox per tenant Business.


6. Nel portale di classica Azure, selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** . 

    ![Configurare il single sign-on] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC749329.png "Configurare il single sign-on")



##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory per Dropbox for Business dell'utente.


### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Per configurare il provisioning dell'utente, procedere come segue:

1. Nel portale di classica Azure, nella pagina integrazione applicazione **Dropbox for Business** , fare clic su **Configura il provisioning dell'utente** per aprire la finestra di dialogo **Configura il Provisioning dell'utente** .

2. Nell'Abilita il provisioning dell'utente in DropBox per pagina Business, fare clic su Abilita il provisioning dell'utente per aprire Accedi a Dropbox per creare un collegamento con finestra di dialogo di Azure Active Directory.  

    ![Il provisioning dell'utente] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769517.png "Il provisioning dell'utente")

3. Nella finestra di dialogo **Accedi a Dropbox per creare un collegamento con Azure Active Directory** , accedere alla Dropbox per tenant Business. 

    ![Il provisioning dell'utente] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769518.png "Il provisioning dell'utente")



4. Fare clic su **Consenti** per concedere Azure Active Directory per accedere ai Dropbox. 

    ![Il provisioning dell'utente] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769519.png "Il provisioning dell'utente")



5. Per completare la configurazione, fare clic sul pulsante di **completamento** .  

    ![Il provisioning dell'utente] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769520.png "Il provisioning dell'utente")




##<a name="assigning-users"></a>Assegnazione di utenti
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-dropbox-for-business-perform-the-following-steps"></a>Per assegnare gli utenti in Dropbox for Business, procedere come segue:

1.  Nel portale di classica Azure, creare un account di prova.

2.  Nella pagina integrazione applicazione **Dropbox for Business **, fare clic su **assegnare agli utenti**.

    ![Assegnare agli utenti] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769521.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione.

    ![Sì] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC767830.png "Sì")
  


È ora necessario attendere 10 minuti e verificare che l'account è stato sincronizzato in Dropbox per le aziende.

Come primo passaggio di verifica, è possibile verificare lo stato di provisioning, facendo clic su **Dashboard** nella pagina delle integrazione di **Dropbox for Business** applicazione nel portale di classica Azure.

![Assegnare agli utenti] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769522.png "Assegnare agli utenti")


Un utente completato il provisioning di ciclo è indicato da uno stato correlato.

![Assegnare agli utenti] (./media/active-directory-saas-dropboxforbusiness-tutorial/IC769523.png "Assegnare agli utenti")


Se si desidera verificare le impostazioni single sign-on, aprire il pannello di accesso.
Per ulteriori informazioni sul pannello di accesso, vedere [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).




## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)