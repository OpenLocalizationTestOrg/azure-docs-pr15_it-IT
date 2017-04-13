<properties
    pageTitle="Il debug di applicazioni Azure in Eclisse"
    description="Informazioni sulle applicazioni Azure debug mediante il Toolkit di Azure per Eclisse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690949.aspx -->

# <a name="debugging-azure-applications-in-eclipse"></a>Il debug di applicazioni Azure in Eclisse #

Usa il Toolkit di Azure per Eclisse, è possibile eseguire il debug le applicazioni se sono in esecuzione in Azure o nell'emulatore di calcolo se si utilizza Windows come il sistema operativo. Nella figura seguente mostra le proprietà di **debug** finestra di dialogo usato per abilitare il debug remoto:

![][ic719504]

In questa esercitazione si presuppone che è già installata un'applicazione che è stata creata correttamente e è familiarità con l'emulatore di elaborazione e la distribuzione di Azure.

Si userà l'applicazione di esercitazione [utilizzando la libreria di Runtime del servizio di Azure in JSP][] come punto di partenza per questo argomento. Prima di procedere, creare l'applicazione se non si è già stato fatto.

## <a name="to-debug-your-application-while-running-in-azure"></a>Per eseguire il debug applicazione mentre è in esecuzione in Azure ##

>[AZURE.WARNING] Supporto corrente del toolkit per il debug linguaggio remoto è destinato principalmente distribuzioni in esecuzione nell'emulatore calcolo Azure. Poiché la connessione di debug non è protetta, non è consigliabile attivare il debug remoto nelle distribuzioni di produzione. Se è necessario eseguire il debug di un'applicazione in esecuzione nel cloud Azure, utilizzare una distribuzione di gestione temporanea, ma tenere presente che l'accesso non autorizzato alla sessione di debug sia possibile se qualcuno conosce l'indirizzo IP della distribuzione cloud, anche se si tratta di una distribuzione di gestione temporanea.

1. Creare un progetto per test nell'emulatore: gestione di progetti di Eclisse In, rapida **MyAzureProject**, fare clic su **proprietà**, fare clic **Azure**e impostare **compilare per** alla **distribuzione nel cloud**.
1. Rigenerare il progetto: dal menu Eclisse, fare clic su **progetto**, quindi fare clic su **Creare tutto**.
1. Distribuire l'applicazione di *gestione temporanea* in Azure.
    >[AZURE.IMPORTANT] Come detto in precedenza, si consiglia di eseguire il debug nell'emulatore di calcolo nella maggior parte dei casi, quindi eseguire il debug nell'ambiente di gestione temporanea solo se è necessaria una debug aggiuntive. È consigliabile non eseguire il debug nell'ambiente di produzione.
1. Quando la distribuzione è pronta in Azure, è possibile ottenere il nome DNS per la distribuzione dal [Portale di gestione Azure][]. Una distribuzione di gestione temporanea ha un nome DNS in forma di http://*&lt;guid&gt;*. cloudapp.net, in cui * &lt;guid&gt; * è un valore GUID assegnato da Azure.
1. In Esplora progetti di Eclisse rapida **WorkerRole1**, fare clic su **Azure**e quindi fare clic su **debug**.
1. Nella finestra di dialogo **proprietà per il debug WorkerRole1** :
    1. Controllare **attivare il debug remoto per questo ruolo.**
    1. Per l' **endpoint di Input da utilizzare**, utilizzare **debug (pubblico: 8090; privato: 8090)**.
    1. Assicurarsi che **JVM avvia in modalità sospensione, in attesa di una connessione di debug** non sia selezionata.
        >[AZURE.IMPORTANT] L'opzione **JVM avvia in modalità sospensione, in attesa di una connessione di debug** è prevista per avanzati scenari nell'emulatore elaborazione solo di debug (non per distribuzioni cloud). Se viene utilizzata l'opzione **JVM avvia in modalità sospensione, in attesa di una connessione di debug** , esso verrà sospendere il processo di avvio del server finché non verrà Eclisse è connesso alla relativa JVM. Sebbene sia possibile utilizzare questa opzione per una sessione di debug utilizzando l'emulatore di calcolo, non utilizzarlo per una sessione di debug in una distribuzione cloud. Inizializzazione del server entrerà in vigore in un'attività di avvio Azure e nel cloud Azure non rendere pubblico endpoint disponibile fino a quando non viene completata l'attività di avvio. Di conseguenza, un processo di avvio non verrà completata correttamente se questa opzione è disponibile in una distribuzione cloud, in quanto non sarà possibile ricevere una connessione da un client Eclisse esterno.
1. Fare clic su **Crea le configurazioni di Debug**.
1. Nella finestra di dialogo **Configurazione di Debug Azure** :
    1. Per **il progetto di linguaggio per eseguire il debug**, selezionare il progetto **MyHelloWorld** .
    1. Per **configurare il debug per**il controllo **cloud Azure (prova)**.
    1. Assicurarsi di **che Azure calcolare emulatore** sia selezionata.
    1. Per **Host**, immettere il nome DNS della distribuzione a fasi, ma senza precedente **http://**. Ad esempio (utilizzare i GUID al posto di GUID illustrato di seguito): **4e616d65 6f6e-6 d 65-6973-526f62657274.cloudapp.net**
1. Fare clic su **OK** per chiudere la finestra di dialogo **Configurazione di Debug Azure** .
1. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà per il debug WorkerRole1** .
1. Se non si dispone di un punto di interruzione già impostato in index.jsp, è necessario impostarla:
    1. All'interno di gestione di progetti di Eclisse, espandere **MyHelloWorld**, espandere **DatiWeb**e fare doppio clic su **index.jsp**.
    1. Pulsante destro del mouse sulla barra blu a sinistra del codice Java index.jsp e fare clic su **Mostra/Nascondi interruzione**, come illustrato di seguito: ![][ic551537]
1. Nel menu del Eclisse, fare clic su **Esegui** , quindi scegliere **Debug configurazioni**.
1. Nella finestra di dialogo **Debug configurazioni** espandere **Remoto applicazione Java** nel riquadro sinistro, selezionare **Azure Cloud (WorkerRole1)**e fare clic su **Debug**.
1. All'interno del browser, eseguire l'applicazione a fasi, **http://***&lt;guid&gt;***.cloudapp.net/MyHelloWorld**, sostituendo il GUID dal nome del DNS per * &lt;guid&gt;*. Se richiesto dalla finestra di dialogo di **Conferma cambia prospettiva** , fare clic su **Sì**. La sessione di debug deve eseguire per la riga di codice in cui è stato impostato il punto di interruzione.

>[AZURE.NOTE] Se si tenta di avviare una remota il debug di connessione a una distribuzione che include più istanze di ruolo in esecuzione, attualmente non è possibile controllare quali istanza verrà verrà inizialmente connesso, come il bilanciamento del carico Azure sceglie un'istanza in modo casuale. Una volta che si è connessi a quell ' istanza, tuttavia, si continuerà debug la stessa istanza. Nota anche se esiste un periodo di inattività di più di 4 minuti (ad esempio, quando è interrotta a un punto di interruzione troppo a lungo), Azure può chiudere la connessione.

## <a name="debugging-a-specific-role-instance-in-a-multi-instance-deployment"></a>Il debug di un'istanza di ruolo specifico in una distribuzione di più istanze ##

Se la distribuzione è in esecuzione nel cloud, è probabile che in uso in più computer, o ruolo, istanze. Consente di sfruttare i vantaggi della garanzia di disponibilità di Azure 99,95% e all'applicazione.

In tali scenari, potrebbe essere necessario quando in modalità remota il debug dell'applicazione Java in un'istanza di ruolo specifico. Tuttavia, se si abilita solo un endpoint di input normale per il debug, il servizio di bilanciamento del carico Azure renderà praticamente per la connessione verrà a un'istanza di ruolo specifico. Se, tuttavia si dovrà connettere è un'istanza del ruolo che specifichi in modo casuale.

Questo è il tipo di scenario in cui a trarre vantaggio da endpoint input istanza renderà più semplice il debug istanza ruolo specifico.

Supponiamo che si prevede di eseguire un massimo di 5 istanze del ruolo della distribuzione. Tramite la pagina delle proprietà **endpoint** nella finestra di dialogo Proprietà ruolo, creare un endpoint input istanza e assegnarla a un intervallo di porte pubbliche, anziché un numero di porta singola. Nella casella **porta pubblica** , ad esempio, specificare **81 85**.

Dopo aver distribuito l'applicazione con questo endpoint istanza, Azure assegna un numero di porta univoci dall'intervallo ognuna delle istanze del ruolo. Per sapere quale istanza se si ha assegnato il numero di porta, quindi, è possibile utilizzare la variabile di ambiente**_PUBLICPORT** *InstanceEndpointName*(dove *InstanceEndpointName* è il nome assegnato al momento della creazione dell'endpoint istanza) configurati automaticamente dal toolkit nella distribuzione (ad esempio, restituendo il valore del piè di pagina di una pagina Web in modo che è possibile leggere quando si visualizza).

Quando si conosce il numero di porta pubblico quell ' istanza è stata assegnata, è possibile farvi riferimento nella configurazione debug in Eclisse, mediante l'apposizione al nome host del servizio. In questo modo verrà Eclisse per connettersi a quell ' istanza e non una delle altre istanze.

## <a name="windows-only-to-debug-your-application-while-running-in-the-compute-emulator"></a>Solo Windows: eseguire il debug applicazione mentre è in esecuzione nell'emulatore di calcolo ##

>[AZURE.NOTE] Azure emulatore è disponibile solo in Windows. Ignorare questa sezione se si usa un sistema operativo diverso da Windows. 

1. Creare un progetto per test nell'emulatore: gestione di progetti di Eclisse In, rapida **MyAzureProject**, fare clic su **proprietà**, fare clic **Azure**e impostare **compilare per** di **Testing nell'emulatore**.
1. Rigenerare il progetto: dal menu Eclisse, fare clic su **progetto**, quindi fare clic su **Creare tutto**.
1. In Esplora progetti di Eclisse rapida **WorkerRole1**, fare clic su **Azure**e quindi fare clic su **debug**.
1. Nella finestra di dialogo **proprietà per il debug WorkerRole1** :
    1. Controllare **attivare il debug remoto per questo ruolo.**
    1. Per l' **endpoint di Input da utilizzare**, utilizzare l'endpoint predefinito generato automaticamente dal toolkit, elencato come **debug (pubblico: 8090; privato: 8090)**.
    1. Assicurarsi che sia selezionata l'opzione **JVM avvia in modalità sospensione, in attesa di una connessione di debug** .
        >[AZURE.IMPORTANT] L'opzione **JVM avvia in modalità sospensione, in attesa di una connessione di debug** è prevista per avanzati scenari nell'emulatore elaborazione solo di debug (non per distribuzioni cloud). Se viene utilizzata l'opzione **JVM avvia in modalità sospensione, in attesa di una connessione di debug** , esso verrà sospendere il processo di avvio del server finché non verrà Eclisse è connesso alla relativa JVM. Sebbene sia possibile utilizzare questa opzione per una sessione di debug utilizzando l'emulatore di calcolo, non utilizzarlo per una sessione di debug in una distribuzione cloud. Inizializzazione del server entrerà in vigore in un'attività di avvio Azure e nel cloud Azure non rendere pubblico endpoint disponibile fino a quando non viene completata l'attività di avvio. Di conseguenza, un processo di avvio non verrà completata correttamente se questa opzione è disponibile in una distribuzione cloud, in quanto non sarà possibile ricevere una connessione da un client Eclisse esterno.
1. Fare clic su **Crea le configurazioni di Debug**.
1. Nella finestra di dialogo **Configurazione di Debug Azure** :
    1. Per **il progetto di linguaggio per eseguire il debug**, selezionare il progetto **MyHelloWorld** .
    1. Per **configurare il debug per**il controllo **Azure calcolare emulatore**.
1. Fare clic su **OK** per chiudere la finestra di dialogo **Configurazione di Debug Azure** .
1. Fare clic su **OK** per chiudere la finestra di dialogo **proprietà per il debug WorkerRole1** .
1. Impostare un punto di interruzione in index.jsp:
    1. All'interno di gestione di progetti di Eclisse, espandere **MyHelloWorld**, espandere **DatiWeb**e fare doppio clic su **index.jsp**.
    1. Pulsante destro del mouse sulla barra blu a sinistra del codice Java index.jsp e fare clic su **Mostra/Nascondi interruzione**, come illustrato di seguito: ![][ic551537]

       Se viene visualizzata un'icona di interruzione all'interno della barra blu a sinistra del codice Java, viene impostato un punto di interruzione.
1. Avviare l'applicazione nell'emulatore di calcolo facendo clic sul pulsante **Esegui nell'emulatore Azure** sulla barra degli strumenti di Azure.
1. Nel menu del Eclisse, fare clic su **Esegui** , quindi scegliere **Debug configurazioni**.
1. Nella finestra di dialogo **Debug configurazioni** espandere **Remoto applicazione Java** nel riquadro sinistro, selezionare **Emulatore Azure (WorkerRole1)**e fare clic su **Debug**.
1. Dopo il calcolo emulatore indica che l'applicazione è in esecuzione, all'interno del browser, eseguito **http://localhost:8080/MyHelloWorld**.
    Se richiesto dalla finestra di dialogo di **Conferma cambia prospettiva** , fare clic su **Sì**.
    La sessione di debug deve eseguire per la riga di codice in cui è stato impostato il punto di interruzione.

Questo è stato illustrato come eseguire il debug in emulatore calcolo; la sezione seguente viene illustrato come eseguire il debug di un'applicazione distribuita in Azure.

## <a name="debugging-notes"></a>Il debug di note ##

* Dopo il debug, è possibile cambiare la prospettiva da **eseguire il Debug** a **Java** tramite facendo clic sul menu del Eclisse, fare clic su **finestra**, **Prospettiva aperta**e selezionando la prospettiva che si desidera utilizzare.
* Per abilitare il debug remoto in GlassFish, non utilizzare la funzionalità di configurazione debug remoto del Toolkit di Azure per Eclisse. Configurare invece GlassFish manualmente. A causa del modo che glassfish considera opzioni Java predefinite in variabili di ambiente, del toolkit remote debug configurazione non funziona correttamente con GlassFish. Se del toolkit remote debug configurazione è attivata, potrebbero impedire la corretta GlassFish venga avviato.

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Utilizzo della libreria di Runtime del servizio Azure in JSP]: http://go.microsoft.com/fwlink/?LinkID=699551

<!-- IMG List -->

[ic719504]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic719504.png
[ic551537]: ./media/azure-toolkit-for-eclipse-debugging-azure-applications/ic551537.png
