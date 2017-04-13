

Per connettersi a **SharePoint Online**, è necessario fornire l'identità (nome utente e password, credenziali della smart card, ecc.) in SharePoint Online. Una volta che si è stati autenticati, è possibile procedere per utilizzare il connettore SharePoint Online nell'app logica. 

Nella finestra di progettazione dell'app logica, procedere come segue per accedere a SharePoint per creare la **connessione** per l'utilizzo nell'app logica:

1. Immettere SharePoint nella casella di ricerca e attendere che la ricerca restituire tutti i trigger e azioni correlate in SharePoint Online:   
![Configurazione di SharePoint][1]  
2. Selezionare il trigger di **SharePoint Online - quando viene creato un file**  
3. Selezionare **accedere a SharePoint Online**:   
![Configurazione di SharePoint][2]    
4. Specificare le credenziali di SharePoint per accedere a eseguire l'autenticazione con SharePoint   
![Configurazione di SharePoint][3]     
5. Al termine l'autenticazione, si verrà reindirizzati logica all'applicazione in uso. Ecco fatto, la connessione è stata creata. Il messaggio nella parte inferiore che indica che a questo punto si è connessi a SharePoint.  
![Configurazione di SharePoint][4]  
6. È quindi possibile aggiungere altri eventi e le azioni che è necessario per completare l'app logica.   

[1]: ./media/connectors-create-api-sharepointonline/connectionconfig1.png
[2]: ./media/connectors-create-api-sharepointonline/connectionconfig2.png 
[3]: ./media/connectors-create-api-sharepointonline/connectionconfig3.png
[4]: ./media/connectors-create-api-sharepointonline/connectionconfig4.png
[5]: ./media/connectors-create-api-sharepointonline/connectionconfig5.png
