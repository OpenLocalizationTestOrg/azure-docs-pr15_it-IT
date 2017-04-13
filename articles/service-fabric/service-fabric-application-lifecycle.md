<properties
   pageTitle="Ciclo di vita di applicazione di servizio tessuti | Microsoft Azure"
   description="Descrive lo sviluppo, distribuzione, test, l'aggiornamento, gestione e la rimozione delle applicazioni di servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="service-fabric-application-lifecycle"></a>Ciclo di vita applicazione servizio tessuti
Come con altre piattaforme, un'applicazione su Azure servizio tessuti favore le seguenti fasi: progettazione, sviluppo, test, distribuzione, l'aggiornamento, manutenzione e la rimozione. Servizio tessuti fornisce supporto di livello superiore per il ciclo di vita applicazione completa di applicazioni cloud, da sviluppo attraverso la distribuzione, gestione quotidiana e la manutenzione di disattivazione finale. Il modello dei servizi consente diversi ruoli di partecipare in modo indipendente nel ciclo di vita dell'applicazione. In questo articolo viene fornita una panoramica delle API e su come vengono utilizzate dai diversi ruoli in tutte le fasi del ciclo di vita dell'applicazione del servizio tessuti.

## <a name="service-model-roles"></a>Ruoli del modello di servizio
I ruoli di modello di servizio sono:

- **Lo sviluppatore del servizio**: sviluppa servizi modulari e generici che possono essere nuovamente illustrativi e utilizzati in più applicazioni dello stesso tipo o tipi diversi. Ad esempio, è possibile utilizzare un servizio di coda per la creazione di un'applicazione di ticket (helpdesk) o un'applicazione di e-commerce (carrello).

- **Sviluppo di applicazioni**: crea applicazioni mediante l'integrazione di un insieme di servizi per soddisfare alcuni requisiti specifici o scenari. Ad esempio un sito Web e-commerce può integrare "JSON di servizio front-end senza informazioni sullo stato", "Servizio informazioni sullo stato asta" e "Servizio informazioni sullo stato coda" per creare una soluzione auctioning.

- **Amministratore dell'applicazione**: decisioni sulla configurazione dell'applicazione (inserendo i parametri di modello configurazione), distribuzione (mapping alle risorse disponibili) e la qualità del servizio. Ad esempio, un amministratore dell'applicazione decide la lingua (in inglese per gli Stati Uniti) o giapponese per il Giappone, ad esempio dell'applicazione. Un'altra applicazione distribuita può avere diverse impostazioni.

- **Operatore**: distribuisce applicazioni basate su configurazione dell'applicazione ed esigenze specificate dall'amministratore dell'applicazione. Ad esempio, un operatore disposizioni e distribuisce l'applicazione e garantisce che sia in esecuzione in Azure. Operatori di monitorare le informazioni di salute e le prestazioni dell'applicazione e gestire l'infrastruttura fisica in base alle esigenze.


## <a name="develop"></a>Sviluppare
1. Uno *sviluppatore del servizio* sviluppa diversi tipi di servizi utilizzando il modello di programmazione [Affidabile operatori](service-fabric-reliable-actors-introduction.md) o [Servizi affidabili](service-fabric-reliable-services-introduction.md) .
2. Uno *sviluppatore del servizio* in modo dichiarativo descrive i tipi di servizio in un file manifesto servizio composta da uno o più pacchetti di codice, configurazione e i dati.
3. Uno *sviluppatore di applicazioni* quindi crea un'applicazione di utilizzo dei tipi di servizi diversa.
4. Uno *sviluppatore dell'applicazione* in modo dichiarativo descrive il tipo di applicazione in un manifesto facendo riferimento manifesti servizio di servizi costitutivi in modo appropriato si esegue l'override e parametri alle diverse impostazioni di configurazione e distribuzione dei servizi costitutivi.

Per esempi, vedere [Guida introduttiva a operatori affidabile](service-fabric-reliable-actors-get-started.md) e [iniziare a utilizzare servizi affidabili](service-fabric-reliable-services-quick-start.md) .

## <a name="deploy"></a>Distribuire
1. Un *amministratore dell'applicazione* suggerite il tipo di applicazione di un'applicazione specifica per essere distribuito in un cluster di servizio tessuti specificando i parametri appropriati dell'elemento **ApplicationType** nel manifesto dell'applicazione.

2. Un *operatore* consente di caricare il pacchetto dell'applicazione utilizzando il [metodo **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o [ **Copia ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt125905.aspx)archivio immagini cluster. Pacchetto dell'applicazione contiene la raccolta di pacchetti e manifesto dell'applicazione. Servizio tessuti distribuisce applicazioni dal pacchetto dell'applicazione memorizzate nell'archivio immagine, che può essere un archivio blob Azure o il servizio di sistema servizio tessuti.

3. L' *operatore* disposizioni quindi il tipo di applicazione del cluster di destinazione dal pacchetto dell'applicazione caricati utilizzando il [metodo **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), il [ **Registro ServiceFabricApplicationType** cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)o l' [operazione di resto **disposizione un'applicazione** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

4. Dopo il provisioning dell'applicazione, un *operatore* avvia l'applicazione con i parametri specificati dall' *amministratore dell'applicazione* utilizzando il [metodo **CreateApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.createapplicationasync.aspx), il [cmdlet **New-ServiceFabricApplication** ](https://msdn.microsoft.com/library/azure/mt125913.aspx)o l' [operazione di resto **Crea applicazione** ](https://msdn.microsoft.com/library/azure/dn707676.aspx).

5. Dopo l'applicazione è stato distribuito, un *operatore* utilizza il [metodo **CreateServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.createserviceasync.aspx), il [cmdlet **New-ServiceFabricService** ](https://msdn.microsoft.com/library/azure/mt125874.aspx)o l' [operazione di resto **Create Service** ](https://msdn.microsoft.com/library/azure/dn707657.aspx) per creare nuove istanze del servizio per l'applicazione in base ai tipi di servizi disponibili.

6. L'applicazione è in esecuzione nel cluster servizio tessuti.

Per esempi, vedere [distribuire un'applicazione](service-fabric-deploy-remove-applications.md) .

## <a name="test"></a>Test
1. Dopo aver distribuito il cluster di sviluppo locale o a un cluster di test, uno *sviluppatore del servizio* viene eseguito lo scenario di test failover incorporati utilizzando le classi [**FailoverTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenarioparameters.aspx) e [**FailoverTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.failovertestscenario.aspx) o [ **ServiceFabricFailoverTestScenario richiama** cmdlet](https://msdn.microsoft.com/library/azure/mt644783.aspx). Scenario di test di failover viene eseguito un servizio specificato tramite le transizioni importanti e failover per assicurarsi che sia ancora disponibile e funzionante.

2. Lo *sviluppatore del servizio* eseguirà lo scenario di test chaos predefiniti con le classi [**ChaosTestScenarioParameters**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenarioparameters.aspx) e [**ChaosTestScenario**](https://msdn.microsoft.com/library/azure/system.fabric.testability.scenario.chaostestscenario.aspx) o [ **ServiceFabricChaosTestScenario richiama** cmdlet](https://msdn.microsoft.com/library/azure/mt644774.aspx). Uno scenario di test chaos provoca in modo casuale più nodo pacchetto codice e gli errori di replica nel cluster.

3. *Lo sviluppatore del servizio* [comunicazioni al servizio di test](service-fabric-testability-scenarios-service-communication.md) creando scenari di test di spostare repliche primarie all'interno del cluster.

Per ulteriori informazioni, vedere [Introduzione al servizio di analisi di errore](service-fabric-testability-overview.md) .

## <a name="upgrade"></a>Eseguire l'aggiornamento
1. Uno *sviluppatore del servizio* Aggiorna i servizi costitutivi dell'applicazione istanza e/o correzioni di bug e fornisce una nuova versione del manifesto di servizio.

2. Uno *sviluppatore dell'applicazione* esegue l'override e Parametrizza le impostazioni di configurazione e distribuzione dei servizi coerenti e offre una nuova versione del manifesto dell'applicazione. Lo sviluppatore dell'applicazione quindi incorpora le nuove versioni manifesti servizio nell'applicazione e offre una nuova versione del tipo di applicazione di un pacchetto dell'applicazione aggiornato.

3. Un *amministratore dell'applicazione* incorpora la nuova versione del tipo di applicazione l'applicazione di destinazione aggiornando i parametri appropriati.

5. Un *operatore* consente di caricare il pacchetto di applicazione aggiornato archivio immagini cluster utilizzando il [metodo **CopyApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.copyapplicationpackage.aspx) o il [cmdlet **ServiceFabricApplicationPackage copia** ](https://msdn.microsoft.com/library/azure/mt125905.aspx). Pacchetto dell'applicazione contiene la raccolta di pacchetti e manifesto dell'applicazione.

6. Un *operatore* disposizioni la nuova versione dell'applicazione del cluster di destinazione utilizzando il [metodo **ProvisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.provisionapplicationasync.aspx), il [ **Registro ServiceFabricApplicationType** cmdlet](https://msdn.microsoft.com/library/azure/mt125958.aspx)o l' [operazione di resto **disposizione un'applicazione** ](https://msdn.microsoft.com/library/azure/dn707672.aspx).

7. Un *operatore* consente di aggiornare l'applicazione di destinazione alla nuova versione utilizzando il [metodo **UpgradeApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.upgradeapplicationasync.aspx), il [cmdlet **Start ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125975.aspx)o l' [operazione di **aggiornamento di un'applicazione** resto](https://msdn.microsoft.com/library/azure/dn707633.aspx).

8. Un *operatore* controlla lo stato di avanzamento dell'aggiornamento utilizzando il [metodo **GetApplicationUpgradeProgressAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.getapplicationupgradeprogressasync.aspx), il [cmdlet **Get-ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt125988.aspx)o l' [operazione di resto **Ottenere applicazione aggiornare lo stato di avanzamento** ](https://msdn.microsoft.com/library/azure/dn707631.aspx).

9. Se necessario, l' *operatore di* modifica e riapplica i parametri dell'aggiornamento dell'applicazione corrente utilizzando il [metodo **UpdateApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.updateapplicationupgradeasync.aspx), il [cmdlet di **Aggiornamento ServiceFabricApplicationUpgrade** ](https://msdn.microsoft.com/library/azure/mt126030.aspx)o l' [operazione di resto **Update l'aggiornamento dell'applicazione** ](https://msdn.microsoft.com/library/azure/mt628489.aspx).

10. Se necessario, l' *operatore* consente di ripristinare l'aggiornamento dell'applicazione corrente utilizzando il [metodo **RollbackApplicationUpgradeAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.rollbackapplicationupgradeasync.aspx), il [cmdlet **Start ServiceFabricApplicationRollback** ](https://msdn.microsoft.com/library/azure/mt125833.aspx)o l' [operazione di **Ripristino applicazione aggiornamento** resto](https://msdn.microsoft.com/library/azure/mt628494.aspx).

11. Servizio tessuti consente di aggiornare l'applicazione di destinazione in esecuzione nel cluster senza perdere la disponibilità di uno dei relativi servizi costitutivi.

Vedere l' [applicazione esercitazione di aggiornamento](service-fabric-application-upgrade-tutorial.md) per gli esempi.

## <a name="maintain"></a>Mantenere
1. Per gli aggiornamenti del sistema operativo e patch, tessuti servizio interagisce con l'infrastruttura di Azure per garantire la disponibilità di tutte le applicazioni in esecuzione nel cluster.

2. Per gli aggiornamenti e patch della piattaforma tessuti servizio servizio tessuti Aggiorna senza perdere la disponibilità delle applicazioni in esecuzione nel cluster.

3. Un *amministratore dell'applicazione* approva l'aggiunta o rimozione dei nodi da un cluster dopo l'analisi dei dati di utilizzo della capacità cronologiche e domanda futuri prevista.

4. Un *operatore* aggiunge e rimuove nodi specificati dall' *amministratore dell'applicazione*.

5. Quando vengono aggiunti nuovi nodi o nodi esistenti verranno rimossi dal cluster, tessuti servizio automaticamente bilancia i carichi le applicazioni in esecuzione su tutti i nodi del cluster per ottenere prestazioni ottimali.

## <a name="remove"></a>Rimuovere
1. Un *operatore* è possibile eliminare una specifica istanza di un servizio in esecuzione nel cluster senza rimuovere l'intera applicazione utilizzando il [metodo **DeleteServiceAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync.aspx), [ **Rimuovi ServiceFabricService** cmdlet](https://msdn.microsoft.com/library/azure/mt126033.aspx)o l' [operazione di **Eliminazione servizio** resto](https://msdn.microsoft.com/library/azure/dn707687.aspx).  

2. Un *operatore* inoltre possibile eliminare un'istanza dell'applicazione e tutti i relativi servizi tramite il [metodo **DeleteApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.deleteapplicationasync.aspx), [ **Rimuovi ServiceFabricApplication** cmdlet](https://msdn.microsoft.com/library/azure/mt125914.aspx)o l' [operazione di **Eliminazione applicazione** resto](https://msdn.microsoft.com/library/azure/dn707651.aspx).

3. Al termine dell'applicazione e dei servizi, l' *operatore* può annullamento del provisioning il tipo di applicazione utilizzando il [metodo **UnprovisionApplicationAsync** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.unprovisionapplicationasync.aspx), il [cmdlet di **Annullamento della registrazione ServiceFabricApplicationType** ](https://msdn.microsoft.com/library/azure/mt125885.aspx)o l' [operazione di **annullamento del provisioning un'applicazione** resto](https://msdn.microsoft.com/library/azure/dn707671.aspx). Annullamento del provisioning il tipo di applicazione non rimuovere il pacchetto dell'applicazione di ImageStore. È necessario rimuovere manualmente il pacchetto dell'applicazione.

4. Un *operatore* rimuove il pacchetto dell'applicazione da ImageStore utilizzando il [metodo **RemoveApplicationPackage** ](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.applicationmanagementclient.removeapplicationpackage.aspx) o [ **Rimuovi ServiceFabricApplicationPackage** cmdlet](https://msdn.microsoft.com/library/azure/mt163532.aspx).

Per esempi, vedere [distribuire un'applicazione](service-fabric-deploy-remove-applications.md) .

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sullo sviluppo, test e gestire applicazioni di servizio tessuti e servizi, vedere:

- [Operatori affidabili](service-fabric-reliable-actors-introduction.md)
- [Servizi affidabili](service-fabric-reliable-services-introduction.md)
- [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)
- [Aggiornamento dell'applicazione](service-fabric-application-upgrade.md)
- [Panoramica di testabilità](service-fabric-testability-overview.md)
- [Esempio di ciclo di vita di applicazioni basate su resto](service-fabric-rest-based-application-lifecycle-sample.md)
