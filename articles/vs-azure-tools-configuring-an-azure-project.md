<properties
   pageTitle="Configurare un progetto di servizio Cloud Azure con Visual Studio | Microsoft Azure"
   description="Informazioni su come configurare un progetto di servizio cloud Azure in Visual Studio, in base ai propri requisiti per il progetto."
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

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurare un progetto di servizio Cloud Azure con Visual Studio

È possibile configurare un progetto di servizio cloud Azure, in base ai propri requisiti per il progetto. È possibile impostare le proprietà del progetto per le categorie seguenti:

- **Pubblicare un servizio cloud in Azure**

  È possibile impostare una proprietà per assicurarsi che un servizio cloud esistente distribuito in Azure non viene eliminato accidentalmente.

- **Esecuzione o il debug di un servizio cloud nel computer locale**

  È possibile selezionare una configurazione del servizio di indicare se si desidera avviare l'emulatore archiviazione Azure.

- **Convalidare un pacchetto di servizio cloud quando viene creato**

  È possibile considerare tutti gli avvisi come errori in modo che è possibile assicurarsi che il pacchetto di servizio cloud verrà distribuito senza problemi. Questo riduce il tempo di attesa se si distribuisce e quindi individuare che si è verificato un errore.

La figura seguente viene illustrato come selezionare una configurazione da utilizzare quando si esegue o eseguire il debug localmente il servizio cloud. È possibile impostare le proprietà del progetto che richiedono da questa finestra, come illustrato nella figura.

![Configurare un progetto di Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Per configurare un progetto di servizio cloud Azure

1. Per configurare un progetto di servizio cloud da **Esplora soluzioni**, aprire il menu di scelta rapida per il progetto di servizio cloud e quindi scegliere **proprietà**.

  Nell'editor di Visual Studio viene visualizzata una pagina con il nome del progetto di servizio cloud.

1. Scegliere la scheda **sviluppo** .

1. Per assicurarsi che si non elimina per sbaglio una distribuzione esistente in Azure, prompt dei comandi prima di eliminare un elenco di distribuzione esistente, scegliere **True**.

1. Per selezionare la configurazione del servizio che si desidera utilizzare quando si esegue o eseguire il debug localmente, il servizio cloud nell'elenco **di configurazione del servizio** scegliere la configurazione del servizio.

  >[AZURE.NOTE] Se si desidera creare una configurazione del servizio da utilizzare, vedere Procedura: gestire le configurazioni dei servizi e dei profili. Se si desidera modificare una configurazione del servizio per un ruolo, vedere [come configurare i ruoli per un servizio cloud Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Per avviare l'archiviazione Azure emulatore quando si esegue o eseguire il debug localmente, il servizio cloud nell' **emulatore lo spazio di archiviazione di Azure Start**, scegliere **True**.

1. Per assicurarsi che non è possibile pubblicare se sono presenti errori di convalida pacchetto, in **Considera gli avvisi come errori**, scegliere **True**.

1. Per assicurarsi che il proprio ruolo web utilizza la stessa porta ogni volta che viene avviato in locale in IIS Express nell' **utilizzo di porte di project web**, scegliere **True**. Per utilizzare una porta specifica per un progetto web specifico, aprire il menu di scelta rapida per il progetto web, scegliere la scheda **proprietà** , fare clic sulla scheda **Web** e modificare il numero di porta nell'opzione **Url Project** nella sezione **IIS Express** . Ad esempio, immettere `http://localhost:14020` come l'URL di project.

1. Per salvare le modifiche apportate alle proprietà del progetto di servizio cloud, scegliere il pulsante **Salva** sulla barra degli strumenti.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come configurare i progetti di servizi cloud Azure in Visual Studio, vedere [configurazione di Azure progetto usando più configurazioni di servizio](vs-azure-tools-multiple-services-project-configurations.md).
