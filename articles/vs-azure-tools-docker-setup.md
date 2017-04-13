<properties
   pageTitle="Configurare un Host Docker con VirtualBox | Microsoft Azure"
   description="Istruzioni dettagliate per configurare un'istanza di Docker predefinito utilizzando computer Docker e VirtualBox"
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="mlearned" />

# <a name="configure-a-docker-host-with-virtualbox"></a>Configurare un Host Docker con VirtualBox

## <a name="overview"></a>Panoramica
In questo articolo illustra la configurazione di un'istanza di Docker predefinito utilizzando computer Docker e VirtualBox. Se si usa la [versione beta Docker per Windows](http://beta.docker.com/), questa configurazione non è più necessaria.

## <a name="prerequisites"></a>Prerequisiti
È necessario installare gli strumenti seguenti.

- [Casella degli strumenti docker](https://www.docker.com/products/overview#/docker_toolbox)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Configurazione di client Docker con Windows PowerShell

Per configurare un client Docker, è sufficiente aprire Windows PowerShell e procedere come segue:

1. Creare un'istanza di host docker predefinita.

    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
 
1. Verificare che l'istanza predefinita sia configurato e in esecuzione. (Verrà visualizzata un'istanza denominata 'default' in esecuzione.

    ```PowerShell
    docker-machine ls 
    ```
        
    ![output ls docker computer][0]
 
1. Impostazione predefinita come host corrente e configurare il shell.

    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```

1. Visualizzare i contenitori Docker attivi. L'elenco deve essere vuoto.

    ```PowerShell
    docker ps
    ```

    ![output ps docker][1]
 
> [AZURE.NOTE]Ogni volta che si riavvia il computer di sviluppo, sarà necessario riavviare l'host docker locale.
> A tale scopo, eseguire il comando seguente al prompt: `docker-machine start default`.

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
