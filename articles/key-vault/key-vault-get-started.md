<properties
    pageTitle="Guida introduttiva di Azure chiave archivio | Microsoft Azure"
    description="Utilizzare questa esercitazione per un'introduzione a Azure chiave archivio per creare un contenitore di protezione avanzato in Azure, archiviare e gestire le chiavi di crittografia e informazioni riservate in Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>

# <a name="get-started-with-azure-key-vault"></a>Guida introduttiva di Azure chiave archivio #
Azure chiave archivio è disponibile nella maggior parte delle aree geografiche. Per ulteriori informazioni, vedere [chiave archivio prezzi pagina](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduzione  
Utilizzare questa esercitazione per un'introduzione a Azure chiave archivio per creare un contenitore di protezione avanzato (un archivio) in Azure, archiviare e gestire le chiavi di crittografia e informazioni riservate in Azure. Illustra il processo di uso di Azure PowerShell per creare un archivio che contiene una chiave o una password è quindi possibile utilizzare un'applicazione Azure. Quindi viene come un'applicazione può utilizzare tale chiave o una password.

**Durata stimata per completare:** 20 minuti

>[AZURE.NOTE]  In questa esercitazione non include istruzioni su come scrivere l'applicazione Azure che una delle operazioni include, vale a dire come autorizzare un'applicazione di utilizzare una chiave o segreta nell'archivio di chiave.
>
>In questa esercitazione utilizza Azure PowerShell. Per istruzioni interfaccia multipiattaforma della riga di comando, vedere [questa esercitazione equivalente](key-vault-manage-with-cli.md).

Per informazioni generali su Azure chiave archivio, vedere [Novità Azure chiave archivio?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- Una sottoscrizione a Microsoft Azure. Se non hai uno, è possibile iscriversi a un [account gratuito](https://azure.microsoft.com/pricing/free-trial/).
- PowerShell Azure, **versione minima di 1.1.0**. Per installare PowerShell Azure e associare all'abbonamento Azure, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md). Se già stato installato PowerShell Azure e non conoscono la versione dalla console di PowerShell di Azure, digitare `(Get-Module azure -ListAvailable).Version`. Quando si dispone di Azure PowerShell versione 0.9.1 tramite 0.9.8 installato, è possibile utilizzare questa esercitazione ancora con alcune modifiche minori. Ad esempio, è necessario utilizzare la `Switch-AzureMode AzureResourceManager` comando e alcuni dei comandi Azure chiave archivio sono stati modificati. Per un elenco dei cmdlet di chiave archivio per le versioni 0.9.1 tramite 0.9.8, vedere [Cmdlet di archivio di Azure chiave](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.98\).aspx). 
- Un'applicazione configurati per utilizzare il tasto o password creati in questa esercitazione. Un'applicazione di esempio è disponibile dall' [Area Download Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Per ulteriori informazioni, vedere il file Leggimi.


In questa esercitazione è progettata per gli utenti inesperti PowerShell di Azure, ma si presuppone che comprendere i concetti di base, ad esempio moduli, i cmdlet e sessioni. Per ulteriori informazioni, vedere [Guida introduttiva a Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Per ottenere informazioni dettagliate per tutti i cmdlet che viene visualizzato in questa esercitazione, utilizzare il cmdlet **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Ad esempio, per ottenere assistenza per il cmdlet **AzureRmAccount di accesso** , digitare:

    Get-Help Login-AzureRmAccount -Detailed

È anche possibile leggere le seguenti esercitazioni per acquisire familiarità con Gestione risorse di Azure in Azure PowerShell:

- [Come installare e configurare PowerShell Azure](../powershell-install-configure.md)
- [Utilizzo di PowerShell Azure con Gestione risorse](../powershell-azure-resource-manager.md)


## <a id="connect"></a>Connettersi alle sottoscrizioni ##

Avviare una sessione di PowerShell Azure e accedere al proprio account Azure con il comando seguente:  

    Login-AzureRmAccount 

Si noti che se si utilizza una specifica istanza di utilizzo di Azure, ad esempio, per enti pubblici Azure, ambiente il parametro - con questo comando. Per esempio:`Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Nella finestra del browser a comparsa, immettere il nome utente dell'account Azure e la password. PowerShell Azure Ottiene tutte le sottoscrizioni che sono associati a questo account e per impostazione predefinita, viene utilizzato il primo.

Se si hanno più abbonamenti e si desidera specificare uno specifico da utilizzare per Azure chiave archivio, digitare quanto segue per visualizzare le sottoscrizioni per l'account:

    Get-AzureRmSubscription

Per specificare l'abbonamento da utilizzare, digitare:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Per ulteriori informazioni sulla configurazione di Azure PowerShell, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).


## <a id="resource"></a>Creare un nuovo gruppo di risorse ##

Quando si utilizza Gestione risorse di Azure, tutte le risorse correlate vengono create all'interno di un gruppo di risorse. Si creerà un nuovo gruppo di risorse denominato **ContosoResourceGroup** per questa esercitazione:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Creare un archivio di chiave ##

Utilizzare il cmdlet [New-AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt603736\(v=azure.300\).aspx) per creare un archivio di chiave. Questo cmdlet sono previsti tre parametri obbligatori: un **nome gruppo di risorse**, un **nome archivio chiave**e la **posizione geografica**.

Ad esempio, se si usa il nome di archivio di **ContosoKeyVault**, il nome del gruppo di risorse di **ContosoResourceGroup**e la posizione **dell'Asia orientale**, digitare:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

L'output di questo cmdlet Visualizza le proprietà dell'archivio di chiave appena creata. Le due proprietà più importanti sono:

- **Nome archivio**: nell'esempio seguente viene **ContosoKeyVault**. Utilizzare questo nome per altri cmdlet chiave archivio.
- **URI archivio**: nell'esempio seguente viene https://contosokeyvault.vault.azure.net/. Le applicazioni che utilizzano l'archivio tramite l'API REST necessario utilizzare questo URI.

L'account Azure è autorizzato a eseguire le operazioni su questo tasto archivio. Nessun altro ancora, è.

>[AZURE.NOTE]  Se viene visualizzato l'errore **l'abbonamento non è registrato per utilizzare spazio dei nomi 'Microsoft.KeyVault'** quando si tenta di creare il nuovo archivio di chiave, eseguire `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"` e quindi eseguire di nuovo il comando nuovo AzureRmKeyVault. Per ulteriori informazioni, vedere [Registro AzureRmResourceProvider](https://msdn.microsoft.com/library/azure/mt759831\(v=azure.300\).aspx).
>

## <a id="add"></a>Aggiungere una chiave o segreta per l'archivio di chiave ##

Se si vuole Azure chiave archivio per creare una chiave protetto da software dell'utente, utilizzare il cmdlet [Aggiungi AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) e digitare quanto segue:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

Tuttavia, se si dispone di una chiave esistente protetto da software in un. File PFX salvato nell'unità C:\ in un file denominato softkey.pfx che si desidera caricare in Azure chiave archivio, digitare quanto segue per impostare la variabile **securepfxpwd** una password di **123** per il. File PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Digitare le operazioni seguenti per importare la chiave di. File PFX, che consente di proteggere la chiave da parte di software nel servizio di archivio di chiave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


È ora possibile fare riferimento questo tasto che è stato creato o caricamento di archivio di chiave Azure, utilizzando il relativo URI. Utilizzare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e utilizzare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere la versione specifica.  

Per visualizzare l'URI per questo tasto, digitare:

    $Key.key.kid

Per aggiungere un segreto all'archivio, che è una password denominata SQLPassword ed è il valore di Pa$ $w0rd all'archivio chiave Azure, innanzitutto convertire il valore di Pa$ $w0rd in una stringa protetta, digitare quanto segue:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Digitare quanto segue:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

È ora possibile fare riferimento questa password aggiunti all'archivio di chiave Azure, utilizzando il relativo URI. Utilizzare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e utilizzare **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere la versione specifica.

Per visualizzare l'URI per questo segreto, digitare:

    $secret.Id

Di seguito consente di visualizzare il tasto o segreto appena creato:

- Per visualizzare la chiave, digitare:`Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
- Per visualizzare il segreto, digitare:`Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

A questo punto, l'archivio chiave e chiave o segreto è pronto per l'utilizzo di applicazioni. È necessario autorizzare applicazioni usarle.  

## <a id="register"></a>Registrare un'applicazione di Azure Active Directory ##

Questo passaggio da completare in genere uno sviluppatore, in un altro computer. Non è specifico di Azure chiave archivio, ma è qui incluso per completezza.


>[AZURE.IMPORTANT] Per completare l'esercitazione, l'account, l'archivio e l'applicazione che verrà registrata in questo passaggio devono essere tutti nella stessa directory Azure.

Le applicazioni che utilizzano un archivio di chiave devono eseguire l'autenticazione tramite un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione prima necessario registrare l'applicazione nel loro Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:


- Un **ID applicazione** (noto anche come un ID Client) e la **chiave di autenticazione** (noto anche come il segreto condiviso). L'applicazione dovrà presentare entrambi questi valori di Azure Active Directory, per ottenere un token. Configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Dell'applicazione di esempio archivio chiave al proprietario dell'applicazione imposta questi valori nel file app.

Per registrare l'applicazione di Azure Active Directory:

1. Accedere al portale di classica Azure.
2. A sinistra, fare clic su **Active Directory**e quindi selezionare la directory in cui registrare l'applicazione. <br> <br> **Nota:** Selezionare la stessa directory che contiene la sottoscrizione di Azure con cui è stato creato l'archivio chiave. Se non si conosce la directory seguente è, fare clic su **Impostazioni**, identificare la sottoscrizione a cui è stato creato l'archivio chiave e prendere nota del nome della directory visualizzato nell'ultima colonna.

3. Fare clic su **applicazioni**. Se nessuna applicazione sono stati aggiunti alla directory, questa pagina vengono visualizzati solo il collegamento **Aggiungi un'App** . Fare clic sul collegamento o in alternativa, è possibile fare clic su **Aggiungi** nella barra dei comandi.
4.  Nella procedura guidata **Aggiungi applicazione** sul **cosa si desidera eseguire?** pagina fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**.
5.  Nella pagina **inviare commenti e suggerimenti sull'applicazione** , specificare un nome per l'applicazione e quindi selezionare **API WEB e/o di applicazione WEB** (impostazione predefinita). Fare clic sull'icona **successivo** .
6.  Nella pagina **App proprietà** specificare **l'URL di accesso via** e **APP ID URI** per l'applicazione web. Se l'applicazione non dispone di questi valori, è possibile renderle per questo passaggio (ad esempio, è possibile specificare http://test1.contoso.com per entrambe le finestre). Non è importante se sono presenti questi siti. Che cos'è importante è che l'app ID URI per ogni applicazione diverso per ogni applicazione nella directory. La directory utilizza questa stringa per identificare l'app.
7.  Fare clic sull'icona di **completamento** per salvare le modifiche nella procedura guidata.
8.  Nella pagina **Guida introduttiva** , fare clic su **Configura**.
9.  Scorrere fino alla sezione **tasti** , selezionare la durata e quindi fare clic su **Salva**. La pagina verrà aggiornata e verrà visualizzato un valore di chiave. È necessario configurare un'applicazione con il valore di chiave e il valore di **ID CLIENT** . Le istruzioni per la configurazione sono specifici dell'applicazione.
10. Copiare il valore di ID client da questa pagina, che verrà utilizzato nel passaggio successivo per impostare le autorizzazioni per l'archivio.

## <a id="authorize"></a>Autorizzare l'applicazione per utilizzare il tasto o segreto ##

Per autorizzare l'applicazione per accedere alla chiave o segreta nell'archivio di, utilizzare il cmdlet  [Set-AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625\(v=azure.300\).aspx) .

Ad esempio, se il nome di archivio **ContosoKeyVault** e sull'applicazione che si desidera autorizzare ha un ID client 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione decrittografare ed eseguire l'accesso con i tasti dell'archivio, eseguire le operazioni seguenti:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se si vuole autorizzare la stessa applicazione di leggere informazioni riservate nell'archivio, eseguire le operazioni seguenti:


    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Se si desidera utilizzare un modulo sicurezza hardware) ##

Per garanzia aggiunta, è possibile importare o generare chiavi nei moduli di sicurezza hardware (HSM) che non è mai lasciare il limite di modulo di sicurezza hardware. La HSM sono FIPS 140-2 livello 2 convalidati. Se questo requisito non è necessario, ignorare questa sezione e passare a [eliminare l'archivio di chiave e associato chiavi e informazioni riservate](#delete).

Per creare questi tasti HSM protetto, è necessario utilizzare il [livello di servizio Azure chiave archivio Premium per supportare le chiavi protetta modulo di sicurezza hardware](https://azure.microsoft.com/pricing/free-trial/). Si noti inoltre che questa funzionalità non è disponibile per la Cina Azure.


Quando si crea l'archivio di chiave, aggiungere il parametro **- SKU** :


    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



In questo archivio chiave, è possibile aggiungere chiavi protetto da software (come illustrato in precedenza) e modulo di sicurezza hardware protetto. Per creare una chiave protetta HSM, impostare il **-destinazione** parametro 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

È possibile utilizzare il comando seguente per importare una chiave da un. File PFX nel computer in uso. Questo comando Importa la chiave in HSM nel servizio di archivio di chiave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


Il comando successivo Importa un "anticipare la chiave" pacchetto (BYOK). Questo scenario consente di generare il tasto nel modulo di sicurezza hardware locale e trasferirla a HSM nel servizio di archivio di chiave, senza la chiave lasciando il limite di modulo di sicurezza hardware:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Per istruzioni più dettagliate su come generare il pacchetto BYOK, vedere [come generare e trasferimento protetto HSM tasti per l'archivio chiave Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Eliminare la chiave insieme di credenziali e chiavi associate e informazioni riservate ##

Se non è più necessario archivio chiave e il tasto o segreto in esso contenuti, è possibile eliminare l'archivio di chiave utilizzando il cmdlet [Rimuovi AzureRmKeyVault](https://msdn.microsoft.com/library/azure/mt619485\(v=azure.300\).aspx) :

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

In alternativa, è possibile eliminare un gruppo di risorse Azure intera, che include l'archivio di chiave e altre risorse inclusi in tale gruppo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Altri cmdlet di PowerShell Azure ##

Altri comandi che possono risultare utili per la gestione di archivio di Azure chiave:

- `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: Consente di ottenere una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate.
- `$Keys[0]`: Questo comando consente di visualizzare un elenco completo delle proprietà per la chiave specificata
- `Get-AzureKeyVaultSecret`: Questo comando elenca una visualizzazione tabulare di tutti i nomi segreti e le proprietà selezionate.
- `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: Esempio come rimuovere una chiave specifica.
- `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: Esempio come rimuovere un segreto specifico.


## <a id="next"></a>Passaggi successivi ##

Per un'esercitazione follow-up che utilizza Azure chiave archivio in un'applicazione web, vedere [Usare Azure chiave archivio da un'applicazione Web](key-vault-use-from-web-application.md).

Per utilizzare l'archivio chiave, vedere [La registrazione di Azure chiave archivio](key-vault-logging.md).

Per un elenco dei cmdlet di PowerShell Azure più recente di Azure chiave archivio, vedere [Cmdlet di archivio di Azure chiave](https://msdn.microsoft.com/library/azure/dn868052\(v=azure.300\).aspx). 
 

Per la programmazione di riferimenti, vedere [Guida per gli sviluppatori di Azure chiave archivio](key-vault-developers-guide.md).
