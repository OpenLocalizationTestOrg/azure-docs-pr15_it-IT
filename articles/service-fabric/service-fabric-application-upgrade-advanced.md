<properties
   pageTitle="Aggiornamento dell'applicazione: argomenti avanzati | Microsoft Azure"
   description="In questo articolo vengono illustrati alcuni argomenti avanzati relativi all'aggiornamento di un'applicazione di servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="service-fabric-application-upgrade-advanced-topics"></a>Aggiornamento dell'applicazione di servizio tessuti: argomenti avanzati

## <a name="adding-or-removing-services-during-an-application-upgrade"></a>Aggiunta o rimozione di servizi durante l'aggiornamento dell'applicazione

Se viene aggiunto un nuovo servizio a un'applicazione che già distribuita, pubblicata come un aggiornamento, il nuovo servizio viene aggiunto all'applicazione distribuita.  Ad esempio un aggiornamento non influisce su uno dei servizi che fanno già parte dell'applicazione. Tuttavia, è necessario avviare un'istanza del servizio che è stata aggiunta per il nuovo servizio sia attivo (utilizzando il `New-ServiceFabricService` cmdlet).

Servizi possono inoltre essere eliminati da un'applicazione come parte di un aggiornamento. Tuttavia, è necessario interrompere tutte le istanze corrente del servizio eliminata prima di procedere con l'aggiornamento (tramite il `Remove-ServiceFabricService` cmdlet). 

## <a name="manual-upgrade-mode"></a>Modalità di aggiornamento manuale

> [AZURE.NOTE]  Considerare la modalità manuale automatico solo per un aggiornamento non riuscito o sospeso. La modalità monitorata è la modalità di aggiornamento consigliata per le applicazioni di servizio tessuti.

Azure tessuti servizio offre diverse modalità di aggiornamento per supportare cluster di sviluppo e di produzione. Opzioni di distribuzione scelte possono essere diverse per ambienti diversi.

L'aggiornamento dell'applicazione in sequenza monitorate è l'aggiornamento più comune da utilizzare nell'ambiente di produzione. Quando viene specificato il criterio di aggiornamento, tessuti servizio garantisce che l'applicazione sia integro prima che l'aggiornamento proceda.

 L'amministratore dell'applicazione è possibile utilizzare la modalità di aggiornamento dell'applicazione in sequenza manuale di totale controllare lo stato dell'aggiornamento tramite i diversi domini di aggiornamento. Questa modalità è utile quando è necessario un criterio di valutazione di integrità personalizzate o complessa o si verifica un aggiornamento non convenzionale (ad esempio, l'applicazione è già la perdita di dati).

Infine, l'aggiornamento automatico dell'applicazione è utile per lo sviluppo o ambienti di prova per fornire un ciclo di iterazione fast durante lo sviluppo di servizio.

## <a name="change-to-manual-upgrade-mode"></a>Passare alla modalità di aggiornamento manuale
**Manuale**, interrompere l'aggiornamento dell'applicazione in UD corrente e modificare la modalità di aggiornamento automatico manuale. L'amministratore deve manualmente chiamare **MoveNextApplicationUpgradeDomainAsync** per procedere con l'aggiornamento o avviare un ripristino avviando un nuovo aggiornamento. Dopo l'aggiornamento entra in modalità manuale, rimane in modo manuale fino all'avvio di un nuovo aggiornamento. Il comando **GetApplicationUpgradeProgressAsync** restituisce tessuti\_applicazione\_aggiornamento\_stato\_materiale\_inoltra\_in sospeso.

## <a name="upgrade-with-a-diff-package"></a>Eseguire l'aggiornamento con un pacchetto diff

È possibile aggiornare un'applicazione di servizio tessuti, il provisioning di un pacchetto dell'applicazione completo e indipendente. È possibile aggiornare un'applicazione anche tramite un pacchetto diff contenente solo i file di applicazione aggiornato, manifesto dell'applicazione aggiornato e i file manifesto del servizio.

Un pacchetto dell'applicazione completo contiene tutti i file necessari per avviare ed eseguire un'applicazione di servizio tessuti. Un pacchetto diff contiene solo i file modificati tra ' ultima disposizione e l'aggiornamento corrente più file di manifesto manifesto dell'applicazione completo e il servizio. I riferimenti del manifesto manifesto servizio che non è possibile trovare nel layout compilazione cercati nell'archivio di immagine.

Pacchetti di applicazioni completo sono necessari per la prima installazione di un'applicazione al cluster. Gli aggiornamenti successivi possono essere un pacchetto dell'applicazione completo o un pacchetto diff.

Occasioni quando l'utilizzo di un pacchetto diff sarebbe un'ottima scelta:

* Un pacchetto diff è preferibile quando si dispone di un pacchetto di applicazione di grandi dimensioni che fa riferimento a più file manifesto di servizio e/o più pacchetti di codice, pacchetti di configurazione o pacchetti di dati.

* Un pacchetto diff è preferibile quando si dispone di un sistema di distribuzione che genera il layout di compilazione direttamente dal processo di compilazione dell'applicazione. In questo caso, anche se non è stato modificato il codice, assembly appena compilato è possibile ottenere un checksum diversi. Utilizzo di un pacchetto dell'applicazione completo richiederà il, è possibile aggiornare la versione in tutti i pacchetti di codice. Utilizzo di un pacchetto diff, solo fornire i file modificati e i file manifesto nel punto in cui è cambiata la versione.

Quando si aggiorna un'applicazione utilizzando Visual Studio, il pacchetto diff viene pubblicato automaticamente. Per creare un pacchetto diff manualmente, è necessario aggiornare il manifesto dell'applicazione e manifesti servizio, ma solo i pacchetti modificati dovrebbero essere incluso nel pacchetto dell'applicazione finale. 

Ad esempio, iniziamo con le applicazioni seguenti (numeri di versione forniti per semplificare la comprensione):

```text
app1        1.0.0
  service1  1.0.0
    code    1.0.0
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

A questo punto, si supponga che si desidera aggiornare solo il codice pacchetto di service1 utilizzando un pacchetto diff tramite PowerShell. A questo punto, l'applicazione aggiornata è la struttura delle cartelle seguenti:

```text
app1        2.0.0      <-- new version
  service1  2.0.0      <-- new version
    code    2.0.0      <-- new version
    config  1.0.0
  service2  1.0.0
    code    1.0.0
    config  1.0.0
```

In questo caso, si aggiorna il manifesto dell'applicazione 2.0.0 e manifesto servizio per service1 in modo da rispecchiare l'aggiornamento del pacchetto di codice. La cartella per il pacchetto di applicazione avrebbe la struttura seguente:

```text
app1/
  service1/
    code/
```

## <a name="next-steps"></a>Passaggi successivi

[Aggiornare l'applicazione mediante Visual Studio](service-fabric-application-upgrade-tutorial.md) fornisce un aggiornamento dell'applicazione utilizzando Visual Studio.

[Aggiornare l'applicazione di uso di Powershell](service-fabric-application-upgrade-tutorial-powershell.md) fornisce un aggiornamento dell'applicazione tramite PowerShell.

È possibile controllare come l'applicazione viene aggiornata utilizzando i [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Effettuare l'aggiornamento delle applicazioni compatibili per imparare a utilizzare [Serializzazione dei dati](service-fabric-application-upgrade-data-serialization.md).

Risolvere i problemi comuni in aggiornamenti dell'applicazione facendo riferimento alla procedura di [Risoluzione dei problemi di aggiornamento delle applicazioni](service-fabric-application-upgrade-troubleshooting.md).
 
