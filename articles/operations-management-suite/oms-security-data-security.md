<properties
   pageTitle="Operazioni gestione Suite sicurezza e protezione dei dati di controllo soluzione | Microsoft Azure"
   description="In questo documento spiega come dati sono gestiti e garantiti in operazioni gestione gruppo di sicurezza e soluzione di controllo."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Protezione dei dati di soluzione operazioni gestione gruppo di sicurezza e controllo

Per aiutare i clienti impedire, rilevare e rispondere a rischi, [operazioni gestione famiglia di prodotti (OMS) sicurezza e la soluzione di controllo](operations-management-suite-overview.md) consente di raccogliere e i processi di dati delle risorse, che include:

- Registro eventi di protezione
- Evento individua eventi per Windows (ETW)
- Eventi di controllo AppLocker
- Registro Windows Firewall
- Eventi di rischio Analitica avanzati
- Risultati della valutazione della previsione
- Risultati della valutazione antimalware
- Risultati della valutazione/patch di aggiornamento
- Flussi di syslogs abilitati in modo esplicito dell'agente

Abbiamo rendere sicuri impegni per proteggere la privacy e sicurezza dei dati. Microsoft aderisce alle linee guida di sicurezza e conformità restrittive, ovvero dalla codifica alla operativo un servizio.
Questo articolo spiega come dati viene gestiti e garantiti in sicurezza OMS e soluzione di controllo.

## <a name="data-sources"></a>Origini dati

Soluzione di controllo e protezione OMS analizzare i dati dal computer in cui è installato l'agente OMS macchine virtuali. Sicurezza OMS e controllare soluzione può raccogliere informazioni di configurazione di eventi di protezione, ad esempio messaggi di registro di sistema, i log di controllo, i registri di IIS ed eventi di Windows. Esempi di tali dati sono: tipo di sistema operativo e versione, processi in esecuzione, il nome del computer, gli indirizzi IP, eseguito l'accesso utente e ID tenant.  

## <a name="data-protection"></a>Protezione dei dati

**Separazione di dati**: dati vengono mantenuti in modo logico distinto per ogni componente in tutto il servizio. Tutti i dati è contrassegnato per l'organizzazione. In questo tag persiste nel ciclo di vita dei dati e viene applicata ogni livello del servizio. 

**Accesso ai dati**: per fornire suggerimenti per la sicurezza e analizzare i rischi di sicurezza, personale Microsoft possono accedere a informazioni raccolte o analizzate dai servizi. Abbiamo rispettare il [Legali Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e l' [Informativa sulla Privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), quale stato che Microsoft non usare il dati dei clienti o estrarre informazioni da essa per scopi commerciali pubblicitari o simili. Per fornire suggerimenti per la sicurezza e analizzare i rischi di sicurezza, personale Microsoft possono accedere alle informazioni raccolte o analizzate dai servizi. Abbiamo usare solo dati dei clienti in base alle esigenze per offrire Azure servizi, ivi compresi a fini compatibili con i servizi. Mantenere tutti i diritti per i propri dati.

**Usare dati**: Microsoft Usa motivi e intelligence rischio visualizzato tra più tenant per migliorare la funzionalità di prevenzione; avviene secondo gli impegni di privacy descritti l' [Informativa sulla Privacy](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Percorso di dati è configurato a livello di area di lavoro OMS, durante la creazione di un'area di lavoro, incluso il processo di configurazione iniziale OMS sicurezza e di controllo.

## <a name="see-also"></a>Vedere anche

In questo documento è stato come dati viene gestiti e garantiti in OMS. Per ulteriori informazioni sulla sicurezza OMS e controllo soluzione, vedere:

- [Panoramica di gestione famiglia di prodotti (OMS) operazioni](operations-management-suite-overview.md)
- [Risposta agli avvisi di sicurezza in protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo e il monitoraggio](oms-security-responding-alerts.md)
- [Monitoraggio delle risorse operazioni Management Suite sicurezza e la soluzione di controllo](oms-security-monitoring-resources.md)

