
Per impostazione predefinita, è possano richiamare le API in un back-end App Mobile in modalità anonima. È necessario limitare l'accesso a solo client autenticati.  

+ **Back-end node (tramite portale)** :  
    
    Nelle **Impostazioni**dell'App Mobile fare clic su **Tabelle semplice** e selezionare la tabella. Fare clic su **Modifica autorizzazioni**, selezionare **accesso autenticazione solo** per tutte le autorizzazioni, quindi fare clic su **Salva**. 

+ **Back-end .NET (c#)**:  

    In project server, passare a **controller** > **TodoItemController.cs**. Aggiungere il `[Authorize]` dell'attributo per la classe **TodoItemController** , come indicato di seguito. Per limitare l'accesso solo ai metodi specifici, è anche possibile applicare questo attributo a tali metodi anziché la classe. Pubblicare di nuovo il progetto server.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Back-end node (tramite codice Node)** :  
    
    Per richiedere l'accesso alle tabelle di autenticazione, aggiungere lo script server Node la riga seguente:


        table.access = 'authenticated';

    Per ulteriori informazioni, consultare [come: richiedono l'autenticazione per l'accesso alle tabelle](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Per informazioni su come scaricare il progetto di codice Guida introduttiva del sito, vedere [come: scaricare il progetto di codice Guida introduttiva di back-end Node utilizzando fra](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).

