<properties
   pageTitle="Con emulatore Express esecuzione e il debug di un servizio cloud in un computer locale | Microsoft Azure"
   description="Con emulatore Express esecuzione e il debug di un servizio cloud in un computer locale"
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


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Con emulatore Express esecuzione e il debug di un servizio cloud in un computer locale

Utilizzando emulatore Express, è possibile testare e il debug di un servizio cloud senza eseguire Visual Studio come amministratore. È possibile impostare le impostazioni del progetto da usare emulatore Express o emulatore completo, in base ai requisiti del servizio cloud. Per ulteriori informazioni sull'emulatore completo, vedere [eseguire un'applicazione di Azure nell'emulatore di calcolo](./storage/storage-use-emulator.md). Emulatore Express è stato incluso prima Azure SDK 2.1 e alla 2.3 SDK Azure, è l'emulatore predefinito.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Utilizzo di emulatore Express di Visual Studio IDE

Quando si crea un nuovo progetto in Azure SDK 2.3 o versione successiva, emulatore Express è già selezionata. Per i progetti esistenti che sono stati creati con una versione precedente di SDK, seguire questa procedura per selezionare emulatore Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Per configurare un progetto per l'utilizzo emulatore Express

1. Dal menu di scelta rapida per il progetto Azure, scegliere **proprietà**e quindi scegliere la scheda **Web** .

1. In **Server di sviluppo locale**, scegliere il pulsante di **opzione Usa IIS Express** . Emulatore Express non è compatibile con Server Web IIS.

1. In **emulatore**, scegliere il pulsante di opzione **Usa emulatore Express** .

    ![Emulatore Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Avvio emulatore Express un prompt dei comandi

Al prompt dei comandi, è possibile avviare la versione express di emulatore di calcolo Azure, csrun.exe, utilizzando l'opzione /useemulatorexpress.

## <a name="limitations"></a>Limitazioni

Prima di utilizzare emulatore Express, è necessario essere consapevoli di alcune limitazioni:

- Emulatore Express non è compatibile con Server Web IIS.

- Il servizio cloud può contenere più ruoli, ma ogni ruolo è limitato a una sola istanza.

- Non è possibile accedere i numeri di porta inferiori a 1000. Ad esempio, se si usa un provider di autenticazione che di solito utilizza una porta inferiore a 1000, potrebbe essere necessario cambiare questo valore su un numero di porta che si trova sopra 1000.

- Eventuali limitazioni relative ad emulatore di calcolo Azure applicano anche a emulatore Express. È ad esempio, non può contenere più di 50 istanze del ruolo per ogni distribuzione. Vedere [esecuzione di un'applicazione Azure nell'emulatore di calcolo](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Passaggi successivi

[Il debug di servizi Cloud](https://msdn.microsoft.com/library/azure/ee405479.aspx)
