La maggior parte degli errori di autenticazione ora il risultato di impostazioni di configurazione non corretti o non coerente. Di seguito sono riportati alcuni suggerimenti specifici per cose da verificare.

* Assicurarsi che non perdere in un punto qualsiasi del pulsante **Salva** . Questo è spesso un'operazione semplice e il risultato è che è necessario essere esamina i valori corretti in una pagina del portale ma in realtà non sono stati salvati in un ambiente di Azure o dell'applicazione di Azure Active Directory.
* Per accedere a impostazioni configurati e il **Impostazioni dell'applicazione** del portale di Azure, verificare che il corretto API app o web app è stata selezionata quando le impostazioni sono state immesse.  Assicurarsi che le impostazioni sono state immesse come **le impostazioni dell'App** e non **le stringhe di connessione**, come il formato di due sezioni è simile.
* Per l'autenticazione di un front end JavaScript, scaricare il manifesto per verificare che `oauth2AllowImplicitFlow` è stato modificato al `true`.
* Verificare che è stata usata HTTPS nel punto in cui è stato configurato URL:

    * Codice di progetto
    * In CORS
    * In impostazioni dell'ambiente Azure App per ogni API app e web app
    * Nella sezione Impostazioni applicazione di Azure Active Directory.
    
    Si noti che se si copia URL di un'app API dal portale, spesso ha `http://` ed è necessario modificare manualmente la `https://`.

* Assicurarsi che le modifiche di codice distribuite correttamente. In una soluzione più progetti, ad esempio, è possibile modificare codice del progetto e accidentalmente scegliere una delle altre se si desidera distribuire la modifica.
* Verificare che si intende URL HTTPS nel browser, non URL HTTP. Per impostazione predefinita, Visual Studio crea pubblicare profili con URL HTTP, ovvero quali apre nel browser dopo la distribuzione di un progetto.
* Per l'autenticazione di un front end JavaScript, assicurarsi che CORS sia configurato correttamente app di API che il codice JavaScript chiama. In caso di dubbi sulle se il problema è correlata CORS, provare a "*" come l'URL di origine consentiti. 
* Per un front end JavaScript aprire nella scheda sviluppo strumenti Console del browser per ottenere ulteriori informazioni sull'errore ed esaminare le richieste HTTP sulla rete. Tuttavia, messaggi di errore Console possono essere fuorvianti. Se si riceve un messaggio che indica un errore CORS, vero problema può essere l'autenticazione. È possibile verificare se ciò avviene eseguendo l'app con l'autenticazione temporaneamente temporaneamente disabilitato.
* Per un'app API .NET, verificare che si desidera ottenere le informazioni nei messaggi di errore possibili mediante l'impostazione [modalità customErrors su disattivato](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Per un'app API .NET, avviare una [sessione remota di debug](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)ed esaminare i valori delle variabili che vengono passati al codice che utilizza ADAL per acquisire un token del titolare o il codice che verifica delle attestazioni rispetto a quello previsto del servizio principale. Si noti che il codice è possibile selezionare i valori di configurazione da molte origini diverse, pertanto è possibile trovare sorprese in questo modo. Ad esempio, se commette `ida:ClientId` come `ida:ClientID` durante la configurazione delle impostazioni dell'ambiente servizio App Azure, è possibile che venga visualizzato il codice di `ida:ClientId` valore cercando da config, ignorando le impostazioni di servizio App Azure. 
* Se non funziona in una finestra di Internet Explorer normale, un log aggiuntivo esistente potrebbe interferire; Provare InPrivate e Chrome o Firefox.
