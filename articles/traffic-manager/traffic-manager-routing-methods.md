<properties
    pageTitle="Il traffico Manager - traffico metodi di routing | Microsoft Azure"
    description="In questo articolo verrà illustrati i metodi di routing il traffico diversi usati per il traffico Manager"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-traffic-routing-methods"></a>Metodi di routing del traffico gestore del traffico

Gestore del traffico Azure supporta tre metodi di routing del traffico per determinare come indirizzare il traffico di rete per gli endpoint del servizio diversi. Gestore del traffico applicato il metodo di routing del traffico a ogni query DNS che riceve. Il metodo di routing del traffico determina quale endpoint restituiti in una risposta DNS.

La gestione di risorse Azure del supporto tecnico per il traffico Manager utilizza terminologia diversa rispetto al modello di distribuzione classica. Nella tabella seguente vengono illustrate le differenze tra i termini di Manager delle risorse e classica:

| Termini di Manager delle risorse | Termini classica |
|-----------------------|--------------|
| Metodo di routing del traffico | Metodo di bilanciamento del carico |
| Metodo di priorità | Metodo di failover |
| Metodo Media ponderata | Metodo circolari |
| Metodo di prestazioni | Metodo di prestazioni |

In base al feedback dei clienti, è stata modificata la terminologia per migliorare la nitidezza e ridurre malintesi comuni. Non vi è alcuna differenza funzionalità.

Sono disponibili tre metodi di routing il traffico in gestore del traffico:

- **Priorità:** Selezionare 'Priority' quando si desidera utilizzare un endpoint del servizio principale per tutto il traffico e fornire backup nel caso in cui il principale o i punti finali di backup non sono disponibili.
- **Ponderato:** Selezionare 'ponderato"quando si desidera distribuire il traffico su un set di punti finali, in modo uniforme o in base a fattori, quali si definire.
- **Prestazioni:** Selezionare 'Prestazioni' dopo avere endpoint in diverse aree geografiche e si desidera agli utenti finali di utilizzare l'endpoint "più vicina" in termini della latenza di rete più bassa.

Tutti i profili di gestore del traffico includono il monitoraggio dell'endpoint salute e failover automatica degli endpoint. Per ulteriori informazioni, vedere [Gestione di traffico Endpoint monitoraggio](traffic-manager-monitoring.md). Un singolo profilo di gestore del traffico è possibile usare uno dei metodi di routing il traffico. È possibile selezionare un metodo di routing traffico diverso per il proprio profilo in qualsiasi momento. Le modifiche vengono applicate all'interno di un minuto e non viene sostenuto tempi di inattività. Metodi di routing del traffico possono essere combinati utilizzando profili gestore del traffico annidate. Annidamento consente sofisticate e flessibile configurazioni di routing del traffico che soddisfano le esigenze di applicazioni complesse. Per ulteriori informazioni, vedere [i profili di gestore del traffico annidate](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Metodo di routing del traffico priorità

Spesso un'organizzazione desidera fornire l'affidabilità per i servizi distribuendo uno o più servizi backup nel caso in cui il servizio principale non è raggiungibile. Il metodo di routing del traffico 'Priority' consente ai clienti Azure di implementare facilmente questo modello di failover.

![Gestore del traffico Azure 'Priority' metodo di routing del traffico][1]

Il profilo di gestore del traffico contiene un elenco priorità endpoint del servizio. Per impostazione predefinita, il traffico Manager invia tutto il traffico all'endpoint (massima priorità) principale. Se l'endpoint di principale non è disponibile, il traffico Manager indirizza il traffico al secondo endpoint. Se entrambi gli endpoint primari e secondari non sono disponibili, il traffico per la terza e così via. Disponibilità dell'endpoint si basa su stato configurato (attivato o disattivato) e il monitoraggio in corso endpoint.

### <a name="configuring-endpoints"></a>Configurazione di endpoint

Azure Gestione risorse, configurare la priorità endpoint in modo esplicito utilizzando la proprietà 'priority' per ogni endpoint. Questa proprietà è un valore compreso tra 1 e 1000. I valori più bassi rappresentano una priorità più alta. I punti finali non è possibile condividere i valori di priorità. L'impostazione della proprietà è facoltativo. Quando viene omesso, viene utilizzata una priorità predefinita in base all'ordine di endpoint.

Con l'interfaccia classica, la priorità endpoint è stata configurata in modo implicito. La priorità si basa sull'ordine in cui sono elencati i punti finali nella definizione del profilo.

## <a name="weighted-traffic-routing-method"></a>Metodo di routing del traffico Media ponderata

Il metodo di routing del traffico 'Ponderato' consente di distribuire il traffico in modo uniforme o usare un peso predefinito.

![Azure il traffico 'Ponderata' routing del traffico metodo Manager][2]

Nel metodo di routing del traffico Media ponderato, assegnare uno spessore di ogni punto finale della configurazione di profilo di gestore del traffico. Il peso è un numero intero da 1 a 1000. Questo parametro è facoltativo. Se omesso, il traffico responsabili utilizzato il valore predefinito di "1".

Per ogni query DNS ricevuti, il traffico Manager sceglie in modo casuale un endpoint. La probabilità di scelta di un estremo dipende dal peso assegnato a tutti gli endpoint disponibili. Utilizzare lo stesso peso in tutti i risultati di endpoint di una distribuzione di traffico pari. Utilizzando spessori superiori o inferiori in endpoint specifici, gli endpoint da restituire più o meno frequentemente nelle risposte DNS.

Il metodo ponderato attiva alcuni scenari utili:

- Aggiornamento dell'applicazione graduale: allocare una percentuale di traffico per instradare a un nuovo endpoint e aumentare gradualmente il traffico nel tempo superiore al 100%.
- Migrazione delle applicazioni in Azure: creare un profilo con i punti finali Azure ed esterni. Modificare lo spessore dei punti finali al preferisce nuovi endpoint.
- La suddivisione cloud per capacità aggiuntiva: espandere rapidamente una distribuzione in locale nel cloud, inserirlo dietro un profilo di gestore del traffico. Quando è necessario capacità nel cloud, è possibile aggiungere o abilitare più endpoint e specificare quale parte del traffico passa a ogni endpoint.

Il nuovo portale Azure supporta la configurazione del routing del traffico ponderato. Non è possibile configurare spessori nel portale di classica. È inoltre possibile configurare spessori utilizzando le versioni classiche di PowerShell di Azure, CLI e le API REST Manager delle risorse.

È importante tenere presente che le risposte DNS vengono memorizzati nella cache dai client e ai server DNS ricorsive i client consente di risolvere i nomi DNS. La memorizzazione nella cache possono avere un impatto sulle distribuzioni il traffico ponderata. Quando il numero di client e server DNS ricorsive è grande, distribuzione il traffico funziona come previsto. Tuttavia, quando il numero di client e server DNS ricorsive è piccolo, memorizzazione nella cache possibile notevolmente inclinare la distribuzione di traffico.

Casi di utilizzo comuni includono:

- Ambienti di sviluppo e test
- Comunicazioni all'applicazione
- Applicazioni destinate a un'utente-base stretta che condividono un'infrastruttura DNS ricorsiva comune (ad esempio, dipendenti della società connessione tramite un proxy)

Questi effetti di memorizzazione nella cache DNS sono comuni a tutti sistemi routing del traffico basata su DNS, non solo Azure il traffico Manager. In alcuni casi, in modo esplicito cancellare la cache DNS può fornire una possibile soluzione. In altri casi, un metodo di routing del traffico alternativo potrebbe essere più adatto.

## <a name="performance-traffic-routing-method"></a>Metodo di routing del traffico delle prestazioni

Distribuire i punti finali in due o più posizioni in tutto il mondo migliorare i tempi di risposta di molte applicazioni, il traffico routing nella posizione più vicina all'utente. Il metodo di routing del traffico 'Prestazioni' fornisce questa funzionalità.

![Gestore del traffico Azure 'Prestazioni' metodo di routing del traffico][3]

L'endpoint "più vicina" non è necessariamente più vicina misurati sulla base distanza geografica. Se, tuttavia, il metodo di routing del traffico 'Prestazioni' determina l'endpoint più vicina per la misurazione della latenza di rete. Gestore del traffico mantiene una tabella di latenza Internet per tenere traccia del tempo di andata e ritorno tra intervalli di indirizzi IP e ogni Data Center di Azure.

Gestore del traffico Cerca l'indirizzo IP di origine della richiesta di DNS in arrivo nella tabella latenza Internet. Gestore del traffico sceglie un endpoint in Data Center Azure ha la latenza più bassa per tale intervallo di indirizzi IP e quindi restituisce l'endpoint di risposta DNS.

Come [Gestore del traffico funzionamento](traffic-manager-how-traffic-manager-works.md), il traffico Manager non riceve query DNS direttamente dai client. Invece query DNS provengono dal servizio DNS ricorsive che i client siano configurati per l'utilizzo. Di conseguenza, l'indirizzo IP utilizzati per determinare l'endpoint "più vicina" non è indirizzo IP del client, ma è l'indirizzo IP del servizio DNS ricorsiva. In pratica, l'indirizzo IP è un buono proxy per il client.

Gestore del traffico aggiorna regolarmente la tabella di latenza Internet per includere le modifiche apportate in Internet globale e nuove aree Azure. Le prestazioni dell'applicazione variano in base alle varianti in tempo reale in caricamento in Internet. Routing del traffico prestazioni non esegue il monitoraggio carica un determinato endpoint del servizio. Tuttavia, se un endpoint è disponibile, il traffico Manager indica non nelle risposte alle query DNS.

Punti da tenere presente:

- Se il profilo contiene più endpoint nella stessa regione Azure, quindi il traffico Manager distribuisce il traffico in modo uniforme tra i punti finali disponibili in quell'area. Se si preferisce una distribuzione di traffico diversi in un'area, è possibile utilizzare [profili di gestore del traffico annidate](traffic-manager-nested-profiles.md).

- Se tutti gli endpoint abilitati in una determinata regione Azure sono inferiori, il traffico Manager distribuisce il traffico tra altri endpoint disponibili anziché l'endpoint più vicino. Questa logica impedisce un errore a cascata non overload endpoint più vicino. Se si desidera definire una sequenza di failover preferito, utilizzare i [profili di gestore del traffico annidate](traffic-manager-nested-profiles.md).

- Quando si utilizza il metodo di routing il traffico di prestazioni con endpoint esterni o i punti finali annidati, è necessario specificare la posizione di tali endpoint. Selezionare il paese Azure più vicino alla distribuzione. Questi percorsi sono i valori supportati dalla tabella latenza Internet.

- Algoritmo scelto il punto finale è deterministico. Esecuzione di query DNS ripetute dallo stesso client indirizzamento allo stesso endpoint. Client in genere, usano i server DNS ricorsive diversi quando in viaggio. Il cliente può essere inviato a un altro endpoint. Routing inoltre può essere modificato con gli aggiornamenti alla tabella latenza Internet. Il metodo di routing del traffico le prestazioni di conseguenza, non garantisce che un client viene sempre instradato allo stesso endpoint.

- Internet latenza modifiche alla tabella, è possibile che alcuni client siano indirizzati a un altro endpoint. Questa modifica routing è più accurata in base a dati di latenza corrente. Gli aggiornamenti sono essenziali per mantenere la precisione di routing del traffico prestazioni come Internet in continua evoluzione.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come sviluppare applicazioni disponibilità utilizzando [il traffico Manager endpoint monitoraggio](traffic-manager-monitoring.md)

Informazioni su come [creare un profilo di gestore del traffico](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
