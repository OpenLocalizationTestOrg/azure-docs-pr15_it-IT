<properties
    pageTitle="Creare un servizio Cloud World Hello per Azure in Eclisse"
    description="Informazioni su come creare un'applicazione Hello World semplice utilizzando il Toolkit di Azure per Eclisse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690944.aspx -->

# <a name="create-a-hello-world-cloud-service-for-azure-in-eclipse"></a>Creare un servizio Cloud World Hello per Azure in Eclisse #

La procedura seguente viene illustrato come creare e distribuire un'applicazione JSP base in Azure mediante il Toolkit di Azure per Eclisse. È illustrato un esempio JSP per semplicità, ma una procedura molto simile sarebbe appropriata per un servlet Java per quanto riguarda la distribuzione di Azure.

L'applicazione di un aspetto simile al seguente:

![][ic600360]

## <a name="prerequisites"></a>Prerequisiti ##

* Un linguaggio sviluppo Kit (JDK), v 1.7 o versione successiva.
* Eclipse IDE per sviluppatori Java Edition, il o versione successiva. Può essere scaricato dal <http://www.eclipse.org/downloads/>.
* Distribuzione di un server web basate sul linguaggio o applicazione, ad esempio Apache Tomcat, GlassFish, server applicazioni JBoss, alla banchina o IBM® WebSphere® applicazione Server libertà Core.
* Un abbonamento Azure, può essere ottenuto tramite <http://azure.microsoft.com/pricing/purchase-options/>.
* Toolkit di Azure per Eclisse. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per Eclisse][].

## <a name="to-create-a-hello-world-application"></a>Per creare un'applicazione Hello World ##

Prima di tutto, iniziamo con la creazione di un progetto di linguaggio.

*  Avviare Eclisse e nel menu fare clic su **File**, fare clic su **Nuovo**e quindi fare clic su **Project Web dinamico**. (Se non è visibile **Dinamico progetto Web** elencato come un progetto disponibile dopo facendo clic su **File**, **Nuovo**, quindi eseguire le operazioni seguenti: fare clic su **File**, fare clic su **Nuovo**, fare clic su **progetto...**, espandere **Web**, fare clic su **Progetto Web dinamico**e fare clic su **Avanti**.)
*  Per motivi di questa esercitazione, nome del progetto **MyHelloWorld**. (Assicurarsi che quando si utilizza il nome, i passaggi successivi in questa esercitazione prevede che il file WAR in chiamarsi MyHelloWorld). Lo schermo apparirà simile al seguente: ![][ic589576]
* Fare clic su **Fine**.
* In visualizzazione di Esplora file di progetto del Eclisse, espandere **MyHelloWorld**. Pulsante destro del mouse **DatiWeb**, fare clic su **Nuovo**e quindi fare clic su **File JSP**.
* Nella finestra di dialogo **Nuovo File JSP** , assegnare un nome di file **index.jsp**. Tenere presente nella cartella padre **MyHelloWorld/DatiWeb**, come illustrato di seguito:  ![][ic659262]
* Nella finestra di dialogo **Seleziona modello JSP** , ai fini di questa esercitazione selezionare **Nuovo File JSP (html)** e fare clic su **Fine**.
* Quando si apre il file index.jsp in Eclisse, aggiungere del testo da visualizzare in modo dinamico **Hello World!** all'interno di esistente `<body>` elemento. L'aggiornamento `<body>` contenuto deve essere visualizzato il seguente:
```
    <body>
    <b><% out.println("Hello World!"); %></b>
    </body>
```
* Salvare index.jsp.

## <a name="to-deploy-your-application-to-azure-the-quick-and-simple-way"></a>Per distribuire l'applicazione di Azure, il modo semplice e rapido ##

Non appena si dispone di un'applicazione web Java possibile eseguire il test, è possibile utilizzare il collegamento seguente per provare direttamente nel cloud Azure.

1. In Gestione di progetti di Eclisse, fare clic su **MyHelloWorld**.
1. Nella barra degli strumenti Eclisse, fare clic su pulsante a discesa **pubblica** e quindi fare clic su **Pubblica come Azure servizio Cloud**
    ![][publishDropdownButton]
1. Se si esegue la pubblicazione dell'applicazione in Azure per la prima volta, non è stato creato un progetto di distribuzione Azure dell'applicazione prima di un progetto di distribuzione Azure essere creati automaticamente. Verrà visualizzato il messaggio seguente, in cui sono elencati anche il pacchetto JDK e il server di applicazione che verrà distribuito automaticamente per eseguire l'applicazione.
    ![][ic789598]

    In questo modo di scelta rapida un modo semplice e rapido testare l'applicazione in Azure senza che sia necessario configurare un server specifico o JDK è diversa da quelle predefinite. Se si è soddisfatti con le impostazioni predefinite, è possibile fare clic su **OK** per continuare con la procedura seguente.
    Tuttavia, se si desidera modificare JDK o applicazione server da utilizzare per l'applicazione, è possibile farlo in un secondo momento modificando il progetto di distribuzione Azure che è stato creato automaticamente, oppure fare clic su **Annulla** e leggere la **sezione progetti di Azure sulla distribuzione** di questa esercitazione.
1. Nella finestra di dialogo **Pubblica su Azure** :
    1. Se sono presenti sottoscrivere selezionare nell'elenco di **sottoscrizione** ancora, seguire questa procedura per importare le informazioni sull'abbonamento:
        1. Fare clic su **Importa da file di impostazioni di pubblicazione**.
        1. Nella finestra di dialogo **Informazioni sull'abbonamento di importazione** , fare clic su **Scarica File di impostazioni di pubblicazione**. Se non si è ancora stato effettuato l'accesso all'account Azure, verrà richiesto agli utenti di accedere. Quindi verrà richiesto di salvare un Azure pubblicare file di impostazioni. Salvarlo nel computer locale.
        1. Ancora presente nella finestra di dialogo **Informazioni sull'abbonamento di importazione** , fare clic sul pulsante **Sfoglia** , selezionare il file di impostazioni di pubblicazione che salvato localmente nel passaggio precedente e quindi fare clic su **Apri**. La schermata dovrebbe essere simile al seguente: ![][ic644267]
        1. Fare clic su **OK**.
    1. Per l' **abbonamento**, selezionare l'abbonamento che si desidera utilizzare per la distribuzione.
    1. Per **account di archiviazione**, selezionare l'account di archiviazione che si desidera utilizzare oppure fare clic su **Nuovo** per creare un nuovo account di archiviazione.
    1. Per **nome del servizio**, selezionare il servizio cloud che si desidera utilizzare oppure fare clic su **Nuovo** per creare un nuovo servizio cloud.
    1. Per **OS di destinazione**, selezionare la versione del sistema operativo che si desidera utilizzare per la distribuzione.
    1. Per l' **ambiente di destinazione**, ai fini di questa esercitazione, selezionare **gestione temporanea**. (Quando si è pronti per distribuire il sito di produzione, è necessario modificare l'impostazione di **produzione**.)
    1. Facoltativo: Verificare che sia selezionata **sovrascrivere distribuzione precedente** se si desidera che la nuova distribuzione sovrascrivere automaticamente la distribuzione precedente. Se si seleziona questa opzione, sarà necessario non problemi esperienza "409 conflitto" durante la pubblicazione nella stessa posizione.
        Si noti che la finestra di dialogo **Pubblica su Azure** contiene una sezione per **L'accesso remoto**. Per impostazione predefinita, l'accesso remoto non è attivato e verrà non abilitato per questo esempio. Per abilitare l'accesso remoto, immettere un nome utente e la password da utilizzare quando si accede in remoto. Per ulteriori informazioni sull'accesso remoto, vedere [Attivazione dell'accesso remoto per le distribuzioni di Azure in Eclisse][].
        La finestra di dialogo **Pubblica su Azure** apparirà simile al seguente: ![][ic719488]
1. Fare clic su **pubblica** per pubblicare l'ambiente di gestione temporanea.
    Quando viene richiesto di eseguire una generazione completa, fare clic su **Sì**. Questa operazione può richiedere alcuni minuti per la prima compilazione.
    Nella sezione visualizzazioni Eclisse a schede verrà visualizzato un **Registro attività Azure** .
    ![][ic719489]
   È possibile usare il registro, come la visualizzazione **della Console** per visualizzare l'avanzamento della distribuzione. In alternativa è possibile accedere al [Portale di gestione Azure][]e usare la sezione **Servizi Cloud** per controllare lo stato.
1. Quando la distribuzione verrà distribuita correttamente, il **Registro attività Azure** Visualizza stato **pubblicato**. Fare clic su **Published**, come illustrato nell'immagine seguente e nel browser verrà visualizzata un'istanza della distribuzione.
    ![][ic719490]

Poiché si tratta di una distribuzione in un ambiente di gestione temporanea, il nome DNS sarà nel formato http://&lt;*guid*&gt;. cloudapp.net e verrà URL contengono il nome DNS più un suffisso per l'applicazione. Ad esempio http://447564652c20426f6220526f636b7321.cloudapp.net/MyHelloWorld. (La parte **MyHelloWorld** è maiuscole e minuscole) È anche possibile visualizzare il nome DNS se si fa clic sul nome di distribuzione nel portale di gestione della piattaforma Azure (all'interno della parte di servizi Cloud del portale di gestione).

Anche se è stata di seguito sono illustrati per una distribuzione nell'ambiente di gestione temporanea, una distribuzione di produzione segue gli stessi passaggi, tranne nella finestra di dialogo **Pubblica su Azure** , selezionare **produzione** invece **di gestione temporanea** per l' **ambiente di destinazione**. Una distribuzione di produzione genera un URL in base al nome DNS preferito, anziché un GUID usato per la gestione temporanea.

>[AZURE.WARNING] A questo punto è stato distribuito l'applicazione Azure nel cloud. Tuttavia, prima di procedere, tenere presente che un'applicazione distribuita, anche se non è in esecuzione, continuerà a attribuzione fatturabili per l'abbonamento. È fondamentale eliminare distribuzioni indesiderate dall'abbonamento Azure.

## <a name="about-azure-deployment-projects"></a>Informazioni sui progetti di distribuzione di Azure ##

Per distribuire una o più applicazioni Java in Azure, è necessario un progetto di distribuzione di Azure. Il ruolo di "pacchetto" che le applicazioni devono essere racchiusi in per essere pubblicata in Azure.

Oltre alle informazioni relative alle applicazioni, un progetto di distribuzione Azure contiene anche informazioni sugli altri componenti principali della distribuzione, soprattutto: il contenitore del server di applicazione per l'esecuzione di un'applicazione web in e runtime del linguaggio per eseguire la registrazione in. Azure supporta una vasta gamma di runtime Java e i server di applicazioni Java che è possibile scegliere tra.

Sebbene nell'esempio riportato di seguito utilizzato è notevolmente semplificata a scopo didattico, un progetto di distribuzione Azure contenere anche altre informazioni di configurazione importanti che consentono di creare servizi cloud quasi altamente complessi, scalabilità, disponibilità di più livelli con le applicazioni. È possibile abilitare **affinità sessione ("sessioni permanenti")**, **rapidamente la memorizzazione nella cache**, **il debug remoto**, **SSL scaricando**, **routing/porta firewall**, **accesso remoto**e un numero di altre funzionalità potenti.

Se è stata completata la sezione precedente di questa esercitazione ("per distribuire l'applicazione di Azure, il modo semplice e rapido"), si verrà visualizzato un nuovo progetto di distribuzione Azure in Gestione progetti generate automaticamente e denominato "**MyHelloWorld_onAzure**".

È possibile anche avviata questa esercitazione creando innanzitutto un progetto di distribuzione di Azure vuoto e quindi aggiungere le applicazioni. È il processo, ma fornisce un maggiore controllo la configurazione iniziale dall'inizio.

Per creare un nuovo progetto di distribuzione Azure da zero, fare clic sul pulsante **Nuovo progetto di distribuzione di Azure** ![][ic710876].

Indipendentemente da se si lavora con un progetto di distribuzione Azure già esistente o crearne uno da zero, in grado di modificare le impostazioni di configurazione e componenti, ad esempio JDK o server applicazioni identico facilmente in qualsiasi momento.

Per modificare JDK, o il server di applicazione o l'elenco di applicazioni in un progetto di distribuzione Azure:

1. Espandere il nodo del progetto (ad esempio **MyHelloWorld_onAzure**) in Gestione progetti
2. Pulsante destro del mouse **WorkerRole1**
3. Espandere il sottomenu strumenti **Azure** nel menu di scelta rapida
4. Fare clic su **configurazione Server**

Indipendentemente che si inizi questa procedura di configurazione del server per la modifica di un progetto di distribuzione di Azure, come illustrato sopra o crearne uno nuovo da zero, verrà visualizzato lo stesso tipo di finestre di dialogo che consente di configurare i JDK, componenti server e dell'applicazione. Per altre informazioni su come modificare le impostazioni nelle finestre di dialogo, ad esempio cambiare JDK, il server di applicazioni e aggiungere o rimuovere le applicazioni in una distribuzione, vedere l'articolo di [proprietà di configurazione Server][] .

## <a name="windows-only-to-deploy-your-application-to-the-compute-emulator"></a>Solo Windows: per distribuire l'applicazione all'emulatore di calcolo ##

>[AZURE.NOTE] Azure emulatore è disponibile solo in Windows. Ignorare questa sezione se si usa un sistema operativo diverso da Windows.

Se è stato creato un nuovo progetto di distribuzione Azure seguendo la procedura descritta in precedenza, ad esempio, in modo implicito, tramite la pubblicazione dell'applicazione in Azure, JDK e applicazione server sono stati configurati per il cloud, ma non per emulazione locale. Per preparare il progetto test nell'emulatore locale, procedere come segue:

1. In Gestione di progetti di Eclisse, fare clic su **MyHelloWorld_onAzure**.
1. Fare clic su **WorkerRole1**.
1. Espandere il sottomenu strumenti **Azure** nel menu di scelta rapida.
1. Fare clic su **configurazione Server**.
1. Nella scheda **JDK** verificare se il toolkit pre-ha configurato predefinito JDK locali dell'utente. In caso contrario, o se si desidera modificare le impostazioni predefinite presunte, assicurarsi che sia selezionata la casella di controllo **Usa JDK dal percorso di file per il testing in locale** e viene specificato il percorso di installazione JDK che si desidera utilizzare. Se si vuole cambiarla, fare clic sul pulsante **Sfoglia** e tramite il controllo Sfoglia, selezionare il percorso della directory di JDK da utilizzare.
1. Fare clic sulla scheda **Server** .
1. Nella casella di testo **percorso del server locale** nella parte inferiore della finestra di dialogo, immettere il percorso di un server installato in locale che corrisponde al tipo e il numero di versione principale di server selezionato nella parte superiore della finestra di dialogo sotto la casella di controllo di **distribuire un server di questo tipo** . Se si desidera utilizzare un tipo diverso o una versione principale del server di applicazioni, modificare la selezione in questa casella di controllo prima di tutto.
1. Fare clic su **OK**.
1. Nella barra degli strumenti Eclisse, fare clic sul pulsante **Esegui nell'emulatore Azure** ![][ic710879]. Se il pulsante **Esegui nell'emulatore Azure** non è attivato, assicurarsi che **MyHelloWorld_onAzure** sia selezionato nella gestione di progetti di Eclisse e verificare che Gestione di progetti di Eclisse con lo stato attivo della finestra corrente. Verrà prima avviare una generazione completa del progetto e quindi avviare l'applicazione web Java nell'emulatore di calcolo. (Si noti che, a seconda delle caratteristiche di prestazioni del computer, la prima compilazione può richiedere tra alcuni secondi per alcuni minuti, ma generazioni successive otterrà più velocemente.) Dopo aver completato il primo passaggio di compilazione, verrà richiesto da Windows controllo (dell'account) per consentire a questo comando apportare modifiche al computer. Fare clic su **Sì**.

>[AZURE.IMPORTANT] Se non viene visualizzata la richiesta di controllo dell'account utente, controllare la barra delle applicazioni di Windows per l'icona dell'account e scegliere prima di tutto. Può succedere che il controllo dell'account utente richiesta non viene visualizzato come una finestra in primo piano, ma è visibile solo come icona sulla barra delle applicazioni.

1. Esaminare l'output dell'elaborazione emulatore dell'interfaccia utente per determinare se ci sono problemi con il progetto. A seconda del contenuto della distribuzione, potrebbe richiedere qualche minuti per l'applicazione essere avviato completamente interno emulatore di calcolo.
1. Avviare il browser e utilizzare l'URL `http://localhost:8080/MyHelloWorld` come indirizzo (il `MyHelloWorld` parte dell'URL è maiuscole e minuscole). Verrà visualizzata l'applicazione di MyHelloWorld (l'output di index.jsp), come nella figura seguente: ![][ic589579]

Quando si è pronti interrompere l'applicazione in esecuzione nell'emulatore di calcolo, nella barra degli strumenti Eclisse, fare clic sul pulsante **Reimposta emulatore Azure** ![][ic710880].

## <a name="to-delete-your-deployment"></a>Per eliminare la distribuzione ##

Per eliminare la distribuzione all'interno del Toolkit di Azure per Eclisse, verificare che sia selezionato **MyHelloWorld_onAzure** in Esplora progetti di Eclisse, assicurarsi che la gestione di progetti Eclipse abbia la finestra corrente focalizzare l'attenzione e quindi fare clic sul pulsante **Annulla pubblicazione** ![][ic710883], nella barra degli strumenti Eclisse. (Impossibile eseguire la stessa operazione facendo clic su **MyHelloWorld_onAzure** nella gestione di progetti di Eclisse, facendo clic su **Azure** e quindi fare clic su **Rimuovi dal Cloud Azure**.) Verrà visualizzata la finestra di dialogo **Progetto Azure annullare la pubblicazione** .

![][ic719491]

Selezionare il servizio di abbonamento e cloud che contiene la distribuzione, selezionare la distribuzione che si desidera eliminare e quindi fare clic su **Annulla pubblicazione**.

(Un'alternativa all'utilizzo del toolkit per eliminare la distribuzione consiste nell'usare la sezione **Servizi Cloud** del portale di gestione di Azure: passare alla distribuzione, selezionarlo e quindi fare clic sul pulsante **Elimina** . Si interrompe e quindi eliminare la distribuzione. Se si desidera interrompere la distribuzione e ne comporta l'eliminazione, fare clic sul pulsante **Interrompi** anziché il pulsante **Elimina** , ma come detto in precedenza, se non si elimina la distribuzione, in base alle tariffe fatturabili continuerà a accumulare per la distribuzione anche se è stato arrestato).

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

[Novità di Azure Toolkit per Eclisse][]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Role Properties]: http://go.microsoft.com/fwlink/?LinkID=699525
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Abilitazione dell'accesso remoto per distribuzioni Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699538
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Proprietà di configurazione server]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Novità di Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic589576]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589576.png
[ic589579]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic589579.png
[ic600360]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic600360.png
[ic644267]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic644267.png
[ic659262]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic659262.png
[ic710876]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710876.png
[ic710879]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710880.png
[ic710882]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic710883.png
[ic719488]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719488.png
[ic719489]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719489.png
[ic719490]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719490.png
[ic719491]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic719491.png
[ic789598]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/ic789598.png
[publishDropdownButton]: ./media/azure-toolkit-for-eclipse-creating-a-hello-world-application/publishDropdownButton.png
