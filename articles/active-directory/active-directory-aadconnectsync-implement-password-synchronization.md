<properties
    pageTitle="Implementare la sincronizzazione delle password con sincronizzazione di Azure AD Connect | Microsoft Azure"
    description="Vengono fornite informazioni sul funzionamento della sincronizzazione delle password e su come abilitarla."
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
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementare la sincronizzazione delle password con sincronizzazione di Azure AD Connect
In questo argomento vengono fornite le informazioni necessarie per sincronizzare le password utente da un server Active Directory locale (AD) a un tipo basato su cloud Azure Active Directory (Azure Active Directory).

## <a name="what-is-password-synchronization"></a>Che cos'è la sincronizzazione delle password
La probabilità che vengono bloccati da portare a termine a causa di una password dimenticata il lavoro correlato al numero di password diverse, è necessario ricordare. Le password più che necessario ricordare, maggiore la probabilità dimenticare uno. Domande e le chiamate sulla reimpostazione della password e altri problemi relativi alla password richiedono più risorse di supporto tecnico.

La sincronizzazione delle password è una funzionalità per sincronizzare le password degli utenti da Active Directory locale a un tipo basato su cloud Azure Active Directory (Azure Active Directory).
Questa caratteristica consente di accedere ai servizi di Azure Active Directory (ad esempio Office 365, Microsoft Intune, CRM Online e servizi di dominio Active Directory Azure) con la stessa password in uso per accedere a di Active Directory locale.

![Che cos'è Azure AD Connect](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Riducendo il numero di password che è necessario mantenere a un solo gli utenti, la sincronizzazione delle password consente di:

- Migliorare la produttività degli utenti
- Ridurre i costi di supporto tecnico  

Inoltre, se si sceglie di utilizzare [**la federazione con ADFS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), è possibile attivare la sincronizzazione delle password come una copia di backup in caso di errore infrastruttura ADFS.

La sincronizzazione delle password è un'estensione la caratteristica di sincronizzazione della directory implementata dal servizio di sincronizzazione Azure AD Connect. Per utilizzare la sincronizzazione delle password, nell'ambiente, è necessario:

- Connettere installa Azure Active Directory  
- Configurare la sincronizzazione tra le locale Active Directory e Azure Active Directory
- Attivare la sincronizzazione delle password

Per ulteriori informazioni, vedere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Per ulteriori informazioni sui servizi di dominio Active Directory che sono configurati per la sincronizzazione FIPS e la password, vedere [la sincronizzazione delle Password e FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Funzionamento della sincronizzazione delle password
Servizi di dominio Active Directory memorizza password in forma di una rappresentazione di valore hash della password utente effettivo. Un risultato di una funzione matematica unidirezionale (la "*algoritmo di hashing*") è un valore hash. Non esiste alcun metodo per ripristinare il risultato di una funzione unidirezionale per la versione di testo di una password. È possibile utilizzare un hash delle password per accedere alla rete locale.

Per sincronizzare la password, sincronizzazione di Azure AD Connect estrae l'hash password da Active Directory locale. Elaborazione di sicurezza aggiuntivo viene applicata con la password prima che viene sincronizzato con il servizio di autenticazione di Azure Active Directory. Le password vengono sincronizzate in ogni utente e in ordine cronologico.

Il flusso di dati effettivo del processo di sincronizzazione password è simile alla sincronizzazione dei dati utente, ad esempio DisplayName o gli indirizzi di posta elettronica. Le password vengono sincronizzate più frequentemente la finestra di sincronizzazione della directory standard per altri attributi. Il processo di sincronizzazione password viene eseguita ogni 2 minuti. Non è possibile modificare la frequenza di questo processo. Quando si sincronizza una password, sovrascrive password cloud esistente.

La prima volta, si abilita la caratteristica di sincronizzazione delle password, esegue una sincronizzazione iniziale della password di tutti gli utenti nell'ambito. Non è possibile definire in modo esplicito un sottoinsieme delle password degli utenti che si desidera sincronizzare.

Quando si modifica una password in locale, la password aggiornata viene sincronizzata, più spesso in pochi minuti.
La funzionalità di sincronizzazione password ritenta automaticamente tentativi di sincronizzazione non riuscita. Se si verifica un errore durante il tentativo di sincronizzazione di una password, un messaggio di errore viene registrato il Visualizzatore eventi.

La sincronizzazione di una password ha alcun effetto sull'utente connesso.
La sessione di servizi cloud corrente non immediatamente dipende dalla modifica della password sincronizzata che si verifica quando è stato effettuato l'accesso a un servizio cloud. Tuttavia, quando il servizio cloud richiede l'autenticazione nuovamente, è necessario fornire la nuova password.

> [AZURE.NOTE] La sincronizzazione delle password solo per l'utente di tipo di oggetto in Active Directory. Non è supportato per il tipo di oggetto iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Funzionamento della sincronizzazione delle password con servizi di dominio Active Directory di Azure
Utilizzare la funzionalità di sincronizzazione delle password per la sincronizzazione delle password locale per i [servizi di dominio Active Directory Azure](../active-directory-domain-services/active-directory-ds-overview.md). Questo scenario consente di servizi di dominio Active Directory Azure eseguire l'autenticazione gli utenti nel cloud con tutti i metodi disponibili nelle in locale Active Directory. L'esperienza di questo scenario è simile all'utilizzo di Active Directory strumento (migrazione) in un ambiente locale.

### <a name="security-considerations"></a>Considerazioni sulla protezione
Quando si sincronizzano le password, la versione di testo della password non viene esposto la funzionalità di sincronizzazione delle password, per Azure Active Directory o uno qualsiasi dei servizi associati.

Inoltre, non è necessario in Active Directory locale per archiviare la password in un formato reversibile. Un riepilogo dei hash di password Active Directory viene utilizzato per la trasmissione tra locale in Active Directory e Azure Active Directory. Riepilogo dei hash della password non è possibile usare per accedere alle risorse nel proprio ambiente locale.

### <a name="password-policy-considerations"></a>Considerazioni sui criteri password
Esistono due tipi di criteri di password che sono interessati abilitando la sincronizzazione delle password:

1. Criteri di complessità della password
2. Criteri di scadenza della password

**Criteri di complessità della password**  
Quando si attiva la sincronizzazione delle password, i criteri di complessità password in Active Directory locale sostituiscono i criteri di complessità nel cloud per gli utenti sincronizzati. È possibile usare tutte le password valide in Active Directory locale per accedere ai servizi di Azure Active Directory.

> [AZURE.NOTE] Le password degli utenti che vengono create direttamente nel cloud sono ancora soggetto a criteri password definiti nel cloud.

**Criteri di scadenza della password**  
Se un utente è l'ambito della sincronizzazione delle password, la password dell'account cloud è impostata su "*Scadenza*".
È possibile continuare ad accedere ai servizi cloud con una password sincronizzata scaduti nel proprio ambiente locale. La password cloud viene aggiornata la volta successiva che si modifica la password nell'ambiente locale.

### <a name="overwriting-synchronized-passwords"></a>Sovrascrivere la sincronizzazione password
Un amministratore manualmente reimpostare la password usando Windows PowerShell.

In questo caso, la nuova password sostituisce la password sincronizzata e tutti i criteri di password definiti nel cloud vengono applicati con la nuova password.

Se si cambia la password locale di nuovo la nuova password sincronizzate nel cloud e sostituisce la password manualmente aggiornata.

## <a name="enabling-password-synchronization"></a>Attivare la sincronizzazione delle password
La sincronizzazione delle password viene abilitata automaticamente durante l'installazione di Azure AD Connect, usare le **Impostazioni rapide**. Per ulteriori informazioni, vedere [Guida introduttiva a Azure AD Connect con le impostazioni express](./connect/active-directory-aadconnect-get-started-express.md).

Se si utilizzano impostazioni personalizzate durante l'installazione di Azure AD Connect, si attiva la sincronizzazione delle password nella pagina di accesso dell'utente. Per ulteriori informazioni, vedere [installazione personalizzata di Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

![Attivare la sincronizzazione delle password](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>La sincronizzazione delle password e FIPS
Se il server è stato bloccato verso il basso in base alle Federal Information Processing Standard (FIPS), MD5 è stata disattivata.

**Per attivare MD5 per la sincronizzazione delle password, procedere come segue:**

1. Passare a **Sync\Bin %programfiles%\Azure Active Directory**.
2. Aprire **miiserver.exe.config**.
3. Passare al nodo **configurazione/runtime** (alla fine del file).
4. Aggiungere il nodo seguente:`<enforceFIPSPolicy enabled="false"/>`
5. Salvare le modifiche.

Per riferimento, questo elemento di cattura è come dovrebbe risultare analoga alla:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Per informazioni sulla protezione e FIPS vedere [AAD la sincronizzazione delle Password, sulla crittografia e FIPS di conformità](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Risoluzione dei problemi di sincronizzazione delle password
Se la password non vengono sincronizzati come previsto, può essere per un sottoinsieme di utenti o per tutti gli utenti.

- Se si dispone di un problema con singoli oggetti, vedere [risoluzione dei problemi di un oggetto che non viene sincronizzato password](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Se si dispone di un problema nel punto in cui non vengono sincronizzata nessuna password, vedere [risoluzione dei problemi in cui non vengono sincronizzata nessuna password](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Risolvere i problemi di un oggetto che non viene sincronizzato password
È possibile risolvere i problemi di sincronizzazione password facilmente controllare lo stato di un oggetto.

Avviare nel **computer e utenti di Active Directory**. Trovare l'utente e verificare che il **che utente deve cambiare la password all'accesso successivo** è deselezionata.
![Password produttivi di Active Directory](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Se è selezionata, quindi chiedere all'utente di accedere e modificare la password. Le password temporanee non vengono sincronizzate con Azure Active Directory.

Se la correttezza di Active Directory, il passaggio successivo consiste nel seguire l'utente nel motore di sincronizzazione. Seguendo l'utente da Active Directory locale Azure Active Directory, è possibile controllare se è presente un errore descrittivo sull'oggetto.

1. Avviare il **[Gestore del servizio di sincronizzazione](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Fare clic su **connettori**.
3. Selezionare l'utente si trova in **Active Directory Connector** .
4. Selezionare **spazio connettore di ricerca**.
5. Individuare l'utente che si sta cercando.
6. Selezionare la scheda **derivazione** e accertarsi che sia presente almeno una regola di sincronizzazione visualizzato **La sincronizzazione delle Password** come **True**. Nella configurazione predefinita, il nome della regola di sincronizzazione è **In da Active Directory - AccountEnabled utente**.  
    ![Informazioni sulla derivazione dell'utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. [Seguire l'utente](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) attraverso il metaverse allo spazio di Azure Active Directory Connector. Oggetto spazio connettore deve disporre di una regola in uscita con **La sincronizzazione delle Password** impostato su **True**. Nella configurazione predefinita, il nome della regola di sincronizzazione è **a AAD - utente partecipa**.  
    ![Proprietà spazio connettore di un utente](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Per visualizzare i dettagli della sincronizzazione password dell'oggetto per la settimana scorsa, fare clic su **log....**.  
    ![Dettagli del Registro di oggetto](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

Nella colonna stato può avere i valori seguenti:

Stato | Descrizione
---- | -----
Successo | La password è stata sincronizzata.
FilteredByTarget | Password è impostata su **Cambiamento obbligatorio password all'accesso successivo**. Password non è stata sincronizzata.
NoTargetConnection | Nessun oggetto nel metaverse o nell'area connettore Azure Active Directory.
SourceConnectorNotPresent | Nessun oggetto trovato nell'area connettore Active Directory locale.
TargetNotExportedToDirectory | L'oggetto nell'area connettore Azure Active Directory non sono ancora stato esportato.
MigratedCheckDetailsForMoreInfo | Mov prima compilazione 1.0.9125.0 è stato creato e viene visualizzata nel suo stato legacy.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Risoluzione dei problemi in cui non vengono sincronizzata nessuna password
Iniziare eseguendo lo script nella sezione [ottenere lo stato delle impostazioni di sincronizzazione delle password](#get-the-status-of-password-sync-settings). Fornisce una panoramica della configurazione di sincronizzazione password.  
![Output dello script di PowerShell dalle impostazioni di sincronizzazione password](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Se la caratteristica non è abilitata in Azure Active Directory o se non è attivato lo stato di canale di sincronizzazione, quindi eseguire l'installazione guidata di connessione. Selezionare **le opzioni di sincronizzazione di personalizzazione** e deselezionare la sincronizzazione delle password. Questa modifica disattiva la caratteristica. Quindi eseguire nuovamente la procedura guidata e riattivare la sincronizzazione delle password. Eseguire lo script per verificare che la configurazione sia corretta.

Se lo script indica che non esiste alcun heartbeat, quindi eseguire lo script in [Trigger una sincronizzazione completa di tutte le password](#trigger-a-full-sync-of-all-passwords). Questo script può anche essere utilizzato per altri scenari in cui la configurazione è corretta ma non vengono sincronizzate le password.

#### <a name="get-the-status-of-password-sync-settings"></a>Visualizzare lo stato delle impostazioni di sincronizzazione delle password

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Avviare una sincronizzazione completa di tutte le password
È possibile attivare una sincronizzazione completa di tutte le password utilizzando il seguente script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Passaggi successivi

* [Azure Active Directory connettersi sincronizzazione: Le opzioni di personalizzazione di sincronizzazione](active-directory-aadconnectsync-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
