<properties
   pageTitle="Aggiornare la raccolta di Azure RemoteApp | Microsoft Azure"
   description="Informazioni su come aggiornare la raccolta RemoteApp di Azure"
   services="remoteapp"
   documentationCenter=""
   authors="lizap"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="update-a-collection-in-azure-remoteapp"></a>Aggiornare una raccolta in RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Sono venga recapitata una volta, inevitabile, quando è necessario aggiornare l'App o l'immagine nella raccolta RemoteApp di Azure. Se si utilizza una delle immagini incluse nell'abbonamento Azure RemoteApp insieme cloud o ibrida, che tutti gli aggiornamenti vengono gestiti dal RemoteApp di Azure, in modo che è possibile posizionare facile.

Tuttavia, se si usa un'immagine personalizzata (che è stato generato da zero o creato modificando una delle immagini), si è responsabile della gestione di App. Se è necessario aggiornare l'immagine o una delle App in essa contenuti, è necessario creare una nuova versione aggiornata dell'immagine e quindi sostituire l'immagine esistente nella raccolta con la nuova immagine aggiornata.

Pertanto, come procedere per informazioni sull'aggiornamento della raccolta? È semplice:

1. Aggiornare l'immagine che è stato utilizzato nella raccolta. Applicare gli eventuali patch o aggiornamenti necessari e quindi salvarla con un nuovo nome.
2. [Caricare](remoteapp-uploadimage.md) o [importare](remoteapp-image-on-azurevm.md) che immagine RemoteApp.
3. A questo punto, nella pagina raccolta fare clic su **Aggiorna**.
4. Scegliere la nuova immagine dall'elenco **Immagine del modello** .
4. Di seguito è difficile, è necessario decidere come gestire gli utenti che utilizzano un'app nella raccolta. Sono disponibili le opzioni seguenti:
    - **Assegnare agli utenti 60 minuti dopo l'aggiornamento**. Non appena l'aggiornamento viene completato, Azure RemoteApp verrà visualizzato un messaggio a tutti gli utenti attivi informarli per salvare i dati e disconnettersi e accedere nuovamente. Dopo aver 60 minuti, tutti gli utenti attivi che non sono connessi disattivare verranno automaticamente disconnessi. Gli utenti possono eseguire immediatamente nuovamente l'accesso.
    - **Accedere immediatamente gli utenti**. Non appena l'aggiornamento viene completato, disconnettersi da tutti gli utenti automaticamente senza alcun avviso. Se si sceglie questa opzione, gli utenti potrebbero perdere dati. Tuttavia, possano riconnettersi all'app immediatamente.

1. Fare clic sul segno di spunta per avviare l'aggiornamento.
