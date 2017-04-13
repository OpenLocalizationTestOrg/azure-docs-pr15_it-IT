<properties
   pageTitle="Elenco di controllo sulla resilienza | Microsoft Azure"
   description="Elenco di controllo che fornisce indicazioni per preoccupazioni sulla resilienza in fase di progettazione."
   services=""
   documentationCenter="na"
   authors="petertaylor9999"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="petertay"/>

# <a name="azure-resiliency-guidance-resiliency-checklist"></a>Linee guida sulla resilienza Azure: elenco di controllo sulla resilienza

La progettazione dell'applicazione per la resilienza richiede pianificazione e fattori una varietà di gestione degli errori che possono verificarsi. Rivedere gli elementi dell'elenco di controllo contro la progettazione dell'applicazione per renderla più flessibile.

## <a name="requirements"></a>Requisiti

- **Definire i requisiti di disponibilità del cliente.** Il cliente avrà requisiti di disponibilità per i componenti dell'applicazione e ciò influisce la progettazione dell'applicazione. È possibile ottenere contratto del cliente per i destinatari di disponibilità di ogni parte dell'applicazione, in caso contrario la struttura potrebbe non soddisfare le aspettative del cliente. Per ulteriori informazioni, vedere la sezione [definizione dei requisiti di adattabilità](guidance-resiliency-overview.md#defining-your-resiliency-requirements) del documento di [Progettazione di applicazioni flessibili per Azure](guidance-resiliency-overview.md) .

## <a name="failure-mode-analysis"></a>Analisi modalità degli errori

- **Eseguire un'analisi per la modalità errore (FMA) per l'applicazione.** FMA è un processo per la creazione di flessibilità in un'applicazione anticipata in fase di progettazione. Gli obiettivi di un FMA includono:  

    - Identificare i tipi di errori di un'applicazione potrebbero verificarsi. 
    - Acquisire i possibili effetti e impatto di ogni tipo di errore relativo all'applicazione.
    - Identificare strategie di ripristino. 

    Per ulteriori informazioni, vedere [Progettazione di applicazioni flessibili per Azure: analisi degli errori modalità][fma].  

## <a name="application"></a>Applicazione

- **Distribuzione di più istanze dei servizi.** Servizi inevitabile avrà esito negativo e se l'applicazione dipende da una singola istanza di un servizio non inevitabile riuscirà anche. Per eseguire il provisioning di più istanze di [Servizio App Azure](../app-service/app-service-value-prop-what-is.md), selezionare un [Piano di servizio App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) che offre più istanze. Per i servizi Cloud di Azure, configurare ognuno dei ruoli di utilizzare [più istanze](../cloud-services/cloud-services-choose-me.md#scaling-and-management). Per [Azure macchine ()](../virtual-machines/virtual-machines-windows-about.md), assicurarsi che l'architettura di macchine Virtuali include più macchine Virtuali e che ogni ed è disponibile in un [set di disponibilità][availability-sets].   

- **Utilizzare un bilanciamento del carico per distribuire le richieste.** Un servizio di bilanciamento del carico distribuisce le richieste dell'applicazione a istanze del servizio integro rimuovendo istanze non corretti da rotazione. Se il servizio utilizza servizio App Azure o servizi Cloud Windows Azure, è già il bilanciamento del carico dell'utente. Tuttavia, se l'applicazione utilizza macchine virtuali di Azure, è necessario effettuare il provisioning di un servizio di bilanciamento del carico. Vedere la panoramica [Di bilanciamento del carico Azure](../load-balancer/load-balancer-overview.md) per altri dettagli. 

- **Configurare gateway applicazione Azure per l'utilizzo di più istanze.** In base ai requisiti dell'applicazione, un [Gateway di applicazioni Azure](../application-gateway/application-gateway-introduction.md) potrebbe essere più adatto alla distribuzione le richieste di servizi dell'applicazione. Tuttavia, singole istanze di servizio Gateway di applicazione non sono garantite da un contratto di servizio in modo che è possibile che l'applicazione potrebbe non riuscire se l'istanza di Gateway di applicazioni esito negativo. Effettuare il provisioning di più istanze di Gateway di applicazioni Media o superiore per garantire la disponibilità del servizio in base ai termini del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/).

- **Usare i set di disponibilità per ogni livello di applicazione**. Inserire le istanze in un [set di disponibilità] [ availability-sets] garantisce la connettività di almeno un'istanza di macchine Virtuali all'interno le condizioni del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_2/). Se non si trovano nelle macchine virtuali in un set di disponibilità di che non si dispone garantisce proteggendoli ed è possibile che potrebbe non riuscire tutte o aggiornati contemporaneamente. 

- **È possibile distribuire l'applicazione tra più aree.** Se l'applicazione viene distribuito in un'area, l'evento raro l'intera area diventa disponibile, l'applicazione anche non saranno disponibile. Potrebbe essere accettabile in base ai termini del contratto di servizio dell'applicazione. In caso affermativo, è possibile distribuire un'applicazione e i relativi servizi tra più aree. Una distribuzione di più paesi è possibile utilizzare un motivo attivo attivo (distribuzione richieste tra più istanze attive) o un motivo attivo passivo (mantenere un'istanza di "calda" riserva, nel caso in cui l'istanza primaria esito negativo). È consigliabile distribuire più istanze di servizi dell'applicazione in coppie internazionali. Per ulteriori informazioni, vedere [Business continuità e ripristino di emergenza (BCDR): aree accoppiati Azure](../best-practices-availability-paired-regions.md).

- **Implementare adattabilità modelli per operazioni remote quando opportuno.** Se l'applicazione dipende comunicazioni tra servizi remoto, il percorso di comunicazione inevitabile avrà esito negativo. Se sono presenti più errori, le istanze rimanenti integrità dei servizi dell'applicazione Impossibile sovraccarico con richieste. Sono disponibili diversi modelli utili per la gestione problemi più comuni, tra cui il modello di timeout, [Riprova motivo][retry-pattern], [interruttore] [ circuit-breaker] motivo e gli altri utenti. Per ulteriori informazioni, vedere [creazione di applicazioni flessibili per Azure](guidance-resiliency-overview.md#implementing-resiliency-strategies). 

- **Utilizzare il ridimensionamento automatico per rispondere a un aumento di carico.** Se l'applicazione non è configurato per scalare automaticamente come carico aumenta, è possibile che i servizi dell'applicazione avrà esito negativo se sono saturazione con le richieste degli utenti. Per ulteriori informazioni, vedere le operazioni seguenti:

    - Generale: [elenco di controllo scalabilità](../best-practices-scalability-checklist.md) 
    - Servizio App Azure: [scalare conteggio delle istanze manualmente o automaticamente][app-service-autoscale]
    - Servizi cloud: [come ridimensionamento automatico un servizio cloud][cloud-service-autoscale]
    - Macchine virtuali: [Imposta scala macchina virtuale e il ridimensionamento automatico][vmss-autoscale]


- **Implementare operazioni asincrone laddove possibile.** Icona del operazioni possono monopolizzi risorse e bloccare altre operazioni mentre il chiamante è in attesa completare il processo. Progettare ogni parte dell'applicazione per consentire operazioni asincrone laddove possibile. Per ulteriori informazioni su come implementare la programmazione asincrona in c#, vedere [Programming asincrono con asincrono e attesa][asynchronous-c-sharp].

- **Utilizzare Gestione il traffico di Azure per indirizzare il traffico dell'applicazione per aree geografiche diverse.**  [Gestore del traffico Azure] [ traffic-manager] esegue il bilanciamento del carico a livello di DNS e instradare il traffico a diverse regioni in base al [routing del traffico] [ traffic-manager-routing] metodo specificato e lo stato del endpoint dell'applicazione. 

- **Configurare e testare le ricerche di integrità per i servizi di bilanciamento del carico e alla gestione.** Assicurarsi che la logica dell'integrità della verifica parti critiche del sistema e risponde in modo appropriato per le ricerche di integrità.

    - Lo stato verifica la presenza di [Azure il traffico Manager] [ traffic-manager] e [Di bilanciamento del carico Azure] [ load-balancer] utilizzare una funzione specifica. Per il traffico Manager verifica dell'integrità determina se venga reindirizzato a un'altra area. Per un bilanciamento del carico, determina se rimuovere una macchina virtuale rotazione.      

    - Per una verifica del traffico Manager l'endpoint di integrità devono controllare le dipendenze critiche che vengono distribuiti all'interno dell'area stessa e il cui errore deve attivare un il controllo a un'altra area.  

    - Per un bilanciamento del carico, l'endpoint di integrità deve report sull'integrità del macchina virtuale. Non includere altre livelli o servizi esterni. In caso contrario, un errore che si trova all'esterno della macchina virtuale causeranno il bilanciamento del carico rimuovere la macchina virtuale rotazione.

    - Per indicazioni sull'implementazione di monitoraggio nell'applicazione, vedere [Integrità Endpoint monitoraggio motivo](https://msdn.microsoft.com/library/dn589789.aspx).

- **Monitorare i servizi di terze parti.** Se l'applicazione ha dipendenze sui servizi di terze parti, identificare la posizione e come possono avere esito negativo questi servizi di terze parti e cosa effetto tali errori avrà sull'applicazione. Un servizio di terze parti non può includere il monitoraggio e diagnostica, pertanto è importante per registrare le chiamate di esse e metterle con dello stato dell'applicazione e tramite un identificatore univoco la registrazione diagnostica. Per ulteriori informazioni sulle procedure consigliate per il monitoraggio e diagnostica, vedere le [indicazioni di monitoraggio e diagnostica] [ monitoring-and-diagnostics-guidance] documento.

- **Assicurarsi che qualsiasi servizio di terze parti che è occupare fornisce un contratto di servizio.** Se l'applicazione dipende da un servizio di terze parti, ma terze parti non garantisce di disponibilità sotto forma di un contratto di servizio, anche disponibilità dell'applicazione non verrà garantita. Il contratto di servizio dipende solo come componente meno disponibile dell'applicazione.


## <a name="data-management"></a>Gestione dei dati

- **Comprendere i metodi di replica per le origini dati dell'applicazione.** I dati dell'applicazione verranno archiviati in origini dati diverse e sono i requisiti di disponibilità diversi. Valutare i metodi di replica per ogni tipo di archiviazione di dati di Azure, inclusi [Replica di spazio di archiviazione di Azure](../storage/storage-redundancy.md) e [SQL attiva geografico-replica di Database](../sql-database/sql-database-geo-replication-overview.md) per garantire che vengono soddisfatti i requisiti dei dati dell'applicazione.

- **Assicurarsi che nessun singolo account utente abbia accesso ai dati di produzione e backup.** Backup dei dati vengano compromesse se un singolo account utente dispone dell'autorizzazione per scrivere di produzione e origini di backup. Un utente non autorizzato potrebbe deliberatamente eliminare tutti i dati, mentre un normale utente accidentalmente Impossibile eliminarlo. Progettare l'applicazione di limitare le autorizzazioni di ogni account utente in modo che solo gli utenti che richiedono l'accesso di scrittura abbiano accesso in scrittura e è solo per produzione o copia di backup, ma non entrambe.

- **Documento origine dati non riuscire sopra e interromperà nuovo processo e testarlo.** Nel caso in cui l'origine dati non riesce catastrophically, sarà necessario un operatore di seguire un set di istruzioni venga reindirizzato a una nuova origine dati. Se la procedura documentazione è presenti errori, non sarà possibile seguire loro correttamente e non sulla risorsa un operatore. Testare regolarmente istruzioni per verificare che un operatore seguirli in grado di esito negativo su correttamente e prova non superata nuovamente l'origine dati.

- **Convalidare il backup dei dati.** Regolarmente verificare che i dati di backup siano quello previsto eseguendo uno script per convalidare le query, schema e l'integrità dei dati. Non esiste alcun punto con una copia di backup se non è utile ripristinare le origini dati. Accedere e inviare una segnalazione eventuali incoerenze in modo che il servizio di backup può essere ripristinato.

- **È possibile utilizzare un tipo di account di archiviazione ridondanti geografico.** Dati archiviati in un account di archiviazione Azure viene sempre replicati in locale. Tuttavia, sono disponibili più strategie di replica tra cui scegliere quando viene eseguito il provisioning di un Account di archiviazione. Selezionare [Azure accesso in lettura geografico ridondanti dello spazio di archiviazione (RA GRS)](../storage/storage-redundancy.md#read-access-geo-redundant-storage) per proteggere i dati dell'applicazione caso quando non è disponibile un'intera regione.

    > [AZURE.NOTE] Per le macchine virtuali, non basarsi su replica RA GRS per ripristinare i dischi macchine Virtuali (file disco rigido virtuale). Utilizzare invece [Il Backup di Azure][azure-backup].   

## <a name="operations"></a>Operazioni

- **Implementare di monitoraggio e segnalazione procedure consigliate per l'applicazione.** Senza corretto monitoraggio, diagnostica e gli avvisi, non è possibile rilevare errori dell'applicazione e Invia avviso un operatore per correggerli. Per ulteriori informazioni sulle procedure consigliate, vedere le [indicazioni di monitoraggio e diagnostica] [ monitoring-and-diagnostics-guidance] documento.

- **Misurare le statistiche delle chiamate remote e verificare le informazioni disponibili al team di applicazione.**  Se si non rilevare e segnalare le statistiche delle chiamate remote in tempo reale e fornire un modo semplice per rivedere queste informazioni, il team operativo non è una visualizzazione istantanea allo stato dell'applicazione. E se è solo ora misura Media delle chiamate remote, non si avrà le informazioni necessarie per rivelare problemi nei servizi. Riepilogare le metriche di chiamate remote, ad esempio latenza, velocità ed errori in percentili 99 e 95. Eseguire analisi statistiche sui dati statistici relativi a rivelare errori che si verificano all'interno di ogni percentile.

- **Tenere traccia il numero di eccezioni temporanee e tentativi per un periodo di tempo appropriato.** Se si non rilevare e monitorare le eccezioni temporanee e tentativi nel tempo, è possibile che un errore o il problema potrebbe dipendere da ritentare dell'applicazione. Se il monitoraggio e la registrazione solo Mostra esito positivo o negativo di un'operazione, verrà nascosto il fatto che l'operazione doveva ripetuta più volte a causa di eccezioni. Una linea di tendenza di crescente eccezioni nel tempo indica che il servizio è verificato un errore e potrebbe non riuscire. Per ulteriori informazioni, vedere [Riprova indicazioni specifiche servizio][retry-service-guidance].

- **Implementare un sistema di preavviso che notifica l'esistenza di un operatore.** Identificare le prestazioni chiave indicatori di stato dell'applicazione, ad esempio eccezioni temporanee e remote latenza di chiamata e impostare i valori soglia appropriata per ognuno di essi. Inviare un avviso per le operazioni quando viene raggiunto il valore di soglia. Impostare le soglie livelli che identificano problemi prima che diventino critici e richiede una risposta di ripristino.

- **Il processo di rilascio per l'applicazione del documento.** Senza documentazione di processo dettagliato release, un operatore potrebbe distribuire un aggiornamento non valido o in modo non corretto configurare le impostazioni per l'applicazione. Chiaramente definire il processo di rilascio del documento e assicurarsi che sia disponibile per l'intero team operazioni. Procedure consigliate per la distribuzione flessibile dell'applicazione in dettaglio nella [distribuzione flessibile] [ guidance-resilient-deployment] sezione del documento sulla resilienza indicazioni.

- **Assicurarsi che più persone i membri del team sono in grado di controllare l'applicazione ed eseguire le operazioni di ripristino manuale.** Se si dispone di un singolo operatore i membri del team in grado di controllare l'applicazione e avviare della procedura di ripristino, tale persona diventa un singolo punto di errore. Organizzare la formazione più persone rilevamento e ripristino e verificare che siano sempre almeno un attivo in qualsiasi momento.

- **Automatizzare il processo di distribuzione dell'applicazione.** Se il personale è necessario per distribuire manualmente l'applicazione, errore umano può causare la distribuzione di esito negativo. Per ulteriori informazioni sulle procedure consigliate per la distribuzione delle applicazioni di automazione, vedere [distribuzione flessibile] [ guidance-resilient-deployment] sezione del documento sulla resilienza indicazioni.

- **Progettare il processo di rilascio per ottimizzare la disponibilità delle applicazioni.** Se il processo di rilascio richiede servizi in linea durante la distribuzione, l'applicazione non sarà disponibile fino a quando non vengano riportati in linea. Utilizzare la tecnica di distribuzione [blu/verde](http://martinfowler.com/bliki/BlueGreenDeployment.html) o [rilasciare Canarie](http://martinfowler.com/bliki/CanaryRelease.html) per distribuire l'applicazione di produzione. Entrambe le tecniche implicano l'invio di distribuzione di codice di rilascio insieme a codice di produzione in modo che gli utenti della versione del codice possono essere reindirizzati a codice di produzione in caso di errore. Per ulteriori informazioni, vedere [distribuzione flessibile] [ guidance-resilient-deployment] sezione del documento sulla resilienza indicazioni.

- **Accedere e controllare le distribuzioni dell'applicazione.** Se si usa in più fasi tecniche di distribuzione, ad esempio blu/verde o canary Release sarà più di una versione dell'applicazione in fase di produzione. Se un problema dovrebbe essere eseguita, è importante stabilire quale versione dell'applicazione provoca un problema. Implementare una strategia efficace di registrazione per acquisire il maggior numero possibile di informazioni specifiche della versione. 

- **Assicurarsi che l'applicazione non viene eseguito e [limiti di Azure abbonamento](../azure-subscription-service-limits.md).** Gli abbonamenti Azure hanno limiti su determinati tipi di risorse, ad esempio numero di gruppi di risorse, numero di core e numero di account di archiviazione.  Se i requisiti dell'applicazione superano i limiti di abbonamento Azure, creare un'altra Azure abbonamento e provisioning sufficienti risorse sono.

- **Assicurarsi che l'applicazione non viene eseguito e [limiti di servizio](../azure-subscription-service-limits.md).** Singoli servizi Azure hanno limiti di consumo &mdash; , ad esempio limiti di spazio di archiviazione, la velocità di trasmissione, numero di connessioni, richieste al secondo e altre metriche. L'applicazione non riuscirà se si tenta di utilizzare risorse oltre questi limiti. Verrà creato limitazione e possibili i tempi di inattività per gli utenti interessati. 

    A seconda del servizio specifico e dei requisiti dell'applicazione, è possibile spesso evitare questi limiti scalabilità (ad esempio, la scelta di un altro livello prezzo) o in orizzontale (aggiunta di nuove istanze).  

- **Progettare i requisiti di spazio di archiviazione dell'applicazione per mantenerlo entro lo spazio di archiviazione Azure prestazioni e scalabilità i siti di destinazione.** Spazio di archiviazione Azure è progettato per funzionare all'interno di destinazioni prestazioni e scalabilità predefinite, in modo progettare l'applicazione di utilizzare lo spazio di archiviazione all'interno di tali destinazioni. Se si superano queste destinazioni applicazione verifichi la limitazione per l'archiviazione. Per risolvere il problema, effettuare il provisioning di altri account di archiviazione. Se si esegue e il limite di Account di archiviazione, effettuare il provisioning di altri abbonamenti Azure e quindi effettuare il provisioning di altri account di archiviazione non esiste. Per ulteriori informazioni, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](../storage/storage-scalability-targets.md).

- **Selezionare le dimensioni di macchine Virtuali appropriata per l'applicazione.** Misurare effettivo CPU, memoria, disco e i/o delle proprie macchine virtuali di produzione e verificare che le dimensioni di macchine Virtuali selezionate siano sufficiente. In caso contrario, l'applicazione potrebbe verificarsi problemi di capacità per macchine virtuali raggiungere i limiti. Le dimensioni di macchine Virtuali sono descritti in dettaglio nel documento di [dimensioni per macchine virtuali in Azure](../virtual-machines/virtual-machines-windows-sizes.md) .

- **Determinare se il carico di lavoro dell'applicazione stabile o variabile nel tempo.** Se il carico di lavoro è soggetto nel tempo, usare macchine Virtuali Azure scala Imposta scala automaticamente il numero di istanze di macchine Virtuali. In caso contrario, sarà necessario aumentare o ridurre il numero di macchine virtuali manualmente. Per ulteriori informazioni, vedere [Panoramica di set di scala macchina virtuale](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

- **Selezionare il livello di servizi più adatto per il Database di SQL Azure.** Se l'applicazione utilizza il Database di SQL Azure, assicurarsi di aver selezionato il livello di servizio appropriato. Se si seleziona un livello che non è in grado di gestire requisiti delle unità (DTU) transazione database dell'applicazione, verrà limitato l'uso dei dati. Per ulteriori informazioni sulla selezione il piano di servizio corretto, vedere il [prestazioni e le opzioni del Database di SQL: informazioni sulle funzionalità disponibili in ogni livello di servizio](../sql-database/sql-database-service-tiers.md) documento. 

- **Avere un piano di ripristino per la distribuzione.** È possibile che la distribuzione dell'applicazione potrebbe non riuscire e determina l'indisponibilità dell'applicazione. Progettare un processo di ripristino per tornare a un'ultima versione nota funzionante e ridurre i tempi di inattività. Vedere [distribuzione flessibile] [ guidance-resilient-deployment] sezione del documento sulla resilienza indicazioni per altre informazioni. 

- **Creare un processo per l'interazione con supporto Azure.** Se il processo di contattare [il supporto di Azure](https://azure.microsoft.com/support/plans/) non è prima necessario contattare il supporto tecnico, i tempi di inattività verrà prolungato come ci si sposta il processo di supporto per la prima volta. Includere il processo di contattare il supporto ed escalation problemi come parte della flessibilità dell'applicazione dall'inizio.

- **Assicurarsi che l'applicazione non utilizza più di un numero massimo di account di archiviazione per l'abbonamento.** Azure consente un massimo di 200 account di archiviazione per l'abbonamento. Se l'applicazione richiede account di archiviazione molto più sono attualmente disponibili in abbonamento, sarà necessario creare una nuova sottoscrizione e creare gli account di spazio di archiviazione aggiuntivo non esiste. Per ulteriori informazioni, vedere [sottoscrizione Azure e limiti di servizio, le quote e i vincoli](../azure-subscription-service-limits.md#storage-limits).

- **Verificare che l'applicazione non superino le destinazioni scalabilità per dischi macchina virtuale.** Una macchina virtuale IaaS Azure supporta l'associazione di un numero di dischi di dati in base a più fattori, tra cui la dimensione memoria virtuale e il tipo di account di archiviazione. Se l'applicazione supera le destinazioni scalabilità per dischi macchina virtuale, eseguire il provisioning degli account di spazio di archiviazione aggiuntivo e creare sono i dischi macchina virtuale. Per ulteriori informazioni, vedere [scalabilità dello spazio di archiviazione di Azure e obiettivi] (... / storage/storage-scalability-targets.md#scalability-targets-for-virtual-machine-disks)

## <a name="test"></a>Test

- **Eseguire failover e failback test per l'applicazione.** Se non sono completamente verificata failover e failback, non si la certezza che i servizi dipendenti nell'applicazione torni in modo sincronizzato durante il ripristino di emergenza. Assicurarsi che i dipendenti dell'applicazione Servizi failover e fail indietro nell'ordine corretto.

- **Eseguire il test per l'applicazione l'inserimento di errori.** L'applicazione può avere esito negativo per diversi motivi, ad esempio scadenza certificato, esaurimento delle risorse di sistema in una macchina virtuale o errori di archiviazione. Testare l'applicazione in un ambiente più vicino possibile alla produzione, tramite la simulazione o l'attivazione errori reali. Ad esempio, eliminare i certificati, artificialmente utilizzare risorse di sistema o eliminare un'origine di spazio di archiviazione. Verificare la capacità dell'applicazione per recuperare tutti i tipi di errori, solo in combinazione. Controllo errori non moltiplicazione o CSS tramite il sistema.

- **Eseguire i test di produzione con i dati utente sintetici e real.** Prova e produzione sono identici raramente, pertanto è importante utilizzare blu/verde o una distribuzione canary e testare l'applicazione di produzione. In questo modo è possibile testare l'applicazione di produzione in condizioni di carico reale e assicurarsi che funziona come previsto quando è completamente distribuito.

## <a name="security"></a>Sicurezza

- **Implementare la protezione a livello di applicazione contro distribuito attacchi (DDoS).** Servizi Azure sono protetti attacchi DDos livello di rete. Tuttavia, Azure non è possibile proteggersi dai attacchi a livello di applicazione, perché è difficile distinguere tra le richieste di utente true da utenti malintenzionati richieste. Per ulteriori informazioni su come proteggere attacchi DDoS livello di applicazione, vedere la sezione "Protezione contro DDoS" di [Protezione di Microsoft Azure rete](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Scarica PDF).

- **Implementare seguire il principio dei privilegi minimi per l'accesso alle risorse dell'applicazione.** Il valore predefinito per l'accesso alle risorse dell'applicazione dovrebbe essere più restrittivo possibile. Concedere autorizzazioni di livello superiore in base all'approvazione. Concessione dell'accesso valore eccessivamente restrittivo per le risorse dell'applicazione per impostazione predefinita causando qualcuno intenzionalmente o accidentalmente eliminazione delle risorse. È importante verificare le autorizzazioni privilegi minimi per le altre risorse che dispongono di sistemi di autorizzazioni, ad esempio SQL Server Azure fornisce [il controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-built-in-roles.md) per la gestione dei privilegi di utente. 

## <a name="telemetry"></a>Telemetria

- **Accedere ai dati di telemetria mentre l'applicazione è in esecuzione nell'ambiente di produzione.** Acquisire informazioni di telemetria affidabile durante l'applicazione è in esecuzione nell'ambiente di produzione o non è sufficiente informazioni per individuare la causa di problemi mentre attivamente è serve agli utenti. Ulteriori informazioni sono disponibili nel registro delle procedure consigliate è disponibile nella [Guida di monitoraggio e diagnostica] [ monitoring-and-diagnostics-guidance] documento.

- **Implementare la registrazione utilizzando un modello asincrono.** Se sono icona del operazioni di registrazione, si potrebbe bloccare il codice dell'applicazione. Assicurarsi che le operazioni di registrazione sono previsti operazioni asincrone. 

- **Correlare i dati di registro oltre i confini del servizio.** In una tipica applicazione a più livelli, richiesta di un utente può scorrere diversi limiti di servizio. Ad esempio una richiesta dell'utente in genere viene creata nel livello web passata a livello aziendale e infine mantenuta nel livello di dati. In scenari più complessi, una richiesta di utente può essere distribuita a molti diversi archivi di servizi e i dati. Assicurarsi che il sistema di registrazione correlato chiamate oltre i confini del servizio, è possibile registrare la richiesta in tutta l'applicazione.

##  <a name="azure-resources"></a>Risorse Azure 

- **Usare i modelli di Azure gestione di risorse a disposizione risorse.** Modelli di gestione risorse rendono più semplice automatizzare le distribuzioni tramite PowerShell o CLI Azure, che illustra un processo di distribuzione più affidabile. Per ulteriori informazioni, vedere [Panoramica di gestione di risorse Azure][resource-manager].

- **Assegnare risorse nomi significativi.** Assegnare risorse nomi significativi, è facile individuare una risorsa specifica e comprendere il proprio ruolo. Per ulteriori informazioni, vedere [consigliate per le risorse Azure convenzioni di denominazione](guidance-naming-conventions.md) 

- **Utilizzare il controllo dell'accesso basato sui ruoli (RBAC)**. Consente di controllare l'accesso alle risorse Azure che si distribuiscono RBAC. RBAC consente di assegnare i ruoli di autorizzazione per i membri del team attrezzi per impedire modifiche o eliminazioni accidentali alle risorse distribuite. Per ulteriori informazioni, vedere [Introduzione a gestione degli accessi nel portale di Azure](../active-directory/role-based-access-control-what-is.md) 

- **Utilizzare blocchi di risorse per le risorse critiche, ad esempio macchine virtuali.** Blocchi di risorse impediscono a un operatore di eliminazione accidentale di una risorsa. Per ulteriori informazioni, vedere [risorse di blocco con Gestione risorse di Azure](../resource-group-lock-resources.md) 

- **Internazionali coppie di parole.** Quando si distribuisce a due aree, scegliere aree la stessa coppia internazionali. In caso di un'interruzione dell'ampio, ripristino di un'area ha la priorità fuori ciascuna coppia. Alcuni servizi, ad esempio lo spazio di archiviazione ridondanti geografico fornire replica automatica all'area accoppiata. Per ulteriori informazioni, vedere [Business continuità e ripristino di emergenza (BCDR): Azure accoppiati aree](../best-practices-availability-paired-regions.md) 

- **Gruppi di risorse organizza dalla funzione e del ciclo di vita**.  In generale, un gruppo di risorse deve contenere risorse che condividono il ciclo di vita stesso. Rende più facile gestire distribuzioni, eliminare distribuzioni di prova e assegnare diritti di accesso, riducendo la possibilità di una distribuzione di produzione viene eliminata involontariamente o modificata. Creare gruppi di risorse separati per produzione, sviluppo e testare ambienti. In una distribuzione a più aree, inserire le risorse per ogni regione nei gruppi di risorse separato. In questo modo più semplice ridistribuire un'area senza influenzare altri zona. 

## <a name="azure-services"></a>Servizi di Azure 

Gli elementi di elenco di controllo seguenti si applicano a servizi specifici in Azure.

###  <a name="app-service"></a>Servizio di App 

- **Utilizzare livello Standard o Premium.** Questi livelli supportano bande orarie di gestione temporanea e l'esecuzione di backup automatico. Per ulteriori informazioni, vedere [panoramica approfondita piani servizio App Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 

- **Evitare di ridimensionamento verso l'alto o verso il basso.** Se, tuttavia, selezionare un livello e le dimensioni di istanza che soddisfano i requisiti di prestazioni in condizioni di carico normali e [scalabilità](../app-service-web/web-sites-scale.md) le istanze di gestire le modifiche nel volume di traffico. Ridimensionamento alto e verso il basso può attivare il riavvio dell'applicazione.  

- **Configurazione dell'archivio come le impostazioni dell'app.** Utilizzare le impostazioni dell'app per contenere le impostazioni di configurazione come le impostazioni dell'app. Definire le impostazioni dei modelli di Manager delle risorse o tramite PowerShell, in modo che è possibile applicarli come parte di una distribuzione automatica / aggiornamento del processo, è più affidabile. Per ulteriori informazioni, vedere [configurare web apps in Azure App servizio](../app-service-web/web-sites-configure.md). 

- **Creare piani di servizio App separati di produzione e test.** Non usare bande orarie nella distribuzione di produzione per la verifica.  Tutte le app nello stesso plan servizio App condividono le stesse istanze di macchine Virtuali. Se si inseriscono produzione e distribuzioni di prova nello stesso piano, può influire negativamente sulla distribuzione della produzione. Ad esempio i test di carico potrebbero influire negativamente sul sito di produzione. Inserendo distribuzioni di prova in un piano separato, possono isolati rispetto alla versione di produzione.  

- **App web separato da web API**. Se la soluzione dispone di un front-end web e dell'API web, prendere in considerazione la loro scomposizione nelle applicazioni di servizio App separate. Questo modello, è facile scomporre la soluzione da carico di lavoro. È possibile eseguire l'applicazione web e le API nei piani di servizio App separati, in modo che possono essere ridimensionate in modo indipendente. Se non è necessario quel livello di scalabilità nella prima di tutto, è possibile distribuire le applicazioni nello stesso piano e spostarli in piani separati in seguito, se necessario.

- **Evitare di utilizzare la funzionalità di backup del servizio di App per eseguire il backup database SQL Azure.** In alternativa, usare [i backup automatizzato di Database SQL][sql-backup]. Copia di backup del servizio di App Esporta il database in un file di .bacpac SQL, che ha un costo DTUs.  

- **Distribuire un intervallo di gestione temporanea aperto.** Creare spazio distribuzione per gestione temporanea. Distribuire gli aggiornamenti di applicazione per l'intervallo di gestione temporanea aperto e verificare che la distribuzione prima di scambio nell'ambiente di produzione. Consente di ridurre il rischio di un aggiornamento errato nell'ambiente di produzione. Garantisce inoltre che tutte le istanze sono riscaldate prima che vengono scambiati nell'ambiente di produzione. Molte applicazioni hanno un riscaldamento significativa e l'ora di inizio debilitante. Per ulteriori informazioni, vedere [configurare ambienti per web apps in Azure App servizio di gestione temporanea](../app-service-web/web-sites-staged-publishing.md). 

-  **Creare spazio distribuzione per mettere in attesa di distribuzione (ultima) ultimo funzionante.** Quando si distribuisce un aggiornamento di produzione, spostare la distribuzione di produzione precedente nell'alloggiamento ultima. In questo modo più semplice ripristinare una distribuzione non corretto. Se viene rilevato un problema in un secondo momento, è possibile ripristinare rapidamente alla versione ultima. Per ulteriori informazioni, vedere [architettura di riferimento Azure: applicazione web base](guidance-web-apps-basic.md). 

- **Attivare la registrazione diagnostica**, tra cui la registrazione delle applicazioni e la registrazione server web. La registrazione è importante per il monitoraggio e diagnostica. Vedere [abilitare la registrazione diagnostica per App web di servizio App Azure](../app-service-web/web-sites-enable-diagnostic-log.md)

- **Registro nell'archiviazione blob**. In questo modo più semplice raccogliere e analizzare i dati. 

- **Creare un account di archiviazione separata per i registri.** Non usare lo stesso account di archiviazione per i registri e dati dell'applicazione. In questo modo per impedire la registrazione da riducendo le prestazioni dell'applicazione. 

- **Monitoraggio delle prestazioni.** Utilizzare un servizio, ad esempio [Relic nuovo](http://newrelic.com/) o [Applicazione approfondimenti](../application-insights/app-insights-overview.md) per monitorare le prestazioni dell'applicazione e il comportamento in condizioni di carico di monitoraggio delle prestazioni.  Monitoraggio delle prestazioni vengono fornite informazioni in tempo reale all'interno dell'applicazione. Consente di diagnosticare i problemi ed eseguire analisi delle cause principali di errori. 


###  <a name="application-gateway"></a>Gateway di applicazioni 

- **Effettuare il provisioning di almeno due istanze.** Distribuire applicazioni Gateway con almeno due istanze. Una singola istanza è un singolo punto di errore. Usare due o più istanze per la ridondanza e scalabilità. Per ottenere di [contratto di servizio](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_0/), è necessario eseguire il provisioning di due o più istanze di medie o più grande. 

### <a name="azure-search"></a>Ricerca di Azure

- **Effettuare il provisioning di più di una replica.** Utilizzare almeno due repliche per lette ad alta disponibilità o tre per disponibilità di lettura e scrittura.

- **Configurare indicizzatori per le distribuzioni più aree.** Se si dispone di una distribuzione di più paesi, prendere in considerazione le opzioni relative a continuità indicizzazione.

    - Se l'origine dati è replicato geografico, scegliere ogni indicizzatore di ogni servizio di ricerca di Azure internazionali della replica di origine dati locale.  

    - Se l'origine dati non è replicato geografico, scegliere più indicizzatori alla stessa origine dati, in modo che i servizi di ricerca di Azure in più aree in modo indipendente e continuamente indicizzare dall'origine dati. Per ulteriori informazioni, vedere [ricerca di Azure prestazioni e ottimizzazione considerazioni][search-optimization].

###  <a name="azure-storage"></a>Spazio di archiviazione Azure 

- **Per i dati dell'applicazione, utilizzare l'accesso in lettura geografico ridondanti archiviazione (RA GRS).** Spazio di archiviazione RA GRS replica i dati in un'area secondaria e fornisce accesso in sola lettura dall'area di secondario. Se c'è un servizio di archiviazione nell'area principale, l'applicazione è possibile leggere i dati dall'area di secondario. Per ulteriori informazioni, vedere [replica di archiviazione Azure](../storage/storage-redundancy.md).

- **Macchine Virtuali per dischi, utilizzare lo spazio di archiviazione Premium** Per ulteriori informazioni, vedere [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md).

- **Per l'archiviazione di coda, creare una coda di backup in un'altra area.** Per l'archiviazione di coda, una replica di sola lettura è limitato uso, perché non è possibile accodare o rimuovere elementi. Se, tuttavia, creare una coda di backup in un account di archiviazione in un'altra area. Se c'è un servizio di archiviazione, l'applicazione può utilizzare coda di backup, fino a quando l'area principale nuovamente disponibile. In questo modo, l'applicazione può comunque elaborare nuove convocazioni.  

###  <a name="documentdb"></a>DocumentDB 

- **Il database replicato tra aree geografiche.** Con un account di più paesi, il database DocumentDB ha area geografica di un'operazione di scrittura e più aree di lettura. Se si verifica un errore nell'area di scrittura, è possibile leggere da un'altra replica. Client SDK viene gestita automaticamente. Può anche eseguire il l'area di scrittura da un'altra area. Per ulteriori informazioni, vedere [dati Distribuisci globalmente con DocumentDB](../documentdb/documentdb-distribute-data-globally.md).


###  <a name="sql-database"></a>Database SQL 

- **Utilizzare livello Standard o Premium.** Questi livelli offrono un periodo più ripristinare in un momento (35 giorni). Per ulteriori informazioni, vedere [prestazioni e le opzioni del Database di SQL](../sql-database/sql-database-service-tiers.md).

- **Abilitare il controllo dei Database di SQL.** Il controllo può essere utilizzato per diagnosticare attacchi o errore umano. Per ulteriori informazioni, vedere [iniziare a utilizzare il controllo dei database SQL](../sql-database/sql-database-auditing-get-started.md). 

- **Replica di geografico Active utilizzo** Utilizzare la replica di geografico Active per creare leggibile secondaria in un'area diversa.  Se il database principale non riesce, o è sufficiente impostare la modalità offline, eseguire manuale caso di errore al database secondario.  Fino a quando non si riesce sopra, il database secondario rimane di sola lettura.  Per ulteriori informazioni, vedere [SQL attiva geografico-replica di Database](../sql-database/sql-database-geo-replication-overview.md). 

- **Sharding utilizzo**. Valutare la possibilità di utilizzo sharding per dividere il database in senso orizzontale. Sharding può fornire isolamento degli errori. Per ulteriori informazioni, vedere [proporzioni fuori con Database di SQL Azure](../sql-database/sql-database-elastic-scale-introduction.md). 

- **Utilizzare il ripristino in un momento di correggere un errore umano.**  Ripristinare in un momento restituisce il database in un momento precedente nel tempo. Per ulteriori informazioni, vedere [ripristinare un database di SQL Azure con il backup del database automatizzati][sql-restore].

- **Utilizzare geografico Ripristina per ripristinare un'interruzione del servizio.** Ripristino geografico consente di ripristinare un database da un backup ridondanti geografico.  Per ulteriori informazioni, vedere [ripristinare un database di SQL Azure con il backup del database automatizzati][sql-restore].


###  <a name="sql-server-running-in-a-vm"></a>SQL Server (in esecuzione in una macchina virtuale)

- **Il database replicato.** Usare SQL Server sempre nella disponibilità gruppi per il database replicato. Fornisce disponibilità se si verifica un errore di una sola istanza di SQL Server. Per ulteriori informazioni, vedere [altre informazioni...](guidance-compute-n-tier-vm.md) 

- **Il backup del database**. Se si utilizza già [Azure Backup](https://azure.microsoft.com/documentation/services/backup/) per eseguire il backup nelle macchine virtuali, valutare la possibilità di utilizzo del [Backup di Azure per carichi di lavoro di SQL Server utilizzando Data Protection Manager](../backup/backup-azure-backup-sql.md). Con questo approccio, non c'è un ruolo di amministratore di backup per l'organizzazione e una procedura di ripristino unificato per macchine virtuali e SQL Server. In caso contrario, utilizzare [SQL Server gestito Backup a Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). 


###  <a name="traffic-manager"></a>Gestore del traffico 

- **Eseguire il failback manuale.** Dopo aver caso di errore, il traffico Manager eseguire failback manuale, anziché automaticamente non funziona indietro. Prima dell'errore indietro, verificare che tutti i sottosistemi applicazione siano corretti.  In caso contrario, è possibile creare una situazione in cui l'applicazione capovolge avanti e indietro tra data center. Per ulteriori informazioni, vedere [Macchine virtuali in esecuzione in più aree](guidance-compute-multiple-datacenters.md).

- **Crea un endpoint di verifica dell'integrità**. Creare un endpoint personalizzato rapporti sullo stato generale dell'applicazione. In questo modo il traffico Manager eseguire in caso di errore qualsiasi percorso critico, non solo il front-end. L'endpoint deve restituire un codice di errore HTTP se qualsiasi dipendenza critica è danneggiato o non è raggiungibile. Non inviare una segnalazione errori per i servizi non critiche, tuttavia. In caso contrario, la verifica dell'integrità potrebbe impostare un trigger failover quando non è necessaria, creazione falsi. Per ulteriori informazioni, vedere [gestione di traffico endpoint monitoraggio e failover](../traffic-manager/traffic-manager-monitoring.md).


###  <a name="virtual-machines"></a>Macchine virtuali 

- **Evitare di eseguire un carico di lavoro di produzione in una singola macchina virtuale.** Una singola distribuzione di macchine Virtuali non è adattabile alle operazioni di manutenzione pianificata o non pianificato. Se, tuttavia, inserire più macchine virtuali in un set di disponibilità o un set di scala macchine Virtuali, con un bilanciamento del carico in primo piano. Per utilizzare il contratto di servizio, almeno due macchine virtuali deve essere distribuito nello stesso set di disponibilità. Per ulteriori informazioni, vedere [Panoramica sul set di scala macchina virtuale](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). 

- **Specificare la disponibilità impostata durante il provisioning macchina virtuale.** Al momento non è possibile aggiungere una VM Manager delle risorse a una disponibilità imposta dopo la macchina virtuale viene eseguito il provisioning. Quando si aggiunge una nuova macchina virtuale per una disponibilità esistente impostare, assicurarsi di creare una scheda di rete per la macchina virtuale e aggiungere la scheda di rete al pool di indirizzi di back-end nel sistema di bilanciamento del carico. In caso contrario, bilanciamento del carico non è possibile indirizzare il traffico di rete a quella macchina virtuale. 

- **Inserire ogni livello di applicazione in un Set di disponibilità separata.** In un'applicazione di più livelli, non inserirvi macchine virtuali da differenti livelli lo stesso set di disponibilità. Macchine virtuali di un set di disponibilità si trovano in domini guasto (FDs) e aggiornare i domini (UD). Tuttavia, per sfruttare le ridondanza FDs e UDs, ogni macchine Virtuali nel set di disponibilità devono avere la possibilità di gestire le richieste di client stesso. 

- **Scegliere le dimensioni di macchine Virtuali destra in base alle esigenze di prestazioni.** Quando si sposta un carico di lavoro esistente in Azure, iniziare con le dimensioni di macchine Virtuali che corrisponde maggiormente ai server locale. Quindi misurare le prestazioni del carico di lavoro effettivo per quanto riguarda disco IOPS CPU e memoria e, se necessario, modificare le dimensioni. In questo modo per assicurarsi che l'applicazione si comporta come previsto in un ambiente basato su cloud. Inoltre, se sono necessarie più schede di rete, tenere presente del limite di NIC per ogni dimensioni. 

- **Utilizzo dell'archiviazione premium per dischi rigidi virtuali.** Lo spazio di archiviazione di Azure Premium fornisce il supporto delle prestazioni ad alta, bassa latenza del disco. Per ulteriori informazioni, vedere [archiviazione Premium: High-Performance lo spazio di archiviazione per carichi di lavoro di Azure macchina virtuale](../storage/storage-premium-storage.md) scegliere una dimensione macchine Virtuali che supporta l'archiviazione premium. 

- **Creare un account di archiviazione separata per ogni macchina virtuale.** Posizione dischi rigidi virtuali per una macchina virtuale in un account di archiviazione separata. In questo modo per evitare di raggiungere i limiti IOPS per gli account di archiviazione. Per ulteriori informazioni, vedere [destinazioni prestazioni e scalabilità dello spazio di archiviazione Azure](../storage/storage-scalability-targets.md). Tuttavia, se si distribuisce un numero elevato di macchine virtuali, tenere presente il limite per abbonamento per gli account di archiviazione. Vedere [limiti di archiviazione](../azure-subscription-service-limits.md#storage-limits).

- **Creare un account di archiviazione separata per registri diagnostici**. Non scrivere registri diagnostici allo stesso account di archiviazione dischi rigidi virtuali, per evitare l'effetto di registrazione diagnostica IOPS per dischi macchine Virtuali. Un account di archiviazione in locale ridondanti (LRS) standard è sufficiente per registri diagnostici. 

- **Installare applicazioni su un disco di dati, non il sistema operativo.** In caso contrario, si potrebbe raggiungere il limite di dimensioni. 

- **Consente di eseguire il backup macchine virtuali Azure Backup.** Backup evitare perdite di dati accidentali. Per ulteriori informazioni, vedere [Proteggere Azure macchine virtuali con un archivio di servizi di recupero](../backup/backup-azure-vms-first-look-arm.md).

- **Abilitare i registri diagnostici**inclusi metriche di integrità di base, i registri dell'infrastruttura e [Avvio diagnostica][boot-diagnostics]. Avvio diagnostica consentono di diagnosticare un errore di avvio, se la macchina virtuale ottiene in uno stato non avvio. Per ulteriori informazioni, vedere [Panoramica di Azure registri diagnostici][diagnostics-logs].

- **Usare l'estensione AzureLogCollector**. (Solo macchine virtuali Windows). Questa estensione aggrega i registri di piattaforma Azure e li carica allo spazio di archiviazione Azure, senza l'operatore in remoto accedendo macchina virtuale. Per ulteriori informazioni, vedere [Estensione AzureLogCollector](../virtual-machines/virtual-machines-windows-log-collector-extension.md).


###  <a name="virtual-network"></a>Rete virtuale 

- **Proprietà consentite o blocco di indirizzi IP pubblici, aggiungere un NSG alla subnet.** Bloccare l'accesso da parte di utenti o consentire l'accesso solo dagli utenti che hanno privilegi per accedere all'applicazione.  

- **Creare una verifica dell'integrità personalizzato.** Analizza integrità servizio di bilanciamento del carico, è possibile testare HTTP o TCP. Se una macchina virtuale viene eseguito un server HTTP, verifica HTTP è un indicatore migliore dello stato di integrità di una verifica TCP. Per una verifica HTTP, utilizzare un endpoint personalizzato che segnala l'integrità generale dell'applicazione, incluse tutte le dipendenze critiche. Per ulteriori informazioni, vedere [Panoramica di bilanciamento del carico Azure](../load-balancer/load-balancer-overview.md).

- **Non bloccare la verifica dell'integrità.** Verifica dell'integrità di bilanciamento carico viene inviato da un indirizzo IP noto, 168.63.129.16. Non bloccare il traffico in o da questo IP in tutti i criteri del firewall o le regole di rete protezione gruppo (NSG). Verifica dello stato di blocco può provocare bilanciamento del carico rimuovere la macchina virtuale rotazione. 

- **Attivare la registrazione di bilanciamento del carico.** I log mostrano quanti macchine virtuali di back-end non ricevono il traffico di rete a causa delle risposte di verifica non riuscito. Per ulteriori informazioni, vedere [analitica del Registro di sistema di bilanciamento del carico Azure](../load-balancer/load-balancer-monitor-log.md).


<!-- links -->
[app-service-autoscale]: ../monitoring-and-diagnostics/insights-how-to-scale.md
[asynchronous-c-sharp]:https://msdn.microsoft.com/library/mt674882.aspx
[availability-sets]:../virtual-machines/virtual-machines-windows-manage-availability.md
[azure-backup]: https://azure.microsoft.com/documentation/services/backup/
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[circuit-breaker]: https://msdn.microsoft.com/library/dn589784.aspx
[cloud-service-autoscale]: ../cloud-services/cloud-services-how-to-scale.md
[diagnostics-logs]: ../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
[fma]: guidance-resiliency-failure-mode-analysis.md
[guidance-resilient-deployment]: guidance-resiliency-overview.md#resilient-deployment
[load-balancer]: load-balancer/load-balancer-overview.md
[monitoring-and-diagnostics-guidance]: ../best-practices-monitoring.md
[resource-manager]: ../azure-resource-manager/resource-group-overview.md
[retry-pattern]: https://msdn.microsoft.com/library/dn589788.aspx
[retry-service-guidance]: ../best-practices-retry-service-specific.md
[search-optimization]: ../search/search-performance-optimization.md
[sql-backup]: ../sql-database/sql-database-automated-backups.md
[sql-restore]: ../sql-database/sql-database-recovery-using-backups.md
[traffic-manager]: ../traffic-manager/traffic-manager-overview.md
[traffic-manager-routing]: ../traffic-manager/traffic-manager-routing-methods.md
[vmss-autoscale]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md
