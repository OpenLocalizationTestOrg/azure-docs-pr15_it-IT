<properties 
    pageTitle="Guida per gli sviluppatori di Azure per la pubblica amministrazione" 
    description="In questo modo un confronto delle caratteristiche e indicazioni sullo sviluppo di applicazioni per la pubblica amministrazione Azure" 
    services="" 
    cloud="gov"
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  <a name="microsoft-azure-government-developer-guide"></a>Guida per gli sviluppatori di Microsoft Azure per la pubblica amministrazione 

<p> Microsoft Azure Government è un fisica e rete isolata istanza di Microsoft Azure.  Questa Guida per gli sviluppatori verrà forniti i dettagli sulle differenze che gli sviluppatori di applicazioni e gli amministratori devono interagire e lavorare con queste aree separate del Azure.

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## <a name="in-this-topic"></a>In questo argomento


+ [Panoramica](#Overview)
+ [Guida per sviluppatori](#Guidance)
+ [Caratteristiche attualmente disponibili in Microsoft Azure Government](#Features)
+ [Mapping degli endpoint](#Endpoint)
+ [Passaggi successivi](#next)


## <a name="Overview"></a>Panoramica

Microsoft Azure Government è un'istanza separata del servizio di Microsoft Azure indirizzare le esigenze di sicurezza e conformità di enti federal statunitensi, stato e governi locale e i relativi provider di soluzioni. Per la pubblica amministrazione Azure offre fisica e isolamento da distribuzioni per enti pubblici non statunitensi rete e fornisce schermato personale IT. 

Microsoft offre diversi strumenti per creare e distribuire le applicazioni cloud globale Microsoft Azure servizio ("globale") e servizi Microsoft Azure Government Microsoft.

Durante la creazione e la distribuzione di applicazioni per gli sviluppatori di servizi per la pubblica amministrazione di Windows Azure, anziché il servizio globale conoscere le principali differenze delle due servizi.  In particolare all'interno di installazione e configurazione il proprio ambiente di programmazione, configurazione endpoint, la creazione di applicazioni e distribuirle come servizi per la pubblica amministrazione Azure.

Le informazioni in questo documento sono riepilogate le differenze e integrano le informazioni disponibili sul sito(http://www.azure.com/gov "Per la pubblica amministrazione Azure") [Per enti pubblici Azure]e la [Raccolta di documentazione tecnica di Microsoft Azure](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") su MSDN. Informazione ufficiale potrebbe essere disponibili in molte altre posizioni ad esempio il [Microsoft Azure Trust Center] (https://azure.microsoft.com/support/trust-center/ "Microsoft Azure Trust Center" /), [Centro documentazione Azure](https://azure.microsoft.com/documentation/) e in [blog Azure] (https://azure.microsoft.com/blog/ "Blog Azure" /). 

Il contenuto è destinato agli sviluppatori che distribuiscono in Microsoft Azure Government e partner.



## <a name="Guidance"></a>Guida per sviluppatori
Poiché la maggior parte dei contenuti tecnici che sono disponibile al momento si presuppone che le applicazioni sono in fase di sviluppo per il servizio globale anziché per la pubblica amministrazione di Microsoft Azure, è importante per garantire che gli sviluppatori siano a conoscenza delle principali differenze per le applicazioni sviluppate per essere ospitati in Azure per enti pubblici.

- Prima di tutto, esistono differenze di caratteristiche e servizi, significa che alcune caratteristiche disponibili in aree specifiche del servizio globale potrebbero non essere disponibile in Azure per enti pubblici.

- Secondo, per le funzionalità disponibili in Azure per enti pubblici, esistono differenze di configurazione del servizio globale.  Di conseguenza, esaminare il codice di esempio, le configurazioni e i passaggi per assicurarsi che la creazione e l'esecuzione all'interno dell'ambiente di servizi Cloud per enti pubblici di Windows Azure.


## <a name="Features"></a>Caratteristiche attualmente disponibili in Microsoft Azure Government
Azure per la pubblica amministrazione attualmente è i seguenti servizi nelle aree IOWA Contattaci GOV e Contattaci GOV VIRGINIA:

- Macchine virtuali
- Servizi cloud
- Spazio di archiviazione
- Active Directory
- Utilità di pianificazione
- Rete virtuale
- Database SQL
- File di Azure
- Servizi multimediali di Microsoft
- Gestore del traffico
- Bus di servizio
- StorSimple
- Redis Cache
- Copia di Backup Azure
- Automazione
- ExpressRoute
- e così via.

Sono disponibili altri servizi e altri servizi verranno aggiunti alla scala cronologica continua.  Per un elenco aggiornato dei servizi, vedere la [pagina delle aree geografiche](https://azure.microsoft.com/regions/#services) che evidenzia ogni regione disponibili e i servizi.  

Contattaci Iowa GOV e Contattaci GOV Virginia sono attualmente data center di supporto per la pubblica amministrazione Azure.  Fare riferimento alla pagina di aree sopra per centri dati corrente e i servizi disponibili.

## <a name="Endpoint"></a>Mapping degli endpoint

Utilizzare le varie fasi durante il mapping endpoint di Microsoft Azure e Database SQL di pubblico per gli endpoint specifici per la pubblica amministrazione Azure nella tabella seguente.


Tipo di servizio|Pubblica Azure|Azure per la pubblica amministrazione
---|---|---
Portale di gestione|Manage.windowsazure.com|Manage.windowsazure.us
Generale|*. windows.net|*. usgovcloudapi.net
Principali|*. core.windows.net|*. core.usgovcloudapi.net
Calcolare|*. cloudapp.net|*. usgovcloudapp.net
Archiviazione BLOB|*. blob.core.windows.net|   *. blob.core.usgovcloudapi.net
Spazio di archiviazione coda|*. queue.core.windows.net|*. queue.core.usgovcloudapi.net
Archiviazione tabella|*. table.core.windows.net|*. table.core.usgovcloudapi.net
Gestione dei servizi|Management.Core.Windows.NET|Management.Core.usgovcloudapi.NET
Database SQL|*. database.windows.net|*. database.usgovcloudapi.net
Endpoint di bilanciamento del carico ARM|https://Management.Windows.NET|https://Management.usgovcloudapi.NET  

* Per l'autenticazione ARM tramite Azure Active Directory, fare riferimento [Autenticazione Azure Manager delle risorse richieste](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>Passaggi successivi

Se si desidera ottenere ulteriori e per enti pubblici Azure sfruttare alcuni dei collegamenti seguenti.

- **[Iscriversi a una versione di valutazione](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[Durante l'acquisizione e l'accesso per la pubblica amministrazione Azure](http://azure.com/gov)**

- **[Panoramica di Azure per la pubblica amministrazione](/azure-government-overview)**

- **[Blog di Azure per la pubblica amministrazione](http://blogs.msdn.com/b/azuregov/)**

- **[Conformità Azure](https://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md
