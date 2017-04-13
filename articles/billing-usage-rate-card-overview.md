<properties
   pageTitle="Ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure | Microsoft Azure"
   description="Fornisce una panoramica dell'utilizzo di fatturazione di Azure e APIs RateCard, che vengono utilizzati per informazioni dettagliate sui consumo risorsa Azure e tendenze."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure

Clienti e partner essere in grado di precisione prevedere e gestire i costi di Azure.  Come si muova un passaggio a un modello di Opex, occorre anche la possibilità di eseguire showback e analisi rifiuto, nonché fornire fedeltà modalità stima e la fatturazione, in particolare per le distribuzioni di grandi dimensioni cloud.

L'uso risorse Azure e tasso scheda API illustrati in questo indirizzo articolo queste esigenze, abilitando nuovi approfondimenti nel consumo di risorse Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Introduzione a risorse Azure e le API RateCard

L'uso di risorse Azure e RateCard APIs sono implementato come un Provider di risorse, come parte della famiglia di API esposte da Gestione risorse di Azure.  

### <a name="azure-resource-usage-api-preview"></a>Utilizzo delle risorse Azure API (Preview)
Clienti e partner è possibile utilizzare l'API di utilizzo delle risorse di Azure per recuperare i dati di consumo Azure stimato. Le funzioni includono:

- **Controllo dell'accesso basato sui ruoli azure** - clienti e partner possibile configurare i criteri di accesso al [portale di Azure](https://portal.azure.com) o tramite [i cmdlet di PowerShell Azure](powershell-install-configure.md) per specificare quali utenti o applicazioni possono ottenere l'accesso ai dati di utilizzo dell'abbonamento. I chiamanti devono utilizzare standard token di Azure Active Directory per l'autenticazione. Il chiamante deve essere aggiunta anche al ruolo di lettore, proprietario o collaboratori di accedere ai dati di utilizzo per un particolare abbonamento Azure.

- **Oraria o giornaliera aggregazioni** - i chiamanti possono specificare i dati di utilizzo Azure in intervalli orari o gli intervalli di giornalieri. Il valore predefinito è giornaliera.

- **Metadati istanza forniti (include i contrassegni delle risorse)** -dettagli a livello di istanza, ad esempio uri risorse completo (/subscriptions/ {id abbonamento} /...), insieme alla risorsa tag informazioni e delle risorse gruppo saranno forniti nella risposta. Per aiutare i clienti in modo deterministico e a livello di programmazione allocare l'utilizzo tramite i contrassegni per i casi di utilizzo come in carica cross.

- **Metadati di risorsa forniti** - dettagli delle risorse, ad esempio nome contatore, contatore categoria, contatore sottocategoria, unità e opzioni internazionali anche possibile passare in risposta, per conferire ai chiamanti una migliore comprensione delle operazioni che è stata utilizzata. Stiamo lavorando anche per allineare la terminologia di metadati delle risorse in portale Azure utilizzo Azure CSV, EA CSV e altre esperienze pubblico, per consentire ai clienti di correlare i dati tra esperienze della fatturazione.

- **L'utilizzo per tutti i tipi di offerta** -dati di utilizzo sarà accessibili per tutti disponibili tipi tra cui uso prepagato, MSDN, impegno monetario, monetario carta di credito ed EA tra gli altri.

### <a name="azure-resource-ratecard-api-preview"></a>Risorsa Azure RateCard API (Preview)
Clienti e partner possono utilizzare l'Azure risorsa RateCard API per ottenere l'elenco delle risorse disponibili Azure, insieme ad stimata prezzi informazioni per ogni. Le funzioni includono:

- **Controllo dell'accesso basato sui ruoli azure** - clienti e partner possibile configurare i criteri di accesso al [portale di Azure](https://portal.azure.com) o tramite [i cmdlet di PowerShell Azure](powershell-install-configure.md) per specificare quali utenti o applicazioni possono ottenere l'accesso ai dati RateCard. I chiamanti devono utilizzare standard token di Azure Active Directory per l'autenticazione. Il chiamante deve essere aggiunta anche al ruolo di lettore, proprietario o collaboratori di accedere ai dati di utilizzo per un particolare abbonamento Azure.

- **Supporto per uso prepagato, MSDN, impegno monetario e carta di credito monetario offre (EA non è supportato)** - questa API fornisce informazioni Azure tasso a livello di offerta, rispetto al livello di iscrizione.  Il chiamante dell'API necessario passare le informazioni dell'offerta per ottenere le tariffe e i dettagli delle risorse.  Come EA offerte personalizzato tariffe per la registrazione, siamo in grado di fornire le tariffe EA al momento.

## <a name="scenarios"></a>Scenari

Ecco alcuni scenari che sono stati resi possibili con la combinazione di utilizzo e APIs RateCard:

- **Azure dedicato durante il mese** - gli utenti possono utilizzare l'utilizzo e APIs RateCard in combinazione per ottenere maggiori approfondimenti in loro cloud spesa durante il mese, per analizzare le tariffe orari e giornalieri gli intervalli delle stime sull'utilizzo e costi.

- **Impostare avvisi** -clienti e partner può configurare automaticamente avvisi basati su monetari o delle risorse nel consumo cloud ottenendo il consumo stimato e stima spese mediante l'utilizzo e l'API RateCard.

- **Fattura Predict** – clienti e partner possano accedere loro consumo stimato e cloud dedicato e applicare algoritmi risorse computer per la previsione che la fattura sarebbe alla fine del ciclo di fatturazione.

- **Analisi del costo pre-consumo** – può clienti anche usare l'API RateCard prevedere quantità loro fattura sarebbe se si trattasse di spostare i carichi di lavoro Azure, per fornire bene accolta numeri di utilizzo. Nel caso clienti carichi di lavoro esistenti in altre aree o cloud privati, anche associare il relativo utilizzo con il Azure dedicato tariffe per ottenere una migliore stima loro Azure stimata. Questo fornisce una visualizzazione avanzata di cosa è possibile ottenere tramite la [Calcolatrice prezzi Azure](https://azure.microsoft.com/pricing/calculator/), come, ad esempio, i partner di fatturazione offrono la possibilità di eseguire il pivot su offerta e confronta/contrasto tra tipi diversi offerta oltre uso prepagato, tra cui impegno monetario e monetario carta di credito. Le API offrono la possibilità di modifiche di stima per area geografica, costo per consentire il tipo di analisi di simulazione necessari per prendere decisioni di distribuzione, come la distribuzione le risorse in controller di dominio differenti in tutto il mondo possono avere un impatto diretto sul costo totale.

- **Analisi di simulazione** -

    - Clienti e partner, è possibile determinare se sia più conveniente per eseguire i carichi di lavoro in un'altra area oppure in un'altra configurazione della risorsa Azure. Azure delle risorse costi possono variare in base alla regione Azure in cui sono in esecuzione e in questo modo, clienti e partner ottenere le ottimizzazioni di costo.

    - Clienti e partner determinare se un altro tipo di offerta Azure restituisce un tasso migliore in una risorsa di Azure.

## <a name="partner-solutions"></a>Soluzioni partner

[Utilizzo di Microsoft Azure e RateCard API abilitare Cloudyn per fornire ITFM per i clienti](billing-usage-rate-card-partner-solution-cloudyn.md) descrive l'esperienza di integrazione offerto dal partner di Azure fatturazione API [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  In questo articolo contiene una descrizione dettagliata del loro esperienze, incluso un breve video che mostra i clienti di Azure utilizzo Cloudyn e le API di fatturazione di Azure per informazioni dettagliate sui vantaggi dai dati consumo Azure.

[Cloud Cruiser e l'integrazione di Microsoft Azure fatturazione API](billing-usage-rate-card-partner-solution-cloudcruiser.md) descrive come [del Cloud Cruiser Express per Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funziona direttamente dal portale di WAP, consentendo ai clienti di facile gestire aspetti operativi e finanziari del loro cloud pubblico Microsoft Azure privato o ospitato da un'unica interfaccia utente.   

## <a name="next-steps"></a>Passaggi successivi
+ Controllare i [Riferimenti alle API REST di fatturazione Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) per ulteriori informazioni su entrambe le API che fanno parte del set di API fornite da Gestione risorse di Azure.
+ Se si preferisce approfondire direttamente il codice di esempio, vedere esempi di codice il nostro Microsoft Azure fatturazione API su [Azure esempi di codice](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Ulteriori informazioni
+ Vedere l'articolo [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md) per altre informazioni su Gestione risorse di Azure.
+ Per ulteriori informazioni sulla suite di strumenti necessari per facilitare il per comprendere gli cloud dedicato, fare riferimento all'articolo Gartner [Mercato Guida per gli strumenti di gestione finanziaria IT (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
