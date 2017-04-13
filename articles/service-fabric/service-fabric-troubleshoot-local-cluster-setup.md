<properties
   pageTitle="Risolvere i problemi di installazione locale di tessuti servizio cluster | Microsoft Azure"
   description="Questo articolo illustra una serie di suggerimenti per la risoluzione dei problemi del cluster di sviluppo locale"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/08/2016"
   ms.author="seanmck"/>

# <a name="troubleshoot-your-local-development-cluster-setup"></a>Risolvere i problemi di installazione di cluster sviluppo locale

Se si verificano problemi durante l'interazione con i cluster di sviluppo di Azure servizio tessuti locale, esaminare i suggerimenti seguenti per possibili soluzioni.

## <a name="cluster-setup-failures"></a>Errori di installazione del cluster

### <a name="cannot-clean-up-service-fabric-logs"></a>Non è possibile eliminare i registri tessuti servizio

#### <a name="problem"></a>Problema

Durante l'esecuzione di script DevClusterSetup, viene visualizzato un errore come questo:

    Cannot clean up C:\SfDevCluster\Log fully as references are likely being held to items in it. Please remove those and run this script again.
    At line:1 char:1 + .\DevClusterSetup.ps1
    + ~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : NotSpecified: (:) [Write-Error], WriteErrorException
    + FullyQualifiedErrorId : Microsoft.PowerShell.Commands.WriteErrorException,DevClusterSetup.ps1


#### <a name="solution"></a>Soluzione

Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. A questo punto dovrebbe essere possibile eseguire correttamente lo script.

## <a name="cluster-connection-failures"></a>Problemi di connessione cluster

### <a name="service-fabric-powershell-cmdlets-are-not-recognized-in-azure-powershell"></a>Cmdlet di PowerShell tessuti servizi non vengono riconosciuti in Azure PowerShell

#### <a name="problem"></a>Problema

Se si tenta di eseguire uno dei cmdlet di PowerShell tessuti servizio, ad esempio `Connect-ServiceFabricCluster` in una finestra di PowerShell Azure non riesce, che indica che il cmdlet non sia riconosciuto. Il motivo è che Azure PowerShell utilizza la versione a 32 bit di Windows PowerShell (anche su versioni del sistema operativo a 64 bit), mentre i cmdlet di servizio tessuti funzionano solo in ambienti a 64 bit.

#### <a name="solution"></a>Soluzione

Eseguire sempre cmdlet tessuti servizio direttamente da Windows PowerShell.

>[AZURE.NOTE] La versione più recente di PowerShell Azure non viene creato un collegamento speciale, in modo che questa non dovrebbe più verificarsi.

### <a name="type-initialization-exception"></a>Digitare eccezione inizializzazione

#### <a name="problem"></a>Problema

Quando si è connessi a cluster di PowerShell, viene visualizzato l'errore indicato per System.Fabric.Common.AppTrace.

#### <a name="solution"></a>Soluzione

La variabile path non è stata impostata correttamente durante l'installazione. Disconnettersi da Windows e accedere di nuovo. Consente di aggiornare completamente il percorso.

### <a name="cluster-connection-fails-with-object-is-closed"></a>Connessione del cluster ha esito negativo e "Oggetto chiuso"

#### <a name="problem"></a>Problema

Una chiamata al ServiceFabricCluster connessione non riesce con un messaggio di errore come questo:

    Connect-ServiceFabricCluster : The object is closed.
    At line:1 char:1
    + Connect-ServiceFabricCluster
    + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo : InvalidOperation: (:) [Connect-ServiceFabricCluster], FabricObjectClosedException
    + FullyQualifiedErrorId : CreateClusterConnectionErrorId,Microsoft.ServiceFabric.Powershell.ConnectCluster

#### <a name="solution"></a>Soluzione

Chiudere la finestra di PowerShell corrente e aprire una nuova finestra di PowerShell come amministratore. A questo punto dovrebbe essere possibile stabilire la connessione.

### <a name="fabric-connection-denied-exception"></a>Eccezione tessuti connessione negato

#### <a name="problem"></a>Problema

Quando il debug da Visual Studio, viene visualizzato un errore FabricConnectionDeniedException.

#### <a name="solution"></a>Soluzione

Questo errore si verifica quando si prova a tenta di avviare un host del servizio elaborare manualmente, invece che consente di runtime del servizio tessuti per avviare automaticamente.

Assicurarsi che non si dispone i progetti di servizio impostato come progetti di avvio della soluzione. Solo i progetti di applicazione di servizio tessuti devono essere impostati come progetti di avvio.

>[AZURE.TIP] Se, dopo l'installazione, il cluster locale inizia a si comportano in modo anomalo, è possibile riportarlo utilizzando applicazione sulla barra delle applicazioni di sistema di Gestione cluster locale. Questa operazione verrà rimosso cluster esistente e configurare un nuovo PIN. Si noti che tutte le applicazioni distribuite e i dati associati verranno rimossi.

## <a name="next-steps"></a>Passaggi successivi

- [Comprendere e risolvere i problemi il cluster con report di stato di sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [Visualizzare il cluster con Esplora risorse tessuti di servizio](service-fabric-visualizing-your-cluster.md)
