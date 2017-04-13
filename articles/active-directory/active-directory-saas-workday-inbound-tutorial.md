<properties 
    pageTitle="Esercitazione: Configurazione lavorativo per la sincronizzazione in ingresso | Microsoft Azure" 
    description="Informazioni su come usare lavorativo come origine dei dati dell'identità di Azure Active Directory." 
    services="active-directory" 
    authors="MarkusVi"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/10/2016" 
    ms.author="markvi" />


#<a name="tutorial-configuring-workday-for-inbound-synchronization"></a>Esercitazione: Configurazione lavorativo per la sincronizzazione in ingresso


L'obiettivo di questa esercitazione è sufficiente visualizzare i passaggi da eseguire in lavorativo e Azure Active Directory per importare gli utenti da lavorativo a Azure Active Directory. 

Lo scenario illustrato in questa esercitazione si presuppone che sia già gli elementi seguenti:

-   Abbonamento valido a Azure Active Directory Premium
-   Un tenant lavorativo
  
Lo scenario illustrato in questa esercitazione include i blocchi predefiniti seguenti:

1. Attivare l'integrazione di applicazioni per giorno lavorativo 


2. Creazione di un utente di sistema di integrazione 


3. Creazione di un gruppo di sicurezza 


4. L'utente di sistema di integrazione l'assegnazione al gruppo di sicurezza 


5. Configurazione delle opzioni di gruppo di sicurezza 


6. Attivare le modifiche dei criteri di sicurezza 


7. Configurazione importazione utente in Active Directory Azure 



##<a name="enabling-the-application-integration-for-workday"></a>Attivare l'integrazione di applicazioni per giorno lavorativo
  
L'obiettivo di questa sezione è viene illustrato come attivare l'integrazione di applicazioni per giorno lavorativo.

### <a name="steps"></a>Procedura:

1.  Nel portale di classica Azure, nel riquadro di spostamento sinistra, fare clic su **Active Directory**.

    ![Active Directory] (./media/active-directory-saas-workday-inbound-tutorial/IC700993.png "Active Directory")

2.  Nell'elenco di **Directory** , selezionare la directory per il quale si desidera attivare l'integrazione di directory.

3.  Per aprire la visualizzazione di applicazioni, nella visualizzazione directory, fare clic su **applicazioni** nel menu superiore.

    ![Applicazioni] (./media/active-directory-saas-workday-inbound-tutorial/IC700994.png "Applicazioni")

4.  Fare clic su **Aggiungi** nella parte inferiore della pagina.

    ![Aggiungi applicazione] (./media/active-directory-saas-workday-inbound-tutorial/IC749321.png "Aggiungi applicazione")

  
5. Nella casella Cerca digitare **lavorativo**.

    ![Aggiungere un'applicazione al gallerry] (./media/active-directory-saas-workday-inbound-tutorial/IC701021.png "Aggiungere un'applicazione al gallerry")

6. Nel riquadro dei risultati selezionare lavorativo e quindi fare clic su completa per aggiungere l'applicazione.

    ![Raccolta dell'applicazione] (./media/active-directory-saas-workday-inbound-tutorial/IC701022.png "Raccolta dell'applicazione")





## <a name="creating-an-integration-system-user"></a>Creazione di un utente di sistema di integrazione

### <a name="steps"></a>Procedura:


1. Nell' **Area di lavoro lavorativo**, immettere creare utente nella casella di ricerca e quindi fare clic su **Crea integrazione con sistema utente**. 

    ![Crea utente] (./media/active-directory-saas-workday-inbound-tutorial/IC750979.png "Crea utente")



2. Completare l'attività **Crea integrazione con sistema utente** specificando un nome utente e password per un nuovo utente di sistema di integrazione.  Lasciare la richiedono nuova Password al successivo accesso opzione non è selezionata, perché questo utente accedono a livello di programmazione. Lasciare il Timeout di sessione in minuti con il relativo valore predefinito 0, che impedirà sessioni dell'utente di timeout in modo anomalo. 

    ![Creare integrazione con sistema utente] (./media/active-directory-saas-workday-inbound-tutorial/IC750980.png "Creare integrazione con sistema utente")
 




## <a name="creating-a-security-group"></a>Creazione di un gruppo di sicurezza

Per lo scenario illustrato in questa esercitazione, è necessario creare un gruppo di sicurezza del sistema integrazione senza vincoli e assegnare l'utente.

### <a name="steps"></a>Procedura:

1. Immettere Crea gruppo di sicurezza nella casella di ricerca e quindi fare clic su **Crea gruppo di sicurezza**. 

    ![Gruppo CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750981.png "Gruppo CreateSecurity")
 

2. Completare l'attività Crea gruppo di sicurezza.  Selezionare gruppo di sicurezza del sistema integrazione, ovvero non vincolato nell'elenco a discesa tipo di gruppo di sicurezza tenant, per creare un gruppo di sicurezza a cui i membri vengano aggiunti in modo esplicito. 

    ![Gruppo CreateSecurity] (./media/active-directory-saas-workday-inbound-tutorial/IC750982.png "Gruppo CreateSecurity")
 



## <a name="assigning-the-integration-system-user-to-the-security-group"></a>L'utente di sistema di integrazione l'assegnazione al gruppo di sicurezza

### <a name="steps"></a>Procedura:


1. Immettere il gruppo di sicurezza di modifica nella casella di ricerca e quindi fare clic su **Modifica gruppo di sicurezza**. 

    ![Modifica gruppo di sicurezza] (./media/active-directory-saas-workday-inbound-tutorial/IC750983.png "Modifica gruppo di sicurezza")
 
 

2. Cercare e selezionare il nuovo gruppo di sicurezza di integrazione in base al nome. 

    ![Modifica gruppo di sicurezza] (./media/active-directory-saas-workday-inbound-tutorial/IC750984.png "Modifica gruppo di sicurezza")

 

3. Aggiungere il nuovo utente di sistema di integrazione al nuovo gruppo di sicurezza. 

    ![Gruppo di sicurezza del sistema] (./media/active-directory-saas-workday-inbound-tutorial/IC750985.png "Gruppo di sicurezza del sistema")  




## <a name="configuring-security-group-options"></a>Configurazione delle opzioni di gruppo di sicurezza

In questo passaggio si concedono a nuove autorizzazioni di gruppo di sicurezza per **recuperare** e **spostare** le operazioni sugli oggetti protetti dai seguenti criteri di sicurezza di dominio:

- Il Provisioning di Account esterno

- Lavoro dati: Report di lavoro pubblico

- Dati di lavoro: Tutte le posizioni

- Dati di lavoro: Corrente personale di informazioni

- Dati di lavoro: Business titolo nel profilo di lavoro

 
### <a name="steps"></a>Procedura:

1. Immettere i criteri di sicurezza dominio nella casella di ricerca e quindi fare clic sul collegamento, criteri di sicurezza di dominio per Area funzionale.  

    ![Criteri di sicurezza di dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750986.png "Criteri di sicurezza di dominio")  
 

2. Ricerca di sistema e selezionare l'area funzionale di **sistema** .  Fare clic su **OK**.  

    ![Criteri di sicurezza di dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750987.png "Criteri di sicurezza di dominio")  


3. Nell'elenco dei criteri di sicurezza per l'area funzionale di sistema espandere amministrazione della protezione e selezionare i criteri di sicurezza del dominio, del provisioning degli Account esterni.  

    ![Criteri di sicurezza di dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750988.png "Criteri di sicurezza di dominio")  


4. Fare clic su **Modifica autorizzazioni**e quindi, nella pagina di finestra di dialogo **Modifica autorizzazioni**, aggiungere il nuovo gruppo di sicurezza all'elenco di gruppi di sicurezza con autorizzazioni di **accedere** e **inserire** integrazione. 

    ![Autorizzazione di modifica] (./media/active-directory-saas-workday-inbound-tutorial/IC750989.png "Autorizzazione di modifica")  

 

5. Ripetere il passaggio 1, in precedenza, per tornare alla schermata per la selezione di aree funzionali, e in questo caso, cercare il personale, selezionare l'area funzionale al personale e fare clic su **OK**.

    ![Criteri di sicurezza di dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750990.png "Criteri di sicurezza di dominio")  
 

6. Nell'elenco dei criteri di sicurezza per l'area funzionale staff, espandere i dati di lavoro: personale e ripetere il passaggio 4 precedenti per ciascuno di essi rimanente criteri di sicurezza:

     - Lavoro dati: Report di lavoro pubblico

     - Dati di lavoro: Tutte le posizioni

     - Dati di lavoro: Corrente personale di informazioni

     - Dati di lavoro: Business titolo nel profilo di lavoro


    ![Criteri di sicurezza di dominio] (./media/active-directory-saas-workday-inbound-tutorial/IC750991.png "Criteri di sicurezza di dominio")  







## <a name="activating-security-policy-changes"></a>Attivare le modifiche dei criteri di sicurezza

### <a name="steps"></a>Procedura:

1. Immettere attivare nella casella di ricerca e quindi fare clic sul collegamento, attivare le modifiche dei criteri di sicurezza in sospeso. 

    ![Attivare] (./media/active-directory-saas-workday-inbound-tutorial/IC750992.png "Attivare") 
 

2. Iniziare l'attività di attivare le modifiche dei criteri di sicurezza in sospeso tramite l'immissione di un commento per motivi di controllo e quindi fare clic su **OK**. 

    ![Attivare in attesa di sicurezza] (./media/active-directory-saas-workday-inbound-tutorial/IC750993.png "Attivare in attesa di sicurezza")   
 

3. Completare l'attività nella schermata successiva selezionando la casella di controllo etichettato conferma e quindi fare clic su **OK**. 

    ![Attivare in attesa di sicurezza] (./media/active-directory-saas-workday-inbound-tutorial/IC750994.png "Attivare in attesa di sicurezza")  





## <a name="configuring-user-import-in-azure-ad"></a>Configurazione importazione utente in Active Directory Azure

L'obiettivo di questa sezione è strutturare sulla configurazione di Azure Active Directory per importare gli utenti da lavorativo.


### <a name="steps"></a>Procedura:


1. Nella pagina integrazione applicazione **lavorativo** , fare clic su **Importazione configurazione utente** per aprire la finestra di dialogo **Configura il Provisioning** .


2. Nella pagina **impostazioni e le credenziali di amministratore** , eseguire le operazioni seguenti e quindi fare clic su **Avanti**: 

    ![Impostazioni e le credenziali di amministratore] (./media/active-directory-saas-workday-inbound-tutorial/IC750995.png "Impostazioni e le credenziali di amministratore")  
 
    un. Digitare il nome dell'utente è stato creato in creazione di testo Nome utente amministratore lavorativo una sezione di integrazione con sistema utente.

    b. Nella casella password di amministratore lavorativo testo, digitare la password dell'utente è stato creato in creazione una sezione di integrazione con sistema utente.

    c. Nella casella di testo URL tenant lavorativo, digitare l'URL o il tenant lavorativo.


3. Nella pagina **Verifica connessione** fare clic su **Avvia test** per confermare la connettività e quindi fare clic su **Avanti**. 

    ![Test connessione] (./media/active-directory-saas-workday-inbound-tutorial/IC750996.png "Test connessione")  
 

4. Nella pagina delle opzioni di **Provisioning** , fare clic su **Avanti**. 

    ![Opzioni di provisioning] (./media/active-directory-saas-workday-inbound-tutorial/IC750997.png "Opzioni di provisioning")


5. Nella finestra di dialogo **avviare il provisioning** , fare clic su **completa**. 

    ![Avviare il provisioning] (./media/active-directory-saas-workday-inbound-tutorial/IC750998.png "Avviare il provisioning")
 

È possibile passare alla sezione **utenti** e controllare se l'utente lavorativo è stata importata.



## <a name="additional-resources"></a>Risorse aggiuntive

* [Elenco di esercitazioni su come integrare App SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Che cos'è l'accesso alle applicazioni e single sign-on con Azure Active Directory?](active-directory-appssoaccess-whatis.md)
