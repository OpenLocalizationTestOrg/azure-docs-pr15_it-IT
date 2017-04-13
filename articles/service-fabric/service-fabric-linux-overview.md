<properties
   pageTitle="Servizio Azure tessuti su Linux | Microsoft Azure"
   description="Servizio tessuti cluster supportano Linux e Java, ovvero sarà possibile distribuire e applicazioni di servizio tessuti host scritte in Java e c# su Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/26/2016"
   ms.author="SubramaR"/>

# <a name="service-fabric-on-linux"></a>Servizio tessuti su Linux

L'anteprima del servizio tessuti su Linux consente di creare, distribuire e gestire applicazioni disponibile e scalabile su Linux esattamente come si farebbe in Windows. Le strutture di tessuti servizi (Services affidabile e operatori affidabile) sono disponibili in linguaggio su Linux oltre c# (.NET Core).  È anche possibile creare [servizi eseguibile guest](service-fabric-deploy-existing-app.md) con qualsiasi lingua o framework. Inoltre, l'anteprima supporta anche orchestrazione contenitori Docker. I contenitori docker supporti l'esecuzione di eseguibili guest o servizi servizio tessuti nativi, che utilizzano il Framework servizio tessuti.

Servizio tessuti su Linux corrisponde livello concettuale al servizio tessuti in Windows (ad eccezione dei specifiche del sistema operativo e il supporto del linguaggio di programmazione). In questo modo, la maggior parte della [documentazione esistente](http://aka.ms/servicefabricdocs) verrà applicata nel consentono di acquisire familiarità con la tecnologia.

> [AZURE.VIDEO service-fabric-linux-preview]

## <a name="supported-operating-systems-and-programming-languages"></a>Sistemi operativi supportati e linguaggi di programmazione

Anteprima limitato supporta la creazione di cluster di sviluppo di una casella oltre cluster più computer in esecuzione Ubuntu Server 16.04 Azure. L'anteprima supporta operatori affidabile e le strutture di servizi senza informazioni sullo stato affidabili in Java e c# oltre eseguibili guest e coordinazione contenitori Docker.  

>[AZURE.NOTE] Raccolte affidabile non sono ancora supportate in Linux. Non sono supportati cluster solo risultino - solo una casella o Linux Azure cluster più computer sono supportate nel riquadro di anteprima.

## <a name="supported-tooling"></a>Strumenti supportati

L'anteprima supporta l'interazione con i cluster tramite CLI Azure. Per gli sviluppatori di linguaggio, integrazione con Eclisse e Yeoman viene fornito con Eclisse supportati Linux e OSX. L'integrazione di OSX utilizza una VM Linux roba da smanettoni tramite vagrant. Per gli sviluppatori c#, l'integrazione con Yeoman viene fornito per generare i modelli di applicazione.

## <a name="next-steps"></a>Passaggi successivi


1. Acquisire familiarità con il framework di programmazione [Affidabile operatori](service-fabric-reliable-actors-introduction.md) e [Servizi affidabili](service-fabric-reliable-services-introduction.md) .

2. [Preparare l'ambiente di sviluppo su Linux](service-fabric-get-started-linux.md)

3. [Preparare l'ambiente di sviluppo in OS x](service-fabric-get-started-mac.md)

4. [Creare un'applicazione servizio tessuti linguaggio su Linux](service-fabric-create-your-first-linux-application-with-java.md)
