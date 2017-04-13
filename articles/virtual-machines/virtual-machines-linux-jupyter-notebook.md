<properties
    pageTitle="Creare un blocco appunti Jupyter/IPython | Microsoft Azure"
    description="Informazioni su come distribuire il blocco appunti Jupyter/IPython in un computer virtuale Linux creato con il modello di distribuzione di manager delle risorse di Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Blocco appunti Jupyter su Azure

Il [progetto Jupyter](http://jupyter.org), in precedenza il [progetto IPython](http://ipython.org)fornisce un insieme di strumenti per il calcolo scientifico utilizzando potenti shell interattiva che combinano esecuzione di codice con la creazione di un documento di calcolo live. Questi file del blocco appunti possono contenere testo scelti, formule matematiche, codice input, i risultati, elementi grafici, video e qualsiasi altro tipo di file multimediali che è in grado di visualizzare un browser web moderno. Se si conosce assolutamente Python e informazioni in un ambiente divertente e interattivo o eseguire alcune gravi computing parallelo/tecnici, il blocco appunti Jupyter è un'ottima scelta.

![Schermata](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) pacchetti utilizzando SciPy e Matplotlib per analizzare la struttura di una registrazione audio.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter due modi: I blocchi appunti Azure o distribuzione personalizzata
Azure offre un servizio che è possibile utilizzare per [iniziare rapidamente a utilizzare Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  Tramite il servizio di blocco appunti di Azure, è possibile facilmente accedere all'interfaccia di web accessibile del Jupyter alle risorse di calcolo scalable con tutte le potenzialità di Python e le relative librerie molti.  Dopo l'installazione viene gestito tramite il servizio, gli utenti possono accedere a queste risorse senza la necessità di amministrazione e configurazione dall'utente.

Se il servizio di blocco appunti non funziona per lo scenario di continuare a leggere questo articolo in cui è verranno visualizzate come distribuire il blocco appunti Jupyter in Microsoft Azure con Linux macchine ().

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Creare e configurare una macchina virtuale in Azure

Il primo passaggio consiste nel creare una macchine () in esecuzione in Azure.
Questa macchina virtuale è completa del sistema operativo nel cloud e verrà utilizzata per eseguire il blocco appunti Jupyter. È in grado di eseguire macchine virtuali Linux e Windows Azure e verranno illustrate le caratteristiche di installazione e configurazione di Jupyter su entrambi i tipi di macchine virtuali.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Creare una VM Linux e apertura di una porta Jupyter

Seguire le istruzioni specificato [qui] [ portal-vm-linux] per creare una macchina virtuale di una distribuzione *Ubuntu* . In questa esercitazione utilizza Ubuntu Server 14.04 risultati. Supponiamo il nome utente *azureuser*.

Dopo la macchina virtuale distribuisce è necessario aprire una regola di protezione per il gruppo di sicurezza di rete.  Dal portale di Azure, passare ai **Gruppi di sicurezza di rete** e aprire la scheda per il gruppo di sicurezza corrispondente di una macchina virtuale. È necessario aggiungere una regola di protezione in ingresso con le seguenti impostazioni: **TCP** per il protocollo **\*** per la porta di origine (pubblico) e **9999** per la porta di destinazione (privato).

![Cattura di schermata](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

Nel gruppo di sicurezza di rete, fare clic su **Interfacce di rete** e prendere nota dell' **Indirizzo IP pubblico** come saranno necessarie per connettere la macchina virtuale nel passaggio successivo.

## <a name="install-required-software-on-the-vm"></a>Installare il software necessario nella macchina virtuale

Per eseguire il blocco appunti Jupyter in nostro macchine Virtuali, è necessario installare Jupyter e le dipendenze. Connettersi al linux macchine virtuali mediante ssh e cui associare il nome utente e la password è scelto al momento della creazione della macchina virtuale. In questa esercitazione verrà utilizzare PuTTY e connettersi da Windows.

### <a name="installing-jupyter-on-ubuntu"></a>L'installazione di Jupyter su Ubuntu
Installare Anaconda una popolari scienza python la distribuzione dei dati, utilizzando uno dei collegamenti forniti da [Analitica serie continua](https://www.continuum.io/downloads).  Stesura di questo documento, i collegamenti seguenti sono al massimo tutte le versioni di Data.

#### <a name="anaconda-installs-for-linux"></a>Installazioni anaconda per Linux
<table>
  <th>3.4 Python</th>
  <th>2.7 Python</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>a 64 bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>a 64 bit</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bit</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bit</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>L'installazione di Anaconda3 2.3.0 a 64 bit in Ubuntu
Ad esempio, si tratta di come è possibile installare Anaconda in Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Cattura di schermata](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Configurazione Jupyter e l'utilizzo di SSL
Dopo l'installazione è necessario richiedere alcuni minuti per installare i file di configurazione per Jupyter. Se si riscontrano dei problemi relativi alla configurazione Jupyter può essere utile esaminare la [Documentazione Jupyter per l'esecuzione di un Server di blocco appunti](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Successivo è `cd` della directory del profilo per creare il certificato SSL e modificare file di configurazione dei profili.

Il comando seguente su Linux.

    cd ~/.jupyter

Usare il comando seguente per creare il certificato SSL (Linux e Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Si noti che si sta creando un certificato SSL autofirmato, quando la connessione al blocco appunti nel browser si otterranno un avviso di sicurezza.  Per l'utilizzo di produzione a lungo termine, verrà da usare un certificato correttamente con associato all'organizzazione.  Poiché la gestione dei certificati è lo scopo di questo demo, si verrà continuare a un certificato autofirmato per il momento.

Oltre a utilizzare un certificato, è anche necessario fornire una password per proteggere il blocco appunti dall'utilizzo non autorizzato.  Per motivi di sicurezza Jupyter Usa password crittografate nel file di configurazione, sarà necessario crittografare la password prima di tutto.  IPython fornisce un'utilità per eseguire questa operazione; al prompt dei comandi eseguire il comando seguente.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Verrà chiesto di una password e conferma e quindi verrà stampata la password. Nota Questa per il passaggio successivo.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Successivamente, è necessario modificare file di configurazione del profilo, ossia il `jupyter_notebook_config.py` file nella directory dell'utente è impegnato in.  Nota che il file non esiste, creare solo se le maiuscole/minuscole.  Il file contiene un numero di campi e per impostazione predefinita tutti i commenti vengono inseriti.  È possibile aprire questo file con un editor di testo di base alle proprie esigenze e verificare che dispone almeno di contenuto seguente. **Assicurarsi di sostituire c.NotebookApp.password nella configurazione con sha1 ottenuto nel passaggio precedente**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Eseguire il blocco appunti Jupyter

A questo punto sono pronti per avviare il blocco appunti Jupyter. A tale scopo, passare alla directory che si desidera archiviare i blocchi appunti in e avviare il server di blocco appunti Jupyter con il comando seguente.

    /anaconda3/bin/jupyter-notebook

Dovrebbe ora essere possibile accedere al blocco appunti Jupyter all'indirizzo `https://[PUBLIC-IP-ADDRESS]:9999`.

Quando si accede al blocco appunti, la pagina di accesso richiede la password. E dopo l'accesso, verrà visualizzato il Dashboard"Jupyter" blocco appunti, che corrisponde al centro di formato controllo per tutte le operazioni di blocco appunti.  Da questa pagina è possibile creare nuovi blocchi appunti e aprire quelli esistenti.

![Cattura di schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>Utilizzando il blocco appunti Jupyter

Se si fa clic sul pulsante **Nuovo** , si verrà visualizzata la pagina di apertura seguenti.

![Cattura di schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

Nell'area contrassegnata con un `In []:` è richiesta l'area di input, qui è possibile digitare il codice Python valido e verrà eseguita quando si raggiunge `Shift-Enter` oppure fare clic sull'icona "Riproduci" (il triangolo verso destra nella barra degli strumenti).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Un potente paradigma: live calcoli documenti con file multimediali elaborati

Il blocco appunti dovrebbe risultare molto naturale per chiunque abbia utilizzato Python e un editor di testo, perché è in alcuni modi in una combinazione di entrambi: è possibile eseguire blocchi di codice Python, ma è anche possibile mantenere le note e altro testo applicando lo stile di una cella da "Codice" a "Delle vendite promozionali" usando il menu a discesa sulla barra degli strumenti.

Jupyter è molto più di un editor di testo, in quanto consente la combinazione di calcolo e file multimediali complessi (testo, immagini, video e qualsiasi elemento che può visualizzare un browser web moderno). È possibile combinare, testo, codice, video e altro ancora.

![Cattura di schermata](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

E con la potenza di molte librerie eccellente di Python per scientifico e tecnico computing, nella schermata seguente, che un calcolo semplice può essere eseguito con la stessa semplicità un'analisi rete complessa in un ambiente.

Questo paradigma di combinazione di potenza del web moderno con calcolo live offre molte possibilità ed è ideale per il cloud; è possibile utilizzare il blocco appunti:

* Un'area di calcolo dettatura registrare esplorativo collaborare a un problema.

* Per condividere i risultati con i colleghi, nel modulo calcolo 'live' o nei formati copia cartacea (HTML, PDF).

* Per distribuire e presentare materiale didattico live che implicano l'invio di calcolo, in modo che gli studenti possono immediatamente sperimentare il codice reale, modificarlo ed eseguirla nuovamente in modo interattivo.

* Per fornire "paper eseguibile" per presentare i risultati della ricerca in modo che può essere immediatamente riprodotta, convalidato ed estesa da altri utenti.

* Come piattaforma per l'elaborazione collaborativa: più utenti possono accedere allo stesso server blocco appunti per condividere una sessione di calcolo.


Se si passa al codice sorgente IPython [repository][], si noterà un'intera directory di esempi di blocco appunti che è possibile scaricare e quindi provare a utilizzare nella propria macchine Virtuali Jupyter Azure.  È sufficiente scaricare il `.ipynb` file del sito e caricarli nel dashboard del blocco appunti in macchine Virtuali di Azure (o scaricarli direttamente in macchina virtuale).

## <a name="conclusion"></a>Conclusioni

Il blocco appunti Jupyter fornisce una potente interfaccia per accedere in modo interattivo il potere degli ecosistema Python in Azure.  Coprire una vasta gamma di casi di utilizzo incluso esplorazione semplice e formazione Python, analisi dei dati e visualizzazione, simulazione ed elaborazione in parallelo. I documenti blocco appunti risultanti contengono un record completo di calcoli che vengono eseguite e può essere condiviso con altri utenti di Jupyter.  Il blocco appunti Jupyter possono essere utilizzato come un'applicazione locale, ma è ideale per le distribuzioni cloud su Azure

Le funzionalità di base di Jupyter sono inoltre disponibili in Visual Studio tramite [Python Tools per Visual Studio][] (PTVS). PTVS è disponibile gratuitamente e Apri origine plug-in di Microsoft tramite cui viene convertita Visual Studio in un ambiente di sviluppo Python avanzato che include un editor avanzato grazie a IntelliSense, debug, analisi e in parallelo computing integrazione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[archivio]: https://github.com/ipython/ipython
[Python Tools per Visual Studio]: http://aka.ms/ptvs
