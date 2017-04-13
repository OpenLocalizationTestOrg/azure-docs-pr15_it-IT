<properties
    pageTitle="Novità di Azure Toolkit per Eclisse"
    description="Informazioni sulle caratteristiche più recenti nel Toolkit di Azure per Eclisse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Novità di Azure Toolkit per Eclisse

## <a name="azure-toolkit-for-eclipse-releases"></a>Azure Toolkit per le versioni Eclisse

In questo articolo contiene informazioni sulle diverse versioni e aggiornamenti più recenti per il Toolkit di Azure per Eclisse.

> [AZURE.NOTE] È inoltre disponibile un Toolkit di Azure per IDE IntelliJ. Per ulteriori informazioni, vedere [Toolkit di Azure per IntelliJ].

### <a name="august-26-2016"></a>26 agosto 2016

Il Toolkit di Azure per Eclisse - versione di agosto 2016 include i miglioramenti seguenti:

* **Distribuzioni JDK personalizzato**. Il Toolkit di Azure per Eclisse supporta ora specificando e distribuzione di una versione JDK non autorizzata per il contenitore di Azure Web App:
  - Oltre JDKs fornito da Azure, è anche possibile scegliere da un'ampia scelta di versioni OpenJDK Zulu messo a disposizione su Azure Azul sistemi.
  - È inoltre possibile specificare il proprio distribuzione JDK se si carica come file ZIP al proprio account di archiviazione.
* **Miglioramenti per la visualizzazione di Azure Explorer**:
  - Supporto per la gestione di macchina virtuale utilizzando il nuovo modello di gestione risorse di Azure: è possibile dell'elenco, creare ed eliminare macchine virtuali basate su manager delle risorse senza uscire dall'IDE.
  - Supporto per Account di archiviazione blob management tramite Gestione risorse di Azure, che integra le funzionalità esistenti per la gestione degli account "classico" spazio di archiviazione.
* **Driver di Microsoft JDBC 6.0 per SQL Server**. Questo aggiornamento include il driver JDBC più recente di Microsoft SQL Server (versione 6.0), che è ora incluse come una raccolta che è possibile aggiungere facilmente ai progetti Java, in modo da sostituire la versione precedente.

### <a name="june-29-2016"></a>29 giugno 2016

Il Toolkit di Azure per Eclisse - 2016 giugno release include i miglioramenti seguenti:

* **Requisito di linguaggio 8**. Il Toolkit di Azure per Eclisse richiede linguaggio 8, anche se questo requisito è valido solo per il toolkit - le applicazioni è possono continuare a usare tutte le versioni di linguaggio supportate da Azure.
* **Supporto per la più recente di linguaggio JDKs**. Le versioni più recenti di JDKs Java sono ora supportate dal Toolkit Azure per Eclisse.
* **Supporto per v2.9.1 SDK Azure**. L'ultima versione di Azure SDK è ora il minimo prerequisito per il Toolkit di Azure per Eclisse.
* **Esempi di integrate**. Il Toolkit di Azure per Eclisse funzionalità diverse applicazioni di esempio per aiutare gli sviluppatori per iniziare.
* **Integrazione strumenti HDInsight**. HDInsight strumenti di Azure sono ora forniti con il Toolkit di Azure per Eclisse. Per ulteriori informazioni, vedere [Plug-in strumenti di HDInsight per Eclisse].
* **Il debug di linguaggio Web Apps remoto**. Il Toolkit di Azure per Eclisse supporta ora il debug remoto di linguaggio web apps nel servizio App Azure.
* **Supporto per la versione Eclisse Luna.** La nuova Eclipse IDE versione minima è Luna.

### <a name="april-12-2016"></a>12 aprile 2016

Il Toolkit di Azure per Eclisse - 2016 aprile release include i miglioramenti seguenti:

* **Supporto per v2.9.0 SDK Azure**. L'ultima versione di Azure SDK è ora il minimo prerequisito per il Toolkit di Azure per Eclisse.
* **Varie facilità di utilizzo, la velocità di risposta e prestazioni miglioramenti relativi al supporto di Azure Web App**. Ottimizzazione delle prestazioni in modalità di comunicazione con i risultati Azure il Toolkit di un numero in un'interfaccia utente più efficiente.
* **Possibilità di eliminare un contenitore di applicazione Web esistente in Azure all'interno di Eclisse**. Il Toolkit di Azure per Eclisse ora consente di eliminare un contenitore di Azure Web esistente senza uscire da Eclisse.

### <a name="march-7-2016"></a>7 marzo 2016

Il Toolkit di Azure per Eclisse - versione di marzo 2016 include i miglioramenti seguenti:

* **Supporto per la distribuzione rapida di applicazioni Java leggere**. Il Toolkit di Azure per Eclisse supporta ora la distribuzione rapida delle applicazioni di linguaggio leggere in Azure Web App contenitori, in modo che la distribuzione delle applicazioni di linguaggio ora richiede secondi anziché minuti.
* **Supporto per la gestione del Web App usando la visualizzazione di Esplora risorse Azure**. La visualizzazione di Esplora risorse Azure Toolkit supporta ora per voce, avviare e arrestare Azure Web Apps.
* **Distribuzioni Tomcat aggiornato, alla banchina e OpenJDK Zulu**. Il Toolkit di Azure per Eclisse fornisce supporto per le versioni aggiornate dei Tomcat, alla banchina e Zulu OpenJDK per le distribuzioni di linguaggio in servizi cloud Azure.

### <a name="january-4-2016"></a>4 gennaio 2016

Il Toolkit di Azure per Eclisse - versione di gennaio 2016 include i miglioramenti seguenti:

* **Supporto per gli aggiornamenti Zulu OpenJDK**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Aggiornamento Tomcat e distribuzioni alla banchina**. Sono state aggiornate le distribuzioni alla banchina e Tomcat quali sono disponibili in Microsoft Azure per l'utilizzo con il Toolkit di Azure per Eclisse.
* **Caratteristica uniformità tra Eclisse e Toolkit IntelliJ per Azure**. Il Toolkit di Azure per Eclisse e il [Toolkit di Azure per IntelliJ] supporta lo stesso set di funzionalità.

### <a name="september-1-2015"></a>1 settembre 2015

Il Toolkit di Azure per Eclisse - versione 2015 settembre include i miglioramenti seguenti:

* **Supporto per gli aggiornamenti Zulu OpenJDK**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Aggiornamento Tomcat e distribuzioni alla banchina**. Sono state aggiornate le distribuzioni alla banchina e Tomcat quali sono disponibili in Microsoft Azure per l'utilizzo con il Toolkit di Azure per Eclisse. (Queste distribuzioni consente agli sviluppatori di creare sviluppo rapido e progetti con il Toolkit di Azure per Eclisse di test.
* **Supporto per i riferimenti Tomcat e alla banchina aggiornati automaticamente**. Oltre a versioni specifiche di Tomcat e alla banchina quali sono disponibili in Azure, gli sviluppatori ora possono fare riferimento a una distribuzione considerata il "più recente (aggiornamento automatico)", che verranno aggiornati automaticamente per la distribuzione più recente di ogni versione principale di alla banchina o Tomcat la volta successiva che le istanze del ruolo sono inserimento Cestino. (Viene eseguito il riciclo automaticamente, ma gli sviluppatori possono attivare manualmente un Cestino tramite il portale di Azure) Questa nuova caratteristica significa che gli sviluppatori non è necessario ridistribuire le applicazioni affinché potranno disporre di software server aggiornato. (
*  Questa funzionalità è attualmente destinata solo ai fini di sviluppo e test o applicazioni non critiche e non è consigliabile produzione.)
* **Visualizzazione di Esplora risorse azure per BLOB, code e tabelle in archiviazione Azure**. In questo modo agli sviluppatori di eseguire un insieme di attività comuni con i relativi elementi dello spazio di archiviazione direttamente da Eclipse IDE. Ad esempio: l'eliminazione, durante il caricamento o download BLOB.

### <a name="august-1-2015"></a>1 agosto 2015

Il Toolkit di Azure per Eclisse - agosto 2015 release include i miglioramenti seguenti:

* **Gestione delle chiavi strumentazione approfondimenti applicazione**. Questo aggiornamento consente di acquisire, creare e gestire le chiavi strumentazione approfondimenti applicazione direttamente da Eclipse IDE.
* **Driver di Microsoft JDBC 4.1 per SQL Server**. Questo aggiornamento include il supporto per il driver JDBC più recente per Microsoft SQL Server.
* **Versione 2.7 di Azure SDK**. Questo aggiornamento più recente di Azure SDK è il nuovo prerequisito per il Toolkit installato in Windows. Si noti che non è necessario nei sistemi operativi non Windows.
* **Aggiornare il supporto per v7 Zulu OpenJDK**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].

### <a name="may-1-2015"></a>1 maggio 2015

Il Toolkit di Azure per Eclisse - versione di maggio 2015 include i miglioramenti seguenti:

* **Caratteristiche migliorate per la selezione di Server dell'interfaccia utente**. In questa versione semplifica l'uso del toolkit di sistemi operativi non Windows.
* **Supporto per i progetti Maven**. In questa versione supporta Maven progetti come applicazioni, che può distribuire il toolkit di Azure e configurare applicazioni approfondimenti.
* **Versione 2.6 di Azure SDK**. Questo aggiornamento più recente di Azure SDK è il nuovo prerequisito per il Toolkit installato in Windows. Si noti che non è necessario nei sistemi operativi non Windows.
* **Aggiornamento di distribuzione anziché ripubblicare**. Se si sta pubblicando un progetto di distribuzione quando la versione precedente è già attiva, il toolkit ora utilizza la funzionalità di aggiornamento distribuzione di Azure anziché arresto distribuzione precedente e ripubblicare da zero, come in passato. In questo modo il servizio cloud per l'esecuzione senza interruzioni ogni volta che è possibile, consentendo disponibilità elevata anche durante un aggiornamento ed accelera il processo di pubblicazione nuovamente.
* **Supporto per la più recente v8 Zulu OpenJDK - aggiornamento 40**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].

### <a name="march-9-2015"></a>9 marzo 2015

Il Toolkit di Azure per Eclisse - versione di marzo 2015 include i miglioramenti seguenti:

* **Supporto per Mac, Ubuntu e altre caratteristiche di Linux**. In questa versione del Toolkit di Azure per Eclisse aggiunge il supporto per Mac OS e piattaforme Unix diverse, in modo che gli sviluppatori possono installare il toolkit per creare, configurare e pubblicare i progetti di linguaggio ai servizi Cloud Azure (PaaS) da Eclisse sui sistemi operativi ad eccezione di Windows.

>[AZURE.NOTE] Questa funzionalità è in anteprima e non è consigliabile per l'utilizzo in ambienti di produzione. Non è disponibile alcun supporto clienti livello contratto di servizio, ma sono aumentare e incoraggiare tutti i commenti.

* **Plug-in di nuovi approfondimenti applicazione**. Gli sviluppatori siano in grado di configurare telemetria automatico server con informazioni dettagliate sui applicazione in Azure.
* **Automazione di distribuzione in base ant riga di comando**. Questa caratteristica consente agli sviluppatori di automatizzare la pubblicazione di versioni più recenti delle loro distribuzioni utilizzando Ant all'esterno di Eclisse. Uno script pre-generato viene configurato automaticamente per un progetto dopo la prima volta che viene distribuito da un Eclisse e distribuzioni successive è possono utilizzare lo script per automatizzare completamente distribuzioni tramite solo la riga di comando.
* **Disponibilità tomcat e alla banchina in Azure per la distribuzione più veloce e più semplice**. Gli sviluppatori ora possono fare riferimento a diverse versioni Tomcat e alla banchina sono disponibili in Azure direttamente invece di dover caricare un server Java per il proprio account (o tramite il Toolkit), pertanto non è necessario per caricare un server di linguaggio per gli scenari rapidi, Guida introduttiva.
* **Metodo di scelta rapida per pubblicazione Java web apps ai servizi cloud Azure**. Per ridurre la curva di apprendimento per gli scenari di sviluppo e test semplice, gli sviluppatori possono pubblicare applicazioni Java più direttamente in Azure. Invece di dover passare attraverso il processo di creazione e configurazione di un progetto di distribuzione di Azure, applicazioni verranno distribuite con un'istanza predefinita di Tomcat v8 e Zulu JVM (OpenJDK).

### <a name="january-30-2015"></a>30 ° gennaio 2015

Il Toolkit di Azure per Eclisse - versione di gennaio 2015 include i miglioramenti seguenti:

* **Supporto per IBM® WebSphere® applicazione Server libertà Core**. In questa versione aggiunge all'elenco dei server di applicazioni supportate da cui è possibile distribuire in Azure toolkit di IBM WebSphere applicazione Server libertà Core. Quest'ultima aggiunta espande l'elenco attuale dei server delle applicazioni supportate &quot;della casella&quot; dal Toolkit, che già incluso diverse versioni di Tomcat, alla banchina, JBoss e GlassFish.
* **L'inserimento di informazioni approfondite applicazione SDK**. Questa raccolta API client appena rilasciato (v0.9.0) diventa parte del pacchetto per le raccolte di Azure in linguaggio.
* **Aggiornamento del pacchetto per le raccolte di Azure per Java**. Questo aggiornamento include le raccolte di Azure per Java v0.7.0 e v2.0.0 API del Client di spazio di archiviazione, nonché v0.9.0 di applicazione approfondimenti SDK appena rilasciato.

### <a name="november-12-2014"></a>12 novembre 2014

Il Toolkit di Azure per Eclisse - versione di novembre 2014 include i miglioramenti seguenti:

* **Supporto per Azure SDK 2,5**. Questo aggiornamento più recente di Azure SDK è il nuovo prerequisito per il Toolkit.
* **Supporto per la versione aggiornata del v 1.8 Zulu OpenJDK, v 1.7 e v 1.6 pacchetti**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Supporto per le nuove dimensioni D Standard per i servizi cloud**, quali offerta viene aumentata prestazioni e risorse di memoria aggiuntiva. Per ulteriori informazioni, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure].

### <a name="october-17-2014"></a>17 ottobre 2014

Il Toolkit di Azure per Eclisse - versione di ottobre 2014 include i miglioramenti seguenti:

* **Miglioramenti delle prestazioni in pubblica in scenari basati su Cloud**. Caricamento di informazioni sull'abbonamento è molto più veloce quando gli utenti hanno più abbonamenti e gli account di archiviazione.
* **Supporto per la versione aggiornata del pacchetto v 1.8 Zulu OpenJDK**. Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Supporto per sostituire le versioni precedenti di 3 ° JDKs di terze parti**. Non è più pacchetti JDK obsoleti verranno visualizzato nel menu a discesa per i nuovi progetti di distribuzione. Riferimenti pacchetti JDK obsoleti continueranno a essere autorizzati a fare in modo che per il tempo viene, ma è consigliabile eseguire l'aggiornamento di tali progetti per si basano sull'ultima progetti esistenti.
* **Versione aggiornata del pacchetto per le raccolte di Azure in linguaggio API libreria**. Per ulteriori informazioni, vedere l' [API del Client di Microsoft Azure].
* **Correzioni di bug.** In questa versione contiene un numero varie correzioni di bug che sono state in base a report utente e verifica.

### <a name="august-5-2014"></a>5 agosto 2014

Il Toolkit di Azure per Eclisse - versione di agosto 2014 include i miglioramenti seguenti

* **Supporto per Azure SDK 2,4.** Le versioni precedenti di Toolkit Eclisse non funziona con questo SDK recenti.
* **Versione aggiornata del v 1.6 Zulu OpenJDK 1.7 e v 1.8 pacchetti.** Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Versione aggiornata del pacchetto per le raccolte di Azure per Java API libreria.** Per ulteriori informazioni, vedere l' [API del Client di Microsoft Azure].
* **Supporto per più tardi pubblicare il formato di File di impostazioni.** È stato aggiunto il supporto per la versione 2.0 del formato di file di impostazioni di pubblicazione.
* **Modifiche all'architettura dietro pubblica alla funzionalità Cloud.** Il Toolkit viene utilizzato l'API del Client di recenti Microsoft Azure per Java per il supporto di pubblicare nel cloud.
* **Correzioni di bug.** In questa versione contiene un numero di correzioni di bug richiesto dall'utente.

### <a name="june-12-2014"></a>12 giugno 2014

Il Toolkit di Azure per Eclisse - versione giugno 2014 è un aggiornamento di manutenzione secondario che fornisce i miglioramenti seguenti:

* **Supporto per il pacchetto di OpenJDK Zulu v 1.8.** Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Versioni aggiornate dei 1,7 pacchetti e il v 1.6 Zulu OpenJDK.** Per ulteriori informazioni, vedere la [pagina web di sistemi di Azul per OpenJDK Zulu].
* **Versione aggiornata del pacchetto per le raccolte di Azure per Java API libreria.** Per ulteriori informazioni, vedere l' [API del Client di Microsoft Azure].
* **Correzioni di bug.** In questa versione contiene un numero di correzioni di bug richiesto dall'utente.

### <a name="april-4-2014"></a>4 aprile 2014

È stato rilasciato il plug-in Azure per Eclisse - versione di aprile 2014. Questo è un aggiornamento alla versione 2.3 di SDK Azure, un prerequisito che verranno scaricate automaticamente quando si installa il plug-in di accompagnamento. Questo aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti di commenti e suggerimenti basati su usabilità dall'anteprima di febbraio 2014:

* **Supporto per la versione di Azure SDK 2.3.** Il plug-in Azure per Eclisse - versione di aprile 2014 richiede Azure SDK 2.3. Quando si utilizza il plug-in di nuovo, se non si dispone già di Azure SDK 2.3, verrà richiesto di consentire l'installazione. Non utilizzare Azure SDK 2.3 con versioni precedenti del plug-in.
* **Aggiornamento delle applicazioni senza distribuzione del pacchetto completata.** Quando si distribuisce applicazioni Java che fanno parte di un progetto, il plug-in ora automaticamente li carica in un account di archiviazione selezionato in modo che sia possibile aggiornarla e Cestino istanze del ruolo per distribuire i bit più recente di applicazione senza dover ricreare e ridistribuire l'intero pacchetto.
* **Tomcat 8 è ora un server di applicazione riconosciuto.** Se si seleziona una directory di installazione di 8 Tomcat nel computer in uso nella scheda **Server** della finestra di dialogo **Progetto di distribuzione di Azure** , il plug-in verrà ora automaticamente lo rileva e sarà possibile distribuire Tomcat 8 in modo automatico, simile a versioni precedenti di Tomcat già nell'elenco.
* **Azul Zulu OpenJDK pacchetto aggiornamenti: aggiornamento v 1.7 51 e v 1.6 aggiornare 47.** Efficace con questa versione, aggiornamento del pacchetto del sistema Azul JDK Apri Zulu v7 51 è disponibile. Inoltre, pacchetti v6 JDK Apri Zulu avviare disponibili, a partire da aggiornamento 47. Questi aggiornamenti sono oltre il pacchetto di v7 Zulu JDK aperto in precedenza disponibile aggiornamento 45, un aggiornamento 40 e aggiornare 25.
* **Supporto per dimensioni A8 e A9 Microsoft Azure Virtual Machine.** È ora possibile distribuire un servizio cloud per la memoria alta A8 e le dimensioni di macchina virtuale A9. Per ulteriori informazioni su questi formati macchine Virtuali, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure].
* **Reindirizzamento automatico da HTTP a HTTPS per i ruoli di attivazione SSL.** Quando il servizio cloud contiene solo HTTPS ruoli, se la richiesta utente specifica HTTP, verrà automaticamente reindirizzata a HTTPS. Non è necessario per creare un ruolo separato per gestire le richieste HTTP.
* **Esprimere emulatore utilizzato per emulazione locale.** Azure Express emulatore a questo punto viene utilizzato come emulatore quando il debug delle applicazioni in locale.
* **Azure è stato rinominato come Microsoft Azure.** Schermate dell'interfaccia utente riflettono che Azure è stato rinominato e non è più chiamata Azure.

### <a name="february-6-2014"></a>6 febbraio 2014

Il plug-in Azure per Eclisse - febbraio 2014 rilasciata anteprima. Questo aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti di commenti e suggerimenti basati su usabilità dall'anteprima di ottobre 2013:

* **Supporto per scaricare SSL.** Scaricando sicura Sockets Layer (SSL) è stato aggiunto come una caratteristica che consente di attivare facilmente il supporto di Hypertext Transfer Protocol Secure (HTTPS) nella distribuzione linguaggio su Azure, senza che sia necessario configurare SSL nel server applicazione Java. Questa operazione è particolarmente importante affinità sessione e/o autenticato scenari di comunicazione. Ad esempio, quando si utilizza il filtro servizio controllo di accesso (ACS), che è già supportato dal toolkit. Per ulteriori informazioni, vedere [SSL scaricando] e su [come usare SSL scaricando].
* **GlassFish 4 è ora un server di applicazione riconosciuto.** Se si seleziona una directory di installazione GlassFish 4 nel computer in uso nella scheda **Server** della finestra di dialogo **Progetto di distribuzione di Azure** , il plug-in verrà ora automaticamente lo rileva e sarà possibile distribuire GlassFish estensioni 4 in modo automatico, simile alla versione 3 estensioni GlassFish già incluso nell'elenco.
* **Aggiornamento del pacchetto Azul Zulu OpenJDK 45.** Efficace con questa versione, aggiornamento Zulu (pacchetto v7 JDK aperto) del sistema Azul 45 è ora disponibile. Questo è oltre l'aggiornamento in precedenza disponibile 40 e aggiornamento 25.
* **Il supporto per 'auto' per l'impostazione privato porte di endpoint.** È possibile impostare una porta privata su automatico per input endpoint ed endpoint interno per consentire alle Azure assegna automaticamente una porta a tale endpoint. In precedenza è possibile assegnare un numero di porta specifico.
* **Supporto per la personalizzazione il nome del certificato (CN) per la creazione di un certificato autofirmato dell'interfaccia utente.** In precedenza, lo stesso nome hardcoded è stato usato per tutti i nuovi certificati; a questo punto è possibile specificare il proprio nome di certificato per distinguere tra più certificati nel portale di Azure utilizzati per diversi scopi.
* **Azure sulla barra degli strumenti:** Barra degli strumenti di Azure è un aggiornato con le modifiche seguenti: 
    * ![][ic710876]Questa icona è stato aggiunto per il **Nuovo progetto di distribuzione di Azure**.
    * ![][ic710877]Questa icona è stato aggiunto come un collegamento alla finestra di dialogo di creazione di un certificato autofirmato.
* **Supporto per le dimensioni del computer virtuale di Azure A5.** È ora possibile distribuire un servizio cloud in memoria alta le dimensioni del computer virtuale A5. Per ulteriori informazioni su questa dimensione memoria virtuale, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure].
* **Supporto per Microsoft Windows Server 2012 R2.** È ora possibile selezionare Windows Server 2012 R2 del sistema operativo cloud.

### <a name="october-22-2013"></a>22 ottobre 2013

Il plug-in Azure per Eclisse - ottobre 2013 Preview rilasciata. Questo aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti di commenti e suggerimenti basati su usabilità dall'anteprima di settembre 2013:

* **Supporto per la versione di Azure SDK 2.2.** Il plug-in Azure per Eclisse - ottobre 2013 Preview supporta Azure SDK 2.2. Il plug-in siano ancora compatibili con Azure SDK 2.1 e Azure SDK 2.2 verrà installato automaticamente se non si dispone già di almeno Azure SDK 2.1 installato.
* **Aggiornamento del pacchetto Azul Zulu OpenJDK 40.** Come annunciato per settembre 2013 visualizzare in anteprima, consente ora plug-in utilizzando un JDK forniti terze parti direttamente su Azure, senza che sia necessario caricare il proprio JDK. Nella versione ottobre 2013, è ora disponibile; aggiornamento Zulu (pacchetto v7 JDK aperto) del sistema Azul 40 si tratta oltre l'aggiornamento originariamente pubblicato 25.
* **Collegamento di distribuzione cloud nel Log delle attività.** All'interno del Registro di attività di Azure, quando la distribuzione, è stato **pubblicato**, è possibile fare clic **Published** poiché ora è un collegamento alla distribuzione; la distribuzione verrà quindi aperto nel browser. (Lo stato **pubblicato** era precedentemente etichettato **in esecuzione**.)
* **Selezione del sistema operativo di destinazione disponibile al momento della pubblicazione.** Finestra di dialogo **Pubblica su Azure** contiene un nuovo campo **OS di destinazione**, che offre un modo più facilmente individuabile consente di impostare il sistema operativo di destinazione.
* **Sovrascrivere automatico distribuzione precedente.** Finestra di dialogo **Pubblica su Azure** contiene una nuova casella di controllo **Sovrascrivi distribuzione precedente**. Se questa opzione è selezionata, quando viene pubblicata della nuova distribuzione sovrascriverà automaticamente la distribuzione precedente. non si vuole &quot;409 conflitto&quot; problemi durante la pubblicazione nella stessa posizione senza prima annullamento della pubblicazione di distribuzione precedente.
* **Alla banchina 9 è ora un server di applicazione riconosciuto.** Se si seleziona una directory di installazione alla banchina 9 nel computer in uso nella scheda **Server** della finestra di dialogo **Progetto di distribuzione di Azure** , il plug-in verrà ora automaticamente lo rileva e sarà possibile distribuire 9 alla banchina in modo automatico, simile alle versioni precedenti di alla banchina già incluso nell'elenco.
* **Aggiungere un ruolo dal menu di scelta rapida di progetto.** Menu di scelta rapida **Azure** project contiene ora una nuova voce di menu **Aggiungi ruolo**, che offre un rapido e altre individuabile consentono di aggiungere un nuovo ruolo al progetto Azure.
* **Un aggiornamento per il pacchetto per le raccolte di Azure per una raccolta di linguaggio.** Ciò dipende dalla versione 0.4.6 dell' [API del Client di Microsoft Azure].

### <a name="september-25-2013"></a>25 settembre 2013

Il plug-in Azure per Eclisse - settembre 2013 Preview rilasciata. Questo aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti di commenti e suggerimenti basati su usabilità dall'anteprima di agosto 2013:

* **Possibilità di distribuire il pacchetto di Azul Zulu OpenJDK disponibile in Azure.** Quando si specificano JDK da utilizzare con la distribuzione di Azure, è stata aggiunta una nuova opzione. Utilizzare questa opzione, è possibile distribuire un pacchetto di terze parti JDK direttamente nel cloud Azure, senza dover caricare il proprio. Sistemi Azul fornisce il primo ad esempio creare un pacchetto chiamato Zulu, in base a OpenJDK, che può essere distribuito ora questa opzione.
* **Un aggiornamento per il pacchetto per le raccolte di Azure per una raccolta di linguaggio.** Ciò dipende dalla versione 0.4.5 dell' [API del Client di Microsoft Azure].

### <a name="august-1-2013"></a>1 agosto 2013

Il plug-in Azure per Eclisse - agosto 2013 Preview rilasciata. Questo è un aggiornamento del rilascio di Azure SDK 2.1, un prerequisito che verranno scaricate automaticamente quando si installa il plug-in di accompagnamento. Questo aggiornamento include nuove caratteristiche, correzioni di bug e alcuni miglioramenti di usabilità basate sui commenti e suggerimenti dall'anteprima di luglio 2013:

* **Rimozione delle opzioni per includere il JDK locale e il server di applicazione locale come parte del pacchetto di distribuzione.** Download JDK e application server dallo spazio di archiviazione cloud durante la distribuzione è preferibile incorporare questi componenti nel pacchetto, dopo il download i risultati di elementi in dimensioni pacchetto di distribuzione, velocizzare i tempi di distribuzione e manutenzione più semplice. Di conseguenza, le opzioni per includere JDK e server applicazioni nel pacchetto di distribuzione sono state rimosse. Progetti esistenti che sono stati configurati per includere il JDK locale e il server di applicazione locale come parte del pacchetto di distribuzione verrà convertite per automatico-carica JDK e applicazione server allo spazio di archiviazione cloud.
* **Supporto per la versione di Azure SDK 2.1.** Il plug-in Azure per Eclisse - agosto 2013 Preview richiede Azure SDK 2.1. Non utilizzare l'anteprima di agosto 2013 con versioni precedenti di Azure SDK e non Azure SDK 2.1 con versioni precedenti di plug-in di Azure per Eclisse.
* **Supporto per la versione Eclisse Kepler.** Correlata all'oggetto, la nuova versione minima necessaria Eclipse IDE è Indigo. Il plug-in Azure per Eclisse è non è più sottoposte in Helios.

### <a name="july-3-2013"></a>3 ° luglio 2013

Il plug-in Azure per Eclisse - luglio 2013 Preview rilasciata. Questo aggiornamento include nuove funzionalità, correzioni di bug e alcuni miglioramenti di commenti e suggerimenti basati su usabilità dall'anteprima di maggio 2013:

* **Possibilità di creare un nuovo account di archiviazione.** È stato aggiunto un pulsante di **Nuovo** alla finestra di dialogo **Aggiungi Account lo spazio di archiviazione** . In questo modo è possibile creare un account di archiviazione all'interno del plug-in Eclisse, senza richiedere agli utenti di accedere al portale di gestione di Azure. (È necessario avere già un abbonamento Azure per utilizzare questa funzionalità.) Per ulteriori informazioni sulla creazione di un nuovo account di archiviazione, vedere [creare un nuovo account di archiviazione].
* **Nuova &quot;(automatico)&quot; opzione per l'account di archiviazione usati per la distribuzione automatica di JDK e server e per la memorizzazione nella cache.** Quando si usa l'opzione **Carica automaticamente** per JDK e server applicazioni, è ora possibile specificare **(automatico)** per l'account di URL e lo spazio di archiviazione da utilizzare durante il caricamento di JDK e applicazione server, o quando si utilizza la memorizzazione nella cache di Azure. Quindi, queste caratteristiche utilizzerà automaticamente lo stesso account di archiviazione di quello che si seleziona nella finestra di dialogo **Pubblica su Azure** . Esercitazione [creazione di un'applicazione Hello World di Azure in Eclisse] è stato aggiornato per utilizzare la nuova opzione **(automatico)** .
* **Possibilità di impostare l'endpoint del servizio di Azure.** Specificare gli endpoint del servizio che determinano che se l'applicazione è distribuito in e gestito dalla piattaforma Azure globale, Azure gestito da 21Vianet in Cina o private piattaforma Azure. Per ulteriori informazioni, vedere [Gli endpoint del servizio di Azure].
* **Distribuzioni di grandi dimensioni è possono specificare una risorsa di archiviazione locale.** Nel caso in cui la distribuzione è troppo grande per essere contenuti nella cartella approot predefinita, è ora possibile specificare una risorsa di archiviazione locale come destinazione della distribuzione per il JDK e applicazione server. Per ulteriori informazioni, vedere [Distribuzione distribuzioni di grandi dimensioni].
* **Supporto per le dimensioni A6 e A7 Azure Virtual Machine.** È ora possibile distribuire un servizio cloud per la memoria alta A6 e A7 Virtual Machine dimensioni. Per ulteriori informazioni su questi formati, vedere [macchina virtuale e le dimensioni di servizio Cloud per Azure].
* **Un aggiornamento per il pacchetto per le raccolte di Azure per una raccolta di linguaggio.** Ciò dipende dalla versione 0.4.4 dell' [API del Client di Microsoft Azure].

### <a name="may-1-2013"></a>1 maggio 2013

Il plug-in Azure per Eclisse - potrebbe 2013 Preview è stato rilasciato. Questo è un aggiornamento principale accompagnamento del rilascio di Azure SDK 2.0, un prerequisito che verranno scaricate automaticamente quando si installa il plug-in. In questa versione include nuove caratteristiche, correzioni di bug e alcuni miglioramenti di usabilità basate sui commenti e suggerimenti dall'anteprima di febbraio 2013:

* **Caricamento automatico di distribuzione da un archivio Azure, JDK e server di applicazione.** Una nuova opzione che consente di caricare il JDK selezionata e il server di applicazione, se necessario, un account di archiviazione di Azure specificato automaticamente e lo distribuisce questi componenti da tale posizione, anziché l'incorporamento nel pacchetto di distribuzione o che hanno quindi manualmente il caricamento di utente. Questa caratteristica più comunemente richiesta può migliorare la facilità di distribuzione di componenti JDK e server, in particolare per utenti principianti. Per una panoramica che utilizza queste opzioni, vedere [creazione di un'applicazione Hello World di Azure in Eclisse].
* **Centralizzata account lo spazio di archiviazione rilevamento e la possibilità di riferimento gli account di archiviazione più facilmente (tramite un controllo elenco a discesa).** Si applica a più funzionalità che si basano su archiviazione, ad esempio JDK e distribuzione di componenti server e la memorizzazione nella cache. Per ulteriori informazioni, vedere [Elenco di Account Azure lo spazio di archiviazione].
* **Impostazione di accesso remoto semplificata nella pubblicazione guidata Cloud.** È sufficiente è digitare un nome utente e password per abilitare l'accesso remoto o lasciare vuoto per mantenere l'accesso remoto disattivato.
* **Un aggiornamento per il pacchetto per le raccolte di Azure per una raccolta di linguaggio.** Ciò dipende dalla versione 0.4.2 dell' [API del Client di Microsoft Azure].
* **Supporto per le sessioni permanenti su Windows Server 2012.** In precedenza, sessioni permanenti ha esito positivo solo in Windows Server 2008 R2 ora entrambe cloud sistema operativo destinazioni supporto sessione affinità.
* **Miglioramenti delle prestazioni di caricamento del pacchetto.** Anche quando JDK e server applicazioni incorporati nel pacchetto di distribuzione, la parte di caricamento del processo di distribuzione può essere circa due volte più veloce rispetto alle versioni precedenti.

### <a name="february-8-2013"></a>8 febbraio 2013

Il plug-in Azure per Eclisse - febbraio 2013 Preview rilasciata. Si tratta di un aggiornamento secondario che include correzioni di bug, commenti e suggerimenti basati su funzionalità e alcune nuove caratteristiche poiché novembre 2012 visualizzare in anteprima:

* Supporto per la distribuzione di JDKs, server di applicazioni e non autorizzato altri componenti da Azure pubblico o privato blob download dello spazio di archiviazione anziché inclusi nel pacchetto di distribuzione quando si distribuisce nel cloud.
* Possibilità di modificare l'ordine in cui vengono elaborati definite dall'utente componenti di un ruolo mediante l'aggiunta di pulsanti **Sposta su** e **Sposta giù** nella sezione **componenti** della **Proprietà ruolo Azure**.
* Un aggiornamento per la raccolta di **pacchetto per le raccolte di Azure per Java** , in base a versione 0.4.0 dell' [API del Client di Microsoft Azure].

### <a name="november-5-2012"></a>5 novembre 2012

Il plug-in Azure per Eclisse - novembre 2012 rilasciata anteprima. Questo è un aggiornamento principale che include un numero di nuove funzionalità, nonché altre correzioni di bug e miglioramenti basate sui commenti e suggerimenti facilità di utilizzo poiché 2012 settembre visualizzare in anteprima:

* Supporto per Microsoft Windows Server 2012 del sistema operativo cloud.
* Supporto per Azure collocato supporto di memorizzazione nella cache per i client memcached.
* Inclusione di librerie Apache Qpid JMS client per sfruttare i vantaggi della messaggistica basata su Azure AMQP.
* Una procedura guidata **Nuovo progetto** migliorata, con una nuova pagina alla fine che consente agli utenti la possibilità di abilitare rapidamente diverse caratteristiche chiave comuni nel proprio progetto: sessioni permanenti, memorizzazione nella cache e il debug remoto.
* Riduzione automatica delle istanze di ruolo su 1 durante l'esecuzione nell'emulatore di calcolo, per evitare i conflitti di associazione porta tra le istanze di server.

### <a name="september-28-2012"></a>28 settembre 2012

Il plug-in Azure per Eclisse - settembre 2012 rilasciata anteprima. Questo aggiornamento del servizio include un numero di altre correzioni di bug dall'anteprima di agosto 2012, oltre al tipo alcuni miglioramenti di usabilità basate sui commenti e suggerimenti in termini di caratteristiche esistenti:

* Supporto per Microsoft Windows 8 e Microsoft Windows Server 2012 del sistema operativo sviluppo, la risoluzione dei problemi che in precedenza impedito il plug-in non funziona correttamente in questi sistemi operativi.
* Supporto migliorato per specificare gli intervalli di porta endpoint.
* Correzioni di bug correlate a percorsi dei file con spazi.
* Ruolo contesto menu miglioramenti per un accesso più rapido alle impostazioni di configurazione specifiche del ruolo.
* Miglioramenti secondarie nella creazione guidata **pubblicazione nel cloud** e un numero di altre correzioni di bug.

### <a name="august-28-2012"></a>28 agosto 2012

Il plug-in Azure per Eclisse - agosto 2012 rilasciata anteprima. L'aggiornamento del servizio include altre correzioni di bug poiché luglio 2012 visualizzare in anteprima, oltre a numerosi miglioramenti di commenti e suggerimenti basati su facilità di utilizzo per le caratteristiche esistenti:

* Nella finestra di dialogo filtro di Azure accesso controllo servizi:
    * **Opzione per incorporare il certificato di firma** nel file WAR dell'applicazione, per semplificare la distribuzione cloud.
    * **Opzione per creare un certificato autofirmato** all'interno del filtro ACS dell'interfaccia utente. Per ulteriori informazioni sul filtro di servizi di controllo accesso Azure, vedere [come eseguire l'autenticazione Web gli utenti con Azure Access controllo servizio utilizzando Eclisse].
* All'interno della creazione guidata progetto di distribuzione di Azure (si applica anche alla pagina delle proprietà del ruolo configurazione Server):
    * **Rilevamento automatico della posizione JDK** del computer (che è possibile ignorare se lo si desidera).
    * **Rilevamento automatico del tipo di server** quando si seleziona la directory di installazione del server dell'applicazione.

### <a name="july-15-2012"></a>15 luglio 2012

Il plug-in Azure per Eclisse - luglio 2012 anteprima che gli indirizzi di un numero di bug di priorità più alta rilevati e/o segnalati dagli utenti dopo il rilascio giugno 2012, rilasciata. Questo è solo un aggiornamento del servizio, nuove caratteristiche non sono contenuti.

### <a name="june-7-2012"></a>7 giugno 2012

Azure plug-in per Eclisse - giugno 2012 CTP ha rilasciato. Nuove caratteristiche:

* **Creazione guidata nuovo progetto di distribuzione di Azure:** Consente di selezionare i JDK server applicazioni Java e le applicazioni di linguaggio direttamente nella creazione guidata migliorata dell'interfaccia utente. Sono inclusi nell'elenco della casella configurazioni del server per scegliere da Tomcat 6, 7 Tomcat, GlassFish estensioni 3, alla banchina 7, 8 alla banchina, JBoss 6 e 7 JBoss (autonomo). Inoltre, è possibile personalizzare l'elenco delle configurazioni server. Questo miglioramento dell'interfaccia utente è un'alternativa per il trascinamento file compressi e copiare gli script di avvio, che in precedenza era l'approccio principale. Ancora questo metodo funziona correttamente, ma probabilmente essere utilizzato solo per gli scenari avanzati.
* **Pagina delle proprietà ruolo configurazione del server:** Consente di cambiare facilmente JDKs, server applicazioni Java e applicazioni associate alla distribuzione dopo aver creato il progetto. Per ulteriori informazioni, vedere [proprietà configurazione del Server].
* **&quot;Pubblicare su cloud&quot; guidata:** offre un modo semplice per distribuire il progetto in Azure direttamente da Eclisse, automazione alla precedentemente manuale Auto-revoca di recupero dei credenziali, l'accesso al portale di gestione di Azure, caricare un pacchetto e così via. Per un esempio di come distribuire direttamente il progetto in Azure, vedere [creazione di un'applicazione Hello World di Azure in Eclisse].
* **Azure sulla barra degli strumenti:** Una barra degli strumenti Azure è ora disponibile in Eclisse che contiene pulsanti che richiamano le caratteristiche seguenti:
    * ![][ic710879]**Esecuzione in Azure emulatore**: viene eseguito il progetto nell'emulatore.
    * ![][ic710880]**Reimpostare emulatore Azure**: reimpostare l'emulatore.
    * ![][ic710881]**Creare Cloud pacchetto per Azure**: consente di compilare il pacchetto per la distribuzione.
    * ![][ic710876]**Nuovo progetto di distribuzione di Azure**: crea un nuovo progetto di distribuzione di Azure.
    * ![][ic710882]**Pubblica Azure cloud**: pubblica il progetto in Azure.
    * ![][ic710883]**L'opzione Annulla pubblicazione**: Elimina la distribuzione.
    * Numero di pulsanti sulla barra degli strumenti Azure sono usati per [creare un'applicazione Hello World di Azure in Eclisse].
* **Azure raccolte per Java:** È ora disponibile come parte del pacchetto solo per le raccolte di Azure per una raccolta di linguaggio in Eclisse, l'installazione del plug-in di accompagnamento e che contiene tutte le dipendenze necessarie anche. Aggiungere un riferimento alla raccolta di un progetto di linguaggio e non è necessario scaricare nulla separatamente. Per ulteriori informazioni, vedere [installare il Toolkit di Azure per Eclisse].
* **Microsoft JDBC Driver 4.0 per SQL Server disponibili durante l'installazione del plug-in:** Durante l'installazione del plug-in di nuovo, è possibile installata la versione più recente del Driver JDBC di Microsoft per SQL Server.
* **Disponibili durante l'installazione del plug-in Filtro servizio controllo accesso azure:** Il nuovo componente, incluso in una raccolta di Eclisse il toolkit consente l'applicazione web Java diretta in modo da sfruttare l'autenticazione di Azure Access Control Service (ACS) con diversi provider di identità, ad esempio Google, Live.com e Yahoo!. Non sarà necessario scrivere logica di autenticazione se stessi, solo configurare alcune opzioni ed eseguire la maggior parte delle consente agli utenti di accedere con ACS automaticamente il filtro. È solo possibile concentrarsi su come scrivere codice che consente agli utenti l'accesso alle risorse in base alla propria identità, così come viene restituito all'applicazione dal filtro all'interno dell'oggetto richiesta. Per un'esercitazione sull'utilizzo del filtro ACS, vedere [come eseguire l'autenticazione Web gli utenti con Azure Access controllo servizio utilizzando Eclisse].
* **Il rilevamento automatico delle prerequisito Azure SDK 1.7:** Quando si crea un nuovo progetto di distribuzione di Azure, Azure SDK 1.7 verranno scaricate automaticamente se non è già installato.
* **Istanza endpoint:** Consente l'accesso all'endpoint porta diretta per la comunicazione con istanze del ruolo di bilanciamento del carico. I punti finali istanza possono essere aggiunti tramite i punti finali dell'interfaccia utente, disponibile tramite la pagina [delle proprietà i punti finali] . Ciò consente di attivare il debug remoto e JMX diagnostica per specifiche calcola le istanze in esecuzione nel cloud in scenari con multi-istanza distribuzioni. 
* **Componenti dell'interfaccia utente:** Rende più facile per gli utenti avanzati impostare dipendenze progetto tra singoli ruoli Azure nel progetto e altre risorse esterne, ad esempio progetti di applicazione Java; semplifica descrivere la logica di distribuzione. Per ulteriori informazioni, vedere [componenti proprietà].
* **Aggiornamento automatico di versioni precedenti di progetti:** Quando si apre un'area di lavoro che contiene Azure progetto creato con una versione precedente del plug-in, progetti precedenti verranno visualizzati in Eclisse come chiuso, in quanto le versioni precedenti di progetti non sono compatibili con la nuova versione. Se si tenta di aprire uno di questi progetti precedenti, verrà creato un aggiornamento guidato. Se l'utente acconsente all'aggiornamento, un nuovo progetto, con **_Upgraded** aggiunto al nome del verrà creato e aggiornato automaticamente per lavorare con la nuova versione. È possibile rinominare il nuovo progetto in base alle esigenze. Come parte dell'aggiornamento, progetto originale non verrà modificato (e resteranno chiuso).

### <a name="december-10-2011"></a>10 dicembre 2011

Azure plug-in per Eclisse - dicembre 2011 CTP ha rilasciato. Nuove caratteristiche:

* **Affinità sessione (&quot;sessioni permanenti&quot;) supporta:** Per abilitare informazioni sullo stato, a barre raggruppate applicazioni Java con solo una singola casella di controllo. Per ulteriori informazioni, vedere [Affinità sessione].
* **Già pronti avvio script di esempio:** Per i più diffusi linguaggio server (Tomcat, alla banchina, JBoss, GlassFish), che è possibile semplicemente copiare e incollare dalla directory di esempi del progetto in script di avvio.
* **Emulatore output di avvio in tempo reale:** A questo punto è possibile controllare l'esecuzione di tutti i passaggi da script di avvio in una finestra console dedicata che mostra lo stato e gli errori in uno script eseguita da Azure.
* **Java.exe automatico, disattivo monitoraggio:** Al termine del java.exe in esecuzione, utilizzando uno script già pronti, leggero automaticamente incluso nella distribuzione, che impone Cestino un ruolo.
* **App Java remote configurazione dell'interfaccia utente di debug:** Consente di attivare facilmente debugger remoto del Eclisse accedere a un'applicazione di linguaggio viene eseguita in emulatore o nel cloud Azure, pertanto è possibile scorrere e il debug del codice Java in tempo reale. Per ulteriori informazioni, vedere [Debug delle applicazioni Azure in Eclisse].
* **Interfaccia utente di configurazione delle risorse di archiviazione locale:** Così non è più necessario configurare le risorse locali modificando direttamente il codice XML. Questa caratteristica consente di accedere al percorso del file efficace della risorsa locale dopo la distribuzione tramite una variabile di ambiente che è possibile fare riferimento direttamente dallo script di avvio. Per ulteriori informazioni, vedere [le proprietà archivio locale].
* **Configurazione della variabile di ambiente dell'interfaccia utente:** Così è non è più necessario impostare variabili di ambiente mediante la modifica manuale della configurazione XML. Per ulteriori informazioni, vedere [le proprietà di variabili di ambiente].
* **Driver JDBC per SQL Azure:** Viene installato tramite il plug-in come una libreria Eclisse perfettamente integrata, l'attivazione programmazione più semplice per SQL Azure. 
* **Accesso dal menu di scelta rapida per la configurazione dei ruoli dell'interfaccia utente**: è sufficiente fare clic su cartella ruolo e fare clic su **proprietà**.
* **Icone nella cartella personalizzata Azure project e ruolo:** Per migliorare la visibilità e semplificare l'esplorazione nell'ambito dell'area di lavoro e project.

## <a name="see-also"></a>Vedere anche ##

Per ulteriori informazioni su Toolkit Azure per Java IDE, vedere i collegamenti seguenti:

- [Azure Toolkit per Eclisse]
  - [Installare il Toolkit di Azure per Eclisse]
  - [Creare un'App Web di Hello World per Azure in Eclisse]
  - *Novità di Azure Toolkit per Eclisse (in questo articolo)*
- [Azure Toolkit per IntelliJ]
  - [Installare il Toolkit di Azure per IntelliJ]
  - [Creare un'App Web di Hello World per Azure in IntelliJ]
  - [Novità di Azure Toolkit per IntelliJ]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure].

<!-- URL List -->

[Azure Toolkit per Eclisse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij.md
[Creare un'App Web di Hello World per Azure in Eclisse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Creare un'App Web di Hello World per Azure in IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Installare il Toolkit di Azure per Eclisse]: ./azure-toolkit-for-eclipse-installation.md
[Installare il Toolkit di Azure per IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novità di Azure Toolkit per IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Pagina web di sistemi di Azul per OpenJDK Zulu]: http://go.microsoft.com/fwlink/?LinkId=402457
[Endpoint del servizio di Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Elenco di Account di archiviazione Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Proprietà componenti]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Il debug di applicazioni Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Distribuzione di distribuzioni di grandi dimensioni]: http://go.microsoft.com/fwlink/?LinkID=699536
[Proprietà endpoint]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Proprietà di variabili di ambiente]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Plug-in strumenti di HDInsight per Eclisse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Modalità di autenticazione utenti Web con il servizio controllo di accesso Azure mediante Eclisse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Come usare SSL scaricando]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Proprietà archivio locale]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API Client di Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Proprietà di configurazione server]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Affinità sessione]: http://go.microsoft.com/fwlink/?LinkID=699548
[Possibilità di scaricare SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Per creare un nuovo account di archiviazione]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Macchina virtuale e le dimensioni di servizio Cloud per Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png
