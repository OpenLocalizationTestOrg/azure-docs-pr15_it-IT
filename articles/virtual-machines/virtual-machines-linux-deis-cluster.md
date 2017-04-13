<properties
   pageTitle="Distribuire un nodo 3 Deis cluster | Microsoft Azure"
   description="In questo articolo viene descritto come creare un nodo 3 Deis cluster su Azure utilizzando un modello di gestione risorse di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Distribuire un nodo 3 Deis cluster

In questo articolo viene descritto il provisioning di un [Deis](http://deis.io/) cluster su Azure. Coprire tutti i passaggi da creare i certificati necessari per la distribuzione e il ridimensionamento di un'applicazione di esempio **passare** cluster appena provisioning.

Di seguito è illustrata l'architettura di sistema distribuito. Un amministratore di sistema gestisce il cluster utilizzando Deis strumenti, ad esempio **deis** e **deisctl**. Connessioni tramite un Azure di bilanciamento del carico, che inoltra le connessioni a uno del membro nodi nel cluster. Accesso client distribuito applicazioni tramite il bilanciamento del carico anche. In questo caso, il servizio di bilanciamento del carico inoltra il traffico a un Deis trama router, che routs ulteriormente il traffico a contenitori Docker corrispondenti presenti nel cluster.

  ![Diagramma dell'architettura di cluster Desis distribuito](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Per eseguire i passaggi seguenti, è necessario:

 * Un abbonamento attivo a Azure. Se non si dispone di uno, è possibile ottenere una traccia gratuita su [azure.com](https://azure.microsoft.com/).
 * Id aziendale o dell'istituto di istruzione per utilizzare i gruppi di risorse Azure. Se si dispone di un account personale e accedere con un id Microsoft, è necessario [creare un id di lavoro da quello personale](virtual-machines-windows-create-aad-work-id.md).
 * Uno - a seconda del sistema operativo client, ossia [PowerShell Azure](../powershell-install-configure.md) o [CLI Azure per Mac, Linux e Windows](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL viene usata per generare i certificati necessari.
 * Client di operazioni, ad esempio [Fra Bash](https://git-scm.com/).
 * Per testare l'applicazione di esempio, è necessario anche un server DNS. È possibile utilizzare tutti i server DNS o servizi che supportano i record di un carattere jolly.
 * Un computer per l'esecuzione Deis strumenti client. È possibile utilizzare un computer locale o una macchina virtuale. È possibile eseguire questi strumenti in qualsiasi distribuzione Linux, ma le istruzioni seguenti usare Ubuntu.

## <a name="provision-the-cluster"></a>Eseguire il provisioning del cluster

In questa sezione, utilizzare un modello di [Gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md) dal repository Apri origine [modelli Guida introduttiva di azure](https://github.com/Azure/azure-quickstart-templates). Prima di tutto necessario copiare verso il basso il modello. Quindi, sarà necessario creare una nuova coppia di chiavi SSH per l'autenticazione. E quindi illustrato come configurare un nuovo identificatore per si cluster. E infine, si userà lo script di Shell o lo script di PowerShell per eseguire il provisioning del cluster.

1. Duplicare l'archivio: [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Passare alla cartella modelli:

        cd azure-quickstart-templates\deis-cluster-coreos

3. Creare una nuova SSH coppia utilizzando ssh keygen:

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Generare un certificato utilizzando la chiave privata precedente:

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Passare a [https://discovery.etcd.io/new](https://discovery.etcd.io/new) per generare un nuovo token di cluster che ha un aspetto simile:

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Ogni cluster CoreOS deve avere un token univoco da questo servizio gratuito. Per altri dettagli, vedere [la documentazione CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) .

6. Modificare il file **config.yaml cloud** per sostituire il token di **individuazione** esistente con il nuovo token:

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modificare il file **azuredeploy parameters.json** : aprire il certificato creato nel passaggio 4 in un editor di testo. Copiare tutto il testo tra `----BEGIN CERTIFICATE-----` e `-----END CERTIFICATE-----` nel parametro **sshKeyData** (è necessario rimuovere tutti i caratteri).

8. Modificare il parametro **newStorageAccountName** . Questo è l'account di archiviazione per dischi OS macchine Virtuali. Il nome dell'account deve essere univoco globale.

9. Modificare il parametro **publicDomainName** . Questo diventa parte del nome del DNS associato l'indirizzo IP pubblico bilanciamento carico. il FQDN finale avrà il formato del _[valore del parametro]_. _[region]_. cloudapp.azure.com. Ad esempio, se si specifica il nome come deishbai32 e il gruppo di risorse viene distribuito all'area occidentale degli Stati Uniti, il FQDN per il servizio di bilanciamento del carico finale sarà deishbai32.westus.cloudapp.azure.com.

10. Salvare il file di parametro. E quindi è possibile eseguire il provisioning cluster tramite PowerShell Azure:

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  o CLI Azure:

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Una volta che viene eseguito il provisioning di un gruppo di risorse, è possibile visualizzare tutte le risorse del gruppo nel portale classica Azure. Come illustrato nella schermata seguente, il gruppo di risorse contiene una rete virtuale con tre macchine virtuali, che fanno parte lo stesso set di disponibilità. Il gruppo contiene anche un bilanciamento del carico, che ha un indirizzo IP pubblico associato.

  ![Gruppo di risorse provisioning nel portale classica Azure](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Installare il client

È necessario **deisctl** per controllare il Deis cluster. Sebbene deisctl viene installato automaticamente in tutti i nodi, è consigliabile utilizzare deisctl su un computer amministrativo separato. Inoltre, perché tutti i nodi sono configurati con solo gli indirizzi IP privati, è necessario usare SSH tunnel su bilanciamento del carico, che ha un indirizzo IP pubblico, per connettersi al computer nodo. Di seguito sono i passaggi della configurazione deisctl su un Ubuntu separata fisica o la macchina virtuale.

1. Installa deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Aggiungere la chiave privata per ssh agente:

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configurare deisctl:

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Il modello consente di definire le regole in entrata NAT che mappano 2223 creare l'istanza 1, 2224 all'istanza 2 e 2225 all'istanza 3. In questo modo la ridondanza per tramite lo strumento deisctl. È possibile esaminare le regole nel portale classica Azure:

![Regole NAT nel sistema di bilanciamento del carico](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Il modello supporta attualmente solo 3 nodi. Verrà visualizzata a causa di un limite di Manager delle risorse Azure modello Definizione regola NAT, che non supporta la sintassi di ciclo.

## <a name="install-and-start-the-deis-platform"></a>Installare e avviare la piattaforma Deis

È ora possibile utilizzare deisctl per installare e avviare il Deis piattaforma:

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Avvio della piattaforma accetta un po' di tempo (quanto 10 minuti). In particolare, avviare il servizio generatore può richiedere molto tempo. E in alcuni casi richiede alcuni tentativi esito positivo: se l'operazione sembra bloccarsi, provare a digitare `ctrl+c` per interrompere l'esecuzione del comando e riprovare.

È possibile utilizzare `deisctl list` per verificare se tutti i servizi sono in esecuzione:

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Congratulazioni! Dopo aver importato parziale Deis clsuter su Azure! Successivamente, si distribuisce un esempio di applicazione Vai a vedere cluster in azione.

## <a name="deploy-and-scale-a-hello-world-application"></a>Distribuire e scalare un'applicazione Hello World

La procedura seguente viene illustrato come distribuire un "Hello World" passare applicazione al cluster. I passaggi si basano su [Deis documentazione](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Per il routing reticolo per il corretto funzionamento, è necessario disporre di un record di un carattere jolly per il dominio che puntano all'indirizzo IP pubblico di bilanciamento del carico. La schermata seguente mostra il record per la registrazione di un dominio di esempio sulla configurazione di GoDaddy:

    ![Record A GoDaddy](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Installa deis:

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Creare una nuova chiave SSH e quindi aggiungere la chiave pubblica GitHub (Naturalmente, è inoltre possibile riutilizzare le chiavi esistente). Per creare una nuova coppia SSH, usare:

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Aggiungere id_rsa.pub o la chiave pubblica della scelta effettuata, a GitHub. È possibile eseguire questa operazione utilizzando l'aggiungere SSH chiave pulsante nella schermata Configurazione chiavi SSH:

  ![Chiave Github](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Registrare un nuovo utente:

        deis register http://deis.[your domain]
<p />
6. Aggiungere la chiave SSH:

        deis keys:add [path to your SSH public key]
  <p />      
7. Creare un'applicazione.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.Push fra attiverà immagini Docker per compilazione e distribuzione, che potrebbe richiedere alcuni minuti. L'esperienza in alcuni casi potrebbe bloccarsi passaggio 10 (immagine Pushing all'archivio privato). In questo caso, è possibile interrompere il processo, rimuovere l'applicazione utilizzando ' deis App: eliminare: <application name> ` to remove the application and try again. You can use `deis apps:list "per trovare il nome dell'applicazione. Se tutto procede, verrà visualizzato il seguente alla fine del comando output:

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Verificare se l'applicazione funziona:

        curl -S http://[your application name].[your domain]
  Verrà visualizzato:

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Scala a 3 istanze dell'applicazione:

        deis scale cmd=3
<p />
11. Facoltativamente, è possibile utilizzare deis info per esaminare i dettagli dell'applicazione. Gli output seguenti sono dalla distribuzione dell'applicazione:

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Passaggi successivi

In questo articolo esaminati tutti i passaggi per effettuare il provisioning di un nuovo Deis cluster su Azure utilizzando un modello di gestione di risorse Azure. Il modello supporta la ridondanza utensili connessioni, nonché il bilanciamento del carico per le applicazioni distribuite. Il modello evita l'uso di indirizzi IP pubblico su nodi di un membro, che consente di risparmiare preziose risorse IP pubbliche e fornisce un ambiente più protetto alle applicazioni host. Per ulteriori informazioni, vedere gli articoli seguenti:

[Panoramica di gestione risorse Azure] [resource-group-overview]  
[Come usare CLI Azure] [azure-command-line-tools]  
[Utilizzo di PowerShell Azure Azure Gestione risorse] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md
