<properties 
    pageTitle="Creare un'app web PHP MySQL nel servizio di App Azure e distribuire tramite FTP" 
    description="Esercitazione in cui viene illustrato come creare un'app web PHP contenente dati in distribuzione FTP MySQL e l'utilizzo in Azure." 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>


#<a name="create-a-php-mysql-web-app-in-azure-app-service-and-deploy-using-ftp"></a>Creare un'app web PHP MySQL nel servizio di App Azure e distribuire tramite FTP

In questa esercitazione viene illustrato come creare un'app web PHP MySQL e su come distribuirlo tramite FTP. In questa esercitazione si suppone che [PHP][install-php], [MySQL][install-mysql], un server web, nonché un client FTP installato nel computer. Le istruzioni disponibili in questa esercitazione possono essere seguite dal sistema operativo, ad esempio Windows, Mac e Linux. Al termine dell'esecuzione di questa Guida, si avrà un'app web PHP/MySQL in esecuzione in Azure.
 
Si apprenderà:

* Informazioni su come creare un'app web e a un database MySQL tramite il portale di Azure. Poiché PHP è attivata nel Web Apps per impostazione predefinita, particolari è necessario eseguire il codice PHP.
* Informazioni su come pubblicare l'applicazione su Azure tramite FTP.
 
Seguendo questa esercitazione si creerà un'app web di registrazione in PHP. L'applicazione è ospitata in un'App Web. Schermata dell'applicazione completata è inferiore a:

![Sito Web PHP Azure][running-app]

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorie, senza impegni. 


##<a name="create-a-web-app-and-set-up-ftp-publishing"></a>Creare un'app web e impostare la pubblicazione FTP

Seguire questa procedura per creare un'app web e un database MySQL:

1. Accesso al [portale di Azure][management-portal].
2. Fare clic sull'icona **+ Nuovo** nell'angolo superiore sinistra del portale di Azure.

    ![Crea nuovo sito Web Azure][new-website]

3. Nella ricerca digitare **app Web + MySQL** e fare clic su **app Web + MySQL**.

    ![Personalizzato crea un nuovo sito Web][custom-create]

4. Fare clic su **Crea**. Immettere un nome di servizio univoca per l'app, un nome per il gruppo di risorse e un nuovo piano di servizio valido.

    ![Nome gruppo di set di risorse][resource-group]


6. Immettere valori per il nuovo database, tra cui accettare le condizioni legali.

    ![Creare nuovi database MySQL][new-mysql-db]
    
7. Quando il web app è stato creato, verrà visualizzata e il servizio app nuovo.


6. Fare clic su **Impostazioni** > **le credenziali di distribuzione**. 

    ![Impostare le credenziali di distribuzione][set-deployment-credentials]

7. Per abilitare la pubblicazione FTP, è necessario specificare un nome utente e password. Salvare le credenziali e prendere nota del nome utente e password creati.

    ![Creare le credenziali di pubblicazione][portal-ftp-username-password]

##<a name="build-and-test-your-app-locally"></a>Compilare e testare l'app in locale

L'applicazione di registrazione è un'applicazione PHP semplice che consente di eseguire la registrazione per un evento mediante l'indirizzo di posta elettronica e nome. Informazioni sui partecipanti precedenti vengono visualizzate in una tabella. Informazioni di registrazione vengono archiviate in un database MySQL. L'app è costituito da due file:

* **index. PHP**: consente di visualizzare un modulo per la registrazione e una tabella contenente le informazioni di persona.
* **CreateTable.PHP**: crea la tabella MySQL per l'applicazione. Questo file sarà possibile utilizzare una sola volta.

Per creare ed eseguire l'app in locale, seguire la procedura seguente. Si noti che per eseguire questi passaggi si PHP, MySQL, e un server web configurare nel computer locale e che si siano state attivate l' [estensione PDO per MySQL][pdo-mysql].

1. Creare un database MySQL chiamato `registration`. Al prompt MySQL con questo comando, è possibile eseguire questa operazione:

        mysql> create database registration;

2. Nella directory radice del server web, creare una cartella denominata `registration` e creare due file al suo interno - un'altra chiamata `createtable.php` e un'altra chiamata `index.php`.

3. Aprire la `createtable.php` dei file in un editor di testo o IDE e aggiungere il codice riportato di seguito. Questo codice verrà utilizzato per creare il `registration_tbl` tabella il `registration` database.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
                        PRIMARY KEY(id),
                        name VARCHAR(30),
                        email VARCHAR(30),
                        date DATE)";
            $conn->query($sql);
        }
        catch(Exception $e){
            die(print_r($e));
        }
        echo "<h3>Table created.</h3>";
        ?>

    > [AZURE.NOTE] 
    > È necessario aggiornare i valori per <code>$user</code> e <code>$pwd</code> con il nome utente MySQL locale e la password.

4. Aprire un web browser e passare alla [http://localhost/registration/createtable.php][localhost-createtable]. Verrà creato il `registration_tbl` tabella nel database.

5. Aprire il file **index. PHP** in un editor di testo o IDE e aggiungere il codice HTML e CSS base per la pagina (codice PHP aggiunto nei passaggi successivi).

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

        ?>
        </body>
        </html>

6. All'interno dei tag PHP, aggiungere codice PHP per la connessione al database.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [AZURE.NOTE]
    > Nuovo, sarà necessario aggiornare i valori per <code>$user</code> e <code>$pwd</code> con il nome utente MySQL locale e la password.

7. Il codice di connessione di database, aggiungere codice per l'inserimento di informazioni di registrazione nel database.

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

8. Infine, subito dopo il codice precedente, aggiungere il codice per recuperare dati dal database.

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

A questo punto è possibile passare a [http://localhost/registration/index.php] [ localhost-index] per verificare l'app.

##<a name="get-mysql-and-ftp-connection-information"></a>Ottenere informazioni sulla connessione MySQL e FTP

Per connettersi al database MySQL che è in esecuzione in applicazioni Web, sarà necessario le informazioni di connessione. Per ottenere informazioni di connessione MySQL, procedere come segue:

1. Dal servizio di app blade app web fare clic sul collegamento gruppo risorse:

    ![Gruppo di risorse selezionare][select-resourcegroup]

1. Da un gruppo di risorse, fare clic sul database:

    ![Selezionare il database][select-database]

2. Dal database di riepilogo, selezionare **Impostazioni** > **proprietà**.

    ![Selezionare proprietà][select-properties]
    
2. Prendere nota dei valori per `Database`, `Host`, `User Id`, e `Password`.

    ![Nota le proprietà][note-properties]

3. Da un'applicazione web, fare clic sul collegamento **Download pubblicare profilo** nell'angolo inferiore destro della pagina:

    ![Download di profilo di pubblicazione][download-publish-profile]

4. Aprire la `.publishsettings` file in un editor XML. 

3. Trovare il `<publishProfile >` elemento con `publishMethod="FTP"` che sarà simile alla seguente:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>
    
Annotare il `publishUrl`, `userName`, e `userPWD` attributi.

##<a name="publish-your-app"></a>Pubblicare l'app

Dopo aver verificato l'app in locale, è possibile pubblicare all'app web tramite FTP. Tuttavia, è innanzitutto necessario aggiornare le informazioni sulla connessione di database dell'applicazione. Usando le informazioni sulla connessione di database è stato acquistato in precedenza (nella **Guida MySQL e informazioni di connessione FTP** sezione), aggiornare le informazioni seguenti in **entrambi** i `createdatabase.php` e `index.php` file con i valori appropriati:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

A questo punto si è pronti pubblicare l'applicazione tramite FTP.

1. Aprire il client FTP desiderato.

2. Immettere il *nome host* dalla `publishUrl` attributo sopra riportati nel client FTP.

3. Immettere il `userName` e `userPWD` attributi indicato in precedenza invariato nel client FTP.

4. Stabilire una connessione.

Dopo aver connesso sarà possibile caricare e scaricare file in base alle esigenze. Assicurarsi che si siano caricando file alla directory radice, ovvero `/site/wwwroot`.

Dopo il caricamento entrambe `index.php` e `createtable.php`, passare alla **http://[site name].azurewebsites.net/createtable.php** per creare la tabella MySQL per l'applicazione, quindi passare alla **http://[site name].azurewebsites.net/index.php** per iniziare a utilizzare l'applicazione.
 
## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori PHP](/develop/php/).

[install-php]: http://www.php.net/manual/en/install.php
[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[localhost-createtable]: http://localhost/tasklist/createtable.php
[localhost-index]: http://localhost/tasklist/index.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-ftp/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-ftp/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-ftp/create_web_mysql.png
[website-details]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-ftp/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-ftp/select_webapp.png
[set-deployment-credentials]: ./media/web-sites-php-mysql-deploy-use-ftp/set_credentials.png
[portal-ftp-username-password]: ./media/web-sites-php-mysql-deploy-use-ftp/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-ftp/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-ftp/create_wa.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-ftp/select_database.png
[select-resourcegroup]: ./media/web-sites-php-mysql-deploy-use-ftp/select_resourcegroup.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-ftp/note-properties.png

[connection-string-info]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
[management-portal]: https://portal.azure.com
[download-publish-profile]: ./media/web-sites-php-mysql-deploy-use-ftp/download_publish_profile_3.png
 
