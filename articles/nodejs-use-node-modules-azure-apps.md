<properties
    pageTitle="Utilizzo dei moduli Node"
    description="Informazioni su come lavorare con i moduli Node quando si utilizza il servizio di App Azure o servizi Cloud."
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizzo di moduli Node con applicazioni Azure

In questo documento vengono fornite indicazioni sull'utilizzo di moduli Node con le applicazioni Azure. Vengono fornite indicazioni su assicurare che l'applicazione utilizza una versione specifica del modulo, nonché con moduli nativi Azure.

Se si ha già familiarità con con moduli Node **package.json** e **npm shrinkwrap.json** , i file seguenti è un breve riepilogo dei quali viene descritto in questo articolo:

* Servizio App Azure comprende file **package.json** e **npm shrinkwrap.json** e possono installare moduli basati su voci in questi file.
* Servizi Cloud Azure aspettarsi tutti i moduli installato sull'ambiente di sviluppo e la **nodo\_moduli** directory devono essere inclusi come parte del pacchetto di distribuzione. È possibile attivare il supporto per l'installazione di moduli che utilizzano **package.json** o **npm shrinkwrap.json** file sui servizi Cloud, ma è necessario Personalizzazione degli script predefinito utilizzato da servizio Cloud progetti. Per informazioni su come eseguire questa operazione, vedere [attività di avvio di Azure eseguire installa npm per evitare la distribuzione di moduli di nodo](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Azure macchine virtuali non vengono trattati in questo articolo, come l'esperienza di distribuzione in una macchina virtuale dipende dal sistema operativo ospitato da un computer virtuale.

##<a name="nodejs-modules"></a>Moduli Node

I moduli sono che può essere caricati pacchetti JavaScript che offrono funzionalità specifiche per l'applicazione. Un modulo è in genere installato lo strumento **npm** , ma alcune (ad esempio il modulo http) vengono forniti come parte del pacchetto Node core.

Quando sono installati moduli, queste vengono memorizzate nel **nodo\_moduli** directory nella radice della struttura di directory dell'applicazione. Tutti i moduli all'interno di **nodo\_moduli** directory gestisce il proprio **nodo\_moduli** directory che contiene i moduli da cui dipende e questo processo si ripete nuovamente per ogni modulo verso il basso catena delle dipendenze. In questo modo tutti i moduli per poter propri requisiti di versione per i moduli che dipende, tuttavia possono risultare in una struttura di directory di grandi dimensioni.

Quando si distribuisce il **nodo\_moduli** directory come parte dell'applicazione, aumenterà le dimensioni della distribuzione rispetto all'utilizzo di un file **package.json** o **npm shrinkwrap.json** . Tuttavia, garantisce che la versione dei moduli utilizzati nella produzione siano equivalenti a quelli utilizzati in fase di sviluppo.

###<a name="native-modules"></a>Moduli nativi

Durante la maggior parte dei moduli sono i file JavaScript semplicemente di testo normale, alcuni moduli sono immagini binarie specifico della piattaforma. I moduli vengono compilati al momento dell'installazione, in genere usando Python e nodo gyp. Poiché servizi Cloud Azure si basano sul **nodo\_moduli** cartella distribuito come parte del modulo applicazione, qualsiasi nativo incluso come parte dei moduli installati deve lavorare in un servizio cloud come è stato installato e compilato in un sistema di sviluppo di Windows.

Servizio di App Azure non supporta tutti i moduli nativi e può avere esito negativo alla compilazione di quelle ottenute con prerequisiti molto specifici. Mentre alcuni moduli popolari come MongoDB sono facoltative dipendenze native e funziona correttamente senza loro lavoro, un grande successo con quasi tutti i moduli nativi attualmente disponibili due possibili soluzioni:

* Eseguire **npm installare** in un computer Windows installati prerequisiti tutti nativo del modulo. Quindi distribuire creato **nodo\_moduli** cartella come parte di un'applicazione di servizio App Azure.
* Servizio App Azure possono essere configurato per eseguire bash personalizzati o script di shell durante la distribuzione, che consente l'opportunità di esecuzione di comandi personalizzati e configurare con precisione il modo in cui **npm installare** è in esecuzione. Per un video che mostra come eseguire questa operazione, vedere [Gli script di distribuzione di sito Web personalizzato con Kudu].

###<a name="using-a-packagejson-file"></a>Utilizzo di un file package.json

Il file **package.json** è un modo per specificare le dipendenze di livello superiore dell'applicazione richiede in modo che la piattaforma hosting possibile installare le dipendenze, anziché dover includere il **nodo\_pacchetti** cartella come parte della distribuzione. Dopo l'applicazione è stato distribuito, il comando **npm installare** viene utilizzato per analizzare il file **package.json** e installare tutte le dipendenze elencate.

Durante lo sviluppo, è possibile utilizzare il **-salvare**, **-Salva-dev**, o **-Salva facoltativo** parametri durante l'installazione dei moduli per aggiungere una voce del modulo a un file di **package.json** automaticamente. Per ulteriori informazioni, vedere [installare npm](https://docs.npmjs.com/cli/install).

Un potenziale problema con il file **package.json** è specifica solo la versione per le dipendenze di livello superiore. Ogni modulo installato potrebbe o potrebbe non essere disponibile la versione dei moduli che dipende e pertanto è possibile che possono ottenere rapidamente una catena di dipendenza diversa da quella usata in fase di sviluppo.

> [AZURE.NOTE]
> Quando si distribuisce al servizio di App Azure, se il file <b>package.json</b> fa riferimento a un modulo nativo durante la pubblicazione dell'applicazione tramite fra verrà visualizzato un messaggio di errore analogo al seguente:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>Utilizzo di un file di npm shrinkwrap.json

Il file **npm shrinkwrap.json** è un tentativo di risolvere le limitazioni di controllo delle versioni del modulo del file **package.json** . Mentre il file **package.json** include solo le versioni per i moduli di primo livello, il file **npm shrinkwrap.json** contiene i requisiti di versione per catena delle dipendenze modulo intero.

Quando l'applicazione è pronta per la produzione, è possibile bloccare-verso il basso requisiti di versione e creare un file di **npm shrinkwrap.json** tramite il comando **npm confezione** . Verrà utilizzato le versioni attualmente installate nel **nodo\_moduli** cartella e registrare questi al file **npm shrinkwrap.json** . Dopo l'applicazione è stata distribuita nell'ambiente di hosting, il comando **npm installare** viene utilizzato per analizzare il file **npm shrinkwrap.json** e installare tutte le dipendenze elencate. Per ulteriori informazioni, vedere [npm confezione](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Quando si distribuisce al servizio di App Azure, se il file <b>npm shrinkwrap.json</b> fa riferimento a un modulo nativo durante la pubblicazione dell'applicazione tramite fra verrà visualizzato un messaggio di errore analogo al seguente:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come utilizzare Node moduli con Azure, informazioni su come [specificare la versione Node], [compilare e distribuire un'app web Node]e [come usare l'interfaccia della riga di comando di Azure per Mac e Linux].

Per ulteriori informazioni, vedere il [Centro per sviluppatori Node](/develop/nodejs/).

[specificare la versione Node]: nodejs-specify-node-version-azure-apps.md
[Come utilizzare la riga di comando di Azure per Mac e Linux]: xplat-cli-install.md
[compilare e distribuire un'app web Node]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Script di distribuzione di sito Web personalizzato con Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
