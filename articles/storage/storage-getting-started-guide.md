<properties
    pageTitle="Guida introduttiva allo spazio di archiviazione di Azure cinque minuti | Microsoft Azure"
    description="Rapidamente come BLOB di Microsoft Azure, tabella e code tramite avvio veloce di Azure lo spazio di archiviazione, Visual Studio ed emulatore archiviazione Azure. Eseguire l'applicazione di archiviazione Azure prima di cinque minuti."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Guida introduttiva allo spazio di archiviazione di Azure cinque minuti

## <a name="overview"></a>Panoramica

È facile Introduzione allo sviluppo di archiviazione Azure. In questa esercitazione viene illustrato come ottenere un'applicazione di archiviazione Azure alto e in esecuzione rapidamente. Utilizzare i modelli di avvio rapido inclusi con Azure SDK per .NET. Queste guide rapide contengono codice prodotto pronto per la tecnologia a portata che illustra alcuni scenari di programmazione base con lo spazio di archiviazione di Azure.

Per ulteriori informazioni sull'archiviazione di Azure prima di leggere i il codice, vedere [Passaggi successivi](#next-steps).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, è necessario disporre i prerequisiti seguenti:

1. Per compilare e generare l'applicazione, è necessario disporre di una versione di [Visual Studio](https://www.visualstudio.com/) installata nel computer in uso.

2. Installare la versione più recente [Azure SDK per .NET](https://azure.microsoft.com/downloads/). il SDK include i progetti di esempio di Guida introduttiva di Azure, emulatore Azure dello spazio di archiviazione e la [Libreria di Client di spazio di archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Verificare la presenza di [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installato nel computer, come necessario per i progetti di esempio di Guida introduttiva di Azure che verrà usato in questa esercitazione.

    Se non si è certi di quale versione di .NET Framework è installata nel computer, vedere [come: determinare quali .NET Framework sono installate versioni](https://msdn.microsoft.com/vstudio/hh925568.aspx). In alternativa, premere il pulsante **Start** o il tasto Windows, digitare **Pannello di controllo**. Scegliere **programmi** > **programmi e funzionalità**e verificare se .NET Framework 4.5 è elencato tra i programmi installati.

4. È necessario un abbonamento a Azure e un account di archiviazione Azure.

    - Per ottenere una sottoscrizione di Azure, vedere [Versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/), [Le opzioni di acquisto](https://azure.microsoft.com/pricing/purchase-options/)e [Offre membro](https://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network e BizSpark e altri programmi Microsoft).
    - Per creare un account di archiviazione in Azure, vedere [come creare un account di archiviazione](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Eseguire l'applicazione di archiviazione Azure prima dello spazio di archiviazione di Azure nel cloud

Dopo avere creato un account, è possibile creare un'applicazione di archiviazione Azure semplice utilizzando uno dei progetti di esempio avvio veloce di Azure in Visual Studio. In questa esercitazione è incentrata su progetti di esempio per l'archiviazione di Azure: **lo spazio di archiviazione di Azure: BLOB**, **lo spazio di archiviazione di Azure: file**, **lo spazio di archiviazione di Azure: code**, e **lo spazio di archiviazione di Azure: tabelle**:

1. Avviare Visual Studio.
2. Dal menu **File** fare clic su **Nuovo progetto**.
3. Nella finestra di dialogo **Nuovo progetto** , fare clic su **installazione** > **modelli** > **c#** > **Cloud** > **Guide rapide** > **Servizi di dati**.
    un. Scegliere uno dei seguenti modelli: **lo spazio di archiviazione di Azure: BLOB**, **lo spazio di archiviazione di Azure: file**, **lo spazio di archiviazione di Azure: code**, o **lo spazio di archiviazione di Azure: tabelle**.
    b. Assicurarsi che sia selezionato **.NET Framework 4.5** come il framework di destinazione.
    - 3.c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato:

    ![Avvio rapido Azure][Image1]

È consigliabile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, scegliere **Esplora soluzioni** dal menu **Visualizza** in Visual Studio. Quindi fare doppio clic sul file Program.cs.

Successivamente, eseguire l'applicazione di esempio:

1.  In Visual Studio, selezionare **Esplora** dal menu **Visualizza** . Aprire il file di App e commento la stringa di connessione per l'emulatore archiviazione Azure:

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Rimuovere il commento per la stringa di connessione per il servizio di archiviazione di Azure e fornire l'archiviazione nome e l'accesso chiave account nell'app.:`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Per recuperare la chiave di accesso di account di archiviazione, vedere [gestire i tasti di scelta dello spazio di archiviazione](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Al termine è fornire il nome dell'account di archiviazione e tasto nel file App, nel menu **File** fare clic su **Salva tutto** per salvare tutti i file di progetto.
4.  Nel menu **Compila** , fare clic su **Compila soluzione**.
5.  Nel menu **Debug** , premere **F11** per eseguire la soluzione passo dopo passo oppure premere **F5** per eseguire la soluzione.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Eseguire l'applicazione di archiviazione Azure prima localmente emulatore lo spazio di archiviazione di Azure

[Azure archiviazione emulatore](storage-use-emulator.md) offre un ambiente locale che emula i servizi Blob Azure, coda e una tabella in fase di sviluppo. È possibile utilizzare l'emulatore lo spazio di archiviazione per testare l'applicazione di archiviazione in locale, senza la creazione di un abbonamento a Azure o un account di archiviazione e senza incorrere qualsiasi costo.

Per provare, creare un'applicazione di archiviazione Azure semplice utilizzando uno dei progetti di esempio avvio veloce di Azure in Visual Studio. In questa esercitazione è incentrata su progetti di esempio **Archiviazione Blob Azure**, **Archivio tabelle Azure**e **Lo spazio di archiviazione di Azure coda** :

1. Avviare Visual Studio.
2. Dal menu **File** fare clic su **Nuovo progetto**.
3. Nella finestra di dialogo **Nuovo progetto** , fare clic su **installazione** > **modelli** > **c#** > **Cloud** > **Guide rapide** > **Servizi di dati**.
    un. Scegliere uno dei seguenti modelli: **lo spazio di archiviazione di Azure: BLOB**, **lo spazio di archiviazione di Azure: file**, **lo spazio di archiviazione di Azure: code**, o **lo spazio di archiviazione di Azure: tabelle**.
    b. Assicurarsi che sia selezionato **.NET Framework 4.5** come il framework di destinazione.
    c. Specificare un nome per il progetto e creare la nuova soluzione di Visual Studio, come illustrato:

    ![Avvio rapido Azure][Image1]

4.  In Visual Studio, selezionare **Esplora** dal menu **Visualizza** . Aprire il file app commento la stringa di connessione per l'account di archiviazione Azure se sia già stato aggiunto. Quindi rimuovere il commento per la stringa di connessione per l'emulatore archiviazione Azure:

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

È consigliabile esaminare il codice sorgente prima di eseguire l'applicazione. Per esaminare il codice, scegliere **Esplora soluzioni** dal menu **Visualizza** in Visual Studio. Quindi fare doppio clic sul file Program.cs.

Successivamente, eseguire l'applicazione di esempio nell'emulatore di spazio di archiviazione di Azure:

1.  Premere il tasto Windows, cercare *emulatore lo spazio di archiviazione di Microsoft Azure*o dal pulsante **Start** e avviare l'applicazione. Quando si avvia l'emulatore, si verrà visualizzata un'icona e una notifica nell'area di visualizzazione delle attività di Windows.
2.  In Visual Studio, fare clic su **Compila soluzione** dal menu **Genera** .
3.  Nel menu **Debug** , premere **F11** per eseguire la soluzione passo dopo passo oppure premere **F5** per eseguire la soluzione dall'inizio alla fine.

## <a name="next-steps"></a>Passaggi successivi

Vedere le risorse per altre informazioni sull'archiviazione Azure:

* [Introduzione all'archiviazione di Microsoft Azure](storage-introduction.md)
* [Iniziare con Esplora risorse lo spazio di archiviazione di Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Guida introduttiva a archiviazione Blob Azure con .NET](storage-dotnet-how-to-use-blobs.md)
* [Guida introduttiva a archiviazione tabella Azure tramite .NET](storage-dotnet-how-to-use-tables.md)
* [Guida introduttiva allo spazio di archiviazione di Azure coda utilizzando .NET](storage-dotnet-how-to-use-queues.md)
* [Guida introduttiva a spazio di archiviazione di Azure File in Windows](storage-dotnet-how-to-use-files.md)
* [Trasferire i dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)
* [Documentazione di archiviazione Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Raccolta di Client di Microsoft Azure lo spazio di archiviazione per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Servizi di archiviazione Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
