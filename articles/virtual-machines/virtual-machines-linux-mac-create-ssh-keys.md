<properties
    pageTitle="Creare chiavi SSH su Linux e Mac | Microsoft Azure"
    description="Generare e utilizzare i tasti SSH su Linux e Mac per la gestione risorse e modelli di distribuzione classica in Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/25/2016"
    ms.author="v-livech"/>

# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Creare le chiavi SSH su Linux e Mac per macchine virtuali Linux in Azure

Con una coppia di chiavi SSH è possibile creare macchine virtuali in Azure utilizzino SSH tasti per l'autenticazione, per impostazione predefinita la necessità di password agli utenti di accedere.  Le password possono essere individuate e aprire nelle macchine virtuali fino a tentativi di forzare implacabili per individuare le password. Macchine virtuali create con i modelli di Azure o `azure-cli` possono includere la chiave pubblica SSH come parte della distribuzione, la rimozione di una configurazione di distribuzione post.  Se ci si connette a una VM Linux da Windows, vedere [questo documento.](virtual-machines-linux-ssh-from-windows.md)

Richiede l'articolo:

- un account Azure ([ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)).

- [Azure CLI](../xplat-cli-install.md) ha eseguito l'accesso con`azure login`

- la modalità di gestione di risorse Azure _deve essere_ CLI Azure`azure config mode arm`

## <a name="quick-commands"></a>Comandi rapidi

In comandi seguenti sostituire gli esempi con il proprio scelte.

SSH tasti possono essere utilizzati per impostazione predefinita di permanenza `.ssh` directory.  

```bash
cd ~/.ssh/
```

Se non si ha un `~/.ssh` directory la `ssh-keygen` comando verrà creato automaticamente con le autorizzazioni corrette.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Immettere il nome del file viene salvato nel `~/.ssh/` directory:

```bash
id_rsa
```

Immettere passphrase per id_rsa:

```bash
correct horse battery staple
```

È ora disponibile una `id_rsa` e `id_rsa.pub` SSH coppia di `~/.ssh` directory.

```bash
ls -al ~/.ssh
```

Aggiungere la chiave appena creata al `ssh-agent` su Linux e Mac (anche aggiunte al portachiavi OSX):

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copiare la chiave pubblica SSH al Linux Server:

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Verificare l'account di accesso tramite chiavi invece che con una password:

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Descrizione dettagliata

Utilizzo di chiavi pubbliche e privatehttp SSH è il modo più semplice agli utenti di accedere ai server Linux. [Crittografia a chiave pubblica](https://en.wikipedia.org/wiki/Public-key_cryptography) offre un modo molto più sicuro agli utenti di accedere al Linux o BSD VM in Azure rispetto alle password, che possono essere forzata brute molto più semplice. La chiave pubblica può essere condivisi con chiunque. ma solo utente corrente (o dell'infrastruttura di sicurezza locale) possiede la chiave privata.  La chiave privata SSH deve avere una [password molto sicura](https://www.xkcd.com/936/) (origine:[xkcd.com](https://xkcd.com)) per salvaguardare essa.  La password è sufficiente per accedere alle SSH privata e **non** la password dell'account utente.  Quando si aggiunge una password per la chiave SSH, crittografa la chiave privata in modo che la chiave privata è inutile senza la password per sbloccarla.  Se un pirata informatico rubare la chiave privata e tale tasto non include una password, si potrà usare la chiave privata agli utenti di accedere a tutti i server con la chiave pubblica corrispondente.  Se una chiave privata è protetto da password non può essere utilizzato da tale utente non autorizzato, fornire un ulteriore livello di protezione dell'infrastruttura in Azure.

In questo articolo consente di creare file, chiave *ssh rsa* formattato consigliati per distribuzioni su Gestione risorse.  chiavi *SSH rsa* sono necessarie nel [portale](https://portal.azure.com) per distribuzioni classica e Manager delle risorse.

## <a name="create-the-ssh-keys"></a>Creare le chiavi SSH

Azure richiede almeno 2048 bit, ssh rsa formattare chiavi pubbliche e private. Per creare l'utilizzo di chiavi `ssh-keygen`, che richiede una serie di domande e quindi scrive una chiave privata e una chiave pubblica corrispondente. Dopo avere creata una macchina virtuale Azure, la chiave pubblica verrà copiata `~/.ssh/authorized_keys`.  Chiavi SSH `~/.ssh/authorized_keys` vengono usate per il client in base alla chiave privata su una connessione di accesso SSH sono destinate a utenti.

## <a name="using-ssh-keygen"></a>Utilizzando ssh keygen

Questo comando consente di creare una password protetta (crittografate) SSH coppia di chiavi con RSA 2048 bit e commenti per semplificarne l'identificazione.  

Iniziare modificando directory, in modo che tutti i ssh chiavi vengono create nella directory.

```bash
cd ~/.ssh
```

Se non si ha un `~/.ssh` directory la `ssh-keygen` comando verrà creato automaticamente con le autorizzazioni corrette.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

_Comando spiegato_

`ssh-keygen`= il programma utilizzato per creare le chiavi

`-t rsa`= tipo di chiave per creare la [RSA formato](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048`= bit della chiave

`-C "myusername@myserver"`= un commento aggiunto alla fine del file di chiave pubblica per semplificarne l'identificazione.  In genere come il commento viene utilizzato un messaggio di posta elettronica, ma è possibile utilizzare qualsiasi appropriata per l'infrastruttura.

### <a name="using-pem-keys"></a>Utilizzo dei tasti PEM

Se si utilizza la visualizzazione classica distribuisce modello (portale classica Azure o Azure servizio gestione CLI `asm`), potrebbe essere necessario usare PEM formattato chiavi SSH per accedere a nelle macchine virtuali Linux.  Ecco come creare una chiave PEM da un'esistente SSH chiave pubblica e un x509 esistente certificato.

Per creare un modulo PEM formattato chiave da una chiave pubblica SSH esistente:

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Esempio di ssh keygen

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

Salvare i file di chiave:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

Nome coppia di questo articolo.  Disporre di una coppia di chiavi denominata **id_rsa** è il valore predefinito e alcuni strumenti potrebbero aspettare il nome di file di chiave privata **id_rsa** in modo che uno è consigliabile. Nella directory `~/.ssh/` è il percorso predefinito per coppie di parole chiave SSH e file di configurazione SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Un elenco del `~/.ssh` directory. `ssh-keygen`Crea la `~/.ssh` directory se non è presenta e imposta anche le modalità di proprietà e file corrette.

Password della chiave:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen`si riferisce a una password come "una frase."  È *fortemente* consigliato per aggiungere una password per i coppie di parole chiave. Senza una password di protezione coppia di chiavi, tutti gli utenti con i file di chiave privata possibile usarlo per accedere a qualsiasi server che contiene la chiave pubblica corrispondente. Aggiunta di una password offre maggiore protezione, nel caso in cui un utente è in grado di accedere al file di chiave privato concesso ora per modificare le chiavi utilizzata per l'autenticazione.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Utilizzando ssh agente per archiviare la password chiave privata

Per evitare di digitare la password di un file di chiave privata con ogni accesso SSH, è possibile utilizzare `ssh-agent` per memorizzare nella cache la password di un file di chiave privata. Se si usa un computer Mac, portachiavi OSX Memorizza password chiave privata in modo sicuro quando si richiama `ssh-agent`.

Verificare innanzitutto che `ssh-agent` è in esecuzione

```bash
eval "$(ssh-agent -s)"
```

Aggiungere la chiave privata da `ssh-agent` utilizzando il comando `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Password della chiave privata, sono archiviate in `ssh-agent`.

## <a name="create-and-configure-an-ssh-config-file"></a>Creare e configurare un file di configurazione SSH

Si tratta di una procedura consigliata per creare e configurare un `~/.ssh/config` file per velocizzare l'accesso e per ottimizzare il comportamento di client SSH.

Nell'esempio seguente mostra una configurazione standard.

### <a name="create-the-file"></a>Creare il file

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modificare il file per aggiungere la nuova configurazione SSH:

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Esempio `~/.ssh/config` file:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Questa configurazione SSH fornisce sezioni per ogni server per attivare abbinare a sé stante coppia dedicata ognuno. Le impostazioni predefinite (`Host *`) sono per qualsiasi host che non corrispondono host specifici livello più alto nel file di configurazione.


### <a name="config-file-explained"></a>Spiegati del file di configurazione

`Host`= il nome dell'host chiamati sul terminale.  `ssh fedora22`indica `SSH` per utilizzare i valori nel blocco impostazioni `Host fedora22` Nota: può trattarsi di tutte le etichette che sia logico per l'utilizzo e non rappresentano il nome host effettivo di un server.

`Hostname 102.160.203.241`= l'indirizzo IP o il nome del DNS per il server viene eseguito l'accesso.

`User myusername`= account utente remoto da utilizzare durante la connessione al server.

`PubKeyAuthentication yes`= indica SSH che si desidera utilizzare una chiave SSH agli utenti di accedere.

`IdentityFile /home/myusername/.ssh/id_id_rsa`= la chiave privata SSH e corrispondente chiave pubblica da utilizzare per l'autenticazione.


## <a name="ssh-into-linux-without-a-password"></a>SSH in Linux senza una password

Dopo aver creato una coppia di chiavi SSH e un file di configurazione SSH configurato, comunque in grado di accedere alle VM Linux rapidamente e sicuro. La prima volta che si accede a un server mediante una chiave SSH prompt dei comandi è per la passphrase per tale file di chiave.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Comando spiegato

Quando `ssh fedora22` viene eseguito SSH prima individua e carica le impostazioni dalla `Host fedora22` blocco, quindi carica tutte le altre impostazioni dall'ultimo blocco `Host *`.

## <a name="next-steps"></a>Passaggi successivi

Freccia su successivo consiste nel creare macchine virtuali Linux Azure utilizzando la nuova chiave pubblica SSH.  Azure macchine virtuali creato con una chiave pubblica SSH come l'account di accesso sono più protette più macchine virtuali create con il metodo di accesso predefinito, la password.  Azure macchine virtuali create con i tasti SSH sono per impostazione predefinita configurato con password disattivata, evitando forzata brute tentativi per l'individuazione.

- [Creare una VM Linux protetta utilizzando un modello di Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Creare una VM Linux protetto tramite il portale di Azure](virtual-machines-linux-quick-create-portal.md)
- [Creare una VM Linux sicura usa CLI Azure](virtual-machines-linux-quick-create-cli.md)
