<properties
   pageTitle="Gestione degli errori di automazione Azure | Microsoft Azure"
   description="Questo articolo fornisce base errore gestione procedura per risolvere gli errori comuni di automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="stevenka"
   editor="tysonn"
   tags="top-support-issue"
   keywords="Errore di automazione, la gestione degli errori"/>
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/06/2016"
   ms.author="sngun; v-reagie"/>

# <a name="error-handling-tips-for-common-azure-automation-errors"></a>Suggerimenti per gli errori comuni delle Azure automazione di gestione degli errori

In questo articolo vengono illustrati alcuni degli errori di automazione di Azure comuni potrebbero verificarsi e vengono suggerite alcune operazioni di gestione degli errori possibili.

## <a name="troubleshoot-authentication-errors-when-working-with-azure-automation-runbooks"></a>Risolvere i problemi di autenticazione quando si lavora con runbook automazione Azure  

### <a name="scenario-sign-in-to-azure-account-failed"></a>Scenario: Accedere all'Account Azure non è riuscito

**Errore:** Viene visualizzato l'errore "Unknown_user_type: utente tipo sconosciuto" quando si lavora con i cmdlet Aggiungi AzureAccount o AzureRmAccount di accesso.

**Motivo dell'errore:** Questo errore si verifica se il nome di risorsa credenziali non è valido o se il nome utente e password utilizzati per impostare la risorsa delle credenziali di automazione non sono validi.

**Suggerimenti risoluzione dei problemi:** Per individuare il problema, eseguire le operazioni seguenti:  

1. Assicurarsi che non si dispone di caratteri speciali, tra cui la **@** caratteri nel nome della risorsa credenziali automazione utilizzata per la connessione a Azure.  

2. Verificare che è possibile utilizzare il nome utente e la password vengono memorizzati nelle credenziali di automazione di Azure nell'editor PowerShell ISE locale. È possibile eseguire questa operazione, eseguire i cmdlet seguenti nella PowerShell ISE:  

        $Cred = Get-Credential  
        #Using Azure Service Management   
        Add-AzureAccount –Credential $Cred  
        #Using Azure Resource Manager  
        Login-AzureRmAccount –Credential $Cred

3. Se l'autenticazione non riesce in locale, ciò significa che è stato ancora configurato correttamente le credenziali di Azure Active Directory. Fare riferimento ai post di blog [Authenticating in Azure con Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) per ottenere l'account di Azure Active Directory configurati correttamente.  


### <a name="scenario-unable-to-find-the-azure-subscription"></a>Scenario: Impossibile trovare l'abbonamento Azure

**Errore:** Viene visualizzato l'errore "l'abbonamento denominata ``<subscription name>`` non è possibile trovare" quando si lavora con i cmdlet selezionare AzureSubscription o selezionare AzureRmSubscription.

**Motivo dell'errore:** Questo errore si verifica se il nome dell'abbonamento non è valido o se l'utente di Azure Active Directory che sta tentando di visualizzare i dettagli di abbonamento non è configurato come un amministratore della sottoscrizione.

**Suggerimenti risoluzione dei problemi:** Per determinare se si sono autenticati correttamente in Azure e si dispone dell'accesso all'abbonamento che si sta tentando di selezionare, eseguire le operazioni seguenti:  

1. Assicurarsi che eseguire **Aggiungi AzureAccount** prima di eseguire il cmdlet **Selezionare AzureSubscription** .  

2. Se viene comunque visualizzato questo messaggio di errore, modificare il codice aggiungendo il cmdlet **Get-AzureSubscription** seguendo il cmdlet **AzureAccount Aggiungi** e quindi eseguire il codice.  A questo punto, verificare se l'output di Get-AzureSubscription contiene i dettagli dell'abbonamento.  
    * Se i dettagli abbonamento nell'output non è visibile, significa che l'abbonamento non è ancora inizializzato.  
    * Se viene visualizzato dettagli abbonamento nell'output, confermare che si usa il nome dell'abbonamento corretta o l'ID con il cmdlet **Selezionare AzureSubscription** .   


### <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a>Scenario: Autenticazione in Azure non riuscita è attivata l'autenticazione a più fattori

**Errore:** Viene visualizzato l'errore "Aggiungi AzureAccount: AADSTS50079: la registrazione di autenticazione (prova-up) è necessaria" durante l'autenticazione in Azure Azure nome utente e la password.

**Motivo dell'errore:** Se si dispone di autenticazione a più fattori nel proprio account Azure, è possibile usare un utente di Azure Active Directory per eseguire l'autenticazione in Azure.  Se, tuttavia, è necessario usare un certificato o un servizio principale per l'autenticazione in Azure.

**Suggerimenti risoluzione dei problemi:** Per usare un certificato con i cmdlet di gestione dei servizi di Azure, fare riferimento a [la creazione e aggiunta di un certificato per la gestione dei servizi Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Per utilizzare un'entità servizio con i cmdlet Gestione risorse di Azure, si riferiscono alla [creazione dell'entità servizio nel portale Azure](./resource-group-create-service-principal-portal.md) e [autenticazione un'entità servizio Gestione risorse Azure.](./resource-group-authenticate-service-principal.md)


## <a name="troubleshoot-common-errors-when-working-with-runbooks"></a>Risolvere i problemi comuni quando si lavora con runbook

### <a name="scenario-runbook-fails-because-of-deserialized-object"></a>Scenario: Runbook non riesce a causa di oggetto

**Errore:** I runbook ha esito negativo e l'errore "non è possibile associare parametro ``<ParameterName>``. Non è possibile convertire il ``<ParameterType>`` valore di tipo Deserialized ``<ParameterType>`` digitare ``<ParameterType>``".

**Motivo dell'errore:** Se il runbook è un flusso di lavoro di PowerShell, Archivia oggetti complessi in formato deserializzato per mantenere lo stato di runbook se il flusso di lavoro è sospesa.  

**Suggerimenti sulla risoluzione dei problemi:**  
Uno dei seguenti tre soluzioni consente di risolvere il problema:

1. Se si tubazioni oggetti complessi da un cmdlet a un'altra, disporre i cmdlet in un InlineScript è impostato su.  
2. Passare il nome o il valore che è necessario dall'oggetto complessa anziché passando l'intero oggetto.  

3. Usare un runbook PowerShell invece un runbook PowerShell del flusso di lavoro.  


### <a name="scenario-runbook-job-failed-because-the-allocated-quota-exceeded"></a>Scenario: Runbook processo non è riuscita perché il superamento della quota allocata

**Errore:** Il processo di runbook non l'errore "la quota per il processo di totale mensile fase di esecuzione è stato raggiunto per l'abbonamento".

**Motivo dell'errore:** Questo errore si verifica quando l'esecuzione del processo supera la quota gratuita 500 minuti per il proprio account. La quota si applica a tutti i tipi di attività di esecuzione di processo, ad esempio un processo di verifica, avviare un processo dal portale di, viene eseguito un processo utilizzando webhooks e pianificazione di un processo per l'esecuzione con Azure portale o nel centro dati. Per ulteriori informazioni sui prezzi per l'automazione vedere [automazione prezzi](https://azure.microsoft.com/pricing/details/automation/).

**Suggerimenti risoluzione dei problemi:** Se si desidera utilizzare più di 500 minuti di elaborazione per mese è necessario modificare la sottoscrizione dalla versione gratuita livello a livello di base. È possibile passare a livello di base eseguendo la procedura seguente:  

1. Accedere al proprio abbonamento Azure  
2. Selezionare l'account di automazione da aggiornare  
3. Fare clic su **Impostazioni** > **livello prezzi e l'uso** > **livello prezzi**  
4. Scegliere **base** e lo **scegliere il livello prezzo**    


### <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a>Scenario: Cmdlet non è riconosciuto durante l'esecuzione di una runbook

**Errore:** Il processo di runbook non l'errore "``<cmdlet name>``: il termine ``<cmdlet name>`` non è riconosciuto come il nome di un cmdlet, funzione, file di script o programma eseguibile."

**Motivo dell'errore:** Questo errore si verifica quando il motore di PowerShell non è possibile trovare il cmdlet che si utilizza il runbook.  Può trattarsi di perché il modulo contenente il cmdlet non è presente l'account, si verifica un conflitto di nome con un nome runbook o il cmdlet esiste anche in un altro modulo e automazione non riesce a risolvere il nome.

**Suggerimenti risoluzione dei problemi:** Una delle seguenti soluzioni consente di risolvere il problema:  

- Verificare di avere immesso correttamente il nome del cmdlet.  

- Verificare che il cmdlet è presente nell'account di automazione e che non sono presenti conflitti. Per verificare se è presenta il cmdlet, aprire una runbook in modalità di modifica e cercare il cmdlet da trovare nella raccolta o Esegui **Get-Command ``<CommandName>`` **.  Dopo aver verificato che il cmdlet è disponibile per l'account e che non sono presenti conflitti di nome con altri cmdlet o runbook, aggiungere all'area di lavoro e assicurarsi che si sta utilizzando un parametro valido, impostato il runbook.  

- Se si dispone di un conflitto di nome e il cmdlet è disponibile in due diversi moduli, è possibile risolvere utilizzando il nome completo per il cmdlet. Ad esempio, è possibile utilizzare **ModuleName\CmdletName**.  

- Se si siano eseguendo la runbook locale in un gruppo di lavoro ibrida, assicurarsi che il modulo/cmdlet è installato nel computer che ospita lavoro ibrida.


### <a name="scenario-a-long-running-runbook-consistently-fails-with-the-exception-the-job-cannot-continue-running-because-it-was-repeatedly-evicted-from-the-same-checkpoint"></a>Scenario: Una lunga runbook non riesce con l'eccezione: "il processo non è possibile continuare a eseguire poiché è stata ripetutamente rimosso dalla stessa verifica".

**Motivo dell'errore:** Questa caratteristica è il comportamento di progettazione per il monitoraggio "Quota" dei processi all'interno di automazione di Azure, che sospende automaticamente un runbook se esegue più di 3 ore. Tuttavia, il messaggio di errore restituito non fornisce "passaggi successivi" Opzioni. È possibile sospendere un runbook per diversi motivi. Sospende avvengano prevalentemente a causa di errori. Ad esempio un'eccezione in un runbook, un errore di rete o un arresto anomalo sul lavoro Runbook esecuzione dal runbook, tutti causeranno dal runbook sospendere e avviare dalla relativa verifica ultimo quando ripresa.

**Suggerimenti risoluzione dei problemi:** La documentazione soluzione per evitare questo problema consiste nell'usare punti di controllo in un flusso di lavoro.  Per informazioni che più fare riferimento ai [Flussi di lavoro di apprendimento PowerShell](automation-powershell-workflow.md#Checkpoints).  Ulteriori informazioni sul "Quota" e del punto di controllo sono disponibili in questo articolo di blog [Con punti di controllo in runbook](https://azure.microsoft.com/en-us/blog/azure-automation-reliable-fault-tolerant-runbook-execution-using-checkpoints/).


## <a name="troubleshoot-common-errors-when-importing-modules"></a>Risolvere gli errori comuni durante l'importazione di moduli

### <a name="scenario-module-fails-to-import-or-cmdlets-cant-be-executed-after-importing"></a>Scenario: Modulo non riesce a importare o non è possibile eseguire i cmdlet dopo l'importazione

**Errore:** Un modulo non riesce a importare o Importa correttamente, ma non cmdlet sono estratti.

**Motivo dell'errore:** Alcuni motivi comuni che un modulo non può importare correttamente in Azure automazione sono:  

- La struttura non corrisponde alla struttura che deve essere automazione sia nella.  

- Il modulo si basa su un altro modulo che non è stato distribuito al proprio account di automazione.  

- Il modulo risulta mancante dipendenze nella cartella.  

- Il cmdlet **New-AzureRmAutomationModule** viene utilizzato per caricare il modulo e non è stato assegnato il percorso completo dello spazio di archiviazione o non è stato caricato il modulo tramite un URL accessibile al pubblico.  

**Suggerimenti sulla risoluzione dei problemi:**  
Una delle seguenti soluzioni consente di risolvere il problema:  

- Verificare che il modulo segua nel formato seguente:  
ModuleName.Zip **->** NomeModulo o numero di versione **->** (ModuleName.psm1, ModuleName.psd1)

- Aprire il file .psd1 e verificare se il modulo presenta dipendenze.  In caso affermativo, è possibile caricare questi moduli per l'account di automazione.  

- Assicurarsi che siano presenti nella cartella modulo qualsiasi DLL di cui si fa riferimento.  


## <a name="troubleshoot-common-errors-when-working-with-desired-state-configuration-dsc"></a>Risolvere i problemi comuni quando si lavora con bene accolta stato configurazione (DSC)  

### <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a>Scenario: Nodo è in stato di errore con un errore "Non trovato"

**Errore:** Il nodo dispone di un report di stato **non riuscito** e che contiene l'errore "il tentativo di ottenere l'azione da server https://``<url>``//accounts/``<account-id>``/Nodes(AgentId=``<agent-id>``)/GetDscAction non riuscita una configurazione valida ``<guid>`` non è possibile trovare."

**Motivo dell'errore:** Questo messaggio di errore in genere si verifica quando il nodo viene assegnato un nome di configurazione (ad esempio ABC) anziché un nome di configurazione nodo (ad esempio ABC. Server Web).  

**Suggerimenti sulla risoluzione dei problemi:**  

- Verificare che si sta assegnando il nodo con "Nome configurazione" e non "di configurazione".  

- È possibile assegnare una configurazione nodi in un nodo nel portale Azure o con un cmdlet di PowerShell.
    - Per assegnare una configurazione nodi a un nodo nel portale di Azure, aprire e il **DSC nodi** , quindi selezionare un nodo e fare clic sul pulsante **assegnare nodo Configurazione** .  
    - Per assegnare una configurazione nodi a un nodo utilizzando cmdlet di PowerShell, utilizzare il cmdlet **Set-AzureRmAutomationDscNode**


### <a name="scenario--no-node-configurations-mof-files-were-produced-when-a-configuration-is-compiled"></a>Scenario: Nessuna configurazione nodo (file MOF) sono stati prodotti quando viene compilata una configurazione

**Errore:** Il processo di compilazione DSC sospende con l'errore: "la compilazione è stato completato correttamente, ma non .mofs configurazione nodo generate".

**Motivo dell'errore:** Quando l'espressione che segue che restituisce la parola chiave **nodo** nella configurazione DSC $null quindi Nessuna configurazione nodo verrà prodotto.    

**Suggerimenti sulla risoluzione dei problemi:**  
Una delle seguenti soluzioni consente di risolvere il problema:  

- Assicurarsi che il valore di expression accanto alla parola chiave **nodo** nella definizione della configurazione non restituiscono $null.  
- Se si sta passando ConfigurationData durante la compilazione della configurazione, verificare che si siano passando i valori previsti che richiede la configurazione da [ConfigurationData](automation-dsc-compile.md#configurationdata).


### <a name="scenario--the-dsc-node-report-becomes-stuck-in-progress-state"></a>Scenario: Report di nodo DSC rimane bloccato "in corso"

**Errore:** DSC agente non restituisce "Nessuna istanza trovata con valori di proprietà specificati."

**Motivo dell'errore:** È stato eseguito l'aggiornamento alla versione WMF e hanno danneggiato WMI.  

**Suggerimenti risoluzione dei problemi:** Seguire le istruzioni in post di blog [DSC problemi noti e limitazioni](https://msdn.microsoft.com/powershell/wmf/limitation_dsc) per la risoluzione del problema.


### <a name="scenario--unable-to-use-a-credential-in-a-dsc-configuration"></a>Scenario: Impossibile utilizzare credenziali in una configurazione DSC

**Errore:** Il processo di compilazione DSC è stato sospeso con l'errore: "errore InvalidOperationException elaborazione proprietà 'credenziale" di tipo ``<some resource name>``: la conversione e la memorizzazione di una password crittografata come testo normale è consentito solo se PSDscAllowPlainTextPassword è impostato su true ".

**Motivo dell'errore:** Si hanno implementato credenziali in una configurazione ma non ha fornito iniz **ConfigurationData** per impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo.  

**Suggerimenti sulla risoluzione dei problemi:**  
- Assicurarsi di passare iniz **ConfigurationData** impostare **PSDscAllowPlainTextPassword** su true per ogni configurazione nodo menzionati nella configurazione. Per ulteriori informazioni, fare riferimento alle [risorse di Azure automazione DSC](automation-dsc-compile.md#assets).


## <a name="next-steps"></a>Passaggi successivi

Se si dispongano di eseguite le procedure di risoluzione dei problemi indicate e necessaria ulteriore assistenza in qualsiasi momento in questo articolo, è possibile:

- È possibile ottenere assistenza da esperti di Azure. Inviare il problema per il [forum MSDN Azure o Overflow dello Stack.](https://azure.microsoft.com/support/forums/)

- File di una richiesta di assistenza Azure. Passare al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e fai clic su **supporto** in **supporto tecnico e di fatturazione**.

- Inviare una richiesta di Script [nell'Interfaccia di Script](https://azure.microsoft.com/documentation/scripts/) se si sta cercando una soluzione di runbook automazione Azure o un modulo di integrazione.

- Registrare commenti e suggerimenti o richieste per l'automazione di Azure nella [Voce di utente](https://feedback.azure.com/forums/34192--general-feedback).
