<properties
    pageTitle="Creare piani di ripristino | Microsoft Azure" 
    description="Creare piani di ripristino con il ripristino del sito di Azure esito negativo su e recuperare gruppi di macchine virtuali e server fisici." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Creare piani di ripristino

Il servizio il ripristino del sito di Azure contribuisce a business continuità e emergenza (BCDR) strategia di ripristino da coordinazione replica, failover e ripristino di macchine virtuali e server fisici. Computer possono essere replicati in Azure o a un data center locale secondario. Per una rapida panoramica leggere [che cos'è il ripristino del sito di Azure?](site-recovery-overview.md).


## <a name="overview"></a>Panoramica

Questo articolo fornisce informazioni sulla creazione e la personalizzazione di piani di ripristino. 

Piani di ripristino è costituito da uno o più gruppi ordinati che contengono macchine virtuali o server fisici che si vuole avere esito negativo su insieme. Piani di ripristino eseguire le operazioni seguenti:

- Definire gruppi di computer che esito negativo su e quindi si avvia insieme.
- Modello dipendenze tra macchine raggruppandole in un gruppo di piano di ripristino. Per esempio se si vuole avere esito negativo su e visualizzata un'applicazione specifica per raggruppare le macchine virtuali per tale applicazione nello stesso gruppo di piano di ripristino.
- Automatizzare ed estendere failover. È possibile eseguire un test pianificato, o failover non pianificato in un piano di ripristino. È possibile personalizzare i piani di ripristino con script, automazione Azure e azioni manuale.

Piani di ripristino vengono visualizzati nei **Piani di ripristino** nel portale del ripristino del sito.


Nella parte inferiore di questo articolo o nel [Forum di servizi di recupero di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr), inserire commenti o domande.

## <a name="before-you-start"></a>Prima di iniziare

Tenere presente quanto segue:

- Un piano di ripristino non deve combinare macchine virtuali con una o più schede. Ciò avviene perché non è consentito combinare queste macchine virtuali in un servizio cloud Azure.
- È possibile estendere piani di ripristino con script e azioni manuale. Tenere presente quanto segue:
    - Scrittura di script con Windows PowerShell.
    - Assicurarsi che gli script utilizzino catture di provare a blocchi in modo che le eccezioni sono gestite normalmente. Se si verifica un'eccezione dello script arresta l'esecuzione e l'attività Mostra come non riuscita.  Se si verifica un errore, non è possibile eseguire qualsiasi parte restante dello script. Se si verifica quando si esegue un failover non pianificato, il piano di ripristino continuerà. Se si verifica quando si esegue pianificato in caso di errore, verrà interrotto il piano di ripristino. In questo caso, correggere lo script, verificare che venga eseguito come previsto e quindi eseguire di nuovo il piano di ripristino.
    - Il comando di scrittura Host non funziona in uno script di piano di ripristino e lo script avrà esito negativo. Se si desidera creare l'output, creare uno script proxy che a sua volta esegue lo script principale e assicurarsi che tutto l'output viene reindirizzato utilizzando il >> comando.
    - Lo script timeout se non viene restituita entro 600 secondi.
    - Se qualsiasi elemento è scritto in STDERR, lo script sarà classificato come non riuscita. Queste informazioni verranno visualizzate i dettagli di esecuzione di script.
    - Se si usa VMM nella distribuzione si noti che:

        - Script di un piano di ripristino eseguite nel contesto dell'account del servizio VMM. Assicurarsi che l'account abbia le autorizzazioni di lettura sulla condivisione remota in cui si trova lo script e testare lo script per eseguire il livello di privilegi VMM account del servizio.
        - Cmdlet VMM vengono inviati in un modulo di Windows PowerShell. Quando si installa la console di VMM, è installato il modulo VMM Windows PowerShell. Il modulo VMM può essere caricato in uno script utilizzando il seguente comando dello script: modulo Importa-nome virtualmachinemanager. È possibile [ottenere ulteriori dettagli](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Assicurarsi di che avere almeno un server di raccolta nella distribuzione VMM. Per impostazione predefinita il percorso di condivisione di una raccolta per un server VMM è disponibile in locale nel server VMM con il nome della cartella MSCVMMLibrary.
        - Se il percorso di condivisione di una raccolta è remoto (o locale ma non condivise con MSCVMMLibrary, configurare l'opzione Condividi come indicato di seguito (tramite \\libserver2.contoso.com\share\ ad esempio):
            - Aprire l'Editor del Registro di sistema e passare a HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure sito Recovery\Registration.
            -  Modificare il valore ScriptLibraryPath e posizionare il valore come \\libserver2.contoso.com\share\. specificare il nome completo FQDN. Specificare le autorizzazioni di condivisione.
            -  Eseguire il test di script con un account utente che ha le stesse autorizzazioni come account del servizio VMM per garantire l'esecuzione script testati autonomo nello stesso modo che nei piani di ripristino. Sul server VMM, impostare il criterio di esecuzione per ignorare come indicato di seguito:
                -  Aprire la console di Windows PowerShell 64 bit con privilegi elevati.
                -  Type: **Set-executionpolicy ignorare**. È possibile [ottenere ulteriori dettagli](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Creare un piano di ripristino

Il modo in cui si crea un piano di ripristino varia nella distribuzione il ripristino del sito.

- **La replica macchine virtuali VMware e server fisici**, creare un piano e aggiungere gruppi di replica che contengono macchine virtuali e server fisici per un piano di ripristino.
- **La replica Hyper-V macchine virtuali (nel cloud VMM)**, creare un piano e aggiungere macchine virtuali di Hyper-V protette da un'area VMM a un piano di ripristino.
- **La replica macchine virtuali di Hyper-V (non nel cloud VMM)**, creare un piano e aggiungere macchine virtuali di Hyper-V protette da un gruppo di protezione a un piano di ripristino.
- **Replica di SAN**, creare un piano e aggiungere un gruppo di replica contenente macchine virtuali al piano di ripristino. Selezionare un gruppo di replica anziché specifiche macchine virtuali poiché tutte le macchine virtuali in un gruppo di replica deve avere esito negativo su insieme (failover si verifica il livello di spazio di archiviazione prima di tutto).


Creare un piano di ripristino come segue:

1. Fare clic su scheda **Ripristino plan di messaggistica unificata** > **Creazione del piano di ripristino**.
Specificare un nome per il piano di ripristino e un'origine e destinazione. Il server di origine deve avere macchine virtuali abilitati per failover e ripristino.

    - Se sta replica da VMM a VMM selezionare **Il tipo di origine** > **VMM**e il server VMM origine e destinazione. Fare clic su **Hyper-V** illustrano nuvole configurati per l'utilizzo di Replica Hyper-V. 
    - Se sta replicate da VMM in VMM tramite SAN seleziona il **Tipo di origine** > **VMM**e il server VMM origine e destinazione. Fare clic su **SAN** illustrano nuvole configurati per la replica di SAN.
    - Se si sta la replica da VMM in Azure selezionare **Il tipo di origine** > **VMM**.  Selezionare il server di origine VMM e **Azure** con quello di destinazione.
    - Se si sta la replica da un sito di Hyper-V selezionare **Il tipo di origine** > **sito Hyper-V**. Selezionare il sito come origine e **Azure **con quello di destinazione.
    - Se si sta replicare da VMware o un server locale fisica in Azure, selezionare un server di configurazione come origine e **Azure** con quello di destinazione

2. In **Selezionare macchine virtuali di** selezionare le macchine virtuali (o gruppo di replica) che si desidera aggiungere al gruppo predefinito (gruppo 1) il piano di ripristino.

## <a name="customize-recovery-plans"></a>Personalizzare i piani di ripristino

Dopo aver aggiunto protetta macchine virtuali o i gruppi di replica al gruppo predefinito ripristino piano e creato il piano che è possibile personalizzare:

- **Aggiungere nuovi gruppi**, è possibile aggiungere gruppi di piano di ripristino aggiuntive. Gruppi di che aggiunti sono numerati nell'ordine in cui vengono aggiunti. È possibile aggiungere fino a tre gruppi. È possibile aggiungere più computer o gruppi di replica a questi nuovi gruppi. Si noti che una macchina virtuale o un gruppo di replica può essere inclusi solo nel gruppo di ripristino di un piano.
- **Aggiungere uno script **, è possibile aggiungere gli script che, prima o dopo il ripristino, gruppo di pianificazione. Quando si aggiunge un script viene aggiunto un nuovo insieme di azioni per il gruppo. Ad esempio verrà creato un set di pre-procedura per la categoria 1 con il nome: gruppo 1: i passaggi precedenti. Tutti i passaggi precedenti saranno elencati in questa serie. Si noti che è possibile aggiungere solo uno script sul sito principale, se si dispone di un server VMM distribuito.
- **Aggiungere un'azione manuale**, è possibile aggiungere manualmente azioni da eseguire prima o dopo un gruppo di piano di ripristino. Quando viene eseguito il piano di ripristino, si interrompe in corrispondenza del punto in cui è stata inserita azione manuale e una finestra di dialogo chiede di specificare che l'azione manuale è stata completata.

## <a name="extend-recovery-plans-with-scripts"></a>Estendere piani di ripristino con gli script

È possibile aggiungere uno script al piano di ripristino:

- Se si dispone di un sito di origine VMM è possibile creare uno script sul server VMM e includere nel piano di ripristino.
- Se sta replicate in Azure è possibile integrare automazione Azure runbook piano di ripristino

### <a name="create-a-vmm-script"></a>Creare uno script VMM


Creare lo script come segue:

1. Creare una nuova cartella nella condivisione di una raccolta, ad esempio \<VMMServerName > \MSSCVMMLibrary\RPScripts. Inserirla nel sito di origine e destinazione server VMM.
2. Creare lo script (ad esempio RPScript) e verificare che il funzionamento previsto.
3. Inserire lo script nella posizione \<VMMServerName > \MSSCVMMLibrary nei server che VMM origine e destinazione.

### <a name="create-an-azure-automation-runbook"></a>Creare un runbook automazione Azure

È possibile estendere il piano di ripristino eseguendo una runbook automazione Azure come parte del piano. [Per ulteriori](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Aggiungere le impostazioni personalizzate per un piano di ripristino

1. Aprire il piano di ripristino che si desidera personalizzare.
2. Fare clic per aggiungere un macchine virtuali o un nuovo gruppo.
3. Per aggiungere uno script o manuale azione fare clic su qualsiasi elemento nell'elenco **passaggio** e quindi fare clic su **Script** o **Azione manuale**. Specificare se si desidera aggiungere lo script o azione prima o dopo l'elemento selezionato. Utilizzare i pulsanti di comando **Sposta su** e **Sposta giù** per spostare la posizione dello script verso l'alto o verso il basso.
4. Se si sta aggiungendo uno script VMM, selezionare **il controllo a script VMM**e nella **Casella Percorso Script** digitare il percorso relativo alla condivisione. Se, ad esempio la condivisione in cui si trova in \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, specificare il percorso: \RPScripts\RPScript.PS1.
5. Se si sta aggiungendo un automazione Azure esegue libro, specificare l' **Account di automazione Azure** in cui si trova il runbook e selezionare appropriato **Azure Runbook Script**.
5. Eseguire caso di errore del piano di ripristino per assicurarsi che lo script funzioni come previsto.


## <a name="next-steps"></a>Passaggi successivi

È possibile eseguire diversi tipi di failover nel piano di ripristino, tra cui caso di errore di prova per controllare l'ambiente e failover pianificato o non pianificato. [Altre informazioni](site-recovery-failover.md).


 
