<properties
    pageTitle="Abilitazione dell'accesso remoto per distribuzioni Azure in Eclisse"
    description="Informazioni su come abilitare l'accesso remoto per distribuzioni Azure mediante il Toolkit di Azure per Eclisse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Abilitazione dell'accesso remoto per distribuzioni Azure in Eclisse

Per informazioni su come risolvere i problemi di distribuzione, è possibile attivare e utilizzare accesso remoto per connettere la macchina virtuale che ospita la distribuzione. La funzionalità di accesso remoto si basa sul Remote Desktop Protocol (RDP). È possibile configurare l'accesso remoto per la distribuzione dopo averlo creato in Azure o se si usa Eclisse con un sistema operativo Windows, è possibile configurare accesso remoto prima di pubblicare in Azure. Si noti che è necessario un client desktop remoto che è compatibile con il sistema operativo per la connessione a macchina virtuale della distribuzione di Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Come abilitare l'accesso remoto prima di distribuire in Azure

> [AZURE.NOTE] Per abilitare l'accesso remoto prima di distribuire all'applicazione di Azure, è necessario essere in esecuzione Eclisse su Windows.

Nella figura seguente mostra le proprietà di **Accesso remoto** finestra di dialogo utilizzata per abilitare l'accesso remoto.

![][ic719494]

Esistono due modi per visualizzare la finestra di dialogo proprietà di **Accesso remoto** :

* Fare clic sul collegamento **Avanzate** nella sezione **Accesso remoto** della finestra di dialogo **Pubblica su Azure** .
* Aprire la finestra di dialogo **proprietà** del progetto Azure.

Quando si crea un nuovo progetto di distribuzione di Azure, il progetto non avranno accesso remoto attivata per impostazione predefinita. Tuttavia, è possibile attivare facilmente accesso remoto specificando il nome utente e la password nella finestra di dialogo **Pubblica su Azure** . La password di accesso remoto è crittografata tramite certificati x. 509. Se non si utilizza il proprio certificato di fornire la crittografia si basa su un certificato autofirmato fornito con il plug-in Azure per Eclisse. Questo certificato autofirmato sia presente nella cartella **della diapositiva** del progetto Azure, memorizzato come un file di certificato pubblico (SampleRemoteAccessPublic.cer) e come un file di certificato informazioni PFX (Personal Exchange) (SampleRemoteAccessPrivate.pfx). La seconda sezione contiene la chiave privata per il certificato e ha una password predefinita, **Password1**. Poiché questa password è pubblico, il certificato predefinito deve essere utilizzato solo per l'apprendimento multiple e non per una distribuzione di produzione. In modo diverso da scopo didattico, quando si desidera attivare sessioni remote per le distribuzioni, scegliere il collegamento **Avanzate** nella finestra di dialogo **Pubblica su Azure** per specificare il proprio certificato. Si noti che è necessario caricare la versione PFX del certificato per il servizio ospitato all'interno del portale di gestione di Azure, in modo che tale Azure è in grado di decrittografare la password dell'utente.

Il resto dell'esercitazione viene illustrato come abilitare l'accesso remoto per un progetto di distribuzione Azure inizialmente creato con accesso remoto disattivato. Per motivi di questa esercitazione, verrà creato un nuovo certificato autofirmato e il relativo file. pfx avrà una password di propria scelta. È inoltre la possibilità di usare un certificato emesso da un'autorità di certificazione.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Come abilitare l'accesso remoto dopo la distribuzione di Azure

Per abilitare l'accesso remoto dopo la distribuzione di Azure, utilizzare la procedura seguente:

1. Accedere al portale di gestione Azure con il proprio account Azure
1. Nell'elenco dei **Servizi Cloud**, selezionare il servizio cloud distribuito
1. Nella pagina web servizio cloud, fare clic sul collegamento **Configura**
1. Nella parte inferiore della pagina di configurazione, fare clic sul collegamento **remoto**
1. Quando viene visualizzata la finestra di dialogo popup:
    * Specificare il ruolo per il quale si desidera Abilita l'accesso remoto
    * Fare clic per selezionare la casella di controllo **Attiva Desktop remoto**
    * Specificare un nome utente e la password che si desidera utilizzare per l'accesso remoto
    * Selezionare il certificato da utilizzare
1. Fare clic su **OK** 

Verrà visualizzato un messaggio che indica che la modifica di configurazione in corso, che potrebbero essere necessari alcuni minuti. Dopo aver completato la modifica di configurazione, seguire i passaggi indicati nella sezione **agli utenti di accedere in remoto** più avanti in questo articolo.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Come abilitare l'accesso remoto nel pacchetto

1. Riquadro progetti del Eclipse mouse sul progetto Azure e scegliere **proprietà**.

1. Nella finestra di dialogo **proprietà** , espandere **Azure** nel riquadro a sinistra e fare clic su **Accesso remoto**.

1. Nella finestra di dialogo **Accesso remoto** , verificare **che abilitare tutti i ruoli accettare le connessioni Remote Desktop con le credenziali di accesso** sia selezionata.

1. Specificare un nome utente per la connessione Desktop remoto.

1. Specificare e confermare la password per l'utente. Il nome utente e password specificati impostato in questa finestra di dialogo verrà utilizzato quando si effettua una connessione Desktop remoto. Si noti che si tratta di una password diversa da quella di PFX.

1. Specificare la data di scadenza per l'account utente.

1. Fare clic su **Nuovo** per creare un nuovo certificato autofirmato. (In alternativa, è possibile selezionare un certificato dal sistema dell'area di lavoro o un file tra i pulsanti **dell'area di lavoro** o **file System** , rispettivamente, ma per scopi di questa esercitazione verrà creato un nuovo certificato.)

    * Nella finestra di dialogo **Nuovo certificato** specificare e confermare la password che per il file PFX in cui si userà.

    * Accettare il valore specificato per **Nome (CN)**o utilizzare un nome personalizzato.

    * Specificare il percorso e il nome in cui verrà salvato il nuovo certificato, nel modulo CER. Per questo passaggio e il passaggio successivo, è possibile utilizzare la cartella **della diapositiva** del progetto di Azure, ma si è gratuito scegliere un'altra posizione. Per motivi di questa esercitazione, si userà **c:\mycert\mycert.cer**. (Creare la cartella **c:\mycert** prima di procedere, o se lo si desidera utilizzare una cartella esistente).

    * Specificare il percorso e il nome in cui verrà salvati il nuovo certificato e la chiave privata, nel modulo pfx. Per motivi di questa esercitazione, si userà **c:\mycert\mycert.pfx**. La finestra di dialogo **Nuovo certificato** dovrebbe essere simile al seguente (aggiornare i percorsi delle cartelle, se non si utilizza **c:\mycert**):

        ![][ic712275]

    * Fare clic su **OK** per chiudere la finestra di dialogo **Nuovo certificato** .

1. La finestra di dialogo di **Accesso remoto** dovrebbe essere simile al seguente:</p>

    ![][ic719495]

1. Fare clic su **OK** per chiudere la finestra di dialogo di **Accesso remoto** .
    
Rigenerare l'applicazione con la compilazione impostata per la pubblicazione nel cloud.

## <a name="to-log-in-remotely"></a>Per accedere modalità remota

Quando l'istanza del ruolo è pronto, possono accedere remoto alla macchina virtuale che ospita l'applicazione.

* Se usano Eclisse Windows ed è stata selezionata l'opzione **Avvia desktop remoto distribuire** durante la distribuzione di Azure, verrà visualizzata con una schermata di accesso di connessione Desktop remoto all'avvio di distribuzione. Quando viene chiesto di immettere il nome utente e la password, immettere i valori specificati per l'utente remoto e sarà possibile agli utenti di accedere.

* È possibile accedere modalità remota tramite il <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portale di gestione Azure</a>:

    * All'interno della visualizzazione di **Servizi Cloud** del portale di gestione di Azure, fare clic su servizio cloud, fare clic su **istanze**, fare clic su una specifica istanza e quindi fare clic sul pulsante **Connetti** . Pulsante **Connetti** viene visualizzato il seguente nella barra dei comandi:

        ![][ic659273]

    * Dopo aver fatto clic sul pulsante **Connetti** , verrà richiesto di aprire un file RDP. Aprire il file e seguire le istruzioni visualizzate. (Si può anche salvare il file nel computer locale e quindi eseguire il file facendo doppio clic in remoto al log alla macchina virtuale senza dover accedere prima di tutto il portale di gestione.)

    * Quando viene chiesto di immettere il nome utente e la password, immettere i valori specificati per l'utente remoto e sarà possibile agli utenti di accedere.

> [AZURE.NOTE] Se utilizza un sistema operativo non Windows, è necessario utilizzare un client Desktop remoto compatibile con il sistema operativo e seguire i passaggi per configurare il client con le impostazioni nel file RDP che è stato scaricato.

## <a name="see-also"></a>Vedere anche

[Azure Toolkit per Eclisse][]

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

[Installare il Toolkit di Azure per Eclisse][] 

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png
