# <a name="azure-technical-documentation-contributor-guide"></a>Guida ai collaboratori di documentazione tecnica Azure

È stato trovato repository GitHub in cui si trova l'origine per la documentazione tecnica pubblicati al centro della documentazione di Azure in [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation).

Questo archivio contiene anche indicazioni per la collaborazione per la documentazione tecnica.  Per un elenco degli articoli della Guida per i collaboratori, vedere [l'indice](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).

## <a name="contribute-to-azure-documentation"></a>Fornire la documentazione Azure

Grazie per l'interesse nella documentazione Azure.

* [Metodi di collaborazione](#ways-to-contribute)
* [Codice di comportamento](#code-of-conduct)
* [Sui propri contributi al contenuto Azure](#about-your-contributions-to-azure-content)
* [Organizzazione di archivio](#repository-organization)
* [Utilizzare GitHub, fra e questo archivio](#use-github-git-and-this-repository)
* [Come usare delle vendite promozionali per formattare l'argomento](#how-to-use-markdown-to-format-your-topic)
* [Commenti e suggerimenti, commenti e supporto](./contributor-guide/feedback-and-comments.md)
* [Altre risorse](#more-resources)
* [Indice degli articoli di Guida di tutti i collaboratori](./contributor-guide/contributor-guide-index.md) (verrà visualizzata la nuova pagina)

## <a name="ways-to-contribute"></a>Metodi di collaborazione 

Può contribuire alla [documentazione Azure](http://azure.microsoft.com/documentation/) in vari modi:

* Collaborare a un [forum di discussione](http://social.msdn.microsoft.com/Forums/windowsazure/home).
* Inviare commenti Disqus nella parte inferiore di articoli.
* Può facilmente contribuire a articoli tecnici nell'interfaccia utente GitHub. Trovare l'articolo in questo repository o vedere l'articolo [http://azure.microsoft.com/documentation](http://azure.microsoft.com/documentation) e fare clic sul collegamento vedere l'articolo passa all'origine GitHub per l'articolo.
* Se si apportano modifiche significative a un articolo esistente, aggiunta o la modifica di immagini o parte di un nuovo articolo, è necessario divisione questo repository, installare fra Bash, delle vendite promozionali tastiera e informazioni su alcuni comandi operazioni.

##<a name="code-of-conduct"></a>Codice di comportamento

Il progetto ha adottato [Microsoft Apri origine codice di comportamento](https://opensource.microsoft.com/codeofconduct/). Per ulteriori informazioni, vedere il [Codice di condurre domande frequenti](https://opensource.microsoft.com/codeofconduct/faq/) o il contatto [opencode@microsoft.com](mailto:opencode@microsoft.com) con altre domande o commenti.

##<a name="about-your-contributions-to-azure-content"></a>Sui propri contributi al contenuto Azure

###<a name="minor-corrections"></a>Correzioni secondarie

Correzioni secondarie o chiarimenti che INVIO per la documentazione ed esempi di codice in questa repo rientrano [Azure sito Web condizioni per l'utilizzo (condizioni)](http://azure.microsoft.com/support/legal/website-terms-of-use/).


###<a name="larger-submissions"></a>Invii più grandi

Se si invia una richiesta di pull con le modifiche di nuove o significative per la documentazione ed esempi di codice, è necessario inviare un commento in GitHub in cui viene richiesto di inviare un contratto di licenza online contributo (USUALE) se si è in uno di questi gruppi:

* Membri del gruppo tecnologie aprire Microsoft.
* Collaboratori che non funzionano per Microsoft.

È necessario per completare il modulo in linea prima possiamo accettare la richiesta di pull.

Per maggiori informazioni, visitare [http://azure.github.io/guidelines/#cla](http://azure.github.io/guidelines/#cla).

## <a name="repository-organization"></a>Organizzazione di archivio

Il contenuto nel repository azure contenuto segue l'organizzazione della documentazione su [Azure.Microsoft.com](http://azure.microsoft.com). Questo archivio contiene due cartelle principali:

### <a name="articles"></a>\articles

La cartella *\articles* contiene gli articoli della documentazione formattati come file delle vendite promozionali con estensione *MD* .

Articoli nella directory radice sono pubblicati Azure.Microsoft.com nel percorso *http://azure.microsoft.com/documentation/articles/ {articolo-nome-senza-md} /*.

* **Articolo nomi di file:** Visualizzare [il file per la denominazione indicazioni](./contributor-guide/file-names-and-locations.md).

Gli articoli di propria cartella servizio pubblicati di Azure.Microsoft.com nel percorso *http://azure.microsoft.com/documentation/articles/service-folder/ {articolo-nome-senza-md} /*

* **Sottocartelle Media:** La cartella *\articles* cartella *\media.* per i file multimediali di articolo directory radice, all'interno che sono sottocartelle con le immagini per ogni articolo.  Le cartelle di servizio contengono una cartella di supporti separati per trovare articoli all'interno di ogni cartella del servizio. Le cartelle di immagine articolo hanno nomi identici ai file articolo, senza l'estensione di file *MD* .

### <a name="includes"></a>\Includes

È possibile creare sezioni di contenuto riutilizzabile da includere in uno o più articoli. Vedere [estensioni personalizzate utilizzate il contenuto tecnico](./contributor-guide/custom-markdown-extensions.md).

### <a name="markdown-templates"></a>modelli di \markdown

Questa cartella contiene il modello delle vendite promozionali standard con la formattazione di base delle vendite promozionali che necessarie per un articolo.

### <a name="contributor-guide"></a>\contributor-Guide

Questa cartella contiene articoli che fanno parte della Guida per i collaboratori.  

## <a name="use-github-git-and-this-repository"></a>Utilizzare GitHub, fra e questo archivio

Per informazioni su come collaborare, come usare UI GitHub di collaborazione piccole modifiche e come divisione e duplicare l'archivio per contributi più significativi, vedere [installare e configurare gli strumenti per la creazione di GitHub](./contributor-guide/tools-and-setup.md).

Se si installa GitBash e si sceglie di lavorare in locale, la procedura per creare una nuova sezione di lavoro locale, apportare le modifiche e inviare che nuovamente le modifiche in ramo principale è elencata nella [fra i comandi per creare un nuovo articolo o aggiornare un articolo esistente](./contributor-guide/git-commands-for-master.md)

### <a name="branches"></a>Diramazioni

È consigliabile creare diramazioni di lavoro locale che utilizzano un ambito specifico di modifica. Ogni ramo deve essere limitato a un singolo concetto/articolo sia per semplificare il flusso di lavoro e ridurre il rischio di conflitti di unione.  Gli sforzi seguenti sono di ambito appropriato per una nuova sezione:

* Un nuovo articolo (e immagini associate)
* Controllo ortografia e grammatica modifiche apportate a un articolo.
* Applicazione di un unico modifiche di formattazione per una vasta serie di articoli (ad esempio nuovo copyright piè di pagina).

## <a name="how-to-use-markdown-to-format-your-topic"></a>Come usare delle vendite promozionali per formattare l'argomento

Tutti gli articoli di questa repository utilizzano delle vendite promozionali flavored GitHub.  Di seguito è riportato un elenco delle risorse.

- [Nozioni di base delle vendite promozionali](https://help.github.com/articles/markdown-basics/)

- [Foglio riassuntivo dei stampabile delle vendite promozionali](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

- Per il nostro elenco degli editor delle vendite promozionali, vedere l' [argomento di installazione e gli strumenti](./contributor-guide/tools-and-setup.md#install-a-markdown-editor).

## <a name="article-metadata"></a>Metadati dell'articolo

Articolo metadati contribuiscono di alcune funzionalità sul sito web azure.microsoft.com, ad esempio attribuzione autore, attribuzione collaboratori, barra di navigazione, articolo descrizioni e SEO ottimizzazioni, nonché creazione di report Microsoft utilizza per valutare le prestazioni del contenuto. I metadati, pertanto sono importanti! [Ecco le indicazioni per accertarsi che i metadati viene eseguita a destra](./contributor-guide/article-metadata.md).

### <a name="labels"></a>Etichette

Etichette automatizzate sono assegnate in modo da estrarre richieste per la gestione del flusso di lavoro richiesta pull e consentono di comunicare qual è il problema con la richiesta di pull:

* Contratto di licenza contributo correlate
    * usuale non obbligatorio: la modifica è relativamente secondaria e non è necessario eseguire l'accesso un USUALE.
    * obbligatorio usuale: l'ambito della modifica relativamente è grande, è necessario accedere un USUALE.
    * firma usuale: collaboratore firmato USUALE, in modo che la richiesta di pull ora possibile spostare in avanti per la revisione.
* Elemento chiave etichette: etichette PnP ad esempio, App moderno e TDC suddividere in categorie le richieste di pull dall'organizzazione interna che è necessario esaminare la richiesta di pull.
* Modifica inviata per autore: l'autore ha ricevuto notifica della richiesta di pull in sospeso.

## <a name="more-resources"></a>Altre risorse

Vedere l' [indice della Guida per il nostro collaboratore](./contributor-guide/contributor-guide-index.md) per tutti gli argomenti le indicazioni.
