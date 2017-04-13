<properties 
   pageTitle="Accesso cloud Azure privati con Visual Studio | Microsoft Azure"
   description="Informazioni su come accedere alle risorse cloud privato utilizzando Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-private-azure-clouds-with-visual-studio"></a>Accesso cloud Azure privati con Visual Studio

##<a name="overview"></a>Panoramica

Per impostazione predefinita, Visual Studio supporta i punti finali resto cloud Azure pubblico. Può trattarsi di un problema, tuttavia, se si sta utilizzando Visual Studio con il cloud Azure privato. È possibile utilizzare i certificati per configurare Visual Studio per accedere a endpoint resto cloud Azure privato. È possibile ottenere questi certificati tramite il Azure pubblicano file di impostazioni.

## <a name="to-access-a-private-azure-cloud-in-visual-studio"></a>Per accedere a un Azure private cloud in Visual Studio

1. Nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) per il cloud privato, scaricare il file di impostazioni di pubblicazione oppure contattare l'amministratore per un file di impostazioni di pubblicazione. Nella versione pubblica di Azure, il collegamento per scaricare questo è [https://manage.windowsazure.com/publishsettings/](https://manage.windowsazure.com/publishsettings/). (Il file scaricato deve avere un'estensione publishsettings).

1. In **Esplora Server** di Visual Studio, scegliere il nodo **Azure** e, nel menu di scelta rapida, scegliere il comando di **Gestione delle sottoscrizioni** .

    ![Gestire il comando abbonamenti](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790778.png)

1. Nella finestra di dialogo **Gestione delle sottoscrizioni di Microsoft Azure** fare clic sulla scheda **certificati** e quindi fare clic su **Importa** .

    ![Importazione di certificati Azure](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790779.png)

1. Nella finestra di dialogo **Importa Microsoft Azure abbonamenti** , passare alla cartella in cui è stato salvato il file di impostazioni di pubblicazione e scegliere il file, quindi scegliere il pulsante **Importa** . Consente di importare i certificati nel file di impostazioni pubblica in Visual Studio. È ora possibile interagire con le risorse cloud privato.

    ![Importare le impostazioni di pubblicazione](./media/vs-azure-tools-access-private-azure-clouds-with-visual-studio/IC790780.png)

## <a name="next-steps"></a>Passaggi successivi

[Pubblicazione in un servizio Cloud Azure da Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

[Procedura: scaricare e importare pubblica impostazioni e informazioni sull'abbonamento](https://msdn.microsoft.com/library/dn385850(v=nav.70).aspx)

