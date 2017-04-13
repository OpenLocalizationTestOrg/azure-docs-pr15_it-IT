<properties
   pageTitle="Panoramica di Cache locale servizio App Azure | Microsoft Azure"
   description="In questo articolo viene descritto come attivare, ridimensionare e lo stato della funzionalità della Cache locale di Azure App servizio della query"
   services="app-service"
   documentationCenter="app-service"
   authors="SyntaxC4"
   manager="yochayk"
   editor=""
   tags="optional"
   keywords=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/04/2016"
   ms.author="cfowler"/>

# <a name="azure-app-service-local-cache-overview"></a>Panoramica di Cache locale servizio App Azure

Contenuti app web Azure memorizzati in archiviazione Azure e viene mostrato in alto in modo permanente come una condivisione di contenuto. Questo modello può essere utilizzata con una varietà di App e presenta gli attributi seguenti:  

* Il contenuto è condiviso tra più istanze di macchine () dell'applicazione web.
* Il contenuto è permanente e può essere modificato eseguendo web app.
* File di log e i file di dati di diagnostica sono disponibili nella stessa cartella del contenuto condivisa.
* Pubblicazione di nuovi contenuti direttamente Aggiorna la cartella del contenuto. È possibile visualizzare immediatamente lo stesso contenuto tramite il sito Web di servizi e l'esecuzione di web app (in genere alcune tecnologie, ad esempio ASP.NET riavviata web app in alcune modifiche di file per ottenere il contenuto più recente).

Sebbene molte applicazioni web utilizzano una o tutte queste funzionalità, alcune applicazioni web si vuole semplicemente un archivio di contenuto massime prestazioni e di sola lettura che possono eseguire da con disponibilità. Queste applicazioni possono trarre vantaggio da un'istanza di macchine Virtuali di una cache locale specifica.

La caratteristica Cache locale di Azure App servizio fornisce una visualizzazione di ruolo web del contenuto. Il contenuto è una cache di annullamento del ma scrittura del contenuto dello spazio di archiviazione creati in modo asincrono all'avvio del sito. Quando la cache è pronta, il sito è cambiato per eseguire il contenuto della cache. App Web che eseguono nella Cache locale sono i seguenti vantaggi:

* Sono disfunzioni latenza che si verifica durante l'accesso a contenuto archiviazione Azure.
* Sono soggetti agli aggiornamenti pianificati o inattività non pianificato e eventuali altre interruzioni con lo spazio di archiviazione di Azure che si verificano nel server che servono la condivisione di contenuto.
* Devono avere un numero minore riavvio app variazioni di condividere lo spazio di archiviazione.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>La modalità Cache locale viene modificato il comportamento del servizio di App

* La cache locale è una copia delle cartelle /site e /siteextensions dell'applicazione web. Viene creata nell'istanza locale macchine Virtuali all'avvio dell'applicazione web. La dimensione della cache locale per l'applicazione web è limitata a 300 MB per impostazione predefinita, ma è possibile aumentare fino a 1 GB.
* La cache locale è di sola lettura. Tuttavia, le modifiche verranno eliminate quando l'applicazione web Sposta macchine virtuali o si ottiene riavvia. Utilizzare non Cache locale per le app che memorizzano dati critici nell'archivio del contenuto.
* Web App è possibile continuare a scrivere file di log e dati di diagnostica come quello attualmente. File di log e i dati, tuttavia, vengono archiviati in locale nella macchina virtuale. Quindi vengono copiati su periodicamente all'archivio di contenuto condiviso. La copia all'archivio di contenuto condiviso è una programmazione migliore - scrittura esegue il backup potrebbe andare perso dovuto a un arresto anomalo inaspettato di un'istanza di macchine Virtuali.
* Viene apportata una modifica la struttura delle cartelle di cartelle di file di registro e dati per le applicazioni web che utilizzano Cache locale. Le cartelle di file di registro e dati lo spazio di archiviazione che seguono il modello di denominazione di "identificatore" + timestamp sono ora sottocartelle. Tutte le sottocartelle corrisponde a un'istanza di macchine Virtuali nel punto in cui l'applicazione web è in esecuzione o è stato eseguito.  
* Pubblicazione le modifiche apportate all'app web tramite qualsiasi i meccanismi di pubblicazione verranno pubblicato all'archivio di contenuto condiviso. Si tratta di una caratteristica predefinita perché vogliamo il contenuto pubblicato come permanente. Per aggiornare la cache locale dell'applicazione web, è necessario essere riavviato. Si sembrare un passaggio di un numero eccessivo? Per rendere il ciclo di vita senza problemi, vedere le informazioni più avanti in questo articolo.
* D:\home farà riferimento per la cache locale. D:\Local continuerà che puntano all'archiviazione specifiche macchine Virtuali temporanea.
* La visualizzazione di contenuto predefinito del sito servizi continuerà a essere che l'archivio di contenuto condiviso.

## <a name="enable-local-cache-in-app-service"></a>Abilitare la Cache locale nel servizio di App

Per configurare Cache locale, utilizzando una combinazione di impostazioni app riservato. È possibile configurare queste impostazioni app usando i metodi seguenti:

* [Portale di Azure](#Configure-Local-Cache-Portal)
* [Gestione risorse di Azure](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Configurare la Cache locale tramite il portale di Azure
<a name="Configure-Local-Cache-Portal"></a>

Per attivare Cache locale alla scala cronologica per online, utilizzare questa impostazione app:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Le impostazioni dell'app portale Azure: Cache locale](media/app-service-local-cache/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Configurare la Cache locale tramite Gestione risorse di Azure
<a name="Configure-Local-Cache-ARM"></a>

```
...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],
    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Modificare l'impostazione di dimensione nella Cache locale

Per impostazione predefinita, la dimensione della cache locale è **300 MB**. Sono inclusi i /site e cartelle /siteextensions che vengono copiate dall'archivio di contenuto, nonché eventuali cartelle di log e dati creati localmente. Per aumentare questo limite, utilizzare l'impostazione di app `WEBSITE_LOCAL_CACHE_SIZEINMB`. È possibile aumentare le dimensioni fino a **1 GB** (1000 MB) per web app.

## <a name="best-practices-for-using-app-service-local-cache"></a>Procedure consigliate per l'uso di Cache locale del servizio di App

È consigliabile utilizzare Cache locale in combinazione con la funzionalità di [Gestione temporanea ambienti](../app-service-web/web-sites-staged-publishing.md) .

* Aggiungere l'impostazione di app _permanenti_ `WEBSITE_LOCAL_CACHE_OPTION` con il valore `Always` per l'intervallo di **produzione** aperto. Se si usa `WEBSITE_LOCAL_CACHE_SIZEINMB`, aggiungere anche come impostazione permanente per l'intervallo di aperto di produzione.
* Creare un intervallo di **gestione temporanea** aperto e pubblica per l'intervallo di aperto di gestione temporanea. In genere non impostare l'intervallo di gestione temporanea aperto come utilizzare Cache locale per consentire un ciclo di vita del test distribuire compilazione continua per la gestione temporanea se si riceve i vantaggi della Cache locale per l'intervallo di aperto di produzione.
*   Verificare il sito con l'intervallo di aperto di gestione temporanea.  
*   Quando si è pronti, eseguire un' [operazione di scambio](../app-service-web/web-sites-staged-publishing.md#to-swap-deployment-slots) tra gli intervalli di prova e di produzione.  
*   Impostazioni permanenti includono nome e permanenti spazio. Pertanto quando l'intervallo di aperto di gestione temporanea Ottiene invertita nell'ambiente di produzione, esso verrà ereditano le impostazioni di app Cache locale. L'appena scambiate produzione verrà eseguita sulla cache locale dopo alcuni minuti e verrà riscaldati come parte di riscaldamento intervallo aperto dopo Scambia. Pertanto una volta completato lo scambio di intervallo aperto, l'intervallo di aperto di produzione verrà eseguito rispetto alla cache locale.

## <a name="frequently-asked-questions-faq"></a>Domande frequenti domande frequenti

### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Come stabilire se Cache locale si applica all'applicazione web

Se un'app web di un archivio di contenuto massime prestazioni affidabile, è necessario, non utilizza l'archivio del contenuto per la scrittura dei dati critici in fase di esecuzione ed è minore di 1 GB di dimensione totale, la risposta è "Sì"! Per ottenere le dimensioni totali delle cartelle /site e /siteextensions, è possibile utilizzare l'estensione del sito "Utilizzo di disco App Web di Azure".  

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Come è possibile verificare se il sito è cambiato all'uso di Cache locale?

Se si usa la caratteristica Cache locale con ambienti di gestione temporanea, l'operazione di scambio non verrà completata fino a quando non viene riscaldata Cache locale. Per verificare se il sito esegue sulla Cache locale, è possibile controllare la variabile di ambiente di lavoro processo `WEBSITE_LOCALCACHE_READY`. Utilizzare le istruzioni nella pagina [variabile di ambiente del processo di lavoro](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) per accedere alla variabile di ambiente processo di lavoro su più istanze.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Dopo la pubblicazione solo nuove modifiche, ma le app web sembra non devono essere. Perché?

Se l'applicazione web utilizza Cache locale, è necessario riavviare il sito per ottenere le ultime modifiche. Non si vuole pubblicare le modifiche in un sito di produzione? Vedere le opzioni di intervallo aperto nella precedente sezione consigliate migliore.

### <a name="where-are-my-logs"></a>Dove si trovano i registri?

Con la Cache locale, le cartelle di dati e i registri delle apparire leggermente diverse. Tuttavia, la struttura delle sottocartelle rimane lo stesso, ad eccezione del fatto che le sottocartelle sono annidate in una sottocartella con il formato "macchine Virtuali identificatore univoco" + Data / ora.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Ho Cache locale abilitato, ma le app web ottiene è riavviato. Perché? Ho pensato che cache locale contribuito con riavvio app frequenti.

Cache locale per evitare il riavvio di app web correlati dello spazio di archiviazione. Tuttavia, un'applicazione web potrebbe comunque essere sottoposte a riavvio durante l'aggiornamento pianificato dell'infrastruttura di macchina virtuale. Riavvio del app complessivo che si verifica con Cache locale abilitato deve essere minore.
