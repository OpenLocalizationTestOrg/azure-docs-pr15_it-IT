<properties 
    pageTitle="Aggiungere un'applicazione Java in Azure App servizio Web Apps" 
    description="In questa esercitazione viene illustrato come aggiungere una pagina o applicazione per l'istanza di Azure App servizio Web App è già configurato per l'utilizzo di linguaggio." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Aggiungere un'applicazione Java in Azure App servizio Web Apps

Dopo avere inizializzato un'app web di linguaggio in [Azure App servizio][] come [creare un'app web di linguaggio nel servizio App Azure](web-sites-java-get-started.md), è possibile caricare l'applicazione posizionando il WAR nella cartella **WebApp** .

Il percorso di navigazione alla cartella **WebApp** varia in base al come si configura l'istanza di Web Apps.

- Se è stato configurato l'app web usando Azure Marketplace, il percorso della cartella **WebApp** sia nel modulo **d:\home\site\wwwroot\bin\application\_server\webapps**, dove **applicazione\_server** è il nome del server applicazioni valide per l'istanza del Web Apps. 
- Se è stato configurato l'app web tramite la configurazione dell'interfaccia utente di Azure, il percorso nella cartella **WebApp** è il modulo **d:\home\site\wwwroot\webapps**. 

Si noti che è possibile utilizzare il codice sorgente per caricare l'applicazione o pagine web, inclusi [scenari di integrazione continua](app-service-continuous-deployment.md). FTP è un'opzione per il caricamento dell'applicazione o pagine web.

Nota per le applicazioni web Tomcat: dopo aver caricato un file WAR nella cartella **WebApp** , il server di applicazione Tomcat rileva stato aggiunto e verrà automaticamente caricata. Si noti che se si copiano file (diversi dal file WAR) per la directory radice, il server di applicazione sarà necessario riavviare vengono utilizzati i file. La funzionalità di autoload per Java Tomcat web apps in esecuzione in Azure è basata su un nuovo file WAR da aggiungere nuovi file o directory aggiunte alla cartella **WebApp** . 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di linguaggio](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Servizio App Azure]: http://go.microsoft.com/fwlink/?LinkId=529714
