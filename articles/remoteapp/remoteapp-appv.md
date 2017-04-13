<properties
    pageTitle="Usare le app di App-V con RemoteApp Azure | Microsoft Azure"
    description="Informazioni su come usare le app App-V in Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Usare le app di App-V in RemoteApp Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

È possibile utilizzare le applicazioni di App-V in una raccolta di ibrido RemoteApp di Azure, che richiede l'aggiunta al dominio.

Prima di iniziare, verificare che installare il client App-V 5.1 con gli aggiornamenti più recenti. È necessario creare un' [immagine personalizzata](remoteapp-create-custom-image.md) che include il client App-V.  

È facile da usare infrastruttura App-V esistente con RemoteApp di Azure. Poiché un insieme di ibrido viene distribuito in un VNET Azure che ha accesso a controller di dominio e macchine virtuali sono dominio, è possibile sfruttare le App-v infrastruttura e distribuzione metodi esistenti all'applicazione di App-V host easyily in Azure RemoteApp. Di seguito sono riportate alcune considerazioni da tenere presenti in base al tipo di distribuzione App-V in uso non ha:

| Opzioni di configurazione |                       | Positivo                                                               | Negativo                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Metodo di recapito       | Trasmissione (su richiesta) | App è sempre le ultime e nuove                                     | Latenza di tempo prima                                                                                    |
|                       | Installato               | Più veloce; App è già presenta nella macchina virtuale                              | Gonfiamento - occupa spazio di immagine (limite 127 GB)                                                           |
| Spazio di archiviazione di app percorso  | Contenuto condiviso        | App viene eseguita in memoria dell'istanza RemoteApp di Azure                         | Davvero memoria e buona connessione a streaming server (file) in cui si trova l'app                      |
|                       | Disco (cache)         | Esecuzione veloce. App non dipende dalla disponibilità di origine di contenuto | Gonfiamento - occupa spazio di immagine (limite 127 GB)                                                           |
| L'assegnazione del             | Utente                  | Richiede l'infrastruttura di App-V autonomo completo                          |                                                                                                       |
|                       | Globale (computer)      |  Pre-pubblicare o di destinazione mediante la pubblicazione server                         |  È necessario aggiornare l'immagine Azure se si desidera aggiornare l'app (grande). Consente di accedere spazio su immagine. |

 Dopo aver creato l'immagine personalizzata e la raccolta ibrida, pubblicare l'applicazione, assegnare agli utenti e applicazioni App-V esistente ospitato in Azure RemoteApp recapitato a qualsiasi dispositivo in un punto qualsiasi.
