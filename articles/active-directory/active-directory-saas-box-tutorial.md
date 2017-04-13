<properties 
    pageTitle="Esercitazione: Azure Active Directory casella integrazione con | Microsoft Azure" 
    description="Ecco come usare con Azure Active Directory per abilitare il single sign-on, il provisioning automatico e altro." 
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
    ms.date="09/29/2016" 
    ms.author="jeedes" />




#<a name="tutorial-azure-active-directory-integration-with-box"></a>Esercitazione: Azure Active Directory casella integrazione con


  
L'obiettivo di questa esercitazione è sufficiente visualizzare l'integrazione di Azure e nella casella.  
Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure
-   Un tenant di test nella casella
  
Al termine di questa esercitazione, gli utenti di Azure Active Directory che è stato assegnato a casella sarà possibile eseguire l'accesso singolo all'applicazione del sito di società di casella (servizio provider avviato accesso) o usando l' [Introduzione al pannello di accesso](active-directory-saas-access-panel-introduction.md).
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1.  Attivare l'integrazione di applicazione per la casella
2.  Configurare il single sign-on
3.  Configurazione utente e il provisioning di gruppo
4.  Assegnazione di utenti

![Scenario:] (./media/active-directory-saas-box-tutorial/IC769537.png "Scenario:")



##<a name="enabling-the-application-integration-for-box"></a>Attivare l'integrazione di applicazione per la casella
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazione per la casella.

###<a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>Per attivare l'integrazione di applicazione per la casella, procedere come segue:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-box-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-box-tutorial/IC749321.png "Aggiungi applicazione")

5.  Nella finestra di dialogo **che cosa si vuole eseguire** , fare clic su **Aggiungi un'applicazione dalla raccolta**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-box-tutorial/IC749322.png "Aggiungere un'applicazione al gallerry")

6.  Nella **casella Cerca**digitare **casella**.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-box-tutorial/IC701023.png "Raccolta dell'applicazione")

7.  Nel riquadro dei risultati, selezionare **casella**e quindi fare clic su **completa** per aggiungere l'applicazione.

    ![Casella] (./media/active-directory-saas-box-tutorial/IC701024.png "Casella")



##<a name="configuring-single-sign-on"></a>Configurare il single sign-on
  
L'obiettivo di questa sezione è strutturare come consentire agli utenti di eseguire l'autenticazione alla casella con il proprio account in Azure Active Directory utilizzando la federazione in base al protocollo SAML. Come parte di questa procedura, è necessario caricare metadati Box.com.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1.  Nel portale di classica Azure, nella pagina integrazione applicazione **casella** , fare clic su **Configura il single sign-on** per aprire la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-box-tutorial/IC769538.png "Configurare il single sign-on")

2.  Nella pagina **specificare come agli utenti di accedere alla casella** , selezionare **Microsoft Azure Active Directory Single Sign-On**e quindi fare clic su **Avanti**.

    ![Configurare il single sign-on] (./media/active-directory-saas-box-tutorial/IC769539.png "Configurare il single sign-on")

3.  Nella pagina **Configura URL App** nella casella **URL Tenant casella** di testo digitare l'URL di tenant casella (ad esempio: https://<mydomainname>. box.com), quindi scegliere **Avanti**.

    ![Configurare URL di app] (./media/active-directory-saas-box-tutorial/IC669826.png "Configurare URL di app")

4.  Nella pagina **Configura il single sign-on nella casella** per scaricare i metadati, fare clic su **Download dei metadati**, quindi scegliere il file di dati in locale nel computer in uso.

    ![Configurare il single sign-on] (./media/active-directory-saas-box-tutorial/IC669824.png "Configurare il single sign-on")

5.  Inoltra del file di metadati alla casella il team di supporto. Le esigenze del team di supporto Configura single sign-on per è.

6.  Selezionare la conferma di single sign-on configurazione e quindi fare clic su **completa** per chiudere la finestra di dialogo **Configura Single Sign-On** .

    ![Configurare il single sign-on] (./media/active-directory-saas-box-tutorial/IC769540.png "Configurare il single sign-on")
##<a name="configuring-user-provisioning"></a>Configurare il provisioning dell'utente
  
L'obiettivo di questa sezione è viene illustrato come attivare il provisioning degli account utente di Active Directory alla casella.

###<a name="to-configure-single-sign-on-perform-the-following-steps"></a>Per configurare il single sign-on, procedere come segue:

1. Nel portale di classica Azure, nella pagina integrazione applicazione **casella** , fare clic su **Configura il provisioning dell'utente** per aprire la finestra di dialogo **Configura il Provisioning dell'utente** . 

    ![Abilitare il provisioning automatico degli utenti] (./media/active-directory-saas-box-tutorial/IC769541.png "Abilitare il provisioning automatico degli utenti")

2. Nella pagina di finestra di dialogo **Abilita provisioning alla casella dell'utente** fare clic su **Attiva il provisioning dell'utente**. 

    ![Abilitare il provisioning automatico degli utenti] (./media/active-directory-saas-box-tutorial/IC769544.png "Abilitare il provisioning automatico degli utenti")

3. Nella pagina **Registra per concedere l'accesso alla casella** specificare le credenziali necessarie e quindi fare clic su **autorizzazione**. 

    ![Abilitare il provisioning automatico degli utenti] (./media/active-directory-saas-box-tutorial/IC769546.png "Abilitare il provisioning automatico degli utenti")


4. Fare clic su **Concedi accesso alla casella** per autorizzare questa operazione e tornare al portale di classica Azure. 

    ![Abilitare il provisioning automatico degli utenti] (./media/active-directory-saas-box-tutorial/IC769549.png "Abilitare il provisioning automatico degli utenti")


5. Nella pagina **Opzioni di Provisioning** le caselle di controllo **tipi di oggetto alla disposizione** consente di selezionare se non vengono effettuato il provisioning di raggruppare oggetti alla casella oltre gli oggetti utente.  Per ulteriori informazioni, vedere "Sezione assegnazione utenti e gruppi" sotto.


6. Per completare la configurazione, fare clic sul pulsante completato. 

    ![Abilitare il provisioning automatico degli utenti] (./media/active-directory-saas-box-tutorial/IC769551.png "Abilitare il provisioning automatico degli utenti")



##<a name="assigning-a-test-user"></a>Assegnazione di un utente di test
  
Per verificare la configurazione, è necessario concedere agli utenti di Azure Active Directory per consentire l'uso di accesso dell'applicazione mediante l'assegnazione.

###<a name="to-assign-users-to-box-perform-the-following-steps"></a>Per assegnare gli utenti alla casella, procedere come segue:

1. Nel portale di classica Azure, creare un account di prova.

2. Nella pagina integrazione applicazione **casella **, fare clic su **assegnare agli utenti**. 

    ![Assegnare agli utenti] (./media/active-directory-saas-box-tutorial/IC769552.png "Assegnare agli utenti")

3.  Selezionare l'utente test, fare clic su **Assegna**e quindi fare clic su **Sì** per confermare l'assegnazione. 

    ![Sì] (./media/active-directory-saas-box-tutorial/IC767830.png "Sì")
  
È ora necessario attendere 10 minuti e verificare che l'account è stato sincronizzato alla casella.

Come primo passaggio di verifica, è possibile verificare lo stato di provisioning, facendo clic su Dashboard d nella pagina di integrazione casella applicazione nel portale di classica Azure.

![Dashboard] (./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

Un utente completato il provisioning di ciclo è indicato da uno stato correlato:

![Stato di integrazione] (./media/active-directory-saas-box-tutorial/IC769555.png "Stato di integrazione")


Nel tenant di casella utenti sincronizzati sono elencati sotto **Gestiti utenti** nella **Console di amministrazione**.

![Stato di integrazione] (./media/active-directory-saas-box-tutorial/IC769556.png "Stato di integrazione")


##<a name="assigning-users-and-groups"></a>Assegnazione di utenti e gruppi

Il **casella > utenti e gruppi** scheda nel portale di classica Azure consente di specificare quali utenti e gruppi devono essere concesso l'accesso alla casella. Assegnazione di un utente o gruppo fa sì che vengano eseguite le operazioni seguenti:

* Azure Active Directory consente all'utente assegnata (tramite l'appartenenza ai gruppi o un'assegnazione diretta) per eseguire l'autenticazione alla casella. Se un utente non è assegnato, Azure Active Directory non consente di accedere alla casella e restituirà un errore nella pagina di accesso di Azure Active Directory.

* Il riquadro di un'app per la casella viene aggiunto dell'utente [applicazione di avvio](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

* Se il provisioning automatico è attivato, gli utenti assegnati e/o gruppi vengono aggiunti alla coda di provisioning per eseguire il provisioning automatico.

    * Se solo gli oggetti utente sono stati configurati per eseguire il provisioning, quindi tutti gli utenti assegnati direttamente nella coda di provisioning e tutti gli utenti che appartengono a tutti i gruppi assegnati verranno inseriti nella coda di provisioning. 
    
    * Se sono stati configurati Raggruppa oggetti per eseguire il provisioning, quindi tutti gli oggetti gruppo assegnate vengono effettuato il provisioning alla casella, come tutti gli utenti che appartengono a questi gruppi. Gruppi e utente vengono mantenute dopo la scrittura alla casella.
    
È possibile utilizzare il **attributi > Single Sign-On** scheda per configurare quali attributi utente (o delle attestazioni) sono presentate alla casella durante l'autenticazione basata su SAML e **attributi > Provisioning** scheda per configurare come utenti e gruppi attributi del flusso da Azure Active Directory alla casella durante il provisioning di operazioni. Vedere le risorse sotto per ulteriori informazioni.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Personalizzazione delle attestazioni rilasciate in token SAML](active-directory-saml-claims-customization.md)
* [Provisioning: Personalizzare i mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md)
* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
