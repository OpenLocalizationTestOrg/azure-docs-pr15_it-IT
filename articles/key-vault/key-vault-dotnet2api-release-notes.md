<properties
   pageTitle="Principali note sulla versione di archivio .NET 2. x API | Microsoft Azure"
   description="Gli sviluppatori .NET utilizzeranno questa API al codice di Azure chiave archivio"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Archivio chiave Azure .NET 2.0 - note sulla versione e Guida di migrazione

Le note e linee guida seguenti sono per gli sviluppatori che utilizzano .NET Azure chiave archivio / libreria c#. In cambiamento rispetto alla 1.0 versione alla versione 2.0, un numero di aggiornamenti è stato apportato che richiedono l'esecuzione di migrazione nel codice in per poter trarre vantaggio da miglioramenti funzionali e le aggiunte, ad esempio il supporto di chiave archivio certificati di funzionalità.

Supporto di chiave archivio certificati offre per la gestione del x509 certificati e quanto segue:  

-   Consente il proprietario di un certificato creare un certificato tramite il processo di creazione di un archivio di chiave o l'importazione di un certificato esistente. Sono inclusi sia autofirmato e autorità di certificazione generato certificati.

- Consente il proprietario di un certificato chiave archivio implementare l'archiviazione sicura e la gestione di X509 certificati senza interazione con chiave privata.  

-   Consente il proprietario di un certificato creare un criterio che indirizza l'archivio di chiave per gestire il ciclo di vita di un certificato.  

-   Consente ai proprietari di certificati fornire informazioni di contatto per la notifica sugli eventi del ciclo di vita di scadenza e il rinnovo del certificato.  

-   Supporta il rinnovo automatico con selezionati emittenti - partner chiave Archivio X509 certificato provider / autorità di certificazione.
    - Nota: Non partner provider/autorità consentite anche ma non supporta la funzionalità di rinnovo automatico.


## <a name="net-support"></a>Supporto .NET
- **.NET 4.0** non è supportata dalla versione 2.0 di .NET Azure chiave archivio / libreria c#
- **.NET Core** supportata dalla versione 2.0 di .NET Azure chiave archivio / libreria c#

## <a name="namespaces"></a>Spazi dei nomi
- Spazio dei nomi per i **modelli** viene modificato da **Microsoft.Azure.KeyVault** a **Microsoft.Azure.KeyVault.Models**.
- Spazio dei nomi **Microsoft.Azure.KeyVault.Internal** viene eliminata.
- Lo spazio dei nomi di Azure SDK dipendenze vengono modificate da **Hyak.Common** e **Hyak.Common.Internals** **Microsoft.Rest** e **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Modifiche al tipo
- *Segreto* impostata su *SecretBundle*
- *Dizionario* impostata su *IDictionary*
- *Elenco<T>, string []* impostata su *IList<T> *
- *NextList* impostata su *NextPageLink*


## <a name="return-types"></a>Restituire tipi
- **KeyList** e **SecretList** restituirà *IPage<T> * anziché *ListKeysResponseMessage*
- Generato **BackupKeyAsync** restituirà *BackupKeyResult* contenente *valore* (backup blob). Prima che è stato incluso il metodo di calcolo e restituire soltanto il valore.

## <a name="exceptions"></a>Eccezioni
- *KeyVaultClientException* viene modificato in *KeyVaultErrorException*
- L'errore servizio viene modificata da *eccezione. Errore* all'eccezione *. Body.Error.Message*.
- Rimuovere informazioni supplementari dal messaggio di errore per **[JsonExtensionData]**.

## <a name="constructors"></a>Costruttori
- Invece di accettare un *HttpClient* come argomento costruttore, la costruzione accetta solo *HttpClientHandler* o *DelegatingHandler []*.



## <a name="downloaded-packages"></a>Pacchetti scaricati  
Durante l'elaborazione di un client dipendenza archivio chiave seguenti sono stati scaricati
#### <a name="previous-package-list"></a>Elenco di pacchetto precedente
- creare un pacchetto id="Hyak.Common" versione = "1.0.2" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Azure.Common" versione = "2.0.4" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Azure.Common.Dependencies" versione = "1.0.0" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Azure.KeyVault" versione = "1.0.0" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Bcl" versione = "1.1.9" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Bcl.Async" versione = "1.0.168" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Bcl.Build" versione = "1.0.14" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Net.Http" versione = "2.2.22" targetFramework = "net45"

#### <a name="current-package-list"></a>Elenco di pacchetto corrente
- creare un pacchetto id="Microsoft.Azure.KeyVault" versione = targetFramework "2.0.0-preview" = "net45"
- creare un pacchetto id="Microsoft.Rest.ClientRuntime" versione = "2.2.0" targetFramework = "net45"
- creare un pacchetto id="Microsoft.Rest.ClientRuntime.Azure" versione = "3.2.0" targetFramework = "net45"


## <a name="class-changes"></a>Modifiche di classe

- **UnixEpoch** classe è stata rimossa
- **Base64UrlConverter** classe è stato rinominato **Base64UrlJsonConverter**

## <a name="other-changes"></a>Altre modifiche

- Supporto per la configurazione dei criteri di tentativi di operazione KV in errori temporanei è stato aggiunto a questa versione dell'API.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- Per le operazioni che restituito un *insieme di credenziali*, il tipo restituito è stato un corso in cui è contenuta una proprietà di archivio. Il tipo restituito è ora *archivio*.
- *PermissionsToKeys* e *PermissionsToSecrets* sono ora *Permissions.Keys* e *Permissions.Secrets*
- Alcune delle modifiche tipi restituiti applicabili al controllo piano anche.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- Il pacchetto viene interrotto **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** per le operazioni di crittografia.
