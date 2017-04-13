<properties
   pageTitle="Configurare l'aggiornamento di un'applicazione di servizio tessuti | Microsoft Azure"
   description="Informazioni su come configurare le impostazioni per l'aggiornamento di un'applicazione di servizio tessuti utilizzando Microsoft Visual Studio."
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />
<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="07/29/2016"
   ms.author="cawa" />

# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Configurare l'aggiornamento di un'applicazione di servizio tessuti in Visual Studio

Visual Studio tools per tessuti servizio Azure supportano l'aggiornamento per la pubblicazione su cluster locale o remoto. Esistono due vantaggi per l'aggiornamento all'applicazione di una versione più recente invece di sostituire l'applicazione durante la verifica e debug:

- Dati dell'applicazione non persi durante l'aggiornamento.
- Disponibilità rimane alta in modo che non possono essere qualsiasi interruzione del servizio durante l'aggiornamento, se sono che sufficienti istanze del servizio distribuiti in domini aggiornamento.

Mentre è in corso l'aggiornamento, è possono eseguire test in un'applicazione.

## <a name="parameters-needed-to-upgrade"></a>Parametri necessari per l'aggiornamento

È possibile scegliere tra due tipi di distribuzione: normale o aggiornamento. Una distribuzione normale consente di cancellare i dati nel cluster e informazioni sulla distribuzione precedente durante una distribuzione aggiornamento conserva. Quando si aggiorna un'applicazione di servizio tessuti in Visual Studio, è necessario specificare i criteri di controllo dell'integrità e parametri di aggiornamento dell'applicazione. Parametri di aggiornamento dell'applicazione sono utili per controllare l'aggiornamento, mentre i criteri di controllo dell'integrità determinano se l'aggiornamento è stata completata. Vedere [aggiornamento dell'applicazione di servizio tessuti: parametri aggiornamento](service-fabric-application-upgrade-parameters.md) per altri dettagli.

Esistono tre modalità di aggiornamento: *monitorate*, *UnmonitoredAuto*e *UnmonitoredManual*.

  - Un aggiornamento monitorate consente di automatizzare l'aggiornamento e la verifica dell'integrità dell'applicazione.

  - Un aggiornamento UnmonitoredAuto consente di automatizzare l'aggiornamento, ma la verifica dell'integrità applicazione verranno ignorati.

  - Quando si esegue l'aggiornamento di UnmonitoredManual, è necessario aggiornare manualmente ogni aggiornamento del dominio.

Ogni modalità di aggiornamento richiede insiemi di parametri diversi. Vedere [applicazione parametri di aggiornamento](service-fabric-application-upgrade-parameters.md) per altre informazioni disponibili opzioni di aggiornamento.

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Aggiornare un'applicazione di servizio tessuti in Visual Studio

Se si usa gli strumenti di Visual Studio servizio tessuti come per aggiornare un'applicazione di servizio tessuti, è possibile specificare un processo di pubblicazione per diventare un aggiornamento invece di una distribuzione normale selezionando la casella di controllo **aggiornamento dell'applicazione** .

### <a name="to-configure-the-upgrade-parameters"></a>Per configurare i parametri di aggiornamento

1. Fare clic sul pulsante **Impostazioni** accanto alla casella di controllo. Viene visualizzata la finestra di dialogo **Modifica parametri di aggiornamento** . Nella finestra di dialogo **Modifica parametri aggiornamento** supporta le modalità di aggiornamento monitorate, UnmonitoredAuto e UnmonitoredManual.

2. Selezionare la modalità di aggiornamento che si desidera utilizzare e quindi compilare la griglia di parametro.

    Valori predefiniti per ogni parametro. Il parametro facoltativo *DefaultServiceTypeHealthPolicy* ha un input di tabella hash. Ecco un esempio di formato input tabella hash di *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* è un altro parametro facoltativo che ha un input di tabella hash nel formato seguente:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Ecco un esempio reale:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```

3. Se si seleziona la modalità di aggiornamento UnmonitoredManual, sarà necessario avviare manualmente una console di PowerShell per continuare e completare il processo di aggiornamento. Fare riferimento a [aggiornamento dell'applicazione di servizio tessuti: argomenti avanzati](service-fabric-application-upgrade-advanced.md) per informazioni su aggiornamento manuale come funziona.

## <a name="upgrade-an-application-by-using-powershell"></a>Aggiornare un'applicazione tramite PowerShell

È possibile utilizzare i cmdlet di PowerShell per eseguire l'aggiornamento di un'applicazione di servizio tessuti. Per informazioni dettagliate, vedere [applicazione di servizio tessuti esercitazione di aggiornamento](service-fabric-application-upgrade-tutorial.md) e [ServiceFabricApplicationUpgrade Start](https://msdn.microsoft.com/library/mt125975.aspx) .

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Specificare un criterio di controllo integrità nel file manifesto dell'applicazione

Ogni servizio in un'applicazione di servizio tessuti può avere propri parametri dei criteri di integrità che sostituiscono i valori predefiniti. È possibile fornire questi valori di parametro nel file manifesto dell'applicazione.

Nell'esempio seguente viene illustrato come applicare un criterio di controllo integrità univoco per ogni servizio nel manifesto dell'applicazione.

```
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla distribuzione di un'applicazione, vedere [distribuire un'applicazione esistente in Azure servizio tessuti](service-fabric-deploy-existing-app.md).
