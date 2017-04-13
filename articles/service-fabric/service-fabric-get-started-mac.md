<properties
   pageTitle="Configurare l'ambiente di sviluppo in Mac OS X | Microsoft Azure"
   description="Installare il runtime, SDK e strumenti e creare un cluster di sviluppo locale. Dopo aver completato l'installazione, sarà possibile creare applicazioni in Mac OS X."
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
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurare l'ambiente di sviluppo in Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OS X](service-fabric-get-started-mac.md)

È possibile creare applicazioni di servizio tessuti da eseguire in cluster Linux con Mac OS X. Questo articolo descrive come configurare l'ambiente Mac per lo sviluppo.

## <a name="prerequisites"></a>Prerequisiti

Servizio tessuti non viene eseguito a livello nativo in OS X. Per eseguire un cluster di servizio tessuti locale, sono disponibili una macchina di virtuale Ubuntu preconfigurata utilizzando Vagrant e VirtualBox. Prima di iniziare, è necessario:

- [Vagrant (v1.8.4 o versioni successive)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Creare la macchina virtuale locale

Per creare la macchina virtuale locale che include un cluster di servizio tessuti nodo 5, eseguire le operazioni seguenti:

1. Duplicare repo Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Individuare la copia locale del repo

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Facoltativo) Modificare le impostazioni di macchine Virtuali predefinite

    Per impostazione predefinita, la macchina virtuale locale è configurata come segue:

    - 3 GB di memoria
    - Rete privata host configurato IP 192.168.50.50 attivazione pass-through di traffico da host Mac

    È possibile modificare queste impostazioni o aggiungere altre operazioni di configurazione per la macchina virtuale nel Vagrantfile. Vedere la [documentazione Vagrant](http://www.vagrantup.com/docs) per l'elenco completo delle opzioni di configurazione.

4. Creare la macchina virtuale

    ```bash
    vagrant up
    ```

    Questo passaggio consente di scaricare l'immagine macchine Virtuali preconfigurata, avvio in locale e quindi impostare un'infrastruttura di servizio locale cluster al suo interno. È possibile aspettarsi per richiedere alcuni minuti. Se il programma di installazione viene completata correttamente, si verrà visualizzato un messaggio nell'output che indica che il cluster avvio in corso.

    ![Avvio di seguire il provisioning di macchine Virtuali di cluster][cluster-setup-script]

5. Verificare che il cluster è stato impostato correttamente posizionandosi servizio tessuti Explorer all'http://192.168.50.50:19080 Esplora risorse (presupponendo che si mantenuti IP di rete privata predefinito).

    ![Servizio tessuti Explorer visualizzati nell'host dei Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installare il plug-in servizio tessuti per Neon Eclisse (facoltativo)

Servizio tessuti fornisce un plug-in per Eclipse Neon IDE che consente di semplificare il processo di creazione e la distribuzione di servizi di linguaggio.

1. Nel Eclisse, verificare di avere Buildship versione 1.0.17 o versione successiva. È possibile controllare le versioni dei componenti installati scegliendo **Guida > i dettagli sull'installazione**. È possibile aggiornare Buildship seguendo le istruzioni riportate [di seguito][buildship-update].

2. Per installare il plug-in servizio tessuti, scegliere **Guida > installare nuovo Software**

3. Nella casella di testo "Lavorare con", immettere: http://dl.windowsazure.com/eclipse/servicefabric.

4. Fare clic su Aggiungi.

    ![Plug-in Neon Eclisse per tessuti servizio][sf-eclipse-plugin-install]

5. Scegliere il servizio tessuti plug-in e fare clic su Avanti.

6. Proseguire con l'installazione e accettare il contratto di licenza per l'utente finale.

## <a name="next-steps"></a>Passaggi successivi

- [Creare la prima applicazione di servizio tessuti per Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Creare un cluster di servizio tessuti nel portale di Azure](service-fabric-cluster-creation-via-portal.md)
- [Creare un cluster di tessuti servizio Gestione risorse Azure](service-fabric-cluster-creation-via-arm.md)
- [Informazioni sul modello di applicazione di servizio tessuti](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
