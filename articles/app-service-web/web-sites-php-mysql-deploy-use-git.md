<properties
    pageTitle="Creare un'app web PHP MySQL nel servizio di App Azure e distribuire utilizzando fra"
    description="Esercitazione in cui viene illustrato come creare un'app web PHP che archivia dati nel MySQL e utilizzare la distribuzione fra in Azure."
    services="app-service\web"
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="mysql"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-git"></a>Creare un'app web PHP MySQL nel servizio di App Azure e distribuire utilizzando fra

In questa esercitazione viene illustrato come creare un'app web PHP MySQL e su come distribuire al [Servizio App](http://go.microsoft.com/fwlink/?LinkId=529714) utilizzando fra. Si utilizzeranno [PHP][install-php], lo strumento della riga di comando MySQL (una parte di [MySQL][install-mysql]) e [fra] [ install-git] installato nel computer. Le istruzioni disponibili in questa esercitazione possono essere seguite dal sistema operativo, ad esempio Windows, Mac e Linux. Al termine dell'esecuzione di questa Guida, si avrà un'app web PHP/MySQL in esecuzione in Azure.

Si apprenderà:

* Come creare un'app web e un database MySQL tramite il [Portale di Azure][management-portal]. Poiché PHP è abilitata [nell'Applicazione del servizio Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) per impostazione predefinita, particolari è necessario eseguire il codice PHP.
* Informazioni su come pubblicare e pubblicare di nuovo all'applicazione di Azure utilizzando fra.
* Come attivare l'estensione di composizione automatizzare compositore attività in ogni `git push`.

Seguendo questa esercitazione si creerà un'app web di registrazione in PHP. L'applicazione è ospitata in Web Apps. Schermata dell'applicazione completata è inferiore a:

![Sito web PHP Azure][running-app]

## <a name="set-up-the-development-environment"></a>Configurare l'ambiente di sviluppo

In questa esercitazione si suppone che [PHP][install-php], lo strumento della riga di comando MySQL (una parte di [MySQL][install-mysql]) e [fra] [ install-git] installato nel computer.

<a id="create-web-site-and-set-up-git"></a>
## <a name="create-a-web-app-and-set-up-git-publishing"></a>Creare un'app web e impostare le operazioni di pubblicazione

Seguire questa procedura per creare un'app web e un database MySQL:

1. Accesso al [portale di Azure][management-portal].
2. Fare clic sull'icona di **Nuovo** .

3. Fare clic su **Vedere tutti** accanto **Marketplace**. 

4. Fare clic su **Web + Mobile**, quindi **Web app + MySQL**. Quindi fare clic su **Crea**.

4. Immettere un nome valido per il gruppo di risorse.

    ![Nome gruppo di set di risorse][resource-group]

5. Immettere valori per la nuova app web.

    ![Creare web app][new-web-app]

6. Immettere valori per il nuovo database, tra cui accettare le condizioni legali.

    ![Creare nuovi database MySQL][new-mysql-db]

7. Quando il web app è stato creato, verrà visualizzata e il app web nuovo.

7. In **Impostazioni** fare clic su **Distribuzione continua**, quindi fare clic su _Configura obbligatori impostazioni_.

    ![Configurare la pubblicazione fra][setup-publishing]

8. Selezionare **Archivio fra locale** per l'origine.

    ![Configurare fra archivio][setup-repository]


9. Per abilitare la pubblicazione di operazioni, è necessario specificare un nome utente e password. Prendere nota del nome utente e password creati. (Se è stata configurata un repository fra prima, questo passaggio verrà ignorato.)

    ![Creare le credenziali di pubblicazione][credentials]


## <a name="get-remote-mysql-connection-information"></a>Ottenere informazioni di connessione remote MySQL

Per connettersi al database MySQL che è in esecuzione in applicazioni Web, sarà necessario le informazioni di connessione. Per ottenere informazioni di connessione MySQL, procedere come segue:

1. Da un gruppo di risorse, fare clic sul database:

    ![Selezionare il database][select-database]

2. **Impostazioni**di database, selezionare **proprietà**.

    ![Selezionare proprietà][select-properties]

2. Prendere nota dei valori per `Database`, `Host`, `User Id`, e `Password`.

    ![Nota le proprietà][note-properties]

## <a name="build-and-test-your-app-locally"></a>Compilare e testare l'app in locale

Dopo avere creato un'app web, è possibile sviluppare l'applicazione localmente e quindi distribuirlo al termine del test.

L'applicazione di registrazione è un'applicazione PHP semplice che consente di eseguire la registrazione per un evento mediante l'indirizzo di posta elettronica e nome. Informazioni sui partecipanti precedenti vengono visualizzate in una tabella. Informazioni di registrazione vengono archiviate in un database MySQL. L'applicazione è costituita da un file (copiare e incollare codice riportato di seguito):

* **index. PHP**: consente di visualizzare un modulo per la registrazione e una tabella contenente le informazioni di persona.

Per creare ed eseguire l'applicazione in locale, seguire la procedura seguente. Si noti che questi passaggi, si dispone delle PHP e MySQL della riga di comando strumento (parte di MySQL) configurare sul computer locale e che è stata attivata l' [estensione PDO per MySQL][pdo-mysql].

1. Connettersi al server MySQL remoto, utilizzando il valore per `Data Source`, `User Id`, `Password`, e `Database` recuperato in precedenza:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Verrà visualizzato il prompt dei comandi di MySQL:

        mysql>

3. Incolla il seguente `CREATE TABLE` comando per creare il `registration_tbl` tabella nel database:

        CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Creare file **index. PHP** in radice della cartella dell'applicazione locale.

5. Aprire il file **index. PHP** in un editor di testo o IDE e aggiungere il codice seguente e completare le modifiche necessarie contrassegnate con `//TODO:` commenti.


        <html>
        <head>
        <Title>Registration Form</Title>
        <style type="text/css">
            body { background-color: #fff; border-top: solid 10px #000;
                color: #333; font-size: .85em; margin: 20; padding: 20;
                font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
            }
            h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
            h1 { font-size: 2em; }
            h2 { font-size: 1.75em; }
            h3 { font-size: 1.2em; }
            table { margin-top: 0.75em; }
            th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
            td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
        </style>
        </head>
        <body>
        <h1>Register here!</h1>
        <p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
        <form method="post" action="index.php" enctype="multipart/form-data" >
              Name  <input type="text" name="name" id="name"/></br>
              Email <input type="text" name="email" id="email"/></br>
              <input type="submit" name="submit" value="Submit" />
        </form>
        <?php
            // DB connection info
            //TODO: Update the values for $host, $user, $pwd, and $db
            //using the values you retrieved earlier from the Azure Portal.
            $host = "value of Data Source";
            $user = "value of User Id";
            $pwd = "value of Password";
            $db = "value of Database";
            // Connect to database.
            try {
                $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
                $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            }
            catch(Exception $e){
                die(var_dump($e));
            }
            // Insert registration info
            if(!empty($_POST)) {
            try {
                $name = $_POST['name'];
                $email = $_POST['email'];
                $date = date("Y-m-d");
                // Insert data
                $sql_insert = "INSERT INTO registration_tbl (name, email, date)
                           VALUES (?,?,?)";
                $stmt = $conn->prepare($sql_insert);
                $stmt->bindValue(1, $name);
                $stmt->bindValue(2, $email);
                $stmt->bindValue(3, $date);
                $stmt->execute();
            }
            catch(Exception $e) {
                die(var_dump($e));
            }
            echo "<h3>Your're registered!</h3>";
            }
            // Retrieve data
            $sql_select = "SELECT * FROM registration_tbl";
            $stmt = $conn->query($sql_select);
            $registrants = $stmt->fetchAll();
            if(count($registrants) > 0) {
                echo "<h2>People who are registered:</h2>";
                echo "<table>";
                echo "<tr><th>Name</th>";
                echo "<th>Email</th>";
                echo "<th>Date</th></tr>";
                foreach($registrants as $registrant) {
                    echo "<tr><td>".$registrant['name']."</td>";
                    echo "<td>".$registrant['email']."</td>";
                    echo "<td>".$registrant['date']."</td></tr>";
                }
                echo "</table>";
            } else {
                echo "<h3>No one is currently registered.</h3>";
            }
        ?>
        </body>
        </html>

4.  In un terminale passare alla cartella dell'applicazione e digitare il comando seguente:

        php -S localhost:8000

A questo punto è possibile passare a **http://localhost:8000 /** per testare l'applicazione.


## <a name="publish-your-app"></a>Pubblicare l'app

Dopo aver verificato l'app in locale, è possibile pubblicarlo per applicazioni Web utilizzando fra. Si inizializzare l'archivio locale fra e pubblicare l'applicazione.

> [AZURE.NOTE]
> Questi sono gli stessi passaggi visualizzati nel portale di Azure alla fine del Set di creare un'app web la pubblicazione fra sezione precedente.

1. (Facoltativo)  Se è stata dimenticata o spostate l'URL di repostitory remoto fra, passare alle proprietà app web nel portale di Azure.

1. Aprire GitBash (o un terminale se fra i `PATH`), passare alla directory radice dell'applicazione ed eseguire i comandi seguenti:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Verrà richiesto per la password creata in precedenza.

    ![Push iniziale in Azure tramite fra][git-initial-push]

2. Passare alla **http://[site name].azurewebsites.net/index.php** per iniziare a utilizzare l'applicazione (queste informazioni memorizzate nel dashboard account):

    ![Sito web PHP Azure][running-app]

Dopo aver pubblicato l'app, è possibile iniziare ad apportare modifiche a tale e utilizzare operazioni per la pubblicazione.

## <a name="publish-changes-to-your-app"></a>Pubblicare le modifiche apportate all'app

Per pubblicare le modifiche apportate all'applicazione in uso, procedere come segue:

1. Apportare modifiche all'app in locale.
2. Aprire GitBash (o un terminale it è fra i `PATH`), passare alla directory radice dell'app ed eseguire i comandi seguenti:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Verrà richiesto per la password creata in precedenza.

    ![Inserimento di modifiche del sito a Azure tramite fra][git-change-push]

3. Passare alla **http://[site name].azurewebsites.net/index.php** per visualizzare l'app e le modifiche apportate:

    ![Sito web PHP Azure][running-app]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

<a name="composer"></a>
## <a name="enable-composer-automation-with-the-composer-extension"></a>Abilitare l'automazione compositore con estensione compositore

Per impostazione predefinita, il processo di distribuzione fra nel servizio App non esegue alcuna operazione con composer.json, se si dispone di uno di un progetto PHP. È possibile abilitare composer.json elaborazione durante `git push` abilitando l'estensione compositore.

1. Nel PHP web blade dell'app nel [portale di Azure][management-portal], fare clic su **Strumenti** > **estensioni**.

    ![Impostazioni dell'estensione compositore][composer-extension-settings]

2. Fare clic su **Aggiungi**, quindi fare clic su **compositore**.

    ![Aggiunta di estensione compositore][composer-extension-add]
    
3. Fare clic su **OK** per accettare le condizioni legali. Fare clic su **OK** per aggiungere l'estensione.

    E **l'estensioni installati** dimostrare l'estensione compositore.  
    ![Visualizzazione di estensione compositore][composer-extension-view]
    
4. A questo punto, eseguire `git add`, `git commit`, e `git push` come sezione precedente. Si noterà ora che compositore l'installazione di dipendenze definite in composer.json.

    ![Compositore estensione successo][composer-extension-success]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori PHP](/develop/php/).

<!-- URL List -->

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx

<!-- IMG List -->

[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png

[composer-extension-settings]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-settings.png
[composer-extension-add]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-add.png
[composer-extension-view]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-view.png
[composer-extension-success]: ./media/web-sites-php-mysql-deploy-use-git/composer-extension-success.png
