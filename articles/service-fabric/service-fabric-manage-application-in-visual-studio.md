<properties
   pageTitle="Gestire le applicazioni in Visual Studio | Microsoft Azure"
   description="Utilizzare Visual Studio per creare, sviluppare, creare un pacchetto, distribuzione e debug i servizi e applicazioni di servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilizzare Visual Studio per semplificare la scrittura e la gestione delle applicazioni di servizio tessuti

È possibile gestire le applicazioni di Azure servizio tessuti e ai relativi servizi tramite Visual Studio. Dopo aver [configurato l'ambiente di sviluppo](service-fabric-get-started.md), è possibile utilizzare Visual Studio per creare le applicazioni di servizio tessuti, aggiungere servizi o creare un pacchetto, registro e distribuire le applicazioni del cluster di sviluppo locale.

## <a name="deploy-your-service-fabric-application"></a>Distribuire l'applicazione di servizio tessuti

Per impostazione predefinita, la distribuzione di un'applicazione combina i passaggi seguenti in un'operazione semplice:

1. Creazione del pacchetto dell'applicazione
2. Durante il caricamento del pacchetto dell'applicazione per l'archivio di immagini
3. Registrare il tipo di applicazione
4. Rimuovere le istanze dell'applicazione in esecuzione
5. Creare una nuova istanza dell'applicazione

In Visual Studio, premendo **F5** verrà inoltre distribuire l'applicazione e il debug a tutte le istanze dell'applicazione. È possibile usare **Ctrl + F5** per distribuire un'applicazione senza eseguire il debug oppure è possibile pubblicare in un cluster locale o remoto utilizzando il profilo di pubblicazione. Per ulteriori informazioni, vedere [pubblicazione di un'applicazione di un cluster remoto utilizzando Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Modalità di Debug dell'applicazione

Per impostazione predefinita, verrà rimosso esistente istanze del tipo di applicazione quando si interrompe il debug o si (se si distribuisce l'app senza associazione verrà), quando si ridistribuire l'applicazione. In questo caso, vengono rimossi tutti i dati dell'applicazione. Durante il debug in locale, è possibile mantenere i dati che si sono già creato quando si verifica una nuova versione dell'applicazione, si desidera mantenere l'applicazione in esecuzione o si desidera che le sessioni di debug successive di eseguire l'aggiornamento dell'applicazione. Strumenti di Visual Studio servizio tessuti fornire una proprietà denominata **Modalità Debug applicazione**, quali controlli se **F5** necessario disinstallare l'applicazione, mantengono l'applicazione in esecuzione dopo una sessione di debug termina o consentire all'applicazione di aggiornati sulle sessioni di debug successive, anziché rimosso e ridistribuito.

#### <a name="to-set-the-application-debug-mode-property"></a>Per impostare la proprietà modalità Debug applicazione

1. Dal menu di scelta rapida del progetto di applicazione, scegliere **proprietà** (o premere **F4** ).
2. Nella finestra delle **proprietà** impostare la proprietà **Modalità Debug applicazione** .

    ![Impostare proprietà modalità Debug applicazione][debugmodeproperty]

Questi sono disponibili le opzioni della **Modalità di Debug dell'applicazione** .

1. **Eseguire l'aggiornamento automatico**: l'applicazione continua a funzionare al termine della sessione di debug. Successiva **F5** verrà considerato la distribuzione di un aggiornamento utilizzando la modalità automatico automatico per l'aggiornamento rapido un'applicazione di una versione più recente con una stringa di data aggiunta. Il processo di aggiornamento consente di mantenere tutti i dati che è stata immessa una sessione di debug precedente.

2. **Mantenere applicazione**: l'applicazione continua a funzionare normalmente del cluster al termine della sessione di debug. Nella prossima **F5** verrà rimossi l'applicazione e l'applicazione appena creato verrà distribuito al cluster.

3. **Rimuovi applicazione** , l'applicazione da rimuovere al termine della sessione di debug.

Per **Eseguire l'aggiornamento automatico** dei dati viene mantenuti applicando le funzionalità di aggiornamento dell'applicazione del servizio tessuti, ma è ottimizzato per ottimizzare le prestazioni invece di sicurezza. Per ulteriori informazioni sull'aggiornamento di applicazioni e come è possibile eseguire l'aggiornamento in un ambiente reale, vedere [aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md).

![Esempio della nuova versione dell'applicazione con data][preservedata]

>[AZURE.NOTE] Questa proprietà non esiste prima versione 1.1 degli strumenti di tessuti di servizio per Visual Studio. Prima di 1.1, utilizzare la proprietà **Mantieni dati avvio** per ottenere lo stesso comportamento. L'opzione "Mantieni applicazione" è stata introdotta nella versione 1.2 degli strumenti tessuti servizio per Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Aggiungere un servizio nell'applicazione di servizio tessuti

È possibile aggiungere nuovi servizi per l'applicazione per estendere la funzionalità.  Per assicurarsi che il servizio è incluso nel pacchetto dell'applicazione, aggiungere il servizio mediante la voce di menu **Nuovo servizio tessuti...** .

![Aggiungere un nuovo servizio tessuti all'applicazione][newservice]

Selezionare un tipo di progetto tessuti servizio da aggiungere all'applicazione e specificare un nome per il servizio.  Vedere [scelta di un framework per il servizio](service-fabric-choose-framework.md) per decidere il tipo di servizio da utilizzare.

![Selezionare un tipo di progetto tessuti servizio da aggiungere all'applicazione][addserviceproject]

Il nuovo servizio verrà aggiunto alla soluzione e pacchetto di applicazione esistente. I riferimenti di servizio e un'istanza di servizio predefinita verrà aggiunto al manifesto dell'applicazione. Il servizio verrà creato e iniziare la volta successiva che si distribuisce l'applicazione.

![Il nuovo servizio verrà aggiunto al manifesto dell'applicazione][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Creare un pacchetto dell'applicazione di servizio tessuti

Per distribuire l'applicazione e i servizi a un cluster, è necessario creare un pacchetto dell'applicazione.  Consente di organizzare il pacchetto manifesto dell'applicazione, manifesti del servizio e altri file necessari in un layout specifico.  Visual Studio imposta e gestisce il pacchetto nella cartella del progetto dell'applicazione nella directory 'pkg'.  Fare clic su **pacchetto** dal menu di scelta rapida **dell'applicazione** crea o aggiorna il pacchetto dell'applicazione.  Può essere necessario eseguire questa operazione se si distribuisce l'applicazione tramite script di PowerShell personalizzati.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Rimuovere le applicazioni e i tipi di applicazione tramite Esplora Cloud

È possibile eseguire le operazioni di gestione di base cluster da in Visual Studio utilizzando Cloud Explorer, è possibile avviare dal menu **Visualizza** . Ad esempio, è possibile eliminare le applicazioni e il provisioning di tipi di applicazioni nei cluster locale o remoto.

![Rimuovere un'applicazione](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Per funzionalità di gestione di più complete cluster, vedere [la visualizzazione del cluster con Esplora risorse tessuti servizio](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

- [Modello di applicazione di servizio tessuti](service-fabric-application-model.md)
- [Distribuzione di applicazioni di servizio tessuti](service-fabric-deploy-remove-applications.md)
- [Gestione dei parametri di applicazione per gli ambienti più](service-fabric-manage-multiple-environment-app-configuration.md)
- [Il debug dell'applicazione di servizio tessuti](service-fabric-debugging-your-application.md)
- [Visualizzare il cluster usando Esplora file tessuti di servizio](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
