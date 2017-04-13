<properties
   pageTitle="Ripristino di emergenza servizio tessuti Azure | Microsoft Azure"
   description="Azure tessuti servizio offre le funzionalità necessarie per gestire tutti i tipi di emergenza. In questo articolo vengono illustrati i tipi di problemi che possono verificarsi e come gestire con loro."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="seanmck"/>

# <a name="disaster-recovery-in-azure-service-fabric"></a>Ripristino di emergenza in Azure servizio tessuti

Assicurarsi di l'esecuzione di un'applicazione cloud disponibilità fondamentale che è possibile superare tutti i diversi tipi di errori, inclusi quelli di fuori del controllo. In questo articolo descrive il layout fisico di un cluster di Azure servizio tessuti nel contesto di emergenza potenziali e fornisce indicazioni su come gestire tali guasti per limitare o eliminare il rischio di inattività o perdita di dati.

## <a name="physical-layout-of-service-fabric-clusters-in-azure"></a>Layout fisico dell'infrastruttura di servizio cluster in Azure

Per comprendere i rischi di diversi tipi di errori, è utile sapere come cluster vengono fisica disposti in Azure.

Quando si crea un cluster di servizio tessuti in Azure, è necessario selezionare un paese in cui verrà ospitato. L'infrastruttura di Azure disposizioni quindi le risorse per tale cluster all'interno dell'area, in particolare il numero di macchine () richiesto. Osservare più da vicino dove e come tali macchine virtuali vengono effettuato il provisioning.

### <a name="fault-domains"></a>Domini di guasto

Per impostazione predefinita, le macchine virtuali del cluster sono distribuite uniformemente in gruppi logici noti come domini guasto (FDs), segmento macchine basate su potenziali errori di hardware host. In particolare, se due macchine virtuali si trovano in due FDs distinte, può essere sicuri di non condividere la stessa origine o rete interruttore. Di conseguenza, una rete locale o che interessano uno macchine Virtuali di alimentazione avrà alcun effetto a altro, consentendo tessuti servizio ribilanciare il carico di lavoro del computer non risponde all'interno del cluster.

È possibile visualizzare il layout del cluster domini guasto usando la mappa cluster fornita in [Esplora risorse tessuti servizio](service-fabric-visualizing-your-cluster.md):

![Nodi distribuiti in domini guasto in Esplora risorse tessuti servizio][sfx-cluster-map]

>[AZURE.NOTE] Altro asse nella mappa cluster Mostra i domini di aggiornamento, in modo logico raggruppano nodi in base alle attività di manutenzione pianificata. Servizio tessuti cluster in Azure vengono sempre disposti in cinque domini aggiornamento.

### <a name="geographic-distribution"></a>Distribuzione geografica

Attualmente sono disponibili [26 aree Azure in tutto il mondo][azure-regions], con molti più annunciato. Una singola area può contenere uno o più centri dati fisici a seconda della domanda e la disponibilità delle posizioni adatti, tra gli altri fattori. Notare che anche in aree geografiche che contengono più fisica data center, non ci sono garanzie che macchine virtuali del cluster verranno distribuite uniformemente tra le posizioni fisiche. In effetti, attualmente, tutte le macchine virtuali per un determinato cluster vengono effettuato il provisioning all'interno di un singolo sito fisico.

## <a name="dealing-with-failures"></a>Gestione di errori

Esistono diversi tipi di errori che possono influenzare il cluster, ognuno con il proprio attenuazione. Esamineremo loro in ordine di probabilità che venga eseguita.

### <a name="individual-machine-failures"></a>Errori di singoli computer

Come detto, gli errori di singoli computer, all'interno della macchina virtuale o all'hardware o software hosting all'interno di un dominio, senza rischi autonomamente. Servizio tessuti vengono in genere rilevare l'errore in pochi secondi e rispondere conseguenza basati sullo stato del cluster. Se, ad esempio, il nodo è stata hosting repliche principale per una partizione, un nuovo database primario è scelto dalle repliche secondario della partizione. Quando Azure conferisce il computer non riuscito di backup, verrà automaticamente per tornare a partecipare al cluster e assumono nuovamente la condivisione del carico di lavoro.

### <a name="multiple-concurrent-machine-failures"></a>Più gli errori di computer simultaneo

Mentre domini guasto notevolmente ridurre il rischio di errori di computer simultanee, è sempre la possibilità di più errori casuale visualizzare diverse macchine in un cluster contemporaneamente.

In generale, come la maggior parte dei nodi rimangono disponibili, il cluster di continuare a funzionare, nonostante in ridotta come repliche informazioni sullo stato visualizzato compressi in un set più piccolo di computer e meno istanze senza informazioni sullo stato sono disponibili per distribuire carico.

#### <a name="quorum-loss"></a>Perdita di base

Se la maggior parte replica per partizione del servizio informazioni sullo stato passare verso il basso, tale partizione entra in uno stato noto come "perdita di base". A questo punto, tessuti servizio si arresta consentendo scrive in tale partizione per assicurarsi che il relativo stato rimane coerente e affidabile. In realtà è stiamo scegliere di accettare un periodo di indisponibilità per garantire che client non è vengono indicati che è stati salvati i dati quando in realtà non è stato. Si noti che, se si è scelto di consentire legge dalle repliche secondarie per tale servizio informazioni sullo stato, è possibile continuare a eseguire le operazioni in questo stato di lettura. Una partizione rimane la perdita di base fino a un numero sufficiente di repliche torni o l'amministratore del cluster forza il sistema a spostare tramite l' [API di ripristino ServiceFabricPartition][repair-partition-ps].

>[AZURE.WARNING] Eseguire un'operazione di ripristino mentre replica primaria è verso il basso comporterà la perdita di dati.

Servizi di sistema può essere inoltre soggetta perdite di base con un impatto essere specifiche per il servizio in questione. Ad esempio perdita di base nel servizio di denominazione impatto risoluzione dei nomi, mentre la perdita di base del servizio di gestione failover Blocca failover e la creazione di nuovi servizi. Si noti che a differenza dei servizi, poiché il ripristino di servizi di sistema *non* è consigliabile. Se, tuttavia, è preferibile attendere fino a tornare repliche verso il basso.

#### <a name="minimizing-the-risk-of-quorum-loss"></a>Ridurre il rischio di perdere di base

È possibile ridurre il rischio di perdere base aumentando la dimensione del set di replica di destinazione per il servizio. Può essere utile considerare il numero di repliche è necessario per quanto riguarda il numero dei nodi disponibili che accettabile in una volta rispettando disponibili per scrittura, tenendo presente che applicazione o aggiornamenti cluster inserirvi nodi temporaneamente non disponibile, oltre a errori hardware.

Valutare la possibilità di negli esempi seguenti presupponendo che è ora configurato i servizi per avere un MinReplicaSetSize di tre, il numero più piccolo consigliato per i servizi di produzione. Con un TargetReplicaSetSize di tre (uno primario e due dei secondari), verrà generato un errore durante l'aggiornamento (due repliche verso il basso) la perdita di base e non sarà più il servizio di sola lettura. In alternativa, se si dispongono di cinque repliche, si potrà contrastare due errori durante l'aggiornamento (tre repliche verso il basso) come due repliche rimanenti possono ancora formare una base all'interno del set di replica minima.

### <a name="data-center-outages-or-destruction"></a>Interruzioni del centro di dati o l'eliminazione

Raramente, centri dati fisica possono diventare temporaneamente non disponibili a causa di perdita di power o connettività di rete. In questi casi, il servizio tessuti cluster e le applicazioni allo stesso modo saranno disponibili ma i dati verranno mantenuti. Per i cluster in esecuzione in Azure, è possibile visualizzare gli aggiornamenti su interruzioni nella [pagina stato Azure][azure-status-dashboard].

In caso di probabilità che un intero fisica data center viene eliminato, qualsiasi cluster tessuti servizio ospitato sono andranno perse, insieme allo stato.

Per evitare questa possibilità, è importante periodicamente [lo stato di backup](service-fabric-reliable-services-backup-restore.md) in un archivio geografico ridondanti e assicurarsi di avere convalidato la possibilità di ripristinare. Con quale frequenza è eseguire un backup sarà dipende l'obiettivo di punto di ripristino (rilasciato). Anche se non è stato completamente implementato backup e ripristino ancora, sarà necessario implementare un gestore per il `OnDataLoss` evento, in modo che è possibile registrare quando si verifica come segue:

```c#
protected virtual Task<bool> OnDataLoss(CancellationToken cancellationToken)
{
  ServiceEventSource.Current.ServiceMessage(this, "OnDataLoss event received.");
  return Task.FromResult(true);
}
```


### <a name="software-failures-and-other-sources-of-data-loss"></a>Errori software e da altre origini perdita di dati

Come causa la perdita di dati, gli errori del codice in servizi, risorse umani errori e violazioni della protezione sono più comuni errori di centro dati diffusi. Tuttavia, in tutti i casi, la strategia di ripristino corrisponde al: eseguire il backup regolari di tutti i servizi informazioni sullo stato e sperimentare la possibilità di ripristinare tale stato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come simulare vari errori utilizzando il [framework testabilità](service-fabric-testability-overview.md)
- Leggere altre risorse di ripristino di emergenza e disponibilità. Microsoft ha pubblicato una grande quantità di indicazioni su questi argomenti. Mentre alcuni di questi documenti riferimento a tecniche specifiche per l'utilizzo in altri prodotti, contengono molte procedure consigliate generali che è possibile applicare anche il contesto di servizio tessuti:
 - [Elenco di controllo di disponibilità](../best-practices-availability-checklist.md)
 - [Eseguire un drill di ripristino di emergenza](../sql-database/sql-database-disaster-recovery-drills.md)
 - [Ripristino di emergenza e disponibilità per applicazioni Azure][dr-ha-guide]


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
