1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  In Jumpbar, fare clic su **Nuovo**, fare clic su **dati + spazio di archiviazione**e quindi fare clic su **DocumentDB (NoSQL)**.

    ![Schermata del portale di Azure, evidenziazione altri servizi e DocumentDB (NoSQL)](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)  

3. Nella finestra e **nuovo account** , specificare la configurazione desiderata per l'account DocumentDB.

    ![Schermata della stessa e nuovo DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-2.png)

    - Nella casella **ID** immettere un nome per identificare il conto DocumentDB.  Quando l' **ID** viene convalidata, nella casella **ID** viene visualizzato un segno di spunta verde. Il valore di **ID** diventa il nome host all'interno di URI. L' **ID** può contenere solo lettere minuscole lettere, numeri e i '-' caratteri e deve essere compreso tra 3 e 50 caratteri. Si noti che *documents.azure.com* viene aggiunto al nome dell'endpoint che si sceglie, il cui risultato diventa l'endpoint di account DocumentDB.

    - Nella casella **NoSQL API** selezionare **DocumentDB**.  

    - Per l' **abbonamento**, selezionare l'abbonamento Azure che si desidera utilizzare per l'account DocumentDB. Se l'account ha un solo abbonamento, tale account sia selezionata per impostazione predefinita.

    - Nel **Gruppo di risorse**, selezionare o creare un gruppo di risorse per l'account DocumentDB.  Per impostazione predefinita, viene creato un nuovo gruppo di risorse. Per ulteriori informazioni, vedere [tramite il portale di Azure per gestire le risorse Azure](../articles/azure-portal/resource-group-portal.md).

    - Consente di specificare l'area geografica in cui pubblicare l'account DocumentDB **posizione** . 

4.  Dopo aver configurate le nuove opzioni account DocumentDB, fare clic su **Crea**. Per controllare lo stato della distribuzione, controllare l'hub di notifiche.  

    ![Creare rapidamente database - schermata dell'hub le notifiche, con la creazione di account DocumentDB](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-4.png)  

    ![Schermata dell'hub le notifiche, che mostra che l'account DocumentDB è stato creato correttamente e distribuito in un gruppo di risorse - notifica di creazione di database in linea](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-5.png)

5.  Dopo la creazione di account DocumentDB, è pronto per l'uso con le impostazioni predefinite. La coerenza predefinito dell'account DocumentDB viene impostata su **sessione**.  È possibile regolare la coerenza predefinito, fare clic su **Uniformità predefinita** nel menu della risorsa. Per ulteriori informazioni sui livelli di coerenza offerti dai DocumentDB, vedere [livelli di coerenza in DocumentDB](../articles/documentdb/documentdb-consistency-levels.md).

    ![Schermata della stessa e gruppo di risorse - iniziare lo sviluppo di applicazioni](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-6.png)  

    ![Schermata della stessa e livello di coerenza - coerenza sessione](./media/documentdb-create-dbaccount/create-nosql-db-databases-json-tutorial-7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md
