<properties 
    pageTitle="Inoltro Azure ibrido connessioni protocollo | Microsoft Azure"
    description="zure inoltro ibrido connessioni protocollo Guida."
    services="service-bus"
    documentationCenter="na"
    authors="clemensv"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="sethm" />

# <a name="azure-relay-hybrid-connections-protocol"></a>Inoltro Azure ibrido connessioni protocollo

Inoltro Azure corrisponde a uno dei pilastri funzionalità principali della piattaforma Bus di servizio Azure. Funzionalità di "Ibrido connessioni" nuova dell'inoltro è un'evoluzione sicura, Apri protocollo basata su HTTP e WebSockets.

Che sostituisce il primo identico denominata funzionalità "Servizi BizTalk" è stata creata in una base protocollo proprietario. L'integrazione delle caratteristiche ibride connessioni Servizi App di Windows Azure continuerà a funzionare come-è.

"Ibrido connessioni" consente la comunicazione bidirezionale, binario flusso tra i due applicazioni di rete, in base al quale una o entrambe le parti può trovarsi dietro NAT o firewall.

In questo documento vengono descritte le interazioni lato client con l'inoltro di connessioni ibrido per la connessione client mittente e comunicare ascoltatore ruoli e come listener accettano nuove connessioni.

## <a name="interaction-model"></a>Modello di interazione

Inoltro connessioni ibrido si connette due parti fornendo un punto rendezvous nel cloud Azure che entrambe le parti consentono di individuare e connettersi a dal punto di vista della propria rete. Tale punto rendezvous è denominato "Ibrido della connessione" in questo e altri documentazione nelle API e nel portale di Azure. Endpoint del servizio ibrido connessioni verrà considerato "servizio" per il resto di questo documento.

Il modello di interazione tende a nomenclatura stabilita da molte altre API reti seguire:

Esiste un comunicare ascoltatore innanzitutto indica preparazione per gestire le connessioni in ingresso e successivamente verranno accettati man mano che arrivano. Su altro lato, non esiste un client di connessione che si connetterà comunicare ascoltatore, che prevede la connessione a accettata per la creazione di un percorso di comunicazione bidirezionali. "Connetti", "Ascolto", "Accetta" è gli stessi termini che sarà possibile trovare nella maggior parte delle API di socket.

Qualsiasi modello di comunicazione inoltrati è una delle due parti stabilire connessioni in uscita verso un endpoint del servizio, che consente di comunicare "ascoltatore" anche "client" in uso stilistici e potrebbe anche altri overload terminologia; la terminologia precisa che serve pertanto per le connessioni ibrido è il seguente:

Le applicazioni su entrambi i lati di una connessione sono denominate "client", poiché sono client al servizio. Client in attesa e accetta le connessioni è "comunicare ascoltatore" o detto "ruolo comunicare ascoltatore". Il client che avvia una nuova connessione verso un comunicare ascoltatore tramite il servizio è denominato "mittente" o "mittente ruolo".

## <a name="listener-interactions"></a>Interazioni comunicare ascoltatore

Comunicare ascoltatore è quattro interazioni con il servizio. tutti i dettagli di rete sono descritti più avanti in questo documento nella sezione Guida di riferimento.

### <a name="listen"></a>Ascolto

Per indicare preparazione per il servizio di comunicare un ascoltatore è pronto per la connessione, viene creata una connessione socket web in uscita. Sincronizzazione connessione viene assegnato il nome di una connessione ibrido configurato lo spazio dei nomi di inoltro e un token di sicurezza che conferisce "Ascolto" a cui assegnare un nome. Quando viene accettato socket web dal servizio, la registrazione è terminata e socket web Fondazione, conservate come "canale di controllo" per l'attivazione di tutte le interazioni successive. Il servizio consente fino a 25 listener simultanee connessione ibrida. Se sono presenti listener 2 o più attivo, le connessioni in ingresso verranno convertite tra di loro in ordine casuale; distribuzione giusta non riesce.

### <a name="accept"></a>Accettare

Ogni volta che un mittente apre una nuova connessione il servizio, il servizio selezionare e una notifica di uno dei listener attivi sulla connessione ibrida. La notifica viene inviata a comunicare ascoltatore tramite il canale di controllo Apri come messaggio JSON contenente l'URL dell'endpoint socket Web in grado di comunicare ascoltatore necessario connettersi per accettare la connessione.

L'URL può e deve essere utilizzato direttamente da comunicare ascoltatore senza necessità di operazioni; le informazioni codificate sono valide solo per un breve periodo di tempo, essenzialmente per purché il mittente è disposto ad attendere per la connessione sia Fondazione-to-end, ma fino a un massimo di 30 secondi. L'URL è utilizzabile solo per il tentativo di una connessione. Come viene stabilito la connessione al socket Web con l'URL di rendezvous, tutte le attività ulteriormente su questo socket Web inoltro dal e al mittente, senza intervento o interpretazione dal servizio.

### <a name="renew"></a>Eseguire il rinnovo 

Il token di sicurezza che deve essere utilizzato per registrare comunicare ascoltatore e mantenere il canale di controllo può scadere mentre comunicare ascoltatore è attivo. La scadenza del token non viene influenzata dalle connessioni in corso, ma causa il canale di controllo che si desidera eliminare dal servizio o appena dopo immediata di scadenza. Gesto "rinnovo" è un messaggio JSON comunicare ascoltatore può inviare messaggi a sostituire il token associato il canale di controllo, in modo che il canale di controllo può essere gestito per lunghi periodi di tempo.

### <a name="ping"></a>Ping 

Se il canale di controllo resta inattivo per un periodo di tempo, intermediari lungo il tragitto, ad esempio carico bilanciamento del carico o NAT possono rilasciare la connessione TCP. Gesto "ping" consente di evitare che tramite l'invio di una piccola quantità di dati sul canale promemoria tutti route di rete che la connessione deve essere attivo e funge anche da un test liveness comunicare ascoltatore. In caso contrario, il canale di controllo deve essere considerato inutilizzabile e comunicare ascoltatore deve riconnettersi.

## <a name="sender-interaction"></a>Interazione mittente

Il mittente ha solo una singola interazione con il servizio, si connette.

### <a name="connect"></a>La connessione

Gesto "connettersi" aperto un socket Web il servizio, fornire il nome della connessione ibrida e un oggetto (facoltativo, ma necessari per impostazione predefinita) token di sicurezza che conferisce l'autorizzazione "Invia" nella stringa di query. Il servizio interagire con comunicare ascoltatore la procedura descritta in precedenza e creare una connessione rendezvous unita con questo socket Web comunicare ascoltatore. Dopo avere accettato socket Web, tutte le altre interazioni sul socket Web sarà pertanto con un comunicare ascoltatore connessi.

## <a name="interaction-summary"></a>Interazione riepilogo

Il risultato di questo modello interazione è che il client mittente proviene dalla sincronizzazione con un "pulito" socket Web che sia connesso a un comunicare ascoltatore e che non deve ulteriormente preambles o preparazione. In questo modo praticamente qualsiasi implementazione di client Web socket facilmente in modo da sfruttare il servizio ibrido connessioni solo specificando un URL predisposto correttamente nel loro livello di client socket Web esistente.

La connessione di rendezvous Socket Web che comunicare ascoltatore ottiene tramite l'interazione accetta anche è pulita e può essere trasferita a qualsiasi implementazione di server socket Web esistente con alcuni minime astrazione aggiuntive per distinguere tra "accetta" operazioni sui listener di rete locale del loro framework e le connessioni ibrido remote "accetta".

## <a name="protocol-reference"></a>Riferimento al protocollo

In questa sezione descrive i dettagli delle interazioni protocollo descritte in precedenza.

Tutte le connessioni socket Web vengono eseguite sulla porta 443 come un aggiornamento da 1.1 HTTPS, in genere ricavata da alcuni framework di socket Web o API. La descrizione di seguito viene mantenuta implementazione neutri, senza che suggerisce un framework specifico.

## <a name="listener-protocol"></a>Protocollo comunicare ascoltatore

Il protocollo comunicare ascoltatore è costituito da due movimenti di connessione e tre operazioni di messaggio.

### <a name="listener-control-channel-connection"></a>Connessione di comunicare ascoltatore controllo canale

Viene aperto il canale di controllo con la creazione di una connessione socket Web:

*wss: / / {dello spazio dei nomi indirizzo} /* *$servicebus* */* *hybridconnection /**{path}? azione di hc sb =... & id di hc sb = … e token di hc sb =... "*

L' *indirizzo di spazio dei nomi* è il nome di dominio completo dello spazio dei nomi che ospita la connessione ibrida, in genere del modulo di Azure inoltro {*nome}. servicebus.windows.net.*

Le opzioni di parametro stringa query sono i seguenti

| Parametri        | Obbligatorio? | Descrizione                                                                                                                                                                                        |
|--------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| azione di hc SB | Sì       | Per il ruolo di comunicare ascoltatore il parametro deve essere **azione di hc sb = ascolto**                                                                                                                                |
| {path}       | Sì       | Percorso di spazio dei nomi con codifica URL della connessione ibrido preconfigurato per registrare questo comunicare ascoltatore in. Questa espressione viene aggiunto a fixed * **$servicebus**/**hybridconnection /** * parte del percorso. |
| token di hc SB  | Sì\*     | Comunicare ascoltatore deve fornire un valido, con codifica URL del servizio Bus condiviso accesso Token per la connessione di ibrido che conferisce destra **ascolto** o dello spazio dei nomi.                                           |
| id di hc SB     | No        | Questo ID facoltativo forniti client consente analisi diagnostica-to-end.                                                                                                                             |

Se la connessione Socket Web non riesce a causa il percorso di connessione ibrido non registrato, o un token non valido o manca o un altro errore, l'errore di riscontro utilizzando il modello di commenti e suggerimenti stato HTTP 1.1 normale. La descrizione dello stato contiene un errore verifica id che possono essere inviate ad Azure supporto:

| Codice | Errore          | Descrizione                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Non trovato      | La connessione ibrido **percorso** non è valido o l'URL di base non è corretto |
| 401  | Non autorizzato   | Il token di sicurezza è mancanti o non valida o non validi                  |
| 403  | Accesso negato      | Il token di sicurezza non è valido per questo percorso per questa azione          |
| 500  | Errore interno | Si è verificato un problema nel servizio                                    |

Se la connessione al socket Web intenzionalmente arresto dal servizio dopo che è stata impostata inizialmente verso l'alto, il motivo per eseguire questa operazione vengono comunicati tramite l'apposito codice errore protocollo di socket Web insieme a un messaggio di errore descrittivo che includerà anche un id di verifica. Il servizio verrà non chiuso il canale di controllo senza che si verifichi una condizione di errore. Qualsiasi chiusura è client controllate.

| Stato WS | Descrizione                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Il percorso di connessione ibrida è stato eliminato o disattivato.                           |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione sono pertanto viola. |
| 1011      | Si è verificato un problema all'interno del servizio.                                           |

### <a name="accept-handshake"></a>Accettare handshake 

Notifica accetta viene inviata dal servizio per comunicare ascoltatore tramite il canale di controllo definito in precedenza come messaggio JSON in una cornice di testo socket Web. Non viene data risposta al messaggio.

Il messaggio contiene un oggetto JSON denominato "accetta", che consente di definire le proprietà seguenti al momento:

-   **indirizzo** : la stringa dell'URL da utilizzare per la creazione di Socket del Web per il servizio per accettare una connessione in ingresso.

-   **id** : l'identificatore univoco per la connessione. Se l'id fornito dal client mittente, il valore di mittente fornito, in caso contrario è un valore generato dal sistema.

-   **connectHeaders** – tutte le intestazioni HTTP fornito all'endpoint inoltro dal mittente, che include anche il protocollo WebSocket Sec e le intestazioni di estensioni di WebSocket Sec.

| Accettare il messaggio                                                                     |
|------------------------------------------------------------------------------------|
```json
{                                                                                  
    "accept" : {                                                                                    
        "address" : "wss://168.61.148.205:443/$servicebus/hybridconnection/{path}?...",                                                                          
        "id" : "4cb542c3-047a-4d40-a19f-bdc66441e736\_G0\_G1",                         
        "connectHeaders" : {                                                                
            "Host" : "…",                                                                       
            "Sec-WebSocket-Protocol" : "…",                                                     
            "Sec-WebSocket-Extensions" : "…"                                                    
        }                                                                                     
    }
}
```

L'indirizzo URL specificato nel messaggio JSON viene utilizzato per comunicare ascoltatore per stabilire il Socket Web di accettare o rifiutare socket mittente.

#### <a name="accepting-the-socket"></a>Accettazione del socket

Per accettare, comunicare ascoltatore stabilisce una connessione WebSocket all'indirizzo fornito.

Presente che per il periodo di anteprima, l'indirizzo URI può utilizzare un indirizzo IP bare e il certificato TLS fornito dal server avrà esito negativo convalida su tale indirizzo. Questo sarà corrette durante l'anteprima.

Se il messaggio "accettare" contiene un'intestazione "Protocollo WebSocket Sec", è probabile che comunicare ascoltatore accetta soltanto il WebSocket se supporta il protocollo e imposta l'intestazione come viene stabilito il WebSocket.

Lo stesso vale per l'intestazione "Estensioni di WebSocket Sec". Se il framework supporta un'estensione, è necessario impostare l'intestazione per la risposta del lato *server* di sincronizzazione "Estensioni di WebSocket Sec" necessari per l'estensione.

L'URL deve essere utilizzato come-per stabilire il socket accetta, ma contiene parametri seguenti:

| Parametri        | Obbligatorio? | Descrizione                                                                                                                                                                                                                                                                                   |
|--------------|-----------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| azione di hc SB | Sì       | Per accettare un socket il parametro deve essere **azione di hc sb = accettare**                                                                                                                                                                                                                          |
| {path}       | Sì       | Percorso di spazio dei nomi con codifica URL della connessione ibrido preconfigurato per registrare questo comunicare ascoltatore in. Questa espressione viene aggiunto a fixed * **$servicebus**/**hybridconnection /** * parte del percorso.                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The path expression MAY be extended with a suffix and a query string expression that follows the registered name after a separating forward slash.                                                                                                                                             
                                                                                                                                                                                                                                                                                                                           
                            This allows the sender client to pass dispatch arguments to the accepting listener when it is not possible to include HTTP headers.                                                                                                                                                            
                                                                                                                                                                                                                                                                                                                           
                            The expectation is that the listener framework will parse out the fixed path portion and the registered name from the path and make the remainder, possibly without any query string arguments prefixed by “sb-“, available to the application for deciding whether to accept the connection.  
                                                                                                                                                                                                                                                                                                                           
                            For more detail see the “Sender Protocol” section below.                                                                                                                                                                                                                                       |
| id di hc SB | No        | Vedere la descrizione dell' **id** .                                                                                                                                                                                                                                                              |

Se si verifica un errore, il servizio può rispondere come indicato di seguito:

| Codice | Errore          | Descrizione                         |
|------|----------------|-------------------------------------|
| 403  | Accesso negato      | L'URL non è valido.               |
| 500  | Errore interno | Si è verificato un problema nel servizio |

Dopo la creazione di connessione, il server verrà arrestato socket Web socket Web mittente arresta, oppure con il seguente stato

| Stato WS | Descrizione                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1001      | Il client mittente chiude la connessione                                        |
| 1001      | Il percorso di connessione ibrida è stato eliminato o disattivato.                           |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione sono pertanto viola. |
| 1011      | Si è verificato un problema all'interno del servizio.                                           |

#### <a name="rejecting-the-socket"></a>Il rifiuto socket

Rifiuto socket dopo aver esaminato il messaggio "accettare" richiede un handshake simile, in modo che il codice di stato e la descrizione di stato comunicare il motivo del rifiuto trasmettere al mittente.

La scelta di progettazione di protocollo consiste nell'usare un handshake WebSocket (che è progettato per terminare in uno stato di errore predefiniti) in modo da comunicare ascoltatore client implementazioni possibile continuare a utilizzare un client WebSocket e non è necessario utilizzare un ulteriore, BI client HTTP.

Per rifiutare il socket, il client accetta indirizzo URI dal messaggio "accettare" e aggiunge due parametri di stringa di query:

| Parametri             | Obbligatorio? | Descrizione                             |
|-------------------|-----------|-----------------------------------------|
| statusCode        | Sì       | Codice di stato numerico HTTP                |
| statusDescription | Sì       | Risorse umana leggibile motivo del rifiuto |

URI risultante viene quindi utilizzato per stabilire una connessione WebSocket; nuovo presente che il certificato TLS potrebbe non corrispondono all'indirizzo durante l'anteprima, in modo che può essere necessario disattivare la convalida.

Quando completato correttamente, questo handshake intenzionalmente non riuscirà con un codice di errore HTTP 410, poiché non WebSocket è stata stabilita. Se si verifica un errore, sono le opzioni seguenti:

| Codice | Errore          | Descrizione                         |
|------|----------------|-------------------------------------|
| 403  | Accesso negato      | L'URL non è valido.               |
| 500  | Errore interno | Si è verificato un problema nel servizio |

### <a name="listener-token-renewal"></a>Rinnovo token comunicare ascoltatore

Quando il token di comunicare ascoltatore sta per scadere, è possibile sostituire inviando un messaggio di cornice testo al servizio tramite il canale di controllo Fondazione. Il messaggio contiene un oggetto JSON denominato "renewToken", che definisce la proprietà seguente al momento:

-   **token** -valido, con codifica URL del servizio Bus condiviso accesso Token dello spazio dei nomi o connessione ibrido che conferisce **ascoltare** a destra.

| renewToken messaggio                                                                                                                                                    
|------------------------------------------------------------------------------------------------|
```json
{
    "renewToken" : {      
        "token" : "SharedAccessSignature sr=http%3a%2f%2fcontoso.servicebus.windows.net%2fHybridConnection1%2f&amp;sig=XXXXXXXXXX%3d&amp;se=1471633754&amp;skn=SasKeyName"
    }                                                                                                                                                            
}
```                                                                                                                                                                      

Se si verifica un errore di convalida token, accesso negato e servizio cloud chiuderà websocket di canale controllo con un messaggio di errore, in caso contrario non viene data risposta.

| Stato WS | Descrizione                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione sono pertanto viola. |

<a name="sender-protocol"></a>Protocollo mittente
---------------

Il protocollo mittente è identico in modo efficace come viene stabilito un comunicare ascoltatore. L'obiettivo è massima trasparenza per il socket Web-to-end. L'indirizzo a cui connettersi è identica a quella di comunicare ascoltatore, ma "azione" è diversa, il token necessita di autorizzazione diversi:

*wss: / / {dello spazio dei nomi indirizzo} /* *$servicebus* */* *hybridconnection /**{path}? azione di hc sb = … e id di hc sb =... \[& token di hc sbc =... \]*

L' *indirizzo di spazio dei nomi* è il nome di dominio completo dello spazio dei nomi che ospita la connessione ibrida, in genere del modulo di Azure inoltro {*nome}. servicebus.windows.net.*

La richiesta può contenere non autorizzate intestazioni HTTP aggiuntive, incluse quelle definite dall'applicazione. Tutte le intestazioni fornite flusso per comunicare ascoltatore e si trova sull'oggetto del messaggio di controllo "accetta" "connectHeader".

Le opzioni di parametro stringa query sono i seguenti

| Parametri        | Obbligatorio? | Descrizione                                                                                                                                                                                                       |
|--------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| azione di hc SB | Sì       | Per il ruolo di comunicare ascoltatore il parametro deve essere **azione = connettersi**                                                                                                                                                    |
| {path}       | Sì       | Percorso di spazio dei nomi con codifica URL della connessione ibrido preconfigurato per registrare questo comunicare ascoltatore in.                                                                                                               
                                                                                                                                                                                                                                               
                            The path expression MAY be extended with a suffix and a query string expression to communicate further                                                                                                             
                                                                                                                                                                                                                                               
                            If the Hybrid Connection is registered under the path “hyco”, the path expression can be “**hyco/**suffix?param=value&…” followed by the query string parameters defined here. A complete expression may then be:  
                                                                                                                                                                                                                                               
                            *wss://{ns}/**$servicebus**/**hybridconnection/*** **hyco/**suffix?param=value*& sb-hc-action=…&sb-hc-id=…\[&sbc-hc-token=…\]*                                                                                     
                                                                                                                                                                                                                                               
                            The path expression is passed through to the listener in the address URI contained in the “accept” control message.                                                                                                |
| token di hc SB | Yes\*     | Comunicare ascoltatore deve fornire valido, con codifica URL del servizio Bus condiviso accesso Token per la connessione di ibrido che conferisce il diritto di **inviare** o dello spazio dei nomi.                                                            | | id di hc SB | No        | ID facoltativo che consente l'analisi diagnostica-to-end e diventa disponibile a comunicare ascoltatore durante la sincronizzazione accetta.                                                                                       |

Se la connessione Socket Web non riesce a causa il percorso di connessione ibrido non registrato, o un token non valido o manca o un altro errore, l'errore di riscontro utilizzando il modello di commenti e suggerimenti stato HTTP 1.1 normale. La descrizione dello stato contiene un errore verifica id che possono essere inviate ad Azure supporto:

| Codice | Errore          | Descrizione                                                            |
|------|----------------|------------------------------------------------------------------------|
| 404  | Non trovato      | La connessione ibrido **percorso** non è valido o l'URL di base non è corretto |
| 401  | Non autorizzato   | Il token di sicurezza è mancanti o non valida o non validi                  |
| 403  | Accesso negato      | Il token di sicurezza non è valido per questo percorso per questa azione          |
| 500  | Errore interno | Si è verificato un problema nel servizio                                    |

Se la connessione al socket Web intenzionalmente arresto dal servizio dopo che è stata impostata inizialmente verso l'alto, il motivo per eseguire questa operazione vengono comunicati tramite l'apposito codice errore protocollo di socket Web insieme a un messaggio di errore descrittivo che includerà anche un id di verifica.

| Stato WS | Descrizione                                                                        |
|-----------|------------------------------------------------------------------------------------|
| 1000      | Comunicare ascoltatore Arresta il socket.                                                 |
| 1001      | Il percorso di connessione ibrida è stato eliminato o disattivato.                           |
| 1008      | Il token di sicurezza è scaduto e i criteri di autorizzazione sono pertanto viola. |
| 1011      | Si è verificato un problema all'interno del servizio.                                           |

## <a name="next-steps"></a>Passaggi successivi:

- [Inoltro domande frequenti](relay-faq.md)
- [Creare uno spazio dei nomi](relay-create-namespace-portal.md)
- [Guida introduttiva a .NET](relay-hybrid-connections-dotnet-get-started.md)
- [Guida introduttiva a nodo](relay-hybrid-connections-node-get-started.md)