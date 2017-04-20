<properties
   pageTitle="Tasto cassaforte Guida per sviluppatori | Microsoft Azure"
   description="Gli sviluppatori possono utilizzare cassaforte chiave Azure per la gestione delle chiavi di crittografia all'interno dell'ambiente Microsoft Azure. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Guida per gli sviluppatori di Azure cassaforte chiave
Utilizzo chiave cassaforte, sarà in grado di accedere in modo sicuro le informazioni riservate all'interno delle applicazioni in modo che:

- Chiavi e informazioni riservate saranno protetti senza dover scrivere manualmente il codice e sarà in grado di utilizzare le proprie applicazioni.
- Sarà in grado di disporre i propri clienti e gestire i propri chiavi in modo che è possibile concentrare sulla fornitura di funzionalità del software di base. In questo modo le proprie applicazioni non proprietario della responsabilità o possibili responsabilità per le chiavi tenant dei clienti e informazioni riservate.
- L'applicazione può utilizzare chiavi di firma e crittografia ancora mantiene la gestione delle chiavi esterno dall'applicazione in modo che la soluzione è adatta per un'applicazione che geograficamente distribuita.

- Con il rilascio di settembre 2016 dell'archivio di codice, le applicazioni possono ora prevedono l'utilizzo dell'archivio chiave certificati. Per ulteriori informazioni, vedere **sulle chiavi, segreti e certificati** l'articolo nella [Guida di riferimento REST](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Per ulteriori informazioni generali su Azure chiave cassaforte, vedere [What ' s cassaforte chiave](key-vault-whatis.md).

## <a name="videos"></a>Video
In questo video viene illustrato come creare il proprio cassaforte chiave e su come utilizzarlo dall'applicazione di esempio 'Hello chiave cassaforte'.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Collegamenti a risorse indicate nel video:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Codice di esempio cassaforte chiave Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Per informazioni su più è possibile seguire il [Tasto cassaforte Blog](http://aka.ms/kvblog) e partecipare ai [Forum cassaforte chiave](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Creazione e gestione degli archivi chiavi

Prima di passare con Azure chiave cassaforte nel codice, è possibile creare e gestire gli archivi tramite REST, modelli Manager delle risorse, PowerShell o CLI, come descritto negli articoli seguenti:

- [Creare e gestire gli archivi di chiave con REST](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Creare e gestire gli archivi di chiave PowerShell](key-vault-get-started.md)
- [Creare e gestire gli archivi di chiave con CLI](key-vault-manage-with-cli.md)
- [Creare un archivio di chiave e aggiungere un segreto tramite un modello di gestione di risorse di Azure](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operazioni di archivi chiave vengono autenticate tramite AAD e autorizzate tramite criteri di accesso chiave Vault personalizzati, definiti per singolo archivio.

## <a name="coding-with-key-vault"></a>Scrittura di codice con chiave cassaforte

Il sistema di gestione dell'archivio di codice per i programmatori è costituito da diverse interfacce con REST la base, [Guida di riferimento API REST cassaforte chiave](https://msdn.microsoft.com/library/azure/dn903609.aspx).

Si è soggetta a autorizzazione ha esito positivo, procedere come segue:

- Gestione delle chiavi di crittografiche utilizzando [Crea](https://msdn.microsoft.com/library/azure/dn903634.aspx), [importazione](https://msdn.microsoft.com/library/azure/dn903626.aspx), [aggiornamento](https://msdn.microsoft.com/library/azure/dn903616.aspx), [eliminazione](https://msdn.microsoft.com/library/azure/dn903611.aspx) e altre operazioni

- Gestire i segreti utilizzando [Get](https://msdn.microsoft.com/library/azure/dn903633.aspx), [aggiornamento](https://msdn.microsoft.com/library/azure/dn986818.aspx), [eliminazione](https://msdn.microsoft.com/library/azure/dn903613.aspx) e altre operazioni

- Utilizzare chiavi di crittografia con [accesso](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verify](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [Crittografa](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[decrittografare](https://msdn.microsoft.com/library/azure/dn878097.aspx) operazioni

SDK seguenti sono disponibili per l'utilizzo di archivi chiave:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![Node](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentazione SDK .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Documentazione SDK Node](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacchetto SDK .NET su Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Pacchetto Node SDK](https://www.npmjs.com/package/azure-keyvault)|

Per ulteriori informazioni sulla versione di .NET SDK 2. x, vedere le [note sulla versione](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Esempio di codice
Per esempi completato con chiave cassaforte con le applicazioni, vedere:

- Applicazione di esempio .NET *HelloKeyVault* e un esempio di servizio web di Azure. [Esempi di codice di tasto cassaforte Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Esercitazione che consentono di imparare a utilizzare Azure chiave cassaforte da un'applicazione web in Azure. [Utilizzare Azure cassaforte chiave da un'applicazione Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procedure

Articoli e gli scenari seguenti offrono indicazioni specifiche attività per l'utilizzo di archivi chiave Azure:

- [Modifica cassaforte chiave tenant ID dopo lo spostamento sottoscrizione](key-vault-subscription-move-fix.md) - quando si sposta la sottoscrizione Azure dal tenant al tenant B, il chiavi archivi esistenti non sono accessibili per le entità (utenti e applicazioni) nel tenant Fix B. questo utilizzo della presente Guida.
- [Accesso al tasto cassaforte firewall](key-vault-access-behind-firewall.md) - per accedere a una cassaforte chiave che deve essere in grado di accedere più endpoint per diverse funzionalità che l'applicazione client cassaforte chiave.

- [Come generare e le chiavi Transfer HSM-Protected per Azure chiave cassaforte](key-vault-hsm-protected-keys.md) - questa consentono di pianificare, generare e quindi trasferire il proprio chiavi HSM protetta da utilizzare con Azure chiave cassaforte.
- [Come passare valori sicuri (ad esempio, le password) durante la distribuzione](../resource-manager-keyvault-parameter.md) - quando è necessario passare un valore sicuro (ad esempio, una password) come parametro durante la distribuzione, è possibile archiviare questo valore come segreto in un archivio chiave Azure e il valore in altri modelli di Manager delle risorse di riferimento.
- [Come utilizzare cassaforte chiave per la gestione delle chiavi extensible con SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - il connettore Server SQL per Azure chiave cassaforte consente a SQL Server e SQL in VM utilizzare al meglio il servizio di Azure chiave cassaforte come provider di Extensible Key Management (EKM) per proteggere le chiavi di crittografia per applicazioni della cartella. Transparent Data Encryption, crittografia di Backup e la crittografia a livello di colonna.
- [Distribuzione dei certificati per le macchine virtuali dall'insieme di credenziali chiave](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - un'applicazione cloud in esecuzione in una macchina virtuale in Azure è necessario un certificato. Come si ottengono questo certificato in questa macchina virtuale oggi?
- Informazioni su [come programma di installazione dell'archivio di codice con rotazione delle chiavi end-to-end e il controllo](key-vault-key-rotation-log-monitoring.md) - questo documento viene descritta come configurare il controllo con Azure chiave cassaforte e rotazione delle chiavi.

Per ulteriori istruzioni specifiche per integrazione e utilizzo di archivi di chiave con Azure, vedere [esempi di modello Ryan Jones Manager delle risorse Azure per cassaforte chiave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integrato con chiave cassaforte

In questi articoli sono su altri scenari e i servizi che differenziano di integrano con chiave cassaforte.

- [Crittografia disco Azure](../security/azure-security-disk-encryption.md) utilizza la funzionalità di [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard di settore di Windows e la funzionalità di [Crittografia DM](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per fornire la crittografia del volume per il sistema operativo e i dischi dati. La soluzione è integrata con Azure cassaforte chiave che consentono di controllare e gestire le chiavi di crittografia su disco e segreti in abbonamento cassaforte chiave, garantendo che tutti i dati nei dischi macchina virtuale vengono crittografati statici nell'archiviazione Azure.


## <a name="supporting-libraries"></a>Librerie di supporto

- [Libreria di base di Microsoft Azure chiave cassaforte](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornisce `IKey` e `IKeyResolver` interfacce per l'individuazione chiavi da identificatori e si eseguono operazioni con i tasti.

- [Estensioni di Microsoft Azure chiave cassaforte](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornisce funzionalità estese per Azure chiave cassaforte.

## <a name="other-key-vault-resources"></a>Altre risorse chiave cassaforte
- [Blog cassaforte chiave](http://aka.ms/kvblog)
- [Forum cassaforte chiave](http://aka.ms/kvforum)
