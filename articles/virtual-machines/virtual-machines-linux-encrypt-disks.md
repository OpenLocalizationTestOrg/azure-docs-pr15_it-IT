<properties
   pageTitle="Crittografare dischi su un VM Linux | Microsoft Azure"
   description="Come crittografare dischi su un VM Linux utilizzando CLI Azure e il modello di distribuzione di Manager delle risorse"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="10/11/2016"
   ms.author="iainfou"/>

# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli"></a>Crittografare dischi su un VM Linux usa CLI Azure
Per la protezione avanzata macchine () e conformità, è possono crittografare dischi virtuali in Azure inattivi. Dischi vengono crittografati utilizzando chiavi di crittografia che sono protetti in un archivio di chiave Azure. Si controllano le chiavi di crittografia e possibile controllare il loro utilizzo. In questo articolo viene illustrato come crittografare dischi virtuali in una VM Linux utilizzando CLI Azure e il modello di distribuzione di Manager delle risorse.


## <a name="quick-commands"></a>Comandi rapidi
Se è necessario eseguire rapidamente attività, i seguenti dettagli della sezione della base comandi per crittografare dischi virtuali in di una macchina virtuale. Ulteriori informazioni e il contesto di ogni passaggio sono disponibili il resto del documento, [iniziare qui](#overview-of-disk-encryption).

È necessario [CLI Azure più recente](../xplat-cli-install.md) installato e registrato con la modalità di gestione risorse come indicato di seguito:

```
azure config mode arm
```

Nell'esempio seguente, sostituire i nomi dei parametri di esempio con valori personalizzati. Includere i nomi dei parametri di esempio `myResourceGroup`, `myKeyVault`, e `myVM`.

Prima di tutto, attivare il provider di Azure chiave archivio all'interno di abbonamento Azure e creare un gruppo di risorse. Nell'esempio seguente viene creato il nome di un gruppo di risorse `myResourceGroup` nel `WestUS` percorso:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Creare un archivio di chiave Azure. Nell'esempio seguente viene creato un archivio di chiave denominato `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Creare una chiave di crittografia nell'archivio tasto e abilitarla per la crittografia del disco. Nell'esempio seguente viene creata una chiave denominata `myKey`:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Creare un endpoint con Azure Active Directory per gestire l'autenticazione e lo scambio di crittografia dall'archivio di chiave. Il `--home-page` e `--identifier-uris` non è necessario essere indirizzo routing effettivo. Per il massimo livello di sicurezza, informazioni riservate client devono essere utilizzati anziché password. CLI Azure attualmente non è possibile generare informazioni riservate client. Informazioni riservate client possono essere generati solo nel portale di Azure. Nell'esempio seguente viene creato un endpoint di Azure Active Directory denominato `myAADApp` e viene utilizzata una password di `myPassword`. Specificare la propria password come indicato di seguito:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Nota la `applicationId` illustrato nell'output rispetto al comando precedente. L'ID dell'applicazione viene utilizzato nei passaggi seguenti:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Aggiungere un disco dati a una macchina virtuale esistente. Nell'esempio seguente viene aggiunto un disco dati a una macchina virtuale denominata `myVM`:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Esaminare i dettagli per l'archivio di chiave e la chiave che è stato creato. È necessario chiave archivio ID, URI e chiave URL nel passaggio finale. Nell'esempio seguente vengono esaminati i dettagli di un archivio di chiave denominato `myKeyVault` e chiave denominata `myKey`:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crittografare i dischi come indicato di seguito, l'immissione di nomi parametro nell'intera:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

CLI Azure non fornisce gli errori dettagliati durante il processo di crittografia. Per ulteriori informazioni sulla risoluzione dei problemi, vedere `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Il comando precedente dispone di numerose variabili e non vengono molto indicazione di perché il processo non riesce, un esempio di comando completo dovrebbero essere come indicato di seguito:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Infine, esaminare lo stato di crittografia per confermare che dischi virtuali a questo punto sono state crittografate. Nell'esempio seguente controlla lo stato di una macchina virtuale denominata `myVM` nel `myResourceGroup` gruppo risorse:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Panoramica di crittografia disco
Dischi virtuali in macchine virtuali Linux vengono crittografati inattivi utilizzando [crittografia data mining](https://wikipedia.org/wiki/Dm-crypt). È gratuito per la crittografia dei dischi virtuali in Azure. Chiavi di crittografia sono archiviate nell'archivio di chiave Azure utilizzando software di protezione oppure è possibile importare o generare le chiavi nei moduli di sicurezza Hardware (HSM) Certificate a FIPS 140-2 standard di livello 2. Si mantenere il controllo di questi tasti crittografico e controlla il loro utilizzo. Questi tasti crittografico vengono utilizzati per crittografare e decrittografare dischi virtuali associati a di una macchina virtuale. Un endpoint di Azure Active Directory offre un meccanismo protetto per il rilascio di questi tasti crittografico come macchine virtuali sono basati su attivato e disattivato.

Il processo per la crittografia una macchina virtuale è come indicato di seguito:

1. Creare una chiave di crittografia in un archivio di chiave Azure.
2. Configurare la chiave di crittografia per poter essere utilizzato per la crittografia dei dischi.
3. Per leggere la chiave di crittografia dall'archivio di chiave di Azure, creare un endpoint con Azure Active Directory con le autorizzazioni appropriate.
4. Eseguire il comando per crittografare i dischi virtuali, specificando l'endpoint di Azure Active Directory e chiave di crittografia appropriata da utilizzare.
5. L'endpoint di Azure Active Directory richiede la chiave di crittografia necessaria Azure chiave archivio.
6. Dischi virtuali vengono crittografati utilizzando la chiave di crittografia fornita.


## <a name="supporting-services-and-encryption-process"></a>Processo di crittografia e i servizi di supporto
La crittografia del si basa sui componenti aggiuntivi seguenti:

- **Azure chiave archivio** - utilizzato per proteggere le chiavi di crittografia e informazioni riservate utilizzati per il processo di crittografia/decrittografia disco. 
  - Se disponibile, è possibile utilizzare un archivio di chiave Azure esistente. Non è necessario dedicare un archivio di chiave per la crittografia dischi.
  - Per separare i limiti amministrativi e visibilità chiave, è possibile creare un archivio di chiave dedicato.
- **Azure Active Directory** - gestisce sicura lo scambio di chiavi di crittografia necessari e l'autenticazione per le azioni richieste. 
  - In genere, è possibile utilizzare un'istanza di Azure Active Directory esistente per l'inserimento di un'applicazione. 
  - L'applicazione è più di un estremo per i servizi di archivio di chiave e macchina virtuale richiedere e ottenere rilasciato la crittografia appropriata. Non si sviluppano un'applicazione reale che si integra con Azure Active Directory.


## <a name="requirements-and-limitations"></a>Requisiti e limitazioni
Requisiti per la crittografia del disco e gli scenari supportati:

- I seguenti server Linux SKU - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES) e Red Hat Enterprise Linux.
- Tutte le risorse (ad esempio archivio chiave account di archiviazione e macchine Virtuali) devono essere nella stessa regione Azure e sottoscrizione.
- A standard, D, DS, G e GS serie macchine virtuali.

Crittografia disco non è attualmente supportata negli scenari seguenti:

- Livello di base macchine virtuali.
- Macchine virtuali create con il modello di distribuzione classica.
- Disabilitare la crittografia del sistema operativo in macchine virtuali Linux.
- Aggiornare la crittografia su un VM Linux già crittografato.


## <a name="create-the-azure-key-vault-and-keys"></a>Creare l'archivio di chiave di Azure e chiavi
Per completare il resto della Guida, è necessario disporre di [Più recente Azure CLI](../xplat-cli-install.md) installato e registrato con la modalità di gestione risorse come indicato di seguito:

```
azure config mode arm
```

Nell'intera esempi comando sostituire tutti i parametri di esempio con nomi, posizione e valori di chiave. Negli esempi seguenti utilizzano una convenzione di `myResourceGroup`, `myKeyVault`, `myAADApp`e così via.

Il primo passaggio consiste nel creare un archivio di chiave Azure per archiviare le chiavi di crittografia. Azure archivio chiave consentono di memorizzare chiavi, informazioni riservate o la password che consentono di implementare in modo efficace le applicazioni e servizi. Per la crittografia disco virtuale, utilizzare chiave archivio per archiviare una chiave di crittografia che viene usata per crittografare o decrittografare dischi virtuali. 

Attivare il provider di Azure chiave archivio nell'abbonamento Azure e quindi crea un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nel `WestUS` percorso:

```bash
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

L'archivio di chiave Azure contenente la crittografia e risorse di elaborazione associato, ad esempio lo spazio di archiviazione e SV stesso deve trovarsi nella stessa regione. Nell'esempio seguente viene creato un archivio di chiave Azure denominata `myKeyVault`:

```bash
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

È possibile memorizzare le chiavi di crittografia tramite software o la protezione del modello di sicurezza Hardware (HSM). Tramite un modulo di sicurezza hardware richiede una chiave archivio premium. Esiste un costo per la creazione di un premio archivio chiave anziché standard archivio chiave che archivia le chiavi protetto da software aggiuntivo. Per creare un premio chiave archivio, nel passaggio precedente aggiungere `--sku Premium` al comando. Nell'esempio seguente Usa tasti protetto da software poiché viene creato un archivio di chiave standard. 

Per entrambi i modelli di protezione e la piattaforma Azure deve concedere l'accesso per richiedere la crittografia quando la macchina virtuale viene avviato per decrittografare dischi virtuali. Creare una chiave di crittografia all'interno di archivio il tasto e quindi abilitarla per l'utilizzo con la crittografia disco virtuale. Nell'esempio seguente viene creata una chiave denominata `myKey` , quindi viene attivata per la crittografia del disco:

```bash
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Creare l'applicazione di Azure Active Directory
Quando dischi virtuali vengono crittografati o decrittografare, utilizzare un endpoint per gestire l'autenticazione e lo scambio di chiavi dall'archivio chiave di crittografia. Questo endpoint, un'applicazione di Azure Active Directory, consente la piattaforma Azure richiedere la crittografia appropriata per conto di macchina virtuale. Un'istanza di Azure Active Directory predefinito è disponibile in abbonamento, anche se molte organizzazioni dedicate directory Azure Active Directory.

Mentre non si sta creando un'applicazione di Azure Active Directory completa, la `--home-page` e `--identifier-uris` non è necessario che l'indirizzo di routing effettivo parametri nell'esempio seguente. Nell'esempio seguente specifica anche un segreto basate su password anziché generare chiavi da all'interno del portale Azure. In questo momento generare chiavi non può essere eseguita da CLI Azure. 

Creare l'applicazione di Azure Active Directory. Nell'esempio seguente viene creata un'applicazione denominata `myAADApp` e viene utilizzata una password di `myPassword`. Specificare la propria password come indicato di seguito:

```bash
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Annotare il `applicationId` restituito nell'output rispetto al comando precedente. L'ID dell'applicazione viene usato in alcuni passaggi rimanenti. Creare un nome principale servizio (SPN) in modo che l'applicazione è accessibile all'interno dell'ambiente. Per correttamente crittografare o decrittografare dischi virtuali, è necessario impostare le autorizzazioni per la chiave di crittografia memorizzate nell'archivio di chiave per consentire l'applicazione di Azure Active Directory per leggere i tasti. 

Creare l'entità servizio e impostare le autorizzazioni appropriate nel modo seguente:

```bash
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Aggiungere un disco virtuale e controllare lo stato di crittografia
Per crittografare effettivamente alcuni dischi virtuale consente di aggiungere un disco una macchina virtuale esistente. Aggiungere un disco dati 5Gb una macchina virtuale esistente come indicato di seguito:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Dischi virtuali non sono attualmente crittografati. Verificare lo stato corrente di crittografia delle macchine Virtuali come indicato di seguito:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Crittografare dischi virtuali
Per crittografare ora dischi virtuali, si combinano tutti i componenti precedenti:

1. Specificare l'applicazione di Azure Active Directory e la password.
2. Specificare l'archivio di chiave per archiviare i metadati per i dischi crittografati.
3. Specificare la crittografia da utilizzare per la crittografia e decrittografia.
4. Specificare se si desidera crittografare il disco del sistema operativo, dischi di dati o tutti.

Consente di esaminare i dettagli per l'archivio di chiave Azure e la chiave che è stata creata, come sia necessario l'ID di archivio di chiave, URI e quindi digitare un URL il passaggio finale:

```bash
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Crittografare il disco virtuale utilizzando l'output di `azure keyvault show` e `azure keyvault key show` comandi come indicato di seguito:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Il comando precedente ha numerose variabili, nell'esempio seguente è il comando completo per riferimento:

```bash
azure vm enable-disk-encryption -g myResourceGroup -n MyVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \ 
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

CLI Azure non fornisce gli errori dettagliati durante il processo di crittografia. Per ulteriori informazioni sulla risoluzione dei problemi, vedere `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` in macchine Virtuali sta effettuando la crittografia.

Infine, consente di controllare lo stato di crittografia per confermare che dischi virtuali a questo punto sono state crittografate:

```bash
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Aggiungere dati aggiuntivi dischi
Dopo che sono stati crittografati dischi dati, è possibile aggiungere in un secondo momento dischi virtuali aggiuntivi per le macchine Virtuali e inoltre crittografato loro. Quando si esegue il `azure vm enable-disk-encryption` comando, incrementare la versione di sequenza utilizzando il `--sequence-version` parametro. Questo parametro versione sequenza consente di eseguire operazioni ripetute nella stessa macchina virtuale.

Ad esempio, consente di aggiungere un secondo disco virtuale di una macchina virtuale come indicato di seguito:

```bash
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Eseguire di nuovo il comando per crittografare dischi virtuali questo per l'aggiunta di `--sequence-version` parametro e incrementare il valore dalla prima esecuzione come indicato di seguito:

```bash
azure vm enable-disk-encryption --resource-group myResourceGroup --vm-name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni sulla gestione archivio chiave Azure, inclusa l'eliminazione di crittografia e archivi, vedere [gestire archivi chiave usa CLI](../key-vault/key-vault-manage-with-cli.md).
- Per ulteriori informazioni sulla crittografia disco, ad esempio la preparazione di una macchina virtuale personalizzata crittografata per caricare in Azure, vedere [La crittografia del Azure](../security/azure-security-disk-encryption.md).