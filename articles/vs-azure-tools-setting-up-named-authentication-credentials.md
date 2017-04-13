<properties
   pageTitle="La configurazione denominata le credenziali di autenticazione | Microsoft Azure"
   description="Informazioni su come per specificare le credenziali da Visual Studio possono usare autenticare le richieste di Azure per pubblicare un'applicazione in Azure da Visual Studio o per monitorare un servizio cloud esistente.. "
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

# <a name="setting-up-named-authentication-credentials"></a>Configurare le credenziali di autenticazione denominato

Per pubblicare un'applicazione in Azure da Visual Studio o per monitorare un servizio cloud esistente, è necessario specificare le credenziali che Visual Studio consente di eseguire l'autenticazione le richieste di Azure. Esistono diverse posizioni in Visual Studio nel punto in cui è possibile accedere a queste credenziali. Ad esempio, da Esplora Server è possibile aprire il menu di scelta rapida per il nodo **Azure** e scegliere **Connetti a Azure**. Quando accede, le informazioni di sottoscrizione associate al proprio account Azure sono disponibile in Visual Studio e altre che occorre fare nulla.

Strumenti di Azure supporta anche un modo meno recente di fornire le credenziali, utilizzando il file di sottoscrizione (publishsettings file). In questo argomento descrive questo metodo è ancora supportato in Azure SDK 2.2.

Gli elementi seguenti sono necessari per l'autenticazione in Azure.

- L'ID di abbonamento

- Certificato valido di x. 509 v3

>[AZURE.NOTE] La lunghezza della chiave del certificato x. 509 v3 deve essere di almeno 2048 bit. Azure Rifiuta qualsiasi certificato che non soddisfa questo requisito o che non è valido.

Visual Studio utilizza l'ID di abbonamento insieme ai dati del certificato come credenziali. Le credenziali appropriate vengono fatto riferimento nel file di sottoscrizione (publishsettings file) che contiene una chiave pubblica per il certificato. Il file di sottoscrizione può contenere le credenziali per più di una sottoscrizione.

È possibile modificare le informazioni di sottoscrizione nella finestra di dialogo **Nuovo/Edit abbonamento** , come spiegheremo più avanti in questo argomento.

Se si desidera creare un certificato manualmente, è possibile vedere le istruzioni in [creare e caricare un certificato di gestione per Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e quindi caricare manualmente il certificato al [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Le credenziali necessarie Visual Studio per gestire i servizi cloud non sono le stesse credenziali necessarie per eseguire l'autenticazione di una richiesta di servizi di archiviazione Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modificare o esportare le credenziali di autenticazione in Visual Studio

È anche possibile impostare, modificare o esportare le credenziali di autenticazione nella finestra di dialogo **Nuovo abbonamento** viene visualizzato se si esegue una delle operazioni seguenti:

- In **Esplora Server**, aprire il menu di scelta rapida per il nodo **Azure** , scegliere **Gestione delle sottoscrizioni**, scegliere la scheda **certificati** e fare clic sul pulsante **Nuovo** o **Modifica** .

- Quando si pubblica un servizio cloud Azure dalla creazione guidata **Pubblicazione applicazione Azure** , scegliere **Manage** nell'elenco **Selezionare l'abbonamento** , quindi scegliere la scheda certificati e fare clic sul pulsante **Nuovo** o **Modifica** .

La procedura seguente presuppone che sia aperta nella finestra di dialogo **Nuova sottoscrizione** .

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Per impostare le credenziali di autenticazione in Visual Studio

1. In **Selezionare un certificato esistente** per l'elenco di autenticazione, scegliere un certificato.

1. Fare clic sul pulsante **Copia il percorso completo** . Il percorso per il certificato (file CER) viene copiato negli Appunti.

    >[AZURE.IMPORTANT] Per pubblicare l'applicazione Azure mediante Visual Studio, è necessario caricare il certificato di [Azure portale classica](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Per caricare il certificato al [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Selezionare il collegamento portale Azure.

         Verrà visualizzata la finestra di [Azure portale classica](http://go.microsoft.com/fwlink/?LinkID=213885) .

    1. Accedere al [portale classica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e quindi fare clic sul pulsante **Servizi Cloud** .

    1. Scegliere il servizio cloud che si è interessati.

        Verrà visualizzata la pagina per il servizio.

    1. Nella scheda **certificati** scegliere il pulsante **Carica** .

    1. Incollare il percorso completo del file CER appena creata e quindi immettere la password specificata.
