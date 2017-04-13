<properties 
    pageTitle="Impostazione di un Computer per lo sviluppo di servizi di supporto con .NET" 
    description="Informazioni sui requisiti per i servizi di supporto utilizzando Media Services SDK per .NET. Anche informazioni su come creare un'app di Visual Studio." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="10/24/2016"  
    ms.author="juliako"/>

#<a name="media-services-development-with-net"></a>Sviluppo di servizi multimediali con .NET

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

In questo argomento viene illustrato come avviare lo sviluppo di applicazioni di servizi multimediali mediante .NET.

La raccolta di **Azure Media Services.NET SDK** consente la programmazione di servizi multimediali utilizzando .NET. È possibile mettere ancora più semplice per lo sviluppo con .NET, viene fornita la raccolta di **Azure Media servizi .NET SDK estensioni** . Questa raccolta contiene un insieme di metodi di estensione e funzioni di supporto che ne semplificano codice .NET. Entrambe le librerie sono disponibili tramite **NuGet** e **GitHub**.


##<a name="prerequisites"></a>Prerequisiti

-   Un account di servizi multimediali in una sottoscrizione di Azure nuovo o esistente. Vedere l'argomento [come creare un Account di servizi di supporto](media-services-portal-create-account.md).
-   Sistemi operativi: Windows 10, Windows 7, Windows 2008 R2 o Windows 8.
-   .NET framework 4.5.
-    Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 o Visual Studio 2010 SP1 (Professional, Premium, Ultimate o Express).


##<a name="create-and-configure-a-visual-studio-project"></a>Creare e configurare un progetto Visual Studio

In questa sezione viene illustrato come creare un progetto in Visual Studio e impostarlo per consentire lo sviluppo di servizi di supporto.  In questo caso il progetto è un'applicazione di console di Windows c#, ma la stessa procedura di configurazione illustrata di seguito si applica ad altri tipi di progetti che è possibile creare per le applicazioni di servizi multimediali (ad esempio, un'applicazione Windows Form o un'applicazione Web ASP.NET).

In questa sezione viene illustrato come utilizzare **NuGet** per aggiungere elementi multimediali servizi .NET SDK e altre raccolte di dipendenti.

In alternativa, è possibile ottenere i bit più recente di Media Services .NET SDK da GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) e [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), creare la soluzione e aggiungere i riferimenti al progetto client. Si noti che tutte le dipendenze necessarie scaricate ed estratti automaticamente.

1. Creare una nuova applicazione Console c# in Visual Studio 2010 SP1 o versioni successive VS. Immettere il **nome**, **percorso**e **Nome soluzione**e quindi fare clic su OK.

2. Compilare la soluzione.

2. Utilizzare **NuGet** per installare e aggiungere **Le estensioni SDK .NET di Azure Media Services**. Installazione di questo pacchetto, anche installa **Media Services.NET SDK** e aggiunge tutte le altre dipendenze necessari.

    Assicurarsi di avere la versione più recente di NuGet installato. Per ulteriori informazioni e istruzioni sull'installazione, vedere [NuGet](http://nuget.codeplex.com/).

2. In Esplora risorse, pulsante destro del mouse sul nome del progetto e scegliere Gestisci NuGet pacchetti...

    Viene visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

3. La raccolta in linea, cercare Azure MediaServices estensioni, scegliere Azure Media servizi .NET SDK estensioni e quindi fare clic sul pulsante Installa.

    Viene modificato il progetto e vengono aggiunti riferimenti a estensioni di file multimediali servizi .NET SDK, Media Services .NET SDK e altri assembly dipendenti.

4. Per alzare di livello un ambiente di sviluppo chiaro, è consigliabile attivare ripristinare pacchetto NuGet. Per ulteriori informazioni, vedere [ripristinare pacchetto NuGet "](http://docs.nuget.org/consume/package-restore).

3. Aggiungere un riferimento a assembly **Configuration** . Questo assembly contiene la Configuration. Classe **ConfigurationManager** utilizzati per accedere ai file di configurazione (ad esempio App).

    Per aggiungere riferimenti utilizzando la finestra di dialogo Gestisci riferimenti, fare clic sul nome del progetto in Esplora soluzioni. Scegliere Aggiungi e riferimenti.

    Verrà visualizzata la finestra di dialogo Gestisci riferimenti.

4. In assembly .NET framework, individuare e selezionare l'assembly Configuration e fare clic su OK.
5. Aprire il file App (aggiungere file al progetto se non è stato aggiunto per impostazione predefinita) e aggiungere una sezione *appSettings* al file.     
Impostare i valori per i servizi multimediali di Windows Azure account e nome chiave account, come illustrato nell'esempio seguente.

    Per trovare il nome e la chiave, accedere al portale di Azure e selezionare il proprio account. Sul lato destro viene visualizzata la finestra di impostazioni. Nella finestra Impostazioni selezionare chiavi. Fare clic sull'icona accanto a ogni casella di testo valore copiato negli Appunti di sistema.


        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

6. Sovrascrivere le istruzioni **using** esistenti all'inizio del file Program.cs con il codice riportato di seguito.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

A questo punto si è pronti iniziare a sviluppare un'applicazione di servizi di supporto.    


##<a name="media-services-learning-paths"></a>Percorsi formativi servizi multimediali

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Inviare commenti e suggerimenti

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
