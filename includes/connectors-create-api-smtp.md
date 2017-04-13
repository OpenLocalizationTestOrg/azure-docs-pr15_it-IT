### <a name="prerequisites"></a>Prerequisiti

- Un account [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol)  


Prima di poter usare l'account SMTP in un'app di logica, è necessario autorizzare l'app logica per connettersi al proprio account SMTP. Per tale operazione può essere facilmente all'interno dell'applicazione di logica nel portale di Azure.  

Ecco i passaggi per autorizzare l'app logica per connettersi al proprio account SMTP, vedere:  
1. Per creare una connessione a SMTP, nella finestra di progettazione di app logica, selezionare **Mostra Microsoft API gestite** nell'elenco a discesa, quindi immettere *SMTP* nella casella di ricerca. Selezionare il trigger o l'azione desiderata da usare:  
![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Se è stata creata tutte le connessioni SMTP prima, verrà viene richiesto di specificare le credenziali di SMTP. Queste credenziali verranno usate per autorizzare l'app logica a cui connettersi e accedere ai dati del proprio account SMTP, vedere:  
![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Si noti la connessione è stata creata, l'ora gratuito procedere con gli altri passaggi nell'app logica:  
 ![](./media/connectors-create-api-smtp/smtp-3.png)  

