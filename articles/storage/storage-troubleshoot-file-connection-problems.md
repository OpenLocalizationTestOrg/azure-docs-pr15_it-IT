<properties
    pageTitle="Risoluzione dei problemi di spazio di archiviazione File Azure | Microsoft Azure"
    description="Risoluzione dei problemi di spazio di archiviazione File Azure"
    services="storage"
    documentationCenter=""
    authors="genlin"
    manager="felixwu"
    editor="na"
    tags="storage"/>

<tags
    ms.service="storage"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genli"/>

# <a name="troubleshooting-azure-file-storage-problems"></a>Risoluzione dei problemi di spazio di archiviazione File Azure

In questo articolo sono elencati problemi comuni che riguardano l'archiviazione dei File di Microsoft Azure quando ci si connette dai client di Windows e Linux. Fornisce anche le possibili cause e risoluzioni per questi problemi.

**Problemi generali (verificarsi nei client Windows e Linux)**

- [Errore quota quando si tenta di aprire un file](#quotaerror)

- [Prestazioni ridotte quando si accede spazio di archiviazione File Azure da Windows o da Linux](#slowboth)

**Problemi di client di Windows**

- [Prestazioni ridotte quando si accede spazio di archiviazione File Azure da Windows 8.1 o Windows Server 2012 R2](#windowsslow)

- [Errore 53 si tenta di installare una condivisione File di Azure](#error53)

- [Usare netto è stata eseguita correttamente non si vede il file Azure condividere collegate in Esplora risorse](#netuse)

- [Il mio account di archiviazione contiene "/" e la rete usare comando ha esito negativo](#slashfails)

- [Il servizio applicativo/non è possibile accedere unità file Azure collegata.](#accessfiledrive)

- [Ulteriori raccomandazioni per ottimizzare le prestazioni](#additional)

**Problemi di client Linux**

- [Errore "Si sta copiando un file a una destinazione che non supporta la crittografia" durante il caricamento/la copia di file ai file di Azure](#encryption)

- [Condivide "Host è verso il basso" errore nel file esistente o la shell si blocca quando si esegue elenco dei comandi in corrispondenza del punto di montaggio](#errorhold)

- [Errore di installazione 115 quando si tenta di file di Azure montaggio su VM Linux](#error15)

- [Linux VM un ritardo casuale in comandi come "ls"](#delayproblem)

## <a name="general-problems"></a>Problemi generali
<a id="quotaerror"></a>
### <a name="quota-error-when-trying-to-open-a-file"></a>Errore quota quando si tenta di aprire un file

In Windows, si ricevano messaggi di errore simile al seguente:

**1816 ERROR_NOT_ENOUGH_QUOTA <> – 0xc0000044**

**STATUS_QUOTA_EXCEEDED**

**Non è sufficiente eseguire il comando**

**Il valore di handle non valido GetLastError: 53**

Su Linux, si ricevono messaggi di errore simile al seguente:

**<filename>[autorizzazione negato]**

**Quota disco superata**

#### <a name="cause"></a>Causa

Il problema è dovuto al fatto che è stato raggiunto il limite massimo di handle aperti simultanei consentite per un file.

#### <a name="solution"></a>Soluzione

Ridurre il numero di punti di manipolazione di Apri simultanee chiudendo alcuni quadratini e riprovare. Per ulteriori informazioni, vedere [Microsoft Azure lo spazio di archiviazione prestazioni e scalabilità del controllo](storage-performance-checklist.md).

<a id="slowboth"></a>
### <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Lentezza nelle prestazioni durante l'accesso spazio di archiviazione File da Windows o Linux

- Se non si dispone di un requisito di dimensione minimo i/o specifico, è consigliabile utilizzare 1 MB come dimensione i/o per ottenere prestazioni ottimali.

- Se si conosce la dimensione finale di un file che si estende con scrittura e il software non presentano problemi di compatibilità quando tail non ancora scritti sul file contenente zeri iniziali, quindi impostare le dimensioni del file in anticipo anziché ogni scrittura da un'operazione di scrittura estensione.

## <a name="windows-client-problems"></a>Problemi di client di Windows
<a id="windowsslow"></a>
### <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Lentezza nelle prestazioni durante l'accesso archiviazione dei File da Windows 8.1 o Windows Server 2012 R2

Per i client che eseguono Windows 8.1 o Windows Server 2012 R2, assicurarsi che sia installato l'aggiornamento rapido [KB3114025](https://support.microsoft.com/kb/3114025) . Questa correzione migliora crea e chiudere le prestazioni di manipolazione.

È possibile eseguire il seguente script per verificare se è stato installato l'aggiornamento rapido su:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se è installato l'aggiornamento rapido, viene visualizzato il seguente output:

**HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies**

**{96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1**

> [AZURE.NOTE]  Immagini di Windows Server 2012 R2 in Azure Marketplace stata applicata la correzione KB3114025 installato per impostazione predefinita a partire da dicembre 2015.

<a id="additional"></a>
### <a name="additional-recommendations-to-optimize-performance"></a>Ulteriori raccomandazioni per ottimizzare le prestazioni

Non è mai creare o aprire un file per i/o memorizzati nella cache che richiede l'accesso in scrittura, ma non accesso in lettura. Vale a dire quando si chiama **CreateFile ()**, non è mai specificare solo **GENERIC_WRITE**, ma sempre specificare **GENERIC_READ | GENERIC_WRITE**. Un punto di sola lettura non scrive nella cache piccole in locale, anche quando il quadratino di aprire solo per il file. Scrittura small impone un gravi problemi di prestazioni. Si noti che la "una" modalità per CRT **fopen()** viene aperto un punto di sola lettura.

<a id="error53"></a>
### <a name="error-53-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>"Errore 53" quando si tenta di installare o disinstallare una condivisione File di Azure

Questo problema può essere dovuto a condizioni seguenti:

#### <a name="cause-1"></a>Causa 1

"Errore di sistema 53 si è verificato. Accesso negato." Per motivi di sicurezza, le connessioni nelle condivisioni file Azure vengono bloccate se non è crittografato il canale di comunicazione e il tentativo di connessione non viene effettuato da dello stesso data center in cui si trovano nelle condivisioni File Azure. Crittografia del canale di comunicazione non è disponibile se il client dell'utente del sistema operativo non supporta la crittografia di piccole e medie imprese. Questo è indicato da una "53 errore di sistema. Accesso negato"messaggio di errore quando si tenta di installare una condivisione file locali o da un centro di dati diversi. Windows 8, Windows Server 2012 e versioni successive di ogni richiesta negotiate che include 3.0 piccole e medie imprese, che supporta la crittografia.

#### <a name="solution-for-cause-1"></a>Soluzione per giusta causa 1

La connessione da un client che soddisfi i requisiti di Windows 8, Windows Server 2012 o versioni successive o che si connettono da una macchina virtuale che si trova in stesso data center dell'account di archiviazione Azure che viene utilizzato per la condivisione di File di Azure.

#### <a name="cause-2"></a>Causa 2

"Errore di sistema 53" durante l'installazione una condivisione file Azure può verificarsi se le comunicazioni in uscita porta 445 al file di Azure data center sono bloccata. Fare clic [qui](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx) per visualizzare il riepilogo degli ISP che consentire o impedire l'accesso da porta 445.

Comcast e alcune organizzazioni IT bloccano questa porta. Per capire se questo è il motivo dietro il messaggio "Errore di sistema 53", è possibile utilizzare Portqry per l'endpoint TCP:445 della query. Se l'endpoint TCP:445 viene visualizzata come filtrati, la porta TCP è bloccata. Ecco un esempio di query:

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

Se TCP 445 bloccato da un righello lungo il percorso di rete, verrà visualizzato il seguente output:

**Porte TCP 445 (servizio microsoft-ds): filtrati**

Per ulteriori informazioni sull'utilizzo di Portqry, vedere [Descrizione dell'utilità della riga di comando Portqry.exe](https://support.microsoft.com/kb/310099).

#### <a name="solution-for-cause-2"></a>Soluzione per la causa 2

Lavorare con l'organizzazione IT per aprire la porta 445 in uscita a [intervalli di indirizzi IP di Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="cause-3"></a>Causa 3

"Errore di sistema 53" può essere ricevuta anche se è attivata la comunicazione NTLMv1 sul client. Con NTLMv1 abilitata creato un client meno sicuro. Di conseguenza, la comunicazione verrà bloccata per i file di Azure. Per verificare se si tratta la causa dell'errore, verificare che la sottochiave del Registro di sistema seguenti sia impostata su un valore pari a 3:

HKLM\System\CurrentControlSet\Control\LSA. > LmCompatibilityLevel.

Per ulteriori informazioni, vedere l'argomento [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) su TechNet.

#### <a name="solution-for-cause-3"></a>Soluzione per la causa 3

Per risolvere il problema, ripristinare il valore di LmCompatibilityLevel nella chiave del Registro di sistema HKLM\System\CurrentControlSet\Control\LSA. il valore predefinito di 3.

File di Azure supporta solo l'autenticazione NTLMv2. Assicurarsi che i criteri di gruppo viene applicato ai client. Verrà impedire questo errore si verifichi. Questo è anche considerato una procedura consigliata. Per ulteriori informazioni, vedere [sulla configurazione di client per l'uso di NTLMv2 tramite criteri di gruppo](https://technet.microsoft.com/library/jj852207(v=ws.11).aspx)

L'impostazione del criterio consigliato è **Invia solo risposta NTLMv2**. Corrisponde a un valore del Registro di sistema di 3. Client di utilizzano solo l'autenticazione NTLMv2 e utilizzano la protezione sessione NTLMv2 se supportata dal server. Controller di dominio accettano LM, NTLM e NTLMv2.

<a id="netuse"></a>
### <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Usare netto è stata eseguita correttamente ma il file non visualizzato Azure condividere collegate in Esplora risorse

#### <a name="cause"></a>Causa

Per impostazione predefinita, in Esplora risorse non viene eseguito come amministratore. Se si esegue **utilizzo netto** da un prompt dei comandi di amministratore, si esegue il mapping di unità di rete "Come amministratore." Poiché mappate siano incentrate sui utente, l'account utente che ha eseguito l'accesso in non vengono visualizzate le unità se sono installati in un altro account utente.

#### <a name="solution"></a>Soluzione

Installare la condivisione di una riga di comando non amministratori. In alternativa, è possibile eseguire [in questo argomento TechNet](https://technet.microsoft.com/library/ee844140.aspx) per configurare il valore del Registro di sistema **EnableLinkedConnections** .

<a id="slashfails"></a>
### <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>Il mio account di archiviazione contiene "/" e la rete usare comando ha esito negativo

#### <a name="cause"></a>Causa

Quando viene eseguito il comando **net use** al prompt dei comandi (cmd.exe), viene analizzata mediante l'aggiunta di "/" come opzione della riga di comando. In questo modo il mapping di unità avere esito negativo.

#### <a name="solution"></a>Soluzione

Per risolvere il problema, è possibile utilizzare una delle operazioni seguenti:

• Usare il comando PowerShell seguente:

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

Da un file batch questa operazione può essere eseguita come

`Echo new-smbMapping ... | powershell -command –`

• Inserire virgolette doppie il tasto per risolvere questo problema, a meno che non "/" è il primo carattere. In caso affermativo, utilizzare la modalità interattiva e immettere la password separatamente o rigenerare le chiavi per ottenere una chiave che non inizino con il carattere barra (/).

<a id="accessfiledrive"></a>
### <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>Il servizio applicativo/non possono accedere unità file Azure collegata

#### <a name="cause"></a>Causa

Unità sono state installate per ogni utente. Se l'applicazione o il servizio è in esecuzione in un altro account utente, gli utenti non vedono l'unità.

#### <a name="solution"></a>Soluzione

Creare l'unità dallo stesso account utente in base al quale è l'applicazione. Ciò è possibile utilizzare strumenti, ad esempio psexec.

In alternativa, è possibile creare un nuovo utente con gli stessi privilegi dell'account di servizio o un sistema di rete e quindi eseguire **cmdkey** e **usare netto** con tale account. Il nome utente deve essere il nome dell'account di archiviazione e la password deve essere la chiave account di archiviazione. Un'altra opzione per **l'utilizzo netto** consiste nel passare il nome dell'account di archiviazione e la chiave nei parametri utente nome e la password del comando **net use** .

Dopo aver seguito le istruzioni, si che venga visualizzato il seguente messaggio di errore: "si è verificato un errore del sistema 1312. Una sessione di accesso specificato non esiste. Si potrebbe già stata terminata"quando si esegue **netto usare** per l'account di servizio di sistema/rete. In questo caso, assicurarsi che il nome utente di quello passato alla **utilizzo netto** includa informazioni sul dominio (ad esempio: "[nome dell'account di archiviazione]. file.core.windows .net").

## <a name="linux-client-problems"></a>Problemi di client Linux

<a id="encryption"></a>
### <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Errore "Si sta copiando un file a una destinazione che non supporta la crittografia"

#### <a name="cause"></a>Causa

File crittografati con BitLocker possono essere copiati ai file di Azure. Tuttavia, l'archiviazione dei File non supporta Crittografia file system NTFS. Di conseguenza, probabile che usano crittografia file System in questo caso. Se si dispone di file crittografati tramite crittografia file System, un'operazione di copia per l'archiviazione dei File potrebbe non riuscire a meno che non il comando copia è decrittografare un file copiato.

#### <a name="workaround"></a>Soluzione alternativa

Per copiare un file di archiviazione dei File, è necessario innanzitutto decrittografarlo. È possibile eseguire questa operazione utilizzando uno dei metodi seguenti:

• Utilizzare **/d copia**.

• Impostare la chiave del Registro di sistema seguente:

- Percorso = HKLM\Software\Policies\Microsoft\Windows\System
- Tipo di valore DWORD =
- Nome = CopyFileAllowDecryptedRemoteDestination
- Valore = 1

Tuttavia, si noti che l'impostazione della chiave del Registro di sistema viene applicata a tutte le operazioni di copia a condivisioni di rete.

<a id="errorhold"></a>
### <a name="host-is-down-error-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>Condivide "Host è verso il basso" errore nel file esistente o la shell si blocca quando si esegue l'elenco dei comandi in corrispondenza del punto di montaggio

#### <a name="cause"></a>Causa

Questo errore si verifica sul client Linux quando il client è rimasto inattivo per un periodo di tempo. Quando si verifica questo errore, il client si disconnette e timeout della connessione client.

#### <a name="solution"></a>Soluzione

Questo problema viene risolto ora nel kernel Linux come parte del [set di modifiche](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93), backport in sospeso in distribuzione Linux.

Per works questo problema, sostenere la connessione ed evitare la visualizzazione in uno stato di inattività, mantenimento di un file in condivisione di File di Azure in cui scrivere periodicamente. Questa operazione deve essere un'operazione di scrittura, ad esempio di riscrittura data creato o modificato il file. In caso contrario, che si ottengano risultati memorizzati nella cache e l'operazione non può attivare la connessione.

<a id="error15"></a>
### <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>"Errore 115 montaggio" quando si tenta di installare i file di Azure in VM Linux

#### <a name="cause"></a>Causa

Distribuzioni Linux ancora non supportano funzionalità di crittografia di piccole e medie imprese 3.0. In alcune distribuzioni utente che venga visualizzato un messaggio di errore "115" se tentano di Azure montaggio file mediante piccole e medie imprese 3.0 a causa di una caratteristica mancante.

#### <a name="solution"></a>Soluzione

Se il client Linux SMB utilizzato non supporta la crittografia, montaggio Azure file mediante 2.1 piccole e medie imprese da una VM Linux nell'interfaccia di dati stesso come account di archiviazione File.

<a id="delayproblem"></a>
### <a name="linux-vm-experiencing-random-delays-in-commands-like-ls"></a>Linux VM un ritardo casuale in comandi come "ls"

#### <a name="cause"></a>Causa

Questo può verificarsi quando il comando non include l'opzione **serverino** . Senza **serverino**, il comando ls viene eseguito un **stat** in tutti i file.

#### <a name="solution"></a>Soluzione

Controllo **serverino** la voce "o altri oggetti/fstab":

azureuser.file.Core.Windows.NET/WMS/comer in/home/sampledir tipo cifs (rw, nodev, relatime, vers = 2.1, sec = ntlmssp, cache = username strict, = xxx, dominio = X, file_mode = 0755, dir_mode = 0755, serverino, rsize = 65536, wsize = 65536, actimeo = 1)

Se l'opzione **serverino** non è presenta, disinstallare e installare di nuovo file di Azure facendo in modo che l'opzione **serverino** selezionata.

## <a name="learn-more"></a>Ulteriori informazioni

- [Guida introduttiva a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md)

- [Guida introduttiva a spazio di archiviazione di File di Azure su Linux](storage-how-to-use-files-linux.md)
