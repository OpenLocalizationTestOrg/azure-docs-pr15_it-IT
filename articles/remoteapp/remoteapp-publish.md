<properties
    pageTitle="Pubblicare un'app in Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come pubblicare applicazioni e risorse in Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Come pubblicare un'app in RemoteApp

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Dopo aver creato la raccolta RemoteApp, è necessario pubblicare le App e le risorse che si desidera rendere disponibile per gli utenti. Le immagini dei modelli fornite con l'abbonamento sono solo pochi App pubblicate per impostazione predefinita, per condividere le altre applicazioni, è necessario pubblicarle.

> [AZURE.NOTE] È necessario aggiornare un'app? È necessario [aggiornare l'immagine](remoteapp-update.md) prima di tutto.

Nella scheda nel portale di **pubblicazione** , fare clic su **pubblica**. È possibile aggiungere un'app dal menu di **avvio** dell'immagine di modello o specificare il percorso in cui è installato l'app sull'immagine del modello. Se si sceglie di aggiungere dal menu **Start** , scegliere l'app per la pubblicazione dall'elenco. Se si sceglie di specificare il percorso per l'app, immettere un nome per l'app e il percorso all'app. Utilizzare le variabili di percorso, ad esempio, "% systemdrive %" anziché "c:\".

> [AZURE.NOTE] Se si desidera aggiungere l'app dal menu di **avvio** , è necessario disporre *aggiunto che app per il * *avviare* * menu nell'immagine del modello.* In caso contrario, RemoteApp verranno visualizzate solo quali *è* sul **pulsante Start** e sarà confuso. 

>Per assicurarsi che l'app nel menu **Start** , inserire un file di scelta rapida - **lnk** - all'interno della cartella Start\Programmi %systemdrive%\ProgramData\Microsoft\Windows\Start.

> Se si dimentica di aggiungere l'app nel menu **Start** al momento della creazione del modello, scegliere di aggiungere il percorso dell'app. (O ricreare l'immagine del modello, ma lo è una quantità maggiore di lavoro.)


 