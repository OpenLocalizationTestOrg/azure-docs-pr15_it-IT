<properties 
    pageTitle="Azure implementazione coinvolgimento Mobile per l'applicazione di elementi multimediali"
    description="Scenario di app elementi multimediali per implementare l'impegno Mobile Azure" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-media-app"></a>Implementare l'impegno per dispositivi mobili con App elementi multimediali

## <a name="overview"></a>Panoramica

John è un manager di progetto per dispositivi mobili per individuare una società media grande. Ha avviato recente una nuova app che contiene un conteggio dei download molto elevato. Ha raggiunto il suo obiettivi per il download ma comunque il suo tornare su Investment(ROI) per ogni utente non soddisfa i suoi requisiti. 

John è già stato individuato perché il suo ROI è troppo basso. Gli utenti spesso interrompere utilizzando il suo app dopo solo 2 settimane e la maggior parte delle loro mai torni. Si desidera incrementare la conservazione dei suoi app.

Dopo alcune prove del iniziale, che ha appreso quando gli utenti con le notifiche push esercizio vengono in genere continuare a usare il suo app. Anche gli utenti che erano inattivi spesso restituirà all'app a seconda che invia notifiche. John decide di investire in un programma di impegno per il suo app che utilizza l'assegnazione del avanzate con le notifiche push.

John ha recente leggere [Azure coinvolgimento di dispositivi mobili - Guida introduttiva alle procedure consigliate](mobile-engagement-getting-started-best-practices.md) e ha scelto di seguire i consigli forniti dalla Guida.

##<a name="objectives-and-kpis"></a>Obiettivi e indicatori KPI

Parti interessate per app John incontro. Business viene generato da annunci come utenti utilizzano il suo media. Aumentando contenuto consumato per utente, John aumenta il suo ricavi. Tutti concordino su un obiettivo principale: per aumentare le vendite da annunci del 25%. Creano Business indicatori di prestazioni chiave (KPI) da misurare e unità tale obiettivo

* Numero di annunci fatto clic su per ogni utente
* Il numero di pagine articolo visitati (per ogni utente / per sessione / settimana / al mese...)
* Quali sono le categorie preferite

In base a riunione John con parti interessate ha è definita la sua KPI Business. Ha segue parte 1 di [Azure coinvolgimento di dispositivi mobili - Guida introduttiva alle procedure consigliate](mobile-engagement-getting-started-best-practices.md). 

Successivamente, la creazione di indicatori KPI coinvolgimento seguenti per assicurarsi che gli obiettivi vengono raggiunti:

* Monitorare i criteri di conservazione in intervalli seguenti: giornaliera, settimanale, settimanale e mensile.
* Numero di utenti attivi
* Archivia la classificazione di app nell'app

In base ai suggerimenti dal team, gli indicatori KPI tecniche seguenti sono state aggiunte a rispondere alle domande seguenti:

* Che cos'è il percorso utente (si visita la pagina, il numero di utenti tempo dedicato su di esso)
* Numero di blocchi o bug rilevato per la sessione?
* Quali sono le versioni del sistema operativo è in esecuzione con gli utenti?
* Che cos'è la dimensione media della schermata per gli utenti?
* Quali tipi di connessioni internet possiedono gli utenti?

Per ogni indicatore KPI, ha record in posizione corretta del suo playbook ha classifica i dati necessari.

## <a name="engagement-program-and-integration"></a>Integrazione e dallo stesso programma coinvolgimento

Ora che John che definisce il suo KPI termine inizia il suo fase strategia coinvolgimento definendo 4 coinvolgimento programmi e gli obiettivi:    ![][1]

John passa più approfondito per descrivere dettagliatamente le notifiche push per ogni applicazione. Notifica push sono definiti dalle cinque elementi:

1. Obiettivo: che cos'è l'obiettivo della notifica
2. Come scade l'obiettivo
3. Obiettivo: che riceverà la notifica?
4. Content: Che cos'è il testo e il formato della notifica (In App/Out di App)
5. Quando: che cos'è il momento migliore per inviare la notifica push

    ![][2]

Per ulteriori informazioni vedere [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

In base alla parte 2 white paper John della sezione di destinazione viene utilizzata per definire quali dati ha per raccogliere e scrive il suo piano Tag congiuntamente con team IT per implementare la soluzione. Dopo la settimana di implementazione e la verifica dell'accettazione di utente, John infine può avviare il suo programmi.

##<a name="program-results"></a>Risultati del programma

4 mesi in un secondo momento, John rivede esibizioni delle applicazioni. Programma iniziale e il programma settimanale sono suoi obiettivi. Il numero di utenti che hanno solo una sessione delle riduzioni delle tariffe, altre caratteristiche dell'applicazione in uso e il numero di connessioni per settimana è doppia.

Il **Programma inattivo** aiuta John comprendere tendenze utente. Sembra che 15% degli utenti inattivi tornare all'app. Tuttavia la maggior parte di esse non rimane attiva più di 1 mese. John preveda una potenziale ottimizzazione di questa sequenza con le notifiche aggiuntive ed espandere il suo contenute scelte.

**Alla scoperta di programma** non funzionare correttamente. Aumenta cross ma non è sufficiente per raggiungere il suo obiettivi di vendita. John identifica che non dispone di sufficiente dati in modo rilevante l'assegnazione del e Suggerisci pianificazione contenuto appropriato. Ha interrompe questo programma e si è incentrata su invio "notifiche push editoriale" con coinvolgimento di Azure Mobile. Il suo giornalisti dispongono già di una soluzione CMS per inviare le notifiche push e non si desidera modificare.

John decide di usare l'API raggiunga che è una API REST HTTP che consente di gestione delle campagne di mano senza la necessità di utilizzare l'interfaccia AZME Web. Con questo approccio John può raccogliere i dati che deve e per consentire il suo writer continuare a usare la soluzione CMS.

Per garantire che funziona correttamente, John chiede team IT per prestare attenzione degli elementi seguenti:

1. **Sistemi operativi** : hanno le proprie regole per gestire le notifiche push, in modo John decide visualizzare un elenco di tutti i casi e viene verificato se l'API gestiscono.
Ad esempio: Push Android sistema consente quadro non inclusa nel caso di iOS.

2. **Intervallo di tempo**: John richiede un'API cui impostare l'intervallo di tempo e una fine su campagne. Si desidera conservare gli utenti da qualsiasi saturazione con la notifica.

3. **Categorie**: team Marketing Prepara modello per ogni tipo di avviso. John chiede team IT per impostare categorie all'interno dell'API.

Dopo alcuni test John è soddisfatto. Grazie a questa API giornalisti comunque possono inviare le notifiche push con loro CMS e Azure Mobile coinvolgimento raccoglie tutti i dati del comportamento per poterli

In seguito a questi 4 prima di tutto i mesi, risultati riflettono una buona complessiva prestazioni e offre sicurezza per John e il suo discussioni, il ROI aumenta utente per 15% e vendite per dispositivi mobili rappresentano 17,5% delle vendite totali, un aumento delle percentuali 7.5 solo quattro mesi.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
