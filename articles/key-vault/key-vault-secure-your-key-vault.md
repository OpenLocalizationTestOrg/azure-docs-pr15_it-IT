<properties
    pageTitle="Proteggere la chiave archivio | Microsoft Azure"
    description="Gestire le autorizzazioni di accesso per archivio chiave per la gestione degli archivi e chiavi e informazioni riservate. Modello di autenticazione e l'autorizzazione per archivio chiave e su come proteggere l'archivio di chiave"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/07/2016"
    ms.author="ambapat"/>


# <a name="secure-your-key-vault"></a>Proteggere la chiave archivio

Archivio chiave Azure è un servizio cloud che protegge chiavi di crittografia e informazioni riservate (ad esempio i certificati, le stringhe di connessione, le password) per le applicazioni cloud. Poiché questi dati sono riservati e business critico, che si desidera proteggere l'accesso per il chiavi archivi in modo che solo autorizzati applicazioni e gli utenti possono accedere archivio chiave. In questo articolo viene fornita una panoramica del modello di access archivio chiave, viene illustrato l'autenticazione e autorizzazione e viene descritto come proteggere l'accesso all'archivio chiave per le applicazioni cloud con un esempio.

## <a name="overview"></a>Panoramica

Accesso a un archivio di chiave verrà controllata mediante due interfacce separate: piano di gestione e dati. Per entrambi i piani le autorizzazioni e l'autenticazione corretta è necessario un chiamante (un utente o un'applicazione) può ottenere l'accesso all'archivio chiave. Autenticazione stabilisce l'identità del chiamante, mentre l'autorizzazione determina quali il chiamante può eseguire le operazioni.

Per l'autenticazione del piano di gestione e piano dati utilizzare Azure Active Directory. Per l'autorizzazione, piano di gestione utilizza controllo dell'accesso basato sui ruoli (RBAC) mentre piano dati vengono utilizzati i criteri di accesso chiave archivio.

Ecco una breve panoramica degli argomenti trattati:

[Autenticazione tramite Azure Active Directory](#authentication-using-azure-active-direcrory) - in questa sezione viene descritto come un chiamante viene autenticato con Azure Active Directory per accedere a un archivio chiave tramite il piano di gestione e dati. 

[Piano di gestione e dati](#management-plane-and-data-plane) - piano di gestione e dati sono due i piani di access usati per l'accesso all'archivio chiave. Ogni piano access supporta operazioni specifiche. In questa sezione vengono descritti i punti finali di access, operazioni supportate e accedere al metodo di controllo utilizzato da ogni piano. 

[Controllo di accesso di piano di gestione](#management-plane-access-control) - In questa sezione che vengono illustrate che consente l'accesso alle operazioni di piano di gestione mediante il controllo dell'accesso basato sui ruoli.

[Controllo dell'accesso il piano di dati](#data-plane-access-control) , in questa sezione viene descritto come utilizzare il criterio di accesso di archivio chiave per controllare l'accesso ai dati piano.

[Esempio](#example) : in questo esempio viene descritto come configurare il controllo di accesso per l'archivio chiave consentire a tre diversi team (team di protezione, gli sviluppatori/operatori e revisori) per eseguire attività specifiche per sviluppare, gestire e monitorare un'applicazione di Azure.


## <a name="authentication-using-azure-active-directory"></a>Autenticazione tramite Azure Active Directory

Quando si crea un archivio di chiave in un abbonamento a Azure, questo viene automaticamente associato con tenant di Azure Active Directory dell'abbonamento. In questo tenant per accedere a questa chiave archivio, è necessario registrare tutti i chiamanti (utenti e applicazioni). Un'applicazione o un utente deve eseguire l'autenticazione con Azure Active Directory per accedere a archivio chiave. Si applica ad accesso piano di piano e dati di gestione. In entrambi i casi, un'applicazione può accedere chiave archivio in due modi:

-  **ogni utente + app accedere** - in genere si tratta di applicazioni che accedono archivio chiave per conto di un utente effettuato l'accesso. PowerShell Azure, portale Azure sono illustrati esempi di questo tipo di accesso. Esistono due modi per concedere l'accesso agli utenti: è un modo per concedere l'accesso agli utenti in modo che possono accedere archivio chiave da qualsiasi applicazione e altro modo per concedere l'accesso utente all'archivio chiave solo quando si utilizzano una specifica applicazione (definita identità composta). 
-  **accesso solo app** - per le applicazioni che esegue servizi daemon, processi e così via in background. Identità dell'applicazione è concesso l'accesso all'archivio chiave.

In entrambi i tipi di applicazioni, l'applicazione autentica con Azure Active Directory utilizzando uno dei [metodi di autenticazione supportati](../active-directory/active-directory-authentication-scenarios.md) e acquisisce un token. Metodo di autenticazione utilizzato dipende dal tipo di applicazione. L'applicazione utilizza tale token quindi Invia richiesta API REST all'archivio chiave. In caso di accesso di piano di gestione delle richieste di vengono indirizzate tramite Gestione risorse Azure endpoint. Quando si accede piano dati, comunica applicazioni direttamente a un tasto di archivio endpoint. Visualizzare ulteriori dettagli sul [flusso di autenticazione intera](../active-directory/active-directory-protocols-oauth-code.md). 

Il nome della risorsa per il quale l'applicazione richiede un token varia a seconda se l'applicazione accede a piano di gestione o un piano dati. Pertanto il nome della risorsa è Gestione piano o dati piano estremità descritto nella tabella in una sezione successiva, a seconda dell'ambiente Azure.

Con un unico meccanismo per la gestione e piano dati autenticazione offre i proprio vantaggi:

- Organizzazioni centralmente controllare l'accesso a tutti gli archivi chiavi dell'organizzazione
- Se un utente lascia, perdono istantaneamente l'accesso a tutti gli archivi chiavi dell'organizzazione
- È possibile personalizzare l'autenticazione tramite le opzioni di Azure Active Directory (ad esempio, l'abilitazione autenticazione a più fattori per una maggiore protezione)

## <a name="management-plane-and-data-plane"></a>Piano di gestione e dati

Archivio chiave Azure è un servizio Azure disponibile tramite il modello di distribuzione di Manager delle risorse di Azure. Quando si crea un archivio di chiave, viene visualizzato un contenitore virtuale in cui è possibile creare altri oggetti quali chiavi, informazioni riservate e certificati. Accedendo l'archivio di chiave utilizzando il piano di gestione e dati per eseguire operazioni specifiche. Interfaccia di piano di gestione viene utilizzato per gestire l'archivio chiave stessa, ad esempio creazione, eliminazione, aggiornamento degli attributi di archivio chiave e impostazione di criteri di accesso per il piano di dati. Interfaccia di piano dati viene utilizzata per aggiungere, eliminare, modificare e utilizzare i tasti, informazioni riservate e certificati archiviati nell'archivio chiave.

Le interfacce piano dati e piano di gestione sono accessibili tramite diversi endpoint (vedere la tabella). La seconda colonna della tabella vengono descritti i nomi DNS per gli endpoint in ambienti diversi Azure. La terza colonna descrive le operazioni eseguite da ogni piano di accesso. Ogni piano di accesso ha anche il proprio meccanismo di controllo di accesso: per il controllo di accesso piano di gestione viene impostato mediante il controllo dell'accesso Azure Resource Manager Role-Based (RBAC), mentre per il controllo di accesso piano dati viene impostato mediante il criterio di accesso chiave archivio.

| Piano di Access | Endpoint di Access | Operazioni | Meccanismo di controllo di accesso|
|--------------|------------------|--------------------|--------|
| Piano di gestione|**Globale:**<br> Management.Azure.com:443<br><br> **Azure Cina:**<br> Management.chinacloudapi.CN:443<br><br> **Azure governo degli Stati Uniti:**<br> Management.usgovcloudapi.NET:443<br><br> **Azure Germania:**<br> Management.microsoftazure.de:443 | Creazione/lettura/aggiornamento/eliminazione chiave archivio <br> Impostare i criteri di accesso per archivio chiave<br>Impostati per archivio chiave | Controllo dell'accesso basato sui ruoli Manager delle risorse Azure (RBAC) |
| Piano di dati | **Globale:**<br> &lt;Nome archivio&gt;. vault.azure.net:443<br><br> **Azure Cina:**<br> &lt;Nome archivio&gt;. vault.azure.cn:443<br><br> **Azure governo degli Stati Uniti:**<br> &lt;Nome archivio&gt;. vault.usgovcloudapi.net:443<br><br> **Azure Germania:**<br> &lt;Nome archivio&gt;. vault.microsoftazure.de:443 | Per le chiavi: Decrittografare, crittografare, UnwrapKey, WrapKey, verificare, eseguire l'accesso, ottenere, elenco, aggiornare, creare, importare, eliminare, Backup, ripristino<br><br> Per informazioni riservate: ottenere, elenco, Set, Elimina | Criterio di accesso chiave archivio|

La gestione dei dati e piano piano accedere ai controlli funzionano in modo indipendente. Ad esempio, se si desidera concedere l'accesso un'applicazione per utilizzare i tasti in un archivio di chiave, è sufficiente concedere autorizzazioni di accesso piano dati con i criteri di accesso archivio chiave e nessun accesso piano di gestione è necessaria per questa applicazione. E viceversa, se si vuole che un utente in grado di leggere le proprietà dell'archivio e tag, ma non ha accesso al chiavi, informazioni riservate o certificati, è possibile concedere l'utente '' l'accesso in lettura utilizzando RBAC e non l'accesso al piano di dati è necessario.

## <a name="management-plane-access-control"></a>Controllo di accesso di piano di gestione

Il piano di gestione è costituito da operazioni che interessano l'archivio di chiave. Ad esempio, è possibile creare o eliminare un archivio di chiave. È possibile ottenere un elenco degli archivi in una sottoscrizione. È possibile recuperare le proprietà dell'archivio chiave (ad esempio SKU, tag) e impostare i criteri di accesso da controllano gli utenti e applicazioni che possono accedere chiavi e informazioni riservate nell'archivio di chiave archivio chiave. Controllo di accesso di piano di gestione utilizza RBAC. Vedere l'elenco completo delle operazioni di archivio chiave che possono essere eseguite tramite il piano di gestione nella tabella nella sezione precedente. 

### <a name="role-based-access-control-rbac"></a>Controllo dell'accesso basato sui ruoli (RBAC)
Ogni abbonamento Azure ha un Azure Active Directory. Utenti, gruppi e le applicazioni dalla directory possono concedere l'accesso per gestire le risorse nella sottoscrizione Azure che utilizzano il modello di distribuzione di Manager delle risorse di Azure. Questo tipo di controllo dell'accesso viene considerato controllo dell'accesso basato sui ruoli (RBAC). Per gestire l'accesso, è possibile usare il [portale di Azure](https://portal.azure.com/), gli [Strumenti di Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o le [API REST di Azure Manager delle risorse](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Con il modello di gestione risorse di Azure, creare l'archivio chiave in delle risorse gruppo e controllare l'accesso al piano di gestione di questo archivio chiave con Azure Active Directory. Ad esempio, è possibile concedere agli utenti o una capacità di gruppo per gestire gli archivi chiavi in un determinato gruppo di risorse.

È possibile concedere l'accesso agli utenti, gruppi e le applicazioni in un ambito specifico mediante l'assegnazione di ruoli RBAC appropriati. Ad esempio, per concedere l'accesso a un utente per la gestione degli archivi chiavi assegnare un ruolo predefinito 'collaboratori chiave archivio' a questo utente in un ambito specifico. L'ambito in questo caso è una sottoscrizione, un gruppo di risorse o solo un archivio di chiave specifico. Un ruolo assegnato a livello di abbonamento si applica a tutti i gruppi di risorse e le risorse all'interno di tale sottoscrizione. Un ruolo assegnato il livello di gruppo di risorse si applica a tutte le risorse in tale gruppo di risorse. Un ruolo per una risorsa specifica si applica solo a quella risorsa. Esistono diversi ruoli predefiniti (vedere [RBAC: ruoli incorporati](../active-directory/role-based-access-built-in-roles.md)), e se i ruoli predefiniti non corrispondono alle proprie esigenze, è inoltre possibile definire i proprio ruoli.

>[AZURE.IMPORTANT] Tenere presente che se un utente ha collaboratori (RBAC) a un piano di gestione archivio chiave, semplicemente un'autorizzazione se stesso accedere al piano di dati, tramite l'impostazione chiave archivio criteri di accesso che controlla l'accesso al piano di dati. Di conseguenza, è consigliabile strettamente controllare chi ha accesso 'Collaboratore' per il chiavi archivi per assicurarsi che solo gli utenti autorizzati possono accedere e gestire archivi chiavi, le chiavi, informazioni riservate e certificati.

## <a name="data-plane-access-control"></a>Controllo dell'accesso piano dati

Il piano dati archivio chiave è costituito da operazioni che interessano gli oggetti di un insieme di credenziali, ad esempio chiavi, informazioni riservate e certificati.  Sono inclusi chiave operazioni, ad esempio la creano, importazione, aggiornamento, elenco, backup e ripristino tasti, operazioni di crittografia, ad esempio accesso, verificare, crittografare, decrittografare, a capo e annullare la disposizione del e impostare i contrassegni e altri attributi per le chiavi. Allo stesso modo, per informazioni riservate che includa, ottenere, impostare, elenco, eliminare.

Dati piano accedere mediante l'impostazione di criteri di accesso per un archivio di chiave. Un utente, gruppo o un'applicazione deve avere le autorizzazioni di collaborazione (RBAC) per il piano di gestione di un archivio chiave per la possibilità di impostare criteri di accesso per tale archivio chiave. Un utente, gruppo o applicazione possibile concedere l'accesso per eseguire operazioni specifiche per le informazioni riservate o chiavi in un archivio di chiave. archivio chiave supporta fino a 16 voci dei criteri di accesso per un archivio di chiave. Creare un gruppo di sicurezza di Azure Active Directory e aggiungere utenti a tale gruppo a cui concedere l'accesso ai piano dati a più utenti in un archivio chiave.

### <a name="key-vault-access-policies"></a>chiave archivio criteri di accesso

criteri di accesso archivio chiave concedono le autorizzazioni per chiavi, informazioni riservate e certificati separatamente. Ad esempio, è possibile assegnare un accesso utente a solo chiavi, ma nessuna autorizzazione per informazioni riservate. Tuttavia, le autorizzazioni per accedere a chiavi o informazioni riservate o certificati sono a livello di archivio. Criterio di accesso archivio chiave in altre parole, non supporta le autorizzazioni a livello di oggetto. È possibile utilizzare per impostare i criteri di accesso di un archivio chiave [portale Azure](https://portal.azure.com/), gli [Strumenti di Azure CLI](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)o [archivio chiave API REST di gestione](https://msdn.microsoft.com/library/azure/mt620024.aspx) .

>[AZURE.IMPORTANT] Si noti che i criteri di accesso archivio chiave si applicano a livello di archivio. Ad esempio quando un utente viene concessa l'autorizzazione per creare ed eliminare le chiavi, Anna può eseguire tali operazioni in tutte le chiavi in tale archivio chiave.

## <a name="example"></a>Esempio

Supponiamo che si sviluppa un'applicazione che utilizza un certificato per SSL, Azure dello spazio di archiviazione per l'archiviazione dei dati e si utilizza una chiave RSA 2048 bit per le operazioni di accesso. Supponiamo che l'applicazione è in esecuzione in una macchina virtuale (o un Set di scala macchine Virtuali). È possibile utilizzare un archivio di chiave per memorizzare tutte le informazioni riservate applicazione e consente di archiviare il certificato di avvio utilizzato dall'applicazione per eseguire l'autenticazione con Azure Active Directory archivio chiave.

Pertanto, di seguito è riportato un riepilogo delle chiavi e informazioni riservate a essere archiviata in un archivio di chiave.

- **Certificato SSL** - per SSL
- **Chiave di archiviazione** - utilizzata per ottenere accesso all'account di archiviazione
- **Chiave RSA 2048 bit** - utilizzato per le operazioni di accesso
- **Certificato avvio** - utilizzato per eseguire l'autenticazione di Azure Active Directory, per accedere all'archivio chiave per recuperare la chiave di archiviazione e utilizzare la chiave RSA per la firma.

A questo punto si soddisfano i responsabili della gestione, la distribuzione e il controllo dell'applicazione. In questo esempio verrà usato tre ruoli.

- **Il team di sicurezza** - si tratta in genere il personale IT da 'ufficio di compagnia (sicurezza direttore)' o equivalente, responsabile della custodia corretta delle informazioni riservate, ad esempio i certificati SSL, le chiavi RSA utilizzate per la firma, le stringhe di connessione per i database, i tasti di account di archiviazione.
- **Gli sviluppatori/operatori** - sono persone sviluppano l'applicazione e quindi distribuirlo in Azure. In genere non fanno parte del team di sicurezza e pertanto che non avrebbero accesso a tutti i dati riservati, ad esempio i certificati SSL, le chiavi RSA, ma che l'applicazione che distribuiscono abbia accesso agli utenti.
- **Controllori** - si tratta in genere un set di persone, isolate da sviluppatori e il personale IT generale diverso. Responsabilità consiste nell'esaminare corretto utilizzo e la manutenzione dei certificati, chiavi e così via e garantire la conformità con standard di protezione dei dati. 

Un altro ruolo che non rispetta l'ambito di questa applicazione, ma Ecco i contenuti pertinenti da menzionare e che sarà l'abbonamento (o il gruppo delle risorse) amministratore. L'amministratore di sottoscrizione di autorizzazioni di accesso iniziale per il team di protezione. Di seguito si presuppone che l'amministratore della sottoscrizione ha concesso l'accesso al team di protezione a un gruppo di risorse in cui tutte le risorse necessarie per risiedono questa applicazione.

Ora vediamo quali azioni su ogni ruolo esegue nel contesto dell'applicazione.

- **Team di protezione**
    - Creare archivi di chiave
    - Attiva la chiave archivio registrazione
    - Aggiungere le chiavi/informazioni riservate
    - Creazione di backup delle chiavi di emergenza
    - Impostare il criterio di accesso archivio chiave per concedere le autorizzazioni a utenti e applicazioni per eseguire operazioni specifiche
    - Periodicamente rullino tasti/informazioni riservate
- **Gli sviluppatori/operatori**
    - Ottenere riferimenti per eseguire l'avvio e certificati SSL (identificazioni personali), lo spazio di archiviazione chiave (segreto URI) e chiave (chiave URI) dal team di protezione di firma
    - Sviluppare e distribuire un'applicazione che accede a livello di programmazione chiavi e informazioni riservate
- **Revisori**
    - Esaminare i registri di utilizzo per confermare utilizzo chiave/segreto corretto e conformità con standard di protezione dei dati

A questo punto si vedranno ora le autorizzazioni di accesso da archivio chiave sono necessari per ogni ruolo (e l'applicazione) per eseguire le attività assegnate. 

| Ruolo utente    | Autorizzazioni di piano di gestione | Autorizzazioni di piano di dati |
|--------------|------------------------------|------------------------|
|Team di protezione|archivio chiave collaboratori|Chiavi: eseguire il backup, creare, eliminare, ottenere, importare, elenco, ripristinare <br> Informazioni riservate: tutti|
|Gli sviluppatori/operatore| archivio chiave distribuire autorizzazione in modo che le macchine virtuali distribuiscono possano recuperare informazioni riservate dall'archivio di chiave | Nessuno |
|Revisori| Nessuno | Chiavi: elenco<br>Informazioni riservate: elenco|
|Applicazione| Nessuno | Chiavi: accesso<br>Informazioni riservate: get |

>[AZURE.NOTE] Controllori necessitano elencare le autorizzazioni per chiavi e informazioni riservate in modo che controllano gli attributi di chiavi e informazioni riservate che non ha generati nei log, ad esempio tag, date di attivazione e scadenza.

Oltre a autorizzazione all'archivio chiave, tutti i tre ruoli anche necessitano l'accesso ad altre risorse. Ad esempio, per essere in grado di distribuire macchine virtuali (o Web App ecc.) Gli sviluppatori/operatori anche necessario l'accesso di 'Collaboratore' a questi tipi di risorse. Controllori necessario accesso in lettura per l'account di archiviazione in cui sono archiviati i registri archivio chiave.

Dal momento che lo stato attivo di questo articolo è la protezione dell'accesso per l'archivio chiave, è solo illustrare le sezioni pertinenti relative a questo argomento e ignorare i dettagli relativi alla distribuzione di certificati, l'accesso a livello di programmazione ecc chiavi e informazioni riservate. Questi dettagli sono già contemplati altrove. Distribuzione di certificati archiviati in archivio chiave alle macchine virtuali viene descritto in un [post di blog](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/), e [il codice di esempio](https://www.microsoft.com/download/details.aspx?id=45343) disponibile che illustra come utilizzare il certificato di avvio per eseguire l'autenticazione di Azure Active Directory per accedere all'archivio chiave.

È possibile concedere la maggior parte delle autorizzazioni di accesso tramite il portale di Azure, ma per concedere le autorizzazioni granulare potrebbe essere necessario usare PowerShell Azure o Azure CLI per ottenere il risultato desiderato. 

Si supponga frammenti di PowerShell seguenti:

- L'amministratore di Azure Active Directory ha creato i gruppi di sicurezza che rappresentano i tre ruoli, vale a dire Team di protezione di Contoso, Contoso App attrezzi, Contoso App Auditors. L'amministratore ha anche aggiunto gli utenti ai gruppi che appartengono.

- **ContosoAppRG** è il gruppo di risorse in cui si trovano tutte le risorse. **contosologstorage** è in cui sono archiviati i log. 

- Archivio chiave **ContosoKeyVault** e lo spazio di archiviazione account utilizzato per archivio chiave registri **contosologstorage** deve essere nello stesso percorso Azure


Prima di tutto l'amministratore di sottoscrizione assegna 'chiave archivio collaboratori' e ' amministratore di accesso utente ' ruoli al team di sicurezza. In questo modo il team di gestire l'accesso ad altre risorse e gestire gli archivi chiavi nel gruppo di risorse ContosoAppRG protezione.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Lo script seguente mostra come il team di protezione può creare un archivio chiave imposta la registrazione e impostare le autorizzazioni di accesso di altri ruoli e dell'applicazione. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionToKeys backup,create,delete,get,import,list,restore -PermissionToSecrets all

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devlopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $role

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionToKeys list -PermissionToSecrets list
```

Il ruolo personalizzato definito, è solo assegnare alla sottoscrizione in cui viene creato il gruppo di risorse ContosoAppRG. Se gli stessi ruoli personalizzati da utilizzare per altri progetti in altri abbonamenti, l'ambito potrebbe avere più sottoscrizioni aggiunte.

L'assegnazione di ruolo personalizzato per gli sviluppatori/operatori per l'autorizzazione "distribuire/azione" ambito è il gruppo di risorse. In questo modo solo macchine virtuali creati nel gruppo di risorse 'ContosoAppRG' ricevono informazioni riservate (certificato SSL e avvio della diapositiva). Non sarà possibile ottenere queste informazioni riservate, anche se erano URI segreta macchine virtuali creato da un membro del team di sviluppo e degli operatori in altro gruppo di risorse.

In questo esempio illustra un semplice scenario. Scenari reali potrebbero essere più complessi e potrebbe essere necessario modificare le autorizzazioni per l'archivio chiave in base alle proprie esigenze. Ad esempio, in questo esempio si presuppone che team sicurezza fornirà la chiave e segreti riferimenti (URI e identificazioni personali) tale team sviluppatori/operatori è necessario farvi riferimento nelle applicazioni. Di conseguenza, non è necessario l'accesso agli sviluppatori/operatori eventuali dati piano. Si noti inoltre che in questo esempio è incentrata su protezione l'archivio chiave. Simile considerazione per proteggere [le macchine virtuali](https://azure.microsoft.com/services/virtual-machines/security/), [gli account di archiviazione](../storage/storage-security-guide.md) e altre risorse Azure troppo.

>[AZURE.NOTE] Nota: Questo esempio mostra come chiave archivio access verrà bloccato verso il basso nell'ambiente di produzione. Gli sviluppatori devono avere le proprie sottoscrizioni o resourcegroup cui hanno autorizzazioni complete per gestire archivi, macchine virtuali di account di archiviazione in cui lo sviluppo dell'applicazione.


## <a name="resources"></a>Risorse

-   [Controllo dell'accesso basato sui ruoli Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    In questo articolo viene illustrato il controllo dell'accesso basato sui ruoli di Azure Active Directory e sul suo funzionamento.

-   [RBAC: Integrato in ruoli](../active-directory/role-based-access-built-in-roles.md)

    In questo articolo viene descritta tutti i ruoli predefiniti disponibili in RBAC.

-   [Informazioni sulla distribuzione classica e Manager delle risorse](../resource-manager-deployment-model.md)

    In questo articolo illustra la distribuzione di Manager delle risorse e modelli di distribuzione classica e illustrati i vantaggi dell'uso dei gruppi di Manager delle risorse e delle risorse

-    [Gestire il controllo dell'accesso basato sui ruoli con PowerShell Azure](../active-directory/role-based-access-control-manage-access-powershell.md)

     In questo articolo viene illustrato come gestire il controllo dell'accesso basato sui ruoli con PowerShell Azure

-   [Gestione di controllo dell'accesso basato sui ruoli con l'API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    In questo articolo viene illustrato come utilizzare l'API REST per gestire RBAC.

-   [Controllo dell'accesso basato sui ruoli per Microsoft Azure da Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Si tratta di un collegamento a un video su Channel 9 dalla conferenza Ignite MS 2015. In questa sessione si parla di accedere alle funzionalità di creazione di report in Azure e gestione ed esplorare consigliate per la protezione dell'accesso a Azure abbonamenti con Azure Active Directory.

-   [Autorizzare l'accesso alle applicazioni web mediante OAuth 2.0 e Azure Active Directory](../active-directory/active-directory-protocols-oauth-code.md)

    In questo articolo vengono illustrati completamento del flusso di OAuth 2.0 per l'autenticazione con Azure Active Directory.

-   [archivio chiave API REST di gestione](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    In questo documento è il riferimento per le API REST gestire l'archivio di chiave a livello di programmazione, inclusa l'impostazione di criteri di accesso chiave archivio.

-   [archivio chiave API REST](https://msdn.microsoft.com/library/azure/dn903609.aspx)

    Creare un collegamento all'archivio chiave documentazione di riferimento all'API REST.

-   [Controllo dell'accesso chiave](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

    Creare un collegamento alla documentazione di riferimento chiave accesso controllo.

-   [Controllo dell'accesso segreta](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

    Creare un collegamento alla documentazione di riferimento chiave accesso controllo.

-   [Impostare](https://msdn.microsoft.com/library/mt603625.aspx) e [rimuovere](https://msdn.microsoft.com/library/mt619427.aspx) criterio di accesso di archivio chiave tramite PowerShell

    Collega a fare riferimento alla documentazione per cmdlet di PowerShell per gestire i criteri di accesso chiave archivio.

## <a name="next-steps"></a>Passaggi successivi

Per un'esercitazione introduttiva recupero di un amministratore, vedere [Guida introduttiva di Azure archivio chiave](key-vault-get-started.md).

Per ulteriori informazioni sull'utilizzo della registrazione per archivio chiave, vedere [archivio chiave Azure registrazione](key-vault-logging.md).

Per ulteriori informazioni sull'utilizzo di chiavi e informazioni riservate con Azure archivio chiave, vedere [sulle chiavi e informazioni riservate](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Nel caso di domande su archivio chiave, visitare l' [archivio chiave Azure forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
