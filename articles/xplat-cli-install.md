<properties
    pageTitle="Installare l'interfaccia della riga di comando Azure | Microsoft Azure"
    description="Installare l'interfaccia della riga di Azure (CLI) per Mac, Linux e Windows iniziare a usare i servizi di Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Installare CLI Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [CLI Azure](xplat-cli-install.md)

Installare rapidamente l'interfaccia della riga di comando Azure (Azure CLI) per l'utilizzo di un insieme di comandi basate su shell Apri origine per la creazione e gestione delle risorse in Microsoft Azure. Sono disponibili diverse opzioni per installare questi strumenti multipiattaforma nel computer in uso: 

* **pacchetto npm** - eseguire npm (manager pacchetto per JavaScript) per installare il pacchetto di Azure CLI più recente nel sistema operativo o distribuzione Linux. È necessario Node e npm nel computer in uso.
* **Programma di installazione** - Download un programma di installazione per agevolare l'installazione su Mac o Windows.
* **Contenitore docker** - iniziare a usare CLI più recente in un contenitore di Docker pronto per la tecnologia a portata. Richiede host Docker nel computer in uso.
    
Per ulteriori opzioni e sfondo, vedere archivio di progetto su [GitHub](https://github.com/azure/azure-xplat-cli). 

Una volta CLI Azure è installata, [connetterlo con l'abbonamento Azure](xplat-cli-connect.md) ed eseguire i comandi di **azure** dall'interfaccia della riga di comando (Bash, terminale, il prompt dei comandi e così via) per lavorare con le risorse Azure.



## <a name="option-1-install-an-npm-package"></a>Opzione 1: Installare un pacchetto npm

Per installare CLI da un pacchetto di npm, assicurarsi che scaricato e installato [Node e npm più recenti](https://nodejs.org/en/download/package-manager/). Eseguire quindi **npm installa** per installare il pacchetto di azure cli: 

    npm install -g azure-cli

Nelle distribuzioni Linux, potrebbe essere necessario utilizzare **sudo** per eseguire il comando __npm__ , come indicato di seguito:

    sudo npm install -g azure-cli

> [AZURE.NOTE]Se è necessario installare o aggiornare Node e npm nel sistema operativo o distribuzione Linux, è consigliabile installare la versione più recente di risultati node (4). Se si usa una versione precedente, è possibile che venga visualizzato degli errori di installazione. 

Se si preferisce, scaricare il più recente di Linux [file tar] [ linux-installer] per il pacchetto di npm in locale. Quindi, installare il pacchetto scaricato npm come indicato di seguito (nelle distribuzioni Linux che potrebbe essere necessario usare **sudo**):

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Opzione 2: Utilizzare un programma di installazione

Se si usa un computer Mac o Windows, i programmi di installazione CLI seguenti sono disponibili per il download:

* [Programma di installazione di Mac OS X][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]In Windows, è inoltre possibile scaricare l' [Installazione guidata piattaforma Web](https://go.microsoft.com/?linkid=9828653) per installare CLI. Il programma di installazione avrà la possibilità di installazione aggiuntive Azure SDK e gli strumenti della riga di comando dopo l'installazione di CLI. 


## <a name="option-3-use-a-docker-container"></a>Opzione 3: Utilizzare un contenitore Docker

Se è stata configurata nel computer come host [Docker](https://docs.docker.com/engine/understanding-docker/) , è possibile eseguire il più recente di Azure CLI in un contenitore Docker. Eseguire il comando seguente (nelle distribuzioni Linux che potrebbe essere necessario usare **sudo**):

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Eseguire i comandi CLI Azure
Dopo l'installazione di CLI Azure, eseguire il comando **azure** dall'interfaccia utente della riga di comando (Bash, terminale, il prompt dei comandi e così via). Ad esempio, per eseguire il comando della Guida, digitare quanto segue:

```
azure help
```
> [AZURE.NOTE]In alcune distribuzioni Linux, può essere visualizzato un messaggio di errore simile a `/usr/bin/env: ‘node’: No such file or directory`. Questo errore proviene da installazioni recenti di Node viene installato in /usr/bin/nodejs. Per risolvere il problema, creare un collegamento symbolic /usr/bin/node eseguendo questo comando:

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Per visualizzare la versione di CLI Azure è stato installato, digitare quanto segue:

```
azure --version
```

A questo punto si è pronti! Per accedere a tutti i comandi CLI per lavorare con le risorse, [connettersi al proprio abbonamento Azure da CLI Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Quando si usa CLI Azure, viene visualizzato un messaggio che chiede se si vuole consentire a Microsoft di raccogliere informazioni sull'uso. La partecipazione è facoltativa. Se si sceglie di partecipare, è possibile impedire in qualsiasi momento eseguendo `azure telemetry --disable`. Per attivare la partecipazione in qualsiasi momento, eseguire `azure telemetry --enable`.


## <a name="update-the-cli"></a>Aggiornamento CLI

Microsoft rilascia spesso versioni aggiornate di CLI Azure. Reinstallare CLI tramite il programma di installazione del sistema operativo o eseguire il contenitore Docker più recente. In alternativa, se si dispone Node e npm installato più recenti, aggiornare digitando le operazioni seguenti (nelle distribuzioni Linux che potrebbe essere necessario usare **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Attivare il completamento della scheda

Scheda completamento dei comandi CLI è supportato per Mac e Linux.

Per abilitarla in zsh, eseguire:

```
echo '. <(azure --completion)' >> .zshrc
```

Per abilitarla in bash, eseguire:

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Passaggi successivi 

* [Connessione da CLI all'abbonamento Azure](xplat-cli-connect.md) per creare e gestire le risorse di Azure.

* Per ulteriori informazioni su come CLI Azure, scaricare il codice sorgente, segnalare problemi o contribuire al progetto, visitare [GitHub repository per CLI Azure](https://github.com/azure/azure-xplat-cli).

* Se hai domande sull'utilizzo di Azure CLI o Azure, visitare i [Forum di Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Se si desidera, è inoltre possibile eseguire in base Python [Azure CLI 2.0 anteprima](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md
