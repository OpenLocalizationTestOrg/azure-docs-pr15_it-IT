<properties
    pageTitle="Installare il Toolkit di Azure per Eclisse | Microsoft Azure"
    description="Informazioni su come installare il Toolkit di Azure per Eclisse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# <a name="installing-the-azure-toolkit-for-eclipse"></a>Installare il Toolkit di Azure per Eclisse

Il Toolkit di Azure per Eclisse offre modelli e le funzionalità che consentono di facilmente creare, sviluppare, testare e distribuire le applicazioni Azure utilizzando l'ambiente di sviluppo Eclipse. Il Toolkit di Azure per Eclisse è un progetto Open Source, il cui codice sorgente è disponibile in base alla licenza MIT dal sito del progetto in GitHub all'indirizzo seguente:

<https://github.com/Microsoft/Azure-Tools-for-Java>

La procedura seguente mostra come installare il Toolkit di Azure per Eclisse.

[AZURE.INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="to-install-the-azure-toolkit-for-eclipse"></a>Per installare il Toolkit di Azure per Eclisse

1. Avviare Eclisse.

1. Quando si apre Eclisse, fare clic sul menu **della Guida** e quindi fare clic su **Installa nuova Software**, come illustrato nella figura seguente.

    ![Installare il Toolkit di Azure per Eclisse][01]

1. Nella finestra di dialogo **Prodotti Software disponibili** all'interno della casella di testo di **lavoro con** , digitare **http://dl.microsoft.com/eclipse** seguito dal tasto **INVIO** .

1. Nel riquadro **nome** selezionare **Toolkit di Azure per Eclisse**e deselezionare **tutti i siti durante l'aggiornamento di contatto installare per trovare il software necessario**. Lo schermo dovrebbe essere simile al seguente:

    ![Installare il Toolkit di Azure per Eclisse][02]

1. Se si espande il **Toolkit di Azure per Eclisse**, verrà visualizzato quanto segue:

    * **Applicazione approfondimenti plug-in per Java**: questo componente consente di utilizzare i servizi di Windows Azure telemetria registrazione e l'analisi per le applicazioni e le istanze di server.
    * **Filtro di servizi di controllo accesso Azure**: questo componente fornisce supporto per l'autenticazione degli utenti dell'applicazione con Azure ACS, attivazione scenari di single sign-on ed esternalizzazione logica identità dall'applicazione.
    * **Plug-in comune di Azure**: questo componente offre funzionalità comune richiesta da altri componenti del toolkit.
    * **Azure Explorer per Eclisse**: questo componente offre funzionalità comune richiesta da altri componenti del toolkit.
    * **Plug-in di Azure per Eclisse con linguaggio**: questo componente fornisce supporto per lo sviluppo di progetti che consentono di creare e distribuire le applicazioni di linguaggio per il cloud di Microsoft Azure in Eclisse e tramite riga di comando.
    * **Plug-in App Web Azure con linguaggio**: questo componente fornisce supporto per la distribuzione di applicazioni web Java per i contenitori di Microsoft Azure Web App.
    * **4.2 Driver JDBC di Microsoft per SQL Server**: questo componente fornisce API JDBC per SQL Server e Database SQL di Microsoft Azure per Java piattaforma Enterprise Edition 8.
    * **Pacchetto per le raccolte di Client Qpid Apache in JMS**: questo componente fornisce il componente client JMS dal progetto Apache Qpid per consentire l'applicazione di usare la messaggistica AMQP in Microsoft Azure.
    * **Pacchetto per Microsoft Azure raccolte per Java**: questo componente fornisce API per accedere ai servizi di Microsoft Azure, ad esempio lo spazio di archiviazione, bus di servizio, runtime del servizio e così via.

1. Fare clic su **Avanti**. (Se si verificano ritardi insoliti quando il toolkit di installazione, assicurarsi che **tutti i siti durante l'aggiornamento di contatto installare per trovare il software necessario** sia deselezionata.)

1. Nella finestra di dialogo **Dettagli installazione** , fare clic su **Avanti**.

    ![Esaminare i dettagli sull'installazione][03]

1. Nella finestra di dialogo **Rivedere licenze** , leggere le condizioni del contratto. Se si accettano le condizioni del contratto, fare clic su **accetto i termini del contratto** e quindi fare clic su **Fine**. (I passaggi rimanenti presuppongono che accettare le condizioni del contratto. Se non si desidera accettare i termini del contratto, chiudere il processo di installazione.)

    ![Esaminare le licenze][04]

    Eclisse verrà scaricati e installati pacchetti necessari.

    ![Stato dell'installazione][05]

1. Se viene richiesto di riavviare Eclisse per completare l'installazione, fare clic su **Sì**.

    ![Riavviare prompt dei comandi][06]

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni su Toolkit Azure per Java IDE, vedere i collegamenti seguenti:

- [Azure Toolkit per Eclisse]
  - *Installare il Toolkit di Azure per Eclisse (in questo articolo)*
  - [Creare un'App Web di Hello World per Azure in Eclisse]
  - [Novità di Azure Toolkit per Eclisse]
- [Azure Toolkit per IntelliJ]
  - [Installare il Toolkit di Azure per IntelliJ]
  - [Creare un'App Web di Hello World per Azure in IntelliJ]
  - [Novità di Azure Toolkit per IntelliJ]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Azure Toolkit per Eclisse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'App Web di Hello World per Azure in Eclisse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web di Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installing the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installare il Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novità di Azure Toolkit per Eclisse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità di Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori di Azure Java]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-01.png
[02]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-02.png
[03]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-03.png
[04]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-04.png
[05]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-05.png
[06]: ./media/azure-toolkit-for-eclipse-installation/eclipse-installation-06.png

