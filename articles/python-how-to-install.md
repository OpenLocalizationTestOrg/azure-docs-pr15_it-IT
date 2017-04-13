<properties
    pageTitle="Installare Python e SDK - Azure"
    description="Informazioni su come installare Python e SDK da usare con Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>L'installazione di Python e SDK

Python semplice da configurare in Windows e preinstallata sul Mac, Linux e [Bash per Windows](https://msdn.microsoft.com/commandline/wsl/about). Questa guida illustra installazione e alla preparazione del computer per l'utilizzo con Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Novità in Python SDK Azure?

Azure SDK per Python include componenti che consentono di sviluppare, distribuire e gestire applicazioni Python per Azure. In particolare, Azure SDK per Python include le operazioni seguenti:

* **Le raccolte gestione**. Queste librerie forniscono un'interfaccia Gestione risorse Azure, ad esempio gli account di archiviazione, macchine virtuali.

* **Librerie di Runtime**. Queste librerie forniscono un'interfaccia per l'accesso alle caratteristiche di Azure, ad esempio servizio di archiviazione e bus.

## <a name="which-python-and-which-version-to-use"></a>Quali Python e la versione da utilizzare

Sono disponibili diverse versioni interpreti Python - alcuni esempi:

* CPython - interprete di Python standard e più comunemente usato
* PyPy - implementazione alternativa veloce, conforme CPython
* IronPython - interprete Python che viene eseguito su .net/CLR
* Jython - interprete Python che viene eseguita la macchina virtuale Java

Versione 2.7 **CPython** o 3.3 + e PyPy 5.4.0 sono testati e supportata per Python Azure SDK.

## <a name="where-to-get-python"></a>Dove ottenere Python?

Esistono diversi modi per ottenere CPython:

* Direttamente da [www.python.org][]
* Da un distro sicure, ad esempio [www.continuum.io][], [www.enthought.com][] o [www.activestate.com][]
* Generazione di origine.

A meno che non si dispone di esigenze specifiche, è consigliabile le prime due opzioni.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Installazione di SDK in Windows, Linux e Mac OS (solo librerie client)

Se si dispone già di Python installato, è possibile utilizzare pip per installare un raggruppamento di tutte le raccolte di client nel proprio 2.7 Python esistente o ambiente Python 3.3 +. Questa operazione verrà scaricate i pacchetti dall' [Indice pacchetto Python][] (PyPI).

Potrebbe essere necessario diritti di amministratore:

- Linux e Mac OS, utilizzare la `sudo` comando: `sudo pip install azure-mgmt-compute`.
- Windows: aprire il PowerShell/prompt dei comandi come amministratore

È possibile installare singolarmente ogni raccolta per ogni servizio Azure:

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Anteprima pacchetti possono essere installati utilizzando il `--pre` contrassegno:

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

È inoltre possibile installare un set di Azure raccolte in una singola riga utilizzando i `azure` pacchetto di metadati. Poiché non tutti i pacchetti di questo pacchetto di metadati pubblicati come stabile ancora, il `azure` pacchetto di metadati è ancora in anteprima. Tuttavia, i pacchetti di base, da prospettive qualità/completezza codice possono essere considerati "stabili" in questo momento
- sarà ufficiale indicato come tale sincronizzati con altre lingue più presto possibile. È in corso non prevede alle modifiche apportate ulteriormente principali nel frattempo.

Poiché si tratta di una versione preview, è necessario utilizzare la `--pre` contrassegno:

```console
   $ pip install --pre azure
```
   
o direttamente

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Ottenere ulteriori pacchetti

L' [Indice di pacchetto Python][] (PyPI) è una selezione di raccolte Python RTF.  Se si è scelto di installare un Distro, è necessario già la maggior parte dei bit interessanti per i diversi scenari di sviluppo di applicazioni web per l'elaborazione tecnica.


## <a name="python-tools-for-visual-studio"></a>Python Tools per Visual Studio

[Python Tools per Visual Studio][] (PTVS) è un plug-in / OSS gratuiti di Microsoft, che viene convertita VS in un IDE Python completa:

![procedure-to-installa-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

Utilizzo PTVS è facoltativo, ma è consigliabile come fornisce supporto Python e progetto Web, il debug, analisi, finestra interattiva, la modifica dei modelli e Intellisense.

PTVS semplifica la distribuzione di Microsoft Azure con supporto per la distribuzione di [Servizi Cloud][] e [siti Web][].

PTVS funziona con un file di 2013 Studio Visual esistente o installazione 2015.  Per la documentazione, download e discussioni, vedere [Python Tools per Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python Azure scenari per Linux e Mac OS

Per Linux o Mac OS, principali scenari di Azure supportate:

1. Utilizzo di servizi di Windows Azure tramite le raccolte di client per Python

2. Esecuzione dell'applicazione in una macchina virtuale Linux

3. Sviluppo e ai siti Web di Azure tramite operazioni di pubblicazione

Il primo scenario consente di creare applicazioni web avanzate che sfruttare le funzionalità di Azure PaaS, ad esempio [archiviazione blob][], [lo spazio di archiviazione di coda][], [lo spazio di archiviazione tabella][] ecc tramite wrapper Pythonic per le API REST di Azure. Questi compatibile con lo stesso Windows, Mac e Linux.  È inoltre possibile utilizzare queste librerie client dal computer di sviluppo locale o una VM Linux in esecuzione in Azure.

Per lo scenario macchine Virtuali, avviare una VM Linux preferito (Ubuntu, CentOS, Suse) e si è soddisfatti/gestire Esegui.  Ad esempio, è possibile eseguire replica [IPython][] del blocco appunti nel computer Windows, Mac o Linux e dal browser a un Linux o macchine Virtuali di più processi di Windows in esecuzione il motore di IPython su Azure. Vedere l'esercitazione [IPython blocco appunti in Azure][] per altre informazioni.

Per informazioni sull'installazione una VM Linux, vedere l'esercitazione [Crea macchina virtuale in esecuzione Linux][] .

Usa fra distribuzione, è possibile sviluppare un'applicazione web Python e pubblicare il progetto in un sito Web Azure da un sistema operativo.  Quando si inserisce l'archivio di Azure, viene creato automaticamente un ambiente virtuale e pip installare i pacchetti necessari.

Per ulteriori informazioni sullo sviluppo e pubblicazione di siti Web di Azure, vedere le esercitazioni per [La creazione di siti Web con Django][], [La creazione di siti Web con bottiglia][]e [Creazione di siti Web con pallone][]. Per ulteriori informazioni generali sull'uso di qualsiasi framework WSGI conforme, vedere [Configurazione Python di siti Web di Azure][].


## <a name="additional-software-and-resources"></a>Software aggiuntivo e risorse:

* [Azure SDK per Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK per Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Esempi di Azure ufficiali per Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribuzione di Python Analitica serie continua][]
* [Distribuzione di Enthought Python][]
* [Distribuzione di ActiveState Python][]
* [SciPy - una famiglia di prodotti di raccolte Python scientifico][]
* [NumPy - una raccolta di valori numerici per Python][]
* [Django Project - un framework/CMS web avanzato][]
* [IPython - un replica/Notebook avanzato per Python][]
* [Blocco appunti IPython su Azure][]
* [Python Tools per Visual Studio su GitHub][]
* [Centro per sviluppatori Python](/develop/python/)

[Distribuzione di Python Analitica serie continua]: http://continuum.io
[Distribuzione di Enthought Python]: http://www.enthought.com
[Distribuzione di ActiveState Python]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.ActiveState.com]: http://www.activestate.com
[SciPy - una famiglia di prodotti di raccolte Python scientifico]: http://www.scipy.org
[NumPy - una raccolta di valori numerici per Python]: http://www.numpy.org
[Django Project - un framework/CMS web avanzato]: http://www.djangoproject.com
[IPython - un replica/Notebook avanzato per Python]: http://ipython.org
[IPython]: http://ipython.org
[Blocco appunti IPython su Azure]: virtual-machines-linux-jupyter-notebook.md
[Servizi cloud]: cloud-services-python-ptvs.md
[Siti Web]: web-sites-python-ptvs-django-mysql.md
[Python Tools per Visual Studio]: http://aka.ms/ptvs
[Python Tools per Visual Studio su GitHub]: https://github.com/microsoft/ptvs
[Python pacchetto indice]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Creare una macchina virtuale che eseguono Linux]: virtual-machines-linux-quick-create-cli.md
[Creazione di siti Web con Django]: web-sites-python-create-deploy-django-app.md
[Creazione di siti Web con bottiglia]: web-sites-python-create-deploy-bottle-app.md
[Creazione di siti Web con pallone]: web-sites-python-create-deploy-flask-app.md
[Configurazione Python con Azure siti Web]: web-sites-python-configure.md
[archiviazione tabella]: storage-python-how-to-use-table-storage.md
[spazio di archiviazione coda]: storage-python-how-to-use-queue-storage.md
[archiviazione BLOB]: storage-python-how-to-use-blob-storage.md
