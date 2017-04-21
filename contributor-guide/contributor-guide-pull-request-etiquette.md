# <a name="pull-request-etiquette-and-best-practices-for-microsoft-contributors-to-azure-documentation"></a>Estrarre l'utilizzo della richiesta e le procedure consigliate per i collaboratori di Microsoft Azure documentazione

Per pubblicare le modifiche alla documentazione, si inviare richieste pull dalla divisione. Ogni richiesta pull è da rivedere prima da unire. Leggere questo articolo per comprendere come è consigliabile lavorare con i revisori richiesta pull e come è possibile creare richieste pull sono più facile e veloce per esaminare in modo che la coda di richieste pull funziona meglio per tutti gli utenti.

## <a name="working-with-pull-request-reviewers"></a>Utilizzo di revisori richiesta pull

Ecco le nozioni di base che informazioni utili sull'uso dei revisori richiesta pull. 

- <b>Comprendere il ruolo del revisore richiesta pull. Revisore:</b>
  - Garantisce la qualità di base del contenuto
  - Impedisce regressione nel repository
  - Fornisce feedback prima di unire

  Recuperare i revisori richiesta sono in un ruolo di governance contenuto. Lo scopo principale non è sufficiente unire qualsiasi elemento viene inviato il più presto possibile. Si aspettano di commenti e suggerimenti che è necessario effettuare gli aggiornamenti, in particolare per trovare articoli nuovi e modificati in modo rilevante.

- <b>Pianificare in anticipo con il revisore richiesta pull:</b>
  - Per le richieste di pull priorità alta
  - Recuperare le richieste di timeout datata versioni
  - Richieste di pull da modificano o aggiungere molti file

- <b>Contratto di servizio per recuperare richiedere revisione</b>

  Archivio privato, ogni volta che la richiesta di pull inserito nella coda di richiesta pull con l'etichetta già pronti per unire il team di prova visualizzare la richiesta di pull entro 12 ore lavorative (L-F, 8 AM a 17: 00) e inviare commenti e suggerimenti o unire se non è necessario alcun feedback. Si applica questo contratto di servizio all'atto della revisione prezzo, non unione. Verranno unite PRs quando soddisfano [i criteri per l'unione](contributor-guide-pr-criteria.md). 

## <a name="make-the-pull-request-queue-work-better-for-everyone"></a>Creare una coda di richiesta pull più idoneo per tutti gli utenti

Esistono due realtà base nella coda di prezzo:

- Recuperare le richieste che sono piccoli nell'ambito e che contengono modifiche molto simile più velocemente da rivedere. 
- Recuperare le richieste che sono grandi nell'ambito o contenenti mista diversi tipi di modifiche richiedere più tempo per esaminare.

È possibile rendere coda di richiesta di pull lavorare meglio seguendo queste procedure consigliate:

- Secondari aggiornamenti separati ad articoli esistenti da nuovi articoli o riscrittura principali. Usare queste modifiche in diverse diramazioni di lavoro. 

- Quando si elimina articoli o immagini, non combinare le eliminazioni con aggiunte contenute nuove o aggiornamenti. Gestire le modifiche/nuovi contenuti in una sezione di lavoro distinta.

- Per le versioni o refactoring del contenuto, pianificare in anticipo con il revisore prezzo. Potrebbe essere necessario la propria assistenza per creare una diramazione della versione o per coordinare la stampa unione volte con i tempi di pubblicazione in modo che il contenuto da pubblicare nel momento giusto.

- Se si sta tentando di coordinare gli aggiornamenti effettuati nel repo ACOM (Internet Explorer, le modifiche alla struttura di spostamento sinistro, posizionate pagine, reindirizzamenti o risorse mappe) con le modifiche apportate nel repository prezzo di contenuto azure, è necessario coordinare il lavoro in anticipo con il revisore prezzo. In caso contrario, si rischia di una quantità elevata di collegamenti interrotti.

## <a name="criteria-for-expedited-pull-requests"></a>Criteri per le richieste di pull rapida

- Contattare azdocprs per velocizzare PRs solo quando assolutamente necessario. È possibile richiedere accelerata prezzo gestione per zona rosso, informativa sulla privacy, legali e problemi di sicurezza; Per esperienza realmente interrotti; e per escalation dirigenti. 
- Contenuto per le versioni funzionalità non può essere utilizzato per la gestione rapida - contenuti del rilascio caratteristica richiede una pianificazione precedente o devono essere gestito tramite coda priorità standard.


## <a name="in-a-hurry-submit-prs-that-can-be-accepted-automatically"></a>In fretta? Inviare PRs che possono essere accettati automaticamente

Utilizzare le regole di automazione PRMerger per ottenere ulteriori le attività quotidiane PRs unite automaticamente.

PRMerger può accettare il prezzo automaticamente, se:
* Influisce su 10 file o meno.
* La presentazione contiene 15 commit o meno.
* Meno di 20% delle modifiche di testo.
* Selettori/switcher non vengono aggiornati.
* Nessun file eliminato o aggiunti.
* Nessuna immagine sono nuova, modificati o eliminati.

Se la richiesta di pull non soddisfa questi criteri, l'etichetta "Non è possibile unire PRmerger" viene assegnato automaticamente in modo che si conosce che richiede l'esecuzione di revisione da un revisore prezzo.

### <a name="need-to-make-a-lot-of-little-changes"></a>È necessario apportare numerose piccole modifiche?

Eseguire la pila dalle regole di automazione PRMerger sopra e procedere come segue:
* Inviare gli articoli con le modifiche light insieme in un file di 10 o meno.
* Creare un prezzo separata per trovare articoli in cui modificare le immagini o selettori. È necessario umana revisione.
* Creare un prezzo separata per trovare articoli nuovi o eliminati. È necessario umana revisione.

## <a name="related"></a>Correlati

- [Rivedere i criteri di qualità per la richiesta di pull](contributor-guide-pr-criteria.md)

- [Estrarre l'automazione commento richiesta](contributor-guide-pull-request-comments.md)
