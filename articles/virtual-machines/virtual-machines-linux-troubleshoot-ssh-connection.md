<properties
    pageTitle="Risoluzione dei problemi di connessione SSH a una macchina virtuale | Microsoft Azure"
    description="Procedura per la risoluzione dei problemi, ad esempio "SSH non riuscita ' o"SSH connessione rifiutata"per un sistema operativo Linux macchine Virtuali di Azure."
    keywords="SSH connessione rifiutata, ssh errore, azure ssh, SSH connessione non è riuscita"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="iainfou"/>

# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Risolvere i problemi di connessioni SSH a una macchina virtuale Linux Azure che non riesce, gli errori evidenti, o è stata rifiutata
Esistono diversi motivi che si verificano errori Secure Shell (SSH), gli errori delle connessioni SSH, o SSH è stata rifiutata quando si tenta di connettersi a una Linux macchine (). In questo articolo consente di trovare e correggere i problemi. È possibile utilizzare il portale Azure fare CLI Azure o macchine Virtuali accesso estensione per Linux per individuare e risolvere i problemi di connessione.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Se necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile contattare esperti Azure su [Overflow dello Stack forum MSDN Azure](http://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare una richiesta di assistenza Azure. Passare al [sito del supporto tecnico Azure](http://azure.microsoft.com/support/options/) e selezionare **supporto**. Per informazioni sull'uso di Azure supporto, leggere le [domande frequenti di supporto Microsoft Azure](http://azure.microsoft.com/support/faq/).


## <a name="quick-troubleshooting-steps"></a>Azioni rapide sulla risoluzione dei problemi
Dopo ogni passaggio sulla risoluzione dei problemi, provare a riconnettersi a macchina virtuale.

1. Reimpostare la configurazione di SSH.
2. Reimpostare le credenziali per l'utente.
3. Verificare che le regole [Gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) consentano il traffico SSH.
    - Verificare l'esistenza di una regola gruppo di sicurezza di rete per consentire il traffico SSH (per impostazione predefinita, la porta TCP 22).
    - Non è possibile utilizzare il reindirizzamento della porta / mapping senza utilizzare un servizio di bilanciamento del carico Azure.
4. Verificare l' [integrità risorsa macchina virtuale](../resource-health/resource-health-overview.md). 
    - Assicurarsi che la macchina virtuale segnala come integro.
    - Se si dispone di diagnostica avvio abilitata, verificare che la macchina virtuale non è la segnalazione errori di avvio nei log.
5. Riavviare la macchina virtuale.
6. Ridistribuire la macchina virtuale.

Continuare la lettura per la procedura di risoluzione dei problemi più dettagliata e spiegazioni.


## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Metodi disponibili per la risoluzione dei problemi di connessione SSH

È possibile reimpostare le credenziali o SSH configurazione utilizzando uno dei metodi seguenti:

- [Portale azure](#using-the-azure-portal) - ottimo se è necessario ripristinare rapidamente la configurazione di SSH o chiave SSH e si non sono installati gli strumenti di Azure.
- [Comandi CLI azure](#using-the-azure-cli) - se si è già nella riga di comando, reimpostare rapidamente la configurazione di SSH o credenziali.
- [Estensione di azure VMAccessForLinux](#using-the-vmaccess-extension) - creare e riutilizzare il file di definizione json per reimpostare le credenziali dell'utente o configurazione SSH.

Dopo ogni passaggio sulla risoluzione dei problemi, provare a riconnettersi per la macchina virtuale. Se non è ancora possibile connettersi, procedere al passaggio successivo.


## <a name="using-the-azure-portal"></a>Tramite il portale di Azure
Il portale di Azure rappresenta un modo rapido per reimpostare le credenziali dell'utente o configurazione SSH senza installare gli strumenti presenti nel computer locale.

Selezionare la macchina virtuale nel portale di Azure. Scorrere fino alla sezione **supporto + risoluzione dei problemi** e selezionare **reimpostare la password** come illustrato nell'esempio seguente:

![Reimpostare le credenziali nel portale di Azure o una configurazione SSH](./media/virtual-machines-linux-troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Reimpostare la configurazione di SSH
Come primo passaggio, selezionare `Reset SSH configuration only` dal menu a discesa **modalità** come la schermata precedente, quindi fare clic sul pulsante **Reimposta** . Al termine di questa azione, provare ad accedere nuovamente la macchina virtuale.

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH per un utente
Per reimpostare le credenziali di un utente esistente, selezionare `Reset SSH public key` o `Reset password` dal menu a discesa **modalità** come la schermata precedente. Specificare il nome utente e una chiave SSH o nuova password, quindi fare clic sul pulsante **Reimposta** .

È anche possibile creare un utente con privilegi di sudo in macchine Virtuali dal menu. Immettere un nuovo nome utente e la password associata o chiave SSH e quindi fare clic sul pulsante **Reimposta** .


## <a name="using-the-azure-cli"></a>Usa CLI Azure
Se non è già fatto, [installare CLI Azure e connettersi al proprio abbonamento Azure](../xplat-cli-install.md). Verificare che si utilizza la modalità di gestione risorse come indicato di seguito:

```
azure config mode arm
```

Se è stato creato e caricare un'immagine di disco Linux personalizzata, assicurarsi di [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) versione 2.0.5 o è installato in un secondo momento. Per macchine virtuali create con le immagini di raccolta, l'estensione di accesso viene già installata e configurata automaticamente.

### <a name="reset-ssh-configuration"></a>Reimpostare SSH configurazione
La configurazione di SSHD è configurato correttamente o il servizio ha rilevato un errore. È possibile reimpostare SSHD per assicurarsi che la configurazione di SSH è valida. Reimpostazione SSHD dovrebbe essere il primo passaggio di risoluzione dei problemi scattate.

Nell'esempio seguente Reimposta SSHD in una macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Utilizzare nomi di gruppi personali macchine Virtuali e delle risorse come segue:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH per un utente
Se SSHD sembra funzionare correttamente, è possibile reimpostare la password per un utente donatore. Nell'esempio seguente consente di reimpostare le credenziali per `myUsername` a quello specificato nel `myPassword`, in macchine Virtuali denominata `myVM` in `myResourceGroup`. Usare i valori come indicato di seguito:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Se mediante l'autenticazione chiave SSH, è possibile reimpostare la chiave SSH per un utente specifico. Nell'esempio seguente viene aggiornata la chiave SSH archiviata in `~/.ssh/azure_id_rsa.pub` per l'utente denominato `myUsername`, in macchine Virtuali denominata `myVM` in `myResourceGroup`. Usare i valori come indicato di seguito:

```bash
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-file ~/.ssh/azure_id_rsa.pub
```


## <a name="using-the-vmaccess-extension"></a>Utilizzando l'estensione VMAccess
L'estensione di Access macchine Virtuali di Linux legge in un file di json che consente di definire le azioni da eseguire. Queste azioni includono reimpostazione SSHD, il ripristino di una chiave SSH o aggiunta di un utente. È comunque usare CLI Azure la chiamata dell'estensione VMAccess, ma è possibile riutilizzare i file json in macchine virtuali più se lo si desidera. Questo approccio consente di creare un archivio di file json che possono essere chiamati per determinati scenari.

### <a name="reset-sshd"></a>Reimposta SSHD
Creare un file denominato `PrivateConf.json` con il contenuto seguente:

```bash
{  
    "reset_ssh":"True"
}
```

È quindi Usa CLI Azure, chiamare il `VMAccessForLinux` estensione per reimpostare la connessione SSHD specificando il file json. Nell'esempio seguente Reimposta SSHD nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori come indicato di seguito:

```bash
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Reimpostare le credenziali SSH per un utente
Se SSHD sembra funzionare correttamente, è possibile reimpostare le credenziali per un utente donatore. Per reimpostare la password per un utente, creare un file denominato `PrivateConf.json`. Nell'esempio seguente consente di reimpostare le credenziali per `myUsername` a quello specificato nel `myPassword`. Immettere le seguenti righe nel `PrivateConf.json` file, utilizzando i valori:

```bash
{
    "username":"myUsername", "password":"myPassword"
}
```

O per reimpostare la chiave SSH per un utente, prima di tutto creare un file denominato `PrivateConf.json`. Nell'esempio seguente consente di reimpostare le credenziali per `myUsername` a quello specificato nel `myPassword`, in macchine Virtuali denominata `myVM` in `myResourceGroup`. Immettere le seguenti righe nel `PrivateConf.json` file, utilizzando i valori:

```bash
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Dopo aver creato il file json, utilizzare CLI Azure per chiamare la `VMAccessForLinux` estensione per reimpostare le credenziali dell'utente SSH specificando il file json. Nell'esempio seguente Reimposta credenziali nella macchina virtuale denominata `myVM` in `myResourceGroup`. Usare i valori come indicato di seguito:

```
azure vm extension set myResourceGroup myVM \
    VMAccessForLinux Microsoft.OSTCExtensions "1.2" \
    --private-config-path PrivateConf.json
```


## <a name="restart-a-vm"></a>Riavviare una macchina virtuale
Se si dispone di reimpostare le credenziali utente e configurazione di SSH o si è verificato un errore in questo modo, è possibile provare a riavviare la macchina virtuale all'indirizzo sottostante problemi di calcolo.

### <a name="azure-portal"></a>Portale di Azure
Per riavviare una macchina virtuale tramite il portale di Azure, selezionare la macchina virtuale e fare clic sul ***riavviare** pulsante come illustrato nell'esempio seguente:

![Riavviare una macchina virtuale nel portale di Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
Nell'esempio seguente si riavvia la macchina virtuale denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori come indicato di seguito:

```bash
azure vm restart --resource-group myResourceGroup --name myVM
```


## <a name="redeploy-a-vm"></a>Ridistribuire una macchina virtuale
È possibile ridistribuire una macchina virtuale in un altro nodo all'interno di Azure, che potrebbe risolvere eventuali problemi di rete sottostante. Per informazioni su ridistribuire una macchina virtuale, vedere [ridistribuire macchina virtuale al nuovo livello di nodo Azure](virtual-machines-windows-redeploy-to-new-node.md).

> [AZURE.NOTE] Al termine dell'operazione, disco temporanee dati andranno persi e indirizzi IP dinamici che sono associati a macchina virtuale verranno aggiornati.

### <a name="azure-portal"></a>Portale di Azure
Per ridistribuire una macchina virtuale tramite il portale di Azure, selezionare la macchina virtuale e scorrere fino alla sezione **supporto + risoluzione dei problemi** . Fare clic sul pulsante **ridistribuire** come illustrato nell'esempio seguente:

![Ridistribuire una macchina virtuale nel portale di Azure](./media/virtual-machines-linux-troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>CLI Azure
Nell'esempio seguente ridistribuisce macchine Virtuali denominata `myVM` nel gruppo di risorse denominato `myResourceGroup`. Usare i valori come indicato di seguito:

```bash
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Macchine virtuali create usando il modello di distribuzione classica

Provare questa procedura per risolvere gli errori di connessione SSH più comuni di macchine virtuali che sono state create usando il modello di distribuzione classica. Dopo ogni passaggio, provare a riconnettersi a macchina virtuale.

- Impostare l'accesso remoto dal [portale di Azure](https://portal.azure.com). Nel portale di Azure, selezionare la macchina virtuale e fare clic sul pulsante **Reimposta remoto** .

- Riavviare la macchina virtuale. Nel [portale di Azure](https://portal.azure.com), selezionare la macchina virtuale e fare clic sul pulsante **Riavvia** .

    -OPPURE-

    Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **macchine virtuali** > **istanze** > **riavviare**.

- Ridistribuire macchine Virtuali in un nuovo nodo Azure. Per informazioni su come ridistribuire una macchina virtuale, vedere [ridistribuire macchina virtuale al nuovo livello di nodo Azure](virtual-machines-windows-redeploy-to-new-node.md).

    Al termine dell'operazione, disco temporanee dati andranno persi e indirizzi IP dinamici che sono associati a macchina virtuale verranno aggiornati.

- Seguire le istruzioni in [come reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md) :
    - Reimpostare la password o chiave SSH.
    - Creare un account utente _sudo_ .
    - Reimpostare la configurazione di SSH.

- Verificare l'integrità di risorse di Virtual Machine per eventuali problemi di piattaforma.<br>
  Selezionare la macchina virtuale e scorrere verso il basso **Impostazioni** > **Verifica dell'integrità**.


## <a name="additional-resources"></a>Risorse aggiuntive

- Se si riesce ancora a SSH per la macchina virtuale dopo aver seguito i passaggi dopo, vedere [ulteriori operazioni seguenti](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md) per rivedere i passaggi aggiuntivi per risolvere il problema.

- Per ulteriori informazioni sulla risoluzione dei problemi di accesso alle applicazioni, vedere [risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale Azure](virtual-machines-linux-troubleshoot-app-connection.md)

- Per ulteriori informazioni sulla risoluzione dei problemi macchine virtuali che sono state create usando il modello di distribuzione classico, vedere [come reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).
