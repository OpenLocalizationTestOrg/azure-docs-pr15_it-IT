<properties
    pageTitle="Panoramica dei connettori App logica | Microsoft Azure"
    description="Panoramica dei connettori che possono essere usate in un'app di logica"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Uso dei connettori in un'app di logica

Connettori consentono di accedere rapidamente agli eventi, dati, quindi azioni servizi, protocolli e piattaforme.  L'elenco completo dei connettori che supporta le applicazioni di logica può [essere trovato qui](apis-list.md).  Connettori possono essere utilizzati come un trigger o un'azione in un'app di logica e può richiedere una *connessione* configurata per usare (ad esempio: autorizzazione di un account Twitter per accedere a o pubblicare il conto).

## <a name="basics"></a>Nozioni di base

Connettori siano è possibile accedere come parte di un'app di logica per l'integrazione con altri servizi come Dynamics Azure, Salesforce, [e altri](apis-list.md)servizi di hosting.  Vengono distribuiti e gestiti da Microsoft, in modo che è possibile creare i flussi di lavoro di integrazione con scala, velocità e sicurezza gestiti.  È possibile aggiungere un connettore a un'app logica la ricerca e selezionando un'azione connettore o trigger in **Microsoft Mostra API gestite**.

![Menu azione per la selezione di trigger][1]

Ogni azione di connettore o trigger avrà un set di proprietà per la configurazione.  È possibile fare clic sul pulsante informazioni per ulteriori informazioni sull'azione o il riferimento la documentazione [per ottenere ulteriori informazioni](apis-list.md).

Se si vuole integrare in un servizio o l'API che non è ancora un connettore, è possibile estendere le applicazioni di logica tramite un [connettore personalizzato](../app-service-logic/app-service-logic-create-api-app.md) o semplicemente chiamare direttamente al servizio tramite un protocollo come HTTP.

## <a name="triggers"></a>Trigger

Alcuni connettori dispongono di un trigger, ovvero un evento da tale connettore verrà generato una app logica e passare in qualsiasi tipo di dati come parte del trigger.  Un trigger è sempre il primo passaggio in un'app di logica.  Trigger popolari includono operazioni come:
 
 * Ricorrenza - ogni ora
 * Quando si riceve una richiesta HTTP
 * Quando viene aggiunto un elemento a una coda
 * Quando si riceve un messaggio di posta elettronica
 
Gli altri utenti è necessario un intervallo di ricorrenza configurato nel con quale frequenza app logica controllerà il servizio per un evento (fino a ogni 15 secondi) e alcuni trigger vengono attivati immediata che si verifica un evento tramite una notifica all'app logica.  

Dopo aver ricevuto un evento, in grado di attivare l'app logica eseguita e le azioni del flusso di lavoro verranno avviato.  Inoltre, sarà possibile accedere a qualsiasi tipo di dati da trigger in tutto il flusso di lavoro (ad esempio trigger 'in un tweet nuovo' passerà il tweet in Esegui).

## <a name="actions"></a>Azioni

La maggior parte dei connettori dispongono di uno o più operazioni che possono essere eseguiti come parte del flusso di lavoro.  Azioni sono tutte le operazioni che si verificano dopo l'esecuzione ha generato da un trigger.  Per aggiungere un fare clic su azione il pulsante **Nuova azione** e cercare il connettore da usare.  Dopo aver selezionato (e dopo la configurazione di tutte le [connessioni](#connections) che possono essere necessari) verrà visualizzata la scheda azioni che è possibile configurare.  È possibile selezionare i dati nei passaggi precedenti, fare clic su in uno qualsiasi dei token di output o immettere in qualsiasi altra configurazione in base alle esigenze.

![Configurazione di un'azione connettore][2]

## <a name="connections"></a>Connessioni

La maggior parte dei connettori è necessario configurare una *connessione* prima di poter usare il connettore.  Una *connessione* è qualsiasi configurazione account di accesso o connessione necessaria per accedere al connettore.  Per i connettori che utilizzano OAuth, creare una connessione indica che l'accesso al servizio (ad esempio Office 365, Salesforce o GitHub) in cui i token di accesso possono essere crittografati e sicuro memorizzati in un archivio segreto Azure.  Altri connettori (ad esempio FTP e SQL) richiedono una connessione che contiene configurazione come indirizzo del server, il nome utente e password.  Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro.  Connessioni saranno in grado di accedere al servizio per purché consenta il servizio.  Per le connessioni di Azure Active Directory OAuth (ad esempio Office 365 e Dynamics) è possibile continuare ad aggiornare il token di accesso tempo indefinito.  Altri servizi potrebbero mettere limiti in quanto tempo è possibile utilizzare un token senza che vengano aggiornati.  In generale determinate azioni quali la modifica di una password verranno invalidate tutti i token di accesso.  

Connessioni possono essere visualizzate e gestite in Azure facendo clic su **Sfoglia** e selezionando **API connessioni**.  Dalla risorsa API connessioni possono visualizzare, modificare, aggiornare o reimpostare l'autorizzazione tutte le connessioni che è stato creato.

## <a name="next-steps"></a>Passaggi successivi

- [Creare la prima app logica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Informazioni comuni ottimali ed esempi delle App logica](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Guida introduttiva a azioni e trigger integrazione enterprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png