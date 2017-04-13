<properties 
    pageTitle="Utilizza PM2 configurazione per NodeJS nelle App Web su Linux | Microsoft Azure" 
    description="Utilizzo di configurazione PM2 per NodeJS nelle App Web su Linux" 
    keywords="servizio di Azure app web app, nodejs, pm2, linux, sistemi operativi"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Utilizzo di configurazione PM2 per Node nelle App Web su Linux

Se si imposta il stack di applicazioni a node per Web Apps su Linux, verrà visualizzato l'opzione per impostare un file di avvio Node, come illustrato nell'immagine seguente.

![][1]

È possibile utilizzare questa a uno

-   Specificare lo script di avvio per l'app node (ad esempio: /bin/server.js)
-   Specificare il file di configurazione PM2 da utilizzare per l'app node (ad esempio: /foo/process.json)

 >[AZURE.NOTE] Se si vuole dai processi di nodo riavviare automaticamente quando si modificano determinati file, è necessario usare la configurazione PM2. In caso contrario il riavvio dell'applicazione non quando riceve le notifiche di modifica da elementi quali distribuzione continua quando viene modificato il codice dell'applicazione.

È possibile controllare la Node [documentazione di file di processo](http://pm2.keymetrics.io/docs/usage/application-declaration/) per tutte le opzioni, ma seguito è riportato un esempio di si utilizzerà come file process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Aspetti da tenere presente in questa configurazione sono 

-   La proprietà di "script" specifica script di avvio dell'applicazione.
-   La proprietà di "istanze" Specifica il numero di istanze del processo di nodo alla barra di avvio. Se si esegue l'applicazione in dimensioni maggiori di macchine Virtuali che dispone di più core, si desidera ingrandire le risorse mediante l'impostazione di un valore maggiore di seguito.
-   La matrice "controllo" Specifica tutti i file per cui è cambiato si desidera riavviare i processi di nodo.
-   Per "watch_options", è necessario attualmente specificare "usePolling" come true a causa il modo in cui che è installato il contenuto dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi ##

* [Che cos'è il servizio di App su Linux?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png