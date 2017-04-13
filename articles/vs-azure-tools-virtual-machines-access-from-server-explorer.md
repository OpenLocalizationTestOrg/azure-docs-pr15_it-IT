<properties
   pageTitle="Accesso a macchine virtuali di Azure da Esplora Server | Microsoft Azure"
   description="Ottenere una panoramica su come visualizzare, creare e gestire Azure macchine () in Esplora Server in Visual Studio."
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

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Accesso a macchine virtuali di Azure da Esplora Server

Tramite Esplora Server in Visual Studio, è possibile visualizzare informazioni sulle macchine virtuali ospitate da Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Accesso a macchine virtuali in Esplora Server

Se si dispone di macchine virtuali ospitate da Azure, è possibile accedervi in Esplora Server. È necessario prima di tutto accedere all'abbonamento Azure per visualizzare il provider di servizi per dispositivi mobili. Per accedere, aprire il menu di scelta rapida per il nodo Azure in Esplora Server e scegliere **Connetti a Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Per informazioni sulle macchine virtuali

1. In Esplora Server, scegliere una macchina virtuale e quindi scegliere F4 per visualizzare la finestra Proprietà.

    Nella tabella seguente vengono illustrate le proprietà disponibili, ma sono tutte di sola lettura. Per modificarli, usare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Proprietà|Descrizione|
  	|---|---|
  	|Nome DNS|L'URL che contiene l'indirizzo Internet della macchina virtuale.|
  	|Ambiente|Per una macchina virtuale, il valore di questa proprietà è sempre produzione.|
  	|Nome|Il nome della macchina virtuale.|
  	|Dimensioni|Le dimensioni della macchina virtuale che indica la quantità di spazio su disco che è disponibile. Per ulteriori informazioni, vedere How To: configurare le dimensioni di macchina virtuale.|
  	|Stato|Valori includono iniziale, introduzione, arresto, arrestato e il recupero di stato. Se viene visualizzato lo stato di recupero, lo stato corrente è sconosciuto. I valori per questa proprietà variano dai valori utilizzati nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|ID abbonamento per il proprio account Azure. È possibile visualizzare queste informazioni nel [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) , visualizzare le proprietà per una sottoscrizione.|

1. Scegliere un nodo endpoint e quindi visualizzare la finestra **proprietà** .

1. Nella tabella seguente sono descritte le proprietà disponibili dell'endpoint, ma sono di sola lettura. Per aggiungere o modificare i punti finali per una macchina virtuale, utilizzare il [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Proprietà|Descrizione|
  	|---|---|
  	|Nome|Un identificatore per l'endpoint.|
  	|Porta privata|Porta per accesso alla rete interna all'applicazione.|
  	|Protocollo|Il protocollo utilizzato il livello di trasporto per l'endpoint, TCP o UDP.|
  	|Porta pubblica|Porta utilizzata per l'accesso pubblico per l'applicazione.|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'uso di ruoli Azure in Visual Studio, vedere [Utilizzo di Desktop remoto con i ruoli di Azure](vs-azure-tools-remote-desktop-roles.md).
