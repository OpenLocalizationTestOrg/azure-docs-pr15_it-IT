# <a name="quality-criteria-for-pull-request-review"></a>Rivedere i criteri di qualità per la richiesta di pull

Questi criteri sono destinati per gli autori che creare e gestire gli articoli tecnici e per i revisori richiesta pull a rivedere le richieste di recuperare contenuto. Se la richiesta di pull non idonei per [l'unione automatica](contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically), verrà rivisto da un revisore richiesta pull umano. Effettuare il pull richiesta revisori esaminare in genere si trova nuove o modificate. Effettuare il pull richiesta revisori valutare le modifiche in una richiesta di pull in base alla revisione qualità blocco e senza blocco elementi elencati in questo articolo.

## <a name="blocking-content-quality-items"></a>Bloccare gli elementi di qualità dei contenuti

Gli aggiornamenti nella richiesta di pull devono soddisfare i criteri seguenti da unire. I revisori richiesta pull forniscono commenti nelle commenti richiesta pull per questi elementi e tipo `#hold-off` nella richiesta di pull per tornare all'utente (autore) con commenti e suggerimenti.

| Categoria | Elemento di revisione qualità |
|----------|---------------------|
|Prerequisiti| La "pronto-a-unione" e le etichette "convalida completata" vengono assegnate le PR|
|Prerequisiti| Non è possibile bloccare la richiesta di pull da un conflitto di unione.|
|Integrità Repo|    Richiesta di pull non contiene nessuna regressione contenuta più evidente.|
|Integrità Repo|    Richiesta di pull non include un repo incorporato o da qualsiasi file insoliti, estranei.|
|Integrità Repo |Richiesta di pull contiene meno di 100 file modificati, a meno che il prezzo aggiornamento intenzionalmente una diramazione della versione dallo schema. (Really, un prezzo deve contenere meno far rispetto a quelle, ma dopo 100 file modificati, GitHub non viene visualizzato il diffs).|
|Assegnare un nome |Nomi di file per i nuovi file seguano le [indicazioni per la denominazione di file](file-names-and-locations.md).|
|Assegnare un nome |Nuove cartelle introdotti Segui repo le [convenzioni di denominazione cartella](file-names-and-locations.md#folder-names-in-the-repo).|
|Contenuto    |L'articolo è un documento tecnico e pertanto nel canale contenuto corretto. Vedere la [elementi indicazioni](content-channel-guidance.md).|
|Contenuto    |Oggetto nel documento tecnico è adatto a un articolo tecnico. Vedere la [elementi indicazioni](content-channel-guidance.md).|
|Contenuto    |L'articolo contiene un paragrafo introduttivo e un corpo procedurale o concettuale del contenuto. L'articolo deve contenere sufficiente, completo contenuto autonoma come un articolo. Non può essere un frammento di piccole dimensioni delle informazioni. (Eccezione: un argomento "Limiti" Se nel contesto di un articolo di grandi dimensioni in cui sono elencati tutti i limiti di un servizio.)|
|Contenuto| Elementi che devono essere elenchi numerati sono numerati, gli elementi che devono essere non ordinati elenchi puntati. Si tratta di base facilità di utilizzo.|
|Contenuto| Insolito o nuovi elementi grafici, architettura delle informazioni o strutture o strutture evidenti non standard necessario esaminato con il revisore prezzo cliente potenziale. Team sperimentano le nuove informazioni che devono disporre di un disegno problema/soluzione o un piano per la valutazione esperimenti.|
|Funzionalità/progettazione del sito| Switcher vengono usate solo per il passaggio in più versioni dello stesso articolo.|
|Funzionalità/progettazione del sito| I titoli delle switchered articoli contengono informazioni che consentono di distinguere ogni articolo da altri articoli nel set di switchered.|
|Funzionalità/progettazione del sito| Creata manualmente sommari non sono consentiti. L'articolo deve utilizzano H2s per il sommario nella pagina.|
|Funzionalità/progettazione del sito| Se sono presente intestazioni H2, l'articolo contiene almeno due intestazioni di H2. Un titolo 2 consente di creare un articolo di voce del sommario. Prima intestazioni H3 è necessario utilizzare i titoli h2 per assicurarsi che venga creato un sommario.|
|Vendite promozionali| HTML: Contenuto di origine non contiene HTML a livello di blocco-secondaria all'interno del testo è consentito HTML, ad esempio apice, pedice, caratteri speciali e altre operazioni secondarie che non è possibile eseguire con delle vendite promozionali. Tabelle HTML sono consentite solo se la tabella contiene elenchi puntati o numerati, ma in genere è un'indicazione che il contenuto deve essere semplificata in modo che l'origine può essere codificata in vendite promozionali.|
|Vendite promozionali   |Gli elementi delle vendite promozionali personalizzati vengono utilizzati quando opportuno. Ex: Note vengono codificate mediante la AZURE. Nota estensione, non come testo normale.|
|SEO    |La "& #124; Identificatore del sito di Microsoft Azure"è necessario|
|SEO    |Il titolo H1 contiene informazioni sufficienti per descrivere il contenuto dell'articolo, per distinguerlo dagli altri articoli Azure ed eseguire il mapping a parole chiave alle probabili cliente. Ad esempio "Overview" come titolo H1 è riuscito.|
|Terminologia| L'utilizzo della acronimo ARM, V1 o V2 come riferimenti per la visualizzazione classica e modelli di distribuzione di Manager delle risorse è un elemento di terminologia blocco.|
|Immagini |Animate non vengono accettate nel repo.|
|Immagini | Immagini di risoluzione chiaro, sono esenti da errori di ortografia e non contengono privati informazioni | 
|Gestione temporanea|Anteprima articolo deve essere libera in gestione temporanea. Non può contenere eventuali problemi di formattazione più evidenti: <br><br>-Un elenco puntato o numerato che viene visualizzata come un paragrafo <br> -Codice in un blocco di codice visualizzate parzialmente nel blocco di codice e in parte fuori <br>-Passaggi numerati numerati in modo errato a causa di rientro difettoso|

## <a name="non-blocking-content-quality-items"></a>Il blocco non gli elementi di qualità del contenuto

Per questi elementi, i revisori richiesta pull forniscono commenti e suggerimenti e istruzioni per l'autore del completamento con correzioni di una richiesta di pull successiva. Tuttavia, questo feedback non blocchi la decisione da unire. Gli autori devono completamento entro 3 giorni lavorativi con correzioni.

| Categoria | Elemento di revisione qualità |
|----------|---------------------|
|Contenuto|Articoli devono avere "passaggi successivi" all'estremità con 1-3 pertinenti e accattivanti passaggi successivi. Testo breve deve essere incluso che consente di scoprire perché i passaggi successivi sono i contenuti pertinenti al cliente. (Solo articoli nuovi). Esempio: <https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-install/><br>![](media/contributor-guide-pr-criteria/nextstepsexample.PNG)|
|Contenuto|Controllo ortografia, grammatica e altri problemi di scrittura - revisori richiesta pull possono fornire commenti e suggerimenti su alcuni problemi senza blocco commenti. Se sono presenti più di alcuni problemi editoriale, i revisori accedere a una richiesta di modifica per l'articolo per la modifica di una post pubblicazione.|
|Immagini|Immagini utilizzano lo stile callout corretto e il colore e schermate lo stile di bordo e segnaposto corretto. [Vedere la Guida di immagine](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Immagini|Immagini includono testo alternativo. [Vedere la Guida di immagine](https://github.com/Azure/azure-content/blob/master/contributor-guide/create-images-markdown.md).|
|Funzionalità/progettazione del sito|Le intestazioni di H2, visualizzata nel sommario nella pagina ideale dovrebbero andare a capo per non più di 2 righe. Più titoli verificare l'articolo sommario più difficili da analizzare.|
|Convenzioni di stile|Tutti i titoli e intestazioni vengono maiuscole/minuscole frase, per lo stile di Azure.|
|Processo|Se la richiesta di pull potrebbe avere stata facilmente modificata per PRmerger automatizzate, i revisori richiesta pull inviare commenti e suggerimenti per l'autore su come utilizzare diramazioni in modo che le modifiche potrebbero essere unite automaticamente. Vedere [l'articolo di prezzo preferito](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-pull-request-etiquette.md#in-a-hurry-submit-prs-that-can-be-accepted-automatically).|
|Processo|Quando si elimina o si rinomina un articolo, assicurarsi di che seguire il processo. Estrarre i revisori sia necessario aggiungere il commento e collegamento seguente in un commento richiesta:<br><br>*Verificare che è stata seguita il processo di Guida per i collaboratori per l'eliminazione di articoli: <https://github.com/Azure/azure-content/blob/master/contributor-guide/retire-or-rename-an-article.md> .*|

## <a name="related"></a>Correlati

- [Utilizzo della richiesta di pull e le procedure consigliate per i collaboratori di Microsoft](contributor-guide-pull-request-etiquette.md)

- [Estrarre l'automazione commento richiesta](contributor-guide-pull-request-comments.md)
