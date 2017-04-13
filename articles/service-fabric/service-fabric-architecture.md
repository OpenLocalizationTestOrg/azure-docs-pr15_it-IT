<properties
   pageTitle="Architettura di servizio tessuti | Microsoft Azure"
   description="Servizio tessuti è una piattaforma di sistemi distribuiti utilizzata per creare scalable, affidabile e facile da gestire applicazioni per il cloud. In questo articolo illustra l'architettura di servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/09/2016"
   ms.author="rsinha"/>

# <a name="service-fabric-architecture"></a>Architettura di tessuti di servizio

Servizio tessuti integrato con sottosistemi a più livelli. Questi sottosistemi consentono di scrivere applicazioni che:

* Disponibilità
* Scalable
* Gestibile
* Da testare

Il diagramma seguente illustra sottosistema principali del servizio tessuti.

![Diagramma dell'architettura tessuti servizio](media/service-fabric-architecture/service-fabric-architecture.png)

In un sistema distribuito alla possibilità di comunicare in modo sicuro tra i nodi in un cluster è fondamentale. Alla base dello stack è il sottosistema di trasporto, che consentono la comunicazione sicura tra i nodi. Sopra il trasporto sottosistema si trova il sottosistema federazione che cluster nodi diversi in una singola entità (denominata cluster) in modo che servizio tessuti possibile rilevare errori, eseguire scelta carattere di riempimento e fornire routing coerente. Sottosistema l'affidabilità, disposti in livelli nella parte superiore di sottosistema federazione è responsabile per l'affidabilità dei servizi di infrastruttura di servizio tramite meccanismi, ad esempio replica, la gestione delle risorse e failover. Sottosistema federazione anche sottostante sottosistema hosting e l'attivazione, che consente di gestire il ciclo di vita di un'applicazione su un singolo nodo. Sottosistema gestione consente di gestire il ciclo di vita di applicazioni e servizi. Sottosistema testabilità consente agli sviluppatori di applicazioni verificare i servizi tramite simulati errori prima e dopo la distribuzione di applicazioni e servizi in ambienti di produzione. Servizio tessuti consente di risolvere le posizioni di servizio attraverso il sottosistema di comunicazione. I modelli di programmazione di applicazioni esposti agli sviluppatori disposti in livelli nella parte superiore di questi sottosistemi insieme il modello di applicazione per abilitare gli utensili.

## <a name="transport-subsystem"></a>Sottosistema di trasporto
Sottosistema di trasporto implementato un canale di comunicazione datagramma punto. Il canale viene utilizzato per la comunicazione all'interno di cluster tessuti servizio e le comunicazioni tra i cluster tessuti servizio e i client. Supporta unidirezionale e gli schemi di comunicazione richiesta-risposta, che fornisce la base per l'implementazione ultimi nel livello federazione. Sottosistema di trasporto protegge la comunicazione con X509 certificati o la sicurezza di Windows. Questo sottosistema viene utilizzato internamente tessuti servizio e non sono direttamente accessibile per gli sviluppatori per la programmazione di applicazioni.

## <a name="federation-subsystem"></a>Sottosistema federazione
Per motivo su un insieme di nodi in un sistema distribuito, è necessario disporre di una visualizzazione coerente del sistema. Sottosistema federazione utilizza primitive comunicazioni fornite dal sottosistema di trasporto e assembla vari nodi in un unico cluster unificato che possono riflettere sul. Fornisce le primitive di sistemi distribuiti necessari altri sottosistema - rilevamento degli errori, scelta di carattere di riempimento e routing coerenti. Sottosistema federazione è basato su tabelle distribuito con uno spazio token 128 bit. Il sottosistema crea una topologia ad anello i nodi, con ogni livello di nodo di inoltrare la chiamata viene assegnata un sottoinsieme dell'area token per la proprietà. Rilevamento degli errori, il livello utilizza un meccanismo leasing sulla base di cuore elevati e arbitrato. Sottosistema federazione garantisce inoltre tramite join più complessi e i protocolli di partenza da un unico proprietario di un token è presente in qualsiasi momento. Questo specificare scelta carattere di riempimento e le garanzie per il routing coerenti.

## <a name="reliability-subsystem"></a>Sottosistema affidabilità
Sottosistema affidabilità meccanismo per verificare lo stato di un servizio di assistenza tessuti disponibilità tramite la _replica_, _Gestione Failover_e _Bilanciamento delle risorse_.

* La replica garantisce che replicare automaticamente le modifiche di stato della replica di servizio principale per repliche secondarie, mantenendo la coerenza tra principale e secondarie riproduzioni in un set di replica di servizi. La replica è responsabile della gestione di base tra le repliche del set. Interagisce con l'unità di failover per ottenere l'elenco delle operazioni da replicare e agente di riconfigurazione fornisce con la configurazione del set di replica. La configurazione indica repliche le operazioni devono essere replicate. Servizio tessuti fornisce una replica predefinito chiamato replica tessuti, che possono essere utilizzati per l'API di modello di programmazione per rendere lo stato del servizio elevata disponibilità e affidabilità.
* Il gestore di Failover garantisce che in nodi vengono aggiunti o rimossi dal cluster, il carico viene automaticamente ridistribuito tra i nodi disponibili. Se un nodo del cluster non riesce, il cluster verrà automaticamente riconfigurare repliche servizio per mantenere la disponibilità.
* Manager delle risorse viene inserito repliche servizio dominio errore del cluster e garantisce che tutte le unità di failover siano operative. Gestione risorse saldi anche risorse del servizio in pool condiviso sottostante dei nodi cluster per ottenere distribuzione carico uniforme ottimale.

## <a name="management-subsystem"></a>Sottosistema di gestione
Il sottosistema gestione fornisce servizio-to-end e gestione del ciclo di vita delle applicazioni. Cmdlet di PowerShell e le API di amministrazione consentono di eseguire il provisioning, distribuire, patch, eseguire l'aggiornamento e deselezionare effettuare il provisioning di applicazioni senza perdita di disponibilità. Sottosistema gestione esegue questa operazione tramite i servizi seguenti.

* **Gestione cluster**: si tratta di servizio principale che interagisce con il gestore di Failover da affidabilità per posizionare le applicazioni nei nodi in base ai vincoli di posizionamento del servizio. Manager delle risorse nel sottosistema failover garantisce che i vincoli mai interrotti. Il responsabile cluster gestisce il ciclo di vita delle applicazioni di disposizione per deselezionare effettuare il provisioning. Si integra con il responsabile dell'integrità per assicurarsi che la disponibilità dell'applicazione non persa dal punto di vista dell'integrità semantic durante l'aggiornamento.
* **Gestione dell'integrità**: questo servizio consente il monitoraggio dell'integrità di entità cluster, servizi e le applicazioni. Entità cluster (ad esempio nodi partizioni servizio e repliche) è possibile segnalare informazioni di stato, quindi aggregate nell'archivio centralizzato integrità. Queste informazioni integrità forniscono un'istantanea complessivo in un momento integrità dei servizi e nodi distribuiti in più nodi cluster, che consente di eseguire operazioni correttive necessarie. Query integrità che API consentono di eseguire una query degli eventi di stato segnalati al sottosistema di integrità. La query dell'integrità API restituiscono i dati non elaborati integrità archiviati nell'archivio di integrità o aggregato, interpretata dati integrità per un'entità cluster specifico.
* **Archivio immagini**: questo servizio fornisce lo spazio di archiviazione e la distribuzione dei file binari dell'applicazione. Questo servizio fornisce un archivio di file distribuito semplice in cui le applicazioni vengono caricate e scaricate da.


## <a name="hosting-subsystem"></a>Sottosistema hosting
Gestione cluster informa l'hosting sottosistema (in esecuzione su ciascun nodo) a quali servizi che devono essere gestiti per un particolare nodo. Sottosistema hosting gestisce quindi il ciclo di vita dell'applicazione su tale nodo. Interagisce con i componenti l'affidabilità e l'integrità per garantire che le repliche siano posizionate correttamente e integrità.

## <a name="communication-subsystem"></a>Sottosistema di comunicazione
Questo sottosistema fornisce messaggistica affidabile all'interno di individuazione cluster e servizi tramite il servizio di denominazione. Il servizio di denominazione risolve i nomi dei servizi in una posizione nel cluster e consente agli utenti di gestire proprietà e i nomi dei servizi. Usa il servizio di denominazione, client in modo sicuro possono comunicare con uno dei nodi cluster per risolvere un nome di servizio e il recupero dei metadati del servizio. Con un semplice client di denominazione API, gli utenti del servizio tessuti possono sviluppare servizi e client in grado di risolvere il percorso di rete corrente nonostante dinamismo nodo o la modifica delle dimensioni del cluster.

## <a name="testability-subsystem"></a>Sottosistema testabilità
Testabilità è un set di strumenti progettati specificamente per la verifica del servizi basati su infrastruttura di servizio. Gli strumenti di consentono uno sviluppatore facilmente provocare errori significativi ed eseguire gli scenari di test per verificare e convalidare i numerosi Stati e transizioni riscontrati un servizio per tutta la durata in modo controllato e sicuro. Testabilità consente anche per eseguire test più in grado di scorrere attraverso varie possibili errori senza perdere la disponibilità. In questo modo è un ambiente di test di produzione.
