<properties
   pageTitle="Scenari comuni di Azure catalogo dati | Microsoft Azure"
   description="Panoramica degli scenari comuni per Azure catalogo dati, incluse la registrazione e individuazione delle origini dati di valore elevato, l'abilitazione di business intelligence in modalità self-service e l'acquisizione di conoscenze esistenti sulle origini dati e processi."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/03/2016"
   ms.author="maroche"/>


# <a name="azure-data-catalog-common-scenarios"></a>Scenari comuni di Azure catalogo dati

In questo articolo vengono presentati scenari comuni in catalogo dati di Azure consente alle organizzazioni per ottenere il massimo da origini dati esistenti.

## <a name="scenario-1---registration-of-central-data-sources"></a>Scenario 1 # - registrazione delle origini dati centrale

Organizzazioni spesso contengono un numero di origini dati di valore elevato. Le origini dati includono linee di sistemi OLTP aziendali, data warehouse e funzionalità di business intelligence / database analitica. Spesso l'aumento del numero di sistemi e la sovrapposizione tra i sistemi, nel tempo in base alle esigenze dell'evolve business e come dell'azienda stessa evolversi da fusione e acquisizioni.

Spesso è difficile per gli utenti di sapere dove si trova i dati all'interno di tali origini dati. Domande come queste sono tutte troppo comuni:

- Dei sistemi HR tre utilizzati all'interno della società, che da utilizzare per creare questo tipo di report
- Nel punto in cui è consigliabile passare per ottenere i numeri di vendita certificati anno fiscale appena interrotta?
- Chi è consigliabile chiedere o Qual è la procedura da utilizzare per ottenere l'accesso al data warehouse?
- Non si sa se questi numeri sono destro-che è possibile fare per informazioni su come questi dati dovrebbe essere usate in precedenza si condivide il dashboard con il team?

In questo scenario consente catalogo dati di Azure. Le origini dati centrale, valore elevato, gestite IT utilizzati in tutta l'organizzazione sono spesso di punto di partenza logico per compilare il catalogo. Anche se tutti gli utenti possono registrare un'origine dati, con il catalogo kick-started con le origini dati che è probabile fornire valore per il numero massimo di utenti aiuta a unità adozione e l'utilizzo del sistema. Per i clienti di introduzione a catalogo dati di Azure, identificazione e la registrazione le origini dati principali utilizzate per molti team dei consumatori dati può essere il primo passaggio per il successo.

Questo scenario presenta anche un'opportunità per annotare le origini dati di alto valore in modo da semplificarne comprendere e accedere. Un aspetto fondamentale di questa attività è per includere informazioni su come gli utenti possono richiedere l'accesso all'origine dati. Catalogo dati di Azure consente agli utenti di fornire l'indirizzo di posta elettronica dell'utente o team responsabile per il controllo dell'accesso ai dati di origine, collegamenti a strumenti esistenti o documenti o testo libero che descrive il processo di richiesta di accesso. Con queste informazioni inclusi nel catalogo, gli utenti che hanno alla scoperta di origini dati registrate, ma che non dispongono ancora delle autorizzazioni per accedere ai dati facilmente possono richiedere l'accesso con i processi definiti e applicando i proprietari di origine dati.

## <a name="scenario-2---self-service-business-intelligence"></a>Scenario #2: business intelligence in modalità self-service

Anche se le soluzioni di business intelligence tradizionale aziendali continuano a essere parte di paesaggi dati molte organizzazioni, la modifica il passo delle business ha reso Business Intelligence in modalità self-service più importanti. Business Intelligence in modalità self-service consente gli information worker e analisti per creare le proprie report, le cartelle di lavoro e dashboard senza basarsi su un team IT centrale – o vengano limitate dalla pianificazione e la disponibilità di tale team IT.

In modalità self-servizi scenari di Business Intelligence, è comune per gli utenti combinare i dati da più origini, molte delle quali potrebbe non ha ancora utilizzate per Business Intelligence e analisi. Sebbene sia possibile già noti alcune di queste origini dati, è spesso un processo per scoprire quali devono essere effettuati per individuare e valutare potenziali origini dati per una determinata attività.

In genere, il processo di rilevamento è uno manuale: analisti utilizzerà le connessioni di rete peer per identificare altre persone che lavorano con i dati cercati. Una volta trovata un'origine dati può essere utilizzato, ma il processo si ripete nuovamente ogni successive modalità self-service BI basata, con più utenti che eseguono lo stesso processo manuale ridondante di individuazione.

Con catalogo dati di Azure, gli utenti possono causare il danneggiamento questo ciclo di attività ridondanti. Una volta un'origine dati è stata rilevata in modo tradizionale, gli analisti possono registrare l'origine dati per renderlo più facilmente individuabili in futuro. Anche se l'utente può aggiungere più valore attraverso l'annotazione le risorse dati registrato, questo non è necessario eseguita contemporaneamente la registrazione. Gli utenti possono contribuire nel tempo, come permesso le pianificazioni, gradualmente alcun valore aggiunto le origini dati registrate nel catalogo.

Questa crescita strutturale del contenuto del catalogo è una naturale complementare per la registrazione all'inizio delle origini dati centrale. L'immissione automatica del catalogo con i dati necessari molti utenti può essere una forte motivazione per l'uso iniziale e individuazione. Per consentire agli utenti di registrare e aggiungere annotazioni aggiuntive può essere un modo per mantenere e colleghi, viva l'attenzione.

È anche importante notare che anche se questo scenario vengono illustrate specificamente Business Intelligence in modalità self-service, i motivi e i problemi legati alla stessa applicata su larga scala aziendali progetti BI anche. Le attività che prevede un processo manuale di individuazione di origine dati è un'attività che possono risultare per l'organizzazione mediante l'utilizzo del catalogo dati di Azure.

## <a name="scenario-3---capturing-tribal-knowledge"></a>Scenario 3 # - acquisizione conoscenze

Come si conoscono i dati che è necessario eseguire il processo e dove trovare i dati?

Se è stato il lavoro per un po' di tempo, è probabile che sia sufficiente conoscere. È stato effettuato un graduale processo di apprendimento e nel tempo state fornite informazioni sulle origini dati che sono fondamentali per il lavoro giorno a altro.

Quando si aggiunge un nuovo dipendente del team, come si riconosce i contenuti i dati che deve eseguire la sua mansione e dove trovarlo?

Probabilità sono chiede è.

Il trasferimento in corso delle conoscenze fa parte del processo di rilevamento di origine dati in piccole e grandi aziende. Più i membri del team senior ed esperto compilata knowledge nel corso degli anni e i membri del team più recenti sono inoltre appreso come chiedere al meglio se hanno domande. Le informazioni più importanti spesso disponibile solo in capi di alcuni utenti chiavi e quando le persone sono in vacanza o lasciare il team, non presenta l'organizzazione.

In alcuni casi questi esperti di dati consentono di rendere l'impegno per documentare conoscenza, condividerlo tramite posta elettronica o nei documenti di Word in un sito di SharePoint del team. Anche se può essere utile, che introduce un nuovo problema individuazione-persone sapere quali documentazione esiste e dove trovarlo...

Catalogo dati di Azure offre una posizione per la condivisione di questo conoscenze e per rendere facilmente individuabili. Dati esperti direttamente possono aggiungere note alle risorse di dati e possono essere inclusi anche i collegamenti alla documentazione esistente. Non solo questo acquisire la conoscenza stesso, ma comporta inoltre la conoscenza all'esperienza stessa che viene utilizzato per l'individuazione di origine dati. Quando si tenta di utilizzare il catalogo per individuare un origine dati non solo verranno ha trovare all'origine, si troverà knowledge dell'esperto che in precedenza erano presenti solo in considerazione dell'esperto a se stesso.
