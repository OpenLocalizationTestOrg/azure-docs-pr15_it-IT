<properties
    pageTitle="Proprietà ruolo Azure"
    description="Informazioni su come utilizzare il Toolkit di Azure per Eclisse per configurare le impostazioni di ruolo Azure."
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
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# <a name="azure-role-properties"></a>Proprietà ruolo Azure #

All'interno del Toolkit di Azure per Eclisse, è possono impostare diverse impostazioni di configurazione per il proprio ruolo Azure.

## <a name="configuring-azure-role-properties"></a>Configurazione delle proprietà di ruolo Azure ##

Configurare le proprietà di ruolo Azure viene eseguita tramite le finestre di dialogo proprietà per il proprio ruolo di lavoro. Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse e selezionare il sottomenu **Azure** . (Se non è presente il ruolo di gestione di progetti Eclipse, espandere il progetto Azure in Gestione progetti).

![][ic789599]

In questo argomento sono descritte le diverse proprietà che è possibile impostare dalle finestre di dialogo **proprietà** . Si noti che molte proprietà verranno compilate automaticamente quando si crea un nuovo progetto di distribuzione di Azure.

Pagine delle proprietà seguenti sono disponibili per i ruoli di Azure.

* [Proprietà macchina virtuale](#virtual_machine_properties)
* [Proprietà memorizzazione nella cache](#caching_properties)
* [Proprietà dei certificati](#certificates_properties)
* [Proprietà componenti](#components_properties)
* [Proprietà di debug](#debugging_properties)
* [Proprietà endpoint](#endpoints_properties)
* [Proprietà di variabili di ambiente](#environment_variables_properties)
* [Il bilanciamento del carico / le proprietà della sessione affinità (detta anche "sessioni permanenti")](#session_affinity_properties)
* [Proprietà archivio locale](#local_storage_properties)
* [Proprietà di configurazione server](#server_configuration_properties)
* [SSL scaricando proprietà](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### <a name="virtual-machine-properties"></a>Proprietà macchina virtuale ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **proprietà**e si hanno la possibilità di modificare le dimensioni della macchina virtuale e anche modificare il numero di istanze, come illustrato nell'immagine seguente.

![][ic719499]

>[AZURE.NOTE] Solo Windows: quando il numero di istanze è impostato su un valore maggiore di 1 e si configura anche un server applicazioni, il toolkit consentirà solo 1 istanza del ruolo per l'esecuzione nell'emulatore, indipendentemente da questa impostazione. Questa operazione consiste nell'evitare i conflitti di associazione porta tra le istanze di server diverso (ad esempio tutti si tenta di eseguire il binding alla porta 8080) durante l'esecuzione nello stesso computer. Impostazione del conteggio variante viene mantenuto, ma entra in vigore solo quando si distribuisce nel cloud.

<a name="caching_properties"></a> 
### <a name="caching-properties"></a>Proprietà memorizzazione nella cache ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **memorizzazione nella cache**. All'interno di questa finestra di dialogo, è possibile abilitare la cache compatibile con memcache denominate, che consente di velocizzare le applicazioni web.

![][ic719483]

All'interno della pagina proprietà di **memorizzazione nella cache** , è possibile specificare le impostazioni globali per le operazioni seguenti:

* Se è abilitata la memorizzazione nella cache collocati.
* la dimensione della cache come percentuale della memoria.
* nome dell'account di archiviazione per salvare lo stato della cache quando l'applicazione viene eseguita come un servizio cloud, o nessuno se non si desidera salvare lo stato della cache. (Il nome dell'account di archiviazione non viene usato quando si esegue l'applicazione nell'emulatore di calcolo.) Se si imposta il nome dell'account di archiviazione su **(auto)** (per impostazione predefinita), la configurazione di cache utilizzerà automaticamente lo stesso account di archiviazione come quello selezionato nella finestra di dialogo **Pubblica su Azure** .

>[AZURE.NOTE] L'impostazione **(automatico)** avrà l'effetto desiderato solo se si pubblica la distribuzione utilizzando il toolkit di Eclisse pubblicazione guidata. Se invece si pubblica il file cspkg manualmente utilizzando un meccanismo esterno, ad esempio il [Portale di gestione Azure][], la distribuzione non funzionerà correttamente.

Finestra di dialogo seguente mostra le proprietà per una cache.

![][ic719501]

* **Nome:** Il nome della cache collocato.
* **Il numero di porta:** Numero di porta da utilizzare per la cache.
* **Criteri di scadenza:** Uno dei seguenti valori che specifica quando una chiave nella cache scade.
    * **Assoluto:** Quando viene raggiunta l'orario indicato da **minuti a live** scade la chiave.
    * **NeverExpires:** Il tasto non hanno una scadenza.
    * **SlidingWindow:** Il tasto scade se non è stato accedervi per la quantità di tempo specificato dall' **minuti a live**; ogni volta che è possibile accedervi, è reimpostare l'orologio di scadenza.
* **Minuti a live:** Numero massimo di minuti per un tasto memcached live soggetti a dei criteri di scadenza.
* **Disponibilità con backup replicate in istanze di ruoli diversi:** Se abilitata, consente di specificare che utilizzano disponibilità replicato backup in istanze di ruoli diversi. Si noti che almeno due istanze del ruolo devono essere valide per la distribuzione per poter utilizzare questa funzionalità.

Per aggiungere una nuova cache, fare clic sul pulsante **Aggiungi** nella pagina delle proprietà **memorizzazione nella cache** e verrà aperta una finestra di dialogo **Configura denominato Cache** . Fornire i valori per le proprietà descritte in precedenza.

Per modificare una cache denominata, selezionare la cache e fare clic sul pulsante **Modifica** nella pagina delle proprietà **memorizzazione nella cache** . Verrà aperta una finestra di dialogo che consente di modificare le proprietà della cache. Fare clic su **OK** per salvare i valori di cache.

Per eliminare una cache, selezionare la cache e fare clic sul pulsante **Rimuovi** nella pagina delle proprietà **memorizzazione nella cache** e quindi fare clic su **Sì** per confermare l'eliminazione.

Per ulteriori informazioni su come utilizzare la memorizzazione nella cache, vedere [come utilizzare Co-located memorizzazione nella cache][].

<a name="certificates_properties"></a> 
### <a name="certificates-properties"></a>Proprietà dei certificati ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **certificati**.

![][ic710964]

In questa finestra di dialogo, è possibile aggiungere o rimuovere i certificati il progetto Eclisse fanno riferimento. Si noti che i certificati riportati di seguito non vengono automaticamente memorizzati all'interno di qualsiasi keystore Java e pertanto non sono automaticamente disponibili per l'uso di un'applicazione di linguaggio. Solo registrati con Azure in modo da essere precaricati in Windows certificato archiviare in macchine virtuali in esecuzione la distribuzione e successivamente utilizzato da altri software di Windows. È al momento, l'unica funzionalità del toolkit che utilizza certificati citati in questo modo nella finestra di dialogo **certificati** [SSL scaricando][], a causa l'interazione con Internet Information Services (IIS) e applicazione di richiedere il Routing (ARR), che richiedono il certificato corretto da renderli disponibili in questo modo.

Quando si distribuisce il progetto in Azure tramite la pubblicazione guidata, verrà richiesto di scegliere i file di informazioni PFX (Personal Exchange) corrispondente a tali certificati insieme le password per caricare automaticamente al servizio di Azure, ma solo se si dispone non è stati caricati sono in precedenza.

<a name="components_properties"></a> 
### <a name="components-properties"></a>Proprietà componenti ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **componenti**. In questa finestra di dialogo avere la possibilità di aggiungere, modificare, o rimuovere componenti del proprio ruolo, nonché modificare l'ordine in cui vengono elaborate.

![][ic719502]

La funzionalità componenti consente di aggiungere dipendenze al progetto di distribuzione di Azure, ad esempio progetti di applicazione Java, file speciali e istruzioni eseguibile della riga di comando per la distribuzione.

Per ogni componente, è possibile specificare:

* Passaggio da eseguire quando si importano il componente nel progetto di distribuzione Azure durante la compilazione.
* Passaggio da eseguire quando si distribuisce il componente nel cloud Azure.

>[AZURE.NOTE] Quando si specificano i file dei componenti o righe di comando, tenere presente che la distribuzione verrà pubblicata in un computer virtuale di Windows, in modo che i passaggi personalizzati devono essere validi per un sistema operativo basato su Windows. 

Componenti presentano le proprietà seguenti:

* **Importazione:** Metodo che indica come verrà importato il componente nel progetto quando viene compilato il progetto. Questa operazione può essere uno dei seguenti valori:
    * **Copia:** Il componente viene copiato dal percorso specificato dalla proprietà **da** nella directory **approot** del ruolo.
    * **AURICOLARI:** Il componente è un archivio di enterprise Java (AURICOLARI) importato da un progetto di applicazione Enterprise nel percorso locale specificato dalla proprietà **da** . (Questa viene rilevata automaticamente dal toolkit in base alla natura del progetto in quella posizione).
    * **JAR:** Il componente è un archivio di linguaggio (VASO) e importato da un progetto di linguaggio nel percorso locale specificato dalla proprietà **da** . (Questa viene rilevata automaticamente dal toolkit in base alla natura del progetto in quella posizione).
    * **Nessuno:** Importare il componente viene eseguita alcuna azione. Questa opzione è disponibile quando il componente verrà considerato uguale a già presenti nella directory **approot** del ruolo o il componente è semplicemente un'istruzione eseguibile della riga di comando, come specificato nella proprietà **come** quando il metodo di **distribuzione** è **exec**.
    * **WAR:** Il componente è un archivio di applicazione web Java (WAR) e importato da un progetto Web dinamico nel percorso locale specificato dalla proprietà **da** . (Questa viene rilevata automaticamente dal toolkit in base alla natura del progetto in quella posizione).
    * **zip:** Il componente è un file zip e importato tramite compressione la directory o il file specificato dalla proprietà **da** .
* **Da:** Percorso di origine sul computer locale la cartella o il file che rappresenta gli elementi da importare per la distribuzione. In questa proprietà, è possono utilizzare le variabili di ambiente di Windows. Tutti i componenti importabili verranno importati nella directory **approot** del ruolo quando il progetto è compilato.
    
    Si noti che è possibile distribuire un componente da un download quando si distribuisce nel cloud (non l'emulatore calcolo). Visualizzare informazioni correlate di sotto informazioni sull'aggiunta di un componente.    
    
* **As:** Nome del file in cui il componente essere importato nella directory **approot** del ruolo, distribuito nel cloud Azure. Lasciare vuoto per mantenere il nome invariati gli è disponibile nel computer locale la proprietà. (Per i componenti eseguibili, vale a dire quelli il cui metodo di **distribuzione** è impostato su **exec**, può essere un'istruzione di riga di comando di Windows non autorizzata.)

    >[AZURE.IMPORTANT] Se si usano gli spazi per questo valore, questi verranno gestiti in modo diverso a seconda del metodo di distribuzione. Se il metodo di distribuzione è **exec**, spazi verranno interpretati come separatori di riga di comando argomento e non come parte del nome del file. Per tutti gli altri metodi di distribuzione, spazi verranno interpretati come parte del nome del file.
    
* **Distribuire:** Metodo che indica l'azione applicata al componente quando viene avviata la distribuzione. Questa operazione può essere uno dei seguenti valori:
    * **Copia:** Il componente viene copiato nel percorso di destinazione specificato dalla proprietà **a** .
    * **exec:** Il componente è un'istruzione di riga di comando di Windows eseguibile eseguita nel contesto del percorso specificato dalla proprietà **per** l'ora di che inizio della distribuzione.
    * **Nessuno:** Nessuna azione viene applicata al componente quando viene avviata la distribuzione.
    * **zip:** Il componente è decompresso il percorso di destinazione specificata dalla proprietà **a** . Questo metodo è disponibile solo quando la proprietà di **importazione** **CAP**.
* **To:** Percorso di destinazione nella macchina virtuale in cui verrà distribuito il componente. Variabili di ambiente Windows possono essere utilizzate in questa proprietà e percorsi dei file sono relativamente **approot**.
    
Per aggiungere un nuovo componente, fare clic sul pulsante **Aggiungi** nella pagina delle proprietà **componenti** e verrà aperta una finestra di dialogo **Componente ruolo Azure** . Fornire i valori per le proprietà descritte in precedenza. 

Di seguito viene illustrato un esempio per l'aggiunta di un nuovo componente WAR.

![][ic719503]

Quando si distribuisce nel cloud (non l'emulatore calcolo), se si desidera distribuire il componente da un download, verificare che **nel cloud, anziché inclusi nel pacchetto di distribuzione da** sia selezionata. Se si vuole scaricare dal proprio account Azure dello spazio di archiviazione, selezionare lo spazio di archiviazione account dall'elenco a discesa **account di archiviazione** (è possibile fare clic sul collegamento di **account** per modificare che cos'è presente nell'elenco), che parzialmente compilazione nel campo **URL** e quindi compilare la parte rimanente dell'URL. Se non si desidera utilizzare l'archiviazione di Azure, selezionare **(nessuno)** dall'elenco a discesa **account di archiviazione** e immettere l'URL per il componente nel campo **URL** . Specificare uno dei metodi seguenti:

* **Copia:** Il componente di download viene copiato nel percorso di destinazione specificato dal percorso **Di Directory** .
* **stesso:** Lo stesso metodo utilizzato per **distribuire da scaricare** per **distribuire dal pacchetto**.
* **zip:** Il componente di download è decompresso il percorso di destinazione specificato dal percorso **Di Directory** .

Per modificare un componente, selezionare il componente e fare clic sul pulsante **Modifica** nella pagina delle proprietà **componenti** . Verrà aperta una finestra di dialogo che consente di modificare le proprietà del componente. Fare clic su **OK** per salvare i valori dei componenti.

Per eliminare un componente, selezionare il componente e fare clic sul pulsante **Rimuovi** nella pagina delle proprietà **componenti** e quindi fare clic su **Sì** per confermare l'eliminazione.

Componenti vengono elaborati nell'ordine elencato. Utilizzare i pulsanti **Sposta su** e **Sposta giù** per cambiare l'ordine.

>[AZURE.NOTE] La funzionalità di configurazione server basato sui componenti anche. I componenti non rimosse o modificati senza rimuovere la configurazione del server corrispondente. Verrà richiesto sulle che quando si tenta di apportare modifiche a tali componenti.

<a name="debugging_properties"></a> 
### <a name="debugging-properties"></a>Proprietà di debug ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **debug**. All'interno di questa finestra di dialogo, si ha la possibilità di abilitare o disabilitare il debug remoto, nonché creare le configurazioni di debug, come illustrato nell'immagine seguente.

![][ic719504]

Per informazioni sul debug, vedere [Debug delle applicazioni Azure in Eclisse][].

<a name="endpoints_properties"></a> 
### <a name="endpoints-properties"></a>Proprietà endpoint ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic sui **punti finali**. In questa finestra di dialogo avere la possibilità di creare un endpoint, nonché modificare o rimuovere un endpoint, come illustrato nell'immagine seguente.

![][ic719505]

Per aggiungere un endpoint, fare clic sul pulsante **Aggiungi** nella pagina delle proprietà **endpoint** e verrà aperta una finestra di dialogo **Aggiungi Endpoint** .

![][ic710897]

Immettere un nome per il punto finale, selezionare il tipo ( **Input**, **interna**o **InstanceInput**) e specificare la porta pubblica e privata. Fare clic su **OK** per salvare i nuovi valori endpoint.

A seconda del tipo di endpoint, è possibile utilizzare gli intervalli di porta come indicato di seguito:

* Per un endpoint istanza di input, la porta pubblica può essere un intervallo di porte (ad esempio **2000 2010**) e la porta privata è un valore fisso.
* Per un endpoint interno, non viene utilizzata la porta pubblica e la porta privata può essere un intervallo o lasciati vuoti o un insieme a un asterisco per indicare che viene impostata automaticamente da Azure.
* Per gli endpoint inpui, la porta pubblica può contenere solo un valore fisso e la porta privata può essere un valore fisso o lasciati vuoti o impostati su un asterisco per indicare che viene impostata automaticamente da Azure.

Se si desidera utilizzare un numero di porta singola invece di un intervallo, lasciare la casella di testo per la fine del vuota intervallo.

Per porte impostati su automatico, se è necessario determinare la porta in realtà è utilizzata in fase di esecuzione, l'applicazione può utilizzare l'API di Runtime del servizio di Azure, descritte in [Riepilogo pacchetto com.microsoft.windowsazure.serviceruntime][].

Per informazioni sull'utilizzo come endpoint input istanza per facilitare il debug di una distribuzione di più istanze, vedere [debug di un'istanza di ruolo specifico in una distribuzione di più istanze][].

Per modificare un endpoint, selezionare l'endpoint e fare clic sul pulsante **Modifica** nella pagina delle proprietà **i punti finali** . Verrà aperta una finestra di dialogo che consente di modificare il nome dell'endpoint di tipo e porte pubbliche e private. Fare clic su **OK** per salvare i valori endpoint modificate.

Per eliminare un endpoint, selezionare l'endpoint e fare clic sul pulsante **Rimuovi** nella pagina delle proprietà **endpoint** e quindi fare clic su **Sì** per confermare l'eliminazione.

Per configurare correttamente alcune delle caratteristiche (ad esempio, memorizzazione nella cache, il debug remoto, sessione affinità o SSL scaricando) attivate dall'utente in un ruolo, il toolkit può configurare automaticamente endpoint speciale che verranno elencati insieme i punti finali definiti dall'utente. Il toolkit impedisce all'utente di modifica o eliminazione ad automaticamente generate endpoint purché è attivata la caratteristica associata.

<a name="environment_variables_properties"></a> 
### <a name="environment-variables-properties"></a>Proprietà di variabili di ambiente ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **Variabili**. In questa finestra di dialogo avere la possibilità di creare una variabile di ambiente, nonché modificare o rimuovere una variabile di ambiente, come illustrato nell'immagine seguente.

![][ic719506]

Le variabili di ambiente sono disponibili per lo script di avvio quando viene avviato il ruolo.

>[AZURE.NOTE] Quando si specificano variabili, tenere presente che la distribuzione verrà pubblicata in un computer virtuale di Windows, in modo che le variabili di ambiente devono essere valide per un sistema operativo basato su Windows.

Esempio di una variabile di ambiente sia disponibile quando viene avviato il ruolo, creare una nuova variabile di ambiente facendo clic sul pulsante **Aggiungi** . Di seguito viene illustrato una variabile di ambiente denominata **MyRoleVersion** che vengono creata e assegnata il valore **1.0**.

![][ic659268]

All'interno del codice jsp, è possibile visualizzare il valore utilizzando il `System.getenv` metodo:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Ottenere il risultato quando viene eseguita l'applicazione:

![][ic552233]

Per modificare una variabile di ambiente, selezionare la variabile di ambiente e fare clic sul pulsante **Modifica** nella pagina delle proprietà **Variabili** . Verrà aperta una finestra di dialogo che consente di modificare le proprietà di variabile di ambiente. Fare clic su **OK** per salvare l'ambiente di valori delle variabili.

Per eliminare una variabile di ambiente, selezionare la variabile di ambiente e fare clic sul pulsante **Rimuovi** nella pagina delle proprietà **Variabili** e quindi fare clic su **Sì** per confermare l'eliminazione.

Per configurare correttamente alcune delle caratteristiche (ad esempio configurazione del Server, il debug remoto o lo spazio di archiviazione locale) attivata dall'utente in un ruolo, il toolkit può configurare automaticamente le variabili di ambiente speciale che verranno elencate insieme variabili di ambiente definite dall'utente. Il toolkit impedisce all'utente di modifica o eliminazione ad automaticamente generate variabili, purché è attivata la caratteristica associata.

<a name="session_affinity_properties"></a> 
### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Il bilanciamento del carico / le proprietà della sessione affinità (detta anche "sessioni permanenti") ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **Bilanciamento del carico**. In questa finestra di dialogo avere la possibilità di abilitare o disabilitare affinità sessione, come illustrato nell'immagine seguente.

![][ic719492]

Per informazioni correlate, vedere [Affinità sessione][]. Si noti inoltre il comportamento della feature nel contesto di SSL scaricando, come descritto nel [Scaricando SSL][].

<a name="local_storage_properties"></a> 
### <a name="local-storage-properties"></a>Proprietà archivio locale ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **Archivio locale**. In questa finestra di dialogo avere la possibilità di creare, modificare o rimuovere spazio di archiviazione locale temporaneo per la macchina virtuale che esegue l'applicazione. Possono impostare valori specifici per le dimensioni dell'archivio locale, anche se il contenuto viene mantenuto quando il ruolo è inserimento Cestino, come illustrato nell'immagine seguente.

![][ic719508]

È anche possibile specificare una variabile di ambiente corrispondente all'archivio locale.

Per impostazione predefinita, tutto ciò che viene distribuito in Azure viene inserito e decompressi nella cartella **approot** dell'istanza del ruolo. Mentre più semplici distribuzioni adattare sono anche dopo decompressione, lo spazio allocato per la directory **approot** è limitata e non ben (meno di 1 GB è accettabile generale). Pertanto, per garantire che Azure assegna spazio su disco sufficiente per grandi che potrebbero non rientrano nella cartella **approot** , impostare una risorsa di archiviazione locale utilizzando la finestra di dialogo **Archivio locale** . Per un modo semplice per eseguire questa operazione, vedere [Distribuzione distribuzioni di grandi dimensioni][].

È possibile facilmente fare riferimento alla risorsa lo spazio di archiviazione da script di avvio (ad esempio, il **startup.cmd**) utilizzando la variabile di ambiente automaticamente associata dal toolkit Eclisse alla risorsa, come illustrato nella finestra di dialogo **Archivio locale** . La variabile di ambiente conterrà il percorso completo della risorsa locale che è ora configurato al momento dell'esecuzione dello script di avvio. 

Per modificare una risorsa di archiviazione locale, selezionare la risorsa archivio locale e fare clic sul pulsante **Modifica** nella pagina delle proprietà **Archivio locale** . Verrà aperta una finestra di dialogo che consente di modificare le proprietà delle risorse di archiviazione locale. Fare clic su **OK** per salvare i valori delle risorse di archiviazione locale.

Per eliminare una risorsa di archiviazione locale, selezionare la risorsa archivio locale e fare clic sul pulsante **Rimuovi** nella pagina delle proprietà **Archivio locale** e quindi fare clic su **Sì** per confermare l'eliminazione.

<a name="server_configuration_properties"></a> 
### <a name="server-configuration-properties"></a>Proprietà di configurazione server ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **Configurazione Server**. All'interno di questa finestra di dialogo hanno la possibilità di aggiungere, rimuovere e modificare il server di applicazione JDK e linguaggio utilizzato nella distribuzione, nonché aggiungere o rimuovere le applicazioni (ad esempio un file WAR, VASO o AURICOLARI) utilizzate nella distribuzione.

### <a name="jdk-configuration"></a>Configurazione di JDK ###

Questa finestra di dialogo consente di specificare il pacchetto JDK da utilizzare per la distribuzione. Se si utilizza Eclisse in Windows, è possibile specificare il pacchetto JDK da utilizzare in locale durante l'esecuzione di Azure emulatore e avere la possibilità di distribuire l'installazione locale di Azure. Nei sistemi operativi non Windows, l'impostazione di JDK emulatore non è applicabile e non è possibile distribuire JDK installato in locale, poiché non è compatibile con Windows. Tuttavia, indipendentemente dal sistema operativo in uso, può sempre scegliere tra i pacchetti JDK parte 3 ° per distribuire in Azure o puntare nella confezione JDK compatibile con Windows da un percorso di download alternativi.

Di seguito è illustrato un esempio di come è possibile specificare un JDK Windows:

![][ic780647]

Se si utilizza Eclisse in Windows, è possibile specificare un JDK da utilizzare con l'emulatore di calcolo. a tale scopo, assicurarsi che **Usa JDK da questo percorso di file per la verifica locale** sia selezionata nella sezione **emulatore distribuzione** . Specificare il percorso per il JDK; è possibile passare a diversi JDK se quello che si desidera utilizzare non è selezionata automaticamente. È anche possibile distribuire il JDK al servizio cloud Azure; a tale scopo, selezionare l'opzione di **distribuzione personale JDK locale (caricamento automatico allo spazio di archiviazione cloud)** nella sezione **distribuzione Cloud** .

Nota: Nei sistemi operativi non Windows, le impostazioni di **distribuzione emulatore** e l'opzione di **distribuzione personale JDK locale** non sono disponibili. Nell'esempio seguente illustra specificando un JDK in un computer Mac o un altro sistema operativo Windows non supportato:

![][ic789643]

Indipendentemente dal sistema operativo che siano presenti, sono disponibili due opzioni di **distribuzione Cloud** per l'origine e tipo del pacchetto JDK:

* **Distribuire un pacchetto JDK parte 3 ° disponibile su Azure** 
* **Distribuire da un download personalizzato** 

Se si usa l'opzione **Distribuisci un 3o di terze parti pacchetto JDK disponibile da Azure** :

1. Selezionare la casella di controllo denominato **Distribuisci un 3o di terze parti pacchetto JDK disponibile da Azure**.
1. Dall'elenco a discesa selezionare il pacchetto JDK parte 3 ° che è disponibile in Azure.
1. La scheda **JDK** un aspetto simile al seguente in Windows:  ![][ic780648] 
    e sarà simile al seguente in Mac OS o altri sistemi operativi Windows non è supportata: ![][ic789643]
1. Fare clic su **OK** per salvare le modifiche.
1. Quando viene richiesto di accettare il contratto di licenza dal provider di pacchetto JDK parte 3 °, leggere le condizioni di licenza. Se che si accettano le condizioni, fare clic su **Sì** per chiudere la finestra di dialogo **accetta contratto di licenza** .
    Si noti che è possibile personalizzare la logica sottostante per i quali gli elementi vengono visualizzati nell'elenco a discesa per l'opzione **Distribuisci un 3o di terze parti pacchetto JDK disponibile da Azure** . Per personalizzare gli elementi nella finestra di dialogo **JDK** , fare clic sul collegamento **Personalizza** . Questa operazione verrà chiudere la pagina delle proprietà **JDK** e aprire il file **componentsets.xml** in Eclisse, è possibile modificare in base alle esigenze. Documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml** stesso.

Se si usa l'opzione **Distribuisci JDK da download personalizzato** :

1. Creare un file ZIP di directory di installazione JDK, verificare che il nodo directory figlio della struttura ZIP e non il contenuto. Prendere nota del nome della directory, sarà necessario in seguito, quindi tenere in considerazione che questa installazione JDK verrà distribuita a una macchina virtuale di Windows.
1. Caricare il file ZIP all'account di archiviazione Azure come blob. È possibile eseguire questo strumento un esternamente disponibili per il caricamento BLOB allo spazio di archiviazione Azure. È consigliabile utilizzare un blob privato. Prendere nota dell'URL blob dei contenuti del CAP.
1. Selezionare la casella di controllo denominato **Distribuisci JDK da download personalizzato**.
    Se si vuole scaricare dal proprio account Azure dello spazio di archiviazione, selezionare lo spazio di archiviazione account dall'elenco a discesa **account di archiviazione** (è possibile fare clic sul collegamento di **account** per modificare che cos'è presente nell'elenco), che parzialmente compilazione nel campo **URL** e quindi compilare la parte rimanente dell'URL. Se non si desidera utilizzare l'archiviazione di Azure, selezionare **(nessuno)** dall'elenco a discesa **account di archiviazione** e immettere l'URL per il download JDK nel campo **URL** . Se utilizzano lo spazio di archiviazione Azure, nomi blob nell'URL devono essere minuscoli.
1. Assicurarsi che la casella di testo **JAVA_HOME** fa riferimento il nome di directory corretta. Per impostazione predefinita, farà riferimento lo stesso nome directory JDK valore che scelto per l'uso locale. Ma se la directory contenuta in ZIP ha un nome diverso (ad esempio a causa di con una versione diversa), aggiornare il nome della directory nella casella di testo **JAVA_HOME** di conseguenza, poiché questa impostazione verrà utilizzata nel cloud (non in emulatore calcolo).
1. Fare clic su **OK** per salvare le modifiche.

Questo è tutto. A questo punto, quando si creano per il cloud, si noterà le dimensioni del pacchetto sarà molto più piccola, il processo di compilazione verrà in genere meno tempo e dell'implementazione stessa quando si pubblica nel cloud opportuno dedicare meno tempo. Si noti che le opzioni di **distribuzione personale JDK locale (caricamento automatico allo spazio di archiviazione cloud)** o **distribuire un JDK da download personalizzati** sono valide solo quando l'applicazione viene distribuita nel cloud. Non hanno effetto sulle esperienze emulatore calcolo; la versione locale dei componenti comunque essere utilizzata quando si distribuisce all'emulatore di calcolo. 

### <a name="server-configuration"></a>Configurazione del server ###

Di seguito è illustrato un esempio di come è possibile specificare un server di applicazioni.

![][ic796926]

Verificare che sia selezionata la casella di controllo di **distribuire un server di questo tipo** e quindi scegliere il tipo di server applicazioni che si desidera utilizzare.

Per specificare un server da utilizzare per la distribuzione cloud, è possibile usufruire delle opzioni seguenti:

1. **Distribuire un 3o di terze parti server disponibile in Azure** - questa opzione è disponibile in particolare in dev/testare scenari in cui l'efficienza di distribuzione e la semplicità è una priorità e il server non richiede una configurazione personalizzata. O quando si desidera utilizzare uno di questi server come punto di partenza ma si includono personalizzazione server appropriato di passaggi di logica di avvio della distribuzione.
1. **Distribuzione di download personalizzati** - questa opzione è disponibile in particolare in scenari di produzione quando si dispone di un server appositamente preparato e configurato che si desidera utilizzare nel cloud.
1. **Distribuire l'installazione di server locale** - questa opzione è disponibile in particolare in se l'installazione di server locale è già configurato personalizzato per l'uso. Se si sceglie questa opzione, è anche necessario specificare il percorso del server locale nella casella di testo **percorso del server locale** riportata di seguito.

Se si usa l'opzione **Distribuisci un 3o di terze parti server disponibile in Azure** :

1. Selezionare la casella di controllo denominato **Distribuisci un 3o di terze parti server disponibile in Azure**.
1. Dal menu a discesa selezionare il software server desiderato per l'uso con la distribuzione nel cloud. Nota, se è già stato specificato un tipo di server da utilizzare in precedenza, sarà limitato a scegliere solo un cloud server nella stessa famiglia di quel tipo di server. Ma se non si è scelto un tipo di server, è possibile scegliere da uno qualsiasi dei server che sono attualmente disponibili in Azure e il tipo di server verrà automaticamente selezionato.
1. Fare clic su **OK** per salvare le modifiche.

Se si utilizza l'opzione di **distribuzione da un download personalizzato** :

1. Assicurarsi di aver già selezionato un tipo di server in base ai passaggi precedenti. In questo modo, il plug-in come distribuire il server dal download personalizzato, come deve essere della stessa famiglia come tipo di server selezionato.
1. Selezionare la casella di controllo denominato **Distribuisci da download personalizzato**.
    Se si vuole scaricare dal proprio account Azure dello spazio di archiviazione, selezionare lo spazio di archiviazione account dall'elenco a discesa **account di archiviazione** (è possibile fare clic sul collegamento di **account** per modificare che cos'è presente nell'elenco), che parzialmente compilazione nel campo **URL** e compilare la parte rimanente dell'URL per il server scarica ZIP (quando mediante archiviazione Azure, nomi blob nell'URL deve essere minuscola). Se non si desidera utilizzare l'archiviazione di Azure, selezionare **(nessuno)** dall'elenco a discesa **account di archiviazione** e immettere l'URL per il download di server ZIP nel campo **URL** . Il file ZIP contiene una sottocartella che rappresenta la directory di installazione del server dell'applicazione. Ad esempio, se si utilizza un file zip per Apache Tomcat 7.0.35, all'interno il file zip sarebbe cartella figlio che rappresenta la directory di installazione, ad esempio **7.0.35 di tomcat apache**. 
1. Specificare il valore per la variabile di ambiente home directory. Viene impostato sul valore utilizzato per il server applicazione locale, se presente, ma è possibile specificare un valore diverso se il server di applicazioni cloud è diverso dal server di applicazione locale. Tuttavia, è necessario verificare che il server di applicazioni cloud sia della stessa famiglia server tipo selezionata in precedenza.
    Se si aggiorna il codice postale server applicazione cloud in futuro, è possibile modificare manualmente l'impostazione home directory o, per averla corrispondono nuovamente l'impostazione locale (se è stata modificata troppo server applicazioni locale).
1. Fare clic su **OK** per salvare le modifiche.

È possibile personalizzare la logica sottostante per i quali gli elementi vengono visualizzati nella scheda **Server** della pagina delle proprietà **Configurazione del Server** . Questa è una funzionalità avanzata che può essere necessario se le proprie esigenze supera i valori predefiniti o se si desidera aggiungere altri server. Per personalizzare la logica, nella finestra di dialogo **Server** , fare clic sul collegamento **Personalizza** . Questa operazione verrà chiudere la pagina delle proprietà **Configurazione del Server** e aprire il file **componentsets.xml** in Eclisse, è possibile modificare in base alle esigenze per estendere il modello di configurazione del server. Documentazione per **componentsets.xml** è inclusa nel file **componentsets.xml** stesso.

Se si usa l'opzione di **distribuzione del server locale (caricamento automatico allo spazio di archiviazione cloud)** :

1. Selezionare la casella di controllo denominato **distribuire il server locale (caricamento automatico allo spazio di archiviazione cloud)**.
1. Utilizzando l'elenco a discesa **account di archiviazione** , selezionare **(automatica)**. Se si specifica **(automatico)** in questo caso, il toolkit di Eclisse utilizzerà lo stesso account di archiviazione per il server come quello selezionato per la distribuzione nella finestra di dialogo **Pubblica su Azure** .
1. Fare clic su **OK** per salvare le modifiche.

Selezionare un percorso di installazione di server nel computer in uso nella casella di testo **percorso del server locale** se una delle seguenti condizioni sono vera:

* Si desidera verificare la distribuzione nell'emulatore (solo per Windows).
* Si desidera distribuire il server installato in locale nel cloud.
* Si desidera utilizzare un download di server personalizzato della propria nel cloud, in cui maiuscole/minuscole, anche assicurarsi che sia selezionata l'opzione di **distribuzione del server locale (caricamento automatico allo spazio di archiviazione cloud)** sopra.

Se nessuna delle opzioni precedenti applicabile per la propria situazione, le impostazioni del server locale sono facoltativa.

### <a name="applications-configuration"></a>Configurazione delle applicazioni ###

Di seguito è illustrato un esempio di come è possibile specificare un'applicazione.

![][ic719512]

Fare clic su **Aggiungi** per aggiungere un'altra applicazione o **rimozione** di rimuovere un'applicazione. Ai fini dell'efficienza, se si desidera utilizzare un download per l'origine di un'applicazione per la distribuzione nel cloud, utilizzare le [proprietà di componenti](#components_properties) per specificare un URL, l'account di archiviazione e così via. 

Iniziare con la versione di aprile 2014, le applicazioni vengono caricate automaticamente nello stesso account di archiviazione (sotto il contenitore **eclipsedeploy** ) come quella selezionata per la distribuzione. La logica di avvio della distribuzione contiene un passaggio che download prima di tali applicazioni da tale account di archiviazione. Ciò significa che è possibile aggiornare le applicazioni nella distribuzione senza dover ricreare e ridistribuire l'intero pacchetto caricando manualmente le versioni più recenti dell'applicazione direttamente in account di archiviazione (tramite il portale di Azure, ad esempio), sostituire i file WAR originariamente caricati sono dal toolkit. Quindi, semplicemente avviato il riciclaggio di tutte le istanze di ruolo tramite il portale di gestione di Azure nuovamente o tramite utilità della riga di comando. (Attivazione ruolo riciclaggio direttamente all'interno del toolkit di Eclisse non è attualmente supportato.)

### <a name="notes-about-server-configuration"></a>Note sulla configurazione del server ###

Le modifiche apportate tramite la pagina delle proprietà **configurazione Server** sono estese le `<component>` elementi del file package. Xml.

Quando si usa **caricare automaticamente...** o le opzioni di **distribuzione da scaricare...** per JDK o server applicazioni e si siano creando per il cloud (non l'emulatore calcolo) e si è connessi alla rete, è possibile creare messaggi, ad esempio le operazioni seguenti nell'output della Console come il generatore di Ant verifica disponibilità del download:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Se è stata selezionata l'opzione di **distribuzione da scaricare...** , può essere visualizzato il seguente messaggio di avviso, ma la compilazione continuerà:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Questo avviso è l'unica indicazione che non è stata verificata disponibilità del download. Pertanto se per qualche motivo, una distribuzione non riesce nel cloud, verificare se si ha ricevuto l'avviso.

Se si vuole disabilitare la verifica di download (ad esempio, se si ritiene rallentamento inutilmente la compilazione), impostare il `verifydownloads` attributo `false` nel `<windowsazurepackage>` elemento di package. XML: 

`<windowsazurepackage verifydownloads="false" ...>` 

Se è selezionata l'opzione **Carica automaticamente...** , quindi nella finestra della console verranno visualizzati messaggi di compilazione segnalare l'avanzamento del processo di caricamento ogni 5 secondi, ogni volta che un caricamento è necessario.

<a name="ssl_offloading_properties"></a> 
### <a name="ssl-offloading-properties"></a>SSL scaricando proprietà ###

Aprire il menu di scelta rapida per il ruolo nel riquadro di gestione progetti del Eclisse, fare clic su **Azure**e quindi fare clic su **Scaricando SSL**. 

![][ic719481]

All'interno di questa finestra di dialogo, è possibile abilitare SSL scaricando, consentendo di facilmente attivare il supporto di HTTPS Hypertext Transfer Protocol Secure () nella distribuzione linguaggio su Azure, senza che sia necessario configurare SSL nel server applicazione Java. Per ulteriori informazioni, vedere [SSL scaricando][] e su [come usare SSL scaricando][].

## <a name="see-also"></a>Vedere anche ##

[Azure Toolkit per Eclisse][]

[Installare il Toolkit di Azure per Eclisse][]

[Creazione di un'applicazione Hello World per Azure in Eclisse][]

[Proprietà del progetto Azure][]

[Elenco di Account di archiviazione Azure][]

Per ulteriori informazioni sull'utilizzo di Azure con linguaggio, vedere il [Centro per sviluppatori di linguaggio Azure][].

<!-- URL List -->

[Centro per sviluppatori di Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portale di gestione di Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit per Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Proprietà del progetto Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Elenco di Account di archiviazione Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Riepilogo pacchetto com.microsoft.windowsazure.serviceruntime]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creazione di un'applicazione Hello World per Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Il debug di un'istanza di ruolo specifico in una distribuzione di più istanze]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Il debug di applicazioni Azure in Eclisse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Distribuzione di distribuzioni di grandi dimensioni]: http://go.microsoft.com/fwlink/?LinkID=699536
[Come utilizzare la memorizzazione nella cache collocati]: http://go.microsoft.com/fwlink/?LinkID=699542
[Come usare SSL scaricando]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installare il Toolkit di Azure per Eclisse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Affinità sessione]: http://go.microsoft.com/fwlink/?LinkID=699548
[Possibilità di scaricare SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png
