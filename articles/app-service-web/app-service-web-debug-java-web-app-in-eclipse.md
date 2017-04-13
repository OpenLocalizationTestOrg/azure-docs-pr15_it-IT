<properties 
    pageTitle="Eseguire il debug di un'App Web di linguaggio su Azure in Eclisse | Microsoft Azure" 
    description="In questa esercitazione viene illustrato come utilizzare il Toolkit di Azure per Eclisse per il debug di un'App Web Java in esecuzione in Azure." 
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

# <a name="debug-a-java-web-app-on-azure-in-eclipse"></a>Eseguire il debug di un'App Web di linguaggio su Azure in Eclisse

In questa esercitazione viene illustrato come eseguire il debug di un'App Web Java in esecuzione in Azure utilizzando il [Toolkit di Azure per Eclisse]. Per semplicità, che verrà utilizzato un esempio Java Server Page (JSP) per questa esercitazione, ma la procedura potrebbe essere simile per un servlet Java quando si esegue il debug in Azure.

Dopo avere completato questa esercitazione, l'applicazione sarà simile a nell'illustrazione seguente durante il debug in Eclisse:

![][01]
 
## <a name="prerequisites"></a>Prerequisiti

* Un linguaggio sviluppo Kit (JDK), v 1,8 o versione successiva.
* Eclipse IDE per sviluppatori Java Edition, il o versione successiva. Può essere scaricato dal <http://www.eclipse.org/downloads/>.
* Distribuzione di un server web basate sul linguaggio o applicazione, ad esempio Apache Tomcat o alla banchina.
* Un abbonamento Azure, può essere ottenuto tramite <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Toolkit di Azure per Eclisse. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per Eclisse].
* Un progetto Web dinamico creato e distribuito al servizio di App Azure; ad esempio, vedere [creare un Hello World Web App per Azure in Eclisse].

## <a name="to-debug-a-java-web-app-on-azure"></a>Eseguire il Debug di un'App Web di linguaggio su Azure

Per completare questa procedura in questa sezione, è possibile utilizzare un progetto Web dinamico esistente che è già stato distribuito come un'App Web di linguaggio su Azure, scaricare un [Progetto Web dinamico di esempio] e seguire i passaggi descritti in [creare un Hello World Web App per Azure in Eclisse] per distribuire in Azure. 

1. Aprire Eclisse.

1. Configurare i timeout per il debug remoto:

    1. Fare clic sul menu di **Windows** in Eclisse e quindi fare clic su **Preferenze**.
    1. Espandere il nodo di **linguaggio** , quindi selezionare **Debug**.
    1. Configurare le impostazioni di **timeout Debugger (ms)** e **timeout (ms) di avvio** per `120000`.

        ![][02]

    1. Fare clic su **OK** per chiudere la finestra di dialogo **Preferenze** .

1. Nella visualizzazione Gestione progetti del Eclisse destro del mouse, fare clic sul progetto di Web dinamici che è stato distribuito di Azure. Quando viene visualizzato il menu di scelta rapida, selezionare **Il Debug come**e quindi fare clic su **Azure Web App**.

    ![][03]

1. Se si tratta la prima volta che si esegue il debug progetto Web dinamico, verrà aperta la finestra di dialogo **Configurazioni Debug** ; è possibile accettare i valori predefiniti specificati dal Toolkit nella scheda **connessione** . Nella scheda **origine** fare clic su **Aggiungi**, quindi **progetto Java**, selezionare **Dinamico progetto Web**e quindi fare clic su **OK**. Dopo aver completato la procedura seguente, fare clic su **Debug**.

    ![][04]

1. Quando viene richiesto di **attivare ora il debug remoto in remoto Web App?**, fare clic su **OK**.

1. Quando viene richiesto in cui **un'applicazione web è pronta per il debug remoto**, fare clic su **OK**.

    ![][05]

1. Quando verrà visualizzata la finestra di dialogo **Debug configurazioni** , fare clic su **Debug**.

1. Un prompt dei comandi di Windows o shell Unix verrà aperto e preparare la connessione necessaria per il debug; è necessario attendere finché non viene eseguita correttamente la connessione all'applicazione Web Java remota prima di continuare. Se si utilizza Windows, avrà l'aspetto nell'illustrazione seguente.

    ![][06]

1. Inserire un punto di interruzione nella pagina JSP, quindi aprire l'URL per il linguaggio Web App in un browser:

    1. Aprire **Esplora Azure** in Eclisse.
    1. Passare al **Web Apps** e si desidera eseguire il debug Java Web App.
    1. Fare clic con il pulsante destro sul Web App e fare clic su **Apri nel Browser**.
    1. Eclisse ora entra in modalità di debug.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

Per ulteriori informazioni sulla creazione di Azure Web Apps, vedere [Panoramica di applicazioni Web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit per Eclisse]: ../azure-toolkit-for-eclipse.md
[Installare il Toolkit di Azure per Eclisse]: ../azure-toolkit-for-eclipse-installation.md
[Creare un'App Web di Hello World per Azure in Eclisse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Progetto Web dinamico di esempio]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro per sviluppatori di Azure Java]: https://azure.microsoft.com/develop/java/
[Panoramica di Web App]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-eclipse/01-debug-java-web-app-in-eclipse.png
[02]: ./media/app-service-web-debug-java-web-app-in-eclipse/02-configure-eclipse-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-eclipse/03-debug-as.png
[04]: ./media/app-service-web-debug-java-web-app-in-eclipse/04-debug-configurations.png
[05]: ./media/app-service-web-debug-java-web-app-in-eclipse/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-eclipse/06-windows-command-prompt-connection-successful-to-remote.png
