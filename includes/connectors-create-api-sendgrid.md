### <a name="prerequisites"></a>Prerequisiti
- Un account [SendGrid](https://www.SendGrid.com/) 

Prima di poter usare l'account SendGrid in un'app di logica, è necessario autorizzare l'app logica per connettersi al proprio account SendGrid. Per tale operazione può essere facilmente all'interno dell'applicazione di logica nel portale di Azure. 

Ecco i passaggi per autorizzare l'app logica per connettersi al proprio account SendGrid:

1. Per creare una connessione a SendGrid, nella finestra di progettazione di app logica, selezionare **Mostra Microsoft API gestite** nell'elenco a discesa, quindi immettere *SendGrid* nella casella di ricerca. Selezionare il trigger o l'azione desiderata da usare:  
  ![SendGrid passaggio 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Se è stata creata tutte le connessioni a SendGrid prima, verrà viene richiesto di specificare le credenziali SendGrid. Queste credenziali verranno utilizzate per autorizzare l'app logica a cui connettersi e accedere ai dati del proprio account SendGrid:  
  ![SendGrid passaggio 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Si noti la connessione è stata creata, l'ora gratuito procedere con gli altri passaggi nell'app logica:  
  ![SendGrid passaggio 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   
