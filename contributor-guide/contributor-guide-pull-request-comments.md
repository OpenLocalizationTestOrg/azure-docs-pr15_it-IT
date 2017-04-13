# <a name="pull-request-comment-automation"></a>Estrarre l'automazione commento richiesta

Serve automazione commento nei commenti richiesta pull per consentire ai collaboratori e agli autori di assegnare le etichette che determinano la richiesta di pull verifica.

| Commento | Risultato | Disponibilità|
| -------- |-------------|-------------|
|#firma digitale | Quando l'autore di un articolo tipi di commento **#sign-off** all'interno del flusso di commento, viene assegnata l'etichetta **pronto per l'unione** . | Pubblici e privati|
|#firma digitale | Se un collaboratore che non si trova tentativi autore nell'elenco per l'approvazione una richiesta di pull pubblico con il commento **#sign-off** , un messaggio viene scritto per la richiesta di pull che indica che l'etichetta può essere assegnata solo dall'autore. | Pubblico |
|#ritagliare un'esenzione | Se si digita **#hold-off** in un commento di richiesta di pull, rimuove l'etichetta di **pronto per unire** - nel caso in cui si desidera riattivarla o si commette un errore. Nel repo privato, questo Assegna etichetta **not unione** . | Pubblici e privati |
| #informazioni e di chiusura | Gli autori possono digitare il commento **#please e di chiusura** all'interno del flusso di commento di una richiesta di pull per chiuderla se si decide di non avere l'unione delle modifiche. | Pubblico |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Risoluzione dei problemi approvazioni in repo pubblica

L'accesso a repo pubblico disattivare automazione è consente solo all'autore di approvazione. Potrebbe essere necessari alcuni elaborazione manuale delle eccezioni:

- **Gli autori di articolo**: per utilizzare l'automazione di commento archivio pubblico, l'account GitHub effettivo deve corrispondere esattamente all'account GitHub elencato nella metadati dell'articolo. Uso delle maiuscole del proprio account rilevanti. Se sono bloccati dalla disconnessione a causa di questo problema, inviare messaggi all'alias azdocprs.

- **Altri dipendenti**: se si è un dipendente la conclusione per conto dell'autore e vengono bloccati dall'automazione, contattare il supporto azdocprs con il collegamento richiesta pull. Indica che ci si trova - responsabili di progetto del team del prodotto stesso, i colleghi su team per la scrittura e scrivere i responsabili del team sono considerati attendibili.



## <a name="related"></a>Correlati

- [Utilizzo della richiesta di pull e le procedure consigliate per i collaboratori di Microsoft](contributor-guide-pull-request-etiquette.md)

- [Rivedere i criteri di qualità per la richiesta di pull](contributor-guide-pr-criteria.md)
