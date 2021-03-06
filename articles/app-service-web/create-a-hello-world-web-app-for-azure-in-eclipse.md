<properties 
    pageTitle="Creare un'App Web di Hello World per Azure in Eclisse" 
    description="In questa esercitazione viene illustrato come utilizzare il Toolkit di Azure per Eclisse per creare un Hello World Web App per Azure." 
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

# <a name="create-a-hello-world-web-app-for-azure-in-eclipse"></a>Creare un'App Web di Hello World per Azure in Eclisse

In questa esercitazione viene illustrato come creare e distribuire un'applicazione Hello World base in Azure come un'App Web usando il [Toolkit di Azure per Eclisse]. È illustrato un esempio JSP base per semplicità, ma una procedura molto simile sarebbe appropriata per un servlet Java per quanto riguarda la distribuzione di Azure.

Dopo avere completato questa esercitazione, l'applicazione sarà simile alla figura riportata di seguito quando si visualizza in un web browser:

![][01]
 
## <a name="prerequisites"></a>Prerequisiti

* Un linguaggio sviluppo Kit (JDK), v 1.7 o versione successiva.
* Eclipse IDE per sviluppatori Java Edition, il o versione successiva. Può essere scaricato dal <http://www.eclipse.org/downloads/>.
* Distribuzione di un server web basate sul linguaggio o applicazione, ad esempio Apache Tomcat o alla banchina.
* Un abbonamento Azure, può essere ottenuto tramite <https://azure.microsoft.com/en-us/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* Toolkit di Azure per Eclisse. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per Eclisse].

## <a name="to-create-a-hello-world-application"></a>Per creare un'applicazione Hello World

Prima di tutto, iniziamo con la creazione di un progetto di linguaggio.

1. Avviare Eclisse e nel menu fare clic su **File**, fare clic su **Nuovo**e quindi fare clic su **Project Web dinamico**. (Se non è visibile **Dinamico progetto Web** elencato come un progetto disponibile dopo facendo clic su **File** e **Nuovo**, quindi eseguire le operazioni seguenti: fare clic su **File**, fare clic su **Nuovo**, fare clic su **progetto...**, espandere **Web**, fare clic su **Progetto Web dinamico**e fare clic su **Avanti**.)

1. Per motivi di questa esercitazione, nome del progetto **MyHelloWorld**. Lo schermo apparirà simile al seguente:

    ![][02]

1. Fare clic su **Fine**.

1. In visualizzazione di Esplora file di progetto del Eclisse, espandere **MyHelloWorld**. Pulsante destro del mouse **DatiWeb**, fare clic su **Nuovo**e quindi fare clic su **File JSP**.

1. Nella finestra di dialogo **Nuovo File JSP** assegnare un nome di file **index.jsp**. Mantenere la cartella padre come **MyHelloWorld/DatiWeb**.

1. Nella finestra di dialogo **Seleziona modello JSP** ai fini di questa esercitazione selezionare **Nuovo File JSP (html)**e quindi fare clic su **Fine**.

1. Quando si apre il file index.jsp in Eclisse, aggiungere del testo da visualizzare in modo dinamico **Hello World!** all'interno di esistente `<body>` elemento. L'aggiornamento `<body>` contenuto dovrebbe essere simile al seguente:

    `<body><b><% out.println("Hello World!"); %></b></body>` 

1. Salvare index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Per distribuire l'applicazione di un contenitore di Azure Web App

Esistono diversi modi in base al quale è possibile distribuire un'applicazione web Java in Azure. In questa esercitazione viene una delle più semplici: applicazione verrà distribuito in un contenitore di App Web di Azure - alcun tipo di progetto speciale né strumenti aggiuntivi non necessari. JDK il software e web contenitore saranno forniti automaticamente da Azure, in modo che non è necessario per caricare il proprio; è sufficiente il linguaggio Web App. Di conseguenza, il processo di pubblicazione per l'applicazione richiederà secondi, non minuti.

1. In Esplora progetti di Eclisse rapida **MyHelloWorld**.

1. Nel menu di scelta rapida, selezionare **Azure**, quindi fare clic su **Pubblica come Azure Web App..**

    ![][03]
   
    In alternativa, dopo avere selezionato il progetto di applicazione web in Gestione progetti, è possibile fare clic sul pulsante a discesa **pubblica** sulla barra degli strumenti e selezionare **Pubblica come Azure Web App** da tale posizione:
   
    ![][14]
   
1. Se non è già stato effettuato Azure da Eclisse, verrà richiesto di accedere all'account Azure:

    ![][04]
   
    Nota: Se si hanno più account Azure, alcune delle istruzioni durante la procedura di accesso in potrebbe essere visualizzato più volte, anche se vengono visualizzati in modo che corrisponda. In questo caso, continuare a seguire le istruzioni di accesso.
1. Dopo aver completato l'accesso all'account Azure, nella finestra di dialogo **Gestione delle sottoscrizioni** verrà visualizzato un elenco delle sottoscrizioni sono associate le proprie credenziali di. Se sono elencati più abbonamenti e si desidera utilizzare solo un sottoinsieme specifico di esse, facoltativamente, è possibile deselezionare quelli che si desidera utilizzare. Dopo aver selezionato le proprie sottoscrizioni, fare clic su **Chiudi**.

    ![][05]
   
1. Quando viene visualizzata la finestra di dialogo **Distribuisci al contenitore di App Web di Azure** , viene visualizzato qualsiasi contenitori App Web creata in precedenza; Se non è stato creato eventuali contenitori, l'elenco sarà vuoto.

    ![][06]
   
1. Se non è stato creato un contenitore di App Web di Azure prima o se si desidera pubblicare l'applicazione di un nuovo contenitore, utilizzare la procedura seguente. In caso contrario, selezionare un contenitore di App Web esistente e andare al passaggio 7 riportato di seguito.

    1. Fare clic su **Nuovo …**

    1. Verrà visualizzata nella finestra di dialogo **Nuovo contenitore App Web** :

        ![][07]

    1. Immettere un' **etichetta DNS** per il contenitore di App Web. l'etichetta DNS foglia dell'URL host per l'applicazione web verrà formato in Azure. Nota: Il nome deve essere disponibile ed è conforme ai requisiti per la denominazione di Azure Web App.

    1. Nel menu a discesa **Web contenitore** , selezionare il software appropriato per l'applicazione.

        Attualmente, è possibile scegliere da 8 Tomcat, Tomcat 7 o alla banchina 9. Una distribuzione recente del software selezionato sarà fornita da Azure e verrà eseguito in una distribuzione recente di 8 JDK creata da Oracle e fornita da Azure.

    1. Nel menu a discesa **abbonamento** , selezionare l'abbonamento che si desidera utilizzare per la distribuzione.

    1. Nel menu a discesa **Gruppo di risorse** , selezionare il gruppo di risorse con cui si desidera associare un'App Web.

        Nota: Azure gruppi di risorse consente di raggruppare le relative risorse in modo che, ad esempio possibile eliminare insieme.

        È possibile utilizzare la seguente procedura per creare un nuovo gruppo di risorse o selezionare un gruppo di risorse esistente (se è installata nessuna) e salta al passaggio g riportata di seguito:

        * Fare clic su **Nuovo …**

        * Verrà visualizzata nella finestra di dialogo **Nuovo gruppo di risorse** :

            ![][08]

        * Nella casella di testo **nome** specificare un nome per il nuovo gruppo di risorse.

        * Nel menu di elenco a discesa di **area** , seleziona i dati di Azure appropriati centrare il percorso per il gruppo di risorse.

        * Fare clic su **OK**.

    1. Menu **App servizio pianificare** sono elencati i piani di servizio app associati al gruppo di risorse che è stata selezionata.

        Nota: Un piano di servizio App specifica le informazioni, ad esempio la posizione di un'applicazione Web, il livello dei prezzi e le dimensioni di istanza di calcolo. Un piano di servizio App singola utilizzabile per più Web Apps, motivo per cui viene gestito separatamente da una specifica distribuzione Web App.

        È possibile selezionare un esistente App servizio pianificare (se è installata nessuna) e salta al passaggio h seguito o utilizzare la seguente procedura per creare un piano di servizio nuove App:

        * Fare clic su **Nuovo …**

        * Verrà visualizzato nella finestra di dialogo **Nuovo servizio di App piani** :

            ![][09]

        * Nella casella di testo **nome** specificare un nome per il piano di servizio nuova App.

        * Nell'elenco a discesa menu **posizione** , seleziona i dati di Azure appropriati centrare il percorso per il piano.

        * Nel menu a discesa **Livello prezzi** selezionare prezzi appropriato per il piano. Scopo di verifica è possibile scegliere **gratuito**.

        * **Nell'Istanza di dimensioni** elenco a discesa menu, seleziona la dimensione dell'istanza appropriata per il piano. Scopo di verifica è possibile scegliere **Small**.

    1. Dopo aver completato tutti i passaggi indicati nella finestra di dialogo Nuovo Web App contenitore dovrebbe essere simile nella figura seguente:

        ![][10]

    1. Fare clic su **OK** per completare la creazione del nuovo contenitore Web App.

        Attendere alcuni secondi per l'elenco dei contenitori di Web App per essere aggiornati e il contenitore di app web appena creato a questo punto dovrebbe essere selezionato nell'elenco.

1. A questo punto si è pronti a completare la distribuzione iniziale dell'App Web di Azure:

    ![][11]

    Fare clic su **OK** per distribuire l'applicazione di linguaggio per il contenitore selezionato Web App.

    Nota: Per impostazione predefinita, l'applicazione potrà essere distribuita come sottodirectory del server dell'applicazione. Se si desidera venga distribuito dell'applicazione principale, selezionare la casella di controllo **Distribuisci alla radice** prima di fare clic su **OK**.

1. Successivamente, verrà visualizzata la visualizzazione **Del Registro di attività di Azure** , che indica lo stato di un'App Web di distribuzione.

    ![][12]

    Il processo di distribuzione di un'App Web di Azure dovrebbe eseguire solo alcuni secondi. Quando prodotto pronto per l'applicazione, verrà visualizzato un collegamento denominato **pubblicati** nella colonna **stato** . Quando si fa clic sul collegamento, si passerà alla home page dell'applicazione Web distribuita.

## <a name="updating-your-web-app"></a>Aggiornamento di un'applicazione Web

Aggiornamento di un oggetto esistente in esecuzione Azure Web App è un processo semplice e rapido e sono disponibili due opzioni per l'aggiornamento:

* È possibile aggiornare la distribuzione di un'App Web Java esistente.
* È possibile pubblicare un'applicazione di linguaggio aggiuntiva allo stesso contenitore App Web.

In entrambi i casi, il processo è identico e richiede solo pochi secondi:

1. In Esplora progetti Eclipse rapida dell'applicazione di linguaggio che si desidera aggiornare o aggiungere un contenitore di App Web esistente.

2. Quando viene visualizzato il menu di scelta rapida, selezionare **Azure** e quindi su **Pubblica come Azure Web App...**

3. Dal momento che è già stato effettuato precedenza, si vedrà un elenco dei contenitori di Web App esistenti. Selezionare quello che si desidera pubblicare o pubblicare di nuovo l'applicazione Java in e fare clic su **OK**.

Alcuni secondi in un secondo momento, la visualizzazione **Del Registro di attività di Azure** mostrerà la distribuzione aggiornata come **Published** e sarà possibile verificare l'applicazione aggiornata in un web browser.

## <a name="stopping-an-existing-web-app"></a>Interrompere un'App Web esistente

Per interrompere un Azure Web App contenitore esistente, (incluse tutte le applicazioni Java distribuite in essa), è possibile utilizzare la visualizzazione di **Esplora risorse Azure** .

Se la visualizzazione di **Esplora risorse Azure** non è già aperto, è possibile aprirlo facendo quindi dal menu **finestra** Eclisse, quindi fare clic su **Mostra visualizzazione** **altro …**e quindi **Azure**e quindi scegliere **Esplora Azure**. Se dispone non in precedenza è connesso, verrà chiesto di farlo.

Quando viene visualizzata la visualizzazione di **Esplora risorse di Azure** , usare seguire questa procedura per interrompere l'applicazione Web: 

1. Espandere il nodo **Azure** .

1. Espandere il nodo **Web Apps** . 

1. Fare clic su App Web desiderato.

1. Quando viene visualizzato il menu di scelta rapida fare clic su **Interrompi**.

    ![][13]

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere i collegamenti seguenti:

* [Centro per sviluppatori di linguaggio](/develop/java/).
* [Panoramica di Web App](app-service-web-overview.md)

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[01]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/01-Web-Page.png
[02]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/02-Dynamic-Web-Project.png
[03]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/03-Context-Menu.png
[04]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/04-Log-In-Dialog.png
[05]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/05-Manage-Subscriptions-Dialog.png
[06]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/07-New-Web-App-Container-Dialog.png
[08]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/08-New-Resource-Group-Dialog.png
[09]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/09-New-Service-Plan-Dialog.png
[10]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/11-Completed-Deploy-Dialog.png
[12]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/12-Activity-Log-View.png
[13]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/13-Azure-Explorer-Web-App.png
[14]: ./media/create-a-hello-world-web-app-for-azure-in-eclipse/publishDropdownButton.png