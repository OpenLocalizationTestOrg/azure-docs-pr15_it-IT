<properties 
    pageTitle="Usare ReportViewer in un sito Web | Microsoft Azure"
    description="In questo argomento viene descritto come creare un sito Web Microsoft Azure con il controllo di Visual Studio ReportViewer che consente di visualizzare un report archiviato in un computer virtuali di Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="guyinacube"
    manager="erikre"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/04/2016"
    ms.author="asaxton" />

# <a name="use-reportviewer-in-a-web-site-hosted-in-azure"></a>Usare ReportViewer in un sito Web ospitato in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


È possibile creare un sito Web Microsoft Azure con il controllo di Visual Studio ReportViewer che consente di visualizzare un report archiviato in un computer virtuali di Microsoft Azure. Il controllo ReportViewer è in un'applicazione Web che si creano mediante il modello di applicazione Web ASP.NET.

>[AZURE.IMPORTANT] I modelli di applicazione Web ASP.NET MVC non supportano il controllo ReportViewer.

Per incorporare il sito Web Microsoft Azure ReportViewer, è necessario completare le attività seguenti.

- **Aggiungere** Assembly per il pacchetto di distribuzione

- **Configurare** Autenticazione e autorizzazione

- **Pubblica** l'applicazione Web ASP.NET in Azure

## <a name="prerequisites"></a>Prerequisiti

Esaminare la sezione "recommendation generali e procedure consigliate" in [Business Intelligence di SQL Server in macchine virtuali di Azure](virtual-machines-windows-classic-ps-sql-bi.md).

>[AZURE.NOTE] Controlli ReportViewer vengono forniti con Visual Studio, Standard Edition o versione successiva. Se si utilizza Web Developer Express Edition, è necessario installare [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) per usare le funzioni di runtime ReportViewer.
>
>ReportViewer configurato in modalità di elaborazione locale non è supportata in Microsoft Azure.

Leggere il white paper [controllo Visualizzatore report di Reporting Services e macchina virtuale di Microsoft Azure basato su server di report](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx).

## <a name="adding-assemblies-to-the-deployment-package"></a>Aggiunta di assembly per il pacchetto di distribuzione

Quando si ospita l'applicazione di ASP.NET in locale, assembly ReportViewer vengono in genere installati direttamente nella cache globale (cache globale) del server IIS durante l'installazione di Visual Studio ed è possibile accedervi direttamente dall'applicazione. Tuttavia, quando si ospita l'applicazione ASP.NET nel cloud, Microsoft Azure non nulla siano installati nella cache globale, pertanto è necessario assicurarsi che assembly ReportViewer sono disponibili in locale per l'applicazione. È possibile eseguire questa operazione mediante l'aggiunta di riferimenti a tali del progetto e configurare copiati in locale.

In modalità di elaborazione remota, il controllo ReportViewer utilizza assembly riportati di seguito:

- **Microsoft.ReportViewer.WebForms.dll**: il codice ReportViewer, è necessario utilizzare ReportViewer nella pagina. Quando si trascina un controllo ReportViewer in una pagina ASP.NET di un progetto, viene aggiunto un riferimento per questo assembly al progetto.

- **Microsoft.ReportViewer.Common.dll**: contiene classi utilizzate dal controllo ReportViewer in fase di esecuzione. Non è automaticamente aggiunti al progetto.

### <a name="to-add-a-reference-to-microsoftreportviewercommon"></a>Per aggiungere un riferimento a Microsoft.ReportViewer.Common

- Pulsante destro del mouse nodo **riferimenti** del progetto e scegliere **Aggiungi riferimento**, selezionare l'assembly nella scheda .NET e fare clic su **OK**.

### <a name="to-make-the-assemblies-locally-accessible-by-your-aspnet-application"></a>Per rendere accessibile localmente assembly dall'applicazione ASP.NET

1. Nella cartella **riferimenti** fare clic su assembly Microsoft.ReportViewer.Common in modo che le relative proprietà vengono visualizzati nel riquadro delle proprietà.

1. Nel riquadro delle proprietà impostare **Copia localmente** su True.

1. Ripetere i passaggi 1 e 2 per Microsoft.ReportViewer.WebForms.

### <a name="to-get-reportviewer-language-pack"></a>Per ottenere ReportViewer Language Pack

1. Installare il pacchetto di redistributable Microsoft Report Viewer 2012 Runtime appropriato [dall'Area Download Microsoft](http://go.microsoft.com/fwlink/?LinkId=317386).

1. Selezionare la lingua nell'elenco a discesa e la pagina venga reindirizzata alla pagina interfaccia di download corrispondente.

1. Fare clic su **Download** per avviare il download di ReportViewerLP.exe.

1. Dopo avere scaricato ReportViewerLP.exe, fare clic su **Esegui** per installare immediatamente oppure fare clic su **Salva** per salvare nel computer in uso. Se si fa clic su **Salva**, tenere presente il nome della cartella in cui si salva il file.

1. Individuare la cartella in cui è stato salvato il file. Pulsante destro del mouse ReportViewerLP.exe, scegliere **Esegui come amministratore**e quindi fare clic su **Sì**.

1. Dopo aver eseguito ReportViewerLP.exe, si vedrà la c:\windows\assembly previsti della risorsa file **Microsoft.ReportViewer.Webforms.Resources** e **Microsoft.ReportViewer.Common.Resources**.

### <a name="to-configure-for-localized-reportviewer-control"></a>Per configurare per il controllo ReportViewer localizzato

1. Scaricare e installare Microsoft Report Viewer 2012 Runtime redistributable package seguendo le istruzioni riportate specificate sopra.

1. Creare <language> cartella del progetto e Copia assembly risorsa associata file non esiste. Il file assembly di risorse da copiare: **Microsoft.ReportViewer.Webforms.Resources.dll** e **Microsoft.ReportViewer.Common.Resources.dll**. Selezionare i file di risorse assembly, quindi nel riquadro Proprietà impostare **Copia nella Directory di Output** **Copia sempre"**.

1. Impostare la lingua e UICulture per il progetto web. Per ulteriori informazioni su come impostare la lingua e lingua dell'interfaccia utente per una pagina Web ASP.NET, vedere [procedura: impostare la lingua e lingua dell'interfaccia utente per la globalizzazione pagina Web ASP.NET](http://go.microsoft.com/fwlink/?LinkId=237461).

## <a name="configuring-authentication-and-authorization"></a>Configurare l'autenticazione e autorizzazioni

ReportViewer deve utilizzare le credenziali appropriate per eseguire l'autenticazione con il server di report e le credenziali devono essere autorizzate dal server di report per accedere ai report desiderato. Per informazioni sull'autenticazione, vedere il white paper [controllo Visualizzatore report di Reporting Services e macchina virtuale di Microsoft Azure basato su server di report](https://msdn.microsoft.com/library/azure/dn753698.aspx).

## <a name="publish-the-aspnet-web-application-to-azure"></a>Pubblicare l'applicazione Web ASP.NET in Azure

Per istruzioni su come pubblicare un'applicazione Web ASP.NET su Azure, vedere [come: eseguire la migrazione e pubblicare un'applicazione Web in Azure da Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) e [iniziare a utilizzare Web Apps e ASP.NET](../app-service-web/web-sites-dotnet-get-started.md).

>[AZURE.IMPORTANT] Se il comando Aggiungi progetto di distribuzione di Azure o aggiungere un progetto di servizi Cloud Azure non viene visualizzato nel menu di scelta rapida in Esplora soluzioni, potrebbe essere necessario modificare il framework di destinazione per il progetto in .NET Framework 4.
>
>I due comandi forniscono essenzialmente la stessa funzionalità. Uno o altri comandi verranno visualizzati nel menu di scelta rapida a seconda della versione di Microsoft Azure SDK sono stati installati.

## <a name="resources"></a>Risorse

[Report di Microsoft](http://go.microsoft.com/fwlink/?LinkId=205399)

[Business Intelligence di SQL Server in macchine virtuali di Azure](virtual-machines-windows-classic-ps-sql-bi.md)

[Usare PowerShell per creare una macchina virtuale Azure con un Server di Report in modalità nativa](virtual-machines-windows-classic-ps-sql-report.md)

[Creazione di report controllo Visualizzatore report Services e Microsoft Azure macchina virtuale basato su server di report](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)
