# <a name="steps-to-follow-when-you-retire-or-change-the-name-of-an-acom-technical-article"></a>Procedure da seguire per ritirare o modificare il nome di un articolo tecnico ACOM

Questa guida è per le PMI che elencate come autore di un articolo che deve essere ritirata dalla sezione documentazione tecnica di azure.microsoft.com. I passaggi si applicano anche se è stato rinominato un file.

Se si è membri della community di Azure e si ritiene che un articolo deve essere ritirato per qualsiasi motivo, lasciare un commento all'interno del flusso di commento Disqus per l'articolo comunicare l'autore che si verificano problemi con l'articolo.

Gli autori di PMI è necessario eseguire alcuni passaggi per ritirare normalmente contenuto in modo che gli utenti del sito Web non dispongono di un'esperienza non valida quando si ritira il contenuto del sito. Eliminare l'articolo o modificare il nome deve essere l'ultima cosa accade!

## <a name="step-1-set-the-article-to-no-indexno-follow-and-republish-it-recommended"></a>Passaggio 1: Impostare l'articolo su no indice/non seguire e pubblicarlo (scelta consigliata)

La prima cosa da fare è ripubblicare l'articolo come no indice/non seguire poche settimane prima eliminarlo effettivamente. Si tratta di best practice "pre-lavoro" per il ritiro dei contenuti. Questa operazione rimuove l'articolo gli indici motore di ricerca in modo che altri utenti possono non è possibile trovare l'articolo nella ricerca. [Wiki interno per informazioni dettagliate, vedere.](https://microsoft.sharepoint.com/teams/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx)

## <a name="step-2-manage-inbound-links-required"></a>Passaggio 2: Gestire i collegamenti in ingresso (obbligatorio)

Determinare se sono presenti i collegamenti in ingresso non Microsoft per il contenuto. Spesso, blog, forum e altro contenuto sul web fa riferimento ad articoli. Frequentemente, è possibile collaborare con i proprietari di blog per modificare questi collegamenti ed è possibile rimuovere o aggiornare i collegamenti di post nei forum. Strumenti analitica Web possono indicare se sono disponibili i collegamenti in ingresso di traffico elevato che potrebbe essere necessario gestire in questo modo.

## <a name="step-3-remove-all-crosslinks-to-the-article-from-the-technical-content-repository-required"></a>Passaggio 3: Rimuovere collegamenti incrociati per l'articolo dal repository contenuto tecnico (obbligatorio)

Non basarsi su reindirizzamenti a prendersi cura della collegamenti incrociati da altri articoli. Aggiorna o Rimuovi croce fa riferimento all'articolo sono eliminazione o ridenominazione, inclusi negli articoli proprietà da altre persone.

1. Assicurarsi che si sta utilizzando una diramazione locale aggiornata – eseguita `git pull upstream master` (o la variante appropriata su questo comando.

2.  Esaminare la cartella di azure-contenuto-prezzo/articoli e cartella azure-contenuto-prezzo/include per uno articoli e include il collegamento per l'articolo che si desidera ritirare e rimuovere i collegamenti incrociati o sostituirle con un nuovo collegamenti incrociati di appropriato. È possibile utilizzare una ricerca e sostituzione di utilità per trovare i collegamenti incrociati se si è installata. In caso contrario, è possibile utilizzare Windows PowerShell gratuitamente! Ecco come usare PowerShell per trovare i collegamenti incrociati:

 un. Avviare Windows PowerShell.

 b. Al prompt dei comandi PowerShell, passare nella cartella pr\articles di contenuto azure:

 `cd azure-content-pr\articles`

 c. Digitare il comando, che verrà elencati tutti i file che contengono un riferimento all'articolo che si sta eliminando:

 `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name`

  Se si preferisce per inviare l'elenco di nomi di file a un file di testo (in questo caso, denominato psoutput.txt), è possibile:

  `Get-ChildItem -Recurse -Include *.md* | Select-String "<the name of the topic you are deleting>" | group path | select name | Out-File C:\Users\<your account>\psoutput.txt`

3. Aggiungere e salvare tutte le modifiche, inviarli la divisione e creare una richiesta di pull per spostare le modifiche apportate dalla divisione a diramazione master dell'archivio principale.

## <a name="step-4-update-the-fwlink-tool-required"></a>Passaggio 4: Aggiornare lo strumento di collegamento (obbligatorio)

Selezionare lo strumento di collegamento per qualsiasi fwlink che potrebbe fare riferimento all'articolo. Scegliere qualsiasi fwlink contenuto sostitutivo; Se non si sta l'alias a cui appartiene il collegamento, parteciparvi. Se i proprietari non è possibile aggiornare il collegamento, file ticket con Microsoft.com per visualizzare il collegamento modificato. Altre informazioni - [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Manage%20inbound%20links%20to%20retired%20topics.aspx).

## <a name="step-5-remove-all-crosslinks-to-the-article-from-other-pages-on-azuremicrosoftcom-and-create-a-redirect-for-the-retired-page-if-appropriate-required"></a>Passaggio 5: Rimuovere le altre pagine nel azure.microsoft.com incrociati all'articolo e creare un reindirizzamento per la pagina ritirato, se appropriato (obbligatorio)

È necessario usare la persona che gestisce e aggiornamenti della pagina documentazione per il servizio per la parte. Se non si sa che si trova tale persona, contattare il partner di contenuto del team. La persona che gestisce e aggiornamenti della pagina documento sarà necessario eseguire due operazioni:

1. In Visual Studio, esaminare **l'intera** ACOM web soluzione per il file da ritirare riferimenti incrociati. Rimuovere i riferimenti incrociati, oppure sostituirle con un riferimento incrociato aggiornato. È necessario rimuovere i collegamenti HTML, nonché le stringhe di risorse correlate per i collegamenti HTML. Altre informazioni, vedere il [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Create%20or%20edit%20a%20service%20landing%20page%20or%20left%20nav.aspx)

2. Se è presente un articolo sostitutivo, creare un reindirizzamento. Altre informazioni, vedere il [wiki interno](http://sharepoint/sites/azurecontentguidance/wiki/Pages/Remove%20published%20pages%20and%20request%20redirects.aspx).

3. Verificare le modifiche al repository.

## <a name="step-6-retire-the-article"></a>Passaggio 6: Ritirare l'articolo

Dopo aver completato la procedura precedente e tali modifiche sono attivi, è possibile eliminare l'articolo dal repository. 

**Importanti:** Quando si eliminano i file, è necessario utilizzare la `git add --all` comando.

## <a name="step-7-remove-links-from-msdn-required"></a>Passaggio 7: Rimuovere collegamenti MSDN (obbligatorio)

Verifica dello strumento di domande e risposte contenuto per i collegamenti interrotti ai argomento rinominata o ritirata e Rimuovi/Correggi i collegamenti in tutti gli argomenti MSDN interessati.

## <a name="step-8-remove-cached-pages-from-search-engines-only-if-absolutely-necessary"></a>Passaggio 8: Rimuovere le pagine memorizzate nella cache dai motori di ricerca (solo se assolutamente necessario)

Eseguire questo solo se il contenuto deve essere rimosso rapidamente a causa di problemi dei clienti legali o gravi. Per procedure consigliate da Google, le eliminazioni pagina priorità normale devono essere gestite solo dai processi motore di ricerca naturale. Passare a queste pagine web da rimuovere pagine web memorizzate nella cache dai motori di ricerca:

[Bing](https://www.bing.com/webmaster/tools/content-removal?rflid=1)
[Google](https://www.google.com/webmasters/tools/removals?pli=1)


### <a name="contributors-guide-links"></a>Collegamenti di Guida dei collaboratori

- [Articolo di panoramica](./../README.md)
- [Indice degli articoli di Guida](./contributor-guide-index.md)
