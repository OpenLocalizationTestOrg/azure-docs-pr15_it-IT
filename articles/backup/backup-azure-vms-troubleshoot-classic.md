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

## <a name="discovery"></a>Individuazione

| Operazione di backup | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Individuazione | Impossibile trovare nuovi elementi - Microsoft Azure Backup rilevato ed errore interno. Attendere alcuni minuti e quindi ripetere l'operazione. | Ripetere il processo di rilevamento dopo 15 minuti.
| Individuazione | Operazione di un'altra ricerca è Impossibile alla scoperta di nuovi elementi – è già in corso. Attendere fino al completamento dell'operazione di individuazione corrente. | Nessuno |

## <a name="register"></a>Eseguire la registrazione
| Operazione di backup | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Eseguire la registrazione | Numero di dischi dati collegati alla macchina virtuale superato il limite supportato -, scollegare alcuni dischi dati in questa macchina virtuale e ripetere l'operazione. Copia di backup Azure supporta fino a 16 dischi dati collegati a una macchina virtuale Azure per il backup | Nessuno |
| Eseguire la registrazione | Copia di Backup di Microsoft Azure errore interno - attendere alcuni minuti e riprovare l'operazione. Se il problema persiste, contattare il supporto Microsoft. | È possibile ottenere questo messaggio di errore a causa di uno della seguente configurazione non supportata di macchine Virtuali in LRS Premium. <br> Spazio di archiviazione Premium macchine virtuali possibile backup utilizzando archivio di servizi di recupero. [Ulteriori informazioni](backup-introduction-to-azure-backup.md/#back-up-and-restore-premium-storage-vms) |
| Eseguire la registrazione | Registrazione non è riuscita a installare agente operazione timeout | Verificare se la versione del sistema operativo del computer virtuale è supportata. |
| Eseguire la registrazione | Comando esecuzione non riuscita - un'altra operazione è in corso tale elemento. Attendere il completamento dell'operazione precedente | Nessuno |
| Eseguire la registrazione | Macchine virtuali con dischi rigidi virtuali archiviati in archiviazione Premium non sono supportate per il backup | Nessuno |
| Eseguire la registrazione | Agente di macchina virtuale non è presente nel computer virtuale - installare la richiesta prerequisito, agente macchine Virtuali e riavviare l'operazione. | [Per ulteriori](#vm-agent) sull'installazione agente macchine Virtuali e come convalidare l'installazione dell'agente macchine Virtuali. |

## <a name="backup"></a>Copia di backup

| Operazione di backup | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Copia di backup | Impossibile comunicare con l'agente di macchine Virtuali relativi allo stato di snapshot. Sottoattività macchine Virtuali snapshot timeout. -Vedere la Guida alla risoluzione dei problemi su come risolvere il problema. | Se si è verificato un problema con l'agente di macchine Virtuali o di accesso per l'infrastruttura di Azure sono bloccati in modo, viene generato questo errore. Ulteriori informazioni su come [il debug dello snapshot di macchine Virtuali problemi](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Se l'agente di macchine Virtuali non causa problemi, quindi riavviare la macchina virtuale. In alcuni casi uno stato macchine Virtuali non corretto può causare problemi e riavviare la macchina virtuale Reimposta questo stato"non valido" |
| Copia di backup | Impossibile creare una copia di backup con un errore interno - ripetere l'operazione in pochi minuti. Se il problema persiste, contattare il supporto Microsoft | Verificare se si verifica un problema temporaneo in accesso all'archiviazione macchine Virtuali. Controllare [Lo stato di Azure](https://azure.microsoft.com/en-us/status/) per controllare se è presente un problema in corso relative al calcolo/archiviazione/network nell'area. Vedere è ridotto Riprova il problema di backup post. |
| Copia di backup | Impossibile eseguire l'operazione come macchine Virtuali non esiste più. | Non è possibile eseguire il backup come macchine Virtuali configurato per il backup sono stato eliminato. Interrompere l'esecuzione di backup ulteriormente passando alla visualizzazione di elementi protetta, selezionare l'elemento protetto e fare clic su Rimuovi protezione. È possibile mantenere i dati selezionando l'opzione di dati di mantenere la copia di Backup. È possibile riprendere la protezione in un secondo momento per macchina virtuale facendo clic su configurare la protezione dalla visualizzazione elementi registrato|
| Copia di backup | Non è riuscito a installare i servizi di recupero Azure estensione sull'elemento selezionato - agente macchine Virtuali è un prerequisito per l'estensione di servizi di recupero di Azure. Installare l'agente di macchine Virtuali di Azure e riavviare l'operazione di registrazione | <ol> <li>Verificare se l'agente di macchine Virtuali è stato installato correttamente. <li>Assicurarsi che il contrassegno sulla configurazione macchine Virtuali sia impostato correttamente.</ol> [Per ulteriori](#validating-vm-agent-installation) sull'installazione agente macchine Virtuali e come convalidare l'installazione dell'agente macchine Virtuali. |
| Copia di backup | Comando esecuzione non riuscita - un'altra operazione è in corso tale elemento. Attendere il completamento dell'operazione precedente e quindi riprovare. | Un backup esistente o un processo di ripristino per la macchina virtuale è in esecuzione e non è possibile avviare un nuovo processo durante l'esecuzione di processo esistente. |
| Copia di backup | Estensione installazione non riuscita con l'errore "COM + non è riuscito a comunicare con il coordinatore delle transazioni Microsoft Distributed | Questo errore indica in genere che il servizio COM + non è in esecuzione. Per informazioni su come risolvere il problema, contattare il supporto Microsoft. |
| Copia di backup | Snapshot non riuscita con l'errore di operazione VSS "unità è bloccata da crittografia unità BitLocker. È necessario sbloccare l'unità dal Pannello di controllo. | Disattivare BitLocker per tutte le unità nella macchina virtuale e osservare se è stato risolto il problema VSS |
| Copia di backup | Macchine virtuali con dischi rigidi virtuali archiviati in archiviazione Premium non sono supportate per il backup | Nessuno |
| Copia di backup | Azure macchina virtuale non trovato. | Si verifica quando viene eliminata la macchina virtuale primaria ma il criterio backup continua cercare una macchina virtuale per eseguire il backup. Per correggere questo errore: <ol><li>Ricreare la macchina virtuale con lo stesso nome e il nome del gruppo risorse stesso [nome] servizio cloud, <br>(OR) <li> Disabilitare la protezione per questa macchina virtuale in modo che backup successivo non viene generato. </ol> |
| Copia di backup | Agente di macchina virtuale non è presente nel computer virtuale - installare la richiesta prerequisito, agente macchine Virtuali e riavviare l'operazione. | [Per ulteriori](#vm-agent) sull'installazione agente macchine Virtuali e come convalidare l'installazione dell'agente macchine Virtuali. |

## <a name="jobs"></a>Processi
| Operazione | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Annulla processo | Annullamento non è supportata per questo tipo di processo, attendere il completamento del processo. | Nessuno |
| Annulla processo | Il processo non è presente in uno stato annullabile - attendere il completamento del processo. <br>OR<br> Il processo selezionato non sia in uno stato annullabile - attendere il completamento del processo.| Probabilità, il completamento del processo quasi; Attendere il completamento del processo |
| Annulla processo | Non è possibile annullare il processo perché non è in corso: annullamento è disponibile solo per i processi che sono in corso. Tentativo di annullare in un in corso processo. | Si verifica questo evento a causa di uno stato transitorio. Attendere qualche minuto e riprovare a eseguire l'operazione di annullamento |
| Annulla processo | Non è possibile annullare il processo - attendere fino al termine del processo. | Nessuno |


## <a name="restore"></a>Ripristinare
| Operazione | Dettagli sull'errore | Soluzione alternativa |
| -------- | -------- | -------|
| Ripristinare | Ripristino non riuscito con errore interno Cloud | <ol><li>Servizio cloud a cui si sta tentando di ripristinare è configurato con le impostazioni DNS. È possibile controllare <br>$deployment = "Nome" AzureDeployment get - nome-intervallo aperto "Produzione" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se c'è indirizzo configurato, significa che le impostazioni DNS siano configurate.<br> <li>Servizio cloud a cui si sta tentando di ripristinare è configurato con IP riservati e macchine virtuali esistenti nel servizio cloud sono in stato di interruzione.<br>È possibile controllare che un servizio cloud ha riservato IP tramite il seguente cmdlet di powershell:<br>$deployment = "nome" AzureDeployment get - nome-intervallo aperto "Produzione" $ Protezione esecuzione programmi. ReservedIPName <br><li>Si sta tentando di ripristinare una macchina virtuale con configurazioni di rete speciali seguenti nella stessa servizio cloud. <br>-Macchine virtuali in configurazione di bilanciamento carico (interno ed esterno)<br>-Macchine virtuali con più indirizzi IP riservato<br>-Macchine virtuali con più schede di rete<br>Selezionare un nuovo servizio cloud nell'interfaccia utente o per, vedere [ripristinare considerazioni](./backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) macchine virtuali con configurazioni di rete speciali</ol> |
| Ripristinare | Il nome DNS selezionato è già utilizzato: specificare un nome DNS diverso e riprovare. | Si riferisce il nome DNS per il nome del servizio cloud (in genere che terminano con. cloudapp.net). Questa operazione deve essere univoco. Se si verifica questo errore, è necessario scegliere un nome diverso macchine Virtuali durante il ripristino. <br><br> Questo errore viene visualizzato solo per gli utenti del portale di Azure. L'operazione di ripristino tramite PowerShell ha avuto esito positivo perché solo Ripristina dischi e non viene creata la macchina virtuale. L'errore verrà rivolti quando la macchina virtuale in modo esplicito viene creata dall'utente dopo il disco di ripristinare il funzionamento. |
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





