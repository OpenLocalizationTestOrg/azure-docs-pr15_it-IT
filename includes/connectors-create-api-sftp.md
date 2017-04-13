### <a name="prerequisites"></a>Prerequisiti

- Un account [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol)  


Prima di poter usare l'account SFTP in un'app di logica, è necessario autorizzare l'app logica per connettersi al proprio account SFTP. Per tale operazione può essere facilmente all'interno dell'applicazione di logica nel portale di Azure.  

Ecco i passaggi per autorizzare l'app logica per connettersi al proprio account SFTP:  
1. Per creare una connessione a SFTP, nella finestra di progettazione di app logica, selezionare **Mostra Microsoft API gestite** nell'elenco a discesa, quindi immettere *SFTP* nella casella di ricerca. Selezionare il trigger **SFTP - quando viene aggiunto o modificato un file** :  
![Immagine di connessione in linea SFTP 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Se è stata creata tutte le connessioni a SFTP prima, verrà viene richiesto di specificare le credenziali SFTP. Queste credenziali verranno usate per autorizzare l'app logica a cui connettersi e accedere ai dati del proprio account SFTP:  
![Immagine di connessione in linea SFTP 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Si noti la connessione è stata creata, l'ora gratuito procedere con gli altri passaggi nell'app logica:   
 ![Immagine di connessione online SFTP 3](./media/connectors-create-api-sftp/sftp-3.png) 
