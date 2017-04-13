<properties
   pageTitle="Distribuire un contenitore di ASP.NET Core Linux Docker a un host remoto Docker | Microsoft Azure"
   description="Informazioni su come utilizzare Visual Studio Tools per Docker per distribuire un'app web ASP.NET di base in un contenitore di Docker in esecuzione in una macchina virtuale Linux di Azure Docker Host"   
   services="azure-container-service"
   documentationCenter=".net"
   authors="mlearned"
   manager="douge"
   editor=""/>

<tags
   ms.service="azure-container-service"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="mlearned"/>

# <a name="deploy-an-aspnet-container-to-a-remote-docker-host"></a>Distribuire un contenitore di ASP.NET a un host Docker remoto

## <a name="overview"></a>Panoramica
Docker è un motore di leggere e contenitore, simile in alcuni modi per una macchina virtuale, è possibile utilizzare per applicazioni e servizi.
In questa esercitazione si mediante l'estensione di [Visual Studio 2015 Tools per Docker](http://aka.ms/DockerToolsForVS) per distribuire un'app ASP.NET di base su un host Docker su Azure tramite PowerShell.

## <a name="prerequisites"></a>Prerequisiti di
Per completare l'esercitazione è necessario quanto segue:

- Creare una macchina virtuale Host Docker Azure come descritto nella [procedura usare docker machine con Azure](./virtual-machines/virtual-machines-linux-docker-machine.md)
- Installare [Visual Studio 2015 Update 3](https://go.microsoft.com/fwlink/?LinkId=691129)
- [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)
- Installare [Visual Studio 2015 Tools per Docker - anteprima](http://aka.ms/DockerToolsForVS)

## <a name="1-create-an-aspnet-core-web-app"></a>1. creare un'applicazione web ASP.NET di base
La procedura seguente le fasi di creazione di un'app ASP.NET di base di base che verrà utilizzata in questa esercitazione.

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. aggiungere supporto Docker

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-use-the-dockertaskps1-powershell-script"></a>3. utilizzare lo Script di PowerShell DockerTask.ps1 

1.  Aprire un prompt dei comandi di PowerShell per la directory radice del progetto. 

    ```
    PS C:\Src\WebApplication1>
    ```

1.  Convalidare il computer remoto host sia in esecuzione. Verrà visualizzato lo stato = esecuzione 

    ```
    docker-machine ls
    NAME         ACTIVE   DRIVER   STATE     URL                        SWARM   DOCKER    ERRORS
    MyDockerHost -        azure    Running   tcp://xxx.xxx.xxx.xxx:2376         v1.10.3
    ```

    > [AZURE.NOTE] Se si usa la versione Beta Docker, l'host non elencato qui.

1.  Compilare utilizzando l'app genera il parametro -

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release -Machine mydockerhost
    ```  

    > [AZURE.NOTE] Se si usa la versione Beta Docker, omettere il - argomento computer
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Build -Environment Release 
    > ```  


1.  Eseguire l'app utilizzando Esegui il parametro -

    ```
    PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release -Machine mydockerhost
    ```

    > [AZURE.NOTE] Se si usa la versione Beta Docker, omettere il - argomento computer
    > 
    > ```
    > PS C:\Src\WebApplication1> .\Docker\DockerTask.ps1 -Run -Environment Release 
    > ```

    Al termine del docker, risulta risultati simili al seguente:

    ![Visualizzare l'app][3]

[0]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]:./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png
