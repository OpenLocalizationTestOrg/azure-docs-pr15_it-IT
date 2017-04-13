<properties
   pageTitle="In locale, monitorare e diagnosticare servizi scritti con Azure servizio tessuti | Microsoft Azure"
   description="Informazioni su come eseguire il monitoraggio e diagnosticare i servizi scritti tramite Microsoft Azure servizio tessuti in un computer di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorare e diagnosticare i servizi di una configurazione di sviluppo di computer locale


> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
- [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

Monitoraggio, individuazione, la diagnosi e risoluzione dei problemi Consenti per continuare con un impatto minimo per l'esperienza utente dei servizi. Monitoraggio e diagnostica è importanti in un ambiente di produzione distribuito effettivo. L'adozione di un modello simile durante lo sviluppo di servizi garantisce che la pipeline di diagnostica funziona quando si sposta in un ambiente di produzione. Servizio tessuti semplifica l'individuazione di sviluppatori di servizi per implementare diagnostica utilizzabili diretta tra le impostazioni di sviluppo locale solo computer e le impostazioni di cluster di produzione reali.


## <a name="debugging-service-fabric-java-applications"></a>Il debug di applicazioni di servizio tessuti Java

Per le applicazioni Java, sono disponibili [più Framework di registrazione](http://en.wikipedia.org/wiki/Java_logging_framework) . Poiché `java.util.logging` è l'opzione predefinita con JRE, viene usato anche per gli [esempi di codice in github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  La discussione seguente viene illustrato come configurare il `java.util.logging` framework. 
 
Utilizzo java.util.logging è possibile reindirizzare i registri delle applicazioni in memoria, flussi di output, file di console o sockets. Per ognuna di queste opzioni sono disponibili gestori predefiniti già specificati in framework. È possibile creare un `app.properties` file per configurare il gestore di file per l'applicazione reindirizzare tutti i log in un file locale. 

Frammento di codice seguente contiene un esempio di configurazione: 

```java 
handlers = java.util.logging.FileHandler
 
java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

La cartella a cui punta la `app.properties` file deve essere presente. Dopo la `app.properties` file viene creato, è necessario modificare anche lo script di punto di ingresso `entrypoint.sh` nel `<applicationfolder>/<servicePkg>/Code/` cartella in cui impostare la proprietà `java.util.logging.config.file` a `app.propertes` file. La voce dovrebbe essere simile il frammento di codice seguente:

```sh 
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```
 
 
Questa configurazione genera registri raccolti in modo rotazione in `/tmp/servicefabric/logs/`. La **%u** e **%g** consentono la creazione di tutti i file desiderati, con i nomi di file mysfapp0.log, mysfapp1.log e così via. Per impostazione predefinita se non è configurato in modo esplicito alcun gestore, il gestore di console è registrato. Una possibile visualizzare i registri nel Registro di sistema in /var/log/syslog.
 
Per ulteriori informazioni, vedere gli [esempi di codice in github](http://github.com/Azure-Samples/service-fabric-java-getting-started).  



## <a name="next-steps"></a>Passaggi successivi
Il codice di analisi stesso aggiunto all'applicazione funziona anche con i test di diagnostica dell'applicazione in un cluster di Azure. Consultare i seguenti articoli discutere le diverse opzioni per gli strumenti che descrivono come è possibile configurare la.
* [Come raccogliere i registri con Azure diagnostica](service-fabric-diagnostics-how-to-setup-lad.md)
