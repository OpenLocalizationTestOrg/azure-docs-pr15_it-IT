<properties
    pageTitle="Limitazione con Azure API di gestione delle richieste avanzate"
    description="Informazioni su come creare e applicare quota flessibile tasso limitazione dei criteri di gestione dell'API Azure."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Limitazione con Azure API di gestione delle richieste avanzate

La possibilità di limitare le richieste in arrivo è un ruolo chiave di Azure API Management. Sia controllando la frequenza delle richieste o i totali delle richieste/trasferiti API gestione consente provider di API per impedire loro API abusi e creare valore per diversi livelli di prodotto API.

## <a name="product-based-throttling"></a>Limitazione di prodotto in base a
Fino a data, la limitazione della velocità funzionalità sono state limitate nell'ambito di un particolare abbonamento di prodotto (essenzialmente chiave), definiti nel portale di publisher API gestione. È utile per il provider di API applicare i limiti per gli sviluppatori che hanno effettuato l'iscrizione a utilizzare le API, tuttavia, non serve, ad esempio, nella limitazione per singoli utenti finali dell'API. È possibile che per singolo utente dell'applicazione per gli sviluppatori di utilizzare la quota intera e quindi impedire altri clienti dello sviluppatore di usare l'applicazione. Inoltre, diversi clienti che potrebbero generare un numero elevato di richieste possono limitare l'accesso agli utenti occasionali.

## <a name="custom-key-based-throttling"></a>Chiave personalizzata in base a limitazione
I nuovi criteri [tasso limite da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) costituiscono una soluzione notevolmente più flessibile per il controllo del traffico. I nuovi criteri consentono di definire le espressioni per identificare i tasti che verranno utilizzati per tenere traccia dell'utilizzo di traffico. Con un esempio più semplice è illustrato il funzionamento. 

## <a name="ip-address-throttling"></a>La limitazione per l'indirizzo IP
I seguenti criteri limitano un indirizzo IP del client singolo a solo 10 chiamate minuto, con un totale di 1.000.000 chiamate e 10.000 kilobyte di larghezza di banda per mese. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Se tutti i client su Internet utilizzavano l'indirizzo IP, questa potrebbe essere in modo efficace di limitare l'uso dall'utente. Tuttavia, è molto probabile che più utenti verranno la condivisione di un solo indirizzo IP pubblico a causa di loro l'accesso a Internet tramite un dispositivo NAT. Nonostante ciò, che consentono l'accesso non autenticato il `IpAddress` potrebbe essere l'opzione ottimale.

## <a name="user-identity-throttling"></a>La limitazione delle identità utente
Se un utente viene autenticato quindi una chiave limitazione può essere generata in base alle informazioni che identifica in modo univoco un utente.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

In questo esempio estrarre l'intestazione di autorizzazione, convertirlo in `JWT` oggetto e utilizzare l'oggetto del token per identificare l'utente e utilizzarla come tasso limitando chiave. Se l'identità dell'utente è archiviato nel `JWT` come una delle altre dichiara quindi che valore può essere utilizzato in sostituzione.

## <a name="combined-policies"></a>Criteri combinati
Sebbene i nuovi criteri di limitazioni offrono un maggiore controllo rispetto i criteri di limitazioni di, esiste valore la combinazione di entrambe le funzioni. La limitazione per l'abbonamento conteggio del numero ([limite chiamata tasso mediante sottoscrizione](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [impostare quota utilizzo dall'abbonamento](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) è un'ottima soluzione per abilitare monetizing di un'API tramite l'addebito in base ai livelli di utilizzo. Maggiore controllo granulare di essere in grado di rallentare dall'utente complementare e impedisce il comportamento di un utente di compromettere l'esperienza di un altro. 

## <a name="client-driven-throttling"></a>Client basati su limitazione
Quando è definita la chiave limitazione utilizzando un' [espressione di criteri](https://msdn.microsoft.com/library/azure/dn910913.aspx), è il provider di API scelta come ambita la limitazione. Tuttavia, uno sviluppatore potrebbe essere necessario controllare giudizio limite propri clienti. Questo è possibile abilitare dal provider di API introducendo un'intestazione personalizzata per consentire l'applicazione client per gli sviluppatori comunicare la chiave all'API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

In questo modo applicazione client per gli sviluppatori scegliere la modalità creare il tasso di limitazione chiave. Un po' di ingegno uno sviluppatore client di creare le proprie livelli tariffa assegnando delle chiavi per gli utenti e l'utilizzo della chiave di rotazione.

## <a name="summary"></a>Riepilogo
Gestione API Azure fornisce tasso e offerta limitazione per proteggere e aggiungere valore per il servizio di API. I nuovi criteri di limitazioni con regole di ambito personalizzate possibile accurato granulare controllare i criteri per consentire ai clienti di compilazione di applicazioni ancora migliore. Gli esempi riportati in questo articolo viene illustrato l'utilizzo di questi nuovi criteri per limitare le chiavi con indirizzi IP client, identità dell'utente e valori client generato tasso di produzione. Tuttavia, esistono molte altre parti del messaggio che potrebbero essere utilizzate, ad esempio agente utente, frammenti di percorso URL, la dimensione dei messaggi.

## <a name="next-steps"></a>Passaggi successivi
Inviare commenti e suggerimenti in thread Disqus di questo argomento. Sarebbe ideale ascoltare su altri valori di chiave potenziali che sono stati una scelta logica negli scenari.

## <a name="watch-a-video-overview-of-these-policies"></a>Guardare un video introduttivo di questi criteri
Per ulteriori informazioni sui criteri [tasso limite da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota da chiave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) contenute in questo articolo, guardare il video seguente.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
