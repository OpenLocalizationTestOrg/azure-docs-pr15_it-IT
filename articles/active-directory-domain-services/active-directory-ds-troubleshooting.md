<properties
    pageTitle="Servizi di dominio Azure Active Directory: Guida alla risoluzione | Microsoft Azure"
    description="Guida alla risoluzione dei problemi per i servizi di dominio Active Directory di Azure"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---troubleshooting-guide"></a>Servizi di dominio Active Directory Azure - risoluzione dei problemi di Guida
In questo articolo vengono forniti alcuni suggerimenti sulla risoluzione dei problemi per problemi che possono verificarsi quando si configura o amministrazione dei servizi di dominio di Azure Active Directory (AD).


## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Non è possibile attivare servizi di dominio Active Directory di Azure per la directory di Azure Active Directory
In questa sezione consente di risolvere gli errori quando si tenta di attivare servizi di dominio Active Directory di Azure per la directory e non riesce o viene attivata o disattivata su 'Disattivato'.

Scegliere la procedura di risoluzione dei problemi che corrispondono al messaggio di errore che si verifica.

|**Messaggio di errore**|**Risoluzione**|
|---|:---|
|*Contoso100.com nome è già in uso nella rete. Specificare un nome che non sia in uso.*|[Conflitto relativo al nome di dominio della rete virtuale](active-directory-ds-troubleshooting.md#domain-name-conflict)
|*Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. Il servizio non dispone di autorizzazioni necessarie per l'applicazione denominata "Sync servizi Azure Active Directory dominio". Eliminare l'applicazione denominata "Azure Active Directory dominio servizi Sync" e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.*|[Servizi di dominio non dispone delle autorizzazioni necessarie per l'applicazione di sincronizzazione di servizi di dominio di Azure Active Directory](active-directory-ds-troubleshooting.md#inadequate-permissions)|
|*Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. L'applicazione Servizi di dominio nel tenant di Azure Active Directory non dispone delle autorizzazioni necessarie per attivare servizi di dominio. Eliminare l'applicazione con la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificatore dell'applicazione e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.*|[L'applicazione Servizi di dominio non è configurato correttamente nel tenant](active-directory-ds-troubleshooting.md#invalid-configuration)|
|*Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. L'applicazione di Microsoft Azure Active Directory viene disabilitato nel tenant di Azure Active Directory. Abilitare l'applicazione con la 00000002-0000-0000-c000-000000000000 identificatore dell'applicazione e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.*|[L'applicazione di Microsoft Graph viene disabilitato nel tenant di Azure Active Directory](active-directory-ds-troubleshooting.md#microsoft-graph-disabled)|


### <a name="domain-name-conflict"></a>Conflitto relativo al nome di dominio
**Messaggio di errore:**

*Contoso100.com nome è già in uso nella rete. Specificare un nome che non sia in uso.*

**Risoluzione:**

Assicurarsi che non si dispone di un dominio esistente con lo stesso nome di dominio disponibile in tale rete virtuale. Si supponga ad esempio, che si dispone di un dominio denominato 'contoso.com' già disponibili nella rete virtuale selezionata. In un secondo momento, si tenta di attivare un dominio gestito di servizi di dominio Active Directory Azure con lo stesso nome di dominio (vale a dire ' contoso.com') in rete virtuale. Si verifica un errore quando si tenta di attivare servizi di dominio Active Directory Azure.

L'errore è a causa dei conflitti di nome per il nome di dominio in rete virtuale. In questo caso, è necessario utilizzare un nome diverso per configurare il dominio gestito di servizi di dominio Active Directory Azure. In alternativa, è possibile annullare il provisioning del dominio esistente e quindi procedere per attivare servizi di dominio Active Directory Azure.


### <a name="inadequate-permissions"></a>Autorizzazioni sufficienti
**Messaggio di errore:**

*Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. Il servizio non dispone di autorizzazioni necessarie per l'applicazione denominata "Sync servizi Azure Active Directory dominio". Eliminare l'applicazione denominata "Azure Active Directory dominio servizi Sync" e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.*

**Risoluzione:**

Verificare se è un'applicazione con il nome "Azure Active Directory dominio servizi Sync" nella directory di Azure Active Directory. Se l'applicazione è presente, eliminarla e quindi abilitare nuovamente servizi di dominio Active Directory Azure.

Eseguire la procedura seguente per verificare la presenza dell'applicazione e l'eliminazione, se è presente l'applicazione:

  1. Passare al **portale classica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).
  2. Selezionare il nodo **Active Directory** nel riquadro sinistro.
  3. Selezionare il tenant di Azure Active Directory (directory) per il quale si desidera attivare servizi di dominio Active Directory Azure.
  4. Passare alla scheda **applicazioni** .
  5. Selezionare l'opzione di **applicazioni appartiene alla società** nell'elenco a discesa.
  6. Cercare un'applicazione di **sincronizzazione di servizi di dominio di Azure Active Directory**. Se è presente l'applicazione, procedere per eliminarla.
  7. Dopo aver eliminato l'applicazione, tenta di attivare servizi di dominio Active Directory Azure nuovamente.


### <a name="invalid-configuration"></a>Configurazione non valida
**Messaggio di errore:**

*Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. L'applicazione Servizi di dominio nel tenant di Azure Active Directory non dispone delle autorizzazioni necessarie per attivare servizi di dominio. Eliminare l'applicazione con la d87dcbc6-a371-462e-88e3-28ad15ec4e64 identificatore dell'applicazione e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.*

**Risoluzione:**

Verificare se è installata un'applicazione con il nome 'AzureActiveDirectoryDomainControllerServices' (con un identificatore dell'applicazione di d87dcbc6-a371-462e-88e3-28ad15ec4e64) nella directory di Azure Active Directory. Se è presente l'applicazione, è necessario eliminarla e quindi abilitare nuovamente servizi di dominio Active Directory Azure.

Usare il seguente script di PowerShell per trovare l'applicazione ed eliminarlo.

> [AZURE.NOTE] Questo script utilizza i cmdlet di **Azure Active Directory PowerShell versione 2** . Per un elenco completo di tutti i cmdlet disponibili e per scaricare il modulo, leggere la [documentazione di riferimento AzureAD PowerShell](https://msdn.microsoft.com/library/azure/mt757189.aspx).

```
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```
<br>

### <a name="microsoft-graph-disabled"></a>Microsoft Graph disattivato
**Messaggio di errore:**

Non è possibile abilitare Servizi di dominio nel tenant di Azure Active Directory. L'applicazione di Microsoft Azure Active Directory viene disabilitato nel tenant di Azure Active Directory. Abilitare l'applicazione con la 00000002-0000-0000-c000-000000000000 identificatore dell'applicazione e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.

**Risoluzione:**

Verificare se è stata disabilitata un'applicazione con l'identificatore 00000002-0000-0000-c000-000000000000. L'applicazione è l'applicazione di Microsoft Azure Active Directory e consente di accedere all'API grafico al tenant di Azure Active Directory. Azure servizi di dominio Active Directory deve questa applicazione deve essere abilitata per la sincronizzazione del tenant di Azure Active Directory per il dominio gestito.

Per correggere questo errore, abilitare questa applicazione e quindi si cerca di attivare servizi di dominio per il tenant di Azure Active Directory.


## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Non sarà possibile accedere al dominio gestito servizi di dominio Active Directory di Azure
Se uno o più utenti nel tenant di Azure Active Directory in grado di accedere al dominio gestito appena creato, eseguire le procedure di risoluzione dei problemi seguenti:

- **Procedi formato UPN:** Prova ad accedere utilizzando il formato UPN (ad esempio 'joeuser@contoso.com') anziché il formato SAMAccountName ('CONTOSO\joeuser'). SAMAccountName può essere generato automaticamente per gli utenti di cui il prefisso UPN è eccessiva o è diverso da quello di un altro utente nel dominio gestito. Il formato UPN necessariamente essere univoco all'interno di un tenant di Azure Active Directory.

> [AZURE.NOTE] È consigliabile utilizzare il formato UPN per accedere a servizi di dominio Active Directory Azure gestito dominio.

- Assicurarsi di avere [attivato la sincronizzazione delle password](active-directory-ds-getting-started-password-sync.md) secondo la procedura descritta in Guida introduttiva.

- **Account esterni:** Assicurarsi che l'account utente interessato non è un account esterno nel tenant di Azure Active Directory. Esempi di account esterni account Microsoft (ad esempio, 'joe@live.com') o agli account utente esterni directory di Azure Active Directory. Poiché i servizi di dominio Active Directory Azure non dispone delle credenziali per tale account utente, questi utenti non possono accedere al dominio gestito.

- **Sincronizzato account:** Se gli account utente interessato vengono sincronizzati da una directory locale, verificare che:
    - Si può distribuito o aggiornate alla [versione più recente consigliata di Azure AD Connect](active-directory-ds-getting-started-password-sync.md#install-or-update-azure-ad-connect).

    - È stato configurato Azure AD Connect per [eseguire una sincronizzazione completa](active-directory-ds-getting-started-password-sync.md).

    - A seconda delle dimensioni della directory, può richiedere un po' di tempo per gli account utente e credenziali hash sia disponibile in servizi di dominio Active Directory Azure. Assicurarsi che attendere per il tempo necessario prima di ritentare l'autenticazione (a seconda delle dimensioni della cartella - poche ore in un giorno o due per directory di grandi dimensioni).

    - Se il problema persiste dopo aver verificato i passaggi precedenti, provare a riavviare il servizio di sincronizzazione di Microsoft Azure Active Directory. Dal computer sincronizzazione un prompt di avvio ed eseguire i comandi seguenti:
      1. Interrompi netto "Microsoft Azure Active Sync"
      2. inizio netto "Microsoft Azure Active Sync"

- **Account basata solo su cloud**: se l'account utente interessato è un account utente basata solo su cloud, verificare che l'utente ha cambiato la propria password dopo l'abilitazione di servizi di dominio Active Directory Azure. Questa operazione comporta la credenziale hash necessari per i servizi di dominio Active Directory di Azure deve essere generato.


## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Gli utenti rimossi dal tenant di Azure Active Directory non vengono rimosse dal proprio dominio gestito
Azure Active Directory è protetto da eliminazioni accidentali degli oggetti utente. Quando si elimina un account utente da tenant di Azure Active Directory, l'oggetto utente corrispondente viene spostato nel Cestino. Quando l'operazione di eliminazione viene sincronizzata con il dominio gestito, imposta l'account utente corrispondente deve essere contrassegnato come disabilitato. Questa caratteristica consente di recuperare o annullare l'eliminazione di account utente in un secondo momento.

Per rimuovere completamente l'account utente dal proprio dominio gestito, eliminare definitivamente l'utente dal tenant di Azure Active Directory. Utilizzare il cmdlet di PowerShell Rimuovi-MsolUser con il '-RemoveFromRecycleBin' opzione, come descritto in questo [articolo di MSDN](https://msdn.microsoft.com/library/azure/dn194132.aspx).


## <a name="contact-us"></a>Contatta Microsoft
Contattare il team di prodotto di servizi di dominio di Azure Active Directory a [condividere commenti e suggerimenti o per il supporto] (attivo-directory-ds-contatto-us.md).
