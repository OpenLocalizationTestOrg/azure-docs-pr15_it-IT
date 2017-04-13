<properties
   pageTitle="Come mantenere un indirizzo IP virtuale costante di un servizio cloud | Microsoft Azure"
   description="Informazioni su come assicurarsi che l'indirizzo IP virtuale (VIP) del servizio cloud Azure non cambia."
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

# <a name="how-to-retain-a-constant-virtual-ip-address-for-a-cloud-service"></a>Come mantenere un indirizzo IP virtuale costante di un servizio cloud

Quando si aggiorna un servizio cloud ospitato in Azure, potrebbe essere necessario accertarsi che l'indirizzo IP virtuale (VIP) del servizio non cambia. Molti servizi di gestione dominio utilizzano il sistema DNS (Domain Name) per la registrazione dei nomi di dominio. DNS funziona solo se l'indirizzo VIP rimarrà invariato. È possibile usare la **Pubblicazione guidata** di Azure Tools per assicurare che l'indirizzo VIP del servizio cloud non viene modificata quando si aggiorna. Per ulteriori informazioni sull'utilizzo di gestione del dominio DNS per i servizi cloud, vedere [configurazione di un nome di dominio personalizzato per un servizio cloud Azure](./cloud-services/cloud-services-custom-domain-name.md).

## <a name="publishing-a-cloud-service-without-changing-its-vip"></a>Pubblicazione di un servizio cloud senza modificare l'indirizzo VIP

L'indirizzo VIP del servizio cloud allocata quando è prima di tutto distribuirlo in Azure in un ambiente specifico, ad esempio l'ambiente di produzione. Non modifica l'indirizzo VIP a meno che non si elimina la distribuzione in modo esplicito o viene eliminato in modo implicito dal processo di aggiornamento di distribuzione. Per mantenere l'indirizzo VIP, non è necessario eliminare la distribuzione e verificare che Visual Studio non si elimina la distribuzione automatica. È possibile controllare il comportamento specificando le impostazioni di distribuzione della **Pubblicazione guidata**che supporta diverse opzioni di distribuzione. È possibile specificare una distribuzione aggiornata o una distribuzione di aggiornamento, che può essere simultaneo o incrementali, ed entrambi i tipi di aggiornamento distribuzioni conservare l'indirizzo VIP. Per le definizioni di questi tipi diversi di distribuzione, vedere [Pubblicazione guidata applicazione Azure](vs-azure-tools-publish-azure-application-wizard.md).  Inoltre, è possibile controllare se la distribuzione precedente di un servizio cloud viene eliminata se si verifica un errore. L'indirizzo VIP potrebbero essere modificati in modo imprevisto se non si imposta l'opzione correttamente.

### <a name="to-update-a-cloud-service-without-changing-its-vip"></a>Per aggiornare un servizio cloud senza modificare l'indirizzo VIP

1. Dopo aver distribuito almeno una volta al servizio cloud, aprire il menu di scelta rapida per il nodo del progetto di Azure e quindi scegliere **pubblica**. Verrà visualizzata la creazione guidata **Pubblicazione applicazione Azure** .

1. Nell'elenco delle sottoscrizioni, scegliere quello a cui si desidera distribuire e fare clic sul pulsante **Avanti** . Verrà visualizzata la pagina di **Impostazioni** della procedura guidata.

1. Nella scheda **Impostazioni comuni** , verificare che il nome del servizio cloud a cui si distribuisce, l' **ambiente**, **Creare configurazione**e la **Configurazione del servizio** siano corrette tutti.

1. Nella scheda **Impostazioni avanzate** , verificare che l'account di archiviazione e l'etichetta di distribuzione siano corretti, che la casella di controllo **Elimina distribuzione in caso di errore** sia deselezionata e che sia selezionata la casella di controllo aggiornamento di **distribuzione** . Selezionando la casella di controllo aggiornamento di **distribuzione** , assicurarsi che la distribuzione non verrà eliminata e l'indirizzo VIP non andranno perse quando si ripubblica l'applicazione. Deselezionando **eliminare la distribuzione nella casella di controllo di errore**, verificare che l'indirizzo VIP non andranno perse se si verifica un errore durante l'installazione.

1. Per specificare ulteriormente la modalità di ruoli da aggiornare, scegliere il collegamento **Impostazioni** accanto alla casella **distribuzione aggiornamenti** e quindi scegliere l'opzione di aggiornamento simultaneo o aggiornamento incrementale nella finestra di dialogo Impostazioni **di distribuzione aggiornamenti** . Se si sceglie aggiornamento incrementale, ogni istanza viene aggiornata una dopo l'altra, in modo che l'applicazione sia sempre disponibile. Se si sceglie aggiornamento simultaneo, verranno aggiornate tutte le istanze nello stesso momento. Aggiornamento simultaneo è veloce ma il servizio potrebbe non essere disponibile durante il processo di aggiornamento.

1. Quando si è soddisfatti delle impostazioni, fare clic sul pulsante **Avanti** .

1. Nella pagina di **Riepilogo** della procedura guidata, verificare le impostazioni e quindi fare clic su **pubblica** .

  >[AZURE.WARNING] Se la distribuzione non riesce, deve soddisfare perché non è riuscito e ridistribuire esauriente, per non lasciare il servizio cloud in uno stato danneggiato.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla pubblicazione in Azure da Visual Studio, vedere [Creazione guidata applicazione Azure pubblica](vs-azure-tools-publish-azure-application-wizard.md).
