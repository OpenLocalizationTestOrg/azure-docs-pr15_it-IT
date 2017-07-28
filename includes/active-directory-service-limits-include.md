Di seguito sono le limitazioni d'uso e altri limiti del servizio per il servizio di Azure Active Directory.

| Categoria | Limiti |
|---|---|
| Directory | Può essere associato a un massimo di 20 directory di Azure Active Directory solo un singolo utente.<br />Esempi di possibili combinazioni: <ul> <li>Un singolo utente crea 20 directory.</li><li>Un singolo utente viene aggiunto a 20 directory come un membro.</li><li>Un singolo utente crea 10 directory e versioni successive è aggiunti da altri utenti a 10 directory diverse.</li></ul> |  
| Oggetti | <ul><li>Un massimo di 500.000 oggetti può essere utilizzato in un'unica directory dagli utenti della versione gratuita di Azure Active Directory.</li><li>Un utente non amministratore può creare non più di 250 oggetti.</li></ul> |
| Estensioni dello schema | <ul><li>Estensioni di tipo stringa possono includere al massimo 256 caratteri. </li><li>Estensioni di tipo binario sono limitate a 256 byte.</li><li>100 valori dell'estensione (attraverso tutti i tipi e tutte le applicazioni) è possono scrivere ogni singolo oggetto.</li><li>Solo utente"", "Gruppo", "TenantDetail", "Dispositivo", "Applicazione" e "ServicePrincipal" entità può essere esteso con tipo "Stringa" o "Binario" tipo valore singolo attributi.</li><li>Estensioni sono disponibili solo in anteprima di 1.21 la versione dell'API del grafico. L'applicazione deve disporre di accesso di scrittura per registrare un'estensione.</li></ul> |
| Applicazioni | Un massimo di 10 utenti possa essere proprietari di una singola applicazione. |
| Gruppi | <ul><li>Un massimo di 10 utenti possa essere proprietari di un singolo gruppo.</li><li>Qualsiasi numero di oggetti può essere membri di un unico gruppo di Azure Active Directory.</li><li>Il numero di membri di un gruppo che è possibile sincronizzare da Active Directory locale Azure Active Directory è limitato ai membri di 15K con Azure Active Directory la sincronizzazione della Directory (DirSync).</li><li>Il numero di membri di un gruppo che è possibile sincronizzare da Active Directory locale Azure Active Directory utilizzando Azure AD Connect è limitato ai membri di 50K.</li></ul> |
| Pannello di accesso | <ul><li>Non esiste alcun limite al numero di applicazioni che possono essere visualizzati nel Pannello di accesso per utente finale, per gli utenti per Azure Active Directory Premium o Enterprise mobilità Suite di licenze assegnate.</li><li>Un massimo di 10 app riquadri (esempi: casella, Salesforce o Dropbox) possono essere visualizzati nel Pannello di accesso per ogni utente finale per gli utenti di licenze assegnati è gratuitamente o Azure Active Directory Basic Edition di Azure Active Directory. Questo limite non si applica agli account di amministratore.</li></ul> |
| Report | Un massimo di 1.000 righe può essere visualizzato o scaricato in tutti i report. Parte decimale verrà troncati eventuali dati aggiuntivi. |