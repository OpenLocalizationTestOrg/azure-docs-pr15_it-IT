<properties
   pageTitle="Pianificazione della capacità di cluster tessuti servizio | Microsoft Azure"
   description="Considerazioni sulla pianificazione capacità di cluster di servizio tessuti. Livelli NodeTypes, durata e affidabilità"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Capacità cluster servizio tessuti considerazioni sulla pianificazione

Per tutte le distribuzioni di produzione, pianificazione delle capacità è un passaggio importante. Ecco alcuni degli elementi che è necessario considerare come parte del processo.

- Il numero di tipi di nodo che deve iniziare con i cluster
- Le proprietà di ogni tipo di nodo (dimensioni, primario, internet affiancate, numero di macchine virtuali e così via).
- Le caratteristiche e affidabilità del cluster

Automatica brevemente esaminare ciascuno di essi.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>Il numero di tipi di nodo che deve iniziare con i cluster

Prima di tutto, è necessario stabilire quali cluster che si sta creando verrà utilizzato per e i tipi di applicazioni che si intende distribuire in questo cluster. Se non si cancella sullo scopo del cluster, probabile non ancora pronti per immettere la capacità di processo di pianificazione.

Stabilire il numero di tipi di nodo che deve iniziare con i cluster.  Ogni tipo di nodo sia associato a un Set di scala macchina virtuale. Ogni tipo di nodo quindi può essere ridimensionato o verso il basso in modo indipendente, è diversi insiemi di porte aperte e può avere metriche capacità diversi. Pertanto la decisione del numero di tipi di nodo essenzialmente un po' come tenendo presente quanto segue:

- L'applicazione dispone di più servizi e uno di questi devono essere pubblico o connessi a internet? Applicazioni tipiche includono un servizio gateway front-end che riceve l'input da un client e uno o più servizi back-end in grado di comunicare con i servizi front-end. Pertanto in questo caso, si otterrà almeno due tipi di nodo.

- I servizi (che costituiscono l'applicazione) hanno esigenze di infrastruttura diverse, ad esempio RAM maggiore o versioni successive cicli di CPU? Ad esempio, si presuppone che l'applicazione che si desidera distribuire contiene un servizio front-end e un servizio di back-end. Il servizio front-end possono essere eseguiti sui più piccoli macchine virtuali (dimensioni macchine Virtuali come D2) che hanno porte aperte a internet.  Il servizio di back-end, tuttavia sono che richiede significative attività di calcolo e deve eseguire in macchine virtuali di maggiore (con dimensioni di macchine Virtuali come D4, D6, D15) che non sono internet affiancate.

 In questo esempio, anche se è possibile spostare tutti i servizi sul tipo di un nodo, è consigliabile che vengono inseriti in un cluster con due tipi di nodo.  In questo modo per ogni tipo di nodo di disporre di proprietà valori univoci, ad esempio la connettività internet o dimensione memoria virtuale. Il numero di macchine virtuali può essere ridimensionato in modo indipendente, anche.  

- Poiché non è possibile prevedere il futuro, passare con informazioni di che conoscere e decidere il numero dei tipi di nodo necessarie per iniziare con le applicazioni. È sempre possibile aggiungere o rimuovere tipi di nodo in un secondo momento. Un cluster di servizio tessuti deve avere almeno un nodo tipo.

## <a name="the-properties-of-each-node-type"></a>Le proprietà di ogni tipo di nodo

Il **tipo di nodo** può essere visualizzato come equivalenti ai ruoli in servizi Cloud. Tipi di nodo definiscono le dimensioni di macchine Virtuali, il numero di macchine virtuali e le relative proprietà. Ogni tipo di nodo definiti in un cluster di servizio tessuti sia configurato come un Set di scala macchina virtuale separata. Set di scala macchine Virtuali sono risorse è possibile utilizzare per distribuire e gestire un insieme di macchine virtuali come un set di calcolare un Azure. Viene definito come scala macchine Virtuali distinti imposta, ogni tipo di nodo quindi può essere ridimensionato o verso il basso in modo indipendente, è diversi insiemi di porte aperte e può avere metriche capacità diversi.

Il cluster può avere più di un tipo di nodo, ma il tipo di nodo principale (il primo definite dall'utente nel portale di) deve avere almeno cinque macchine virtuali di cluster utilizzati per carichi di lavoro di produzione (o almeno tre macchine virtuali per i cluster di test). Se si desidera creare il cluster utilizzando un modello di Manager delle risorse, si noterà un attributo **è principale** in definizione di tipo nodo. Il tipo di nodo principale è il tipo di nodo in cui vengono inseriti i servizi di sistema servizio tessuti.  

### <a name="primary-node-type"></a>Tipo di nodo principale
Per un cluster con più tipi di nodo, sarà necessario sceglierne uno di questi utenti siano principale. Ecco le caratteristiche di un tipo di nodo principale:

- La dimensione minima di macchine virtuali per il tipo di nodo principale è il livello di durata che scelto. Il valore predefinito per il livello di durata è minimo. Scorrere verso il basso per informazioni dettagliate sul qual è il livello di durata e i valori che può richiedere.  

- Il numero minimo di macchine virtuali per il tipo di nodo principale è il livello di affidabilità che scelto. Il valore predefinito per il livello di affidabilità: grigio chiaro. Scorrere verso il basso per informazioni dettagliate sul qual è il livello di affidabilità e i valori che può richiedere.

- I servizi di sistema tessuti di servizio (ad esempio, il servizio di gestione Cluster o il servizio di archiviazione immagine) si trovano sul tipo di nodo principale e quindi l'affidabilità e durata del cluster è il valore di livello di affidabilità e il valore di livello durata selezionare il tipo di nodo principale.

![Schermata di un cluster che è disponibili due tipi di nodo ][SystemServices]


### <a name="non-primary-node-type"></a>Tipo di nodo principale non
Per un cluster con più tipi di nodo, c'è un tipo di nodo principale e gli altri sono non primario. Ecco le caratteristiche di un tipo di nodo principale non:

- La dimensione minima di macchine virtuali per questo tipo di nodo dipende dal livello di durata che scelto. Il valore predefinito per il livello di durata è minimo. Scorrere verso il basso per informazioni dettagliate sul qual è il livello di durata e i valori che può richiedere.  

- Il numero minimo di macchine virtuali per questo tipo di nodo può essere una. Tuttavia è consigliabile scegliere questo numero in base al numero di repliche di applicazione/servizi che si desidera eseguire in questo tipo di nodo. Dopo aver distribuito il cluster, è possibile sostituire il numero di macchine virtuali in un tipo di nodo.


## <a name="the-durability-characteristics-of-the-cluster"></a>Le caratteristiche di durata del cluster

Il livello di durata viene utilizzato per indicare al sistema dei privilegi che nelle macchine virtuali hanno con l'infrastruttura di Azure sottostante. Nel tipo di nodo principale, questo privilegi consente tessuti servizio sospendere qualsiasi richiesta di livello infrastruttura macchine Virtuali (ad esempio un riavviare il computer macchine Virtuali, reimage macchine Virtuali o la migrazione di macchine Virtuali) che influiscono sui requisiti di base per i servizi di sistema e i servizi informazioni sullo stato. Nei tipi di nodo principale non questo privilegi consente tessuti servizio sospendere le richieste di livello infrastruttura macchine Virtuali come riavviare il computer macchine Virtuali, reimage macchine Virtuali, la migrazione di macchine Virtuali e così via, che influiscono sui requisiti di base per i servizi informazioni sullo stato in esecuzione al suo interno.

Questo privilegi sono espresso i valori seguenti:

- È possibile sospendere oro - i processi di infrastruttura per una durata di 2 ore per UD

- È possibile sospendere Silver - i processi di infrastruttura per una durata di 30 minuti per UD (questa è attualmente non è abilitata per l'utilizzo. Dopo l'attivazione sarà disponibile in tutte le macchine virtuali standard di single-core e versioni successive).

- Minimo - senza privilegi. Questo è il valore predefinito.

## <a name="the-reliability-characteristics-of-the-cluster"></a>Le caratteristiche di affidabilità del cluster

Il livello di affidabilità viene utilizzato per impostare il numero di repliche dei servizi che si desidera eseguire il cluster del tipo di nodo principale. Più il numero di repliche, più affidabile i servizi di sistema sono i cluster.  

Il livello di affidabilità può richiedere i valori seguenti.

- Platinum - eseguire i servizi di sistema con un valore di destinazione duplicata Imposta numero di 9

- Oro - eseguita i servizi di sistema con un valore di destinazione duplicata imposta conteggio di 7

- Argento - eseguita i servizi di sistema con un valore di destinazione duplicata Imposta numero 5

- Minimo - eseguita i servizi di sistema con un valore di destinazione duplicata imposta conteggio di 3

>[AZURE.NOTE] Il livello di affidabilità che scelto determina il numero minimo di nodi che deve avere il tipo di nodo principale. Il livello di affidabilità ha alcun effetto sulla dimensione massima del cluster. Per rendere un cluster di 20 nodo che viene eseguito l'affidabilità minimo.

 È possibile scegliere di aggiornare l'affidabilità del cluster da un livello a un altro. Questa operazione attiverà aggiornamenti cluster necessari per modificare la replica di servizi di sistema impostare conteggio. Attendere che l'aggiornamento in corso per completare prima di apportare altre modifiche al cluster, ad esempio aggiungere nodi e così via.  È possibile monitorare l'avanzamento dell'aggiornamento del servizio tessuti Explorer o eseguendo [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la pianificazione della capacità e configurare un cluster, leggere le operazioni seguenti:
- [Protezione cluster tessuti servizio](service-fabric-cluster-security.md)
- [Introduzione del modello di integrità servizio tessuti](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
