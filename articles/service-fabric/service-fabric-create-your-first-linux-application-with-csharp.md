<properties
   pageTitle="Creare un'applicazione servizio tessuti su Linux utilizzando c# | Microsoft Azure"
   description="Creare e distribuire un'applicazione di servizio tessuti tramite C#"
   services="service-fabric"
   documentationCenter="csharp"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="csharp"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/04/2016"
   ms.author="subramar"/>


# <a name="create-your-first-azure-service-fabric-application"></a>Creare la prima applicazione Azure servizio tessuti

> [AZURE.SELECTOR]
- [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
- [Linguaggio - Linux](service-fabric-create-your-first-linux-application-with-java.md)
- [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)

Servizio tessuti fornisce SDK per la creazione di servizi su Linux Core .NET e Java. In questa esercitazione si osserva come creare un'applicazione per Linux e creare un servizio in c# (.NET Core).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, accertarsi di avere [configurare l'ambiente di sviluppo Linux](service-fabric-get-started-linux.md). Se si usa Mac OS X, è possibile [configurare un ambiente di una casella Linux in una macchina virtuale utilizzando Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Creare l'applicazione

Un'applicazione di servizio tessuti può contenere uno o più servizi, ognuna con un ruolo specifico l'esecuzione di funzionalità dell'applicazione. Service tessuti SDK per Linux include un generatore [Yeoman](http://yeoman.io/) che rende più facile per creare il primo servizio e per aggiungere più avanti. Utilizzare Yeoman per creare un'applicazione con un singolo servizio.

1. In terminale, digitare il comando per iniziare a creare una struttura mediante la seguente:`yo azuresfcsharp`

2. Assegnare un nome applicazione.

3. Scegliere il tipo di servizio prima e denominarla. Ai fini di questa esercitazione, si sceglie un servizio attore affidabile.

  ![Generatore di servizio tessuti Yeoman per C#][sf-yeoman]

>[AZURE.NOTE] Per ulteriori informazioni sulle opzioni, vedere [Panoramica sul modello di programmazione servizio tessuti](service-fabric-choose-framework.md).

## <a name="build-the-application"></a>Creare l'applicazione

I modelli di servizio tessuti Yeoman includono uno script di compilazione che è possibile utilizzare per creare l'app dal terminale (dopo aver passare alla cartella di applicazione).

  ```bash
 cd myapp 
 ./build.sh 
  ```

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

## <a name="start-the-test-client-and-perform-a-failover"></a>Avviare il client di test ed eseguire caso di errore

Progetti attore esegue alcuna operazione autonomamente. Richiedono un altro servizio o client per inviare messaggi. Il modello attore include un semplice script di test che è possibile utilizzare per interagire con il servizio attore.

1. Eseguire lo script tramite l'utilità di espressioni di controllo per visualizzare l'output del servizio attore.

    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```

2. In Esplora tessuti servizio individuare nodo che ospita la replica principale per il servizio attore. Nella figura seguente è il nodo 3.

    ![Ricerca di replica primaria in Esplora tessuti di servizio][sfx-primary]

3. Fare clic sul nodo trovato nel passaggio precedente, quindi scegliere **Disattiva (riavvio)** dal menu Azioni. Questa azione riavvia uno dei cinque nodi del cluster locale imporre caso di errore in una replica secondaria in esecuzione in un altro nodo. Come si esegue questa operazione, prestare attenzione all'output dal client di prova e si noti che il contatore continua a incrementare nonostante il failover.


## <a name="next-steps"></a>Passaggi successivi

- [Ulteriori informazioni su operatori affidabile](service-fabric-reliable-actors-introduction.md)
- [Interazione con i cluster di servizio tessuti usa CLI Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
