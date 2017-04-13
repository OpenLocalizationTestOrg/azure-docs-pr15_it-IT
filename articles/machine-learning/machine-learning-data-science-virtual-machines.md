<properties
    pageTitle="Macchine virtuali di scienze di dati di Azure | Microsoft Azure"
    description="Impostare su una macchina virtuale scienza di dati"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Macchine virtuali di scienze di dati di Azure

Istruzioni, vedere di seguito che descrivono come configurare una macchina virtuale Azure e un SV Azure con il servizio SQL Server IPython blocco appunti. La macchina virtuale di Windows è configurata con supporto di strumenti, ad esempio blocco appunti IPython, Esplora archivi Azure e AzCopy, nonché altre utilità sono utili per i progetti di ricerca di scienze dati. Esplora archivi Azure e AzCopy, ad esempio, fornire agevolmente per caricare i dati allo spazio di archiviazione Azure dal computer locale o per scaricare nel computer locale dallo spazio di archiviazione. 

In questo menu forniti collegamenti ad argomenti che descrivono come configurare i diversi ambienti di scienze di dati utilizzati dal [Team dati scienza processo (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Diversi tipi di Azure macchine virtuali possono essere completato il provisioning e configurati per essere utilizzato come parte di un ambiente di scienze dati basate su cloud. La decisione su quale versione di macchina virtuale da utilizzare dipende il tipo e la quantità di dati modellare con apprendimento e la destinazione per i dati nel cloud. 

* Per istruzioni sulle domande da porsi una decisione, vedere [Pianificare dell'ambiente di Azure Machine Learning dati scienza](machine-learning-data-science-plan-your-environment.md). 
* Per un catalogo di alcuni scenari che possono verificarsi quando si esegue analitica avanzate, vedere [scenari per il processo di Analitica avanzati e tecnologia di apprendimento Azure](machine-learning-data-science-plan-sample-scenarios.md)

Sono disponibili due set di istruzioni:

* [Configurare una macchina virtuale Azure come un server di blocco appunti IPython per analitica avanzate](machine-learning-data-science-setup-virtual-machine.md) viene illustrato come effettuare il provisioning di una macchina virtuale Azure con blocco appunti IPython e altri strumenti utilizzati per eseguire una ricerca di scienze di dati per i casi in cui è possibile utilizzare un modulo di Azure spazio di archiviazione diversa da quella SQL per archiviare i dati.

* [Configurare una macchina virtuale Azure SQL Server come server di blocco appunti IPython per analitica avanzate](machine-learning-data-science-setup-sql-server-virtual-machine.md) viene illustrato come effettuare il provisioning di una macchina virtuale Server SQL Azure con blocco appunti IPython e altri strumenti utilizzati per eseguire una ricerca di scienze di dati per i casi in cui è possibile utilizzare un database SQL di memorizzazione dei dati.

Una volta provisioning e configurato, queste macchine virtuali possono essere utilizzati come server IPython blocco appunti per l'esplorazione e l'elaborazione dei dati e per altre attività necessarie in combinazione con Azure apprendimento e Team dati scienza processo (TDSP). Passaggi successivi del processo di scienze dei dati sono mappati nel [percorso di apprendimento TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) e possono includere procedure dettagliate che spostare i dati in SQL Server o HDInsight, elaborano e in tale posizione di esempio in preparazione per l'apprendimento dai dati di apprendimento di Azure computer.


> [AZURE.NOTE] Azure macchine virtuali di prezzo come **pagare solo per i quali si utilizza**. Per garantire che non sta fatturato quando non si utilizza la macchina virtuale, deve essere impostato su **arrestato (Deallocated)** dal [Portale classica Azure](http://manage.windowsazure.com/). Per istruzioni dettagliate o come rilasciare macchina virtuale, vedere [arresto e rilasciare macchina virtuale quando non è in uso](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 
