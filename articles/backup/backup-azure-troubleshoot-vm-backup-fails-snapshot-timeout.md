<properties
   pageTitle="Copia di Backup macchine Virtuali Azure viene interrotta: Impossibile comunicare con l'agente di macchine Virtuali per stato snapshot - sottoattività macchine Virtuali Snapshot timeout | Microsoft Azure"
   description="I segnali più cause e soluzioni per gli errori di backup macchine Virtuali di Azure correlati a Impossibile comunicare con l'agente di macchine Virtuali relativi allo stato di snapshot. Sottoattività macchine Virtuali di snapshot errore di timeout"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="cfreeman"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jimpark; markgal;genli"/>

# <a name="azure-vm-backup-fails-could-not-communicate-with-the-vm-agent-for-snapshot-status---snapshot-vm-sub-task-timed-out"></a>Copia di Backup macchine Virtuali Azure viene interrotta: Impossibile comunicare con l'agente di macchine Virtuali per stato snapshot - sottoattività macchine Virtuali Snapshot timeout

## <a name="summary"></a>Riepilogo

Dopo avere effettuato la registrazione e la programmazione di un virtuale macchina per Azure Backup, il servizio di Azure Backup avvia il processo di backup pianificata per la comunicazione con l'estensione di backup in macchine Virtuali per creare uno snapshot nel momento. Sono disponibili le condizioni che potrebbero impedire il snapshot attivate che genera un errore di backup. In questo articolo vengono fornite procedure per i problemi relativi agli errori di backup macchine Virtuali di Azure relativi all'errore di timeout snapshot.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Sintomo

Copia di Backup di Microsoft Azure per infrastruttura come servizio (IaaS) macchine Virtuali non riesce e restituisce il seguente messaggio di errore nei dettagli dell'errore processo nel [portale di Azure](https://portal.azure.com/):

**Impossibile comunicare con l'agente di macchine Virtuali per stato snapshot - timeout sottoattività macchine Virtuali di Snapshot.**

## <a name="cause"></a>Causa
Esistono quattro cause comuni di errore simile al seguente:

- La macchina virtuale non ha accesso a Internet.
- Agente di macchine Virtuali di Microsoft Azure installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux).
- L'estensione di backup non riesce ad aggiornare o caricare.
- Non è possibile recuperare lo stato di istantanee o non è possibile snapshot.

## <a name="cause-1-the-vm-does-not-have-internet-access"></a>Causa 1: La macchina virtuale non ha accesso a Internet
Per il requisito di distribuzione, la macchina virtuale senza accesso a Internet o è associate le restrizioni sul posto che impediscono l'accesso all'infrastruttura di Azure.

L'estensione di backup richiede una connessione per gli indirizzi IP pubblici Azure a funzionare correttamente. In questo modo l'invio di comandi a un endpoint di archiviazione Azure (URL HTTP) per gestire snapshot della macchina virtuale. Se l'estensione non dispone dell'accesso a Internet, non viene individuata la copia di backup.

### <a name="solution"></a>Soluzione
In questo scenario, utilizzare uno dei metodi seguenti per risolvere il problema:

- Intervalli di indirizzi IP di proprietà consentite Data Center del Azure
- Creare un percorso per il traffico HTTP

### <a name="to-whitelist-the-azure-datacenter-ip-ranges"></a>La proprietà consentite di Azure degli intervalli IP Data Center

1. Ottenere l' [elenco di indirizzi IP del Data Center del Azure](https://www.microsoft.com/download/details.aspx?id=41653) per whitelisted.
2. Sbloccare gli indirizzi IP utilizzando il cmdlet New-NetRoute. Eseguire questo cmdlet in macchine Virtuali di Azure in una finestra di PowerShell privilegi elevati (eseguita come amministratore).
3. Aggiungere regole per il gruppo di sicurezza rete (NSG) se si dispone di uno per consentire l'accesso per gli indirizzi IP.

### <a name="to-create-a-path-for-http-traffic-to-flow"></a>Per creare un percorso per il traffico HTTP

1. Se si dispone di limitazioni di rete in posizione (ad esempio un NSG), distribuire un server proxy HTTP per indirizzare il traffico.
2. Se si dispone di un gruppo di sicurezza di rete (NSG), aggiungere regole per consentire l'accesso a Internet da proxy HTTP.

Informazioni su come [configurare un proxy HTTP per i backup macchine Virtuali](backup-azure-vms-prepare.md#using-an-http-proxy-for-vm-backups).

## <a name="cause-2-the-microsoft-azure-vm-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Motivo 2: L'agente di macchine Virtuali di Microsoft Azure installato nella macchina virtuale non è aggiornato (per macchine virtuali Linux)

### <a name="solution"></a>Soluzione
Errori più correlati agente o estensione per le macchine virtuali Linux sono causati da problemi che influiscono su un agente di macchine Virtuali precedente. In generale, i primi passi per risolvere questo problema sono i seguenti:

1. [Installare l'ultima agente macchine Virtuali di Azure](https://github.com/Azure/WALinuxAgent).
2. Assicurarsi che l'agente di Azure sia in esecuzione nella macchina virtuale. A tale scopo, eseguire il comando seguente:```ps -e```

    Se questo processo non è in esecuzione, utilizzare i comandi seguenti per riavviarla.

    Per Ubuntu:```service walinuxagent start```

    Per altre distribuzioni: ' ' service waagent start
```

3. [Configure the auto restart agent](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).

4. Run a new test backup. If the failures persist, please collect logs from the following folders for further debugging.

    We require the following logs from the customer’s VM:

    - /var/lib/waagent/*.xml
    - /var/log/waagent.log
    - /var/log/azure/*

If we require verbose logging for waagent, follow these steps to enable this:

1. In the /etc/waagent.conf file, locate the following line:

    Enable verbose logging (y|n)

2. Change the **Logs.Verbose** value from n to y.
3. Save the change, and then restart waagent by following the previous steps in this section.

## Cause 3: The backup extension fails to update or load
If extensions cannot be loaded, then Backup fails because a snapshot cannot be taken.

### Solution
For Windows guests:

1. Verify that the iaasvmprovider service is enabled and has a startup type of automatic.
2. If this is not the configuration, enable the service to determine whether the next backup succeeds.

For Linux guests:

The latest version of VMSnapshot Linux (extension used by backup) is 1.0.91.0

If the backup extension still fails to update or load, you can force the VMSnapshot extension to be reloaded by uninstalling the extension. The next backup attempt will reload the extension.

### To uninstall the extension

1. Go to the [Azure portal](https://portal.azure.com/).
2. Locate the particular VM that has backup problems.
3. Click **Settings**.
4. Click **Extensions**.
5. Click **Vmsnapshot Extension**.
6. Click **uninstall**.

This will cause the extension to be reinstalled during the next backup.

## Cause 4: The snapshots status cannot be retrieved or the snapshots cannot be taken
VM backup relies on issuing snapshot command to underlying storage. The backup can fail because it has no access to storage or because of a delay in snapshot task execution.

### Solution
The following conditions can cause snapshot task failure:

| Cause | Solution |
| ----- | ----- |
| VMs that have Microsoft SQL Server Backup configured. By default, VM Backup runs a VSS Full backup on Windows VMs. On VMs that are running SQL Server-based servers and on which SQL Server Backup is configured, snapshot execution delays may occur. | Set following registry key if you are experiencing backup failures because of snapshot issues.<br><br>[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT] "USEVSSCOPYBACKUP"="TRUE" |
| VM status is reported incorrectly because the VM is shut down in RDP. If you shut down the virtual machine in RDP, check the portal to determine whether that VM status is reflected correctly. | If it’s not, shut down the VM in the portal by using the ”Shutdown” option in the VM dashboard. |
| Many VMs from the same cloud service are configured to back up at the same time. | It’s a best practice to spread out the VMs from the same cloud service to have different backup schedules. |
| The VM is running at high CPU or memory usage. | If the VM is running at high CPU usage (more than 90 percent) or high memory usage, the snapshot task is queued and delayed and eventually times out. In this situation, try on-demand backup. |
|The VM cannot get host/fabric address from DHCP.|DHCP must be enabled inside the guest for IaaS VM Backup to work.  If the VM cannot get host/fabric address from DHCP response 245, then it cannot download ir run any extensions. If you need a static private IP, you should configure it through the platform. The DHCP option inside the VM should be left enabled. View more information about [Setting a Static Internal Private IP](../virtual-network/virtual-networks-reserved-private-ip.md).|
