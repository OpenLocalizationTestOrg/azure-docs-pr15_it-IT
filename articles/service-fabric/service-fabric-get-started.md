<properties
   pageTitle="Configurare l'ambiente di sviluppo | Microsoft Azure"
   description="Installare il runtime, SDK e strumenti e creare un cluster di sviluppo locale. Dopo aver completato l'installazione, sarà possibile creare applicazioni."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/26/2016"
   ms.author="ryanwi"/>

# <a name="prepare-your-development-environment"></a>Preparare l'ambiente di sviluppo

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OS X](service-fabric-get-started-mac.md)

 Per creare ed eseguire [applicazioni Azure servizio tessuti] [ 1] nel computer di sviluppo, installare il runtime, SDK e strumenti. Inoltre, è necessario attivare l'esecuzione di script Windows PowerShell inclusi in SDK.

## <a name="prerequisites"></a>Prerequisiti
### <a name="supported-operating-system-versions"></a>Versioni di sistemi operativi supportati
Le versioni di sistemi operativi seguenti sono supportate per lo sviluppo di:

- Windows 7
- Windows 8 e Windows 8.1
- Windows Server 2012 R2
- Windows 10

>[AZURE.NOTE] Per impostazione predefinita, Windows 7 include solo Windows PowerShell 2.0. Cmdlet di PowerShell tessuti servizio richiede PowerShell 3.0 o versione successiva. È possibile [scaricare Windows PowerShell 5.0] [ powershell5-download] da Microsoft Download Center.

## <a name="install-the-runtime-sdk-and-tools"></a>Installare il runtime, SDK e strumenti

L'installazione guidata piattaforma Web offre due configurazioni per lo sviluppo di tessuti di servizio:

- [Installare il runtime del servizio tessuti, SDK e strumenti per Visual Studio 2015 (richiede l'aggiornamento di Visual Studio 2015 2 o versione successiva)][full-bundle-vs2015]
- [Installare il runtime del servizio tessuti e SDK solo (senza strumenti Visual Studio)][core-sdk]

## <a name="enable-powershell-script-execution"></a>Attivare l'esecuzione di script di PowerShell

Servizio tessuti utilizza script di Windows PowerShell per la creazione di un cluster di sviluppo locale e per la distribuzione di applicazioni da Visual Studio. Per impostazione predefinita, Windows blocca questi esecuzione di script. Per consentire loro, è necessario modificare il criterio di esecuzione di PowerShell. Aprire PowerShell come amministratore e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="next-steps"></a>Passaggi successivi
Ora che è stato completato la configurazione dell'ambiente di sviluppo, avviare la creazione e l'esecuzione di App.

- [Creare un'applicazione servizio tessuti in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
- [Informazioni su come distribuire e gestire applicazioni il cluster locale](service-fabric-get-started-with-a-local-cluster.md)
- [Informazioni sui modelli di programmazione: servizi affidabili e operatori affidabile](service-fabric-choose-framework.md)
- [Vedere gli esempi di codice servizio tessuti su GitHub](https://aka.ms/servicefabricsamples)
- [Visualizzare il cluster utilizzando servizio tessuti Esplora](service-fabric-visualizing-your-cluster.md)
- [Seguire il percorso formativo tessuti servizio per un'introduzione generali della piattaforma](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)

[1]: http://azure.microsoft.com/en-us/campaigns/service-fabric/ "Pagina della campagna tessuti servizio"
[2]: http://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Collegamento VS 2015 WebPI"
[full-bundle-dev15]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Collegamento WebPI Dev15"
[core-sdk]:http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Collegamento SDK WebPI core"
[powershell5-download]:https://www.microsoft.com/en-us/download/details.aspx?id=50395
