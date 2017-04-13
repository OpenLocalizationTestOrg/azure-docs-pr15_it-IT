
<properties 
    pageTitle="Come RemoteApp di Azure Salva dati utente e le impostazioni? | Microsoft Azure"
    description="Informazioni su come RemoteApp di Azure Salva dati utente utilizzando il disco di profili utente."
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

# <a name="how-does-azure-remoteapp-save-user-data-and-settings"></a>Come RemoteApp di Azure Salva dati utente e le impostazioni?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp Salva identità dell'utente e le personalizzazioni tra i dispositivi e le sessioni. Questo utente vengono memorizzati in un disco per ogni raccolta per utente, noto come un disco di profilo utente (UDP). Il disco segue l'utente e garantisce che l'utente ha un'esperienza uniforme, indipendentemente da dove eseguire l'accesso. Salva 

Dischi profilo utente vengono completamente trasparenti all'utente, gli utenti salvare documenti alla cartella documenti (in quello che appare come un'unità locale) e modificare le impostazioni di app come al solito. Nello stesso momento tutte le impostazioni personali mantenute quando ci si connette a RemoteApp Azure da qualsiasi dispositivo. L'utente potrà visualizzare è i dati nella stessa posizione.

Ogni UDP sono 50GB di spazio di archiviazione permanente e sono presenti entrambe le impostazioni utente di dati e applicazione. 

Continuare a leggere per informazioni dettagliate sui dati di profilo utente.

>[AZURE.NOTE] È necessario disattivare l'UDP? È possibile che a questo punto - estrazione post di blog del Pavithra, [Disattivare utente profilo dischi (UPDs) in Azure RemoteApp](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/), per informazioni dettagliate.


## <a name="how-can-an-admin-get-to-the-data"></a>Come un amministratore può accedere ai dati?

Se è necessario accedere ai dati per uno degli utenti (per il ripristino di emergenza o se l'utente lascia l'azienda), contattare il supporto di Azure e fornire le informazioni di sottoscrizione per la raccolta e l'identità dell'utente. Il team di Azure RemoteApp fornisce un URL nel disco rigido virtuale. Scaricare il disco rigido virtuale e recuperare tutti i documenti o file che necessari. Si noti che il disco rigido virtuale 50GB, per rendere un po' per scaricarlo.


## <a name="is-the-data-backed-up"></a>Sono i backup dei dati?

Sì, si salva una copia di backup dei dati utente per area geografica. I dati è di sola lettura e sono accessibili nello stesso modo come dati regolari (contatto Azure RemoteApp ottenerlo), se il centro dati principale è verso il basso. I dati vengono copiati in tempo reale per il percorso di backup e non è mantenere copie diverse versioni. E quindi sulla possibilità di danneggiamento dei dati è non sarà possibile ripristinarlo in una versione funzionante in precedenza nota ma se il centro dati principale è verso il basso, sarà possibile ottenere dati utente da altra posizione.

## <a name="how-do-users-see-the-upd-on-the-server-side"></a>Come gli utenti visualizzano l'UDP sul lato server?

Ogni utente avrà le proprie directory nel server che esegue il mapping a loro UDP: c:\Users\username.

## <a name="whats-the-best-way-to-use-outlook-and-upd"></a>Che cos'è il modo migliore per utilizzare Outlook e UDP?

RemoteApp Azure consente di salvare lo stato di Outlook (cassette postali, file pst) tra le sessioni. A tale scopo, occorre PST verrà archiviato nei dati di profilo utente (c:\users\<nomeutente >). Questo è il percorso predefinito per i dati, in modo finché non si modifica la posizione, i dati verranno mantenuti tra le sessioni.

È consigliabile utilizzare la modalità "memorizzati nella cache" in Outlook e la modalità "server/online" per la ricerca.

Consultare [questo articolo](remoteapp-outlook.md) per ulteriori informazioni sull'utilizzo di Outlook e RemoteApp di Azure.

## <a name="what-about-redirection"></a>E il reindirizzamento?
È possibile configurare RemoteApp di Azure per consentire agli utenti di accedere ai dispositivi locali installando e configurando [il reindirizzamento](remoteapp-redirection.md). Quindi saranno in grado di accedere ai dati di UDP dispositivi locali.

## <a name="can-i-use-my-upd-as-a-network-share"></a>È possibile utilizzare gli UDP come una condivisione di rete?
No. UPDs non possono essere utilizzati come una condivisione di rete. Un UDP è disponibile solo per l'utente quando l'utente è connesso attivamente a RemoteApp di Azure.

## <a name="if-i-delete-a-user-from-a-collection-is-their-upd-deleted"></a>Se Elimina un utente da una raccolta, viene eliminato loro UDP?

No, quando si elimina un utente, non è eliminare automaticamente le UDP - se, tuttavia, si archiviare i dati fino a quando non si elimina la raccolta. 90 giorni dopo l'eliminazione della raccolta verranno eliminati UPDs tutti. 

Se è necessario eliminare un UDP da una raccolta, rivolgersi RemoteApp di Azure, possiamo eliminare UDP dal nostro lato.

## <a name="can-i-access-my-users-upds-either-current-or-deleted-users"></a>È possibile accedere UPDs gli utenti (utenti correnti o eliminati)?

Sì, se si contatta [RemoteApp di Azure](mailto:remoteappforum@microsoft.com), è possibile impostare è un URL per accedere ai dati. Scaricare dati o file dall'UDP prima della scadenza l'accesso è necessario per circa 10 ore.

## <a name="are-upds-available-offline"></a>Sono disponibili offline UPDs?

Attualmente Microsoft non offre l'accesso offline a UPDs, oltre la finestra di access ora 10 descritta in precedenza. Questo significa che non attualmente è un modo per fornire l'accesso a lungo sufficiente per completare le attività più complesse, ad esempio il software antivirus nel UPDs o accesso ai dati per un controllo.

## <a name="do-registry-key-settings-persist"></a>Mantenute le impostazioni di chiave del Registro di sistema?
Sì, scrittura a HKEY_Current_User fa parte dell'UDP.

## <a name="can-i-disable-upds-for-a-collection"></a>È possibile disattivare UPDs per una raccolta?

Sì, è possibile chiedere RemoteApp di Azure disattivare UPDs per una sottoscrizione, ma non è possibile eseguire tale personalmente. Questo significa che UPDs verrà disabilitata per tutte le raccolte nella sottoscrizione.

È consigliabile disattivare UPDs in una delle situazioni seguenti: 

- È necessario completare l'accesso e controllo dei dati utente (per controllare ed esaminare scopi, ad esempio istituti).
- Si dispone di terze parti 3o utente Gestione soluzioni locale del profilo e si decide di continuare a utilizzare nella distribuzione di Azure RemoteApp dominio. Questo richiede l'agente di profilo essere caricato in immagine oro. 
- Non è necessario di spazio di archiviazione dati locali o si dispone di tutti i dati nella condivisione file o cloud e si desidera per controllare il salvataggio dei dati in locale utilizzando RemoteApp di Azure.

Per ulteriori informazioni, vedere [Disattivare utente profilo dischi (UPDs) RemoteApp di Azure](https://blogs.technet.microsoft.com/enterprisemobility/2015/11/11/disable-user-profile-disks-upds-in-azure-remoteapp/) .

## <a name="can-i-restrict-users-from-saving-data-to-the-system-drive"></a>È possibile impedire agli utenti di salvare i dati nell'unità di sistema?

Sì, ma è necessario configurare che nell'immagine del modello prima di creare la raccolta. Per bloccare l'accesso all'unità di sistema, procedere come segue:

1. Eseguire **gpedit** sull'immagine del modello.
2. Passare a **Configurazione utente > Preferenze > componenti di Windows > Esplora**.
3. Selezionare le opzioni seguenti:
    - **Nascondere le unità specificate in questo Computer**
    - **Impedire l'accesso alle unità dal Computer locale**

## <a name="can-i-seed-upds-i-want-to-put-some-data-in-the-upd-thats-available-the-first-time-the-user-signs-in"></a>È possibile inizializzare UPDs? Si desidera alcuni dati vengono inseriti in UDP che è disponibile l'utente accede per la prima volta.

Sì, quando si crea l'immagine di modello, è possibile aggiungere informazioni al profilo predefinito. Che informazioni vengono quindi aggiunte all'UDP.

## <a name="can-i-change-the-size-of-the-upd-depending-on-how-much-data-i-want-to-store"></a>È possibile modificare le dimensioni di UDP a seconda di come quantità di dati che si desidera archiviare?

No, tutti UPDs dispone di 50 GB di spazio di archiviazione. Se si desidera memorizzare diverse quantità di dati, provare a eseguire le operazioni seguenti:

1. Disabilitare UPDs per la raccolta.
2. Configurare una condivisione file agli utenti di accedere.
3. Caricare la condivisione di file tramite uno script di avvio. Per informazioni dettagliate sugli script di avvio in Azure RemoteApp, vedere di seguito.
4. Indicare agli utenti di salvare tutti i dati per la condivisione di file.


## <a name="how-do-i-run-a-startup-script-in-azure-remoteapp"></a>Come eseguire uno script di avvio in RemoteApp di Azure

Se si desidera eseguire uno script di avvio, iniziare mediante la creazione di un'attività programmata nell'immagine modello che si intende utilizzare per la raccolta. (Questa *prima* eseguirlo.) 

![Creare un'attività di sistema](./media/remoteapp-upd/upd1.png)

![Creare un'attività di sistema che viene eseguita quando un utente accede](./media/remoteapp-upd/upd2.png)

Nella scheda **Generale** , assicurarsi di cambiare l' **Account utente** in sicurezza a "BUILTIN\Users".

![Cambiare l'account utente a un gruppo](./media/remoteapp-upd/upd4.png)

Attività programmata verrà avviato lo script di avvio, utilizzando le credenziali dell'utente. Pianificare l'attività venga eseguita ogni volta un utente accede.

![Impostare i trigger per l'attività come "all'accesso"](./media/remoteapp-upd/upd3.png)

È anche possibile usare [gli script di avvio in base a criteri di gruppo](https://technet.microsoft.com/library/cc779329%28v=ws.10%29.aspx). 

## <a name="what-about-placing-a-startup-script-in-the-start-menu-would-that-work"></a>Informazioni sull'inserimento di uno script di avvio nel menu di avvio Sarebbe che funziona?

In altre parole, è possibile crea un file bat che esegue uno script finestra Configurazione e salvare la cartella c:\ProgramData\Microsoft\Windows\Start Avvio\Programmi\Esecuzione automatica e quindi hanno script eseguita ogni volta che si inizia una sessione di RemoteApp?

No, che non è supportata con RemoteApp di Azure, che utilizza RDSH, che non supportano anche gli script di avvio nel menu Start.

## <a name="can-i-use-mstscexe-the-remote-desktop-program-to-configure-logon-scripts"></a>È possibile utilizzare mstsc.exe (il programma Desktop remoto) per configurare gli script di accesso?

Risposta errata, non supportato da Azure RemoteApp.

## <a name="can-i-store-data-on-the-vm-locally"></a>È possibile archiviare dati nella macchina virtuale in locale?

NO, i dati archiviati in un punto qualsiasi nella macchina virtuale diversa da nell'UDP andranno persi. C'è molto probabile l'utente non sarà possibile ottenere le stesse macchine Virtuali successiva ora in cui sono accedere a RemoteApp di Azure. Abbiamo non mantengono persistenza macchine Virtuali di utente, in modo che l'utente non verrà accedere a macchina virtuale stessa e i dati andranno persi. Inoltre, quando si aggiorna la raccolta, macchine virtuali esistente vengono sostituite con un nuovo set di macchine virtuali - che indica che i dati archiviati in macchine Virtuali stesso verranno persi. Il suggerimento consiste nel memorizzare i dati in UDP, archiviazione condivisa come file di Azure, un file server all'interno di un VNET o nel cloud usando un sistema di archiviazione cloud come DropBox.

## <a name="how-do-i-mount-an-azure-file-share-on-a-vm-using-powershell"></a>Come deve essere montaggio una condivisione File Azure in una macchina virtuale tramite PowerShell?

È possibile utilizzare il cmdlet Net PSDrive per installare l'unità, come indicato di seguito:

    New-PSDrive -Name <drive-name> -PSProvider FileSystem -Root \\<storage-account-name>.file.core.windows.net\<share-name> -Credential :<storage-account-name>


È inoltre possibile salvare le credenziali eseguendo le operazioni seguenti:

    cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>


Che consente di ignorare - parametro Credential nel cmdlet New-PSDrive.
