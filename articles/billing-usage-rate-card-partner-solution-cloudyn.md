<properties
   pageTitle="L'uso di Microsoft Azure e Abilita API RateCard Cloudyn per fornire ITFM per i clienti | Microsoft Azure"
   description="Fornisce una prospettiva univoca da Microsoft Azure fatturazione partner Cloudyn, nel loro esperienze integrazione le API di fatturazione di Azure il prodotto.  Questa operazione è particolarmente utile per i clienti di Azure e Cloudyn sono interessati a utilizzando/provare Cloudyn per servizi di Windows Azure."
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

# <a name="microsoft-azure-usage-and-ratecard-apis-enable-cloudyn-to-provide-itfm-for-customers"></a>L'uso di Microsoft Azure e Abilita API RateCard Cloudyn per fornire ITFM per i clienti

Cloudyn, un partner di sviluppo di Microsoft e un provider iniziale delle funzionalità di gestione cloud, è stato scelto per visualizzare un'anteprima privata del nuovo uso risorse di Microsoft Azure e RateCard APIs.  L'API l'uso consente l'accesso ai dati consumo Azure stimato per una sottoscrizione. L'API RateCard fornisce informazioni sui prezzi complete di tutti i servizi di Azure, per i clienti non - Enterprise Agreement EA. Integrate, queste API forniscono una base informazioni complete di input in strumenti di gestione finanziaria IT (ITFM), ad esempio quelli forniti da Cloudyn.

## <a name="introduction"></a>Introduzione

La cosiddetta "moltiplicazione" di dati da API l'uso con i dati di API RateCard (utilizzo [unità] prezzo [$unit] = dettagliate sull'uso e costo) consente di creare più granulare, accurate e affidabile fatturazione informazioni disponibili per Azure oggi.

![Panoramica ITFM][1]

Utilizzo di queste API fornisce informazioni chiave sull'utilizzo dei clienti e i costi, che consente di Cloudyn per analizzare gli account dei clienti in modo semplice e livello di programmazione ed eseguire varie attività ITFM per i clienti.

## <a name="integrating-cloudyn-with-the-ratecard-and-usage-apis"></a>Integrazione di Cloudyn con l'API di utilizzo e RateCard
API RateCard richiede diversi parametri di input, ossia quali informazioni l'area geografica, valuta e impostazioni locali, ma quello più importante è OfferDurableID che specifica il tipo di Azure che offre il cliente utilizza (uso prepagato, piani legacy impegno 6 e 12 mesi, MSDN offerte, MPN offerte, offerte e gli altri utenti). Il OfferDurableID sono disponibili in [fatturazione portale e l'uso di Azure](https://account.windowsazure.com/Subscriptions), in "Offrono ID" per la sottoscrizione specificata.

Al momento della registrazione per i servizi di [Cloudyn per Azure](https://www.cloudyn.com/microsoft-azure/) , i clienti possono aggiungere il proprio codice OfferDurableID, che consente di Cloudyn in modo da estrarre le informazioni pertinenti prezzi tramite l'API RateCard.  Informazioni sui diversi tipi di offerte sono disponibili una pagina [Dei dettagli offerta di Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) .

![Cenni preliminari sul motore Cloudyn ITFM][2]

Usa Cloudyn dell'utilizzo e APIs RateCard, oltre alle API di prestazioni di Azure, per creare ulteriori livelli di visualizzazione, analitica, gli avvisi, relazioni, gestione dei costi e suggerimenti pratici, offrendo ai clienti Azure uno strumento ITFM cloud affidabili enterprise.

## <a name="cloudyn-itfm-use-cases-enabled-by-usage-and-ratecard-api-integration"></a>Casi di utilizzo Cloudyn ITFM attivati per l'integrazione di utilizzo e API RateCard
Casi attivati per l'uso di utilizzo comune Cloudyn ITFM e RateCard APIs includono:

+ Consente di **Analisi dei costi** - cloud costi l'interruzione in qualsiasi dimensione identificativo nativi (provider, servizio, account, l'area geografica e così via). L'utilizzo di Azure e RateCard APIs impostare un'operazione semplice, fornendo la suddivisione più granulare di utilizzo e costo per ogni account, quindi raggruppati e filtrati in base a Cloudyn e visualizzati all'utente, in formato tabulare o elemento grafico dati.

![Grafico a torta di analisi dei costi][3]

+ **Costo allocazione 360** - finanze consente ai responsabili IT per individuare la suddivisione del costo effettivo, driver e tendenze della loro distribuzione cloud. Consente ulteriormente responsabili facilmente associare le spese di distribuzione business unit, i reparti, aree geografiche e altre informazioni, fornire informazioni dettagliate sui senza precedenti in costi cloud e agevolare showbacks e i rifiuti di addebito enterprise. L'utilizzo di Azure e RateCard APIs serve come input al motore di allocazione costo del Cloudyn, si integra con l'API definendo metodi e business per l'assegnazione delle risorse senza tag o untaggable.

![Grafico di allocazione costi 360][4]

+ **Ridimensionamento di cost-Effective** - consigli ridimensionamento destro per poco utilizzate macchine virtuali, riducendo le spese del cliente in macchine di grandi dimensioni o eccessiva provisioning. Non esaminando macchine virtuali CPU e metriche di RAM (tramite API di prestazioni), ore di run-time (tramite l'uso dell'API) e costo (tramite API RateCard). Cloudyn vengono fornite indicazioni di ridimensionamento destro basate sulle risorse poco utilizzate CPU o RAM (prestazioni) e calcola risparmi stimata moltiplicando il delta del prezzo (RateCard) tra macchine virtuali per il tempo-utilizzo effettivo (utilizzo) del computer poco utilizzati.

![Costo effettivo di ridimensionamento][5]

+ **Consigli portabilità cloud** - fornisce consigli finanziari nel cloud portabilità. Esamina i costi di corrente di un utente di risorse cloud distribuiti dei fornitori principali cloud e confronta al costo di una distribuzione equivalente in Azure. Fornisce quindi granulare, per ogni risorsa, finanziario basate su portabilità consigli in Azure. Dopo la valutazione della distribuzione equivalente necessaria in Azure (in base alle preferenze utente e la metrica prestazioni), Cloudyn utilizza l'API RateCard per valutare il costo di distribuzione equivalente in Azure.

+ **Report sulle prestazioni** - attivate le prestazioni di Azure API, questi report offrono una matrice di caratteristiche di utilizzo della CPU e RAM consigli l'ottimizzazione. Di seguito è un esempio di report utilizzo istanza, eseguire una presentazione di scomposizione istanza dall'utilizzo della CPU Media.

![Report sulle prestazioni][6]

+ **Gestione di categoria** - una potente caratteristica di Cloudyn che consente alle risorse non organizzate cloud. Fornisce agli utenti la possibilità di creare le categorie univoche (tag) per la misurazione efficace e la segnalazione in linea con il procedure aziendali. Inoltre, gli utenti possono facilmente regolare categorizzare incoerente tagging (ad esempio gli errori di battitura e altre differenze) e rileva automaticamente le risorse senza tag per attribuzione precisi sui costi.

![Categoria Manager][7]

## <a name="video"></a>Video

Ecco un breve video che mostra i clienti di Azure utilizzo Cloudyn per Azure e le API di fatturazione di Azure, per ottenere informazioni approfondite dai dati consumo Azure.

> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## <a name="next-steps"></a>Passaggi successivi

+ Avviare una versione di valutazione gratuita [Cloudyn per Azure](https://www.cloudyn.com/microsoft-azure/) per vedere come è possibile ottenere la trasparenza di costo con analitica e creazione di report personalizzate per la distribuzione di cloud di Microsoft Azure.
+ Per una panoramica delle RateCard APIs e l'utilizzo delle risorse di Azure, vedere [ottenere informazioni approfondite nel consumo di risorse di Microsoft Azure](billing-usage-rate-card-overview.md) .
+ Controllare i [Riferimenti alle API REST di fatturazione Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) per ulteriori informazioni su entrambe le API che fanno parte del set di API fornite da Gestione risorse di Azure.
+ Se si preferisce approfondire direttamente il codice di esempio, vedere esempi di codice il nostro Microsoft Azure fatturazione API su [Azure esempi di codice](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Ulteriori informazioni
+ Per ulteriori informazioni sulle offerte di Microsoft Azure Enterprise Agreement (EA), visitare la pagina [Azure licenze per le aziende] (https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Vedere l'articolo [Panoramica di gestione risorse Azure](azure-resource-manager/resource-group-overview.md) per altre informazioni su Gestione risorse di Azure.
+ Per ulteriori informazioni sulla suite di strumenti necessari per facilitare il per comprendere gli cloud dedicato, fare riferimento all'articolo Gartner [Mercato Guida per gli strumenti di gestione finanziaria IT (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png
