#### <a name="prerequisites"></a>Prerequisiti
- Un account Azure; è possibile creare un [account gratuito](https://azure.microsoft.com/free)
- Un account di [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Prima di utilizzare l'account di Dynamics in un'app di logica, autorizzare l'app di logica per connettersi al proprio account CRM Online. È possibile eseguire questa operazione facilmente all'interno dell'applicazione di logica nel portale di Azure. 

Autorizzare l'app logica per connettersi al proprio account CRM Online procedendo come segue:

1. Creare un'app di logica. Nella finestra di progettazione logica App, selezionare **Mostra Microsoft API gestite** nell'elenco a discesa e quindi immettere "dynamics" nella casella di ricerca. Selezionare uno dei trigger o azioni:  
  ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se è stata creata in precedenza tutte le connessioni a Dynamics, viene chiesto di accedere con le credenziali di Dynamics:  
  ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Selezionare **l'accesso**e immettere il nome utente e la password. Selezionare **Accedi**. 

    Queste credenziali vengono utilizzate per autorizzare l'app di logica di connettersi e accedere ai dati nel proprio account Dynamics. 
4. Si noti che la connessione è stata creata. A questo punto, procedere con gli altri passaggi nell'app logica:  
  ![](./media/connectors-create-api-crmonline/dynamics-properties.png)
