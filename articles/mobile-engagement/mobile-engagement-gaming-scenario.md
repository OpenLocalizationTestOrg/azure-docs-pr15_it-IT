<properties 
    pageTitle="Azure implementazione coinvolgimento Mobile per l'applicazione di gioco"
    description="Scenario di app gioco per implementare l'impegno Mobile Azure" 
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

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implementare l'impegno per dispositivi mobili con gioco App

## <a name="overview"></a>Panoramica

Un avvio gioco ha creato una nuova app gioco role play/strategia di pesca in base a. Il gioco è stato configurarlo e usarlo per 6 mesi. Questo gioco è un enorme successo e ha milioni di download e la conservazione è molto elevata rispetto alle altre App gioco avvio. Durante la riunione di revisione trimestrali, parti interessate concordino che necessarie per aumentare Ricavi medi per utente (ARPU). Pacchetti di gioco Premium sono disponibili come offerte speciali. Questi Pack gioco consentono agli utenti di aggiornare l'aspetto e le prestazioni dei loro righe relativa alla pesca e ami da o tackles nel gioco. Tuttavia, le vendite di pacchetto sono molto basse. In modo decidere prima di analizzare l'esperienza dei clienti con uno strumento analitica, e quindi per sviluppare un coinvolgimento programma per ingrandire le dimensioni delle vendite utilizzando avanzate segmentazione.

Basata su [Azure coinvolgimento di dispositivi mobili - Guida introduttiva alle procedure consigliate](mobile-engagement-getting-started-best-practices.md) definire una strategia di impegno.

##<a name="objectives-and-kpis"></a>Obiettivi e indicatori KPI

Parti interessate per il gioco incontro. Tutti concordino su un obiettivo principale - ad incrementare le vendite di pacchetto premium 15%. Creano Business indicatori di prestazioni chiave (KPI) da misurare e unità tale obiettivo

* Dal livello del gioco vengono acquistati questi pacchetti
* Che cos'è i ricavi per utente, sessione, settimana e mese?
* Quali sono i tipi di acquisto preferite?

Parte 1 della [Guida introduttiva](mobile-engagement-getting-started-best-practices.md) viene illustrato come definire gli obiettivi e indicatori KPI. 

Con indicatori KPI tramite Business definiti a questo punto, il responsabile del prodotto Mobile crea indicatori KPI di impegno per determinare i criteri di conservazione e le tendenze di nuovo utente.

* Monitorare i criteri di conservazione e l'uso in seguenti intervalli: ogni giorno, ogni 2 giorni, settimanale, mensile e ogni 3 mesi
* Numero degli utenti attivi
* Valutazione app nello store

In base ai suggerimenti dal team, gli indicatori KPI tecnici seguenti sono state aggiunte a rispondere alle domande seguenti:

* Che cos'è il percorso utente (si visita la pagina, quantità utenti tempo dedicato su di esso)
* Numero di bug rilevato per sessione o si blocca
* Quali sono le versioni del sistema operativo è in esecuzione con gli utenti?
* Che cos'è la dimensione media della schermata per gli utenti?
* Quali tipi di connettività internet possiedono gli utenti?

Per ogni indicatore KPI Mobile Product Manager specifica i dati ha bisogno e dove si trova nella propria playbook.

## <a name="engagement-program-and-integration"></a>Integrazione e dallo stesso programma coinvolgimento

Prima di creare un'applicazione di impegno avanzate, direttore di progetto Mobile responsabile del progetto deve avere una conoscenza approfondita di come e quando i prodotti vengono utilizzati dagli utenti.

Dopo 3 mesi, il direttore di progetto Mobile abbia raccolto abbastanza dati per migliorare le proprie vendite di notifica push di app. Conosce che:

* Il primo acquisto accade in genere a livello di 14. Per il 90% di tali casi, l'acquisto riguarda nuove armi leggendaria $3.
* 80% di questi casi, gli utenti che hanno effettuato l'acquisto di una, continuare con il prodotto e apportare ulteriori acquisti.
* Gli utenti che hanno superato il livello di 20, avviare dedicare più di 10/ settimana.
* Gli utenti comandi sono particolarmente utili per acquistare pacchetti premium livello 16, 24 e 32.

Grazie a questa analisi direttore di progetto Mobile decide di creare sequenze di notifica per ingrandire le dimensioni nelle vendite da inizio app push specifico. Crea tre sequenze push che ha chiama: introduzione programma, programma vendite e programma inattivo. Per ulteriori informazioni vedere [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
