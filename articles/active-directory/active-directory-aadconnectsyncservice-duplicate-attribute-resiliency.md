<properties
    pageTitle="Sulla resilienza attributo duplicato e la sincronizzazione delle identità | Microsoft Azure"
    description="Nuovo comportamento di come gestire gli oggetti con UPN o ProxyAddress conflitti durante la sincronizzazione della directory con Azure AD Connect."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>Sulla resilienza attributo duplicato e la sincronizzazione delle identità
Adattabilità degli attributi duplicati è una caratteristica di Azure Active Directory che consente di eliminare attrito dovuto **UserPrincipalName** e **ProxyAddress** conflitti durante l'esecuzione di uno degli strumenti di sincronizzazione di Microsoft.

Questi due attributi sono in genere necessari per essere univoco in tutto **utente**, **gruppo**o **contatto** oggetti in un determinato tenant di Azure Active Directory.

> [AZURE.NOTE] Solo gli utenti possono avere UPN.

Il nuovo comportamento con questa caratteristica consente è in parte cloud della pipeline di sincronizzazione, pertanto è client indipendente e i contenuti pertinenti per i prodotti di sincronizzazione di Microsoft inclusi Azure AD Connect, DirSync e MIM + connettore. Il termine generico "client di sincronizzazione" viene utilizzato in questo documento per rappresentare uno di questi prodotti.

## <a name="current-behavior"></a>Comportamento corrente
Se esiste un tentativo di effettuare il provisioning di un nuovo oggetto con un valore UPN o ProxyAddress che viola il vincolo di unicità, Azure Active Directory blocca tale oggetto non verrà creato. Analogamente, se un oggetto viene aggiornato con un UPN o ProxyAddress non univoco, l'aggiornamento ha esito negativo. Il tentativo di provisioning o aggiornamento ritentato dai client di sincronizzazione in fase di ogni ciclo di esportazione e continua a non riuscire fino a quando non viene risolto il conflitto. Un messaggio di posta elettronica report di errore viene generato al momento ogni tentativo e un messaggio di errore viene registrato dal client di sincronizzazione.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento con resilienza attributo duplicato
Anziché completamente la mancata provisioning o aggiornare un oggetto in un attributo duplicato, Azure Active Directory "quarantena" l'attributo duplicato che violi il vincolo di univocità. Se l'attributo è necessario per il provisioning, ad esempio UserPrincipalName, il servizio assegna un valore segnaposto. Il formato di questi valori temporanei  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***".  
Se l'attributo non è necessario, ad esempio un **ProxyAddress**, Azure Active Directory vengono messi in quarantena l'attributo conflitto e prosegue con la creazione di oggetti o aggiornamento.

Dopo la messa in quarantena l'attributo, informazioni sul conflitto vengono inviate tramite posta elettronica di report di errore stesso utilizzata il comportamento precedente. Tuttavia, queste informazioni viene visualizzata solo nella segnalazione errori una volta, quando si verifica la quarantena, non continuano a essere registrati in futuri messaggi di posta elettronica. Inoltre, poiché l'esportazione per l'oggetto ha avuto esito positivo, il client di sincronizzazione non registra un messaggio di errore e non si riconnette crea / operazione alla sincronizzazione successiva cicli di aggiornamento.

Per supportare questo comportamento è stato aggiunto un nuovo attributo per le classi di oggetti utente, gruppo e contatto:  
**DirSyncProvisioningErrors**

Si tratta di un attributo multivalore che viene usato per archiviare gli attributi in conflitto che violino il vincolo di univocità devono essere aggiunti normalmente. Attività timer in background è stata attivata di Azure Active Directory non viene eseguita ogni ora per la ricerca di conflitti attributo duplicato che sono stati risolti e verranno rimosse automaticamente gli attributi in questione dalla quarantena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Attivazione adattabilità attributo duplicato
Adattabilità degli attributi duplicati sarà il nuovo comportamento predefinito su tutti i tenant di Azure Active Directory. Sarà incluso per impostazione predefinita per tutti i tenant che appurato di sincronizzazione per la prima volta su 22 agosto 2016 o versione successiva. Tenant abilitati sincronizzazione prima di questa data avrà attivata la funzionalità in batch. Questa implementazione inizierà settembre 2016 e verrà inviata una notifica tramite posta elettronica contatto tecnico notifica del tenant ogni con una data specifica quando l'attivata.

Una volta sulla resilienza attributi duplicati è stata attivata non può essere disabilitata.

Per verificare se è attivata per il tenant, è possibile eseguire questa operazione, scaricare la versione più recente del modulo di Azure Active Directory PowerShell e in esecuzione:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Se si preferisce tempestiva abilitare la caratteristica prima che sia attivato per il tenant, è possibile eseguire questa operazione, scaricare la versione più recente del modulo di Azure Active Directory PowerShell e in esecuzione:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificazione degli oggetti con DirSyncProvisioningErrors
Esistono due metodi per identificare gli oggetti contenenti questi errori a causa di proprietà duplicati in conflitto, il portale di amministrazione di Office 365 e Azure Active Directory PowerShell. Esistono esteso a portale base contabile aggiuntiva in futuro.

### <a name="azure-active-directory-powershell"></a>PowerShell di Azure Active Directory
Per i cmdlet di PowerShell in questo argomento, si verifica quanto segue:

- Tutti i cmdlet seguenti sono tra maiuscole e minuscole.
- **-ErrorCategory PropertyConflict** deve sempre essere inclusi. Sono non disponibili attualmente altri tipi di **ErrorCategory**, ma questa operazione può essere esteso in futuro.

Prima di tutto, iniziare eseguendo **MsolService Connetti** e immettere le credenziali per un amministratore tenant.

Quindi, utilizzare il cmdlet e gli operatori seguenti per visualizzare gli errori in diversi modi:

1. [Visualizzare tutti](#see-all)

2. [In base al tipo di proprietà](#by-property-type)

3. [Valore in conflitto](#by-conflicting-value)

4. [Utilizzo di una stringa di ricerca](#using-a-string-search)

5. [Ordinati](#sorted)

6. [Una quantità limitata o tutti](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Visualizzare tutti
Una volta connessa, per visualizzare un elenco generale di provisioning attributo errori nel tenant di eseguire:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Un risultato simile al seguente:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>In base al tipo di proprietà
Per visualizzare errori in base al tipo di proprietà, aggiungere il contrassegno **- PropertyName** con l'argomento **UserPrincipalName** o **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

O

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Valore in conflitto
Per visualizzare errori relativi a una proprietà specifica aggiungere il contrassegno **- PropertyValue** (**- PropertyName** deve essere utilizzato anche quando si aggiunge il flag):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>Utilizzo di una stringa di ricerca
Per eseguire un'ampia stringa di ricerca utilizzare il contrassegno **- SearchString** . Può essere utilizzato in modo indipendente da tutti i contrassegni precedenti, ad eccezione delle **-ErrorCategory PropertyConflict**, che è sempre necessario:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Una quantità limitata o tutti
1. **MaxResults <Int> ** può essere utilizzato per limitare la query a un numero specifico di valori.

2. Per assicurarsi che tutti i risultati vengono recuperati nel caso in cui è presente un numero elevato di errori **tutti** utilizzabili.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portale di amministrazione di Office 365

È possibile visualizzare gli errori di sincronizzazione directory nell'interfaccia di amministrazione di Office 365. Il report nel portale di Office 365 Visualizza solo gli oggetti **utente** che dispone di questi errori. Non è visibile informazioni sui conflitti tra **i contatti**e **gruppi** .


![Utenti attivi] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utenti attivi")

Per istruzioni su come visualizzare gli errori di sincronizzazione directory nell'interfaccia di amministrazione di Office 365, vedere [identificare gli errori di sincronizzazione delle directory in Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Segnalazione errori di sincronizzazione identità
Quando viene gestito un oggetto con un conflitto di attributo duplicato con la nuova funzionalità è inclusa una notifica nella posta elettronica segnalazione errori di sincronizzazione identità standard che viene inviata per la notifica tecnico contattare per il tenant. Tuttavia, è una modifica importante questo comportamento. In passato, informazioni su un conflitto di attributo duplicato verrà incluso in ogni messaggio di errore successive fino a quando non è stato risolto il conflitto. Con la nuova funzionalità, la notifica di errore per un conflitto specificato visualizzata solo una volta - al momento in cui che viene messo in quarantena l'attributo in conflitto.

Ecco un esempio di è simile alla notifica tramite posta elettronica per un conflitto ProxyAddress:  
    ![Utenti attivi](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Risoluzione dei conflitti
Risoluzione dei problemi tattiche strategia e la risoluzione degli errori non devono essere diversi da quella degli attributi duplicati errori sono stati gestiti in passato. L'unica differenza è che l'attività timer introduce tramite tenant sul lato del servizio per aggiungere automaticamente l'attributo in questione l'oggetto una volta risolto il conflitto.

L'articolo seguente illustra varie strategie di risoluzione dei problemi: [duplicati o attributi non validi impediscono la sincronizzazione della directory in Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemi noti
Nessuno di questi problemi noti: riduzione del servizio o perdita di dati. Alcuni di essi sono estetici, gli altri utenti a causa standard "*pre-adattabilità*" gli errori di attributo duplicato generata invece di messa in quarantena l'attributo conflitto e un'altra causa alcuni errori richiedere la correzione manuale aggiuntiva.

**Comportamento di base:**

1. Oggetti con le configurazioni attributo specifico continueranno a ricevere gli errori di esportazione anziché gli attributi duplicati viene messo in quarantena.  
Per esempio:

    un. Creazione di nuovo utente in Active Directory con l'UPN di **Joe@contoso.com** e ProxyAddress**smtp:Joe@contoso.com**

    b. Le proprietà dell'oggetto sono in conflitto con un gruppo esistente, in cui è ProxyAddress **SMTP:Joe@contoso.com**.

    c. Dopo l'esportazione, viene generato un errore di **conflitto ProxyAddress** anziché affidare agli attributi di conflitto messo in quarantena. L'operazione viene ripetuta dopo ogni ciclo di sincronizzazione successiva, come sarebbe stato prima che la caratteristica di flessibilità è stata abilitata.

2. Se in locale vengono creati con lo stesso indirizzo SMTP due gruppi, uno non riesce a disposizione al primo tentativo con un errore **ProxyAddress** duplicata standard. Tuttavia, il valore duplicato sia correttamente in quarantena al ciclo di sincronizzazione successivo.

**Report del portale di office**:

1. Messaggio di errore dettagliato per due oggetti in un set di conflitto UPN è lo stesso. Indica che sono entrambe hanno il nome dell'entità modificato / messo in quarantena, mentre in realtà solo uno di essi qualsiasi tipo di dati modificato.

2. Messaggio di errore dettagliato per un conflitto UPN Mostra displayName non corretto per un utente che ha il nome dell'entità modificato/messo in quarantena. Per esempio:

    un. Sincronizza **l'utente** per primo con **UPN = User@contoso.com **.

    b. **Utente B** è tentato di sincronizzare la prossima con **UPN = User@contoso.com **.

    c. **Dell'utente B** UPN viene modificato in **User1234@contoso.onmicrosoft.com** e **User@contoso.com** viene aggiunto a **DirSyncProvisioningErrors**.

    d. Il messaggio di errore per **Utente B** deve indicare che **un utente** ha già **User@contoso.com** come un UPN, ma mostra displayName personale **Dell'utente B** .



**Segnalazione errori di sincronizzazione identità**:

Il collegamento per *la procedura risolvere il problema* non è corretto:  
    ![Utenti attivi](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

Deve puntare a [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Vedere anche

- [Sincronizzazione di Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)

- [Identificare gli errori di sincronizzazione della directory in Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
