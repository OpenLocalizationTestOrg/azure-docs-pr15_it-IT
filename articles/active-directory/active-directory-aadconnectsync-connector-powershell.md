<properties
   pageTitle="Connettore PowerShell | Microsoft Azure"
   description="In questo articolo viene descritto come configurare Windows PowerShell connettore Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="windows-powershell-connector-technical-reference"></a>Tecniche di Windows PowerShell connettore
Questo articolo descrive il connettore di Windows PowerShell. L'articolo si applica ai seguenti prodotti:

- Gestione identità Microsoft 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   È necessario utilizzare correzione 4.1.3671.0 o versioni successive [KB3092178](https://support.microsoft.com/kb/3092178).

Per MIM2016 e FIM2010R2, il connettore è disponibile per il download dall' [Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Panoramica del connettore PowerShell
Il connettore PowerShell consente di integrare il servizio di sincronizzazione con i sistemi esterni che offrono API basate su Windows PowerShell. Il connettore fornisce un collegamento tra le funzionalità dell'agente di gestione basato su chiamata extensible connettività 2 framework (ECMA2) e Windows PowerShell. Per ulteriori informazioni su framework ECMA, vedere [Extensible connettività 2.2 gestione agente della Guida di riferimento](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Prerequisiti
Prima di utilizzare il connettore, accertarsi di avere le operazioni seguenti per il server di sincronizzazione:

- 4.5.2 Microsoft .NET Framework o versioni successive
- Windows PowerShell 2.0, 3.0 o 4.0

Il criterio di esecuzione nel server del servizio di sincronizzazione deve essere configurato per consentire al connettore eseguire gli script di Windows PowerShell. A meno che gli script viene eseguito il connettore è firmato digitalmente, configurare i criteri di esecuzione eseguendo questo comando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Creare un nuovo connettore
Per creare una connessione di Windows PowerShell nel servizio di sincronizzazione, è necessario specificare una serie di script di Windows PowerShell che vengono eseguite le operazioni richieste dal servizio di sincronizzazione. A seconda dell'origine dati si connette a e le funzionalità necessarie, gli script è necessario implementare varia. In questa sezione descrive ogni script che può essere implementato e quando necessari.

Il connettore di Windows PowerShell è progettato per archiviare ogni script all'interno del database di servizio di sincronizzazione. Sebbene sia possibile eseguire gli script che sono archiviati nel file system, è più semplice inserire il corpo di ogni script direttamente alla configurazione del connettore.

Per creare un connettore PowerShell, selezionare **Agente di gestione** e **creare**nel **Servizio di sincronizzazione** . Selezionare il connettore **PowerShell (Microsoft)** .

![Creare connettore](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Connettività
Inserire i parametri di configurazione per la connessione a un sistema remoto. Questi valori in modo sicuro vengono archiviati dal servizio di sincronizzazione e resi disponibili per gli script di Windows PowerShell quando viene eseguito il connettore.

![Connettività](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

È possibile configurare i parametri di connessione seguenti:

**Connettività**

Parametro | Valore predefinito | Scopo
--- | --- | ---
Server | <Blank> | Nome del server che il connettore deve connettersi.
Dominio | <Blank> | Dominio delle credenziali per l'archiviazione da utilizzare quando viene eseguito il connettore.
Utente | <Blank> | Nome utente delle credenziali per l'archiviazione da utilizzare quando viene eseguito il connettore.
Password | <Blank> | Password delle credenziali per l'archiviazione da utilizzare quando viene eseguito il connettore.
Rappresentare Account del connettore | FALSO | Quando è true, il servizio di sincronizzazione esegue gli script di Windows PowerShell nel contesto di credenziali. Se possibile, è consigliabile che il parametro **$Credentials** viene passato a ogni script viene utilizzato invece di rappresentazione. Per ulteriori informazioni sulle autorizzazioni aggiuntive necessari per usare questa opzione, vedere [Configurazione aggiuntive per la rappresentazione](#additional-configuration-for-impersonation).
Caricare profilo utente quando si utilizza | FALSO | Indica a Windows per caricare il profilo utente delle credenziali del connettore durante la rappresentazione. Se l'utente rappresentato dispone di un profilo, il connettore non viene caricato il profilo. Per ulteriori informazioni sulle autorizzazioni aggiuntive necessari per utilizzare questo parametro, vedere [Configurazione aggiuntive per la rappresentazione](#additional-configuration-for-impersonation).
Tipo di accesso quando si utilizza | Nessuno | Tipo di accesso durante la rappresentazione. Per ulteriori informazioni, consultare [dwLogonType] [ dw] documentazione.
Solo script con firma | FALSO | Se true, il connettore di Windows PowerShell convalida che ogni script presenta una firma digitale. Se è false, verificare che di Windows PowerShell esecuzione criterio del server servizio di sincronizzazione RemoteSigned o senza restrizioni.

**Modulo comune**  
Il connettore consente di archiviare un modulo di Windows PowerShell condiviso nella configurazione. Quando il connettore viene eseguito uno script, il modulo Windows PowerShell viene estratto nel file System in modo che possa essere importato da ogni script.

Per gli script di importare, esportare e sincronizzazione delle Password, il modulo comune viene estratto nella cartella MAData del connettore. Per gli script di individuazione Schema, convalida, grafici e partizione, il modulo comune viene estratto per la cartella % TEMP %. In entrambi i casi, lo script modulo comuni estratto è denominato in base all'impostazione nome Script modulo comune.

Per caricare un modulo denominato FIMPowerShellConnectorModule.psm1 dalla cartella MAData, utilizzare l'istruzione seguente:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Per caricare un modulo denominato FIMPowerShellConnectorModule.psm1 dalla cartella % TEMP %, utilizzare l'istruzione seguente:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Convalida dei parametri**  
Script di convalida è uno script di Windows PowerShell facoltativo che può essere utilizzato per verificare che i parametri di configurazione del connettore forniti dall'amministratore siano validi. La convalida del server, credenziali di connessione e la connettività parametri sono comuni utilizzi dello script di convalida. Script di convalida viene chiamato dopo le schede seguenti e finestre di dialogo vengono modificati:

- Connettività
- Parametri globali
- Configurazione delle partizioni

Script di convalida riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | Scheda configurazione o finestra di dialogo che ha generato la richiesta di convalida.
ConfigParameters | [KeyedCollection] [ keyk] [stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.

Script di convalida deve restituire un singolo oggetto ParameterValidationResult alla pipeline.

**Individuazione dello schema**  
Script di individuazione Schema è obbligatorio. Questo script restituisce i tipi di oggetto, attributi e vincoli per gli attributi che il servizio di sincronizzazione utilizza quando si configurano le regole del flusso di attributi. Script di individuazione Schema eseguire durante la creazione del connettore e popola schema del connettore. Viene usato anche tramite l'azione Aggiorna Schema in Gestione servizi di sincronizzazione.

Script di individuazione schema riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection] [ keyk] [stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.

Lo script deve restituire un singolo [Schema] [ schema] oggetto alla pipeline. L'oggetto Schema è composto da [SchemaType] [ schemaT] oggetti che rappresentano i tipi di oggetti (ad esempio: utenti e gruppi). L'oggetto SchemaType contiene un insieme di [SchemaAttribute] [ schemaA] oggetti che rappresentano gli attributi (ad esempio: nome, cognome e indirizzo postale) del tipo.

**Altri parametri**  
Oltre alle impostazioni di configurazione standard, è possibile definire le impostazioni di configurazione personalizzata aggiuntive specifiche per l'istanza del connettore. È possibile specificare questi parametri sul connettore partizione, o passaggio Esegui livelli e accessibili tramite lo script di Windows PowerShell pertinenti. Impostazioni di configurazione personalizzate possono essere create nel database del servizio di sincronizzazione in formato testo normale o può essere crittografati. Crittografa automaticamente il servizio di sincronizzazione e decrittografa protetta le impostazioni di configurazione quando richiesto.

Per specificare le impostazioni di configurazione personalizzate, separare il nome di ogni parametro con un punto e virgola (,).

Per accedere alle impostazioni di configurazione personalizzato da uno script, è necessario suffisso il nome di un carattere di sottolineatura ( \_ ) e l'ambito del parametro (globale, partizione o RunStep). Ad esempio, per accedere il parametro FileName globale, utilizzare il frammento di codice:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Funzionalità
La scheda funzionalità di gestione dell'agente progettazione definisce il comportamento e le funzionalità del connettore. Le selezioni effettuate in questa scheda non possono essere modificate dopo aver creato il connettore. Questa tabella sono elencate le impostazioni di funzionalità.

![Funzionalità](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Funzionalità | Descrizione |
--- | --- |
[Nome distinto stile][dnstyle] | Indica se il connettore supporta nomi distinti e pertanto, lo stile.
[Tipo di esportazione][exportT] | Determina il tipo di oggetti che vengono visualizzate allo script di esportazione. <li>AttributeReplace – include l'elenco completo dei valori per un attributo multivalore quando viene modificato l'attributo.</li><li>AttributeUpdate – include solo i delta per un attributo multivalore quando viene modificato l'attributo.</li><li>MultivaluedReferenceAttributeUpdate - include una serie completa di valori per gli attributi multivalore non di riferimento e Delta solo per gli attributi di riferimento multivalore.</li><li>ObjectReplace – include tutti gli attributi per un oggetto quando qualsiasi attribuire modifiche</li>
[Normalizzazione dei dati][DataNorm] | Indica il servizio di sincronizzazione per normalizzare attributi ancoraggio prima vengono fornite agli script.
[Conferma dell'oggetto][oconf] | Configura il comportamento di importazione in sospeso nel servizio di sincronizzazione. <li>Normale: il comportamento predefinito che prevede esportate tutte le modifiche apportate ai confermata tramite importazione</li><li>NoDeleteConfirmation: quando si elimina un oggetto, non esiste alcun importazione in sospeso generato.</li><li>NoAddAndDeleteConfirmation – quando un oggetto viene creato o eliminato, non esiste alcun importazione in sospeso generato.</li>
Utilizzare nome distinto come ancoraggio | Se lo stile di nome distinto è impostato su LDAP, anche l'attributo di ancoraggio per lo spazio di connettore è il nome distinto.
Operazioni simultanee dei connettori diversi | Quando è selezionata, è possono eseguire contemporaneamente di più connettori di Windows PowerShell.
Partizioni | Quando è selezionata, il connettore supporta più partizioni e individuazione partizione.
Gerarchia | Quando è selezionata, il connettore supporta una struttura gerarchica stile LDAP.
Attiva importazione | Quando è selezionata, il connettore consente di importare dati tramite gli script di importazione.
Attiva importazione Delta | Quando è selezionata, il connettore può richiedere delta gli script di importazione.
Abilitare l'esportazione | Quando è selezionata, il connettore Esporta dati tramite script di esportazione.
Abilitare l'esportazione completa | Quando è selezionata, gli script di esportazione supportano l'esportazione lo spazio connettore intera. Per usare questa opzione, abilitare esportare devono inoltre essere archiviati.
Nessun valore di riferimento nel primo passaggio di esportazione | Quando è selezionata, gli attributi di riferimento vengono esportati in un secondo passaggio di esportazione.
Abilitare Rinomina oggetto | Quando è selezionata, è possono modificare nomi distinti.
Elimina aggiungere come sostituire | Quando è selezionata, Elimina aggiungere operazioni vengono esportate come una singola sostituzione.
Abilitare le operazioni di Password | Quando è selezionata, script di sincronizzazione delle password sono supportate.
Abilitare la Password di esportazione nel primo passaggio | Quando è selezionata, le password impostata durante il provisioning vengono esportate quando viene creato l'oggetto.

### <a name="global-parameters"></a>Parametri globali
La scheda parametri globali nella finestra di progettazione di agente di gestione consente di configurare gli script di Windows PowerShell eseguiti dal connettore. È inoltre possibile configurare valori globali per le impostazioni di configurazione personalizzato definite nella scheda connettività.

**Individuazione di partizione**  
Una partizione è uno spazio dei nomi separato all'interno di uno schema condiviso. Ad esempio, in Active Directory ogni dominio è una partizione all'interno di un insieme di strutture. Una partizione è logico per l'importazione ed esportazione operazioni. Importazione / esportazione presentano partizione di un contesto e tutte le operazioni accade in questo contesto. Le partizioni dovrebbero rappresentare una gerarchia in LDAP. Nome distinto di una partizione viene utilizzato in Importa per verificare che tutti gli oggetti restituiti siano all'interno dell'ambito di una partizione. Il nome distinto partizione viene usato anche durante il provisioning del Metaverse nello spazio connettore per determinare la partizione che deve essere associato a un oggetto durante l'esportazione.

Script di individuazione partizione riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters  | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.

Lo script deve restituire un oggetto una sola [partizione] [ part] oggetto o un elenco [L] degli oggetti partizione alla pipeline.

**Individuazione di gerarchia**  
Script di individuazione gerarchia viene utilizzato solo quando la funzionalità di stile nome distinto LDAP. Lo script viene utilizzato per operazioni di esportazione e consentono di individuare e selezionare un insieme di contenitori che viene considerato avanti o indietro dell'ambito per l'importazione. Lo script deve fornire solo un elenco dei nodi figlio diretti del nodo principale fornito lo script.

Script di individuazione gerarchia riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
ParentNode | [HierarchyNode][hn] | Il nodo radice della gerarchia in cui lo script deve restituire elementi figlio diretti.

Lo script deve restituire un oggetto di HierarchyNode un singolo elemento figlio o un elenco [L] degli oggetti figlio HierarchyNode alla pipeline.

#### <a name="import"></a>Importazione
Connettori che supportano operazioni di importazione devono implementare tre script.

**Avviare l'importazione**  
Inizio importazione script viene eseguito all'inizio di un passaggio di importazione eseguire. Durante questo passaggio, è possibile stabilire una connessione al sistema di origine ed eseguire operazioni preliminari prima di importare dati dal sistema connesso.

Inizio importazione script riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Segnala lo script sul tipo di importazione eseguire delta o completa, partizione, gerarchia, filigrana e dimensioni pagina previsto.
Tipi di | [Schema][schema] | Schema per lo spazio del connettore da importata.

Lo script deve restituire un singolo [OpenImportConnectionResults] [ oicres] oggetto per la pipeline, ad esempio:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importare dati**  
Lo script di importazione dei dati viene chiamato dal connettore finché lo script indica che non contiene altri dati per importare. Il connettore di Windows PowerShell ha un formato di pagina di 9.999 oggetti. Se lo script restituisce più di 9.999 oggetti per l'importazione, è necessario supportare suddivisione in pagine. Espone connettore è una proprietà di dati personalizzati che è possibile utilizzare per un archivio di una filigrana in modo che ogni volta che lo script di dati di importazione, lo script riprende importazione di oggetti in cui è stata interrotta.

Script di importazione dati riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contiene la filigrana (CustomData) che può essere usata durante paging importazioni e delta Importa.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Segnala lo script sul tipo di importazione eseguire delta o completa, partizione, gerarchia, filigrana e dimensioni pagina previsto.
Tipi di | [Schema][schema] | Schema per lo spazio del connettore da importata.

Script di dati importazione necessario scrivere un elenco [[CSEntryChange][csec]] oggetto alla pipeline. Questa raccolta è composto da attributi CSEntryChange che rappresentano ogni oggetto da importare. Durante l'esecuzione di importazione completa, questa raccolta deve avere una serie completa di oggetti CSEntryChange che dispongono di tutti gli attributi per tutti gli oggetti. Durante una Delta l'importazione, l'oggetto CSEntryChange deve contenere i delta livello attributo per tutti gli oggetti per importare o una rappresentazione completa di oggetti che sono stati modificati (modalità di sostituzione).

**Importazione di fine**  
Al termine dell'importazione eseguita lo script fine importare. Questo script deve eseguire le operazioni di pulizia necessarie (ad esempio, chiudere le connessioni a sistemi) e rispondere agli errori.

Fine importazione script riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Segnala lo script sul tipo di importazione eseguire delta o completa, partizione, gerarchia, filigrana e dimensioni pagina previsto.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Segnala lo script del motivo che è stata terminata l'importazione.

Lo script deve restituire un singolo [CloseImportConnectionResults] [ cicres] oggetto per la pipeline, ad esempio:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Esportazione
Identico all'architettura di importazione del connettore, connettori che supportano l'esportazione necessario implementare tre script.

**Avviare l'esportazione**  
Script di esportazione inizio viene eseguito all'inizio di un'operazione di esportazione eseguire. Durante questo passaggio, è possibile stabilire una connessione al sistema di origine e condurre operazioni preparatorie prima di esportare i dati al sistema connesso.

Script di esportazione inizio riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Segnala lo script sul tipo di esportazione eseguire delta o completa, partizione, gerarchia e dimensioni pagina previsto.
Tipi di | [Schema][schema] | Schema per lo spazio di connettore viene esportato.

Lo script non deve restituire un tipo di output per la pipeline.

**Esportare i dati**  
Il servizio di sincronizzazione chiama lo script di esportazione dati quante volte necessarie elaborare tutte le esportazioni in sospeso. Se lo spazio connettore include altre esportazioni in attesa di quelle della pagina del connettore, lo script di esportazione dei dati può essere chiamato più volte e probabilmente più volte per lo stesso oggetto.

Script di esportazione dati riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
CSEntries | IList[CSEntryChange][csec] | Elenco di tutti gli oggetti il connettore spazio con in sospeso esportazioni elaborare durante questo passaggio.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Segnala lo script sul tipo di esportazione eseguire delta o completa, partizione, gerarchia e dimensioni pagina previsto.
Tipi di | [Schema][schema] | Schema per lo spazio di connettore viene esportato.

Script di esportazione dati deve restituire un [PutExportEntriesResults] [ peeres] oggetto alla pipeline. L'oggetto non è necessario includere le informazioni sul risultato per ogni connettore esportato, a meno che non si verifica un errore o modificarne l'attributo di ancoraggio. Ad esempio, per restituire un oggetto PutExportEntriesResults alla pipeline:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Esportazione di fine**  
Al termine dell'esportazione eseguito script esportare fine per l'esecuzione. Questo script deve eseguire le operazioni di pulizia necessarie (ad esempio, chiudere le connessioni a sistemi) e rispondere agli errori.

Script di esportazione fine riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Segnala lo script sul tipo di esportazione eseguire delta o completa, partizione, gerarchia e dimensioni pagina previsto.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Segnala lo script del motivo per che l'esportazione è stata terminata.

Lo script non deve restituire un tipo di output per la pipeline.

#### <a name="password-synchronization"></a>Sincronizzazione delle password
Connettori di Windows PowerShell da utilizzare come un valore di destinazione per le modifiche/reimpostazione della password.

Lo script password riceve i parametri seguenti dal connettore:

Nome | Tipo di dati | Descrizione
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][stringa, [ConfigParameter][cp]] | Tabella di parametri di configurazione per il connettore.
Credenziali | [PSCredential][pscred] | Contiene le eventuali credenziali immesse dall'amministratore della scheda connettività.
Partizione | [Partizione][part] | Partizione di directory a cui fa parte di CSEntry.
CSEntry | [CSEntry][cse] | Voce di spazio connettore per l'oggetto ricevuto una modifica della password o Ripristina.
OperationType | Stringa | Indica se l'operazione è la reimpostazione (**SetPassword**) o una modifica (**Cambia password**).
PasswordOptions | [PasswordOptions][pwdopt] | Flag che specificano la password corretta reimpostazione comportamento. Questo parametro è disponibile solo se OperationType **SetPassword**.
OldPassword | Stringa | Popolata con una password per la modifica della password dell'oggetto. Questo parametro è disponibile solo se OperationType **Cambia password**.
NewPassword | Stringa | Popolata con nuova password dell'oggetto che è necessario impostare lo script.

Lo script password non è previsto per restituire i risultati nella pipeline di Windows PowerShell. Se si verifica un errore dello script di password, lo script genera una delle seguenti eccezioni per segnalare il problema il servizio di sincronizzazione:

- [PasswordPolicyViolationException] [ pwdex1] – generata se la password non soddisfa i criteri di password nel sistema connesso.
- [PasswordIllFormedException] [ pwdex2] – generata se la password non è accettabile per il sistema connesso.
- [PasswordExtension] [ pwdex3] – generata per tutti gli altri errori di script password.

## <a name="sample-connectors"></a>Connettori di esempio
Per una panoramica completa i connettori di esempio disponibile, vedere [Windows PowerShell connettore esempio connettore insieme][samp].

## <a name="other-notes"></a>Altre note

### <a name="additional-configuration-for-impersonation"></a>Configurazione aggiuntive per la rappresentazione
Concedere all'utente che viene rappresentato le autorizzazioni seguenti nel server del servizio di sincronizzazione:

Accesso in lettura chiavi del Registro di sistema seguenti:

- HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
- HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Per determinare l'identificatore di protezione (SID) dell'account del servizio servizio di sincronizzazione, eseguire i comandi di PowerShell seguenti:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Accesso in lettura per le cartelle di sistema di file seguenti:

- %ProgramFiles%\Microsoft forefront identità Manager\2010\Synchronization Service\Extensions
- %ProgramFiles%\Microsoft forefront identità Manager\2010\Synchronization Service\ExtensionsCache
- %ProgramFiles%\Microsoft forefront identità Manager\2010\Synchronization Service\MaData\\{ConnectorName}

Sostituire il nome del connettore Windows PowerShell per il segnaposto {ConnectorName}.

## <a name="troubleshooting"></a>Risoluzione dei problemi

-   Per informazioni su come attivare la registrazione risolvere i problemi di connessione, informazioni su [come abilitare ETW Tracing per i connettori](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
