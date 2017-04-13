<properties
   pageTitle="Guida introduttiva a servizi affidabili | Microsoft Azure"
   description="Introduzione alla creazione di un'applicazione di Microsoft Azure servizio tessuti con e senza stati servizi."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/26/2016"
   ms.author="vturecek"/>

# <a name="get-started-with-reliable-services"></a>Guida introduttiva a servizi affidabili

> [AZURE.SELECTOR]
- [C# in Windows](service-fabric-reliable-services-quick-start.md)
- [Linguaggio su Linux](service-fabric-reliable-services-quick-start-java.md)

In questo articolo vengono illustrate le nozioni di base di Azure tessuti affidabile servizi e viene descritto come creare e distribuire un'applicazione di servizio affidabile semplice scritta in linguaggio.

## <a name="installation-and-setup"></a>Installazione e configurazione
Prima di iniziare, accertarsi di avere l'ambiente di sviluppo di servizio tessuti configurare nel computer in uso.
Se è necessario configurare l'account, passare alla [Guida introduttiva su Mac](service-fabric-get-started-mac.md) o [Guida introduttiva su Linux](service-fabric-get-started-linux.md).

## <a name="basic-concepts"></a>Concetti di base
Per iniziare con i servizi di affidabile, è necessario conoscere alcuni concetti di base:

 - **Tipo di servizio**: si tratta di implementazione del servizio. Viene definito per la classe è scrivere che si estende `StatelessService` e qualsiasi altro codice o dipendenze usate al suo interno, oltre a un nome e un numero di versione.

 - **Istanza di servizio denominata**: per eseguire il servizio, si creano istanze denominate del tipo di servizio, molto come si creano istanze di oggetti di un tipo di classe. Istanze del servizio sono in effetti istanze di oggetto della classe servizio che si scrive. 

 - **Host del servizio**: le istanze del servizio non è creare necessarie per l'esecuzione all'interno di un host. L'host del servizio è solo un processo in cui è possibile eseguire istanze del servizio.

 - **La registrazione del servizio**: registrazione riunisce tutti gli elementi tutti. Con il runtime del servizio tessuti in un host del servizio per consentire l'infrastruttura di servizio creare le istanze di questa per l'esecuzione, è necessario registrare il tipo di servizio.  

## <a name="create-a-stateless-service"></a>Creare un servizio senza informazioni sullo stato

Prima di tutto creare una nuova applicazione di servizio tessuti. Service tessuti SDK per Linux include un Yeoman generatore per fornire supporto temporaneo per un'applicazione di servizio tessuti con un servizio senza informazioni sullo stato. Avviare eseguendo Yeoman seguente comando:

```bash
$ yo azuresfjava
```

Seguire le istruzioni per creare un **Servizio senza informazioni sullo stato affidabile**. Per questa esercitazione, assegnare un nome applicazione "HelloWorldApplication" e il servizio "HelloWorld". Il risultato sarà incluso directory per il `HelloWorldApplication` e `HelloWorld`.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>Implementazione del servizio

Aprire **HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**. Questa classe consente di definire il tipo di servizio e supporti l'esecuzione di codice. L'API del servizio fornisce due punti di ingresso per il codice:

 - Un metodo di punto di ingresso aperte, denominato `runAsync()`, in cui è possibile iniziare l'esecuzione di qualsiasi carichi di lavoro, tra cui carichi di lavoro di calcolo di lunga.

```java
@Override
protected CompletableFuture<?> runAsync() {
    ...
}
```

 - Punto di ingresso comunicazioni nel punto in cui è possibile collegare la pila di comunicazione scelto. Si tratta in cui è possibile iniziare la ricezione delle richieste da utenti e altri servizi.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

In questa esercitazione, è dedicata la `runAsync()` metodo punto di ingresso. Si tratta in cui è possibile iniziare immediatamente eseguire il codice.

### <a name="runasync"></a>RunAsync

La piattaforma chiama questo metodo quando un'istanza di un servizio viene inserito e pronto per l'esecuzione. Ciclo di apertura/chiusura di un'istanza del servizio può verificarsi più volte per tutta la durata del servizio nel suo insieme. Questo problema può verificarsi per diversi motivi, tra cui:

- Il sistema sposta le istanze del servizio di bilanciamento del carico risorsa.
- Si verificano nel codice.
- Applicazione o nel sistema viene aggiornato.
- L'hardware sottostante si verifica un'interruzione.

Questa orchestrazione verrà gestita dal servizio tessuti per mantenere il servizio disponibilità e non bilanciate correttamente.

#### <a name="cancellation"></a>Annullamento

È fondamentale che il codice in `runAsync()` possibile interrompere l'esecuzione quando una notifica tramite servizio tessuti. Il `CompletableFuture` restituito da `runAsync()` viene annullata quando servizio tessuti richiede il servizio per interrompere l'esecuzione. Nell'esempio seguente viene illustrato come gestire un evento di annullamento: 

```java
    @Override
    protected CompletableFuture<?> runAsync() {

        CompletableFuture<?> completableFuture = new CompletableFuture<>();
        ExecutorService service = Executors.newFixedThreadPool(1);
        
        Future<?> userTask = service.submit(() -> {
            while (!Thread.currentThread().isInterrupted()) {
                try
                {
                   logger.log(Level.INFO, this.context().serviceName().toString());
                   Thread.sleep(1000);
                }
                catch (InterruptedException ex)
                {
                    logger.log(Level.INFO, this.context().serviceName().toString() + " interrupted. Exiting");
                    return;
                }
            }
         });
 
        completableFuture.handle((r, ex) -> {
            if (ex instanceof CancellationException) {
                userTask.cancel(true);
                service.shutdown();
            }
            return null;
        });
 
        return completableFuture;
   }
``` 

### <a name="service-registration"></a>Registrazione del servizio

Tipi di servizio devono essere registrati con il runtime del servizio tessuti. Il tipo di servizio sia definito nel `ServiceManifest.xml` e la classe di servizio implementata `StatelessService`. Nel punto di ingresso principale processo viene eseguita la registrazione del servizio. In questo esempio è il punto di ingresso principale processo `HelloWorldServiceHost.java`:

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    } 
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration: {0}", ex.toString());
        throw ex;
    }
}
```

## <a name="run-the-application"></a>Eseguire l'applicazione

Il Yeoman supporto temporaneo include uno script gradle per generare l'applicazione e bash script per distribuire e annullare-distribuire l'applicazione. Per eseguire l'applicazione, creare innanzitutto l'applicazione con gradle:

```bash
$ gradle
```

Questo produrranno un pacchetto dell'applicazione di servizio tessuti che può essere distribuito mediante servizio tessuti Azure CLI. Lo script install.sh contiene i comandi di Azure CLI necessari per distribuire il pacchetto dell'applicazione. È sufficiente eseguire lo script install.sh per distribuire:

```bask
$ ./install.sh
```
