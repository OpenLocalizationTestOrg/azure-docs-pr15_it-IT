<properties 
    pageTitle="Come installare Apache Qpid Proton-C in un VM Linux | Microsoft Azure"
    description="Come creare una macchina virtuale Linux CentOS utilizzando macchine virtuali di Azure e su come creare e installare la libreria Apache Qpid Proton-C."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/29/2016"
    ms.author="sethm" />

# <a name="install-apache-qpid-proton-c-on-an-azure-linux-vm"></a>Installare Apache Qpid Proton-C in un supporto virtuale Linux Azure

[AZURE.INCLUDE [service-bus-selector-amqp](../../includes/service-bus-selector-amqp.md)]

In questa sezione viene illustrato come creare una macchina virtuale Linux CentOS utilizzando macchine virtuali di Azure e su come scaricare, creare e installare la libreria Apache Qpid Proton-C insieme alle associazioni di lingua Python e PHP. Dopo aver completato la procedura seguente, sarà possibile eseguire gli esempi di Python e PHP inclusi in questa Guida.

Il primo passaggio viene eseguito tramite il [portale classica Azure][]. Lo screenshot seguente mostra come è stata creata una macchina virtuale CentOS denominato "Luca centos":

![Proton in un supporto virtuale Linux Azure][0]

Dopo il provisioning, il portale vengono visualizzate le operazioni seguenti:

![Proton in un supporto virtuale Linux Azure][1]

Per accedere al computer, è necessario conoscere la porta dell'endpoint per SSH. È possibile ottenere questo valore da [Azure portale classica][] selezionando la macchina virtuale appena creata e facendo clic sulla scheda **i punti finali** . Lo screenshot seguente mostra che il pubblica porta SSH per il computer sia 57146.

![Proton in un supporto virtuale Linux Azure][2]

È ora possibile connettersi al computer utilizzando SSH. In questo esempio viene utilizzato lo strumento PuTTY, come l'immagine riportata di seguito:

![Proton in un supporto virtuale Linux Azure][3]

Per le app Python e PHP, in questo esempio utilizza le librerie di client Proton da Apache. Queste librerie sono disponibili per il download da [http://qpid.apache.org/download.html](http://qpid.apache.org/download.html). Il file Leggimi nel pacchetto di distribuzione illustra i passaggi necessari per installare le dipendenze e generare Proton. Di seguito è riportato un riepilogo dei passaggi:

1.  Modificare il file di configurazione slurp (/ etc/yum.conf) e commento l'esclusione di aggiornamenti per ottenere intestazioni kernel (\# escludere = kernel\*). Questo è necessario installare il compilatore gcc.

2.  Installare i pacchetti prerequisiti:

    ```
    # required dependencies 
    yum install gcc cmake libuuid-devel
    
    # dependencies needed for ssl support
    yum install openssl-devel
    
    # dependencies needed for bindings
    yum install swig python-devel ruby-devel php-devel java-1.6.0-openjdk
    
    # dependencies needed for python docs
    yum install epydoc
    ```

1.  Scaricare la raccolta di Proton:

    ```
    [azureuser@this-user ~]$ wget http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    --2016-04-17 14:45:03--  http://apache.panu.it/qpid/proton/0.9/qpid-proton-0.9.tar.gz
    Resolving apache.panu.it (apache.panu.it)... 81.208.22.71
    Connecting to apache.panu.it (apache.panu.it)|81.208.22.71|:80... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 868226 (848K) [application/x-gzip]
    Saving to: ‘qpid-proton-0.9.tar.gz’
    
    qpid-proton-0.9.tar.gz                               
    
    100%[====================================================================================================================>] 847.88K   102KB/s    in 8.4s    
    
    2016-04-17 14:45:12 (101 KB/s) - ‘qpid-proton-0.9.tar.gz’ saved [868226/868226]
    ```

1.  Estrarre il codice Proton da tale archivio di distribuzione:

    ```
    tar xvfz qpid-proton-0.9.tar.gz
    ```

1.  Creare e installare il codice utilizzando la procedura seguente, acquisita dal file Leggimi:

    ```
    From the directory where you found this README file:    
    
    mkdir build cd build
            
    # Set the install prefix. You may need to adjust depending on your      
    # system.       
    cmake -DCMAKE\_INSTALL\_PREFIX=/usr ..
            
    # Omit the docs target if you do not wish to build or install       
    # documentation.        
    make all docs
            
    # Note that this step will require root privileges.     
    make install
    ```

Dopo aver completato la procedura seguente, Proton è installato nel computer e pronto per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

Pronti per altre informazioni? Visitare il collegamento seguente:

- [Panoramica di servizio Bus AMQP][]

[Panoramica di servizio Bus AMQP]: service-bus-amqp-overview.md
[0]: ./media/service-bus-amqp-apache/amqp-apache-1.png
[1]: ./media/service-bus-amqp-apache/amqp-apache-2.png
[2]: ./media/service-bus-amqp-apache/amqp-apache-3.png
[3]: ./media/service-bus-amqp-apache/amqp-apache-4.png

[Portale classica Azure]: http://manage.windowsazure.com


