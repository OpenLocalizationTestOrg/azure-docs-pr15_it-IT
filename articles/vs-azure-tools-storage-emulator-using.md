<properties 
   pageTitle="Configurazione e utilizzo emulatore lo spazio di archiviazione con Visual Studio | Microsoft Azure"
   description="Configurazione e utilizzo emulatore lo spazio di archiviazione con Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configurazione e utilizzo emulatore lo spazio di archiviazione con Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Panoramica
Ambiente di sviluppo di Azure SDK include emulatore lo spazio di archiviazione, un'utilità che simula i servizi dello spazio di archiviazione disponibili in Azure sul computer locale sviluppo Blob, coda e una tabella. Se ci si trova la compilazione di un servizio cloud che utilizza i servizi di archiviazione Azure o tutte le applicazioni esterne che chiama i servizi di archiviazione di scrittura, è possibile testare il codice in locale in base a emulatore lo spazio di archiviazione. Gli strumenti di Azure per Microsoft Visual Studio integrare gestione dell'emulatore lo spazio di archiviazione in Visual Studio. Gli strumenti di Azure inizializzare il database di emulatore lo spazio di archiviazione al primo utilizzo, viene avviato il servizio di archiviazione emulatore quando si esegue o il debug del codice da Visual Studio e offre accesso in sola lettura ai dati emulatore lo spazio di archiviazione tramite Esplora aree dello spazio di archiviazione di Azure.

Per informazioni dettagliate sull'emulatore di spazio di archiviazione, inclusi i requisiti di sistema e le istruzioni di configurazione personalizzata, vedere [usare l'emulatore di spazio di archiviazione Azure di sviluppo e la verifica](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Esistono alcune differenze nelle funzionalità la simulazione emulatore lo spazio di archiviazione e i servizi di archiviazione Azure. Nella documentazione di Azure SDK per informazioni sulle differenze specifiche, vedere [differenze tra lo spazio di archiviazione emulatore e servizi di archiviazione Windows Azure](./storage/storage-use-emulator.md) .

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configurazione di una stringa di connessione per l'emulatore lo spazio di archiviazione

Per accedere a emulatore lo spazio di archiviazione da codice all'interno di un ruolo, possibile configurare una stringa di connessione che punta alla emulatore lo spazio di archiviazione e versioni successive possono essere modificate in modo che puntino a un account di archiviazione Azure. Una stringa di connessione è un'impostazione di configurazione in grado di leggere il proprio ruolo in fase di esecuzione per connettersi a un account di archiviazione. Per ulteriori informazioni su come creare stringhe di connessione, vedere [configurazione dell'applicazione di Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Si può restituire un riferimento all'account emulatore lo spazio di archiviazione dal codice utilizzando la proprietà **DevelopmentStorageAccount** . Questo approccio funziona correttamente se si desidera accedere emulatore lo spazio di archiviazione dal codice, ma se si intende pubblicare l'applicazione in Azure, sarà necessario creare una stringa di connessione per accedere all'account di archiviazione Azure e modificare il codice per usare la stringa di connessione prima di pubblicare. Se si desidera passare tra l'account di emulatore lo spazio di archiviazione e di un account di archiviazione Azure frequentemente, una stringa di connessione verrà semplificare il processo.

## <a name="initializing-and-running-the-storage-emulator"></a>Inizializzazione e l'esecuzione di emulatore lo spazio di archiviazione

È possibile specificare quando si esegue o eseguire il debug del servizio Visual Studio, Visual Studio avvia automaticamente l'emulatore lo spazio di archiviazione. In Esplora risorse, aprire il menu di scelta rapida per il progetto di **Azure** e scegliere **proprietà**. Nella scheda **sviluppo** , nell'elenco **Avviare Azure archiviazione emulatore** scegliere **True** (se non è già impostata su tale valore).

La prima volta che si esegue o eseguire il debug il servizio da Visual Studio, emulatore lo spazio di archiviazione avvia un processo di inizializzazione. Questo processo si riserva porte locali per emulatore lo spazio di archiviazione e crea il database di emulatore lo spazio di archiviazione. Una volta completato, non è necessario eseguire di nuovo a meno che non viene eliminato il database di emulatore archiviazione questo processo.

>[AZURE.NOTE] A partire dalla versione di giugno 2012 degli strumenti di Azure, l'archiviazione emulatore viene eseguito, per impostazione predefinita, in SQL Express LocalDB. Nelle versioni precedenti degli strumenti di Azure, l'emulatore lo spazio di archiviazione viene eseguito rispetto a un'istanza predefinita di SQL Express 2005 o 2008, che è necessario installare prima è possibile installare Azure SDK. È anche possibile eseguire emulatore lo spazio di archiviazione in base a un'istanza denominata di SQL Express o un oggetto denominato o istanza predefinita di Microsoft SQL Server. Se è necessario configurare emulatore lo spazio di archiviazione per eseguire un'istanza diversa da quella predefinita, vedere [usare l'emulatore di spazio di archiviazione Azure di sviluppo e la verifica](./storage/storage-use-emulator.md).

Emulatore archiviazione offre un'interfaccia utente per visualizzare lo stato dei servizi archivio locale e per iniziare, interrompere e reimpostare i valori. Una volta avviato il servizio di archiviazione emulatore, visualizzare l'interfaccia utente o avviare o arrestare il servizio facendo clic sull'icona di area di notifica di Microsoft Azure Emulator sulla barra delle applicazioni di Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Visualizzazione dei dati emulatore lo spazio di archiviazione in Esplora Server

Il nodo dello spazio di archiviazione di Azure in Esplora Server consente di visualizzare i dati e modificare le impostazioni per i dati blob e una tabella negli account di archiviazione, inclusi emulatore lo spazio di archiviazione. Per ulteriori informazioni, vedere [esplorazione e la gestione risorse di archiviazione con Esplora risorse Server](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
