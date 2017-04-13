<properties 
    pageTitle="Azure impegno per dispositivi mobili Guida introduttiva alle procedure consigliate"
    description="Recupero Guida introduttiva per Azure Mobile coinvolgimento e procedure consigliate per la selezione" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="wesmc7777"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="10/04/2016"
    ms.author="wesmc;ricksal"/>

# <a name="azure-mobile-engagement---getting-started-guide-with-best-practices"></a>Azure impegno per dispositivi mobili - Guida introduttiva alle procedure consigliate

## <a name="overview"></a>Panoramica

**Lo schermo per dispositivi mobili è uno spazio estremamente competitivo:** In 2013, uno Studio trova che il dispositivo mobile Media aveva 27 applicazioni installate. Gli utenti state dedicate in genere 30 ore al mese le applicazioni. La maggior parte il tempo trascorso su social networking e gioco (circa il 20 ore). Entro 2014, Android market era circa 1,5 milioni applicazioni per gli utenti cui scegliere. Apple store contenuti circa 1,2 milioni di App. Uso di app per dispositivi mobili è ancora crescente, poiché gli sviluppatori gareggiare in questo mercato. 

Dall'utente per dispositivi mobili Media per installare e disinstallare App con frequenza elevata a seconda di modificare gli interessi e di esperienze in app. Per determinare il successo di un'app diventa fondamentale utili limitarsi quanti utenti installano l'app. È importante sapere come utile l'app viene eseguita e se tale tendenza l'utilizzo in fase di modifica. Diventano importanti gli aspetti seguenti:

- Sono inizio fino a trovare le app obsoleti o che non interessano gli utenti? 
- Quanti utenti sono state interrotte, utilizzare l'app tutto? 
- In app acquisti più popolari verso l'alto o verso il basso?
- Gli utenti non riescono a completare flussi di lavoro a causa di problemi con l'app o mancanza di interesse? 
- Impossibile mantenere l'app utili e rilevanti premendo contenuto aggiornato agli utenti? 
- Da questo contenuto aggiornato essere lo stesso per tutti gli utenti o basato su segmenti utente basati sul comportamento nell'app? 
 
Risposte alle domande simili ai seguenti potrebbero aiutare estendere la durata e dei ricavi dell'app. Sono inoltre consente di definire e mantenere la base di utenti. 

Elementi multimediali correlati App è solitamente alcune delle più alta conservazione tra gli utenti. Un motivo è che rendono contenuto aggiornato disponibili costantemente agli utenti. Risultano in genere anticipato alle notifiche push utile indirizzati a un segmento di utenti in un grande impatto sulla conservazione di app. 

Il programma di Azure Mobile coinvolgimento consentono di estendere la conservazione e criteri di conservazione dell'app, fornendo un metodo per raccogliere e analizzare informazioni dettagliate sull'utilizzo dell'app. Aiuta classificare in base al comportamento degli utenti e creare campagne attivo per fornire le notifiche push e i messaggi in app a segmenti identificato utente. Indicatori di prestazioni chiave (KPI) misurano attivo come gli utenti sono con diversi aspetti dell'app. Azure coinvolgimento di dispositivi mobili sono disponibili i metodi per determinare questi indicatori KPI. Può essere utile aumentare il rendimento dell'investimento (ROI) di fornitura dell'infrastruttura che necessari per aumentare il coinvolgimento con l'app per dispositivi mobili. 

Per ottenere il massimo da Azure Mobile coinvolgimento, è necessario iniziare con un piano di coinvolgimento ben progettata. Il piano per identificare i dati granulari che è necessario essere in grado di segmento di utenti. In app esperienze e questo può essere basato su comportamento. Affinché il piano per l'esito positivo, è consigliabile definire chiaramente l'indicatore KPI che consentiranno di misurare gli obiettivi dell'app. Con indicatori di prestazioni Cancella definiti, è possibile incorporare facilmente la logica necessaria nell'app per raccogliere dati correttamente granulare che verrà utilizzato per analizzare e valutare gli indicatori KPI. In questo argomento è una Guida best practice guide per la definizione di indicatori KPI da utilizzare con il piano di progetto. 


## <a name="step-1-define-your-kpis-to-fit-the-bet-model"></a>Passaggio 1: Definire gli indicatori KPI per adattare il modello di elementi di maggiore rilevanza


Correttamente la definizione di indicatori KPI può essere difficile per completare. App progettato per il settore diversi dispongono di specifiche ed obiettivi. Ciò potrebbe far confondere l'approccio. Per informazioni su come evitare il problema, obiettivi e indicatori KPI devono essere suddivise in tre categorie principali: **Business**, **impegno**e **tecniche**. Verrà visualizzata la cosiddetta il **modello di elementi di maggiore rilevanza**.

Un buon piano avrà in genere obiettivi con indicatori KPI che misurano successi in ognuna delle categorie seguenti del modello di elementi di maggiore rilevanza.


#### <a name="business-kpis"></a>Indicatori di prestazioni chiave aziendale

Indicatori di prestazioni chiave aziendale dovrebbe essere la parte più semplice per creare. Probabilmente già definiti questi elementi in una forma quando è pianificato l'app per dispositivi mobili. Questi indicatori di prestazioni chiave in genere Guida misura dei ricavi e il ROI per si app. L'elenco seguente include alcuni esempi di indicatori KPI Business che può assistere l'utente durante la definizione del indicatori di prestazioni:

- Indicatori di prestazioni chiave Business elementi multimediali
    - Fatto clic sul numero di annunci
    - Numero di visite pagina per ogni utente
    - Numero di sottoscrizioni corrente
- Gioco Business KPI
    - Numero di acquisti nell'app
    - Media delle entrate per utente (ARPU)
    - Tempo dedicato per la sessione
    - Giorni riprodotti e corrente nel livello di gioco
- Indicatori KPI E-Commerce Business
    - App giorni utilizzato
    - Media delle entrate per utente (ARPU)
    - Importo medio nel carrello durante l'estrazione
    - Categoria di prodotto per la maggior parte delle visualizzazioni e gli acquisti
- Indicatori KPI Business banca e assicurazione
    - Numero di account
    - Caratteristiche attivati
    - Pagine di offerta visitate
    - Avvisi selezionato o attivato      



#### <a name="engagement-kpis"></a>Indicatori di prestazioni chiave coinvolgimento

Un indicatore KPI coinvolgimento è un indicatore di prestazioni misurare l'impegno degli utenti. Le tendenze in quest ' area consentono di determinare i criteri di conservazione dell'app. Di seguito sono riportati alcuni indicatori di prestazioni di esempio per questo tipo di indicatori KPI:

- Utenti attivi negli ultimi 7 giorni
- Numero di utenti inattivi per negli ultimi sette giorni
- Numero di utenti che non è stato utilizzato l'app 30 giorni  

Alcuni fattori esterni più evidenti possono influire gli indicatori di quest ' area. Ad esempio, provare a un dispositivo mobile per essere con un utente in qualsiasi momento. Questa operazione può o potrebbe non essere vera. Un'app di gioco potrebbe è solitamente maggiore utilizzo intorno festività quando un giocatore possibile che venga riprodotto più durante il lavoro o indietro dell'istituto di istruzione.   

Ben definiti indicatori KPI in questa categoria devono consentono di misurare la relazione tra le app e i clienti.



#### <a name="technical-kpis"></a>Tecnici indicatori KPI

Indicatori di prestazioni in questa categoria consentono di determinare se l'app è corretto, non funziona o un arresto anomalo. Questi indicatori di possono misurare lo stato dell'app e individuare i problemi di usabilità che potrebbero impedire agli utenti di utilizzare l'app. Informazioni raccolte per questa categoria potrebbero inoltre contenere informazioni sulle prestazioni da pertinenti al team di marketing. I dati potrebbero essere utili per risolvere eventuali problemi IT e supporto tecnico ai team di identificare bug non segnalati. 
 
Ecco alcuni esempi di indicatori KPI tecnici:

- Conta. numeri e le informazioni sull'eccezione non gestita o gestito 
- Timestamp per ultimo arresto anomalo
- Fatto clic sul pulsante ultimo o ultima pagina visitata
- Utilizzo di memoria dell'app
- Frequenza di App
- Versione del sistema operativo che l'app è in esecuzione
- Versione App

Definire questi indicatori KPI per aiutare misurano le prestazioni di app e risolvere potenziali bug. Questo indicatori consentono di ridurre il tempo che necessario per offrire una soluzione per i clienti. Potrebbe inoltre consentono di definire un segmento di utenti che hanno problemi un particolare. È possibile usare tale segmentazione degli utenti per creare le campagne per recapitare le notifiche relative a disponibili correzioni e potenziali promozioni per recuperare la soddisfazione del cliente. 


#### <a name="playbook-exercise-1-create-your-kpi-dashboard"></a>Playbook esercizio 1: Creare il dashboard di indicatori KPI

Quando si definisce la strategia di marketing, gli indicatori KPI devono presentare una visualizzazione per ognuno degli obiettivi principali. Dovrebbero essere chiaramente di coordinate che consentono di raccogliere informazioni importanti per monitorare l'app e il comportamento dell'utente finale.

Creare un dashboard di indicatori KPI che contiene le informazioni relative alla

1.  Quali sono gli indicatori KPI per l'app?
2.  Quali dati punta sarà utilizzare per rappresentare ogni indicatore KPI?
3.  Dove si trovano questi dati per l'applicazione (ad esempio dello schermo, impostazioni, sistema...)
4.  È possibile riprodurre una sequenza di impegno per l'indicatore KPI?

È possibile usare il foglio di lavoro di **Generatore di indicatori KPI** nel nostro [Modello Playbook Media] [ Media Playbook link] per esempi e linee guida.



## <a name="step-2-your-engagement-program"></a>Passaggio 2: Il programma di partecipazione attiva


Un programma di grande impegno per dispositivi mobili deve essere considerato un componente chiave dell'app. Trattarsi assolutamente un ottimo programma benvenuto che viene eseguito per un utente durante i giorni prima dell'uso delle app. Ciò può provocare un effetto molto positivo coinvolgimento e conservazione dell'app. Studi sono mostrato che la maggior parte degli utenti sospendere l'utilizzo di un'app primi giorni dopo l'installazione. Si desidera cercare soddisfare o superare interesse le aspettative dei clienti in anticipo mentre l'utente riceve ancora l'app. Assicurarsi che la presentazione il valore di chiave e i vantaggi dell'app ai clienti. 


![](./media/mobile-engagement-getting-started-best-practices/unsegmented-push-notifications.png)

Notifiche push sono migliore di impegni iniziale con utenti di dispositivi mobili. Tuttavia, devono prestare particolare attenzione quando gli utenti per le notifiche push segmenti. Perché dopo l'aspetto di un utente che sta ricevendo posta indesiderata o che non interesse notifiche, può avere impatto negativo. In pochi clic, un utente può eliminare l'applicazione non è mai restituire. L'utente deve ricevere valore in app altamente personalizzato anziché la posta indesiderata generica.

Dopo che gli utenti sono attivamente, può essere utile il programma di impegno unità altri aspetti dell'app.

Ad esempio, impostare una campagna che richiede agli utenti attivi di valutare l'app. Poiché questo segmento di utenti è più attivo e ha l'esperienza con la maggior parte delle app, si aspetta per consentire la valutazione più precisa. Dopo avere inserito una classificazione di app elevato, può essere utile aumento dei download organico dell'app anche ridurre i costi di acquisizione dei clienti nuovo.



#### <a name="engagement-sequence"></a>Sequenza di impegno


Un programma di impegno globale include sequenze impegno diverso. Ogni sequenza l'obiettivo di raggiungere gli obiettivi diversi.


###### <a name="life-push-sequence"></a>Sequenza di push di vita


Gli obiettivi per una sequenza di push vita_utile sono diversi in base al ciclo di vita di impegno dell'utente con l'app. Un utente particolare può essere nuovo, attivo o inattivo molto. Nelle diverse fasi di un ciclo di vita, gli utenti potrebbero trarre vantaggio dal contenuto aggiornato in forma di suggerimenti o collegamenti alla documentazione. 

Ad esempio un nuovo utente può aiutare orientarsi a un'app o trarre vantaggio da un nuova incentivi utente simili al seguente la prima volta che avvia l'app...

*"Contenti consentire al cliente integrata! È necessario ricordare di accesso per ottenere il mese 1 ° gratuito!"*


###### <a name="behavioral-push-sequence"></a>Sequenza di push visive

La sequenza di comportamento push obiettivo per aumentare l'utilizzo in base a un comportamento utente raccolto per l'app.  

Ad esempio, un utente molto attivo di un'app di calcio fantasia essere utile vengano viva l'attenzione con la seguente notifica push...

*"John si desidera trovare calcio gravi! Accedere alla sezione per il Campionato e vincono libero accesso per la SuperBowl!"*


###### <a name="alerting-push-sequence"></a>Sequenza di notifica push

Gli utenti verranno apprezzare le notizie basate su propri interessi. Una sequenza di avviso push migliora coinvolgimento inviando avvisi in base a interessi un utente ha mostrato chiaramente. Può trattarsi esplicito quando un utente seleziona i propri interessi nell'app. È stato anche determinare in modo implicito in base a dati raccolti durante l'interazione dell'utente con l'app.

Ad esempio, l'utente di un'app di E-Commerce regolarmente può acquistare un marchio specifico di caffè che acquisite con un indicatore KPI di business. L'avviso seguente è possibile migliorare coinvolgimento dell'utente con l'app.
 
*"Ciao seguenti, uno dei marchi preferiti di caffè verrà visualizzato in vendita 25% disattivare la prima settimana del settembre 2015. Grazie per come cliente e per verificare che sono state presente".*

###### <a name="rentention-push-sequence"></a>Sequenza di push Rentention

Questa sequenza l'obiettivo di mantenere gli utenti che utilizzano un campagne di notifica push ripetitive utili unità regolare gli strumenti di catturare l'app. Questo può migliorare conservazione app se l'utente piace le interazioni. 

Ad esempio, l'utente di un'app correlate Sport potrebbe ricevere la seguente notifica push settimanale in base ai team preferito dell'utente:

*"Per partecipare all'estrazione 200 punti, vedere voto se New York Yankees vincono loro gioco questa settimana contro Roma blu Jays!"*


#### <a name="the-3w-approach"></a>L'approccio 3W

Masterizzazione sequenze push diversi consente di comunicare agli utenti finali. Tuttavia, è comunque necessario utilizzare l'approccio 3W per personalizzare le notifiche. L'approccio 3W deve prendere in considerazione chi, cosa accade e il momento per ogni notifica. Se si soddisfano chiaramente questi tre domande si notifiche deve essere correttamente incentrato per coinvolgimento.

![](./media/mobile-engagement-getting-started-best-practices/who-what-when.png)



###### <a name="who-the-user-segment-that-will-receive-messages"></a>Chi: L'utente segmento che riceverà i messaggi

Invio di notifiche agli utenti deve essere considerato un canale di comunicazione molto sensibili. Assicurarsi che le notifiche di che obiettivo per inviare a un segmento di utenti e sono limitate a fini di tale segmento di utenti. Una notifica in modo non corretto indirizzata è molto probabile che hanno un effetto negativo su un utente. È consigliabile posta indesiderata con l'app disinstallata. 

Utilizzare una combinazione di criteri specifici tecnici e di funzionamento quando si definisce segmenti utente che riceveranno le notifiche. Un semplice esempio che definisce un segmento di utenti potrebbe essere simile alla seguente istruzione:

"Tutti gli utenti che ha avviato un'applicazione per dispositivi mobili per la prima volta 3 giorni fa e visitati pagina di accesso due volte senza effettivamente il completamento di un account di accesso".
 
L'istruzione consente di identificare i dati desiderati per la raccolta per supportare uno scenario specifico.


###### <a name="what-the-message-that-you-will-send"></a>Che cosa: Il messaggio da inviare

**Tonalità**

Negli impegni usare un tono più appropriato per il per gli utenti segmentati. Questo è un ottimo modo per connettersi con gli utenti finali e promuovere l'interesse di un utente nell'app. 

**Reindirizzamento**

Utilizzare una notifica push per più di aprire l'applicazione. Se il messaggio di notifica fornisce un contesto, ad esempio notizie trasmissione o una promozione di prodotto, questa notifica può completa collegamento direttamente al contenuto all'interno dell'applicazione. A tale scopo, è necessario creare una combinazione di URL per consentire l'applicazione di gestire il reindirizzamento. Quando si lavora sulle sequenze coinvolgimento, si tratta di un passaggio importante non dimenticare.

Inoltre, è possibile gestire il reindirizzamento per altri sistemi. Con un URL di azione, ad esempio, è possibile reindirizzare gli utenti finali a molti altri sistemi incluse le operazioni seguenti:

- Un sito Web
- Una cassetta postale con già configurato la posta elettronica
- Una casella con SMS
- Un servizio di connessione
- Direttamente all'applicazione di archiviazione per la valutazione dell'applicazione. 

In questo modo molte opportunità di partecipazione a utenti finali e creare regole automatiche per migliorare le prestazioni.


**Formato/contenuto**

Diversi tipi e formati di notifica Push:

1. **Annunci** : consente di inviare messaggi pubblicitari agli utenti in diversi momenti (dall'app nell'app o in qualunque momento).
2. **Sondaggi** : consente di raccogliere informazioni da utenti finali ponendo domande. Risposta a queste domande è quindi disponibili durante la creazione dei criteri agli utenti finali di destinazione.
3. **Inserisce dati** : consente di inviare un file di dati binario o base 64 per aggiornare l'app. Le informazioni contenute in un push di dati viene inviate all'applicazione per personalizzare gli utenti nell'app. L'applicazione deve essere progettato per supportare i dati in un push di dati.
4. **Riquadri (solo per Windows Phone)** : è possibile usare Microsoft Push Notification Service (MPNS) per inviare la notifica Push nativo contenente dati XML (supportati dal SDK versione 0.9.0. Il contenuto finale per le sezioni non può superare 32 kilobyte.). Viene visualizzato il messaggio direttamente nel riquadro della scheda.
5. **La visualizzazione Web** : una visualizzazione web è un popup contenuto web che contiene. Questa opzione viene visualizzata quando l'utente finale ha fatto clic su notifica push. Una visualizzazione web consente di altre interagiscono con l'utente finale.
 
>[AZURE.NOTE] Assicurarsi che il contenuto che si intende inviare come le notifiche push rispetti la piattaforma rispettiva (iOS, Android e Windows) linee guida per lo sviluppo di App e invio di notifiche push.

 


###### <a name="when-the-timing-of-your-campaign"></a>Quando: L'intervallo della campagna

Quando l'orario migliore per attivare una campagna che attiva le notifiche push? Devono essere manuale o automatico? Deve ricorrente? Determinare il momento giusto o frequenza è fondamentale per coinvolgimento degli utenti con i risultati migliori. Per ogni sequenza coinvolgimento e scenario, è necessario specificare quando sarà l'orario migliore per inviare le notifiche push. Ecco alcuni esempi possibili:

![](./media/mobile-engagement-getting-started-best-practices/campaign-timing-examples.png)

Se si intende inviare notifiche molti quotidianamente, è necessario effettuare attentamente che gli utenti sarà in grado delle comunicazioni come posta indesiderata. 

Azure coinvolgimento Mobile in due modi per evitare le comunicazioni viene considerate come posta indesiderata. Prima di tutto usare segmentazione singole per assicurarsi che non sono destinati gli stessi utenti. Inoltre, Azure Mobile coinvolgimento fornisce una caratteristica "quota". Questa caratteristica è possibile limitare le notifiche inviate per una campagna. Ad esempio, l'impostazione di una quota predefinita a 5 per settimana garantisce che un utente incluso come parte del segmento di utenti campagna riceve le notifiche non più di 5 per tale settimana.





#### <a name="playbook-exercise-2-create-your-engagement-program"></a>Playbook esercizio 2: Creare il programma di partecipazione attiva

Dedicare alcuni riepilogo obiettivi e definizione campagne che si prevede di riprodurre utilizzando sequenze specifiche. Verificare che si applica l'approccio 3W alle notifiche in campagne. 

Usare il foglio di lavoro di **Programma coinvolgimento** nel [Modello Playbook Media] [ Media Playbook link] per esempi e linee guida.


## <a name="step-3-app-integration"></a>Passaggio 3: App integrazione


#### <a name="create-a-tag-plan"></a>Creare un piano di tag

Per integrare Azure Mobile coinvolgimento l'app sarà necessario creare un piano di tag. Il piano di tag è l'elemento essenziale del progetto. Definisce la relazione tra le specifiche marketing, il flusso di lavoro dell'applicazione e i dati reali tag raccolti nell'app misurare indicatori KPI. Indica quali analitica sarà possibile visualizzare nel portale. Consente inoltre di definire segmenti utente e inviare le notifiche push attivo a partecipare agli utenti finali. Dopo avere il piano di tag definito, aggiungere il codice di integrare nell'app è semplice con Azure Mobile coinvolgimento SDK.

Un piano di tag non deve contrassegnare tutti gli elementi in un'applicazione. Deve contenere solo i dati di contrassegno che fanno parte di una strategia di impegno per dispositivi mobili. Questo sarà probabilmente diverso tra le applicazioni. Il [Modello Playbook Media] [ Media Playbook link] forniti da consente di Azure Mobile coinvolgimento si creazione un piano di tag con un determinato metodo. Utilizzare il foglio di lavoro **Piano Tag** come una Guida per la creazione del piano di tag.

Quando si definisce una sezione di tag nel foglio di lavoro, è molto specifiche. Questo è molto importante per evitare confusione. Dettaglio ogni previsto scenario in cui verrà inviato ogni tag. Includere il nome dell'attività in cui è incorporato ogni tag. Questo tutti includere nella parte **Informative** del foglio di lavoro. Foglio di lavoro piano tag dovrebbe essere il riferimento principale per la verifica del test. 

Nella sezione **dati da raccogliere** il team di sviluppo deve individuare tipi, nomi, i valori ed extra informazioni chiave/coppie necessari per ogni tag verrà incorporato nell'applicazione.

È consigliabile esaminare il piano di Tag con tutti i team associati al progetto. Apportare correzioni necessarie e confermare che tutto è deselezionata per team marketing e sviluppo.

Il foglio di lavoro di **istruzione di lavoro** può essere utilizzato per guidare che le persone coinvolte nel progetto.


#### <a name="data-types"></a>Tipi di dati

Si tratta di tipi di supporto di dati da Azure Mobile coinvolgimento comuni.

###### <a name="devices-and-users"></a>Dispositivi e i relativi utenti

Azure Mobile coinvolgimento Identifica utenti generando un identificatore univoco per ogni dispositivo. Questo ID è chiamato identificatore del dispositivo (o deviceid). Viene generato in modo che tutte le applicazioni in esecuzione su tale stesso dispositivo condividono lo stesso identificatore di dispositivo.

###### <a name="sessions-and-activities"></a>Sessioni e attività

Una sessione è un'istanza dell'app viene eseguita da un utente. Dal momento in cui che l'utente avvia l'app fino all'interruzione si estende la sessione.

Un'attività è un raggruppamento logico di un insieme di operazioni che può eseguire l'app durante una sessione. Si tratta di una schermata specifica nell'app, ma può essere che qualsiasi elemento definito da logica dell'applicazione. Almeno si consiglia di etichettare ogni schermata o attività per l'app. Consentono di comprendere il percorso dell'utente.


###### <a name="events"></a>Eventi

Gli eventi vengono utilizzati per segnalare l'interazione dell'utente con l'app. Possono essere azioni immediate, ad esempio la condivisione del contenuto o avviare un video. Tagging eventi fornirà con gli insiemi di dati che mostrano la modalità di interazione tra gli utenti con l'app. 

###### <a name="jobs"></a>Processi

Processi vengono utilizzati per segnalare le azioni che hanno una durata. Alcuni esempi possono essere:

- Esecuzione di chiamate API
- Tempo di visualizzazione degli annunci
- Durata delle attività in background 
- Durata processo di acquisto
- Visualizzazione di un video


###### <a name="errors"></a>Errori

Gli errori vengono utilizzati per segnalare problemi rilevati dall'applicazione. Ad esempio azioni dell'utente non corretti o errori di chiamata API.

###### <a name="application-information"></a>Informazioni sull'applicazione

Informazioni sull'applicazione (App Info) consente di contrassegnare i dati relativi a un'esperienza utente in un'applicazione. Viene generato da interazione dell'utente con l'applicazione. 

Per una chiave di informazioni app specificato, Azure Mobile coinvolgimento solo tiene traccia del valore più recente (nessuno). Informazioni di App, vengono visualizzate lo stato di un'applicazione o gli utenti finali. Ad esempio lo stato di log o gruppo di prodotti preferito dell'utente.

###### <a name="crash-data"></a>Dati di un arresto anomalo

Un arresto anomalo dati raccolti automaticamente con gli errori di applicazione report Mobile coinvolgimento SDK non gestiti dall'applicazione. Ad esempio un'eccezione non gestita che si verifica.


###### <a name="extra-data"></a>Dati aggiuntivi

Eventi, gli errori, attività e processi possono essere migliorati con parametri. Si tratta di uno sviluppatore può fornire come dati specifici dall'applicazione le informazioni aggiuntive. È importante per la definizione di segmentazione diffusamente. 

Ad esempio, il valore di un tag "articolo" consente agli utenti finali di segmento in base a chi visualizzare quel particolare articolo. Tuttavia, che non sia sufficiente. Può risultare più efficace se tale stesso tag "articolo" incluse anche informazioni aggiuntive, ad esempio "news_category" all'interno di un'attività. Questo potrebbe essere utile per determinare dinamicamente categorie preferite per l'utente. 

Informazioni aggiuntive viene segnalato come una coppia chiave/valore. Nell'esempio per l'applicazione di elementi multimediali, le informazioni aggiuntive per "news_category" è il valore per tale categoria. Ad esempio, "Sport", "risparmio" o "politica".





#### <a name="tag-and-sdk-integration"></a>Integrazione di tag e SDK 

Per istruzioni dettagliate per l'integrazione di Azure Mobile coinvolgimento SDK in un'applicazione, seguire la documentazione per la [Partecipazione attiva SDK integrazione](mobile-engagement-windows-store-integrate-engagement.md) nel sito Web di Azure. Scegliere la piattaforma di destinazione i collegamenti nella parte superiore della pagina.

È consigliabile creare progetti per due applicazioni basate su Azure Mobile coinvolgimento. Uno per lo sviluppo e gestione temporanea test e l'altro di gestione temporanea di produzione. Il team IT possa quindi alzare di livello di gestione temporanea test di produzione quando il test di accettazione utente ha esito positivo.



#### <a name="user-acceptance-testing-uat"></a>Accettazione utente (UAT)

Test di accettazione utente (UAT) permette di assicurarsi che tutto funzioni come previsto. Flussi di lavoro possono essere completati e raccolgono tutti i dati necessari in base al piano tag:
 
- Informazioni tagging devono essere disponibili in base alla documentazione concetti AZME
- Tutte le informazioni che necessarie raccolte (inclusi Extra info valore e App info)
- Corrispondenze nomenclatura secondo yout pianificare Tag
- Non esiste alcun tag duplicato inviato

Verificare accuratamente tutti i tipi di comportamento di notifica che non è stato incorporato nell'app

- Dati di annunci, sondaggi, inserisce tutte le potenzialità app e all'interno dell'applicazione
- Visualizzazioni di testo o del Web
- Aggiornamento di badge, categorie



#### <a name="setup"></a>Programma di installazione

Impostazione di Azure Mobile coinvolgimento è molto semplice. Tutta la documentazione relativa all'interfaccia utente è disponibile nel sito Web di Azure Mobile coinvolgimento [come esplorare l'interfaccia utente](mobile-engagement-user-interface-home.md).

Si consiglia di iniziare installando e configurando i ruoli destro e appartenenza ai ruoli per gli utenti del progetto. Consente di gestire l'accesso appropriato per la piattaforma per tutti gli utenti. I ruoli possono includere:

- Amministratori
- Sviluppatori
- Visualizzatori 

In seguito:
- Registrare il deviceID per verificare il proprio dispositivo.
- Vai alle impostazioni del proprio account e impostare il fuso orario di grafici e l'ora di recapito notifica impostare per il fuso orario.
- Passare a impostazioni dell'applicazione e registrare il "-informazioni App" è necessario a portata di mano utente finale di destinazione.

Per ulteriori informazioni su come eseguire la prima campagna di notifica push, esaminare [per imparare a uso e gestione di push per la comunicazione agli utenti finali](mobile-engagement-how-tos.md).



## <a name="conclusion"></a>Conclusioni


Programmi coinvolgimento sono iterativi e dovrebbe di migliorare il proprio continuamente mentre si modifica la soluzione più appropriata per l'app. 

Inizialmente, quando si sviluppa esperienza con coinvolgimento strategie non è consigliabile definire una strategia intera impegno globale. Un approccio passo dopo passo identificando gli indicatori KPI e su come ottimizzare il team. Strategia di impegno sarà univoco per ogni app.

Dopo che è stata sviluppata esperienza è possibile aggiungere le operazioni seguenti per le applicazioni di impegno:

- Verifica: Acquisite utenti ed è probabilmente definire origini raccolta di dati. Azure coinvolgimento mobile può essere collegato a origini dati insieme. È possibile eseguire il monitoraggio delle prestazioni di ogni origine. Queste informazioni saranno interessanti ottimizzare l'investimento di acquisizione di immagini. 

- A / B test: si tratta di un elemento essenziale del programma di partecipazione attiva. Ogni app è il proprio specifiche. Con / B-test, è possibile migliorare il programma di partecipazione attiva.

- L'ubicazione geografico: Si tratta di una grande opportunità per marchi. Grazie a questa caratteristica è possibile contattare il posto giusto e un'ora. È consigliabile verificare che raccolte dati sufficienti comportamento degli utenti finali prima di iniziare a utilizzare l'ubicazione geografico.

- Push dei dati: push di dati è un push invisibile. Push dei dati consente di personalizzare l'applicazione basata su comportamento degli utenti finali. Se, ad esempio un segmento di utenti consulta spesso prodotti high-tech, il proprietario dell'app possibile inviare un push di dati che verrà utilizzate la sua home page di contenuto ad alta tecnologia.



## <a name="next-steps"></a>Passaggi successivi

- [Creare un account Azure Mobile coinvolgimento](mobile-engagement-create.md).
- Visitare [definire la strategia di impegno Mobile](mobile-engagement-define-your-mobile-engagement-strategy.md) per ulteriori informazioni sulla definizione strategia coinvolgimento Mobile.



  

<!--Image references-->


<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
