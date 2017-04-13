<properties
    pageTitle="Cronologia delle versioni di diagnostica Windows Azure"
    description="Spiegazione delle modifiche apportate in versioni diverse di diagnostica Windows Azure come spediti con versioni diverse di Microsoft Azure SDK."
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/12/2016"
    ms.author="robb"/>


# <a name="microsoft-azure-diagnostics-version-history"></a>Cronologia delle versioni di diagnostica Microsoft Azure

Nuovi utenti di diagnostica Windows Azure? Vedere [Panoramica di diagnostica Windows Azure](azure-diagnostics.md).

Ogni versione di Azure SDK in genere viene fornito con una nuova versione di Azure diagnostica. Nella tabella seguente vengono descritte le versioni di Azure SDK e diagnostica Azure associate alla versione SDK.



Versione SDK Azure | Versione di diagnostica Azure | Modello
--- | --- | ---
1. x      | 1.0 | plug-in
2.0 per 2,4| 1.0 | "
2,5      | 1.2 | estensione
2.6      | 1.3 | "
2.7      | 1.4 | "
2,8      | 1,5 | "


La versione più recente è 1,5, fornito con Azure SDK 2,8. La versione dell'estensione diagnostica Azure fornito con SDK verrà usata unicamente per gli scenari emulatore locale. Qualsiasi applicazione distribuita utilizza la versione più recente automaticamente durante l'esecuzione in Azure, indipendentemente dal fatto che è integrata con la versione di SDK dell'applicazione. Tuttavia, a meno che non si installa la versione più recente di Azure SDK, potrebbe non essere tutti gli strumenti che consentono di utilizzano le nuove funzioni.

Varie caratteristiche e modifiche descritte di seguito.

## <a name="azure-sdk-28"></a>Azure SDK 2,8
Azure SDK 2,8 aggiunta la possibilità di inviare i dati di diagnostica per [Informazioni dettagliate sui applicazione](./application-insights/app-insights-cloudservices.md) semplificate diagnosticare i problemi attraverso l'applicazione, nonché il livello di sistema e infrastruttura.

## <a name="azure-26-diagnostics-changes"></a>Modifiche di diagnostica Azure 2.6

Per i progetti di servizio Cloud di Azure SDK 2.6 in Visual Studio, sono state apportate modifiche seguenti. (Le modifiche applicate anche alle versioni più recenti di Azure SDK).

- Emulatore locale supporta ora diagnostica. Indica che è possibile raccogliere dati di diagnostica e assicurarsi che l'applicazione consiste nel creare tracce destra mentre sta sviluppare e testare in Visual Studio. La stringa di connessione `UseDevelopmentStorage=true` consente di raccogliere dati di diagnostica mentre si esegue il progetto di servizio cloud in Visual Studio utilizzando emulatore archiviazione Azure. Tutti i dati di diagnostica vengono raccolte nell'account di archiviazione (spazio di archiviazione di sviluppo).

- La stringa di connessione di diagnostica lo spazio di archiviazione account (Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString) verrà archiviata nuovo file di configurazione (. cscfg) del servizio. L'account di archiviazione di diagnostica Azure SDK 2,5 stato specificato nel file diagnostics.wadcfgx.

Esistono alcune importanti differenze tra come stringa di connessione ha esito positivo in Azure SDK 2,4 e versioni precedenti e sul suo funzionamento in Azure SDK 2.6 e versioni successive.

- In Azure SDK 2,4 e versioni precedenti, la stringa di connessione è stata utilizzata come un runtime il plug-in di diagnostica per ottenere le informazioni sull'account di archiviazione per trasferire i registri di diagnostica.

- In Azure SDK 2.6 e versioni successive, la stringa di connessione di diagnostica viene utilizzata da Visual Studio per configurare l'estensione di diagnostica con le informazioni sull'account di archiviazione appropriato durante la pubblicazione. La stringa di connessione consente di definire gli account di archiviazione diversi per le configurazioni di servizi diversa utilizzate Visual Studio per la pubblicazione. Tuttavia, poiché il plug-in di diagnostica non è più disponibile (dopo aver Azure SDK 2,5), il file cscfg autonomamente non è possibile attivare l'estensione di diagnostica. È necessario attivare l'estensione separatamente tramite strumenti, ad esempio Visual Studio o PowerShell.

- Per semplificare il processo di configurazione l'estensione di diagnostica con PowerShell, l'output di pacchetto da Visual Studio contiene anche pubblico XML di configurazione per l'estensione di diagnostica per ogni ruolo. Visual Studio utilizza la stringa di connessione di diagnostica per compilare le informazioni sull'account di archiviazione presente nella configurazione del pubblica. I file di configurazione pubblico creati nella cartella estensioni e seguono il modello PaaSDiagnostics. <RoleName>. PubConfig.xml. Le distribuzioni di PowerShell in base a è possono utilizzare questo modello per eseguire il mapping di ogni configurazione a un ruolo.

- La stringa di connessione nel file cscfg viene usata anche tramite il portale di Azure per accedere ai dati di diagnostica in modo che può essere visualizzati nella scheda **monitoraggio** . Per configurare il servizio per visualizzare dati di monitoraggio dettagliati nel portale è necessaria la stringa di connessione.

### <a name="migrating-projects-to-azure-sdk-26-and-later"></a>Migrazione di progetti di Azure SDK 2.6 e versioni successive

Durante la migrazione da Azure SDK 2,5 a Azure SDK 2.6 o versione successiva, se si dispone di un account di archiviazione di diagnostica specificato nel file .wadcfgx, quindi rimarrà non esiste. Per sfruttare la possibilità di utilizzare gli account di archiviazione diversi per le configurazioni di archiviazione diversa, è necessario aggiungere manualmente la stringa di connessione al progetto. Se si sta eseguendo la migrazione di un progetto da Azure SDK 2,4 o versione precedente a Azure SDK 2.6, le stringhe di connessione di diagnostica vengono mantenute. Tuttavia, tenere presente le modifiche in come stringhe di connessione sono considerate Azure SDK 2.6 specificato nella sezione precedente.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Come Visual Studio determina l'account di archiviazione di diagnostica

- Se si specifica una stringa di connessione di diagnostica nel file cscfg, Visual Studio viene utilizzato per configurare l'estensione di diagnostica per la pubblicazione e per generare i file xml di configurazione pubblico durante imballaggio.

- Se nessuna stringa di connessione di diagnostica viene specificata nel file cscfg, quindi Visual Studio ricorre all'utilizzo dell'account di archiviazione specificato nel file .wadcfgx per configurare l'estensione di diagnostica quando la pubblicazione e generare i file xml di configurazione pubblico quando imballaggio.

- La stringa di connessione di diagnostica nel file cscfg ha la precedenza su account di archiviazione nel file .wadcfgx. Se si specifica una stringa di connessione di diagnostica nel file cscfg, Visual Studio utilizza che e ignora l'account di archiviazione in .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-checkbox-do"></a>Che cosa indica la "aggiornamento sviluppo archiviazione stringhe di connessione..." casella di controllo fare?

La casella di controllo **Aggiorna sviluppo dello spazio di archiviazione le stringhe di connessione di diagnostica e memorizzazione nella cache con le credenziali dell'account di archiviazione Microsoft Azure durante la pubblicazione in Microsoft Azure** offre un modo comodo per aggiornare tutte le stringhe di connessione di sviluppo dello spazio di archiviazione account con l'account di archiviazione Azure specificato durante la pubblicazione.

Si supponga ad esempio si seleziona questa casella di controllo e la stringa di connessione di diagnostica specifica `UseDevelopmentStorage=true`. Quando si pubblica il progetto in Azure, Visual Studio viene aggiornato automaticamente la stringa di connessione di diagnostica con l'account di archiviazione specificato nella creazione guidata pubblicazione. Tuttavia, se è stato specificato un account di archiviazione reale come stringa di connessione diagnostica, quindi quell'account viene usato.

## <a name="diagnostics-functionality-differences-between-azure-sdk-24-and-earlier-and-azure-sdk-25-and-later"></a>Differenze di funzionalità di diagnostica tra Azure SDK 2,4 e versioni precedenti e Azure SDK 2,5 e versioni successive

Se si sta aggiornando un progetto da Azure SDK 2,4 a Azure SDK 2.5 o versione successiva, è necessario tenere in considerazione le differenze di funzionalità di diagnostica seguenti.

- **API di configurazione sono obsoleti** -configurazione livello di programmazione di diagnostica è disponibile in Azure SDK 2,4 o versioni precedenti, ma è deprecata in Azure SDK 2.5 e versioni successive. Se la configurazione di diagnostica attualmente è stata definita nel codice, sarà necessario riconfigurare tali impostazioni da zero nel progetto migrato affinché diagnostica per continuare a lavorare. File di configurazione di diagnostica per Azure SDK 2,4 è diagnostics.wadcfg e diagnostics.wadcfgx per Azure SDK 2.5 e versioni successive.

- **Diagnostica per le applicazioni di servizio cloud può essere configurata solo a livello di ruolo, non a livello di istanza.**

- **Ogni volta che si distribuisce l'app, la configurazione di diagnostica viene aggiornata** , possono verificarsi problemi di uniformità se si modifica la configurazione di diagnostica da Esplora Server e quindi ridistribuita l'app.

- **In file di configurazione di diagnostica, non nel codice sono configurati in Azure SDK 2,5 e immagini della versione successive, arresto anomalo del sistema** -se hai anomalo configurato nel codice, è necessario trasferire manualmente la configurazione dal codice in file di configurazione, perché l'anomalo Azure SDK 2.6 non vengono trasferiti durante la migrazione.
