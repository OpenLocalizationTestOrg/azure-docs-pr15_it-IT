<properties
    pageTitle="Livelli di coerenza in DocumentDB | Microsoft Azure"
    description="DocumentDB contiene quattro livelli di coerenza per consentire la coerenza eventuale saldo, disponibilità e latenza compromessi."
    keywords="eventuale coerenza, documentdb azure, Microsoft azure"
    services="documentdb"
    authors="syamkmsft"
    manager="jhubbard"
    editor="cgronlun"
    documentationCenter=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="syamk"/>

# <a name="consistency-levels-in-documentdb"></a>Livelli di coerenza in DocumentDB

Azure DocumentDB è progettato da zero con distribuzione globale in considerazione. Progettato per offrire garanzie prevedibili bassa latenza, un contratto di servizio disponibilità 99,99% e più modelli di ben coerenza Media. Al momento DocumentDB sono disponibili quattro livelli la coerenza: sessione sicura obsolescenza delimitata, e finale. Oltre a **sicuro** e la **coerenza eventuale** modelli comunemente offerte da altri database NoSQL DocumentDB inoltre offre due modelli la coerenza con attenzione codificata e operationalized – **delimitata obsolescenza** e **sessione**e ha convalidato la loro utilità rispetto a casi di utilizzo di scenari reali. Questi livelli quattro coerenza collettivamente consentono di rendere ben ponderato compromesso tra la coerenza, disponibilità e latenza. 

## <a name="scope-of-consistency"></a>Ambito della coerenza

L'ambito granularità coerenza è una richiesta di singolo utente. Una richiesta di scrittura potrebbe corrisponde a un Inserisci, Sostituisci, upsert o eliminare operazione (con o senza l'esecuzione di un trigger pre o post associato). O una richiesta di scrittura può corrispondere all'esecuzione di una routine JavaScript archiviati operativo su più documenti all'interno di una partizione transazione. Come per le operazioni di scrittura, un'operazione di lettura/query ha effetto solo a una richiesta di singolo utente. L'utente potrebbe essere necessario impaginazione su un grande set di risultati, che si estende su più partizioni, ma ogni leggere transazione nell'ambito di una singola pagina e served all'interno di una singola partizione.

## <a name="consistency-levels"></a>Livelli di coerenza

È possibile configurare un livello di coerenza predefinito nel proprio account di database che si applica a tutte le raccolte (per tutti i database) con il proprio account di database. Per impostazione predefinita, tutti lettura e le query eseguite su risorse definite dall'utente utilizzerà il livello di coerenza predefinito specificato per l'account di database. Tuttavia, è possibile ridurre il livello di coerenza di una richiesta di lettura/query specifica specificando l'intestazione della richiesta [[x ms-coerenza livello]](https://msdn.microsoft.com/library/azure/mt632096.aspx) . Esistono quattro tipi di livelli la coerenza supportati dal protocollo di replica DocumentDB che offrono un compromesso Cancella tra garanzie specifiche coerenza e prestazioni ottimali, come descritto di seguito.

![DocumentDB offre più, definiti anche la coerenza (Media) modelli disponibili][1]

**Sicuro**: 

- La coerenza sicura offre una garanzia [linearizability](https://aphyr.com/posts/313-strong-consistency-models) con legge garantiti per restituire la versione più recente di un documento. 
- La coerenza sicura garantisce che la scrittura è visibile solo dopo che si impegna in modo permanente da base maggior parte delle repliche. La scrittura è uno sincrono approvata in modo permanente principale e la base del database secondari oppure viene interrotta. Lettura è sempre riconosciuta dalla maggior parte leggere base, un client mai possibile vedere un'operazione di scrittura parziale o non salvate ed è sempre garantito per leggere la scrittura riconosciuta più recenti. 
- Account DocumentDB configurati per utilizzare la coerenza sicura non è possibile associare più aree di Azure con il proprio account DocumentDB. 
- Il costo di un'operazione di lettura (in termini di [unità richiesta](documentdb-request-units.md) consumate) con coerenza sicura è maggiore di sessione e finale, ma lo stesso obsolescenza limitata.
 

**Obsolescenza Bounded**: 

- Delimitata obsolescenza coerenza garanzie che legge potrebbero rimanere indietro scrive al massimo versioni *K* o prefissi di un documento o *t* intervallo di tempo. 
- Di conseguenza, quando la scelta delimitata obsolescenza, è possibile configurare "obsolescenza" in due modi: 
    - Numero di versioni *K* del documento per il quale legge rimanere indietro di scrittura
    - Intervallo di tempo *t* 
- Delimitata obsolescenza offerte totale ordine globale tranne all'interno della finestra di obsolescenza"". Si noti che la lettura monotona garantisce è presente in un'area interne ed esterne "obsolescenza finestra". 
- Obsolescenza limitata garantisce maggiore coerenza rispetto alla sessione o coerenza finale. Per le applicazioni distribuite a livello globale, è consigliabile che utilizzare obsolescenza limitata per gli scenari in cui si desidera coerenza sicura ma anche desidera 99,99% di disponibilità e bassa latenza. 
- Account DocumentDB configurati con coerenza obsolescenza limitata è possibile associare il proprio account DocumentDB di qualsiasi numero di aree Azure. 
- Il costo di un'operazione di lettura (in termini di destinatari consumate) con obsolescenza limitata è maggiore di sessione e la coerenza finale, ma diverso da quello di coerenza sicura.

**Sessione**: 

- A differenza dei modelli di uniformità globale offerti dai livelli la coerenza obsolescenza sicuro e delimitato, la coerenza sessione ambita è una sessione client. 
- La coerenza sessione è ideale per tutti gli scenari in cui una sessione di dispositivo o utente implicita poiché garantisce monotona legge, monotona scrittura e lettura garantisce propri scrive (RYW). 
- La coerenza sessione fornisce coerenza prevedibile per una sessione e massimo leggere velocità offrendo il minimo latenza lettura e scrittura. 
- Account DocumentDB configurati con coerenza sessione è possibile associare il proprio account DocumentDB di qualsiasi numero di aree Azure. 
- Il costo di un'operazione di lettura (in termini di destinatari consumata) a livello coerenza sessione è obsolescenza meno sicuro e delimitato, ma la coerenza più finale
 

**Eventuale**: 

- La coerenza eventuale garantisce che in assenza di qualsiasi ulteriori operazioni di scrittura, repliche all'interno del gruppo infine convergerà. 
- La coerenza finale è la forma più debole di coerenza nel punto in cui un client può ottenere i valori che risalgono a quelli che aveva prima.
- La coerenza eventuale offre la coerenza lettura più debole ma offre la latenza più bassa di lettura e scrittura.
- Account DocumentDB configurati con coerenza finale è possibile associare il proprio account DocumentDB di qualsiasi numero di aree Azure. 
- Il costo di un'operazione di lettura (in termini di destinatari consumate) con la coerenza eventuale è minore di tutti i livelli di coerenza DocumentDB livello.


## <a name="consistency-guarantees"></a>Garanzie di coerenza

Nella tabella seguente acquisisce vari garanzie coerenza corrispondente ai livelli di quattro coerenza.

| Garanzia                                                         |    Sicuro                                       |    Obsolescenza limitata                                                                           |    Sessione                                       |    Eventuale                                 |
|----------------------------------------------------------|-------------------------------------------------|------------------------------------------------------------------------------------------------|--------------------------------------------------|--------------------------------------------------|
|    **Totale ordine globale**                                |    Sì                                          |    Sì, all'esterno della finestra di obsolescenza""                                                      |    No, ordine parziale "sessione"                   |    No                                            |
|    **Garanzia prefisso coerenti**                       |    Sì                                          |    Sì                                                                                         |    Sì                                           |    Sì                                           |
|    **Lettura monotona**                                   |    Sì                                          |    Sì, tra le aree all'esterno della finestra obsolescenza e in un'area sempre questa impostazione.     |    Sì, per la sessione specificata                    |    No                                            |
|    **Scrittura monotona**                                  |    Sì                                          |    Sì                                                                                         |    Sì                                           |    Sì                                           |
|    **Leggere la scrittura**                                  |    Sì                                          |    Sì                                                                                         |    Sì (nell'area di scrittura)                      |    No                                            |


## <a name="configuring-the-default-consistency-level"></a>La configurazione del livello di uniformità predefinita

1.  Nel [portale di Azure](https://portal.azure.com/), in Jumpbar, fare clic su **DocumentDB (NoSQL)**.

2. In e il **DocumentDB (NoSQL)** , selezionare l'account di database per la modifica.

3. E il conto, fare clic su **predefinito coerenza**.


4. In e **l'Uniformità predefinita** , selezionare il nuovo livello di coerenza e fare clic su **Salva**.

    ![Schermata per evidenziare l'icona impostazioni e la voce di uniformità predefinita](./media/documentdb-consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Livelli di coerenza per le query

Per impostazione predefinita per le risorse definite dall'utente, il livello di uniformità per le query è allo stesso livello coerenza per legge. Per impostazione predefinita, l'indice viene aggiornato in modo sincrono in ogni Inserisci, Sostituisci o eliminazione di un documento alla raccolta. In questo modo le query rispettare allo stesso livello coerenza a quella di lettura documento. Mentre DocumentDB scrittura ottimizzato e supporta volumi velocità di scrittura di documento, la manutenzione degli indici icona del e la gestione delle query coerente, è possibile configurare determinate raccolte per aggiornare l'indice lentamente. L'indicizzazione Prato ulteriormente ottimizzare le prestazioni di scrittura ed è ideale per gli scenari di acquisizione di massa quando un carico di lavoro è principalmente Incolla di lettura.  

Modalità di indicizzazione|  Legge|  Query  
-------------|-------|---------
Coerenti (impostazione predefinita)|   Selezionare dalla obsolescenza sicuro, delimitato, sessione, o finale|    Selezionare dalla obsolescenza sicuro, delimitato, sessione, o finale|
Lenta|   Selezionare dalla obsolescenza sicuro, delimitato, sessione, o finale|    Eventuale  

Come con le richieste di lettura, è possibile ridurre il livello di coerenza di una richiesta di query specifica specificando l'intestazione della richiesta di [livello la coerenza di ms x](https://msdn.microsoft.com/library/azure/mt632096.aspx) .

## <a name="next-steps"></a>Passaggi successivi

Se si desidera eseguire ulteriori informazioni sui livelli la coerenza e compromessi, è consigliabile le risorse seguenti:

-   Doug Giorgi. La coerenza dei dati replicato spiegazione tramite baseball (video).   
[https://www.YouTube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
-   Doug Giorgi. Vengono illustrati la coerenza dei dati replicato attraverso baseball.   
[http://Research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.PDF](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
-   Doug Giorgi. Garanzie sessione per debole dati replicati coerenti.   
[http://DL.ACM.org/Citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
-   Daniel Abadi. La coerenza compromessi nella moderna distribuite sistemi struttura del Database: CAP è solo una parte del brano ".   
[http://computer.org/CSDL/mags/CO/2012/02/mco2012020037-Abs.HTML](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
-   Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Stoica ione. Probabilistica delimitata obsolescenza (PBS) per filtrare quorum parziale.   
[http://VLDB.org/pvldb/vol5/p776_peterbailis_vldb2012.PDF](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
-   Vogels Werner. Eventuale coerente - aggiornamento.    
[http://allthingsdistributed.com/2008/12/eventually_consistent.HTML](http://allthingsdistributed.com/2008/12/eventually_consistent.html)


[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png
