
<properties
    pageTitle="Spazio di archiviazione Azure coerente: differenze e considerazioni | Microsoft Azure"
    description="Comprendere le differenze di archiviazione Azure e altre considerazioni sulla distribuzione di spazio di archiviazione Azure coerenti."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Spazio di archiviazione Azure coerente: differenze e considerazioni

Spazio di archiviazione Azure coerente è l'insieme di servizi cloud di spazio di archiviazione in Microsoft Azure pila. Spazio di archiviazione Azure coerente fornisce blob, tabella, coda e funzionalità di gestione di account con semantica di Azure coerente. In questo articolo riepilogate le differenze di spazio di archiviazione Azure coerente note con lo spazio di archiviazione di Azure. Inoltre, sono riepilogate altre considerazioni da tenere presenti quando si installa la versione di anteprima attualmente accessibili dello Stack di Microsoft Azure.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Differenze note

Questa versione Technical Preview di spazio di archiviazione Azure coerente non dispone di uniformità caratteristica 100% con Azure lo spazio di archiviazione per le versioni di API supportate. Nota differenze di caratteristiche sono i seguenti:

-   Alcuni tipi di account di archiviazione non sono ancora disponibili, ad esempio Standard\_RAGRS e Standard\_GRS.

-   Premio\_gli account di archiviazione LRS possono effettuare il provisioning. Non esistono tuttavia attualmente limiti delle prestazioni o garanzie.

-   Azure funzionalità di file non è ancora disponibile.

-   API di intervalli di ottenere pagina non supporta il recupero delle pagine che si differenziano tra snapshot di BLOB di pagine.

-   API di intervalli di ottenere pagina restituisce pagine in cui sono 4 KB di dettaglio.

-   Chiavi partizione e riga in implementazione di tabella di archiviazione Azure coerenti sono ogni limitata a 400 caratteri (vale a dire 800 byte).

-   I nomi BLOB l'implementazione del servizio di Azure coerente dello spazio di archiviazione Blob sono limitati a 880 caratteri (vale a dire 1760 byte).

-   Implementazione dell'archiviazione Azure coerente tenant lo spazio di archiviazione dati di utilizzo dei report fornisce contatori l'uso dello spazio di archiviazione che sono uguali a quelli disponibili nelle Azure, con la stessa semantica e unità. Attualmente, tuttavia, l'indicatore di utilizzo di transazioni di archiviazione non include le transazioni IaaS e l'indicatore di utilizzo di trasferimento dei dati non viene fatta distinzione l'utilizzo della larghezza di banda per il traffico di rete interna o esterna a un'area dello Stack di Azure.

-   Esistono alcune differenze nell'ambito di funzionalità per la gestione di archiviazione. È ad esempio, non è possibile modificare il tipo di account o disporre domini personalizzati. Inoltre, è disponibile solo la coerenza API a livello di premio\_LRS tipo di account di archiviazione.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

-   **Test.** Non distribuire l'archiviazione Azure coerenti in ambienti di produzione che utilizzano la versione corrente di Microsoft Azure Stack Technical Preview. Questa versione è destinata solo ai fini della valutazione in un ambiente di prova.

-   **Le prestazioni**. La versione di Microsoft Azure Stack Technical Preview di spazio di archiviazione Azure coerente non è completamente ottimizzare le prestazioni.

-   **Scalabilità.** La versione di Microsoft Azure Stack Technical Preview di spazio di archiviazione Azure coerente non è ottimizzata per scalabilità.

## <a name="version-support-considerations"></a>Considerazioni relative al supporto versione

Le versioni seguenti sono supportate in questa versione di anteprima di Azure coerente dello spazio di archiviazione:

> Raccolta di Client di archiviazione Azure: [Microsoft Azure lo spazio di archiviazione 6. x .NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Servizi di dati di archiviazione Azure: [04/2015/05 API REST versione](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Servizi di gestione di archiviazione Azure: [Anteprima 01 di 05 2015](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Passaggi successivi

-   [Introduzione all'archiviazione coerente Azure](azure-stack-storage-overview.md)
