<properties 
    pageTitle="Guida dell'utente di agenti Linux | Microsoft Azure" 
    description="Informazioni su come installare e configurare agente Linux (waagent) per gestire l'interazione della macchina virtuale con Controller dell'infrastruttura di Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="szarkos" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/17/2016" 
    ms.author="szark"/>



# <a name="azure-linux-agent-user-guide"></a>Guida di Azure Linux agente utente

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="introduction"></a>Introduzione

Microsoft Azure Linux Agent (waagent) gestisce Linux e FreeBSD il provisioning e interazione macchine Virtuali con il Controller dell'infrastruttura di Azure. Fornisce le seguenti funzionalità per Linux e FreeBSD IaaS distribuzioni:

> [AZURE.NOTE] Agente di Azure Linux [file Leggimi](https://github.com/Azure/WALinuxAgent/blob/master/README.md) per informazioni dettagliate, vedere.

* **Il Provisioning di immagine**
  - Creazione di un account utente
  - Configurazione dei tipi di autenticazione SSH
  - Distribuzione di chiavi pubbliche SSH e coppie di parole chiave
  - Impostazione del nome host
  - Pubblicare il nome host DNS della piattaforma
  - Creazione di report SSH host chiave ID digitale per la piattaforma
  - Gestione delle risorse disco
  - Formattazione e caricare il disco di risorse
  - Configurazione dello spazio di scambio

* **Rete**
  - Gestisce indirizza per migliorare la compatibilità con server DHCP piattaforma
  - Assicura la stabilità del nome dell'interfaccia di rete

* **Kernel**
  - Configura NUMA virtuale (disabilitare per kernel < 2.6.37)
  - Utilizza entropia Hyper-V per /dev/random
  - Configura il timeout SCSI per il dispositivo principale (che può essere remoto)

* **Diagnostica**
  - Reindirizzamento della console alla porta seriale

* **Distribuzioni SCVMM**
    - Consente di rilevare e avvia automaticamente l'agente VMM per Linux durante l'esecuzione in un ambiente System Center Virtual Machine Manager 2012 R2

* **Estensione macchine Virtuali**
    - Inserire componente creato da Microsoft e i partner in Linux VM (IaaS) per abilitare software e automazione di configurazione
    - Implementazione di riferimento estensione macchine Virtuali in [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)


## <a name="communication"></a>Comunicazioni
Si verifica il flusso di informazioni dalla piattaforma all'agente tramite i due canali:

* Un avvio associati DVD per distribuzioni IaaS. Il DVD include un file di configurazione OVF conforme che include tutte le informazioni di provisioning diversa da quella le chiavi SSH effettive.
* Un endpoint TCP esposizione un API REST utilizzata per ottenere la distribuzione e configurazione della topologia.


## <a name="requirements"></a>Requisiti
I seguenti sistemi sono stati testati e funzionano con l'agente di Linux Azure in:

> [AZURE.NOTE] In questo elenco può variare dall'elenco ufficiale dei sistemi supportati sulla piattaforma Microsoft Azure, come descritto di seguito: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6,7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Altri sistemi supportati:

* FreeBSD 10 + (agente di Linux Azure v2.0.10 +)

Agente di Linux varia in alcuni pacchetti di sistema per il corretto funzionamento:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Utilità di file System: sfdisk, fdisk, mkfs, separate
* Strumenti di password: chpasswd, sudo
* Strumenti di elaborazione del testo: sed, grep
* Strumenti di rete: route ip
* Kernel il supporto per montaggio quelli utente.

## <a name="installation"></a>Installazione
Installazione utilizzando un RPM o un pacchetto DEB dal repository pacchetto di distribuzione è il metodo di installazione e aggiornamento dell'agente di Linux Azure preferito. Il [approvato provider distribuzione](virtual-machines-linux-endorsed-distros.md) integrare il pacchetto dell'agente di Azure Linux le immagini e archivi.

Fare riferimento alla documentazione di [Azure Linux agente repo su Github](https://github.com/Azure/WALinuxAgent) per le opzioni di installazione avanzata, ad esempio l'installazione di origine o nelle posizioni personalizzate o prefissi.


## <a name="command-line-options"></a>Opzioni della riga di comando

### <a name="flags"></a>Contrassegni

- dettagliato: aumentare il livello di dettaglio del comando specificato
- forzare: Ignora conferma interattiva per alcuni comandi

### <a name="commands"></a>Comandi

- Guida: Elenca i comandi supportati e contrassegni.

- annullare l'implementazione: tenta di eliminare dal sistema e renderli più adatto per il provisioning di nuovo. Questa operazione per eliminare le operazioni seguenti:
 * Tutte le chiavi di host SSH (se Provisioning.RegenerateSshHostKeyPair è "y" nel file di configurazione)
 * Configurazione dei server dei nomi in /etc/resolv.conf
 * Password radice da /etc/shadow. (se Provisioning.DeleteRootPassword è "y" nel file di configurazione)
 * Leasing di client DHCP memorizzati nella cache
 * Nome host Reimposta a localhost.localdomain


> [AZURE.WARNING] Annullamento dell'implementazione non garantisce che l'immagine è deselezionata di tutte le informazioni riservate e adatto per la ridistribuzione.


- annullare l'implementazione + utente: consente di eseguire tutte le opzioni in - annullare l'implementazione (sopra) anche Elimina l'ultimo account utente provisioning (ottenuto dal /var/lib/waagent) e dati associati. Questo parametro è quando deselezionare il provisioning di un'immagine che era precedentemente il provisioning su Azure in modo che può essere acquisizione e riutilizzato.

- versione: consente di visualizzare la versione di waagent

- serialconsole: Configura GRUB per contrassegnare ttyS0 (la prima porta seriale) come console di avvio. In questo modo kernel avvio registri vengono inviati alla porta seriale che resi disponibili per il debug.

- daemon: eseguire waagent come daemon gestire l'interazione con la piattaforma. In questo argomento viene specificato per waagent dello script di inizializzazione waagent.

- avviare: eseguire waagent in background


## <a name="configuration"></a>Configurazione

Un file di configurazione (/ etc/waagent.conf) consente di controllare le azioni di waagent. Di seguito è riportato un esempio di file di configurazione:

    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None

Le opzioni di configurazione sono descritti in seguito. Opzioni di configurazione sono dei tipi di tre. Valore booleano, stringa o Integer. Le opzioni di configurazione booleana possono essere specificate come "y" o "n". La parola chiave speciale "Nessuno" possono essere utilizzati per alcune stringa tipo configurazione voci come descritto in dettaglio riportata di seguito.

**Provisioning.Enabled:**  
Tipo: booleano  
Predefinito: y

In questo modo all'utente di abilitare o disabilitare la funzionalità di provisioning nell'agente. Valori validi sono "y" o "n". Se il provisioning è disabilitato, le chiavi host e utente SSH nell'immagine vengono mantenute e qualsiasi configurazione specificata in Azure il provisioning di API verrà ignorata.

> [AZURE.NOTE] Il `Provisioning.Enabled` valore predefinito del parametro "n" per le immagini di Cloud Ubuntu che utilizzano cloud-inizializzazione per il provisioning.

  
**Provisioning.DeleteRootPassword:**  
Tipo: booleano  
Predefinito: n

Se viene cancellato set, la password radice nel file di ombreggiatura e così via/durante il processo di provisioning.


**Provisioning.RegenerateSshHostKeyPair:**  
Tipo: booleano  
Predefinito: y

Se durante il processo di provisioning da /etc/ssh/vengono eliminati insieme, tutti SSH host coppie di chiavi (ecdsa, dsa e rsa). E viene generata una sola coppia aggiornata.

Il tipo di crittografia coppia di chiavi aggiornato essere configurato per la voce Provisioning.SshHostKeyPairType. Si noti che alcune distribuzioni ricreerà SSH coppie di parole chiave per qualsiasi tipo di crittografia mancanti quando si riavvia il daemon SSH (ad esempio, dopo il riavvio).


**Provisioning.SshHostKeyPairType:**  
Tipo: stringa  
Predefinito: rsa

Impostare per un tipo di algoritmo di crittografia supportato da daemon SSH sul computer virtuale. I valori in genere supportati sono "rsa", "dsa" e "ecdsa". Si noti che "putty.exe" in Windows non supporta "ecdsa". Pertanto, se si prevede di utilizzare putty.exe in Windows per connettersi a una distribuzione Linux, utilizzare "rsa" o "dsa".


**Provisioning.MonitorHostName:**  
Tipo: booleano  
Predefinito: y

Impostazione, waagent verrà monitorare Linux virtual machine per modifiche hostname (come restituito dal comando "hostname") e aggiornare automaticamente la configurazione di rete per riflettere la modifica dell'immagine. Per inviare la modifica del nome ai server DNS, la macchina virtuale verrà riavviato networking. Verrà creato in breve perdita di connettività Internet.


**Provisioning.DecodeCustomData**  
Tipo: booleano  
Predefinito: n

Se impostata, waagent consente di decodificare CustomData da base 64.


**Provisioning.ExecuteCustomData**  
Tipo: booleano  
Predefinito: n

Se impostata, waagent eseguirà CustomData dopo il provisioning.


**Provisioning.PasswordCryptId**  
Stringa di tipo:  
Predefinito: 6

Algoritmo utilizzato dalla crittografia durante la generazione di hash della password.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  


**Provisioning.PasswordCryptSaltLength**  
Stringa di tipo:  
Predefinito: 10

Lunghezza del salt casuale utilizzato per la generazione hash della password.


**ResourceDisk.Format:**  
Tipo: booleano  
Predefinito: y

Se impostata, il disco di risorsa della piattaforma verrà formattato e installato da waagent se il tipo di file System richiesto dall'utente in "ResourceDisk.Filesystem" è un valore diverso da "ntfs". Una singola partizione di tipo Linux (83) sarà disponibile sul disco. Si noti che questa partizione non essere formattata se può essere installato correttamente.


**ResourceDisk.Filesystem:**  
Tipo: stringa  
Predefinito: ext4

Specifica il tipo di file System per il disco di risorsa. Valori supportati variano in base distribuzione Linux. Se la stringa X, quindi mkfs. X deve essere presente sull'immagine Linux. Immagini di 11 SLES devono essere utilizzate in genere 'ext3'. Immagini FreeBSD devono usare 'ufs2' qui.


**ResourceDisk.MountPoint:**  
Tipo: stringa  
Predefinita: risorsa/mnt / 

Consente di specificare il percorso in cui è installato il disco di risorsa. Si noti che il disco di risorse è un disco *temporaneo* e può essere svuotato quando la macchina virtuale viene rimosso con deprovisioning.


**ResourceDisk.MountOptions**  
Tipo: stringa  
Predefinito: nessuno

Specifica le opzioni di montaggio disco passare al comando -o montaggio. Si tratta di un elenco di valori, virgole. 'nodev, nosuid'. Vedere mount(8) per informazioni dettagliate.


**ResourceDisk.EnableSwap:**  
Tipo: booleano  
Predefinito: n

Se impostata, un file di scambio (/ scambio) sul disco risorsa viene aggiunta allo spazio di scambio di sistema.


**ResourceDisk.SwapSizeMB:**  
Tipo: Integer  
Predefinito: 0

Le dimensioni del file di scambio in megabyte.


**Logs.Verbose:**  
Tipo: booleano  
Predefinito: n

Se viene aumentata set, il livello di dettaglio di log. Waagent registrare /var/log/waagent.log e consente di sfruttare le funzionalità di logrotate di sistema per ruotare il log.


**SISTEMA OPERATIVO. EnableRDMA**  
Tipo: booleano  
Predefinito: n

Se è impostato, l'agente tenterà di installare e quindi caricare un driver kernel RDMA che corrisponde alla versione del firmware hardware sottostante.


**SISTEMA OPERATIVO. RootDeviceScsiTimeout:**  
Tipo: Integer  
Predefinito: 300

Consente di configurare il timeout SCSI in secondi nelle unità disco rigido e i dati del sistema operativo. Se non è impostata, il sistema vengono utilizzati i valori predefiniti.


**SISTEMA OPERATIVO. OpensslPath:**  
Tipo: stringa  
Predefinito: nessuno

Può essere utilizzato per specificare un percorso alternativo per openssl binario da utilizzare per le operazioni di crittografia.


**HttpProxy.Host, HttpProxy.Port**  
Tipo: stringa  
Predefinito: nessuno

Se impostata, l'agente utilizzerà il server proxy per accedere a internet. 


## <a name="ubuntu-cloud-images"></a>Immagini Ubuntu Cloud

Si noti che le immagini Cloud Ubuntu utilizzano [cloud-inizializzazione](https://launchpad.net/ubuntu/+source/cloud-init) per eseguire molte attività di configurazione, in caso contrario, viene gestita da agente di Linux Azure.  Si tengano presenti le differenze seguenti:


- **Provisioning.Enabled** predefinito è "n" per le immagini di Cloud Ubuntu che consente di eseguire operazioni di provisioning cloud-inizializzazione.

- Parametri di configurazione seguenti hanno effetto sulle immagini Cloud Ubuntu che utilizzano cloud-inizializzazione per gestire il disco di risorsa e scambiare spazio:

 - **ResourceDisk.Format**
 - **ResourceDisk.Filesystem**
 - **ResourceDisk.MountPoint**
 - **ResourceDisk.EnableSwap**
 - **ResourceDisk.SwapSizeMB**

- Vedere le risorse seguenti per configurare il punto di montaggio risorsa disco e scambio di spazio su Ubuntu Cloud immagini durante il provisioning di:

 - [Ubuntu Wiki: Configurare le partizioni scambiare](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
 - [Inserire dati personalizzati in una macchina virtuale Azure](virtual-machines-windows-classic-inject-custom-data.md)

