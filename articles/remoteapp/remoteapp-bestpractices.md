<properties
    pageTitle="Procedure consigliate di Azure RemoteApp | Microsoft Azure"
    description="Procedure consigliate per la configurazione e uso RemoteApp di Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="best-practices-for-configuring-and-using-azure-remoteapp"></a>Procedure consigliate per la configurazione e uso RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Le informazioni seguenti consentono di configurare e utilizzare RemoteApp di Azure in modo produttivo.

## <a name="connectivity"></a>Connettività


- Usa sempre la versione più recente di client. Utilizzando i client meno recenti potrebbe causare problemi di connettività e altre esperienze peggiore. Abilitare gli aggiornamenti automatici delle applicazioni del dispositivo verificare che il client più recente sia installato sempre.
- Usa sempre la connessione internet disponibile più stabile e affidabile.  
- Utilizzare solo le connessioni proxy per ottenere prestazioni ottimali connettività.  Proxy SOCKS non è supportato.

## <a name="applications"></a>Applicazioni


- Salvare e chiudere le applicazioni RemoteApp quando vengono eseguite con l'applicazione. Non chiudere l'applicazione può comportare la perdita di dati.
- Convalidare le applicazioni personalizzate prima di utilizzarli in Azure RemoteApp. Sono inclusi garantire che lavorare su una piattaforma più di sessione e non utilizzare risorse non necessarie, ad esempio memoria e CPU che potrebbe dannoso per un altro utente nella stessa raccolta. Per informazioni, scaricare e verificare la [Compatibilità procedure consigliate per applicazioni per Servizi Desktop remoto](http://www.dabcc.com/resources/Application%20Compatibility%20Best%20Practices%20for%20Remote%20Desktop%20Services.pdf).

## <a name="configuration-and-management"></a>Gestione e configurazione


- Mantenere le immagini di modello aggiornato, l'installazione degli aggiornamenti software e altre correzioni importanti in base alle esigenze. In questo modo che come scale di auto Azure RemoteApp per soddisfare le capacità, è corretto, ogni istanza.  
- Assicurarsi che la distribuzione di Active Directory Federation Services (ADFS) è sicura e affidabile. In caso contrario l'autenticazione client possono avere esito negativo, impedendo agli utenti di accedere a RemoteApp di Azure.
- Configurare le immagini dei modelli con le applicazioni installate, ruoli o funzionalità in modo che sono prive di stato. Non basarsi su tutte le istanze di macchine virtuali in un servizio RemoteApp in uno stato persistente.
    - Archiviare tutti i dati utente in profili utente o altri percorsi di archiviazione esterni al servizio, ad esempio locale file condivisioni o OneDrive.
    - Archiviare dati condivisi in percorsi di archiviazione esterni al servizio, ad esempio locale file condivisioni o OneDrive.
    - Configurare le impostazioni di sistema nell'immagine del modello, invece che su singole macchine virtuali in un servizio.
    - Disattivare gli aggiornamenti automatici del software per le applicazioni pubblicate - alternativa applicare manualmente l'immagine di modello e testarli prima di distribuire dal modello.
