<properties
   pageTitle="Compilazione della riga di comando per Azure | Microsoft Azure"
   description="Compilazione della riga di comando per Azure"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="command-line-build-for-azure"></a>Compilazione della riga di comando per Azure

## <a name="overview"></a>Panoramica

È possibile creare un pacchetto per la distribuzione di Azure eseguendo MSBuild al prompt dei comandi. È possibile configurare e definire build per il debug, gestione temporanea e produzione, oltre ad alcune del processo di compilazione di automazione.


## <a name="microsoft-build-engine-msbuild"></a>Motore di compilazione di Microsoft (MSBuild)

Utilizzando Microsoft Build Engine (MSBuild), è possibile creare ambienti in cui non è installato Visual Studio prodotti nella build. MSBuild utilizza un formato XML per i file di progetto di extensible e completamente supportato da Microsoft. In questo formato di file, è possibile descrivere cosa gli elementi devono essere progettate per uno o più piattaforme e configurazioni.

È anche possibile eseguire MSBuild al prompt e in questo argomento descrive l'approccio. Mediante l'impostazione delle proprietà in un prompt dei comandi, è possibile creare configurazioni specifiche di un progetto. Analogamente, è inoltre possibile definire le destinazioni compilate il comando MSBuild. Per ulteriori informazioni sui parametri della riga di comando e MSBuild, vedere [Informazioni di riferimento della riga di comando MSBuild](https://msdn.microsoft.com/library/ms164311.aspx).

## <a name="installation"></a>Installazione

Come viene descritta la procedura seguente, è necessario installare software e strumenti sul server di compilazione prima di creare un pacchetto di Azure tramite MSBuild:

1. Installare .NET Framework 4 o versioni successive, che include MSBuild.

1. Installare gli [Strumenti di creazione condivisa di Azure](http://go.microsoft.com/fwlink/?LinkId=394615) (cercare MicrosoftAzureAuthoringTools x64.msi o MicrosoftAzureAuthoringTools x86.msi.

1. Installare le [Raccolte di Azure per .NET](http://go.microsoft.com/fwlink/?LinkId=394616) (cercare MicrosoftAzureLibsForNet x64.msi o MicrosoftAzureLibs x86.msi.

1. Copiare il file Microsoft.WebApplication.targets da un'installazione di Visual Studio in un altro computer.

    Il file si trova nel \MSBuild\Microsoft\Visual Studio\v12.0\WebApplications file c:\Programmi\Microsoft (x86) directory (v11.0 per Visual Studio 2012) e si deve copia nella stessa directory sul server di compilazione.

1. Installare [Azure Tools per Visual Studio](http://go.microsoft.com/fwlink/?LinkId=394616).

    Cercare WindowsAzureTools.vs120.exe creare progetti di Visual Studio 2013.

## <a name="msbuild-parameters"></a>Parametri di MSBuild

Il modo più semplice per creare un pacchetto è come eseguirlo tramite con la `/t:Publish` opzione. Per impostazione predefinita, questo comando crea una directory in relazione alla cartella radice per il progetto, ad esempio ProjectDir\bin\Configuration\app.publish\. quando si creazione un progetto di Azure, viene generato due file, il file di pacchetto e il relativo file di configurazione:

- Project.cspkg

- ServiceConfiguration.TargetProfile.cscfg

Per impostazione predefinita, ogni progetto Azure include uno consente di creare file di configurazione del servizio per locale (debug) e un altro per build cloud (produzione o gestione temporanea), ma è possibile aggiungere o rimuovere i file di configurazione del servizio in base alle esigenze. Quando si crea un pacchetto Visual Studio, verrà richiesto il file di configurazione del servizio da includere insieme al pacchetto. Quando si crea un pacchetto tramite MSBuild, il file di configurazione del servizio locale è incluso per impostazione predefinita. Per includere un altro file di configurazione del servizio, impostare il `TargetProfile` proprietà del comando MSBuild (`MSBuild /t:Publish /p:TargetProfile=ProfileName`).

Se si desidera utilizzare una directory alternativa per il pacchetto archiviato e file di configurazione, impostare il percorso utilizzando il `/p:PublishDir=Directory\` opzione, incluso il separatore di barra rovesciata finale.

## <a name="deployment"></a>Distribuzione

Una volta creato il pacchetto, è possibile distribuirlo in Azure. Per un'esercitazione che illustra il processo, vedere il sito Web di Azure. Per informazioni su come automatizzare il processo, vedere [Delivery continua per i servizi Cloud in Azure](./cloud-services/cloud-services-dotnet-continuous-delivery.md).
