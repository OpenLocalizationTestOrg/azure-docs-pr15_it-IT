<properties
    pageTitle="Che cos'è l'automazione Azure | Microsoft Azure"
    description="Informazioni su quali valore automazione Azure fornisce e ottenere risposte alle domande frequenti in modo che per iniziare la creazione, utilizzo runbook e Azure automazione DSC."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="che cos'è l'automazione, automazione azure, esempi di automazione azure"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="05/10/2016"
    ms.author="magoedte;bwren"/>

# <a name="azure-automation-overview"></a>Panoramica di automazione Azure

Automazione di Microsoft Azure consente agli utenti di automatizzare le attività manuale lunga, errori e frequenti che in genere eseguite in un ambiente cloud e dell'organizzazione. Consente di risparmiare tempo e aumenta l'affidabilità dei normali attività amministrative e anche le pianifica eseguire automaticamente a intervalli regolari. È possibile automatizzare processi utilizzando runbook o automatizzare la gestione della configurazione utilizzando Configurazione dello stato bene accolta. In questo articolo fornisce una breve panoramica di automazione di Azure e fornisce le risposte ad alcune domande comuni. È possibile fare riferimento ad altri articoli in questa raccolta per informazioni più dettagliate sugli argomenti diversi.


## <a name="automating-processes-with-runbooks"></a>Automazione dei processi con runbook

Un runbook è un insieme di attività da eseguire il processo automatizzato di automazione di Azure. Potrebbe essere un processo semplice, ad esempio un computer virtuale e la creazione di una voce di log o si abbia un runbook complesse che combina altri runbook più piccolo per eseguire un processo complesso tra più risorse o persino più aree e sugli ambienti locale.  

Ad esempio, si potrebbe disporre di un processo manuale esistente del troncamento un database SQL se si avvicina a dimensioni massime che include più passaggi, ad esempio la connessione al server, la connessione al database, ottenere le dimensioni del database corrente, controllare se soglia ha superato troncare e notificare utente. Invece di eseguire manualmente queste operazioni, è possibile creare un runbook per l'esecuzione tutte le attività come un unico processo. Iniziare dal runbook, fornire le informazioni necessarie, ad esempio il nome del server SQL, il nome del database e posta elettronica del destinatario e quindi occupare durante il completamento del processo. 


## <a name="what-can-runbooks-automate"></a>Cosa può automatizzare runbook?

Runbook in Azure automazione sono basato su Windows PowerShell o flusso di lavoro di Windows PowerShell, in modo che si tutto ciò che è possibile eseguire PowerShell. Se un'applicazione o servizio include un'API, è possibile utilizzarli un runbook. Se si dispone di un modulo PowerShell per l'applicazione, è possibile caricare tale modulo Azure automazione e includere i cmdlet nel runbook. Azure automazione runbook eseguire nel cloud Azure e possono accedere a qualsiasi cloud risorse o risorse esterne che è possibile accedervi dal cloud. Usa [Lavoro Runbook ibrida](automation-hybrid-runbook-worker.md), runbook eseguire nell'interfaccia di dati locali per gestire le risorse locali. 


## <a name="getting-runbooks-from-the-community"></a>Guida runbook dalla community

La [Raccolta Runbook](automation-runbook-gallery.md#runbooks-in-runbook-gallery) contiene runbook da Microsoft e alla community è possibile utilizzare invariato nel proprio ambiente o personalizzarli secondo le proprie esigenze. Sono inoltre utili come riferimenti per imparare a creare il proprio runbook. Si può contribuire anche il proprio runbook per la raccolta che si ritiene che altri utenti possono risultare utili. 


## <a name="creating-runbooks-with-azure-automation"></a>Creazione di runbook con automazione Azure 

È possibile [creare il proprio runbook](automation-creating-importing-runbook.md) da zero o modificare runbook dalla [Raccolta Runbook](http://msdn.microsoft.com/library/azure/dn781422.aspx) per le proprie esigenze. Esistono tre diversi [tipi di runbook](automation-runbook-types.md) che è possibile scegliere in base alle esigenze ed esperienza di PowerShell. Se si preferisce utilizzare direttamente il codice di PowerShell, è possibile utilizzare un [runbook PowerShell](automation-runbook-types.md#powershell-runbooks) o [runbook PowerShell del flusso di lavoro](automation-runbook-types.md#powershell-workflow-runbooks) che si modifica offline o con l' [editor di testo](http://msdn.microsoft.com/library/azure/dn879137.aspx) nel portale di Azure. Se si preferisce per modificare un runbook senza vengano esposti al codice sottostante, è possibile creare un [grafico runbook](automation-runbook-types.md#graphical-runbooks) utilizzando l' [editor grafico](automation-graphical-authoring-intro.md) nel portale di Azure. 

Si preferisce spettatori lettura? Osservare la sotto video da Microsoft Ignite sessione in maggio 2015. Nota: Mentre i concetti e le funzionalità descritte in questo video sono corrette, che automazione di Azure avanzamento molto poiché è stato registrato in questo video, a questo punto è un'interfaccia utente più ampia nel portale di Azure e supporta la funzionalità aggiuntive.

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## <a name="automating-configuration-management-with-desired-state-configuration"></a>Gestione della configurazione con bene accolta configurazione dello stato di automazione 

[DSC PowerShell](https://technet.microsoft.com/library/dn249912.aspx) è una piattaforma di gestione che consente di gestire, distribuire e applicare una configurazione per host fisici e macchine virtuali utilizzando una sintassi di PowerShell dichiarativa. È possibile definire configurazioni su un DSC estrarre Server centrale che è possano recuperare e applicare automaticamente i computer di destinazione. DSC fornisce un set di cmdlet di PowerShell che è possibile utilizzare per gestire le configurazioni e risorse.  

[DSC automazione Azure](automation-dsc-overview.md) è una soluzione basata sul cloud per DSC PowerShell che fornisce i servizi necessari per gli ambienti aziendali.  È possibile gestire le risorse DSC in Azure automazione e applicare configurazioni in macchine virtuali o fisiche che recuperano da un Server di estrarre DSC nel cloud Azure.  Fornisce inoltre reporting services che determinano che le eventi importanti come i nodi hanno deviato dalla configurazione assegnata e quando una nuova configurazione è state applicate. 


## <a name="creating-your-own-dsc-configurations-with-azure-automation"></a>Creare le proprie configurazioni DSC con automazione Azure

[Configurazioni DSC](automation-dsc-overview.md#azure-automation-dsc-terms) specificare lo stato di un nodo desiderato.  Più nodi è possono applicare la stessa configurazione per assicurare che mantengono uno stato identico.  È possibile creare una configurazione utilizzando il testo eventualmente contenuto editor sul computer locale e quindi importare in automazione Azure in cui è possibile compilare e applicarlo nodi.


## <a name="getting-modules-and-configurations"></a>Ottenere le configurazioni e i moduli 

È possibile ottenere [PowerShell moduli](automation-runbook-gallery.md#modules-in-powershell-gallery) contenenti i cmdlet che è possibile usare nel runbook e configurazioni DSC dalla [Raccolta di PowerShell](http://www.powershellgallery.com/). È possibile avviare questa raccolta dal portale di Azure e importare moduli direttamente in Azure automazione oppure è possibile scaricare e importarli manualmente. Non è possibile installare i moduli direttamente dal portale di Azure, ma è possibile scaricare installarli come si farebbe con qualsiasi altro modulo. 


## <a name="example-practical-applications-of-azure-automation"></a>Applicazioni pratiche di esempio di automazione di Azure 

Ecco alcuni esempi delle quali sono i tipi di scenari di automazione con l'automazione di Azure. 

* Creare e copiare macchine virtuali in diversi abbonamenti Azure. 
* Pianificare le copie di file da un computer locale a un contenitore di archiviazione Blob Azure. 
* Automatizzare le funzioni di sicurezza, ad esempio negare le richieste da un client quando viene rilevato un attacco di negazione del servizio. 
* Garantire macchine continuamente allineamento con criteri di sicurezza configurati.
* Gestire la distribuzione continua codice dell'applicazione su cloud e sull'infrastruttura locale. 
* Creare un insieme di strutture di Active Directory in Azure per l'ambiente di laboratorio. 
* Troncare i valori di una tabella in un database SQL se DB si avvicina dimensioni massime. 
* Quando in modalità remota aggiornare le impostazioni di ambiente per un sito Web di Azure. 


## <a name="how-does-azure-automation-relate-to-other-automation-tools"></a>Come è la relazione tra Azure automazione e altri strumenti di automazione?

[Servizio di Gestione automazione (SMA)](http://technet.microsoft.com/library/dn469260.aspx) deve automatizzare le attività di gestione nel cloud privato. L'installazione in locale nel centro dati come componente di [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/). Automazione di Azure e SMA usare lo stesso formato runbook basato su Windows PowerShell e del flusso di lavoro di Windows PowerShell, ma SMA non supporta [runbook grafica](automation-graphical-authoring-intro.md).  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) è destinato automazione di risorse locale. Utilizza un formato runbook diverso da quello automazione di Azure e servizio di Gestione automazione e presenta un'interfaccia grafica per creare runbook senza creare degli script. Il relativo runbook sono costituiti da attività da Integration Pack scritti specificamente per Orchestrator. 


## <a name="where-can-i-get-more-information"></a>Dove è possibile ottenere altre informazioni? 

Una serie di risorse sono disponibili per ulteriori informazioni su Azure automazione e la creazione di propri runbook. 

* **Libreria di automazione Azure** è nel punto in cui ci si trova immediatamente. Gli articoli di questa raccolta forniscono la documentazione completa sulla configurazione e amministrazione di Azure automazione e per la modifica dei propri runbook. 
* [Cmdlet di PowerShell Azure](http://msdn.microsoft.com/library/jj156055.aspx) vengono fornite informazioni per automatizzare operazioni Azure usando Windows PowerShell. Runbook utilizzare i cmdlet per gestire le risorse Azure. 
* [Blog sulla gestione](https://azure.microsoft.com/blog/tag/azure-automation/) fornisce le informazioni più aggiornate su Azure automazione e altre tecnologie di gestione da Microsoft. È consigliabile sottoscrivere questo blog per mantenersi aggiornati con le ultime notizie dal team di automazione di Azure. 
* [Forum di automazione](http://go.microsoft.com/fwlink/p/?LinkId=390561) consente di pubblicare domande sull'automazione di Azure per essere gestita da Microsoft e dalla community di automazione. 
* [Cmdlet di automazione Azure](https://msdn.microsoft.com/library/mt244122.aspx) vengono fornite informazioni per automatizzare le attività amministrative. Contiene i cmdlet per la gestione di account di automazione, risorse, runbook, DSC.


## <a name="can-i-provide-feedback"></a>È possibile inviare commenti e suggerimenti? 

**Inviare commenti e suggerimenti!** Se si sta cercando una soluzione di runbook automazione Azure o un modulo di integrazione, pubblicare una richiesta di Script nell'interfaccia di Script. Se si dispone di commenti e suggerimenti o richieste per l'automazione di Azure, pubblicarli nella [Voce di utente](http://feedback.windowsazure.com/forums/34192--general-feedback). Grazie. 


