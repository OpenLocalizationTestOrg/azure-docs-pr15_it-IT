<properties 
    pageTitle="Eseguire il debug di un'App Web di linguaggio su Azure in IntelliJ | Microsoft Azure" 
    description="In questa esercitazione viene illustrato come utilizzare il Toolkit di Azure per IntelliJ per il debug di un'App Web Java in esecuzione in Azure." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="selvasingh" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="asirveda;robmcm"/>

# <a name="debug-a-java-web-app-on-azure-in-intellij"></a>Eseguire il debug di un'App Web di linguaggio su Azure in IntelliJ

In questa esercitazione viene illustrato come eseguire il debug di un'App Web Java in esecuzione in Azure utilizzando il [Toolkit di Azure per IntelliJ]. Per semplicità, che verrà utilizzato un esempio Java Server Page (JSP) per questa esercitazione, ma la procedura potrebbe essere simile per un servlet Java quando si esegue il debug in Azure.

Dopo avere completato questa esercitazione, l'applicazione sarà simile a nell'illustrazione seguente durante il debug in IntelliJ:

![][01]
 
## <a name="prerequisites"></a>Prerequisiti

* Un linguaggio sviluppo Kit (JDK), v 1,8 o versione successiva.
* IntelliJ IDEA Ultimate Edition. Può essere scaricato dal <https://www.jetbrains.com/idea/download/index.html>.
* Distribuzione di un server web basate sul linguaggio o applicazione, ad esempio Apache Tomcat o alla banchina.
* Un abbonamento Azure, può essere ottenuto tramite <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Toolkit di Azure per IntelliJ. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per IntelliJ].
* Un progetto Web dinamico creato e distribuito al servizio di App Azure; ad esempio, vedere [creare un Hello World Web App per Azure in IntelliJ].

## <a name="to-debug-a-java-web-app-on-azure"></a>Eseguire il Debug di un'App Web di linguaggio su Azure

Per completare questa procedura in questa sezione, è possibile utilizzare un progetto Web dinamico esistente che è già stato distribuito come un'App Web di linguaggio su Azure, scaricare un [Progetto Web dinamico di esempio] e seguire i passaggi descritti in [creare un Hello World Web App per Azure in IntelliJ] per distribuire in Azure. 

1. Aprire il progetto di linguaggio Web App che è stato distribuito in Azure in IntelliJ.

1. Fare clic sul menu **Esegui** e quindi fare clic su **Modifica configurazioni**.

    ![][02]

1. Quando viene visualizzata la finestra di dialogo **Configurazioni di esecuzione/Debug** : 

    1. Selezionare **Azure Web App**.
    1. Fare clic su **+** per aggiungere una nuova configurazione.
    1. Specificare un **nome** per la configurazione.
    1. Accettare i valori predefiniti rimanenti vengono suggeriti dal Toolkit Azure e quindi fare clic su **OK**.

        ![][03]

1. Selezionare la configurazione di debug Azure Web App appena creata in alto a destra del menu e fare clic su **Debug**

    ![][04]

1. Quando viene richiesto di **attivare ora il debug remoto in remoto Web App?**, fare clic su **OK**.

1. Quando viene richiesto in cui **un'applicazione web è pronta per il debug remoto**, fare clic su **OK**.

    ![][05]

1. Selezionare la configurazione di debug Azure Web App appena creato in alto a destra del menu e quindi fare clic su **Debug**.

1. Un prompt dei comandi di Windows o shell Unix verrà aperto e preparare la connessione necessaria per il debug; è necessario attendere finché non viene eseguita correttamente la connessione all'applicazione Web Java remota prima di continuare. Se si utilizza Windows, il messaggio si presenterà simile al seguente:

    ![][06]

1. Inserire un punto di interruzione nella pagina JSP, quindi aprire l'URL per il linguaggio Web App in un browser:

    1. Aprire **Esplora Azure** in IntelliJ.
    1. Passare al **Web Apps** e si desidera eseguire il debug Java Web App.
    1. Fare clic con il pulsante destro sul Web App e fare clic su **Apri nel Browser**.
    1. IntelliJ ora entra in modalità di debug.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

Per ulteriori informazioni sulla creazione di Azure Web Apps, vedere [Panoramica di applicazioni Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit per IntelliJ]: ../azure-toolkit-for-intellij.md
[Installare il Toolkit di Azure per IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Creare un'App Web di Hello World per Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Progetto Web dinamico di esempio]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro per sviluppatori di Azure Java]: https://azure.microsoft.com/develop/java/
[Panoramica di Web App]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png
