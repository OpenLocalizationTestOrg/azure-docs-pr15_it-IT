<properties 
    pageTitle="Come utilizzare io.js con Azure App servizio Web Apps" 
    description="Informazioni su come usare un'app web nel servizio App Azure con io.js." 
    services="app-service\web" 
    documentationCenter="nodejs" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="nodejs" 
    ms.topic="article" 
    ms.date="08/11/2016"
    ms.author="robmcm" />

# <a name="how-to-use-iojs-with-azure-app-service-web-apps"></a>Come utilizzare io.js con Azure App servizio Web Apps

Impostazioni generali di divisione nodo [io.js] caratteristiche diverse differenze progetto Node del Joyent, incluso un modello di governance più aperto, un ciclo di rilascio più veloce e un'alternativa più veloce adozione di nuove funzionalità sperimentazione JavaScript.

Mentre [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714) Web Apps ha numero di versioni Node preinstallato, nonché per un file binario Node fornito dall'utente. In questo articolo vengono descritti due metodi per consentire l'uso di io.js nella App servizio Web App: l'uso di uno script di distribuzione "Extended", che consente di configurare automaticamente Azure per utilizzare l'ultima versione di io.js disponibili, come il caricamento manuale di un io.js binario. 

<a id="deploymentscript"></a>
## <a name="using-a-deployment-script"></a>Utilizzo di uno Script di distribuzione

Durante la distribuzione di un'app di Node App servizio Web Apps viene eseguito un numero di comandi small per assicurarsi che l'ambiente sia configurato correttamente. Usa uno script di distribuzione, questo processo può essere personalizzato per includere il download e la configurazione di io.js.

[Io.js Script di distribuzione](https://github.com/felixrieseberg/iojs-azure) è disponibile in GitHub. Per abilitare io.js in un'applicazione web, è sufficiente copiare **.deployment**, **Deploy** e **IISNode.yml** nella radice della cartella dell'applicazione e distribuire applicazioni Web.  

Il primo file, **.deployment**indica Web Apps per l'esecuzione **Deploy** durante la distribuzione. Questo script viene eseguito tutti i passaggi comune per un'applicazione Node, ma anche scaricate l'ultima versione di io.js. Infine, **IISNode.yml** Configura Web Apps per utilizzare solo il io.js scaricato binario anziché un binario Node preinstallato.

> [AZURE.NOTE] Per aggiornare il file binario io.js usati, solo ridistribuire l'applicazione - lo script verrà scaricate una nuova versione di io.js a ogni singolo che viene distribuita.

<a id="manualinstallation"></a>
## <a name="using-manual-installation"></a>Con l'installazione manuale

Installazione manuale di una versione io.js personalizzato include solo due passaggi. Scaricare innanzitutto **positivi x64** binario direttamente dalla [distribuzione io.js]. Obbligatorio sono due file - **iojs.exe** e **iojs.lib**. Salvare entrambi i file in una cartella all'interno di un'applicazione web, ad esempio in **bin/iojs**.

Per configurare Web Apps per utilizzare **iojs.exe** invece di una versione nodo preinstallata, creare un file **IISNode.yml** nella radice dell'applicazione e aggiungere la riga seguente.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## <a name="next-steps"></a>Passaggi successivi

In questo articolo che è stato illustrato come utilizzare io.js con App servizio Web Apps, utilizzando sia fornito gli script di distribuzione installazione anche come manuale. 

> [AZURE.NOTE] IO.js in fase di sviluppo piena e aggiornate più spesso di Node. Una serie di moduli Node potrebbe non funzionare con io.js -, consultare [io.js su GitHub] per la risoluzione dei problemi.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

[IO.js]: https://iojs.org
[distribuzione IO.js]: https://iojs.org/dist/
[IO.js in GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 