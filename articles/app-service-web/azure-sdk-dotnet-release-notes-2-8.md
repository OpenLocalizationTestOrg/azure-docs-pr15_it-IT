
<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2,8" 
   description="Note sulla versione di Azure SDK per .NET 2,8" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK per .NET 2,8, 2.8.1 e 2.8.2

##<a name="overview"></a>Panoramica
 
In questo articolo contiene le note sulla versione (che include i problemi noti e le ultime modifiche) per Azure SDK per .NET 2,8, 2.8.1 e. 2.8.2 rilascia. 

Per un elenco completo delle nuove caratteristiche e gli aggiornamenti effettuati in questa versione, vedere l'annuncio di [Azure SDK 2,8 per Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK per .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Scaricare Azure SDK per .NET 2,8

[Azure SDK per .NET 2,8 per Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK per .NET 2,8 per Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>Supporto tecnico .NET 4.5.2 

####<a name="known-issues"></a>Problemi noti

Azure .NET SDK 2,8 consente di creare .NET 4.5.2 pacchetti servizio Cloud. Tuttavia 4.5.2 .NET framework non verrà installato come impostazione predefinita rilasciare immagini del sistema operativo Guest fino al gennaio 2016 OS Guest. Nelle versioni precedenti, 4.5.2 .NET framework saranno disponibili tramite una versione di rilascio del sistema operativo Guest separata: novembre 02 2015. Vedere la pagina [versioni Guest Azure e nella matrice compatibilità SDK](../cloud-services/cloud-services-guestos-update-matrix.md) per tenere traccia di quando verrà rilasciato l'immagine.  L'immagine di 2015 02 novembre viene rilasciato è possibile scegliere di utilizzare tale immagine aggiornando il servizio Cloud (cscfg) file di configurazione. Nella configurazione del servizio file imposta l'attributo osVersion dell'elemento ServiceConfiguration la stringa "WA-GUEST-OS-4.26_201511-02". Se si sceglie di acconsentire esplicitamente al utilizzare questa immagine, quindi non sarà più ottenere gli aggiornamenti automatici per il sistema operativo Guest. Per ottenere gli aggiornamenti automatici di osVersion deve essere impostata su "*" e .NET 4.5.2 saranno disponibili solo tramite gli aggiornamenti automatici in gennaio 2016.

###<a name="azure-data-factory"></a>Dati di Azure Factory

####<a name="known-issues"></a>Problemi noti 

Durante la creazione di progetti di **Modello di dati Factory** associate ai dati di esempio, script di shell power azure potrebbe non riuscire se power azure shell versione installata nel computer dopo 0.9.8.

Per creare correttamente questo tipo di progetto, è necessario installare [power azure shell versione 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Strumenti di gestione risorse di Azure 

####<a name="breaking-changes"></a>Ultime modifiche

Lo script di PowerShell fornito per il progetto di gruppo di risorse Azure è stato aggiornato in questa versione per l'uso con i cmdlet di PowerShell Azure nuovi versione 1.0.  Il nuovo script non funzionerà da con Visual Studio quando si utilizza una versione di SDK prima di 2,8.  

Script di progetti creati in versioni precedenti di SDK non vengono eseguite da Visual Studio quando si utilizza SDK 2,8.  Tutti gli script continueranno a funzionare all'esterno di Visual Studio con la versione appropriata dei cmdlet di PowerShell Azure.  

SDK 2,8 richiede versione 1.0 dei cmdlet di PowerShell Azure.  Tutte le altre versioni di SDK richiedono versione 0.9.8 dei cmdlet di PowerShell Azure.  Per ulteriori informazioni, vedere [questo](http://go.microsoft.com/fwlink/?LinkID=623011) blog.

###<a name="web-tools-extensions"></a>Estensioni degli strumenti Web

####<a name="known-issues"></a>Problemi noti

Problemi noti seguenti verranno risolto nella versione seguente.

- App servizio correlato Cloud ed Esplora Server combinazione per ambienti non di produzione (ad esempio Cina Azure o clienti dello Stack di Azure) non funzionano. Per i clienti in queste aree interessate, scaricare il profilo di pubblicazione dal portale di Azure consentirà possibilità di pubblicazione. Una versione futura in grado di ripristinare movimenti, ad esempio "Allegare Debugger" e "Visualizzare i registri Streaming" Cina Azure colleghi e Stack. 
- I clienti che venga visualizzata errori durante la creazione quando approfondimenti App istanza a cui sono distribuzione è in un'area diversa da quella degli Stati Uniti orientali servizio di App. In questi scenari, creazione di un servizio di App del portale e scaricare il profilo di pubblicazione consentirà scenari di pubblicazione. 

###<a name="azure-hdinsight-tools"></a>Strumenti di Azure HDInsight

####<a name="new-updates"></a>Nuovi aggiornamenti

- È possibile eseguire la query Hive cluster tramite HiveServer2 con quasi alcun sovraccarico e vedere che il processo di accesso in tempo reale.
- Utilizzare la nuova Hive attività esecuzione visualizzazione è possibile esaminare il lavoro più approfondito, informazioni più dettagliate e identificare i potenziali problemi.

Per informazioni, vedere [Azure SDK 2,8 per Visual Studio 2013 e Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK per .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemi noti di Visual Studio 2013 e Visual Studio 2015
 
1. WebJob attivate pubblica su bande orarie verrà Mostra e errore e non è possibile impostare una pianificazione, ma verrà push il WebJob in Azure. I clienti che hanno bisogno di un processo pianificato quindi possono utilizzare il portale di Azure per impostare la pianificazione per il WebJob. 
2. Python potrebbero verificarsi problemi di debugger. Team del servizio è attuare una soluzione per questo, ma se i clienti sono interessati, per comunicare a Microsoft SA nei forum o nel blog annuncio o rilasciare sezione commenti note. 
3. In alcune aree geografiche (ad esempio sud India) verificherà servizio App errori di provisioning. Si tratta coerente con il portale e i clienti che hanno verificano questo problema è possono utilizzare il portale di Azure per richiedere l'accesso per la pubblicazione in queste aree geografico. Una volta richiedono l'accesso a queste aree utilizzando il provisioning del portale di Azure deve lavorare. 

##<a name="azure-sdk-for-net-282"></a>Azure SDK per .NET 2.8.2

Dopo l'installazione del 2.8.2 strumenti clienti potrebbero verificarsi problemi di seguito.         

- Se si utilizza Windows 10 e non è stato installato Internet Explorer, si venga visualizzato un messaggio di errore "Internet Explorer non trovato".
Per risolvere il problema, installare Internet Explorer utilizzando la finestra di dialogo Aggiungi/Rimuovi componenti di Windows.

Se si rileva questo problema, usare la caratteristica Invia un smile per segnalare il.

Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) post.
##<a name="other-updates"></a>Altri aggiornamenti

Per altri aggiornamenti, vedere [annuncio di Azure SDK 2,8 post](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Vedere anche

[Post di annuncio SDK 2,8 Azure](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Supporto e informazioni pensionistico per Azure SDK per .NET e le API](https://msdn.microsoft.com/library/azure/dn479282.aspx)

