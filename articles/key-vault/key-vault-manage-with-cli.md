<properties
    pageTitle="Gestire archivi chiave usa CLI | Microsoft Azure"
    description="Utilizzare questa esercitazione per automatizzare le attività comuni in archivio chiave tramite CLI"
    services="key-vault"
    documentationCenter=""
    authors="BrucePerlerMS"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/26/2016"
    ms.author="bruceper"/>

# <a name="manage-key-vault-using-cli"></a>Gestire archivi chiave usa CLI #
Azure chiave archivio è disponibile nella maggior parte delle aree geografiche. Per ulteriori informazioni, vedere [chiave archivio prezzi pagina](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introduzione  
Utilizzare questa esercitazione per un'introduzione a Azure chiave archivio per creare un contenitore di protezione avanzato (un archivio) in Azure, archiviare e gestire le chiavi di crittografia e informazioni riservate in Azure. Illustra il processo di utilizzo dell'interfaccia della riga di comando di Azure multipiattaforma per creare un archivio che contiene una chiave o una password è quindi possibile utilizzare un'applicazione Azure. Quindi viene come un'applicazione quindi possibile utilizzare tale chiave o una password.

**Durata stimata per completare:** 20 minuti

>[AZURE.NOTE]  In questa esercitazione non include istruzioni su come scrivere l'applicazione Azure che una delle operazioni include, che mostra come autorizzare un'applicazione di utilizzare una chiave o segreta nell'archivio di chiave.
>
>Al momento non è possibile configurare Azure chiave archivio nel portale di Azure. Utilizzare queste istruzioni interfaccia della riga di comando multipiattaforma. In alternativa, per le istruzioni di PowerShell di Azure, vedere [questa esercitazione equivalente](key-vault-get-started.md).

Per informazioni generali su Azure chiave archivio, vedere [Novità Azure chiave archivio?](key-vault-whatis.md)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- Una sottoscrizione a Microsoft Azure. Se non hai uno, è possibile iscriversi per una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial).
- Interfaccia della riga di comando versione 0.9.1 o versione successiva. Per installare la versione più recente e connettersi al proprio abbonamento Azure, vedere [installare e configurare l'interfaccia di Azure multipiattaforma della riga di comando](../xplat-cli-install.md).
- Un'applicazione configurati per utilizzare il tasto o password creati in questa esercitazione. Un'applicazione di esempio è disponibile dall' [Area Download Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Per ulteriori informazioni, vedere il file Leggimi.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Visualizzazione della Guida dell'interfaccia della riga di comando con Azure multipiattaforma

In questa esercitazione si presuppone che si ha familiarità con la riga di comando (Bash, terminale, il prompt dei comandi)

--Assistenza o -h parametro può essere utilizzato per visualizzare la Guida per i comandi specifici. In alternativa, di azure help [comando] [opzioni] formato può anche essere utilizzato per restituire le stesse informazioni. Ad esempio tutti i comandi seguenti restituiscono le stesse informazioni:

    azure account set --help

    azure account set -h

    azure help account set

In caso di dubbi sui parametri necessari per un comando, vedere la Guida relativa utilizzando - Guida, -h o azure help [comando].

È anche possibile leggere le seguenti esercitazioni per acquisire familiarità con Gestione risorse di Azure interfaccia della riga di comando in Azure multipiattaforma:

- [Come installare e configurare interfaccia riga di comando di Azure multipiattaforma](../xplat-cli-install.md)
- [Utilizzo dell'interfaccia della riga di comando di Azure multipiattaforma e su Azure Gestione risorse](../xplat-cli-azure-resource-manager.md)


## <a name="connect-to-your-subscriptions"></a>Connettersi alle sottoscrizioni

Per accedere tramite un account aziendale, utilizzare il comando seguente:

    azure login -u username -p password

o se si desidera connettersi digitando in modo interattivo

    azure login

>[AZURE.NOTE]  Il metodo di accesso funziona solo con account aziendale. Un account dell'organizzazione è un utente e su cui verrà gestito dall'organizzazione, definito nel tenant di Azure Active Directory dell'organizzazione.


Se non si dispone di un account dell'organizzazione e si utilizza un account Microsoft agli utenti di accedere al proprio abbonamento Azure, è possibile creare con facilità una eseguendo la procedura seguente.

1.  Accesso all'account di accesso al [Portale di gestione Azure](https://manage.windowsazure.com/)e fare clic su Active Directory.
2.  Se è presente alcuna directory, selezionare Crea directory e fornire le informazioni richieste.
3.  Selezionare la directory e aggiungere un nuovo utente. Il nuovo utente è un account dell'organizzazione. Durante la creazione dell'utente, verranno fornite sia un indirizzo di posta elettronica per l'utente e una password temporanea. Salvare queste informazioni come viene utilizzato in un altro passaggio.
4.  Dal portale, selezionare impostazioni e quindi selezionare gli amministratori. Selezionare Aggiungi e aggiungere il nuovo utente come amministratore condivisa. In questo modo l'account dell'organizzazione gestire l'abbonamento Azure.
5.  Infine, uscire dal portale Azure e quindi eseguire nuovamente l'accesso usando il nuovo account dell'organizzazione. Se si tratta di registrazione di tempo prima con questo account, verrà richiesto di cambiare la password.

Per ulteriori informazioni sull'utilizzo di un account dell'organizzazione con Microsoft Azure, vedere [iscriversi a Microsoft Azure come un'organizzazione](../active-directory/sign-up-organization.md).

Se si hanno più abbonamenti e si desidera specificare uno specifico da utilizzare per Azure chiave archivio, digitare quanto segue per visualizzare le sottoscrizioni per l'account:

    azure account list

Per specificare l'abbonamento da utilizzare, digitare:

    azure account set <subscription name>

Per ulteriori informazioni sulla configurazione dell'interfaccia della riga di comando di Azure multipiattaforma, vedere [come installare e configurare interfaccia della riga di comando Azure multipiattaforma](../xplat-cli-install.md).


## <a name="switch-to-using-azure-resource-manager"></a>Passare a Gestione risorse di Azure

Archivio chiave richiede Gestione risorse di Azure, quindi digitare le operazioni seguenti per passare alla modalità di gestione di risorse Azure:

    azure config mode arm

## <a name="create-a-new-resource-group"></a>Creare un nuovo gruppo di risorse

Quando si utilizza Gestione risorse di Azure, tutte le risorse correlate vengono create all'interno di un gruppo di risorse. Si creerà un nuovo gruppo di risorse 'ContosoResourceGroup' per questa esercitazione.

    azure group create 'ContosoResourceGroup' 'East Asia'

Il primo parametro è nome gruppo di risorse e il secondo parametro è il percorso. Per posizione, utilizzare il comando `azure location list` per identificare come specificare un percorso alternativo a quello in questo esempio. Per ulteriori informazioni, digitare:`azure help location`

## <a name="register-the-key-vault-resource-provider"></a>Registrare il provider di risorse chiave archivio
Assicurarsi che sia registrato il provider di risorse di archivio di chiave nell'abbonamento:

`azure provider register Microsoft.KeyVault`

Solo deve essere eseguita una sola volta per ogni abbonamento.


## <a name="create-a-key-vault"></a>Creare un archivio di chiave

Utilizzare la `azure keyvault create` comando per creare un archivio di chiave. Questo script sono previsti tre parametri obbligatori: il nome di un gruppo di risorse, un nome di archivio chiave e l'area geografica.

Ad esempio, se si usa il nome di archivio di ContosoKeyVault, il nome del gruppo di risorse di ContosoResourceGroup e la posizione dell'Asia orientale, digitare:

    azure keyvault create --vault-name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'

L'output del comando Visualizza le proprietà dell'archivio di chiave appena creata. Le due proprietà più importanti sono:

- **Nome**: nell'esempio seguente viene ContosoKeyVault. Utilizzare questo nome per altri cmdlet chiave archivio.
- **vaultUri**: nell'esempio seguente viene https://contosokeyvault.vault.azure.net. Le applicazioni che utilizzano l'archivio tramite l'API REST necessario utilizzare questo URI.

L'account Azure è autorizzato a eseguire le operazioni su questo tasto archivio. Nessun altro ancora, è.


## <a name="add-a-key-or-secret-to-the-key-vault"></a>Aggiungere una chiave o segreta per l'archivio di chiave

Se si vuole Azure chiave archivio per creare una chiave protetto da software dell'utente, utilizzare la `azure key create` comando e digitare quanto segue:

    azure keyvault key create --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --destination software

Tuttavia, se si dispone di una chiave esistente in un file di .pem salvato come file locale in un file denominato softkey.pem che si desidera caricare in Azure chiave archivio, digitare quanto segue per importare la chiave di. File PEM, che consente di proteggere la chiave da parte di software nel servizio di archivio di chiave:

    azure keyvault key import --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey' --pem-file './softkey.pem' --password 'PaSSWORD' --destination software

È ora possibile fare riferimento la chiave che è stato creato o caricamento di archivio di chiave Azure, utilizzando il relativo URI. Utilizzare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** per ottenere sempre la versione corrente e utilizzare **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** per ottenere la versione specifica.

Per aggiungere un segreto all'archivio, che corrisponde una password denominata SQLPassword e con il valore di Pa$ $w0rd in Azure chiave archivio, digitare quanto segue:

    azure keyvault secret set --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword' --value 'Pa$$w0rd'

È ora possibile fare riferimento questa password aggiunti all'archivio di chiave Azure, utilizzando il relativo URI. Utilizzare **https://ContosoVault.vault.azure.net/secrets/SQLPassword** per ottenere sempre la versione corrente e utilizzare **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** per ottenere la versione specifica.

Di seguito consente di visualizzare il tasto o segreto appena creato:

- Per visualizzare la chiave, digitare:`azure keyvault key list --vault-name 'ContosoKeyVault'`
- Per visualizzare il segreto, digitare:`azure keyvault secret list --vault-name 'ContosoKeyVault'`


## <a name="register-an-application-with-azure-active-directory"></a>Registrare un'applicazione di Azure Active Directory

Questo passaggio da completare in genere uno sviluppatore, in un altro computer. Non è specifico di Azure chiave archivio, ma è incluso in questo caso, per completezza.


>[AZURE.IMPORTANT] Per completare l'esercitazione, l'account, l'archivio e l'applicazione che verrà registrata in questo passaggio devono essere tutti nella stessa directory Azure.

Le applicazioni che utilizzano un archivio di chiave devono eseguire l'autenticazione tramite un token di Azure Active Directory. A tale scopo, il proprietario dell'applicazione prima necessario registrare l'applicazione nel loro Azure Active Directory. Al termine della registrazione, il proprietario dell'applicazione ottiene i valori seguenti:


- Un **ID applicazione** (noto anche come un ID Client) e la **chiave di autenticazione** (noto anche come il segreto condiviso). L'applicazione dovrà presentare entrambi i valori di Azure Active Directory, per ottenere un token. Configurazione dell'applicazione per eseguire questa operazione dipende dall'applicazione. Dell'applicazione di esempio archivio chiave al proprietario dell'applicazione imposta questi valori nel file app.



Per registrare l'applicazione di Azure Active Directory:

1. Accedere al portale di Azure.
2. A sinistra, fare clic su **Active Directory**e quindi selezionare la directory in cui registrare l'applicazione. <br> <br> Nota: È necessario selezionare la stessa directory che contiene la sottoscrizione di Azure con cui è stato creato l'archivio chiave. Se non si conosce la directory seguente è, fare clic su **Impostazioni**, identificare la sottoscrizione a cui è stato creato l'archivio chiave e prendere nota del nome della directory visualizzato nell'ultima colonna.

3. Fare clic su **applicazioni**. Se nessuna applicazione sono stati aggiunti alla directory, questa pagina verrà visualizzato solo il collegamento **Aggiungi un'App** . Fare clic sul collegamento o in alternativa, è possibile fare clic su **Aggiungi** nella barra dei comandi.
4.  Nella procedura guidata **Aggiungi applicazione** sul **cosa si desidera eseguire?** pagina fare clic su **Aggiungi un'applicazione di sviluppo di organizzazione**.
5.  Nella pagina **inviare commenti e suggerimenti sull'applicazione** specificare un nome per l'applicazione e selezionare **API WEB e/o di applicazione WEB** (impostazione predefinita). Fare clic sull'icona successivo.
6.  Nella pagina **App proprietà** specificare **l'URL di accesso via** e **APP ID URI** per l'applicazione web. Se l'applicazione non dispone di questi valori, è possibile renderle per questo passaggio (ad esempio, è possibile specificare http://test1.contoso.com per entrambe le finestre). Non è importante se sono presenti questi siti; che cos'è importante è che l'app ID URI per ogni applicazione diverso per ogni applicazione nella directory. La directory utilizza questa stringa per identificare l'app.
7.  Fare clic sull'icona di completamento per salvare le modifiche nella procedura guidata.
8.  Nella pagina Guida introduttiva, fare clic su **Configura**.
9.  Scorrere fino alla sezione **tasti** , selezionare la durata e quindi fare clic su **Salva**. La pagina verrà aggiornata e verrà visualizzato un valore di chiave. È necessario configurare un'applicazione con il valore di chiave e il valore di **ID CLIENT** . (Le istruzioni per la configurazione sarà specifici dell'applicazione.)
10. Copiare il valore di ID client da questa pagina, che verrà utilizzato nel passaggio successivo per impostare le autorizzazioni per l'archivio.




## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizzare l'applicazione per utilizzare il tasto o segreto

Per autorizzare l'applicazione per accedere alla chiave o segreta nell'archivio di, utilizzare la `azure keyvault set-policy` comando.

Ad esempio, se il nome di archivio è ContosoKeyVault e l'applicazione che si desidera autorizzare dispone di un ID client di 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e si vuole autorizzare l'applicazione decrittografare ed eseguire l'accesso con i tasti dell'archivio, quindi eseguire le operazioni seguenti:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-keys '["decrypt","sign"]'

>[AZURE.NOTE] Se si esegue su prompt dei comandi di Windows, si deve sostituire virgolette doppie e anche di escape le virgolette doppie interne. Ad esempio: "[\"decrittografare\",\"accesso\"]".

Se si vuole autorizzare la stessa applicazione di leggere informazioni riservate nell'archivio, eseguire le operazioni seguenti:

    azure keyvault set-policy --vault-name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --perms-to-secrets '["get"]'

## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se si desidera utilizzare un modulo sicurezza hardware) ##

Per garanzia aggiunta, è possibile importare o generare chiavi nei moduli di sicurezza hardware (HSM) che non è mai lasciare il limite di modulo di sicurezza hardware. La HSM sono FIPS 140-2 livello 2 convalidati. Se questo requisito non è necessario, ignorare questa sezione e passare a [eliminare l'archivio di chiave e associato chiavi e informazioni riservate](#delete-the-key-vault-and-associated-keys-and-secrets).

Per creare questi tasti HSM protetto, è necessario disporre di una sottoscrizione di archivio che supporta le chiavi protetta modulo di sicurezza hardware.

Quando si crea la keyvault, aggiungere il parametro 'sku':

    azure azure keyvault create --vault-name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'

In questo archivio, è possibile aggiungere chiavi protetto da software (come illustrato in precedenza) e modulo di sicurezza hardware protetto. Per creare una chiave protetta HSM, impostare il parametro di destinazione per 'HSM':

    azure keyvault key create --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --destination 'HSM'

È possibile utilizzare il comando seguente per importare una chiave da un file .pem nel computer in uso. Questo comando Importa la chiave in HSM nel servizio di archivio di chiave:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --destination 'HSM' --password 'PaSSWORD'

Il comando successivo Importa un "anticipare la chiave" pacchetto (BYOK). Consente di generare il tasto nel modulo di sicurezza hardware locale e trasferirla a HSM nel servizio di archivio di chiave, senza la chiave lasciando il limite di modulo di sicurezza hardware:

    azure keyvault key import --vault-name 'ContosoKeyVaultHSM' --key-name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --destination 'HSM'

Per istruzioni più dettagliate su come generare il pacchetto BYOK, informazioni su [come utilizzare i tasti HSM-Protected con Azure chiave archivio](key-vault-hsm-protected-keys.md).


## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Eliminare la chiave insieme di credenziali e chiavi associate e informazioni riservate

Se non è più necessario archivio chiave e il tasto o segreto in esso contenuti, è possibile eliminare l'archivio di chiave tramite il comando Elimina keyvault azure:

    azure keyvault delete --vault-name 'ContosoKeyVault'

In alternativa, è possibile eliminare un gruppo di risorse Azure intera, che include l'archivio di chiave e altre risorse inclusi in tale gruppo:

    azure group delete --name 'ContosoResourceGroup'


## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Altri comandi dell'interfaccia della riga di comando di Azure multipiattaforma

Altri comandi che si potrebbe utili per la gestione di Azure chiave archivio.

Questo comando elenca una visualizzazione tabulare di tutte le chiavi e le proprietà selezionate:

    azure keyvault key list --vault-name 'ContosoKeyVault'

Questo comando consente di visualizzare un elenco completo delle proprietà per la chiave specificata:

    azure keyvault key show --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Questo comando elenca una visualizzazione tabulare di tutti i nomi segreti e le proprietà selezionate:

    azure keyvault secret list --vault-name 'ContosoKeyVault'

Ecco un esempio di come rimuovere un tasto specifico:

    azure keyvault key delete --vault-name 'ContosoKeyVault' --key-name 'ContosoFirstKey'

Ecco un esempio di come rimuovere un segreto specifico:

    azure keyvault secret delete --vault-name 'ContosoKeyVault' --secret-name 'SQLPassword'


## <a name="next-steps"></a>Passaggi successivi

Per la programmazione di riferimenti, vedere [Guida per gli sviluppatori di Azure chiave archivio](key-vault-developers-guide.md).
