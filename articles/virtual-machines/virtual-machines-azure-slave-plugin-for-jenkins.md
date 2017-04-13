<properties
    pageTitle="Come utilizzare secondario Azure plug-in con integrazione continua Jenkins | Microsoft Azure"
    description="Viene descritto come utilizzare il plug-in secondario Azure con integrazione continua Jenkins."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Come utilizzare secondario Azure plug-in con integrazione continua Jenkins

È possibile utilizzare secondario Azure plug-in per Jenkins a disposizione i nodi secondari in Azure in fase di esecuzione distribuito compilazione.

## <a name="install-the-azure-slave-plug-in"></a>Installare il plug-in secondario Azure

1. Nel dashboard Jenkins, fare clic su **Gestisci Jenkins**.

1. Nella pagina **Gestisci Jenkins** , fare clic su **Gestisci plug-in**.

1. Fare clic sulla scheda **disponibile** .

1. Nel campo filtro sopra l'elenco dei plug-in disponibili digitare **Azure** per limitare l'elenco pertinenti plug-in.

    Se è scelto di scorrere l'elenco dei plug-in disponibili, si noterà secondario Azure plug-nella sezione **Gestione Cluster e distribuito creare** .

1. Selezionare la casella di controllo **Plug-in Azure secondario** .

1. Fare clic su **installazione senza riavvio** o **Download e installazione dopo il riavvio**.

Dopo l'installazione del plug-in, i passaggi successivi sono per configurare il plug-in con il profilo di sottoscrizione Azure e creare un modello che verrà utilizzato per la creazione della macchina virtuale per il nodo secondario.


## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurare secondario Azure plug-in con il tuo profilo

Un profilo di sottoscrizione, noto anche come pubblica impostazioni, è un file XML che contiene le credenziali sicure e alcune informazioni aggiuntive che è necessario per lavorare con Azure l'ambiente di sviluppo. Per configurare secondario Azure plug-in, è necessario:

* L'id di abbonamento
* Certificato di gestione dell'abbonamento

Questi sono disponibili nel [profilo di sottoscrizione]. Di seguito è illustrato un esempio di un profilo di sottoscrizione.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Dopo aver ottenuto il tuo profilo, seguire questa procedura per configurare secondario Azure plug-in:

1. Nel dashboard Jenkins, fare clic su **Gestisci Jenkins**.

1. Fare clic su **Configura sistema**.

1. Scorrere verso il basso della pagina per individuare la sezione **Cloud** .

1. Fare clic su **aggiungere nuovi cloud > Microsoft Azure**.

    ![sezione cloud][cloud section]

    Seguente illustra i campi in cui è necessario immettere i dettagli dell'abbonamento.

    ![configurazione della sottoscrizione][subscription configuration]

1. Copiare i valori di certificato id e la gestione di sottoscrizione il tuo profilo e incollarli nei campi appropriati.

    Quando si copia il certificato di id e la gestione di abbonamento, non includere le offerte di racchiudono i valori.

1. Fare clic su **Verifica configurazione**.

1. Quando la configurazione viene verificata che sia corretto, fare clic su **Salva**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurare modelli di macchine virtuali per secondario Azure plug-in

Modelli di macchine virtuali definisce i parametri che il plug-in verrà utilizzato per creare un nodo secondario in Azure. Nei passaggi seguenti, è necessario creare un modello per una macchina virtuale Ubuntu.

1. Nel dashboard Jenkins, fare clic su **Gestisci Jenkins**.

1. Fare clic su **Configura sistema**.

1. Scorrere verso il basso della pagina per individuare la sezione **Cloud** .

1. Nella sezione **Cloud** trovare **Aggiungere Azure macchina virtuale modello**e quindi fare clic su **Aggiungi**.

    ![Aggiungere macchine virtuali modello][add vm template]

    Seguente illustra i campi in cui è immettere i dettagli relativi al modello che si sta creando.

    ![configurazione generale vuota][blank general configuration]

1. Nella casella **nome** immettere un nome di servizio cloud Azure. Se il nome immesso si riferisce a un servizio cloud esistente, la macchina virtuale eseguire il provisioning di tale servizio. Azure in caso contrario, verrà creato uno nuovo.

1. Nella casella **Descrizione** immettere il testo che descrive il modello che si sta creando. Questo è valido solo per i record e non viene utilizzato per il provisioning di una macchina virtuale.

1. Nella casella **etichette** utilizzata per identificare il modello che si sta creando e successivamente utilizzata per il modello di fare riferimento durante la creazione di un processo Jenkins. Per il nostro obiettivo immettere **linux** in questa casella.

1. Nell'elenco **Opzioni internazionali** fare clic sull'area in cui verrà creata la macchina virtuale.

1. Nell'elenco **Formato macchina virtuale** , fare clic su dimensione appropriata.

1. Nella casella **Nome dell'Account di archiviazione** , specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che siano presenti nell'area stesso come servizio cloud che si inizierà a usare. Se si desidera creare nuovo archiviazione, è possibile lasciare vuota la casella.

1. Tempo di conservazione specifica il numero di minuti prima Jenkins Elimina un secondario inattivo. Lasciare il valore predefinito di 60. È anche possibile scegliere arrestare secondario anziché eliminarlo quando è inattivo. A tale scopo, selezionare la casella di controllo **Arresto solo (non eliminare) dopo il tempo di conservazione** .

1. Selezionare la condizione appropriata nell'elenco **l'utilizzo** quando verrà utilizzato il nodo secondario. Per ora, fare clic su **questo nodo il più possibile utilizzare**.

    A questo punto, il modulo dovrebbe essere simile alla seguente:

    ![configurazione di modello generale di verifica][checkpoint general template config]

    Il passaggio successivo consiste nel fornire dettagli sull'immagine del sistema operativo che si desidera il secondario venga creato.

1. Nella casella degli strumenti **immagine famiglia o Id** , è necessario specificare quali immagine del sistema verrà installato nel computer virtuale. È possibile selezionare da un elenco delle famiglie di immagine oppure specificare un'immagine personalizzata.

    Se si desidera selezionare da un elenco delle famiglie di immagine, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia immagine. Ad esempio, digitare **U** verrà visualizzato un elenco delle famiglie Ubuntu Server. Dopo aver selezionato dall'elenco, Jenkins utilizzerà la versione più recente di tale immagine di sistema da tale gruppo durante il provisioning di macchina virtuale.

    ![Esempio di elenco immagine del sistema operativo][OS Image list sample]

    Se si dispone di un'immagine personalizzata che si desidera utilizzare in alternativa, immettere il nome di tale immagine personalizzata. I nomi immagine personalizzata non vengono visualizzati in un elenco, è necessario assicurarsi che il nome immesso correttamente.

    Per questa esercitazione, digitare **U** per visualizzare un elenco delle immagini Ubuntu e quindi fare clic su **Ubuntu Server 14.04 risultati**.

1. Nell'elenco **Metodo di avvio veloce** fare clic su **SSH**.

1. Copiare lo script seguente e incollarlo nella casella **Inizializzazione Script** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    Verrà eseguito lo script di inizializzazione dopo aver creata la macchina virtuale. In questo esempio, lo script installa Java, fra e ant.

1. Nelle caselle **nome utente** e **Password** , immettere i valori preferiti per l'account di amministratore che verrà creato nel computer virtuale.

1. Fare clic su **Modello di verifica** per controllare se i parametri specificati sono validi.

1. Fare clic su **Salva**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Creare un processo Jenkins che viene eseguito su un nodo secondario in Azure

In questa sezione, si sta creando un'attività Jenkins che verrà eseguita su un nodo secondario in Azure. È necessario disporre di un progetto sulla GitHub a seguirli.

1. Nel dashboard Jenkins, fare clic su **Nuovo elemento**.

1. Immettere un nome per l'attività che si sta creando.

1. Per il tipo di progetto, fare clic su **progetto Freestyle**.

1. Fare clic su **Ok**.

1. Nella pagina di configurazione delle attività, selezionare **Limita nel punto in cui è possibile eseguire il progetto**.

1. Nella casella **Espressione etichetta** immettere **linux**. Nella sezione precedente, viene creato un modello secondario è denominato **linux**, che corrisponde a qual caso si specifica.

1. Nella sezione **creare** fare clic su **Aggiungi compilazione passaggio** e selezionare **Execute shell**.

1. Modificare lo script seguente sostituendo **(il nome dell'account GitHub)**, **(il nome del progetto)**e **(directory del progetto)** con i valori appropriati e incollare lo script modificato nell'area di testo che viene visualizzato.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Fare clic su **Salva**.

1. Nel dashboard Jenkins al passaggio del mouse sull'attività che appena creato e fare clic sulla freccia in giù per visualizzare le opzioni di attività.

1. Fare clic su **creare ora**.

Jenkins verranno quindi creare un nodo secondario utilizzando il modello creato nella sezione precedente ed eseguire lo script specificato nel passaggio genera per questa attività.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: https://azure.microsoft.com/develop/java/
[profilo di sottoscrizione]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png