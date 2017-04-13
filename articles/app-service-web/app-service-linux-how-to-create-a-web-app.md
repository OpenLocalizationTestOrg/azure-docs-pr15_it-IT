<properties 
    pageTitle="Come creare un'App Web con il servizio di App su Linux | Microsoft Azure" 
    description="Web app Creazione flusso di lavoro per il servizio di App su Linux." 
    keywords="servizio di applicazione Azure, web app, linux, sistemi operativi"
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

# <a name="create-a-web-app-with-app-service-on-linux"></a>Creare un'App Web con il servizio di App su Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>Tramite il portale di gestione per creare un'applicazione web
È possibile avviare la creazione di un'applicazione Web su Linux dal [portale di gestione](https://portal.azure.com) come illustrato nell'immagine seguente.

![][1]

Dopo aver selezionato l'opzione riportata di seguito, verranno visualizzate e il Crea come illustrato nell'immagine seguente. 

![][2]

-   Assegnare un nome di un'applicazione web.
-   Scegliere un gruppo di risorse esistente o crearne uno nuovo. (Vedere aree disponibili nella [sezione limitazioni](./app-service-linux-intro.md)).
-   Scegliere un piano di servizio app esistente oppure creare un nuovo uno (vedere app servizio piano note nella [sezione limitazioni](./app-service-linux-intro.md)). 
-   Scegliere stack di applicazione che si desidera utilizzare. È possibile scegliere tra diverse versioni di Node e PHP. 

Dopo avere inserito l'app creata, è possibile modificare stack applicazione dalle impostazioni dell'applicazione come illustrato nell'immagine seguente.

![][3]

## <a name="deploying-your-web-app"></a>Distribuzione di un'applicazione web

Opzioni "distribuzione" dal portale di gestione offre la possibilità di utilizzare un archivio fra o a un archivio GitHub locale per distribuire l'applicazione. Le istruzioni sono successivamente in modo analogo a un'app web non Linux ed è possibile seguire le istruzioni in nostro [distribuzione fra locale](./app-service-deploy-local-git.md) o l'articolo [distribuzione continua](./app-service-continuous-deployment.md) per GitHub.

È anche possibile utilizzare FTP per caricare l'applicazione al sito. È possibile ottenere l'endpoint FTP per un'app web dalla sezione dei registri diagnostica, come illustrato nell'immagine seguente.

![][4]


## <a name="next-steps"></a>Passaggi successivi ##

* [Che cos'è il servizio di App su Linux?](./app-service-linux-intro.md)
* [Utilizzo di configurazione PM2 per Node nelle App Web su Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
