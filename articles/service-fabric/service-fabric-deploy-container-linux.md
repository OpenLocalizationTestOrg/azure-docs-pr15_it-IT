<properties
   pageTitle="Servizio tessuti e distribuzione i contenitori di Linux | Microsoft Azure"
   description="Infrastruttura di servizio e l'utilizzo della contenitori Docker distribuire le applicazioni di microservice. In questo articolo descrive le funzionalità offerte da tessuti servizi per i contenitori e su come distribuire un'immagine di contenitore Docker in un cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="deploy-a-docker-container-to-service-fabric"></a>Distribuire un contenitore Docker tessuti servizio

> [AZURE.SELECTOR]
- [Distribuire il contenitore di Windows](service-fabric-deploy-container.md)
- [Distribuire Docker contenitore](service-fabric-deploy-container-linux.md)

In questo articolo illustra la creazione di servizi nei contenitori nei contenitori Docker su Linux.

Servizio tessuti sono diverse funzionalità contenitore utili per le applicazioni che sono costituiti di microservices che sono in contenitore. Si tratta di servizi nei contenitori.

Includono le funzionalità;

- Attivazione e la distribuzione di immagini contenitore
- Governance delle risorse
- Autenticazione archivio
- Contenitore mapping porta all'host porta
- Comunicazioni e individuazione contenitore a
- Consente di configurare e impostare le variabili di ambiente


## <a name="packaging-a-docker-container-with-yeoman"></a>Imballaggio un contenitore docker con yeoman
Quando imballaggio un contenitore su Linux, è possibile scegliere di utilizzare un modello yeoman o [creare manualmente il pacchetto dell'applicazione](service-fabric-deploy-container.md#manually-packaging-and-deploying-a-container).

Un'applicazione di servizio tessuti può contenere uno o più contenitori, ognuna con un ruolo specifico l'esecuzione di funzionalità dell'applicazione. Service tessuti SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che consente di creare un'applicazione e aggiungere un'immagine di contenitore facilmente. Utilizzare Yeoman per creare una nuova applicazione con un singolo contenitore Docker denominato *SimpleContainerApp*. È possibile aggiungere che altri servizi in un secondo momento modificando generati file manifesto.

## <a name="create-the-application"></a>Creare l'applicazione

1. In terminale, digitare **yo azuresfguest**.

2. Per il framework scegliere **contenitore** .

3. Assegnare un nome applicazione, ad esempio SimpleContainerApp

4. Fornire l'URL per l'immagine contenitore da un repo DockerHub. Assume la forma [repo] / [nome immagine]

![Generatore di servizio tessuti Yeoman per i contenitori][sf-yeoman]

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Dopo aver creato l'applicazione, è possibile distribuire sul cluster locale usa CLI Azure.

1. Connettersi a cluster tessuti servizio locale.

    ```bash
    azure servicefabric cluster connect
    ```

2. Utilizzare lo script di installazione fornito nel modello di copiare il pacchetto di applicazione di archivio di immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.

    ```bash
    ./install.sh
    ```

3. Aprire un browser e passare al servizio tessuti Explorer in http://localhost:19080/Explorer (host locale Sostituisci con IP privato di macchine Virtuali con Vagrant su Mac OS X).

4. Espandere il nodo applicazioni e notare che è ora disponibile una voce per il tipo di applicazione e un altro per la prima istanza di tale tipo.

5. Utilizzare lo script di disinstallazione fornito nel modello di eliminare l'istanza dell'applicazione e annullare la registrazione del tipo di applicazione.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'infrastruttura di servizio e contenitori](service-fabric-containers-overview.md)
- [Interazione con i cluster di servizio tessuti usa CLI Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png

