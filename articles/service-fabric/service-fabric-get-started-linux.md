<properties
   pageTitle="Configurare l'ambiente di sviluppo su Linux | Microsoft Azure"
   description="Installare il runtime e SDK e creare un cluster di sviluppo locale su Linux. Dopo aver completato l'installazione, sarà possibile creare applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="seanmck"/>

# <a name="prepare-your-development-environment-on-linux"></a>Preparare l'ambiente di sviluppo su Linux


> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OS X](service-fabric-get-started-mac.md)

 Per distribuire ed eseguire [le applicazioni di Azure servizio tessuti](service-fabric-application-model.md) nel computer di sviluppo Linux, installare il runtime e SDK comuni. È anche possibile installare facoltativo SDK per Java e .NET Core.

## <a name="prerequisites"></a>Prerequisiti
### <a name="supported-operating-system-versions"></a>Versioni di sistemi operativi supportati
Le versioni di sistemi operativi seguenti sono supportate per lo sviluppo di:

- Ubuntu 16.04 (Xerus Xenial)

## <a name="update-your-apt-sources"></a>Aggiornare le origini apt

Per installare il SDK e il pacchetto di runtime associato tramite apt get, è innanzitutto necessario aggiornare le origini apt.

1. Aprire un terminale.
2. Aggiungere l'elenco origini repo servizio tessuti.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Aggiungere la nuova chiave GPG per il gruppo di chiavi apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    ```

4. Aggiornare gli elenchi di pacchetto in base a archivi appena aggiunti.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk"></a>Installare e configurare il SDK

Una volta le origini vengono aggiornate, è possibile installare il SDK.

1. Installare il pacchetto Service tessuti SDK. Verrà richiesto di confermare l'installazione e per accettare un contratto di licenza.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

2. Eseguire lo script di installazione SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

## <a name="set-up-the-azure-cross-platform-cli"></a>Configurare la CLI multipiattaforma e su Azure

[Azure multipiattaforma CLI] [ azure-xplat-cli-github] include i comandi per interagire con entità servizio tessuti, incluse le applicazioni e cluster. Basato su Node in tal caso [, assicurarsi di avere installato nodo] [ install-node] prima di procedere con le istruzioni riportate di seguito.

1. Duplicare repo github al computer di sviluppo.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Alternare repo duplicato e installare le dipendenze del CLI mediante la gestione di pacchetti di nodo (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Creare un symlink dalla cartella bin/azure di repo duplicato a /usr/bin/azure in modo che venga aggiunta al percorso e i comandi disponibili da qualsiasi directory.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Infine, abilitare i comandi di servizio tessuti di completamento automatico.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

## <a name="set-up-a-local-cluster"></a>Configurare un cluster locale

Se tutti gli elementi sia installato correttamente, dovrebbe essere possibile avviare un cluster locale.

1. Eseguire lo script di installazione cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Aprire un web browser e passare a http://localhost:19080/soluzioni. Se il cluster ha avviato, verrà visualizzato nel dashboard di servizio tessuti Explorer.

    ![Servizio tessuti Explorer su Linux][sfx-linux]

A questo punto in grado di distribuire pacchetti di applicazioni di servizio tessuti predefiniti o nuovi file in base a contenitori guest o eseguibili guest. Per creare nuovi servizi utilizzando il linguaggio o .NET Core SDK, seguire i passaggi di impostazione facoltativi riportata di seguito.

## <a name="install-the-java-sdk-and-eclipse-neon-plugin-optional"></a>Installare il linguaggio SDK ed Eclisse Neon plug-in (facoltativo)

SDK linguaggio offre le raccolte e i modelli necessari per la compilazione di servizi di infrastruttura di servizio utilizzando linguaggio.

1. Installare il pacchetto di linguaggio SDK.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Eseguire lo script di installazione SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

È possibile installare il plug-in Eclisse per tessuti servizio da IDE Neon Eclisse.

1. Nel Eclisse, verificare di avere Buildship versione 1.0.17 o versione successiva. È possibile controllare le versioni dei componenti installati scegliendo **Guida > i dettagli sull'installazione**. È possibile aggiornare Buildship seguendo le istruzioni riportate [di seguito][buildship-update].

2. Per installare il plug-in servizio tessuti, scegliere **Guida > installare nuovo Software**

3. Nella casella di testo "Lavorare con", immettere: http://dl.windowsazure.com/eclipse/servicefabric

4. Fare clic su Aggiungi.

    ![Plug-in Eclisse][sf-eclipse-plugin]

5. Scegliere il servizio tessuti plug-in e fare clic su Avanti.

6. Proseguire con l'installazione e accettare il contratto di licenza per l'utente finale.

## <a name="install-the-net-core-sdk-optional"></a>Installare .NET Core SDK (facoltativo)

.NET Core SDK fornisce le raccolte e i modelli necessari per la compilazione di servizi di infrastruttura di servizio usati multipiattaforma .NET Core.

1. Installare il pacchetto di .NET Core SDK.

    ```bash
    sudo apt-get install servicefabricsdkcsharp
    ```

2. Eseguire lo script di installazione SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'applicazione Java su Linux](service-fabric-create-your-first-linux-application-with-java.md)

- [Preparare l'ambiente di sviluppo in OS x](service-fabric-get-started-mac.md)


<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
