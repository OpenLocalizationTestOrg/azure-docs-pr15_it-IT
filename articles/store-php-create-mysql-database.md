<properties
    pageTitle="Creazione e la connessione a un database MySQL in Azure"
    description="Informazioni su come usare il portale di Azure per creare un database MySQL e quindi effettuare la connessione da un'app web PHP in Azure."
    documentationCenter="php"
    services="app-service\web"
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="multiple"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm;cephalin"/>

# <a name="create-and-connect-to-a-mysql-database-in-azure"></a>Creazione e la connessione a un database MySQL in Azure

In questa esercitazione viene illustrato come creare un database MySQL nel [portale di Azure](https://portal.azure.com) (provider è [ClearDB](http://www.cleardb.com/)) e su come connettersi ad esso da un'app web PHP in esecuzione in [Azure App servizio](./app-service/app-service-value-prop-what-is.md). 

> [AZURE.NOTE] È anche possibile creare un database MySQL come parte di un [modello di app Marketplace](./app-service-web/app-service-web-create-web-app-from-marketplace.md).

## <a name="create-a-mysql-database-in-azure-portal"></a>Creare un database MySQL nel portale di Azure

Per creare un database MySQL nel portale di Azure, eseguire le operazioni seguenti:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel menu a sinistra, fare clic su **Nuovo** > **dati + spazio di archiviazione** > **MySQL Database**.

    ![Creare un database MySQL in Azure - inizio](./media/store-php-create-mysql-database/create-db-1-start.png)

2. In Nuovo MySQL Database [blade](azure-portal-overview.md), configurare un nuovo database MySQL come indicato di seguito (*blade*: una pagina del portale che verrà visualizzato in orizzontale):

    - **Nome del database**: digitare un nome in modo univoco personale
    - **Abbonamento**: scegliere l'abbonamento da utilizzare
    - **Tipo di database**: selezionare **condivisi** per livelli economico o non si libera o **dedicato** ottenere risorse dedicate. 
    - **Gruppo risorse**: aggiungere il database MySQL a un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) esistente o inserire una nuova. Risorse nello stesso gruppo possono essere gestite insieme.
    - **Posizione**: selezionare un percorso vicino è. Quando si aggiungono a un gruppo di risorse esistente, è bloccato in posizione del gruppo di risorse.
    - **Livello prezzi**: fare clic su **Livello prezzi**, quindi selezionare un'opzione di prezzo (livello di**mercurio** è gratuito), quindi fare clic su **Seleziona**. 
    - **Note legali**: fare clic su **Note legali**, rivedere i dettagli di acquisto e fare clic su **acquisto**.
    - **Aggiungi a dashboard**: selezionare se si desidera accedere direttamente dal dashboard. Questa operazione è particolarmente utile se non si ha familiarità con ancora navigazione portale.
    
    Nella schermata riportata di seguito è solo un esempio di come è possibile configurare il database MySQL.  
    ![Creare un database MySQL in Azure - configurare](./media/store-php-create-mysql-database/create-db-2-configure.png)

3. Dopo aver completato la configurazione, fare clic su **Crea**.

    ![Creare un database MySQL in Azure - creazione](./media/store-php-create-mysql-database/create-db-3-create.png)

    Verrà visualizzata una possibilità di notifica popup che si sa che si è iniziata la distribuzione.

    ![Creare un database MySQL in Azure - in corso](./media/store-php-create-mysql-database/create-db-4-started-status.png)

    Verrà visualizzato un altro a comparsa una volta completata la distribuzione. Il portale verrà aperto automaticamente il blade database MySQL.

<a name="connect"></a>
## <a name="connect-to-your-mysql-database"></a>Connettersi al database MySQL

Per visualizzare le informazioni di connessione per il nuovo database MySQL, fare clic su **proprietà** blade dell'applicazione web.
    
![Creare un database MySQL in Azure - blade database MySQL](./media/store-php-create-mysql-database/create-db-5-finished-db-blade.png)

È ora possibile usare le informazioni di connessione in qualsiasi app web. Un esempio che illustra come usare le informazioni di connessione da una semplice applicazione PHP è disponibile [qui](https://github.com/WindowsAzure/azure-sdk-for-php-samples/tree/master/tasklist-mysql).

## <a name="connect-a-laravel-web-app-from-the-php-get-started-tutorial"></a>Connettere un'app web Laravel (dall'esercitazione introduttiva get PHP)

Si supponga che è stata appena terminata esercitazioni [creare, configurare e distribuire un'app web PHP in Azure](./app-service-web/app-service-web-php-get-started.md) e ottenere un'app web [Laravel](https://www.laravel.com/) in esecuzione in Azure. È possibile aggiungere facilmente le funzionalità di database Laravel all'applicazione in uso. Seguire la procedura seguente:

>[AZURE.NOTE] I passaggi seguenti presuppongono che sono state esercitazioni [creare, configurare e distribuire un'app web PHP in Azure](./app-service-web/app-service-web-php-get-started.md).

1. Configurare l'app Laravel nel proprio ambiente di sviluppo locale in modo che puntino a database MySQL. A tale scopo, aprire `.env` dell'App Laravel la directory radice e configurare le opzioni di database MySQL.

        DB_CONNECTION=mysql
        DB_HOST=<HOSTNAME_from_properties_blade>
        DB_PORT=<PORT_from_properties_blade>
        DB_DATABASE=<see_note_below>
        DB_USERNAME=<USERNAME_from_properties_blade>
        DB_PASSWORD=<PASSWORD_from_properties_blade>

    >[AZURE.NOTE] In e il **proprietà** , il nome del database MySQL potrebbe o potrebbe non essere quello indicato nel campo **Nome del DATABASE** . Si consiglia di controllare il parametro di Database nel campo **Stringa di connessione** . 
    >
    >![Creare un database MySQL in Azure - in corso](./media/store-php-create-mysql-database/connect-db-1-database-name.png)

2. Il modo più rapido per verificare di avere accesso MySQL ora consiste nell'utilizzare [pagine di supporto temporaneo autenticazione del Laravel predefinito](https://laravel.com/docs/5.2/authentication#authentication-quickstart). In terminale della riga di comando, eseguire i comandi seguenti dalla directory principale dell'applicazione Laravel:

         php artisan migrate
         php artisan make:auth

    Il primo comando crea le tabelle in Azure in migrazioni predefinite in base alla `database/migrations` directory e il secondo comando scaffolds visualizzazioni di base e route per l'autenticazione e la registrazione utente.

3. Esegui il server di sviluppo:

        php artisan serve

4. Nel browser passare alla http://localhost:8000 e registrare un nuovo utente, come illustrato:

    ![Connettersi a un database MySQL nella Azure - registra utente](./media/store-php-create-mysql-database/connect-db-2-development-server.png)

    Seguire i prompt dell'interfaccia utente completato la registrazione. Una volta completata la registrazione, verranno registrati in.
    
    ![Connettersi a un database MySQL nella Azure - registra utente](./media/store-php-create-mysql-database/connect-db-3-registered-user.png)

    A questo punto si sviluppa l'app nel database MySQL in Azure.

5. A questo punto, è necessario solo replicare il `.env` impostazioni per un'app web Azure. Eseguire i comandi di Azure CLI seguenti:

        azure site appsetting add DB_CONNECTION=mysql
        azure site appsetting add DB_HOST=<HOSTNAME_from_properties_blade>
        azure site appsetting add DB_PORT=<PORT_from_properties_blade>
        azure site appsetting add DB_DATABASE=<Database_param_from_CONNECTION_INFO_from_properties_blade>
        azure site appsetting add DB_USERNAME=<USERNAME_from_properties_blade>
        azure site appsetting add DB_PASSWORD=<PASSWORD_from_properties_blade>

    Verificare il funzionamento in [configurare l'app web Azure](./app-service-web/app-service-web-php-get-started.md#configure).

6. Successivamente, eseguire il commit e push in Azure le modifiche locali apportate in precedenza durante l'esecuzione `php artisan make:auth`.

        git add .
        git commit -m "scaffold auth views and routes"
        git push azure master

7. Passare all'app web Azure.

        azure site browse

8. Accedere usando le credenziali utente creata in precedenza.

    ![Connettersi a un database MySQL nella Azure - passare a Azure web app](./media/store-php-create-mysql-database/connect-db-4-browse-azure-webapp.png)

    Dopo l'accesso, verrà visualizzato la schermata di post-accesso descrittivo.
    
    ![Connettersi a un database MySQL nella Azure - eseguito l'accesso](./media/store-php-create-mysql-database/connect-db-5-logged-in.png)

    Congratulazioni, un'applicazione web PHP in Azure a questo punto l'accesso ai dati dal database MySQL. 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori PHP](/develop/php/).
