<properties
    pageTitle="Come utilizzare secondario Azure plug-in con integrazione continua Hudson | Microsoft Azure"
    description="Viene descritto come utilizzare il plug-in secondario Azure con integrazione continua Hudson."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Come utilizzare secondario Azure plug-in con integrazione continua Hudson

Secondario Azure plug-in per Hudson consente di effettuare il provisioning di nodi secondari in Azure in fase di esecuzione distribuito compilazione.

## <a name="install-the-azure-slave-plug-in"></a>Installare il plug-in secondario Azure

1. Nel dashboard Hudson fare clic su **Gestisci Hudson**.

1. Nella pagina **Gestisci Hudson** fare clic su **Gestione plug-in**.

1. Fare clic sulla scheda **disponibili** .

1. Fare clic su **Cerca** e digitare **Azure** per limitare l'elenco pertinenti plug-in.

    Se è scelto di scorrere l'elenco dei plug-in disponibili, si troverà secondario Azure plug-nella sezione **Gestione Cluster e distribuito compilare** la scheda **ad altri utenti** .

1. Selezionare la casella di controllo del plug-in **Azure secondario**.

1. Fare clic su **Installa**.

1. Riavviare Hudson.

Dopo l'installazione del plug-in, i passaggi successivi sono per configurare il plug-in con il profilo di sottoscrizione Azure e creare un modello che verrà utilizzato per la creazione di macchine Virtuali per il nodo secondario.

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

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Dopo avere creato il tuo profilo, seguire questa procedura per configurare secondario Azure plug-in.

1. Nel dashboard Hudson fare clic su **Gestisci Hudson**.

1. Fare clic su **Configura sistema**.

1. Scorrere verso il basso della pagina per individuare la sezione **Cloud** .

1. Fare clic su **aggiungere nuovi cloud > Microsoft Azure**.

    ![aggiungere nuovi cloud][add new cloud]

    Seguente illustra i campi in cui è necessario immettere i dettagli dell'abbonamento.

    ![configurare il profilo][configure profile]

1. Copiare il certificato di id e la gestione di abbonamento dal profilo abbonamento e incollarli nei campi appropriati.

    Quando si copia il certificato di id e la gestione di abbonamento, **non** includere le offerte di racchiudono i valori.

1. Fare clic su **configurazione di verifica**.

1. Quando si è verificata correttamente la configurazione, fare clic su **Salva**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurare modelli di macchine virtuali per secondario Azure plug-in

Modelli di macchine virtuali definisce i parametri che del plug-in verrà utilizzato per creare un nodo secondario in Azure. Nei passaggi seguenti che sarà creata modello per un VM Ubuntu.

1. Nel dashboard Hudson fare clic su **Gestisci Hudson**.

1. Fare clic su **configurazione di sistema**.

1. Scorrere verso il basso della pagina per individuare la sezione **Cloud** .

1. All'interno della sezione **Cloud** trovare **Aggiungere Azure macchina virtuale modello** e fare clic sul pulsante **Aggiungi** .

    ![Aggiungere macchine virtuali modello][add vm template]

1. Specificare il nome di un servizio cloud nel campo **nome** . Se il nome specificato si riferisce a un servizio cloud esistente, la macchina virtuale eseguire il provisioning di tale servizio. Azure in caso contrario, verrà creato uno nuovo.

1. Nel campo **Descrizione** immettere il testo che descrive il modello che si sta creando. Queste informazioni sono valida solo per scopi di documenti e non viene utilizzate per il provisioning di una macchina virtuale.

1. Nel campo **etichette** immettere **linux**. L'etichetta viene utilizzata per identificare il modello che si sta creando e successivamente utilizzata per il modello di fare riferimento durante la creazione di un processo Hudson.

1. Selezionare un'area in cui verrà creata la macchina virtuale.

1. Selezionare le dimensioni di macchine Virtuali appropriata.

1. Specificare un account di archiviazione in cui verrà creata la macchina virtuale. Assicurarsi che siano presenti nell'area stesso come servizio cloud che si inizierà a usare. Se si desidera creare nuovo archiviazione, è possibile lasciare vuoto questo campo.

1. Tempo di conservazione specifica il numero di minuti prima Hudson Elimina un secondario inattivo. Lasciare il valore predefinito di 60.

1. In **uso**, selezionare la condizione appropriata quando verrà utilizzato il nodo secondario. Per ora, selezionare **il nodo il più possibile utilizzare**.

    A questo punto, la maschera risulterebbe simile alla seguente:

    ![configurazione di modello][template config]

1. **Immagine famiglia** o Id è necessario specificare quali immagine del sistema verrà installato sulla macchina virtuale. È possibile selezionare da un elenco delle famiglie di immagine oppure specificare un'immagine personalizzata.

    Se si desidera selezionare da un elenco delle famiglie di immagine, immettere il primo carattere (maiuscole/minuscole) del nome della famiglia immagine. Ad esempio, digitare **U** verrà visualizzato un elenco delle famiglie Ubuntu Server. Dopo aver selezionato dall'elenco, Jenkins utilizzerà la versione più recente di tale immagine di sistema da tale gruppo durante il provisioning di una macchina virtuale.

    ![Elenco della famiglia di sistemi operativi][OS family list]

    Se si dispone di un'immagine personalizzata che si desidera utilizzare in alternativa, immettere il nome di tale immagine personalizzata. I nomi immagine personalizzata non vengono visualizzati in un elenco in modo che è necessario assicurarsi che il nome immesso correttamente.    

    Per questa esercitazione, digitare **U** per visualizzare un elenco di immagini Ubuntu e scegliere **Ubuntu Server 14.04 risultati**.

1. Per **avviare metodo**, selezionare **SSH**.

1. Copiare lo script seguente e incollarlo in campo **inizializzazione script** .

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

    Verrà eseguito lo **script inizializzazione** dopo aver creata la macchina virtuale. In questo esempio, lo script installa Java, fra e ant.

1. Nei campi **nome utente** e **Password** , immettere i valori preferiti per l'account di amministratore che verrà creato nella macchina virtuale.

1. Fare clic sul **Modello verificare** come controllare se i parametri specificati sono validi.

1. Fare clic su **Salva**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Creare un processo Hudson che viene eseguito su un nodo secondario in Azure

In questa sezione, si sta creando un'attività Hudson che verrà eseguita su un nodo secondario in Azure.

1. Nel dashboard Hudson fare clic su **Nuovo processo**.

1. Immettere un nome per il processo che si sta creando.

1. Per il tipo di processo, selezionare **Genera un processo software gratuiti stile**.

1. Fare clic su **OK**.

1. Nella pagina di configurazione del processo, selezionare **Limita nel punto in cui è possibile eseguire il progetto**.

1. Selezionare **menu nodo ed etichetta** e quindi **linux** (viene specificato l'etichetta durante la creazione di modello di macchina virtuale nella sezione precedente).

1. Nella sezione **creare** fare clic su **Aggiungi compilazione passaggio** e selezionare **Execute shell**.

1. Modificare lo script seguente sostituendo **{il nome dell'account github}**, **{il nome del progetto}**e **{directory del progetto}** con i valori appropriati e incollare lo script modificato nell'area di testo che viene visualizzato.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Fare clic su **Salva**.

1. Nel dashboard Hudson trovare il processo appena creato e fare clic sull'icona della **programmazione una compilazione** .

Hudson verranno quindi creare un nodo secondario utilizzando il modello creato nella sezione precedente ed eseguire lo script specificato nel passaggio genera per questa attività.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: https://azure.microsoft.com/develop/java/
[profilo di sottoscrizione]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

