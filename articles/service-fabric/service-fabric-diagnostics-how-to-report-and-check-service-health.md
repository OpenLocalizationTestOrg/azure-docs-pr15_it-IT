<properties
   pageTitle="Report e verificare l'integrità con Azure servizio tessuti | Microsoft Azure"
   description="Informazioni su come inviare report di stato dal codice servizio e verificare l'integrità del servizio mediante gli strumenti di monitoraggio dell'integrità Azure servizio tessuti disponibili in."
   services="service-fabric"
   documentationCenter=".net"
   authors="toddabel"
   manager="mfussell"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/06/2016"
   ms.author="toddabel"/>

# <a name="report-and-check-service-health"></a>Report e verificare l'integrità del servizio
Quando il provider di servizi verificarsi problemi, la possibilità di rispondere e risolvere i problemi e interruzioni dipende la possibilità di individuare rapidamente i problemi. Se si segnalare problemi ed errori al gestore integrità Azure servizio tessuti dal codice servizio, è possibile utilizzare gli strumenti che contiene un servizio tessuti per controllare lo stato di integrità di monitoraggio standard.

Esistono due modi che è possibile segnalare integrità del servizio:

- Utilizzo degli oggetti [partizione](https://msdn.microsoft.com/library/system.fabric.istatefulservicepartition.aspx) o [CodePackageActivationContext](https://msdn.microsoft.com/library/system.fabric.codepackageactivationcontext.aspx) .  
È possibile utilizzare il `Partition` e `CodePackageActivationContext` oggetti per segnalare lo stato di elementi che fanno parte di contesto corrente. Ad esempio codice eseguito come parte di una replica segnalare integrità solo in tale replica, partizione a cui appartiene e l'applicazione che fa parte di.

- Usare `FabricClient`.   
È possibile utilizzare `FabricClient` a integrità dei report dal codice servizio se il cluster non è [protetta](service-fabric-cluster-security.md) o se il servizio è in esecuzione con privilegi di amministratore. Non è vera nella maggior parte degli scenari reali. Con `FabricClient`, è possibile segnalare integrità su qualsiasi entità che fa parte del cluster. Se possibile, tuttavia, codice servizio dovrà essere inviata solo report correlata alla propria integrità.

In questo articolo illustra un esempio di report di stato dal codice di servizio. Nell'esempio illustrato anche possano utilizzare gli strumenti disponibili tessuti servizio in per controllare lo stato di integrità. In questo articolo deve essere una rapida introduzione alle funzionalità dell'infrastruttura di servizio di monitoraggio dello stato. Per informazioni più dettagliate, è possibile leggere la serie di articoli approfondite dello stato che iniziano con il collegamento alla fine di questo articolo.

## <a name="prerequisites"></a>Prerequisiti
È necessario siano installati i seguenti:

   * Visual Studio 2015
   * Servizio tessuti SDK

## <a name="to-create-a-local-secure-dev-cluster"></a>Per creare un cluster dev sicura locale
- Aprire PowerShell con privilegi di amministratore ed eseguire i comandi seguenti.

![Comandi che illustrano come creare un cluster di sviluppo protetto](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-secure-dev-cluster.png)

## <a name="to-deploy-an-application-and-check-its-health"></a>Distribuire un'applicazione e verificare l'integrità

1. Aprire Visual Studio come amministratore.

2. Creare un progetto utilizzando il modello di **Servizio informazioni sullo stato** .

    ![Creare un'applicazione di servizio tessuti con il servizio informazioni sullo stato](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/create-stateful-service-application-dialog.png)

3. Premere **F5** per eseguire l'applicazione in modalità di debug. L'applicazione verrà distribuito sul cluster locale.

4. Dopo l'applicazione è in esecuzione, pulsante destro del mouse sull'icona di gestione di Cluster locali nell'area di notifica e selezionare **Gestisci Cluster locale** dal menu di scelta rapida per aprire Esplora tessuti servizio.

    ![Aprire Esplora risorse tessuti servizio dall'area di notifica](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/LaunchSFX.png)

5. Lo stato dell'applicazione deve essere visualizzato come illustrato nell'immagine. Al momento dell'applicazione dovrebbe essere integro senza errori.

    ![Integro applicazione di servizio tessuti Explorer](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-healthy-app.png)

6. È anche possibile verificare l'integrità tramite PowerShell. È possibile utilizzare ```Get-ServiceFabricApplicationHealth``` controllo dello stato dell'applicazione e si può usare ```Get-ServiceFabricServiceHealth``` per verificare l'integrità del servizio. Il rapporto di stato per la stessa applicazione PowerShell è in questa immagine.

    ![Applicazione integro PowerShell](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/ps-healthy-app-report.png)

## <a name="to-add-custom-health-events-to-your-service-code"></a>Per aggiungere eventi dello stato personalizzati al codice servizio
I modelli di progetto tessuti servizio Visual Studio contengono codice di esempio. La procedura seguente mostra come è possibile creare report personalizzati agli eventi dal codice servizio. Ad esempio report verrà visualizzato automaticamente in strumenti standard per il monitoraggio dell'integrità che fornisce tessuti servizio, ad esempio servizio tessuti Explorer, visualizzazione di Azure integrità del portale e PowerShell.

1. Riaprire l'applicazione creata in precedenza in Visual Studio oppure creare una nuova applicazione utilizzando il modello di Visual Studio **Servizio informazioni sullo stato** .

2. Aprire il file Stateful1.cs e individuare il `myDictionary.TryGetValueAsync` chiamare il `RunAsync` metodo. È possibile vedere che questo metodo restituisce un `result` che contiene il valore corrente del contatore poiché è la logica chiave in questa applicazione per mantenere un conteggio in esecuzione. Se si trattasse di un'applicazione reale e la mancanza di risultato rappresentato un errore, si desidera contrassegnare l'evento.

3. Per dichiarare un evento di stato quando la mancanza di risultato rappresenta un errore, aggiungere la procedura seguente.

    un. Aggiungere il `System.Fabric.Health` spazio dei nomi per il file Stateful1.cs.

    ```csharp
    using System.Fabric.Health;
    ```

    b. Aggiungere il codice seguente dopo il `myDictionary.TryGetValueAsync` chiamata

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
    Verranno segnalate dell'integrità della replica perché segnalato da un servizio informazioni sullo stato. Il `HealthInformation` parametro archivia le informazioni sul problema integrità che viene segnalato.

    Se è stato creato un servizio senza informazioni sullo stato, utilizzare il codice seguente

    ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
    ```

4. Se il servizio è in esecuzione con privilegi di amministratore o se il cluster non è [protetta](service-fabric-cluster-security.md), è inoltre possibile utilizzare `FabricClient` a integrità dei report, come illustrato di seguito.  

    un. Creare il `FabricClient` istanza dopo il `var myDictionary` dichiarazione.

    ```csharp
    var fabricClient = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });
    ```

    b. Aggiungere il codice seguente dopo il `myDictionary.TryGetValueAsync` chiamata.

    ```csharp
    if (!result.HasValue)
    {
       var replicaHealthReport = new StatefulServiceReplicaHealthReport(
            this.ServiceInitializationParameters.PartitionId,
            this.ServiceInitializationParameters.ReplicaId,
            new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error));
        fabricClient.HealthManager.ReportHealth(replicaHealthReport);
    }
    ```

5. Di seguito consente di simulare questo errore e visualizzarlo visualizzati gli strumenti di monitoraggio dell'integrità. Per simulare l'errore, il commento nella prima riga l'integrità dei report di codice aggiunto in precedenza. Dopo è commentare la prima riga, il codice risulterà simile al seguente:

    ```csharp
    //if(!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportReplicaHealth(healthInformation);
    }
    ```
 Questo codice ora genererà questo rapporto di stato ogni volta che `RunAsync` viene eseguito. Dopo aver apportato le modifiche, premere **F5** per eseguire l'applicazione.

6. Dopo l'applicazione è in esecuzione, aprire Esplora tessuti di servizio per verificare l'integrità dell'applicazione. In questo caso, servizio tessuti Explorer visualizzerà che l'applicazione è danneggiato. Verrà visualizzata a causa dell'errore che è stato segnalato dal codice che è stato aggiunto in precedenza.

    ![Applicazione non corretti in Esplora tessuti servizio](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/sfx-unhealthy-app.png)

7. Se si seleziona la replica principale in visualizzazione struttura del servizio tessuti Explorer, si vedrà che **Lo stato di integrità** indica un errore troppo. Servizio tessuti Explorer viene visualizzata anche i dettagli del report integrità che sono stati aggiunti per il `HealthInformation` parametro nel codice. È possibile visualizzare gli stessi report dello stato di PowerShell e il portale di Azure.

    ![Integrità duplicata in Esplora tessuti servizio](./media/service-fabric-diagnostics-how-to-report-and-check-service-health/replica-health-error-report-sfx.png)

Questo report rimarrà in Gestione integrità finché non viene sostituito da un altro report o fino a quando non viene eliminata la replica. Perché non è stata impostata `TimeToLive` per questo rapporto di stato nel `HealthInformation` oggetto, il report non è mai scadrà.

È consigliabile che deve essere dichiarato integrità nel livello più granulare, in questo caso è la replica. È anche possibile segnalare integrità nella `Partition`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
this.Partition.ReportPartitionHealth(healthInformation);
```

Per lo stato report su `Application`, `DeployedApplication`, e `DeployedServicePackage`, utilizzare `CodePackageActivationContext`.

```csharp
HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
var activationContext = FabricRuntime.GetActivationContext();
activationContext.ReportApplicationHealth(healthInformation);
```

## <a name="next-steps"></a>Passaggi successivi
[Approfondimento su integrità dei servizi tessuti](service-fabric-health-introduction.md)
