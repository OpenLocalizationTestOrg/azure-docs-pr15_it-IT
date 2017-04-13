<properties
    pageTitle="Creare un'app web di linguaggio nel servizio di App Azure | Microsoft Azure"
    description="In questa esercitazione viene illustrato come distribuire un'app web di linguaggio al servizio App Azure."
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
    ms.topic="get-started-article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="create-a-java-web-app-in-azure-app-service"></a>Creare un'app web di linguaggio nel servizio di App Azure

[AZURE.INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

In questa esercitazione viene illustrato come creare un linguaggio [web app in Azure App servizio] tramite il [Portale di Azure]. Il portale di Azure è un'interfaccia web che è possibile utilizzare per gestire le risorse Azure.

> [AZURE.NOTE] Per completare questa esercitazione, è necessario un account di Microsoft Azure. Se non si dispone di un account, è possibile [attivare i vantaggi della propria sottoscrizione Visual Studio] o [iscriversi per una versione di valutazione gratuita].
>
> Se si desidera iniziare a usare il servizio di App Azure prima che si effettua l'iscrizione per un account Azure, accedere al [Servizio di App provare]. Non esiste, è possibile creare immediatamente un'app web starter breve nel servizio App; Nessuna carta di credito è necessaria e non impegni.

## <a name="java-application-options"></a>Opzioni applicazione Java

Esistono diversi modi, che è possibile configurare un'applicazione Java in un'app web App servizio. 

1. Creare un'app e quindi configurare **le impostazioni dell'applicazione**.

    Servizio di App sono disponibili diverse versioni Tomcat e alla banchina con configurazione predefinita. Se l'applicazione che dovranno essere ospitati funzionano con una delle versioni incorporate, questo metodo di installazione di un contenitore di web risulta più semplice ed è ideale quando si desidera eseguire è caricare un file war in un contenitore di web. Per questo metodo, creare un'app nel portale di Azure e quindi passare alla sezione e **Impostazioni applicazione** per l'app scegliere la versione di linguaggio insieme il contenitore web linguaggio desiderato. Quando si usa questo metodo Java e il contenitore web vengono eseguiti dal file di programma. Altri metodi di inserire il contenitore web e potenzialmente JVM nel disco rigido. Quando si usa questo modello, è non disponibile per modificare i file in questa parte del file system. Questa indica che non è possibile eseguire operazioni come configurare il file *server.xml* o spostare file di libreria nella cartella */lib* . Per ulteriori informazioni, vedere il [creare e configurare un'applicazione web Java](#appsettings) sezione più avanti in questa esercitazione.
    
2. Usare un modello di Azure Marketplace.

    Azure Marketplace include modelli di creare e configurare applicazioni web Java con Tomcat o alla banchina contenitori web automaticamente. I contenitori web creato i modelli sono configurabili. Per ulteriori informazioni, vedere la sezione di [usare un modello di linguaggio di Azure Marketplace](#marketplace) di questa esercitazione.
  
3. Creare un'app e quindi copiare manualmente e modificare file di configurazione 

    È possibile pubblicare un'applicazione di linguaggio personalizzata non distribuisce in uno dei contenitori web forniti dal servizio di App. Per esempio:
    
    * L'applicazione di linguaggio richiede una versione di Tomcat o alla banchina che direttamente non è supportata dal servizio di App o fornita nella raccolta.
    * Applicazione Java riceve le richieste HTTP e non si distribuisce come una GUERRA in un contenitore di web esistente.
    * Si desidera configurare autonomamente il contenitore web da zero. 
    * Si desidera utilizzare una versione di linguaggio che non è supportato nel servizio di App e si desidera caricare personalmente.

    In questi casi, è possibile creare un'app tramite il portale di Azure e quindi fornire manualmente i file di runtime appropriato. In questo caso, i file verranno contati contro le quote di spazio di archiviazione per il piano di servizio di App. Per ulteriori informazioni, vedere [caricare un'app web personalizzata di linguaggio in Azure].

## <a name="portal"></a>Creare e configurare un'applicazione web Java

In questa sezione viene illustrato come creare un'app web e configurarlo per Java utilizzando e il **impostazioni dell'applicazione** del portale.

1. Accedere al [portale di Azure].

2. Fare clic su **Nuovo > Web + Mobile > Web App**.

    ![Nuova Web App][newwebapp]

4. Immettere un nome per l'applicazione web nella finestra di **Web app** .

    Questo nome deve essere univoco nel dominio di azurewebsites.net perché l'URL dell'applicazione web sarà {nome}. azurewebsites.net. Se il nome che immesso non è univoco, un punto esclamativo rosso visualizzato nella casella di testo.

5. Selezionare un **Gruppo di risorse** o crearne uno nuovo.

    Per ulteriori informazioni sui gruppi di risorse, vedere [tramite il portale di Azure per gestire le risorse Azure].

6. Selezionare un **Percorso/del piano di servizio App** o crearne uno nuovo.

    Per ulteriori informazioni sui piani di servizio di App, vedere [Panoramica sul piano di servizio App Azure].

7. Fare clic su **Crea**.

    ![Creare Web App][newwebapp2]
 
8. Quando il web app è stato creato, fare clic su **Web Apps > {un'app web}**.
 
    ![Selezionare Web App][selectwebapp]

9. In e il **Web app** , fare clic su **Impostazioni**.

10. Fare clic su **Impostazioni applicazione**.

11. Scegliere la **versione Java**desiderata. 

12. Scegliere la **versione secondaria di linguaggio**desiderata. Se si seleziona **più recente**, l'app utilizzerà la versione secondaria più recente disponibile nel servizio di App per tale versione principale di linguaggio. L'elemento **più recente** sia univoco ad App linguaggio create dalle **impostazioni dell'applicazione**. Se si crea l'app linguaggio dalla raccolta, è necessario gestire contenitore e le modifiche JVM. 

12. Scegliere il **contenitore Web**desiderato. Se si seleziona un contenitore il cui nome inizia con **più recente**, l'app di mantenere la versione più recente di tale versione principale di contenitore di web è disponibili nel servizio di App. 

    ![Versioni del contenitore Web][versions]

13. Fare clic su **Salva**.

    All'interno di alcuni secondi, un'applicazione web diventerà basato su Java e configurata per utilizzare il contenitore web selezionata.

14. Fare clic su **Web App > {la nuova app web}**.

15. Fare clic sull' **URL** per passare al nuovo sito.

    La pagina web conferma la creazione di un'applicazione web basata su Java.

## <a name="marketplace"></a>Usare un modello di linguaggio di Azure Marketplace

In questa sezione viene illustrato come utilizzare Azure Marketplace per creare un'app web di linguaggio. Lo stesso flusso generale può anche essere utilizzato per creare un basato su Java mobile o un'app API. 

1. Accedere al [portale di Azure]

2. Fare clic su **Nuovo > Marketplace**.

    ![Nuovo Marketplace][newmarketplace]

3. Fare clic su **Web + Mobile**.

    Potrebbe essere necessario scorrere verso sinistra per visualizzare e il **Marketplace** nel punto in cui è possibile selezionare **Web + Mobile**.

4. Nella casella di testo di ricerca, immettere il nome di un server di applicazioni Java, ad esempio **Apache Tomcat** o **alla banchina**e quindi premere INVIO.

5. Nei risultati della ricerca, fare clic su server di applicazioni Java.

    ![Web alla banchina per dispositivi mobili][webmobilejetty]

6. In e il primo **Tomcat Apache** o **alla banchina** , fare clic su **Crea**.

    ![Cala Blade portale][jettyblade]

7. In e il successivo **Tomcat Apache** o **alla banchina** , immettere un nome per l'applicazione web nella finestra di **Web app** .

    Questo nome deve essere univoco nel dominio di azurewebsites.net perché l'URL dell'applicazione web sarà {nome}. azurewebsites.net. Se il nome che immesso non è univoco, un punto esclamativo rosso visualizzato nella casella di testo.

8. Selezionare un **Gruppo di risorse** o crearne uno nuovo.

    Per ulteriori informazioni sui gruppi di risorse, vedere [tramite il portale di Azure per gestire le risorse Azure].

9. Selezionare un **Percorso/del piano di servizio App** o crearne uno nuovo.

    Per ulteriori informazioni sui piani di servizio di App, vedere [Panoramica sul piano di servizio App Azure].

10. Fare clic su **Crea**.

    ![Creare cala portale][jettyportalcreate2]

    In breve, in genere minori di un minuto Azure termina creare la nuova app web.

11. Fare clic su **Web App > {la nuova app web}**.

12. Fare clic sull' **URL** per passare al nuovo sito.

    ![URL cala][jettyurl]

    Tomcat viene fornito con un set predefinito di pagine. Se si è scelto Tomcat, si vedrà una pagina simile al seguente.

    ![Web app usando Tomcat Apache][tomcat]

    Se si è scelto alla banchina, si vedrà una pagina simile al seguente. Alla banchina non è disponibile un set di pagina predefinito, in modo JSP stessa che viene utilizzato per un sito di linguaggio vuoto viene riutilizzato qui.

    ![Web app usando alla banchina][jetty]

Una volta creato il web app con un contenitore di app, vedere la sezione [passaggi successivi](#next-steps) per informazioni su come caricare un'applicazione all'app web.

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone di un server di applicazioni Java in esecuzione in un'app web di Azure App servizio. Per distribuire il codice personalizzato per l'applicazione web, vedere [aggiungere un'applicazione o pagina Web per un'app web di linguaggio].

Per ulteriori informazioni sullo sviluppo di applicazioni Java in Azure, vedere il [Centro per sviluppatori di linguaggio].

<!-- URL List -->

[Aggiungere un'app web di linguaggio di un'applicazione o una pagina Web]: ./web-sites-java-add-app.md
[Panoramica di piani di Azure servizio di App]: ../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md
[Portale di Azure]: https://portal.azure.com/
[attivare i vantaggi della propria sottoscrizione Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[iscriversi a una versione di valutazione gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[Provare il servizio di App]: http://go.microsoft.com/fwlink/?LinkId=523751
[in Azure App servizio Web app]: http://go.microsoft.com/fwlink/?LinkId=529714
[Centro per sviluppatori Java]: /develop/java/
[Tramite il portale di Azure per gestire le risorse Azure]: ../azure-portal/resource-group-portal.md
[Caricare un'app web Java personalizzata in Azure]: ./web-sites-java-custom-upload.md

<!-- IMG List -->

[newwebapp]: ./media/web-sites-java-get-started/newwebapp.png
[newwebapp2]: ./media/web-sites-java-get-started/newwebapp2.png
[selectwebapp]: ./media/web-sites-java-get-started/selectwebapp.png
[versions]: ./media/web-sites-java-get-started/versions.png
[newmarketplace]: ./media/web-sites-java-get-started/newmarketplace.png
[webmobilejetty]: ./media/web-sites-java-get-started/webmobilejetty.png
[jettyblade]: ./media/web-sites-java-get-started/jettyblade.png
[jettyportalcreate2]: ./media/web-sites-java-get-started/jettyportalcreate2.png
[jettyurl]: ./media/web-sites-java-get-started/jettyurl.png
[tomcat]: ./media/web-sites-java-get-started/tomcat.png
[jetty]: ./media/web-sites-java-get-started/jetty.png
