<properties 
   pageTitle="Debug di un servizio cloud pubblicato con traccia di diagnostica e Visual Studio | Microsoft Azure"
   description="Debug di un servizio cloud pubblicato con traccia di diagnostica e Visual Studio"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Debug di un servizio cloud pubblicato con traccia di diagnostica e Visual Studio

##<a name="overview"></a>Panoramica

Con IntelliTrace, è possibile registrare le informazioni di debug complete per un'istanza del ruolo durante l'esecuzione in Azure. Se è necessario individuare la causa del problema, è possibile utilizzare i log di IntelliTrace per scorrere il codice da Visual Studio come se venisse eseguita nello Azure. In effetti, IntelliTrace record principali esecuzione di codice e dati dell'ambiente quando l'applicazione Azure è in esecuzione come un servizio cloud di Azure e consente di riprodurre i dati registrati da Visual Studio. In alternativa, è possibile utilizzare il debug remoto per allegare direttamente a un servizio cloud in esecuzione in Azure. Vedere [debug servizi Cloud](http://go.microsoft.com/fwlink/p/?LinkId=623041).

>[AZURE.IMPORTANT] IntelliTrace è destinato solo gli scenari di debug e non deve essere utilizzato per una distribuzione di produzione.

>[AZURE.NOTE] È possibile utilizzare IntelliTrace se si dispone di Visual Studio Enterprise installato e le destinazioni dell'applicazione Azure .NET Framework 4 o versione successiva. IntelliTrace raccoglie informazioni per i ruoli Azure. Macchine virtuali per i ruoli sempre eseguite per sistemi operativi a 64 bit.

## <a name="to-configure-an-azure-application-for-intellitrace"></a>Per configurare un'applicazione Azure per IntelliTrace

Per abilitare per un'applicazione Azure, è necessario creare e pubblicare l'applicazione di un progetto di Visual Studio Azure. È necessario configurare IntelliTrace per l'applicazione Azure pubblicarla in Azure. Se si pubblica l'applicazione senza configurare IntelliTrace ma si decide che si desidera eseguire questa operazione, sarà necessario pubblicare l'applicazione nuovamente da Visual Studio. Per ulteriori informazioni, vedere [pubblicazione di un servizio Cloud usando gli strumenti di Azure](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Quando si è pronti distribuire l'applicazione Azure, verificare che il progetto è destinato generatori sta impostato per **eseguire il Debug**.

1. Aprire il menu di scelta rapida per il progetto Azure in Esplora soluzioni e scegliere **pubblica**.
 
    Verrà visualizzata la creazione guidata pubblicazione applicazione Azure.

1. Per raccogliere i registri di IntelliTrace per l'applicazione quando viene pubblicato nel cloud, selezionare la casella di controllo **Abilita IntelliTrace** .

    >[AZURE.NOTE] È possibile abilitare IntelliTrace o analisi quando si pubblica l'applicazione Azure. Non è possibile abilitare entrambe.

1. Per personalizzare la configurazione di IntelliTrace base, scegliere il collegamento ipertestuale **Impostazioni** .

    Viene visualizzata la finestra di dialogo Impostazioni di IntelliTrace, come illustrato nella figura seguente. È possibile specificare quali eventi a un log, se si desidera raccogliere informazioni sulla chiamata, quali moduli e i processi per raccogliere i registri per e la quantità di spazio da allocare per la registrazione. Per ulteriori informazioni su IntelliTrace, vedere [debug con IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).

    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Il log di IntelliTrace è un file di log circolare della dimensione massima specificata nelle impostazioni IntelliTrace (dimensione predefinita è 250 MB). Log di IntelliTrace vengono raccolte in un file nel file system della macchina virtuale. Quando si richiedono i log, uno snapshot è acquisito in tale momento e scaricato nel computer locale.

Dopo l'applicazione Azure è stato pubblicato in Azure, è possibile determinare se è stata attivata IntelliTrace dal nodo Azure calcolare in Esplora Server, come illustrato nell'immagine seguente:

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Download dei registri IntelliTrace per un'istanza del ruolo

È possibile scaricare dei registri IntelliTrace per un'istanza del ruolo dal nodo **Servizi Cloud** in **Esplora Server**. Espandere il nodo **Servizi Cloud** fino a individuare l'istanza che si è interessati, aprire il menu di scelta rapida per questa istanza e scegliere **Visualizza i registri di IntelliTrace**. I log di IntelliTrace vengono scaricati in un file in una directory nel computer locale. Ogni volta che si richiede la IntelliTrace registri, viene creato un nuovo snapshot.

Quando vengono scaricati i log, Visual Studio visualizza lo stato di avanzamento dell'operazione nella finestra registro attività Azure. Come mostrato nella figura seguente, è possibile espandere la voce per l'operazione visualizzare più dettagli.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

È possibile continuare a lavorare in Visual Studio durante il download di log IntelliTrace. Quando il log di termine del download, verrà automaticamente aperto in Visual Studio.

>[AZURE.NOTE] I log di IntelliTrace possono contenere eccezioni che il framework genera e successivamente gestisce. Codice struttura interna genera queste eccezioni durante un normale avvio di un ruolo, in modo che può essere ignorato.

## <a name="see-also"></a>Vedere anche

[Il debug di servizi Cloud](https://msdn.microsoft.com/library/ee405479.aspx)

