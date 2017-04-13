<properties
   pageTitle="Distribuire i dati a livello globale con DocumentDB | Microsoft Azure"
   description="Informazioni sul ripristino di replica geografico, failover e dati di scala mondo utilizzando database globale di Azure DocumentDB, un servizio di database NoSQL completamente gestito."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuire i dati a livello globale con DocumentDB

> [AZURE.NOTE] Distribuzione globale dei database DocumentDB è generalmente disponibile e attivata automaticamente per tutti gli account DocumentDB appena creati. Stiamo lavorando per abilitare la distribuzione globale per tutti gli account esistenti, ma nel frattempo, se si desidera distribuzione globale abilitata nel proprio account, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) e si verrà attivarla per l'ora.

Azure DocumentDB è progettato per soddisfare le esigenze di applicazioni IoT composta da milioni di dispositivi distribuiti a livello globale e le applicazioni di scala internet forniscono esperienze tempestivo agli utenti in tutto il mondo. Questi sistemi di database affrontano per ottenere accesso bassa latenza ai dati dell'applicazione da più aree geografiche con garanzie coerenza e la disponibilità di dati ben definito. Come un sistema di database distribuita a livello globale, DocumentDB semplifica la distribuzione globale dei dati offrendo account database completamente gestita più aree che forniscono Cancella compromessi tra la coerenza, disponibilità e prestazioni ottimali, tutti con garanzie corrispondente. Account di database DocumentDB sono disponibili con disponibilità, latenza ms cifra singola, più [livelli di coerenza ben] [consistency], failover internazionali trasparente con API più assegnazione di una posizione e la possibilità di scalare illimitate velocità e lo spazio di archiviazione in tutto il mondo. 

  
## <a name="configuring-multi-region-accounts"></a>Configurazione di account più aree

Configurare il proprio account DocumentDB scalare in tutto il mondo può essere gestito in meno di un minuto tramite il [portale di Azure](documentdb-portal-global-replication.md). È sufficiente è selezionare il livello di coerenza destra tra diversi livelli di coerenza ben supportati e associare qualsiasi numero di aree Azure con l'account di database. Livelli di coerenza DocumentDB offrono Cancella compromesso tra garanzia di coerenza specifico e prestazioni. 

![DocumentDB offre più, definiti anche la coerenza (Media) modelli disponibili][1]

DocumentDB offre più, definiti anche la coerenza (Media) modelli tra cui scegliere.

Selezionare il livello di coerenza destra dipende dai dati coerenza garantire le esigenze dell'applicazione. DocumentDB automaticamente replica i dati in tutte le aree specificate e garantisce la coerenza selezionato per il proprio account di database. 


## <a name="using-multi-region-failover"></a>Utilizzo di più paesi failover 

Azure DocumentDB è in grado di failover trasparente account di database in più regioni Azure: le nuove [API più assegnazione di una posizione] [ developingwithmultipleregions] garanzia che l'app è possibile continuare a usare un endpoint logico e viene interrotta grazie al failover. Failover viene controllato dall'utente, che forniscono della flessibilità necessaria per spostare il database account nell'evento uno degli intervalli di condizioni di errore possibili si verificano, inclusi applicazione, infrastruttura, servizio o errori internazionali (reali o simulati). In caso di errore internazionali DocumentDB, il servizio non riuscirà trasparente sull'account di database e l'applicazione continua per accedere ai dati senza perdere la disponibilità. Mentre DocumentDB offre [disponibilità del 99,99% contratti di servizio][sla], è possibile testare end dell'applicazione di proprietà disponibilità end tramite la simulazione, sia [a livello di programmazione] di un errore internazionali[ arm] nonché come tramite il portale Azure.


## <a name="scaling-across-the-planet"></a>Ridimensionamento parte del mondo
DocumentDB consente di effettuare il provisioning di velocità in modo indipendente e utilizzare lo spazio di archiviazione per ciascuna raccolta DocumentDB in qualsiasi scala globalmente tra tutte le aree associate all'account di database. Un insieme di DocumentDB viene automaticamente distribuito globalmente e gestito tra tutte le aree associati all'account di database. Raccolte all'interno di account database possono essere distribuite in una delle regioni Azure in cui il [servizio DocumentDB è disponibile][serviceregions]. 

La velocità acquistato e archiviazione utilizzato per ogni raccolta DocumentDB è il provisioning automatico tra tutte le aree identico. In questo modo, l'applicazione per adattarsi perfettamente globo [acquistati solo la velocità e lo spazio di archiviazione in uso all'interno di ogni ora][pricing]. Ad esempio, se si hanno effettuato il provisioning di 2 milioni di destinatari per una raccolta di DocumentDB, quindi ognuna delle aree geografiche associate all'account di database ottiene 2 milioni di destinatari per la raccolta. Come illustrato di seguito.

![Ridimensionamento velocità per una raccolta di DocumentDB in quattro aree][2]

DocumentDB garantisce < 10 ms Latenza lettura e < 15 ms scrittura in P99. Le richieste di lettura si estendono mai bordo al Data Center per garantire i [requisiti di uniformità è stato selezionato][consistency]. Le operazioni di scrittura sono sempre base approvata localmente prima vengono riconosciuti ai client. Ogni account di database è configurato con priorità di area di scrittura. L'area designata con priorità più alta agirà come area di scrittura corrente per l'account. Tutti i SDK indirizza trasparente database account scrive all'area di scrittura corrente. 

Infine, poiché DocumentDB è completamente [indipendente dal schema] [ vldb] -non è mai necessario preoccuparsi di gestione/aggiornamento di schemi o gli indici secondari in più Data Center. Le [query SQL] [ sqlqueries] durante l'applicazione e modelli di dati continuano ad evolversi continuare a lavorare. 


## <a name="enabling-global-distribution"></a>Attivazione di distribuzione globale 

È possibile scegliere di rendere i dati in locale o globale distribuiti un associando uno o più aree Azure con un DocumentDB database account. Aggiungere o rimuovere le aree al proprio account di database in qualsiasi momento. Per abilitare la distribuzione globale tramite il portale, informazioni su [come eseguire la replica di database globale DocumentDB tramite il portale di Azure](documentdb-portal-global-replication.md). Per abilitare la distribuzione globale a livello di programmazione, vedere [sviluppo di applicazioni con gli account DocumentDB più aree](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sulla distribuzione dei globalmente con DocumentDB nei seguenti articoli:

* [Il provisioning di velocità e lo spazio di archiviazione per una raccolta] [throughputandstorage]
* [Assegnazione di una posizione più API tramite resto. .NET, Java, Python e nodo SDK] [developingwithmultipleregions]
* [Livelli di coerenza in DocumentDB] [consistency]
* [Contratti di servizio disponibilità] [sla]
* [Gestione di account del database] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

