<properties
 pageTitle="Formati per i servizi cloud | Microsoft Azure"
 description="Elenca le dimensioni di macchina virtuale diversa (e ID) per i ruoli web e di lavoro di servizio cloud Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="10/27/2016"
 ms.author="adegeo"/>

# <a name="sizes-for-cloud-services"></a>Formati per i servizi Cloud

In questo argomento descrive le opzioni per le istanze di ruolo di servizio Cloud (ruoli web ruoli e lavoro) e le dimensioni massime disponibili. Fornisce inoltre considerazioni sulla distribuzione da tenere presenti quando si prevede di usare queste risorse. Le dimensioni di ogni hanno un ID inserito nel [file di definizione del servizio](cloud-services-model-and-package.md#csdef).

Servizi cloud corrisponde a uno dei numerosi tipi di risorse di elaborazione offerte da Azure. Fare clic [qui](cloud-services-choose-me.md) per ulteriori informazioni sui servizi Cloud.

> [AZURE.NOTE]Per visualizzare i limiti di Azure correlati, vedere [abbonamento Azure e limiti del servizio, quote e vincoli](../azure-subscription-service-limits.md)

## <a name="sizes-for-web-and-worker-role-instances"></a>Dimensioni per istanze del ruolo web e di lavoro

Sono disponibili più dimensioni standard per scegliere da in Azure. Considerazioni per alcuni di questi formati includono:

* Macchine virtuali di serie D sono progettate per l'esecuzione di applicazioni che richiedono più grande potenza e prestazioni del disco temporaneo. Macchine virtuali di serie D forniscono processori più veloci, un rapporto di memoria a core superiore e un'unità (SSD) per il disco temporaneo. Per informazioni dettagliate, vedere l'annuncio sul blog di Azure, [Nuove dimensioni macchina virtuale D serie](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).

* Serie Dv2, una versione successiva per la serie D originale, caratteristiche più potente CPU. La CPU Dv2 serie è circa 35% più veloce la CPU D serie. Si basa sull'ultima generazione 2,4 GHz Intel Xeon® E5-2673 v3 processore (Haswell) e con Intel Turbo Preamplificazione grazie alla tecnologia di 2.0, può essere eseguita fino a 3.1 GHz. La serie di Dv2 ha le stesse configurazioni di memoria e disco la serie D.

*   Macchine virtuali di serie G offrono la maggior parte della memoria ed eseguire sugli host che dispongono di processori della famiglia Intel Xeon E5 V3.

*   È possibile distribuire macchine virtuali di serie su diversi tipi di hardware e processori. Le dimensioni sono limitata, in base a hardware, per offrire prestazioni processore coerente per l'istanza in esecuzione, indipendentemente dalla hardware in che viene distribuito. Per determinare l'hardware in cui viene distribuita questo dimensioni, eseguire una query hardware virtuale dall'interno della macchina virtuale.

*   La dimensione A0 è eccessivo sottoscritta hardware fisico. Per questo dimensioni specifiche, altri distribuzioni possono compromettere le prestazioni del carico di lavoro in esecuzione. Le prestazioni relative viene descritta di seguito come previsto previsto, soggetto a una variabilità approssimativa del 15%.


La dimensione della macchina virtuale influenza i prezzi. La dimensione influenza anche la capacità di elaborazione, memoria e spazio di archiviazione della macchina virtuale. Spazio di archiviazione costi vengono calcolati separatamente in base alle pagine nella finestra account di archiviazione. Per informazioni dettagliate, vedere [I dettagli prezzi macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Prezzi lo spazio di archiviazione Azure](https://azure.microsoft.com/pricing/details/storage/). 


Le considerazioni seguenti potrebbero essere utili per scegliere una dimensione:


* Le dimensioni A8 A11 e H serie sono noto anche come *istanze complesse*. L'hardware che esegue queste dimensioni progettato e ottimizzato per complesse e applicazioni di rete che richiede significative attività, tra cui le massime prestazioni informatiche (HPC) cluster simulazioni, modellazione e le applicazioni. La serie di A8 A11 utilizza Intel Xeon E5-2670 @ 2,6 GHZ e la serie di H utilizza Intel Xeon E5-2667 v3 @ 3,2 GHz. Per informazioni dettagliate e considerazioni sull'uso di questi formati, vedere [informazioni sulla macchine virtuali di serie di una serie di H e complesse](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md). 

* Serie Dv2, serie D, G-serie sono ideali per applicazioni che richiedono CPU più veloci, locale migliori disco prestazioni o disporre di versioni successive richieste di memoria.  Offrono una potente combinazione per molte applicazioni di livello aziendale.

*   Alcuni degli host fisici in Azure data Center non possono supportare dimensioni maggiori di macchina virtuale, ad esempio A5: A11. Di conseguenza, viene visualizzato il messaggio di errore **Impossibile configurare macchina virtuale {nome computer}** o **Impossibile creare macchina virtuale {nome computer}** durante il ridimensionamento di una macchina virtuale esistente in una nuova dimensione; creazione di una nuova macchina virtuale in una rete virtuale creata prima 16 aprile 2013; o l'aggiunta di una nuova macchina virtuale a un servizio cloud esistente. Vedere [errore: "Non è possibile configurare macchina virtuale"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) al forum di supporto per soluzioni alternative per ogni scenario di distribuzione.  

* L'abbonamento potrebbe anche limitare il numero di core che è possibile distribuire nelle famiglie determinate dimensioni. Per aumentare la quota, contattare il supporto di Azure.


## <a name="performance-considerations"></a>Considerazioni sulle prestazioni

Abbiamo creato il concetto del Azure calcolare unità (ACU) per fornire un modo per confrontare le prestazioni di elaborazione (CPU) tra gli SKU di Azure. Questo consente di identificare facilmente che probabilmente soddisfare le esigenze di prestazioni SKU.  ACU è attualmente standardizzato su una piccola (Standard_A1) macchine Virtuali viene quindi 100 e tutti gli altri SKU rappresentano circa come molto più rapidamente che SKU supporti l'esecuzione di un benchmark standard. 

>[AZURE.IMPORTANT] Il ACU è solo un'istruzione.  I risultati per il carico di lavoro possono variare. 

<br>

|SKU famiglia |ACU/Core |
|---|---|
|[Standard_A0](#a-series)   |50 |
|[Standard_A1-4](#a-series) |100 |
|[Standard_A5 7](#a-series) |100 |
|[A8 A11](#a-series)    |225 *|
|[D1 14](#d-series) |160 |
|[D1 15v2](#dv2-series) |210 - 250 *|
|[G1-5](#g-series)  |180 - 240 *|
|[H](#h-series) |290 - 300 *|

ACUs contrassegnati con un * utilizzare la tecnologia Intel® Turbo per aumentare la frequenza di CPU e un aumento delle prestazioni.  La quantità della Preamplificazione può variare in base alla dimensione macchine Virtuali, carico di lavoro e altri carichi di lavoro in esecuzione nello stesso host.

## <a name="size-tables"></a>Tabelle di dimensioni

Le tabelle seguenti mostrano le dimensioni e le capacità che forniscono.

* Capacità di archiviazione verrà visualizzata nell'unità di GiB o 1024 ^ 3 byte. Quando il confronto dischi misurato in GB (1000 ^ 3 byte) a dischi misurati in GiB (1024 ^ 3) tenere presente che numeri capacità indicati in GiB potrebbero apparire più piccoli. Ad esempio 1023 GiB = 1098.4 GB

* Effettiva disco rigido è espressa in ingresso/uscita operazioni al secondo (IOPS) e s in MBps = 10 ^ 6 byte/sec.

* Dischi dati possono funzionare in modalità cache o non memorizzate nella cache. Operazione di disco dati memorizzati nella cache, la modalità cache host è impostata su **sola lettura** o **ReadWrite**.  Operazione disco di dati non memorizzate nella cache, la modalità cache host è impostata su **Nessuno**.

* Rete massima indica il numero massimo aggregato larghezza di banda allocata e assegnare a ogni tipo di macchine Virtuali. La larghezza di banda massima fornisce indicazioni per la selezione del tipo di macchine Virtuali corretto per garantire la capacità di rete adeguata sono disponibile. Quando si sposta tra bassa, Media, ad alta e molto elevato, la velocità aumenterà di conseguenza. Le prestazioni di rete effettivo dipende da diversi fattori, inclusi le impostazioni di rete dell'applicazione e carichi di lavoro e di rete.


## <a name="a-series"></a>Serie

| Dimensioni        | Core CPU | Memoria: GiB | Disco rigido locale: GiB | Dischi dati max | Velocità disco dati massima: IOPS | NIC max / della larghezza di banda di rete |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A0 | 1         | 0.768        | 20                    | 1              | 1 x 500              | 1 / bassa                   |
| Standard_A1 | 1         | 1,75         | 70                    | 2              | 2 x 500              | 1 / moderato              |
| Standard_A2 | 2         | 3,5 GB       | 135                   | 4              | 4 x 500              | 1 / moderato              |
| Standard_A3 | 4         | 7            | 285                   | 8              | 8 x 500              | 2 / elevato                  |
| Standard_A4 | 8         | 14           | 605                   | 16             | 16 x 500             | 4 / elevato                  |
| Standard_A5 | 2         | 14           | 135                   | 4              | 4 X 500              | 1 / moderato              |
| Standard_A6 | 4         | 28           | 285                   | 8              | 8 x 500              | 2 / elevato                  |
| Standard_A7 | 8         | 56           | 605                   | 16             | 16 x 500             | 4 / elevato                  |

## <a name="a-series---compute-intensive-instances"></a>Serie - istanze complesse

Per informazioni e considerazioni sull'uso di questi formati, vedere [informazioni sulla macchine virtuali di serie di una serie di H e complesse](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


| Dimensioni         | Core CPU | Memoria: GiB | Disco rigido locale: GiB | Dischi dati max | Velocità disco dati massima: IOPS | NIC max / della larghezza di banda di rete |
|--------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A8 * | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / elevato                  |
| Standard_A9 * | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / molto elevato             |
| Standard_A10 | 8         | 56           | 382                   | 16             | 16 x 500             | 2 / elevato                  |
| Standard_A11 | 16        | 112          | 382                   | 16             | 16 x 500             | 4 / molto elevato             |

* RDMA dotato di

## <a name="d-series"></a>Serie D


| Dimensioni         | Core CPU | Memoria: GiB | SSD locale: GiB | Dischi dati max | Velocità disco dati massima: IOPS | NIC max / della larghezza di banda di rete |
|--------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderato              |
| Standard_D2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / elevato                  |
| Standard_D3  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / elevato                  |
| Standard_D4  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / elevato                  |
| Standard_D11 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / elevato                  |
| Standard_D12 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / elevato                  |
| Standard_D13 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / elevato                  |
| Standard_D14 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / molto elevato             |

## <a name="dv2-series"></a>Serie di Dv2

| Dimensioni            | Core CPU | Memoria: GiB | SSD locale: GiB | Dischi dati max | Velocità disco dati massima: IOPS | NIC max / della larghezza di banda di rete |
|-----------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_D1_v2  | 1         | 3.5          | 50                   | 2              | 2 x 500              | 1 / moderato              |
| Standard_D2_v2  | 2         | 7            | 100                  | 4              | 4 x 500              | 2 / elevato                  |
| Standard_D3_v2  | 4         | 14           | 200                  | 8              | 8 x 500              | 4 / elevato                  |
| Standard_D4_v2  | 8         | 28           | 400                  | 16             | 16 x 500             | 8 / elevato                  |
| Standard_D5_v2  | 16        | 56           | 800                  | 32             | 32 x 500             | 8 / molto elevato        |
| Standard_D11_v2 | 2         | 14           | 100                  | 4              | 4 x 500              | 2 / elevato                  |
| Standard_D12_v2 | 4         | 28           | 200                  | 8              | 8 x 500              | 4 / elevato                  |
| Standard_D13_v2 | 8         | 56           | 400                  | 16             | 16 x 500             | 8 / elevato                  |
| Standard_D14_v2 | 16        | 112          | 800                  | 32             | 32 x 500             | 8 / molto elevato        |
| Standard_D15_v2 | 20        | 140          | 1.000                | 40             | 40 x 500             | 8 / molto elevato        |

## <a name="g-series"></a>Serie G

| Dimensioni        | Core CPU | Memoria: GiB  | SSD locale: GiB  | Dischi dati max | Velocità massima del disco: IOPS | NIC max / della larghezza di banda di rete |
|-------------|-----------|--------------|----------------------|----------------|--------------------|-----------------------|
| Standard_G1 | 2         | 28           | 384                  | 4              | 4 x 500            | 1 / elevato                  |
| Standard_G2 | 4         | 56           | 768                  | 8              | 8 x 500            | 2 / elevato                  |
| Standard_G3 | 8         | 112          | 1.536                | 16             | 16 x 500           | 4 / molto elevato             |
| Standard_G4 | 16        | 224          | 3.072                | 32             | 32 x 500           | 8 / molto elevato        |
| Standard_G5 | 32        | 448          | 6,144                | 64             | 64 x 500           | 8 / molto elevato        |


## <a name="h-series"></a>Serie di H

Azure macchine virtuali di serie H sono la successiva generazione di elaborazione con prestazioni macchine virtuali destinate a esigenze di elaborazione high-end, ad esempio la modellazione molecolare e calcolo di dinamica fluida. Queste 8 e macchine virtuali di 16 core si basano sulla tecnologia di processore Intel Haswell E5 2667 V3 con DDR4 memoria e archiviazione SSD in base a locale. 

Oltre a potenza della CPU sostanziale la serie H offre diverse opzioni per la rete RDMA bassa latenza utilizzando FDR InfiniBand e varie configurazioni di memoria per supportare i requisiti di calcolo in modo intensivo della memoria.


| Dimensioni           | Core CPU | Memoria: GiB | SSD locale: GiB | Dischi dati max | Velocità massima del disco: IOPS | NIC max / della larghezza di banda di rete |
|----------------|-----------|-------------|--------------------------|----------------|---------------------------|------------------------------|
| Standard_H8    | 8         | 56          | 1000                     | 16             | 16 x 500                    | 8 / elevato                      |
| Standard_H16   | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / molto elevato                  |
| Standard_H8m   | 8         | 112         | 1000                     | 16             | 16 x 500                    | 8 / elevato                      |
| Standard_H16m  | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / molto elevato                 |
| Standard_H16r * | 16        | 112         | 2000                     | 32             | 32 x 500                    | 8 / molto elevato                  |
| Standard_H16mr * | 16        | 224         | 2000                     | 32             | 32 x 500                    | 8 / molto elevato                  |


* RDMA dotato di

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Note: Standard A0 - A4 utilizzando CLI e PowerShell 

Nel modello di distribuzione classica alcuni nomi di dimensioni macchine Virtuali sono leggermente diversi in CLI e PowerShell:

* Standard_A0 è ExtraSmall 
* Standard_A1 è piccola
* Standard_A2 è medio
* Standard_A3 è grande
* Standard_A4 è ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurare le dimensioni per i servizi Cloud

È possibile specificare le dimensioni di macchina virtuale di un'istanza del ruolo come parte del modello di servizio descritto dal [file di definizione del servizio](cloud-services-model-and-package.md#csdef). Le dimensioni del ruolo determinano il numero di core CPU, la capacità di memoria e le dimensioni del sistema locale file assegnata a un'istanza in esecuzione. Scegliere le dimensioni di ruolo in base a requisito di risorse dell'applicazione.

Ecco un esempio per impostare le dimensioni di ruolo siano [Standard_D2](#general-purpose-d) per un'istanza di ruolo Web:

```xml
<WorkerRole name="Worker1" vmsize="<mark>Standard_D2</mark>">
...
</WorkerRole>
```

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [abbonamento azure e limiti del servizio, le quote e i vincoli](../azure-subscription-service-limits.md).
- Informazioni [sulle macchine virtuali di serie di una serie di H e complesse](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) per carichi di lavoro come High-performance Computing (HPC).

