<properties
    pageTitle="Script di sviluppo di azione con basati su Linux HDInsight | Microsoft Azure"
    description="Informazioni su come personalizzare i cluster basati su Linux HDInsight con azione Script. Le azioni script rappresentano un modo per personalizzare i cluster Azure HDInsight specificando le impostazioni di configurazione cluster o l'installazione di servizi aggiuntivi, strumenti o altro software sul cluster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Sviluppo di azione di script con HDInsight

Le azioni script rappresentano un modo per personalizzare i cluster Azure HDInsight specificando le impostazioni di configurazione cluster o l'installazione di servizi aggiuntivi, strumenti o altro software sul cluster. È possibile utilizzare le azioni script durante la creazione di cluster o in un cluster in esecuzione.

> [AZURE.NOTE] Le informazioni contenute in questo documento sono specifiche di cluster basati su Linux HDInsight. Per informazioni sull'utilizzo di azioni di script con i cluster basato su Windows, vedere [sviluppo di azione di Script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Quali sono le azioni script?

Azioni di script sono script Bash Azure eseguibile nei nodi del cluster per apportare le modifiche di configurazione o installare il software. Un'azione script viene eseguita come radice e fornisce diritti di accesso completo ai nodi cluster.

Azioni di script possono essere applicate tramite i metodi seguenti:

| Utilizzare questo comando per applicare uno script... | Durante il cluster creazione... | In un cluster in esecuzione... |
| ----- |:-----:|:-----:|
| Portale di Azure | ✓ | ✓ |
| PowerShell Azure | ✓ | ✓ |
| CLI Azure | &nbsp; | ✓ |
| HDInsight .NET SDK | ✓ | ✓ |
| Modelli di gestione risorse di Azure | ✓ | &nbsp; |

Per ulteriori informazioni sull'uso di questi metodi per applicare le azioni script, vedere [personalizzare HDInsight cluster utilizzo di azioni di script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Procedure consigliate per lo sviluppo di script

Quando si sviluppa uno script personalizzato per un cluster di HDInsight, sono disponibili diverse procedure consigliate da tenere presenti:

- [Versione Hadoop](#bPS1)
- [Destinazione la versione del sistema operativo](#bps10)
- [Fornire stabili collegamenti alle risorse script](#bPS2)
- [Utilizzare le risorse precompilate](#bPS4)
- [Verificare che lo script di personalizzazione cluster idempotenti](#bPS3)
- [Assicurarsi di disponibilità di architettura cluster](#bPS5)
- [Configurare i componenti personalizzati per l'utilizzo di archiviazione Blob Azure](#bPS6)
- [Scrivere le informazioni STDOUT e STDERR](#bPS7)
- [Salvare i file in formato ASCII con terminazioni nuova riga](#bps8)
- [Utilizzare la logica riprova per correggere gli errori temporanei](#bps9)

> [AZURE.IMPORTANT] Azioni di script devono essere completate entro 60 minuti oppure vengono riprodotti timeout. Durante il provisioning di nodo, lo script è stato eseguito contemporaneamente ad altri processi di installazione e configurazione. Concorrenza per le risorse, ad esempio ora o in rete larghezza di banda può causare lo script richiedere più tempo rispetto a un ambiente di sviluppo.

### <a name="bPS1"></a>Versione Hadoop

Versioni diverse di HDInsight dispongono di versioni diverse di Hadoop servizi e dei relativi componenti. Se uno script che prevede una versione specifica di un servizio o componente, è necessario utilizzare solo lo script con la versione di HDInsight che include i componenti necessari. È possibile trovare informazioni sulle versioni dei componenti inclusi in HDInsight utilizzando il documento di [controllo delle versioni componente HDInsight](hdinsight-component-versioning.md) .

###<a name="bps10"></a>Versione del sistema operativo

HDInsight basati su Linux è basata sulla distribuzione Ubuntu Linux. Versioni diverse di HDInsight si basano su versioni diverse di Ubuntu, che può influire comportamento di uno script. HDInsight 3,4 e versioni precedenti, ad esempio, si basano versioni Ubuntu che utilizzano Upstart. Versione 3.5 si basa su 16.04 Ubuntu, che utilizza Systemd. Systemd e Upstart si basano su diversi comandi, in modo che lo script deve essere scritti per l'uso con entrambi.

Un'altra importante differenza tra HDInsight 3.4 e 3.5 è che `JAVA_HOME` punta ora al linguaggio 8.

È possibile controllare la versione del sistema operativo utilizzando `lsb_release`. Frammenti di codice seguenti dello script di installazione tonalità viene illustrato come determinare se lo script è in esecuzione Ubuntu 14 o 16:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

È possibile trovare lo script completo che contiene questi frammenti in https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Per la versione di Ubuntu utilizzato da HDInsight, vedere il documento [versione HDInsight](hdinsight-component-versioning.md) .

Per comprendere le differenze tra Systemd e Upstart, vedere [Systemd per gli utenti Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornire stabili collegamenti alle risorse script

È necessario assicurarsi che gli script e le risorse utilizzate dallo script restano disponibili per l'intera durata del cluster e che le versioni dei file non cambiano per la durata. Queste risorse sono necessari se verranno aggiunti nuovi nodi al cluster durante il ridimensionamento operazioni.

La procedura consigliata consiste nello scaricare e archiviare tutti gli elementi in un account Azure lo spazio di archiviazione dall'abbonamento.

> [AZURE.IMPORTANT] L'account di archiviazione utilizzato deve essere l'account di archiviazione predefinito per il cluster o un contenitore pubblico e di sola lettura in qualsiasi altro account di archiviazione.

Gli esempi forniti da Microsoft, ad esempio, sono archiviati in account di archiviazione [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) è un contenitore di pubblico di sola lettura e gestito dal team di HDInsight.

### <a name="bPS4"></a>Utilizzare le risorse precompilate

Per ridurre il tempo che necessario per eseguire lo script, evitare le operazioni che compilare risorse dal codice sorgente. Se, tuttavia, pre-compilare le risorse e archiviare la versione binaria in archiviazione Blob Azure in modo che può essere scaricato rapidamente al cluster da uno script.

### <a name="bPS3"></a>Verificare che lo script di personalizzazione cluster idempotenti

Script devono essere progettati per essere idempotente nel senso che se lo script è stato eseguito più volte, è necessario che il cluster viene restituito lo stesso stato ogni volta che viene eseguito.

Ad esempio, se uno script personalizzato viene installata un'applicazione in /usr/local/bin in eseguire la prima, quindi a ogni esecuzione successive lo script controllare se l'applicazione esiste già nella posizione /usr/local/bin prima di procedere con un'altra procedura per lo script.

### <a name="bPS5"></a>Assicurarsi di disponibilità di architettura cluster

Cluster HDInsight basati su Linux include due nodi testa attivi all'interno del cluster e script azioni sono è stato eseguito per entrambi i nodi. Se i componenti che si installa prevedono un solo nodo principale, è necessario progettare uno script che verrà installate solo il componente su uno dei due nodi testa del cluster.

> [AZURE.IMPORTANT] Servizi predefiniti installati come parte di HDInsight sono progettati per avere esito negativo su tra i due nodi di testa, in base alle esigenze, tuttavia questa funzionalità non è estesa ai componenti personalizzati installati tramite script zioni. Se sono necessari i componenti installati tramite un'azione script altamente disponibile, è necessario implementare un proprio meccanismo di failover che usa due nodi di testa disponibili.

### <a name="bPS6"></a>Configurare i componenti personalizzati per l'utilizzo di archiviazione Blob Azure

Componenti installati nel cluster potrebbero avere una configurazione predefinita che utilizza l'archiviazione Hadoop Distributed File System (HDFS). HDInsight utilizza lo spazio di archiviazione Blob Azure (WASB) come spazio di archiviazione predefinito. In questo modo un sistema di file compatibili con HDFS che rende i dati anche se il cluster viene eliminato. È necessario configurare componenti installare e per usare WASB anziché HDFS.

Ad esempio, le operazioni seguenti consente di copiare il file giraph examples.jar dal file system locale al WASB:

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Scrivere le informazioni STDOUT e STDERR

Le informazioni scritte STDOUT e STDERR durante l'esecuzione di script ha eseguito l'accesso e possono essere visualizzate tramite l'interfaccia web Ambari.

> [AZURE.NOTE] Ambari saranno disponibili solo se il cluster è stato creato correttamente. Se si usa un'azione script durante la creazione di cluster e la creazione non riesce, vedere la sezione sulla risoluzione dei problemi [che personalizzare HDInsight cluster tramite script azione](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) per altri modi di accesso alle informazioni registrate.

La maggior parte dei pacchetti di installazione e utilità già informazioni verranno scritte STDOUT e STDERR, tuttavia è consigliabile aggiungere ulteriori opzioni di registrazione. Per inviare il testo da utilizzare STDOUT `echo`. Per esempio:

        echo "Getting ready to install Foo"

Per impostazione predefinita, `echo` invia la stringa in STDOUT. Per impostarlo su STDERR, aggiungere `>&2` prima `echo`. Per esempio:

        >&2 echo "An error occurred installing Foo"

Consente di reindirizzare le informazioni inviate a STDOUT (1, ossia predefinita in modo non elencato qui,) per STDERR (2). Per ulteriori informazioni sul reindirizzamento IO, vedere [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Per ulteriori informazioni sulla visualizzazione delle informazioni registrate da azioni di script, vedere [personalizzare HDInsight cluster tramite script azione](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Salvare i file in formato ASCII con terminazioni nuova riga

Script Bash devono essere archiviate in formato ASCII, con linee terminate da nuova riga. Se i file sono archiviati come UTF-8, che possono includere un ordine dei byte all'inizio del file o con terminazioni di CRLF, ossia comuni per gli editor di Windows, lo script avrà esito negativo con errori simili al seguente:

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Utilizzare la logica riprova per correggere gli errori temporanei

Durante il download dei file, l'installazione di pacchetti mediante apt get o altre azioni che la trasmissione di dati tramite internet, l'azione potrebbe non riuscire a causa degli errori di reti temporanei. Ad esempio, la risorsa remota che la comunicazione con potrebbe essere in corso il ripristino in un nodo di backup.

Per rendere lo script flessibili errori temporanei, è possibile implementare logica di ripetizione. Di seguito è illustrato un esempio di una funzione che verrà eseguito ai comandi passato a tale e (se il comando non riesce,) Ripetere fino a tre volte. Due secondi tra ogni Riprova.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Di seguito sono esempi di uso della funzione.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Metodi di supporto per script personalizzati

metodi di supporto di script azione sono utilità che è possibile usare durante la scrittura di script personalizzati. Questi sono definiti in [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)e possono essere inclusi in script tramite le operazioni seguenti:

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

In questo modo helper seguenti disponibili per l'utilizzo in uno script:

| Uso di supporto | Descrizione |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Download di un file dall'URL di origine per il percorso del file specificato. Per impostazione predefinita, sovrascriverà non un file esistente. |
| `untar_file TARFILE DESTDIR` | Estrae un file tar (utilizzando `-xf`,) nella directory di destinazione. |
| `test_is_headnode` | Se è stato eseguito su un nodo cluster testa, restituisce 1. in caso contrario, 0. |
| `test_is_datanode` | Se il nodo corrente è un nodo dati (lavoro), restituisce 1. in caso contrario, 0. |
| `test_is_first_datanode` | Se il nodo corrente è il primo nodo di dati (lavoro) (denominato workernode0), restituirà il valore 1; in caso contrario, 0. |
| `get_headnodes` | Restituisce il nome di dominio completo della headnodes del cluster. I nomi sono delimitati da virgole. In caso di errore viene restituita una stringa vuota. |
| `get_primary_headnode` | Ottiene il nome di dominio completo del headnode principale. In caso di errore viene restituita una stringa vuota. |
| `get_secondary_headnode` | Ottiene il nome di dominio completo del headnode secondario. In caso di errore viene restituita una stringa vuota. |
| `get_primary_headnode_number` | Ottiene il suffisso numerico del headnode principale. In caso di errore viene restituita una stringa vuota. |
| `get_secondary_headnode_number` | Ottiene il suffisso numerico del headnode secondario. In caso di errore viene restituita una stringa vuota. |

## <a name="commonusage"></a>Modelli di utilizzo comune

In questa sezione fornisce indicazioni sull'implementazione di alcuni dei modelli di utilizzo comuni che potrebbero verificarsi durante la scrittura di script personalizzato.

### <a name="passing-parameters-to-a-script"></a>Il passaggio di parametri a uno script

In alcuni casi, uno script può richiedere parametri. Ad esempio, potrebbe essere necessario la password dell'amministratore per il cluster per recuperare le informazioni dall'API REST Ambari.

Parametri per lo script noto come _parametri di posizione_e sono assegnati alle `$1` per il primo parametro `$2` per il secondo e in modo-on. `$0`contiene il nome dello script stesso.

Valori passati allo script come parametri devono essere racchiusi tra virgolette singole (') in modo che il valore passato è considerato come letterale e speciale non viene assegnato a parentesi come '!'.

### <a name="setting-environment-variables"></a>Impostazione delle variabili di ambiente

Impostazione di una variabile di ambiente viene eseguita mediante le operazioni seguenti:

    VARIABLENAME=value

Dove nomevariabile è il nome della variabile. Per accedere alla variabile dopo questa operazione, utilizzare `$VARIABLENAME`. Ad esempio, per assegnare un valore specificato da un parametro posizione come una variabile di ambiente denominata PASSWORD, usare:

    PASSWORD=$1

Quindi possibile utilizzare i successivo accesso alle informazioni `$PASSWORD`.

Nell'ambito dello script esistono solo le variabili di ambiente impostate all'interno di script. In alcuni casi, potrebbe essere necessario aggiungere variabili di ambiente larghezza sistema verranno mantenuta dopo il completamento script. In genere si tratta in modo che gli utenti di connettersi al cluster tramite SSH possono usare i componenti installati da uno script. È possibile eseguire questa operazione aggiungendo la variabile di ambiente `/etc/environment`. Ad esempio, il codice seguente aggiunge __HADOOP\_conferenza telefonica con\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accesso alle posizioni in cui sono memorizzati gli script personalizzati

A uno script utilizzati per personalizzare un cluster deve essere nell'account di spazio di archiviazione predefinito per il cluster o, se in un altro account di archiviazione, in un contenitore di sola lettura pubblico. Se lo script accede alle risorse si trova in un' posizione anche queste dovranno essere accessibile al pubblico (almeno pubblico sola lettura). È consigliabile, ad esempio scaricare un file per il cluster utilizzando `download_file`.

Archiviare il file in un account di archiviazione Azure accessibile dal cluster (ad esempio, l'account di archiviazione predefinito), fornirà accesso rapido, come questo spazio di archiviazione è all'interno della rete Azure.

### <a name="checking-the-operating-system-version"></a>Controllo della versione del sistema operativo

Versioni diverse di HDInsight si basano su versioni specifiche di Ubuntu. È possibile le differenze tra versioni del sistema operativo che è necessario verificare in uno script. Ad esempio, potrebbe essere necessario installare un file binario associato alla versione di Ubuntu.

Per verificare la versione del sistema operativo, utilizzare `lsb_release`. Ad esempio, di seguito viene illustrato come riferimento a un file tar diversa a seconda della versione del sistema operativo:

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Elenco di controllo per la distribuzione di un'azione script

Ecco i passaggi effettuati durante la preparazione per la distribuzione di questi script:

- Inserire i file che contengono script personalizzati in un percorso in cui si è accessibile dai nodi del cluster durante la distribuzione. Può trattarsi di una delle predefinito o altri account di archiviazione specificato al momento dell'installazione cluster o qualsiasi altro contenitore di spazio di archiviazione accessibili.

- Aggiungere controlli in script per assicurarsi che eseguono impotently, in modo che lo script può essere eseguito più volte sullo stesso nodo.

- Utilizzare un /tmp directory file temporanei per mantenere i file scaricati utilizzati dagli script e quindi li elimina dopo esecuzione script.

- Nel caso in cui sono stati modificati le impostazioni a livello del sistema operativo o file di configurazione del servizio Hadoop, può essere necessario riavviare servizi HDInsight in modo che sia possibile selezionare le eventuali impostazioni di livello di sistema operativo, ad esempio le variabili di ambiente impostare gli script.

## <a name="runScriptAction"></a>Come eseguire un'azione script

È possibile utilizzare le azioni script per personalizzare i cluster HDInsight tramite il portale di Azure, Azure PowerShell, modelli di Azure risorsa Manager (ARM) o HDInsight .NET SDK. Per ulteriori informazioni, vedere [come utilizzare script azione](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Esempi di script personalizzati

Microsoft offre script di esempio per installare componenti in un cluster di HDInsight. Gli script di esempio e istruzioni su come usarli sono disponibili i collegamenti seguenti:

- [Installare e usare tonalità nello HDInsight cluster](hdinsight-hadoop-hue-linux.md)
- [Installare e usare R nei cluster HDInsight Hadoop](hdinsight-hadoop-r-scripts-linux.md)
- [Installare e usare Solr sui HDInsight cluster](hdinsight-hadoop-solr-install-linux.md)
- [Installare e usare Giraph sui HDInsight cluster](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] I documenti in precedenza sono specifici di cluster basati su Linux HDInsight. Per gli script che funzionano con HDInsight basato su Windows, vedere [sviluppo di azione di Script con HDInsight (Windows)](hdinsight-hadoop-script-actions.md) o utilizzare i collegamenti disponibili nella parte superiore di ogni articolo.

##<a name="troubleshooting"></a>Risoluzione dei problemi

Errori che possono verificarsi quando si usa script sviluppato sono i seguenti:

__Error__: `$'\r': command not found`. Talvolta da `syntax error: unexpected end of file`.

_Causa_: questo errore si verifica quando le righe di uno script terminano con CRLF. Sistemi UNIX aspettano solo nuova riga come la fine della riga.

Più spesso questo problema si verifica quando lo script è stato creato in un ambiente di Windows, come CRLF è una linea comune per molti editor di testo in Windows per terminare.

_Risoluzione_: se si tratta di un'opzione nell'editor di testo, selezionare il formato Unix o nuova riga per le terminazioni di riga. È anche possibile utilizzare i comandi seguenti in un sistema Unix per modificare il carattere CRLF a una nuova riga:

> [AZURE.NOTE] I comandi seguenti sono approssimativamente in cambiano le terminazioni di linea CRLF alla nuova riga. Selezionare una basata sull'utilità disponibili nel sistema.

| Comando | Note |
| ------- | ----- |
| `unix2dos -b INFILE` | Il file originale verrà backup con un. Estensione BAK |
| `tr -d '\r' < INFILE > OUTFILE` | File di output conterrà una versione con solo le terminazioni di nuova riga |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Questa operazione modificherà il file direttamente senza creare un nuovo file |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | File di output contiene una versione con solo le terminazioni di nuova riga.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Causa_: questo errore si verifica quando lo script è stato salvato come UTF-8 con un segno di ordine Byte (DB).

_Soluzione_: salvare il file come ASCII o come UTF-8 senza una distinta base. È anche possibile utilizzare il comando seguente in un sistema Linux o Unix per creare un nuovo file senza la distinta base:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Per il comando precedente, sostituire __file di input__ con il file contenente la distinta base. __File di output__ deve essere un nuovo nome di file che contiene lo script senza distinta base.

## <a name="seeAlso"></a>Passaggi successivi

* Informazioni su come [personalizzare HDInsight cluster tramite script azione](hdinsight-hadoop-customize-cluster-linux.md)

* Utilizzare il [riferimento HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx) per altre informazioni sulla creazione di applicazioni .NET che gestiscono HDInsight

* Utilizzare l' [API REST HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) per imparare a usare resto per eseguire operazioni di gestione sui cluster HDInsight.
