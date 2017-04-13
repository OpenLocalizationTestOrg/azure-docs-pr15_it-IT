<properties
    pageTitle="Risolvere i problemi di Azure macchina virtuale backup | Microsoft Azure"
    description="Risolvere i problemi di backup e ripristino di Azure macchine virtuali"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="trinadhk;jimpark;"/>


# <a name="troubleshoot-azure-virtual-machine-backup"></a>Risolvere i problemi di backup di Azure macchina virtuale

> [AZURE.SELECTOR]
- [Archivio di servizi di recupero](backup-azure-vms-troubleshoot.md)
- [Archivio di backup](backup-azure-vms-troubleshoot-classic.md)

È possibile risolvere gli errori rilevati durante l'utilizzo del Backup Azure con le informazioni riportate nella tabella riportata di seguito.

## <a name="backup"></a>Copia di backup

| Operazione di backup | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
|Copia di backup|    Impossibile eseguire l'operazione come macchine Virtuali non esiste più. -Arresta la protezione macchina virtuale senza eliminare i dati di backup. Ulteriori informazioni su http://go.microsoft.com/fwlink/?LinkId=808124   |Si verifica quando viene eliminata la macchina virtuale primaria ma il criterio backup continua cercare una macchina virtuale per eseguire il backup. Per correggere questo errore: <ol><li> Ricreare la macchina virtuale con lo stesso nome e il nome del gruppo risorse stesso [nome] servizio cloud,<br>(OR)</li><li> Arrestare la protezione macchina virtuale con o senza eliminare i dati di backup. [Per ulteriori informazioni](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Copia di backup|Impossibile comunicare con l'agente di macchine Virtuali relativi allo stato di snapshot. -Assicurarsi che macchine Virtuali abbiano accesso a internet. Inoltre, aggiornare l'agente di macchine Virtuali citate nella Guida alla risoluzione dei problemi in http://go.microsoft.com/fwlink/?LinkId=800034 |Se si è verificato un problema con l'agente di macchine Virtuali o di accesso per l'infrastruttura di Azure sono bloccati in modo, viene generato questo errore. Informazioni su altre informazioni su debug macchina virtuale snapshot problemi.<br> Se l'agente di macchine Virtuali non causa problemi, quindi riavviare la macchina virtuale. In alcuni casi uno stato macchine Virtuali non corretto può causare problemi e riavviare la macchina virtuale Reimposta questo stato"non valido"|
|Copia di backup|    Ripristino servizi estensione non riuscita. -Assicurarsi che sia presente sul computer virtuale agente macchina virtuale più recenti e servizio agente è in esecuzione. Ripetere l'operazione di backup e in caso contrario, contattare il supporto tecnico Microsoft.|   Questo errore viene generato quando agente macchine Virtuali non è aggiornato. Sezione "Aggiornare l'agente di macchine Virtuali" riportata di seguito per aggiornare l'agente di macchine Virtuali, fare riferimento.|
|Copia di backup |Macchina virtuale non esiste. -, Assicurarsi che la macchina virtuale esistente o seleziona un altro computer virtuale. | Si verifica quando viene eliminata la macchina virtuale primaria ma il criterio backup continua cercare una macchina virtuale per eseguire il backup. Per correggere questo errore: <ol><li> Ricreare la macchina virtuale con lo stesso nome e il nome del gruppo risorse stesso [nome] servizio cloud,<br>(OR)<br></li><li>Arrestare la protezione macchina virtuale senza eliminare i dati di backup. [Per ulteriori informazioni](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol>|
|Copia di backup |Impossibile eseguire il comando. -Un'altra operazione è in corso tale elemento. Attendere il completamento dell'operazione precedente e quindi riprovare. |Un backup esistente o un processo di ripristino per la macchina virtuale è in esecuzione e non è possibile avviare un nuovo processo durante l'esecuzione di processo esistente.|
| Copia di backup | Copia di dischi rigidi virtuali dall'archivio backup timeout - ripetere l'operazione in pochi minuti. Se il problema persiste, contattare il supporto Microsoft. | Questo succede quando sono presenti troppi dati da copiare. Verificare la presenza di minore di 16 dischi di dati. |
| Copia di backup | Impossibile creare una copia di backup con un errore interno - ripetere l'operazione in pochi minuti. Se il problema persiste, contattare il supporto Microsoft | Questo errore può verificarsi per 2 motivi: <ol><li> In accesso all'archiviazione macchine Virtuali non esiste un problema temporaneo. Controllare [Lo stato di Azure](https://azure.microsoft.com/en-us/status/) per controllare se è presente un problema in corso relative al calcolo/archiviazione/network nell'area. Vedere è ridotto Riprova il problema di backup post. <li>La macchina virtuale originale è stata eliminata e pertanto non è possibile adottare backup. Per mantenere i dati di backup per una macchina virtuale eliminata senza interrompere gli errori di backup, rimuovere la protezione della macchina virtuale e scegliere l'opzione per mantenere i dati. Verrà interrotta la pianificazione di backup e anche i messaggi di errore ricorrente. |
| Copia di backup | Non è riuscito a installare i servizi di recupero Azure estensione sull'elemento selezionato - agente macchine Virtuali è un prerequisito per l'estensione di servizi di recupero di Azure. Installare l'agente di macchine Virtuali di Azure e riavviare l'operazione di registrazione | <ol> <li>Verificare se l'agente di macchine Virtuali è stato installato correttamente. <li>Assicurarsi che il contrassegno sulla configurazione macchine Virtuali sia impostato correttamente.</ol> [Per ulteriori](#validating-vm-agent-installation) sull'installazione agente macchine Virtuali e come convalidare l'installazione dell'agente macchine Virtuali. |
| Copia di backup | Estensione installazione non riuscita con l'errore "COM + non è riuscito a comunicare con il coordinatore delle transazioni Microsoft Distributed | Questo errore indica in genere che il servizio COM + non è in esecuzione. Per informazioni su come risolvere il problema, contattare il supporto Microsoft. |
| Copia di backup | Snapshot non riuscita con l'errore di operazione VSS "unità è bloccata da crittografia unità BitLocker. È necessario sbloccare l'unità dal Pannello di controllo. | Disattivare BitLocker per tutte le unità nella macchina virtuale e osservare se è stato risolto il problema VSS |
| Copia di backup | Macchine virtuali con dischi rigidi virtuali archiviati in archiviazione Premium non sono supportate per il backup | Nessuno |
| Copia di backup | Azure macchina virtuale non trovato. | Si verifica quando viene eliminata la macchina virtuale primaria ma il criterio backup continua cercare una macchina virtuale per eseguire il backup. Per correggere questo errore: <ol><li>Ricreare la macchina virtuale con lo stesso nome e il nome del gruppo risorse stesso [nome] servizio cloud, <br>(OR) <li> Disattivare la protezione per questa macchina virtuale in modo che i processi di backup non verrà creati </ol> |
| Copia di backup | Agente di macchina virtuale non è presente nel computer virtuale - installare la richiesta prerequisito, agente macchine Virtuali e riavviare l'operazione. | [Per ulteriori](#vm-agent) sull'installazione agente macchine Virtuali e come convalidare l'installazione dell'agente macchine Virtuali. |

## <a name="jobs"></a>Processi

| Operazione | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Annulla processo | Annullamento non è supportata per questo tipo di processo, attendere il completamento del processo. | Nessuno |
| Annulla processo | Il processo non è presente in uno stato annullabile - attendere il completamento del processo. <br>OR<br> Il processo selezionato non sia in uno stato annullabile - attendere il completamento del processo.| Ogni probabilità il completamento del processo quasi; Attendere il completamento del processo |
| Annulla processo | Non è possibile annullare il processo perché non è in corso: annullamento è disponibile solo per i processi che sono in corso. Tentativo di annullare in un in corso processo. | Si verifica questo evento a causa di uno stato transitorio. Attendere qualche minuto e riprovare a eseguire l'operazione di annullamento |
| Annulla processo | Non è possibile annullare il processo - attendere fino al termine del processo. | Nessuno |


## <a name="restore"></a>Ripristinare
| Operazione | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Ripristinare | Ripristino non riuscito con errore interno Cloud | <ol><li>Servizio cloud a cui si sta tentando di ripristinare è configurato con le impostazioni DNS. È possibile controllare <br>$deployment = "Nome" AzureDeployment get - nome-intervallo aperto "Produzione" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se c'è indirizzo configurato, significa che le impostazioni DNS siano configurate.<br> <li>Servizio cloud a cui si sta tentando di ripristinare è configurato con IP riservati e macchine virtuali esistenti nel servizio cloud sono in stato di interruzione.<br>È possibile controllare che un servizio cloud ha riservato IP tramite il seguente cmdlet di powershell:<br>$deployment = "nome" AzureDeployment get - nome-intervallo aperto "Produzione" $ Protezione esecuzione programmi. ReservedIPName <br><li>Si sta tentando di ripristinare una macchina virtuale con configurazioni di rete speciali seguenti nella stessa servizio cloud. <br>-Macchine virtuali in configurazione di bilanciamento carico (interno ed esterno)<br>-Macchine virtuali con più indirizzi IP riservato<br>-Macchine virtuali con più schede di rete<br>Selezionare un nuovo servizio cloud nell'interfaccia utente o per, vedere [ripristinare considerazioni](./backup-azure-arm-restore-vms.md/#restoring-vms-with-special-network-configurations) macchine virtuali con configurazioni di rete speciali</ol> |
| Ripristinare | Il nome DNS selezionato è già utilizzato: specificare un nome DNS diverso e riprovare. | Si riferisce il nome DNS per il nome del servizio cloud (in genere che terminano con. cloudapp.net). Questa operazione deve essere univoco. Se si verifica questo errore, è necessario scegliere un nome diverso macchine Virtuali durante il ripristino. <br><br> Si noti che questo errore viene visualizzato solo per gli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell riuscirà perché solo Ripristina dischi e non viene creata la macchina virtuale. L'errore verrà rivolti quando la macchina virtuale in modo esplicito viene creata dall'utente dopo il disco di ripristinare il funzionamento. |
| Ripristinare | La configurazione di rete virtuale specificato non è corretta - specificare una configurazione di rete virtuale diversa e riprovare. | Nessuno |
| Ripristinare | Il servizio cloud specificato utilizza un indirizzo IP riservato, che non corrispondono con la configurazione della macchina virtuale da ripristinare: specificare un servizio cloud diverso che non usa IP riservato o scegliere un altro punto di ripristino per il ripristino. | Nessuno |
| Ripristinare | Servizio cloud ha raggiunto limite numero di punti finali inpui: ripetere l'operazione specificando un servizio cloud diverso o utilizzando un endpoint esistente. | Nessuno |
| Ripristinare | Account di archiviazione di archivio e di destinazione backup sono in due aree geografiche diverse, assicurarsi che l'account di archiviazione specificato nell'operazione di ripristino sia nella stessa regione Azure come archivio di backup. | Nessuno |
| Ripristinare | Account di archiviazione specificato per l'operazione di ripristino non supportato - account di archiviazione solo Basic/Standard con localmente ridondanti o le impostazioni di replica ridondanti geografico sono supportate. Selezionare un account di archiviazione supportati | Nessuno |
| Ripristinare | Tipo di Account di archiviazione per l'operazione di ripristino specificato non è in linea: accertarsi che l'account di archiviazione specificato nell'operazione di ripristino è in linea | Questo problema può verificarsi a causa di un errore temporaneo in archiviazione Azure o a causa di un'interruzione. Scegliere un altro account di archiviazione. |
| Ripristinare | Quota di gruppo di risorse è stato raggiunto: eliminare alcuni gruppi di risorse dal portale Azure o contattare il supporto di Azure per aumentare i limiti. | Nessuno |
| Ripristinare | Subnet selezionata non esiste - seleziona una subnet esistente | Nessuno |


## <a name="policy"></a>Criteri
| Operazione | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Creare criteri | Non è possibile creare criteri - ridurre le opzioni di conservazione per continuare con la configurazione dei criteri. | Nessuno |


## <a name="vm-agent"></a>Agente di macchine Virtuali

### <a name="setting-up-the-vm-agent"></a>Configurare l'agente di macchine Virtuali
In genere, l'agente di macchine Virtuali è già presenta in macchine virtuali creato dalla raccolta di Azure. Tuttavia, macchine virtuali di cui eseguire la migrazione da Data Center locale avrebbe non è installato l'agente di macchine Virtuali. Per tali macchine virtuali, è necessario che l'agente di macchine Virtuali installati in modo esplicito. Altre informazioni [sull'installazione dell'agente di macchine Virtuali in una macchina virtuale esistente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Per macchine virtuali di Windows:

- Scaricare e installare l' [agente MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Sono necessari privilegi di amministratore per completare l'installazione.
- [Aggiornare la proprietà macchine Virtuali](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è installato.

Per le macchine virtuali Linux:

- Installare più recente [dell'agente Linux](https://github.com/Azure/WALinuxAgent) da github.
- [Aggiornare la proprietà macchine Virtuali](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) per indicare che l'agente è installato.


### <a name="updating-the-vm-agent"></a>Agente di macchine Virtuali di aggiornamento
Per macchine virtuali di Windows:

- Agente di macchine Virtuali l'aggiornamento è semplice come reinstallare i [file binari agente macchine Virtuali](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Tuttavia, è necessario assicurarsi che nessuna operazione di backup venga eseguito durante l'aggiornamento dell'agente di macchine Virtuali.

Per le macchine virtuali Linux:

- Seguire le istruzioni [sull'Aggiornamento agente macchine Virtuali Linux](../virtual-machines/virtual-machines-linux-update-agent.md).


### <a name="validating-vm-agent-installation"></a>Installazione dell'agente di macchine Virtuali di convalida
Come verificare la versione dell'agente di macchine Virtuali in macchine virtuali di Windows:

1. Accedere al computer virtuale Azure e passare alla cartella *C:\WindowsAzure\Packages*. È necessario trovare il file WaAppAgent.exe presenta.
2. Pulsante destro del mouse sul file, passare a **proprietà**e quindi selezionare la scheda **Dettagli** . Il campo versione del prodotto deve essere 2.6.1198.718 o versioni successive

## <a name="troubleshoot-vm-snapshot-issues"></a>Risoluzione dei problemi di Snapshot macchine Virtuali
Copia di backup macchine Virtuali si basa sul comando snapshot allo spazio di archiviazione sottostante. Non dispone di accesso per lo spazio di archiviazione o di ritardo in esecuzione dell'attività snapshot può avere esito negativo il backup. Di seguito può causare un errore di attività snapshot.

1. Accesso alla rete allo spazio di archiviazione è bloccato utilizzando NSG<br>
   Ulteriori informazioni su come [abilitare l'accesso alla rete](backup-azure-vms-prepare.md#2-network-connectivity) allo spazio di archiviazione utilizzando uno dei due whitelist di indirizzi IP o server proxy.
2.  Macchine virtuali con backup di Sql Server configurato possono causare ritardi attività snapshot <br>
    Per impostazione predefinita macchine Virtuali backup problemi VSS il backup completo in macchine virtuali di Windows. In macchine virtuali che eseguono Sql Server e Sql Server è configurato il backup, causando ritardi in esecuzione snapshot. Impostare le seguenti chiave del Registro di sistema se si verificano errori di backup a causa di problemi di snapshot.

    ```
    [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
    "USEVSSCOPYBACKUP"="TRUE"
    ```
3.  Stato macchine Virtuali segnalato in modo non corretto perché macchine Virtuali arresto in RDP.  <br>
    Se si dispone di arresta la macchina virtuale in RDP, controllare indietro nel portale che lo stato di macchine Virtuali applicato correttamente. In caso contrario, arrestare la macchina virtuale nel portale utilizzando l'opzione di 'Arresto' in macchine Virtuali dashboard.
4.  Se più di condivisione di quattro Virtual Machine servizio lo stesso cloud, configurare più criteri di backup per i tempi backup, pertanto non sono che più di quattro backup macchine Virtuali vengono avviate contemporaneamente sul passaggio. Provare a promuovere il backup avvio un'ora di differenza tra i criteri. 
5.  Macchine Virtuali sono in esecuzione in CPU/memoria alta.<br>
    Se la macchina virtuale è in esecuzione in CPU elevato usage(>90%) o memoria, snapshot attività è in coda, ritardo e verrà infine Ottiene scadute. Provare a backup su richiesta in questi casi.

<br>

## <a name="networking"></a>Rete
Ad esempio tutte le estensioni, estensione Backup necessario l'accesso a internet per l'uso. Non dispone di accesso a internet può essere accompagnato in diversi modi:

- L'installazione di estensione può avere esito negativo
- Possono avere esito negativo operazioni di backup (ad esempio snapshot su disco)
- Visualizzazione dello stato dell'operazione di backup può avere esito negativo

La necessità di risoluzione degli indirizzi internet pubblico è stata autosnodata [qui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). È necessario controllare le configurazioni DNS per il VNET e assicurarsi che possano essere risolti URI Azure.

Una volta completata la risoluzione dei nomi in modo corretto, l'accesso per gli indirizzi IP Azure anche deve essere fornito. Per sbloccare l'accesso a infrastruttura di Azure, eseguire una delle seguenti operazioni:

1. Proprietà consentite Data Center del Azure intervalli di indirizzi IP.
    - È possibile ottenere l'elenco di [Azure Data Center IP di](https://www.microsoft.com/download/details.aspx?id=41653) essere whitelisted.
    - Sbloccare gli indirizzi IP utilizzando il cmdlet [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) . Eseguire questo cmdlet in macchine Virtuali Azure in una finestra di PowerShell privilegi elevati (eseguita come amministratore).
    - Aggiungere regole per la NSG (se si dispone di un'organizzazione) per consentire l'accesso per gli indirizzi IP.
2. Creare un percorso per il traffico HTTP
    - Se si dispone di alcune restrizioni di rete in posizione (una rete gruppo di sicurezza, ad esempio) distribuire un server proxy HTTP per instradare il traffico. Procedura per distribuire un server HTTP Proxy disponibili [qui](backup-azure-vms-prepare.md#2-network-connectivity).
    - Aggiungere regole per la NSG (se si dispone di un'organizzazione) per consentire l'accesso a INTERNET del proxy HTTP.

>[AZURE.NOTE] DHCP deve essere abilitato all'interno del guest per il Backup di macchine Virtuali IaaS per l'uso.  Se è necessario un indirizzo IP privato statico, è necessario configurarlo mediante la piattaforma. L'opzione DHCP all'interno della macchina virtuale deve essere abilitata.
Visualizzare ulteriori informazioni [sull'impostazione di un indirizzo IP privato interno statico](../virtual-network/virtual-networks-reserved-private-ip.md).
