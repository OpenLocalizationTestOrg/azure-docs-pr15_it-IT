1. Accedere al [portale di Azure].

2. Fare clic su **+ Nuovo** > **Web + Mobile** > **App Mobile**, quindi immettere un nome per il back-end App Mobile.

3. Per il **Gruppo di risorse**, selezionare un gruppo di risorse esistente o crearne uno nuovo (come l'app, con lo stesso nome). 
 
    È possibile selezionare un altro piano di servizio App o crearne uno nuovo. Per ulteriori informazioni sui servizi di App piani e come creare un nuovo piano in un prezzi diversi livelli e nella posizione desiderata, vedere [panoramica approfondita piani servizio App Azure](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

4. Per il **piano di servizio App**, il piano predefinito (nel [livello Standard](https://azure.microsoft.com/pricing/details/app-service/)) sia selezionato. È anche possibile selezionare il piano o [crearne uno nuovo](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md#create-an-app-service-plan). Impostazioni del piano di servizio di App determinano la [posizione, caratteristiche, costi e calcolare risorse](https://azure.microsoft.com/pricing/details/app-service/) associato all'app. 

    Dopo aver scelto il piano, fare clic su **Crea**. In questo modo si crea back-end App Mobile. 
    
6. In pala di **Impostazioni** per la nuova App Mobile di back-end, fare clic su **Guida introduttiva** > piattaforma app client > **connettere un database**. 

    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-data-connection.png)

7. In e **l'Aggiungi connessione dati** , fare clic su **Database SQL** > **Crea un nuovo database**, digitare il **nome del**database, scegliere un livello prezzo, quindi fare clic su **Server**.  È possibile riutilizzare il nuovo database. Se si dispone già di un database nella stessa posizione, se, tuttavia è possibile **usare un database esistente**. Perché i costi di larghezza di banda e latenza superiore non è consigliabile l'utilizzo di un database in un percorso diverso.
 
    ![](./media/app-service-mobile-dotnet-backend-create-new-service/dotnet-backend-create-db.png)

8. In e il **nuovo server** , digitare un nome server univoco nel campo **nome del Server** , specificare un account di accesso e una password, selezionare **Consenti azure servizi per accedere a server**e fare clic su **OK**. In questo modo si crea il nuovo database.

9. Nuovo in e **l'Aggiungi connessione dati** , fare clic su **stringa di connessione**, digitare i valori di accesso e la password per il database e fare clic su **OK**. Attendere alcuni minuti per il database essere distribuite correttamente prima di continuare.

<!-- URLs. -->
[Portale di Azure]: https://portal.azure.com/
