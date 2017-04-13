<properties 
    pageTitle="Configurare e usare l'API di consigli risorse computer | Microsoft Azure" 
    description="API di raccomandazioni Microsoft integrato con domande frequenti su risorse computer Azure" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 

#<a name="setting-up-and-using-machine-learning-recommendations-api-faq"></a>Configurazione e uso Machine Learning consigli API domande frequenti su


**Che cos'è consigli?**

>[AZURE.NOTE]È necessario iniziare a usare il servizio di Cognitive API consigli invece di questa versione. Il servizio Cognitive consigli andrà a sostituire il servizio e tutte le nuove funzionalità da sviluppare sono. Offre nuove funzionalità, come il batch di supporto, un migliore API Explorer, un esperienza di superficie, più coerente l'iscrizione/fatturazione API chiaro e così via.
> Ulteriori informazioni sulla [migrazione al nuovo servizio cognitiva](http://aka.ms/recomigrate)

Per le aziende e imprese che si basano su raccomandazioni per incrociate e vendere su prodotti e servizi ai propri clienti, consigli in apprendimento Azure fornisce un motore consigli modalità self-service. È un'implementazione di filtro collaborativo che utilizza GPO matrice come algoritmo dei core. Gli sviluppatori di applicazioni possono accedere con l'API REST consigli. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Cosa può fare con suggerimenti?**

CONSIGLI accetta come input un elemento o un insieme di elementi e restituisce un elenco di suggerimenti pertinenti. Ad esempio: un cliente di un rivenditore online fa clic su un prodotto. Rivenditore online invia tale prodotto come input per consigli ottiene in un elenco di prodotti e decide che questi prodotti verrà visualizzato al cliente. È consigliabile utilizzare raccomandazioni per ottimizzare il negozio online o anche per informare l'interno centro delle vendite di reparto o una chiamata.

**Esistono alcune limitazioni d'uso?**

Consigli comporta le limitazioni di utilizzo seguenti:
* Numero massimo di modelli per abbonamento: 10
* Numero massimo di elementi che può contenere un catalogo: 100.000
* Il numero massimo di punti in cui vengono mantenute è ~ 5.000.000. Meno recente verrà eliminato verranno caricati o segnalati nuovi file.
* Dimensioni massime dei dati che è possono inviare tramite posta elettronica (ad esempio, importazione catalogo dati, importare dati di utilizzo) sono 200 MB
* Numero di transazioni al secondo indica di generazione modello consigli che non è attiva è indica ~ 2. Genera un modello consigli attivo può contenere fino a 20 indica.

##<a name="purchase-and-billing"></a>Acquisto e fatturazione 


**Quanto costa costo consigli durante il periodo di avvio?**

Consigli è un servizio basato su sottoscrizione. In carica si basa su volume delle transazioni al mese. È possibile controllare [offerta pagina] (https://datamarket.azure.com/dataset/amla/recommendations) in Microsoft Azure Marketplace per informazioni sui prezzi.

**Sono disponibili i costi associati consigli traccia e archiviare attività dell'utente per uno specifico utente?**

Non al momento.

**Consigli dispone di una versione di valutazione gratuita?**

Esiste un itinerario gratuito che è limitato a 10.000 transazioni al mese.

**Quando per la fatturazione per consigli?**

Un abbonamento a pagamento è un abbonamento per cui esiste una tariffa mensile. Quando si acquista un abbonamento a pagamento, immediatamente addebitate per l'utilizzo del primo mese. Si addebitato l'importo associato l'offerta nella pagina sottoscrizione (più imposte applicabili). Questo addebito mensile viene effettuato ogni mese nella stessa data di calendario l'acquisto originale finché non si annulla l'abbonamento. 

**Come è possibile passare a un servizio di livello superiore?**

È possibile acquistare o aggiornare l'abbonamento da [offerta pagina] pagina (https://datamarket.azure.com/dataset/amla/recommendations) in Microsoft Azure Marketplace.

Quando si aggiorna un abbonamento:

* Transazioni rimangono l'abbonamento precedente non vengono aggiunti alla nuova sottoscrizione. 
* Si paga prezzo completo per la nuova sottoscrizione, anche se si dispone di inutilizzato le transazioni sull'abbonamento precedente.

Processo per l'aggiornamento di un abbonamento:

* Nevigate la pagina [offerta] (https://datamarket.azure.com/dataset/amla/recommendations).
* Accedere al Marketplace se non è ancora effettuato l'accesso.
* Nel riquadro destro sono elencati tutti i piani disponibili. Fare clic sul pulsante di opzione per il piano a cui a che si desidera eseguire l'aggiornamento.
* Se si desidera aggiornare, fare clic su **OK**. Se non si desidera aggiornare, fare clic su **Annulla**.

**Importante** Leggere con attenzione la finestra di dialogo prima dell'aggiornamento perché ci sono implicazioni fatturazione e l'utilizzo.

**Quando terminerà l'abbonamento a consigli**

L'abbonamento terminerà quando si Annulla. Se si desidera annullare le sottoscrizioni, vedere le istruzioni seguenti.

**Come si annulla l'abbonamento consigli?**

Per annullare l'abbonamento, utilizzare la procedura seguente. Se l'abbonamento corrente è un abbonamento a pagamento, l'abbonamento continua a essere valide fino alla fine del periodo di fatturazione corrente. Se è necessaria l'annullamento di efficienti immediatamente, contattaci al [Supporto tecnico Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

**Nota** L'annullamento dell'abbonamento prima del termine del periodo di fatturazione o per le transazioni non utilizzate in un periodo di fatturazione, non viene assegnato alcun rimborso.

* Passare a [offerta pagina] (https://datamarket.azure.com/dataset/amla/recommendations).
* Accedere al Marketplace se non è ancora effettuato l'accesso.
* Fare clic su **Annulla** a destra del nome di set di dati e stato. È possibile utilizzare la sottoscrizione fino alla fine del periodo di fatturazione corrente oppure è stato raggiunto il limite di transazione (a chiunque si verifichi prima).

Se si desidera annullare l'abbonamento immediatamente in modo è possibile acquistare un nuovo abbonamento, file ticket al [Supporto tecnico Microsoft](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

##<a name="getting-started-with-recommendations"></a>Guida introduttiva a consigli

**È raccomandazioni per uno specifico utente?** 

Suggerimenti in apprendimento è per le aziende e imprese che si basano su raccomandazioni per incrociate e vendere su prodotti o servizi ai propri clienti. Se si dispone di un sito Web per i clienti, forza vendita, un interno forza vendita o un call center e in caso di un catalogo di più decine di prodotti o servizi, le basi trarre vantaggio da tramite consigli. 

Sperimentare consigli è progettato per essere semplice. La versione corrente in base all'API richiede competenze di programmazione di base. Se è necessaria assistenza, contattare il produttore che ha sviluppato il sito Web. Se si dispone di un reparto IT interno o uno sviluppatore sessioni, si dovrebbe essere possibile consigli per l'utilizzo. 

**Quali sono i prerequisiti per la configurazione consigli?**

Consigli richiede si dispone di un elenco delle scelte dell'utente in relazione al catalogo. Se si t dispone di un file di log e si dispone di un sito Web esposto a cliente, suggerimenti possono raccogliere attività dell'utente dell'utente. 

Consigli richiede un catalogo di prodotti o servizi. Se si t sono del catalogo, consigli è possono utilizzare i dati di utilizzo dei clienti e convertire un catalogo. Un catalogo implicito non verrà inclusi gli elementi che non sono stato registrati come parte delle transazioni utente.

**Come configurare suggerimenti per la prima volta?**

Dopo [la sottoscrizione] (https://datamarket.azure.com/dataset/amla/recommendations) a consigli, utilizzare la documentazione API in [Azure Machine Learning consigli-Guida introduttiva](machine-learning-recommendation-api-quick-start-guide.md) per configurare il servizio.

**Dove è possibile reperire documentazione API?** 

La documentazione API è [Azure Machine Learning consigli Quick Start Guide](machine-learning-recommendation-api-quick-start-guide.md).

**Quali opzioni necessario caricare dati catalogo e l'uso in consigli**

Sono disponibili due opzioni per il caricamento dei dati di catalogo e l'utilizzo: È possibile esportare i dati dal sistema CRM o altri log e caricarlo in consigli oppure è possibile aggiungere tag al sito Web che tiene traccia delle attività dell'utente. Se si utilizza il metodo di quest'ultimo, i dati vengono archiviati in Azure.

##<a name="maintenance-and-support"></a>Manutenzione dei servizi e supporto tecnico

**Le dimensioni il set di dati possibile?**

Ogni set di dati può contenere fino a 100.000 elementi catalogo e fino a 2048 MB di dati di utilizzo.
Inoltre, una sottoscrizione può contenere fino a 10 set di dati (modelli).

**Dove si può trovare supporto tecnico per i suggerimenti?**

Supporto tecnico è disponibile nel sito del [Supporto Microsoft Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) .

**Dove reperibili le condizioni di utilizzo**

[Consigli API condizioni del servizio di Microsoft Azure apprendimento](https://datamarket.azure.com/dataset/amla/recommendations#terms).



 
