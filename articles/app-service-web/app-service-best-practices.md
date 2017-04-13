<properties
    pageTitle="Procedure consigliate per servizio App Azure"
    description="Procedure consigliate e risoluzione dei problemi del servizio di App Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Procedure consigliate per servizio App Azure

In questo articolo sono riepilogate procedure consigliate per l'uso di [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Installazione
Quando le risorse Azure la composizione di una soluzione, ad esempio un'app web e un database si trovano in diverse aree geografiche gli effetti possono includere le seguenti:

*  Maggiore latenza di comunicazione tra le risorse
*  In base alle tariffe monetari per i dati in uscita trasferire cross-area, come indicato nella [pagina prezzo Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Installazione della stessa regione più adatto per risorse Azure la composizione di una soluzione, ad esempio un account di database o lo spazio di archiviazione utilizzati per contenere il contenuto o dati e un'app web. Durante la creazione di risorse, assicurarsi che siano presenti nell'area di Azure stesso a meno che non è installato business specifiche o progettare motivo che non siano. È possibile spostare un'applicazione di servizio App alla stessa area del database utilizzando il [servizio App clonare caratteristica](app-service-web-app-cloning-portal.md) attualmente disponibili per pianificare servizio App Premium app.   

## <a name="memoryresources"></a>Quando App occupano più del previsto
Quando si nota un'app implica l'uso della memoria più del previsto come indicato tramite il monitoraggio o consigli su servizi di prendere in considerazione la [caratteristica di correzione automatica del servizio di App](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Una delle opzioni per la funzionalità Correzione automatica richiede azioni personalizzate in base a una determinata soglia della memoria. Azioni estendono spettro di notifiche tramite posta elettronica per indagini tramite memoria a attenuazione sul posto Riciclando il processo di lavoro. Correzione automatica può essere configurato tramite config e tramite un'interfaccia utente intuitiva come descritto in questo post di blog per l' [Estensione del sito di App del servizio supporto tecnico](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando App utilizzano CPU più del previsto
Quando si noterà che un'app implica l'uso di più CPU superiore a quello previsto o esperienze ripetuto CPU viene utilizzata come indicato tramite il monitoraggio o consigli valutare la possibilità di scalabilità o in orizzontale il piano di servizio di App. Se l'applicazione è trovato, scalabilità è l'unica opzione disponibile, mentre se l'applicazione è in uscita e ridimensionamento si otterranno maggiore flessibilità e potenziale scala superiore. 

Per ulteriori informazioni sulle applicazioni "senza informazioni sullo stato" vs "trovato" è possibile guardare questo video: [pianificare un'applicazione di più livelli Scalable - to-End in Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Per ulteriori informazioni sulle opzioni di ridimensionamento e il ridimensionamento automatico del servizio di App di lettura: [scala un'App Web nel servizio App Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando le risorse di socket sono esaurite
In genere gli esaurire le connessioni in uscita TCP è l'utilizzo di librerie client che non è implementate riutilizzare le connessioni TCP o in caso di un protocollo di livello superiore, ad esempio HTTP - non vengono utilizzato attivo a mantenere. Consultare la documentazione per ognuna delle raccolte di cui fa riferimento App del piano di servizio App per assicurarsi che vengono configurati o accedere nel codice per il riutilizzo efficiente di connessioni in uscita. Anche seguire le indicazioni della documentazione di raccolta per di creazione e rilascio o pulizia per evitare la perdita di connessioni. Mentre tali librerie client indagini sono in effetti lo stato di avanzamento possono evitando scalabilità a più istanze.  

## <a name="appbackup"></a>Quando l'app backup avvii non funziona
Le due più comuni motivi per cui si verifica un errore backup app: impostazioni di archiviazione non validi e configurazione di database non valido. Questi errori, in genere, si verificano quando sono presenti modifiche alle risorse di archiviazione o database o le modifiche alla modalità di accedere a queste risorse (ad esempio le credenziali dell'aggiornamento per il database selezionato nelle impostazioni di backup). L'esecuzione di backup in genere eseguito su una pianificazione e richiedono l'accesso al database e lo spazio di archiviazione (per l'output dei backup dei file) (per la copia e lettura contenuto da includere nel backup). Il risultato di accedere a una di queste risorse è coerente errori di backup. 

Quando si verificano errori di backup, esaminare i risultati più recenti per comprendere il tipo di errore in corso. In caso di errori di accesso di spazio di archiviazione, esaminare e aggiornare le impostazioni di archiviazione usate nella configurazione di backup. In caso di errori di accesso di database, esaminare e aggiornare le stringhe di connessione come parte delle impostazioni di app; procedere quindi aggiornare la configurazione di backup per includere correttamente i database necessari. Per ulteriori informazioni sulla copia di backup app vedere la documentazione [backup automatici di un'app web nel servizio App Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Quando vengono distribuite nuove app Node al servizio App Azure
Azure configurazione predefinita del servizio di App per App Node è destinata in base alle proprie esigenze di App più comuni. Se configurazione per l'app Node da trarre vantaggio da ottimizzazioni personalizzate per migliorare le prestazioni o ottimizzare l'utilizzo delle risorse per le risorse CPU/memoria/rete, è possibile esaminare le procedure consigliate e risoluzione dei problemi. In questo articolo documentazione descrive le impostazioni di iisnode potrebbe essere necessario configurare per l'app Node, vengono descritti i diversi scenari o problemi che potrebbe essere esposti l'app e viene illustrato come risolvere questi problemi: [Guida alla risoluzione dei problemi per le applicazioni di nodo del servizio di App Azure e procedure consigliate](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   


