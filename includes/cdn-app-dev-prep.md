## <a name="prerequisites"></a>Prerequisiti di

È possibile scrivere codice per la gestione di rete CDN, è necessario eseguire alcune operazioni di preparazione per attivare il codice interagire con Gestione risorse Azure.  A questo scopo è necessario:

* Creare un gruppo di risorse per contenere il profilo CDN che viene creata in questa esercitazione
* Configurare Azure Active Directory per fornire l'autenticazione per l'applicazione
* Applicare le autorizzazioni per il gruppo di risorse in modo che solo gli utenti autorizzati dal nostro tenant di Azure Active Directory è possono interagire con il profilo di rete CDN

### <a name="creating-the-resource-group"></a>Creazione di un gruppo di risorse

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic sul pulsante **Nuovo** in alto a sinistra e quindi **Gestione**e **Gruppo di risorse**.
    
    ![Creare un nuovo gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)

3. Chiamare il gruppo di risorse *CdnConsoleTutorial*.  Selezionare l'abbonamento e scegliere un percorso più vicino.  Se si desidera, è possibile fare clic la casella di controllo **Aggiungi a dashboard** per aggiungere il gruppo di risorse al dashboard nel portale.  Questo renderà più facilmente in seguito.  Dopo aver completato le selezioni, fare clic su **Crea**.

    ![Assegnare un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)

4. Una volta creato il gruppo di risorse, se non si è bloccarla ai dashboard, è possibile trovare facendo clic su **Sfoglia**e quindi **Gruppi di risorse**.  Fare clic sul gruppo di risorse per aprirlo.  Prendere nota dell' **ID abbonamento**.  Sarà necessario utilizzarlo in un secondo momento.

    ![Assegnare un nome al gruppo di risorse](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>Creazione dell'applicazione di Azure Active Directory e l'applicazione delle autorizzazioni

Esistono due metodi per l'autenticazione di app con Azure Active Directory: singoli utenti o un'entità servizio. Un servizio principale è simile a un account del servizio di Windows.  Invece di concessione di autorizzazioni per interagire con i profili di rete CDN un utente specifico, è invece concedere le autorizzazioni per il servizio principale.  Identità di servizio vengono in genere utilizzate per i processi automatizzati non interattivo.  Anche se in questa esercitazione è la scrittura di un'app console interattiva, viene illustrato l'approccio principale del servizio.

Creazione di un servizio principale è costituito da diversi passaggi, compresa la creazione di un'applicazione di Azure Active Directory.  A tale scopo, verranno [seguire questa esercitazione](../articles/resource-group-create-service-principal-portal.md).

> [AZURE.IMPORTANT] Assicurarsi di seguire tutti i passaggi dell' [esercitazione collegate](../articles/resource-group-create-service-principal-portal.md).  È *fondamentale* di completarla esattamente come descritto.  Annotare l' **ID tenant**, **il nome di dominio tenant** (in genere un *. onmicrosoft.com* dominio a meno che non è stato specificato un dominio personalizzato), **ID client**e **chiave di autenticazione client**, come occorre sono disponibili più avanti.  Essere prestare attenzione a proteggere **l'ID client** e la **chiave di autenticazione client**, come le credenziali possono essere utilizzate da tutti gli utenti di eseguire operazioni come principale del servizio. 
>   
> Quando viene visualizzato con il passaggio denominato [Configura multi-tenant applicazione](../articles/resource-group-create-service-principal-portal.md#configure-multi-tenant-application), selezionare **No**.
> 
> Quando viene visualizzato con il passaggio [Assegna un'applicazione di ruolo](../articles/resource-group-create-service-principal-portal.md#assign-application-to-role), utilizzare il gruppo di risorse creato in precedenza, *CdnConsoleTutorial*, ma anziché ruolo **lettore** assegnare il ruolo di **Collaboratore profilo CDN** .  Dopo aver assegnato il ruolo di **Collaboratore profilo CDN** l'applicazione per il gruppo di risorse, tornare in questa esercitazione. 

Dopo aver creato il servizio principale e il ruolo di **Collaboratore profilo CDN** , e **l'utenti** per il gruppo di risorse dovrebbe essere simile alla seguente.

![Blade utenti](./media/cdn-app-dev-prep/cdn-service-principal-include.png)


### <a name="interactive-user-authentication"></a>Autenticazione utente interattivo

Se invece di un'identità di servizio, invece si desidera utilizzare l'autenticazione utente interattivi, il processo è molto simile a quello per un'identità di servizio.  Infatti, è necessario seguire la stessa procedura ma apportare alcune modifiche minori.

> [AZURE.IMPORTANT] Seguire questi passaggi successivi solo se si sceglie di utilizzare l'autenticazione utente anziché un servizio principale.

1. Durante la creazione dell'applicazione, invece di **Applicazione Web**, scegliere **applicazione nativa**. 
    
    ![Applicazione nativa](./media/cdn-app-dev-prep/cdn-native-application-include.png)
    
2. Nella pagina successiva, verrà richiesto per un **reindirizzare URI**.  URI non convalidati ma ricordarsi è stata immessa.  È necessario in un secondo momento. 

3. Non è necessario per creare una **chiave di autenticazione client**.

4. Invece di assegnazione di un'identità di servizio per il ruolo di **Collaboratore profilo CDN** , verranno assegnare singoli utenti o gruppi.  In questo esempio, è possibile vedere che è stato assegnato *CDN Demo utente* per il ruolo di **Collaboratore profilo CDN** .  
    
    ![Accesso degli utenti singoli](./media/cdn-app-dev-prep/cdn-aad-user-include.png)

