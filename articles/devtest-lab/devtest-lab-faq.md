<properties
    pageTitle="Esercitazioni di Azure DevTest domande frequenti su | Microsoft Azure"
    description="Trovare le risposte alle domande frequenti di Azure DevTest esercitazioni"
    services="devtest-lab,virtual-machines"
    documentationCenter="na"
    authors="tomarcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="devtest-lab"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="tarcher"/>

# <a name="azure-devtest-labs-faq"></a>Esercitazioni di Azure DevTest domande frequenti

In questo articolo fornisce le risposte ad alcune domande comuni sulle esercitazioni DevTest Azure.

[AZURE.INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="general"></a>Generale
- [Cosa fare se una domanda non ha ricevuto risposta qui?](#what-if-my-question-isnt-answered-here)
- [Perché è opportuno usare Azure DevTest esercitazioni?](#why-should-i-use-azure-devtest-labs) 
- [Che cosa significa "tranquillità, self-service"?](#what-does-quotworry-free-self-servicequot-mean)
- [Come è possibile utilizzare Azure DevTest esercitazioni?](#how-can-i-use-azure-devtest-labs) 
- [Come viene effettuata per le esercitazioni DevTest Azure?](#how-am-i-billed-for-azure-devtest-labs) 
 
## <a name="security"></a>Sicurezza 
- [Che cosa sono diversi livelli di protezione in Azure DevTest esercitazioni?](#what-are-the-different-security-levels-in-azure-devtest-labs) 
- [Creazione di un ruolo per consentire agli utenti di eseguire una specifica attività](#how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task) 
 
## <a name="cicd-integration--automation"></a>Automazione e integrazione CI/CD 
 
- [Azure DevTest esercitazioni si integra con le toolchain CI/CD?](#does-azure-devtest-labs-integrate-with-my-cicd-toolchain) 
 
## <a name="virtual-machines"></a>Macchine virtuali 
 
- [Perché non vengono visualizzate determinate macchine virtuali in e il macchine virtuali di Azure visualizzata all'interno di Azure DevTest esercitazioni?](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs) 
- [Che cos'è la differenza tra immagini personalizzate e le formule?](#what-is-the-difference-between-custom-images-and-formulas) 
- [Come creare più macchine virtuali dallo stesso modello contemporaneamente?](#how-do-i-create-multiple-vms-from-the-same-template-at-once) 
- [Come spostare le macchine virtuali Azure esistente nel mio laboratorio Azure DevTest esercitazioni?](#how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab) 
- [È possibile associare più dischi personale macchine virtuali?](#can-i-attach-multiple-disks-to-my-vms) 
- [Come è possibile automatizzare il processo di caricamento di file disco rigido virtuale per creare immagini personalizzate?](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images) 
- [Come è possibile automatizzare il processo di eliminazione tutte le macchine virtuali nel mio laboratorio?](#how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab)
 
## <a name="artifacts"></a>Elementi 
 
- [Quali sono gli elementi?](#what-are-artifacts) 
 
## <a name="lab-configuration"></a>Configurazione dell'esercitazione 
 
- [Creazione di un ambiente di lavoro da un modello di gestione risorse di Azure](#how-do-i-create-a-lab-from-an-azure-resource-manager-template) 
- [Perché le macchine virtuali create in gruppi di risorse diversi con i nomi non autorizzati? È possibile rinominare o modificare i gruppi di risorse?](#why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups) 
- [Esercitazioni quanti è possibile creare nello stesso abbonamento?](#how-many-labs-can-i-create-under-the-same-subscription)
- [Quanti macchine virtuali è possibile creare per laboratorio?](#how-many-vms-can-i-create-per-lab)
- [Come è possibile condividere un collegamento diretto al mio laboratorio?](#how-do-i-share-a-direct-link-to-my-lab)
- [Che cos'è un account Microsoft?](#what-is-a-microsoft-account)
 
## <a name="troubleshooting"></a>Risoluzione dei problemi 
 
- [Personale elemento non è riuscita durante la creazione di macchine Virtuali. Come risolvere essa?](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it) 
- [Perché non è del proprio account virtuale di rete salvataggio correttamente?](#why-isnt-my-existing-virtual-network-saving-properly)  

### <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se una domanda non ha ricevuto risposta qui?
Se la domanda non è elencata qui, indicare e Internet consente di trovare una risposta.

- Inserire una domanda nella [Disqus thread](#comments) alla fine di queste domande frequenti e con il team di Azure Cache e altri membri della community su questo articolo.
- Per raggiungere un pubblico più ampio, pubblicare una domanda nel [forum MSDN di Azure DevTest esercitazioni](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs)e con il team di Azure DevTest esercitazioni e gli altri membri della community.
- Per rendere una richiesta di funzionalità, inviare le convocazioni e idee [Azure DevTest esercitazioni utenti vocali](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="why-should-i-use-azure-devtest-labs"></a>Perché è opportuno usare Azure DevTest esercitazioni? 
Azure esercitazioni DevTest possibile salvare il tempo di team e denaro. Gli sviluppatori possono creare ambienti con più basi diverse e utilizzare gli elementi per distribuire e configurare applicazioni rapidamente. Utilizzo di formule e immagini personalizzate, macchine virtuali possono essere salvate come modelli e riprodotto con facilità. Inoltre, esercitazioni offrono diversi criteri configurabili che consentono agli amministratori di laboratorio di ridurre gli sprechi e gestire gli ambienti del team. Questi criteri includono arresto, soglia costo, massime macchine virtuali per ogni utente e le dimensioni massime dei macchine Virtuali. Per una spiegazione più approfondita delle esercitazioni DevTest Azure, leggere la [Panoramica](devtest-lab-overview.md) o guardare il [video introduttivo](/documentation/videos/videos/what-is-azure-devtest-labs). 

### <a name="what-does-worry-free-self-service-mean"></a>Che cosa significa "tranquillità, self-service"?
"Tranquillità, Self-Service" significa che gli sviluppatori e tester creare il proprio ambiente in base alle esigenze e gli amministratori hanno la sicurezza di sapere che esercitazioni DevTest Azure consente di ridurre al minimo rifiuti e controllare costo. Gli amministratori possono specificare le dimensioni di macchine Virtuali consentite, il numero massimo di macchine virtuali, e quando macchine virtuali sono avviate e arrestate. Esercitazioni DevTest Azure facilmente anche monitorare i costi e impostare avvisi per rimanere conoscere come risorse in laboratorio in uso. 

### <a name="how-can-i-use-azure-devtest-labs"></a>Come è possibile utilizzare Azure DevTest esercitazioni? 
Esercitazioni DevTest Azure può essere utile ogni volta che richiedono dev o ambienti di prova e si desidera riprodurli rapidamente e/o gestirli con costo salvataggio criteri. 

Ecco alcuni scenari in cui gli utenti utilizzano Azure DevTest esercitazioni per: 

- Gestire gli ambienti di sviluppo e test in un'unica posizione, utilizzando i criteri per ridurre i costi e immagini personalizzate per condividere compila all'interno del team.
- Sviluppo delle applicazioni di immagini personalizzate per salvare lo stato del disco in tutte le fasi di sviluppo.
- Tenere traccia dei costi in relazione allo stato di avanzamento. 
- Creazione di ambienti di massa test per test controllo qualità.
- Utilizzo di elementi e le formule per configurare con facilità e riprodurre un'applicazione in ambienti diversi. 
- Distribuire macchine virtuali di hackathons (collaborativo dev o test lavoro) e quindi facilmente deselezionare il provisioning di loro al termine dell'evento. 

### <a name="how-am-i-billed-for-azure-devtest-labs"></a>Come viene effettuata per le esercitazioni DevTest Azure? 
Esercitazioni DevTest Azure è un servizio gratuito, ovvero creare esercitazioni e configurare i criteri, modelli e gli elementi è gratuito. Pagando solo per le risorse Azure utilizzate entro le esercitazioni, ad esempio macchine virtuali, gli account di archiviazione e le reti virtuali. Per ulteriori informazioni sul costo delle risorse, informazioni su [Azure DevTest esercitazioni prezzi](https://azure.microsoft.com/pricing/details/devtest-lab/). 

### <a name="what-are-the-different-security-levels-in-azure-devtest-labs"></a>Che cosa sono diversi livelli di protezione in Azure DevTest esercitazioni?  
Diritti di accesso è determinato dal [Controllo di accesso Azure Role-Based (RBAC)](../active-directory/role-based-access-built-in-roles.md). Per comprendere il funzionamento di access, è utile per comprendere le differenze tra un'autorizzazione, un ruolo e un ambito come definito da RBAC.

- **Autorizzazione** - un'autorizzazione è un accesso definito per una determinata azione. Un'autorizzazione, ad esempio, potrebbe essere macchine di accesso in lettura a tutti. 
- **Ruolo** - un ruolo è un set di autorizzazioni che possono essere raggruppati e assegnata a un utente. Ad esempio, un proprietario della sottoscrizione"" ha accesso a tutte le risorse all'interno di una sottoscrizione. 
- **Ambito** - un ambito è un livello nella gerarchia di Azure delle risorse. Ad esempio, potrebbe essere un ambito di un gruppo di risorse o un unico laboratorio o l'intero abbonamento. 
 
Nell'ambito di esercitazioni DevTest Azure, sono disponibili due tipi di ruoli per definire le autorizzazioni degli utenti: laboratorio proprietario e laboratorio utente.

- **Proprietario laboratorio** - un proprietario di laboratorio ha accesso alle risorse all'interno del laboratorio. Di conseguenza, essi possono modificare i criteri, leggere e scrivere macchine virtuali, modificare la rete virtuale e così via. 
- **Utente laboratorio** - un utente di laboratorio può visualizzare tutte le risorse di laboratorio, ad esempio macchine virtuali, criteri e le reti virtuali, ma non è possibile modificare i criteri o macchine virtuali creati da altri utenti. È anche possibile creare ruoli personalizzati in Azure DevTest esercitazioni e si imparerà vedere l'articolo [concedere autorizzazioni utente per i criteri di laboratorio specifico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). 
 
Poiché gli ambiti sono gerarchici, quando un utente disponga delle autorizzazioni per un determinato ambito, vengono automaticamente sono concesse le autorizzazioni in tutti gli ambiti di livello inferiore incluso. Ad esempio, se un utente viene assegnato il ruolo di proprietario della sottoscrizione, quindi hanno accesso a tutte le risorse in un abbonamento. Queste risorse includono tutte le macchine virtuali, tutte le reti virtuali e tutte le esercitazioni. In questo modo, il proprietario di un abbonamento eredita automaticamente il ruolo del proprietario dell'esercitazione. Tuttavia, l'operazione non è valido. Il proprietario di un laboratorio ha accesso a un laboratorio, è un ambito inferiore rispetto al livello di sottoscrizione. Di conseguenza, il proprietario di un laboratorio non è possibile vedere macchine virtuali o reti virtuali o tutte le risorse di fuori del laboratorio. 

### <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>Creazione di un ruolo per consentire agli utenti di eseguire una specifica attività
Un articolo completo su come creare ruoli personalizzati e assegnare autorizzazioni a tale ruolo sono disponibili qui. Ecco un esempio di uno script che crea il ruolo "DevTest esercitazioni avanzate" utente, che dispone dell'autorizzazione per avviare e arrestare tutti macchine virtuali di laboratorio:
 
    $policyRoleDef = Get-AzureRmRoleDefinition "DevTest Labs User" 
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
    $policyRoleDef.Id = $null 
    $policyRoleDef.Name = "DevTest Labs Advance User" 
    $policyRoleDef.IsCustom = $true 
    $policyRoleDef.AssignableScopes.Clear() 
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
    $policyRoleDef = New-AzureRmRoleDefinition -Role $policyRoleDef  
 
### <a name="does-azure-devtest-labs-integrate-with-my-cicd-toolchain"></a>Azure DevTest esercitazioni si integra con le toolchain CI/CD? 
Se si utilizza VSTS, esiste un' [estensione Azure DevTest esercitazioni attività](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) che consente di automatizzare la pipeline di rilascio in Azure DevTest esercitazioni. Alcuni degli scopi di questa estensione:

- Creazione e la distribuzione automatica di una macchina virtuale e la configurazione con l'ultima build con Azure copia del File o PowerShell VSTS attività. 
- Acquisizione automaticamente lo stato di una macchina virtuale al termine del test per riprodurre un bug nella stessa macchina virtuale per un'analisi più approfondita. 
- Eliminare la macchina virtuale alla fine della pipeline di rilascio quando non è più necessario. 

Post di blog seguenti fornire istruzioni e informazioni sull'utilizzo dell'estensione VSTS:
 
- [Esercitazioni di Azure DevTest-estensione VSTS](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/) 
- [Distribuzione di una nuova macchina virtuale in un AzureDevTestLab esistente da VSTS](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) 
- [Utilizzo di Visual Studio Release Management per distribuzioni continue a AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) 
 
Per altre toolchains CI/CD, è possono ottenere allo stesso modo tutti gli scenari indicati in precedenza che è possono ottenere tramite l'estensione di attività VSTS tramite la distribuzione di [modelli di gestione risorse di Azure](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) uso [dei cmdlet di Azure PowerShell](../resource-group-template-deploy.md) e [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Utilizzare [Le API REST per le esercitazioni DevTest](http://aka.ms/dtlrestapis) per l'integrazione con i toolchain.  

### <a name="why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-azure-devtest-labs"></a>Perché non vengono visualizzate determinate macchine virtuali in e il macchine virtuali di Azure visualizzata all'interno di Azure DevTest esercitazioni?
Creazione di una macchina virtuale in Azure DevTest esercitazioni, autorizzazione per accedere a quella macchina virtuale. In grado di visualizzarlo in e l'esercitazioni ed e il **macchine virtuali** . Gli utenti del ruolo DevTest esercitazioni è possono visualizzare tutte le macchine virtuali create durante l'esercitazione tramite blade **tutte le macchine virtuali** dell'ambiente di test. Tuttavia, gli utenti del ruolo DevTest esercitazioni non vengono automaticamente concesse accesso in lettura alle risorse di macchine Virtuali creati da altri. Pertanto, tali macchine virtuali non vengono visualizzate in e il **macchine virtuali** . 

### <a name="what-is-the-difference-between-custom-images-and-formulas"></a>Che cos'è la differenza tra immagini personalizzate e le formule? 
Un'immagine personalizzata è un disco rigido virtuale (disco rigido virtuale), mentre la formula viene un'immagine che è possibile configurare le altre impostazioni che è possibile salvare e riprodurre. Un'immagine personalizzata può essere preferibile se si desidera creare rapidamente ambienti diversi con la stessa immagine di base e non modificabile. Una formula può essere meglio se si desidera riprodurre la configurazione della macchina virtuale con i bit più recente, una rete/subnet virtuale o dimensioni specifiche. Per informazioni più dettagliate, vedere l'articolo, [immagini personalizzate di confronto e le formule in DevTest esercitazioni](devtest-lab-comparing-vm-base-image-types.md). 
 
### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Come creare più macchine virtuali dallo stesso modello contemporaneamente? 
È possibile utilizzare [l'estensione attività VSTS](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) o [generare un modello di gestione risorse di Azure](devtest-lab-add-vm-with-artifacts.md#save-arm-template) durante la creazione di una macchina virtuale e [distribuire il modello di gestione risorse di Azure da Windows PowerShell](../resource-group-template-deploy.md). 
 
### <a name="how-do-i-move-my-existing-azure-vms-into-my-azure-devtest-labs-lab"></a>Come spostare le macchine virtuali Azure esistente nel mio laboratorio Azure DevTest esercitazioni? 
È in corso Progettazione di una soluzione per passare direttamente macchine virtuali di Azure DevTest esercitazioni ma attualmente è possibile copiare le macchine virtuali esistenti in Azure DevTest esercitazioni come indicato di seguito: 

1. Copiare il file disco rigido virtuale con le macchine Virtuali esistenti utilizzando questo [script di Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1) 
1. [Creare l'immagine personalizzata](devtest-lab-create-template.md) all'interno del laboratorio di Azure DevTest esercitazioni. 
1. Creare una macchina virtuale in laboratorio dalla immagine personalizzata 
 
### <a name="can-i-attach-multiple-disks-to-my-vms"></a>È possibile associare più dischi personale macchine virtuali? 
Associare più dischi in macchine virtuali è supportato.  
 
### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Come è possibile automatizzare il processo di caricamento di file disco rigido virtuale per creare immagini personalizzate? 
Sono disponibili due opzioni:

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) utilizzabile per copiare o caricare file disco rigido virtuale all'account di archiviazione associato laboratorio.
- [Esplora archivi di Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'applicazione autonoma che esegue Windows, OSX e Linux.   
 
Per trovare l'account di spazio di archiviazione di destinazione associato con l'ambiente, procedere come segue:

1. Accedere al [portale di Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). 
1. Selezionare **I gruppi di risorse** riquadro a sinistra. 
1. Individuare e selezionare il gruppo di risorse associato ambiente. 
1. Scegliere uno degli account di archiviazione e il **Panoramica** . 
1. Selezionare **BLOB**.
1. Cercare i caricamenti nell'elenco. Se non è presente, tornare al passaggio 4 e provare un altro account di archiviazione.
1. Utilizzare l' **URL** come destinazione nel comando AzCopy.


### <a name="how-can-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Come è possibile automatizzare il processo di eliminazione tutte le macchine virtuali nel mio laboratorio?

Oltre a eliminare macchine virtuali dall'ambiente nel portale di Azure, è possibile eliminare tutte le macchine nell'ambiente utilizzando uno script di PowerShell. Nell'esempio seguente, è sufficiente modificare i valori dei parametri sotto il commento di **valori da modificare** . È possibile recuperare il `subscriptionId`, `labResourceGroup`, e `labName` valori e il laboratorio nel portale di Azure. 


    # Delete all the VMs in a lab
    
    # Values to change
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Login to your Azure account
    Login-AzureRmAccount
    
    # Select the Azure subscription that contains the lab. This step is optional
    # if you have only one subscription.
    Select-AzureRmSubscription -SubscriptionId $subscriptionId
    
    # Get the lab that contains the VMs to delete.
    $lab = Get-AzureRmResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    
    # Get the VMs from that lab.
    $labVMs = Get-AzureRmResource | Where-Object { 
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.ResourceName -like "$($lab.ResourceName)/*"}
    
    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzureRmResource -ResourceId $labVM.ResourceId -Force
    }




### <a name="what-are-artifacts"></a>Quali sono gli elementi? 
Gli elementi sono elementi personalizzabili che possono essere utilizzati per distribuire i bit più recente o strumenti di sviluppo in una macchina virtuale. Sono collegate alle macchine Virtuali durante la creazione con pochi semplici e quando viene eseguito il provisioning di macchine Virtuali, gli elementi di distribuire e configurare le macchine Virtuali. Esistono vari elementi già esistenti nel nostro [archivio Github pubblico](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), ma è possibile anche [creare propri elementi](devtest-lab-artifact-author.md). 

### <a name="how-do-i-create-a-lab-from-an-azure-resource-manager-template"></a>Creazione di un ambiente di lavoro da un modello di gestione risorse di Azure 
Sono disponibili un [repository Github dei modelli di gestione risorse di Azure laboratorio](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) che è possibile distribuire come- o modificarle per creare modelli personalizzati per le esercitazioni. Ognuno di questi modelli è disponibile un collegamento che è possibile fare clic per distribuire il laboratorio come-in abbonamento Azure, oppure è possibile personalizzare il modello e [distribuire tramite PowerShell o CLI Azure](../resource-group-template-deploy.md).
 
### <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>Perché le macchine virtuali create in gruppi di risorse diversi con i nomi non autorizzati? È possibile rinominare o modificare i gruppi di risorse? 
Gruppi di risorse vengono creati in questo modo affinché Azure DevTest esercitazioni gestire le autorizzazioni utente e l'accesso a macchine virtuali. Mentre è possibile spostare la macchina virtuale a un altro gruppo di risorse con il nome desiderato, eseguire in modo non è consigliabile. Stiamo lavorando a migliorare l'esperienza per consentire una maggiore flessibilità.   
 
### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Esercitazioni quanti è possibile creare nello stesso abbonamento? 
Non esiste alcun limite specifico per il numero di esercitazioni che possono essere create per abbonamento. Tuttavia, le risorse utilizzate sono limitate per abbonamento. Per ulteriori informazioni i [limiti e le quote imposti nelle sottoscrizioni Azure](../azure-subscription-service-limits.md) e [migliorare questi limiti](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests). 
 
### <a name="how-many-vms-can-i-create-per-lab"></a>Quanti macchine virtuali è possibile creare per laboratorio? 
Non esiste alcun limite specifico per il numero di macchine virtuali che possono essere create per laboratorio. Tuttavia, attualmente laboratorio supporta solo circa 40 macchine virtuali in esecuzione nello stesso momento nello spazio di memorizzazione standard e 25 macchine virtuali contemporaneamente in esecuzione in archiviazione premium. Attualmente stiamo lavorando a crescenti questi limiti. 

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Come è possibile condividere un collegamento diretto al mio laboratorio?

Per condividere un collegamento diretto agli utenti di laboratorio è possibile eseguire la procedura seguente.

1. Passare alla laboratorio nel portale di Azure.
2. Copiare l'URL di laboratorio nel browser e condividerlo con altri utenti di laboratorio. 

>[AZURE.NOTE] Se gli utenti di laboratorio sono gli utenti esterni con un [account MSA](#what-is-a-microsoft-account) e non appartengono ad Active directory della società, si verifichi un errore durante l'esplorazione per il collegamento specificato. Se si riceve un messaggio di errore, gli utenti devono fare clic sul nome nell'angolo superiore sinistro del portale di Azure e selezionare la directory in cui è presente il laboratorio dalla sezione **Directory** del menu.

### <a name="what-is-a-microsoft-account"></a>Che cos'è un account Microsoft?

È un account Microsoft usato per quasi tutte le operazioni eseguite con i dispositivi di Microsoft e i servizi. È un indirizzo di posta elettronica e la password utilizzati per accedere a Skype, Outlook.com, OneDrive, Windows Phone e Xbox LIVE – e significa che il file, foto, contatti e impostazioni possono eseguire si per qualsiasi dispositivo. 

>[AZURE.NOTE] Account Microsoft usato per essere chiamati "Windows Live ID".
 
### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Personale elemento non è riuscita durante la creazione di macchine Virtuali. Come risolvere essa? 
Per informazioni su come recuperare i registri riguardanti l'elemento non riuscito, consultare il post di blog [come risolvere i problemi non funziona elementi AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) - scritto da uno dei nostro MVP. 
 
### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Perché non è del proprio account virtuale di rete salvataggio correttamente?  
Una possibilità è che il nome di rete virtuale contiene periodi. In caso affermativo, provare a rimuovere i periodi o sostituirli con segni meno e quindi riprovare a salvare la rete virtuale.
