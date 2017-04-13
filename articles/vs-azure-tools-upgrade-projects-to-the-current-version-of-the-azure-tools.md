<properties
   pageTitle="Come eseguire l'aggiornamento di progetti per la versione corrente di Azure strumenti | Microsoft Azure"
   description="Informazioni su come eseguire l'aggiornamento di un progetto Azure in Visual Studio per la versione corrente di strumenti di Azure"
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

# <a name="how-to-upgrade-projects-to-the-current-version-of-the-azure-tools-for-visual-studio"></a>Come eseguire l'aggiornamento di progetti per la versione corrente di Azure Tools per Visual Studio

## <a name="overview"></a>Panoramica

Dopo avere installato la versione corrente di Azure Tools (o una versione precedente più recente di 1.6), tutti i progetti che sono stati creati usando gli strumenti di Azure rilasciare prima 1.6 (November 2011) verranno aggiornate automaticamente non appena si apre. Se si riscontrano ancora tale versione installata per creare progetti utilizzando la versione (November 2011) 1.6 di questi strumenti, è possibile aprire i progetti nella versione precedente e decidere in un secondo momento se si desidera aggiornarli.

## <a name="how-your-project-changes-when-you-upgrade-it"></a>Come un progetto viene modificato quando si esegue l'aggiornamento

Se un progetto viene aggiornato automaticamente oppure specificare che si desidera eseguire l'aggiornamento, viene modificato il progetto per lavorare con le versioni correnti dei determinati assembly e alcune proprietà vengono modificate anche come descritto in questa sezione. Se il progetto richiede altre modifiche incompatibile con la versione più recente degli strumenti, è necessario apportare manualmente le modifiche.

- Config per ruoli web e il file di App per i ruoli di lavoro vengono aggiornati per fare riferimento alla versione più recente di Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitoirTraceListener.dll.

- Assembly Microsoft.WindowsAzure.StorageClient.dll, Microsoft.WindowsAzure.Diagnostics.dll e Microsoft.WindowsAzure.ServiceRuntime.dll vengono aggiornate alle nuove versioni.

- Profili di pubblicazione memorizzate nel file di progetto Azure (.ccproj) vengono spostati in un file separato, con .azurePubXml estensione, nella sottodirectory **pubblica** .

- Alcune proprietà del profilo pubblica vengono aggiornati per supportare funzionalità nuove e modificate. **AllowUpgrade** viene sostituito da **DeploymentReplacementMethod** perché è possibile aggiornare un servizio cloud distribuito contemporaneamente o in modo incrementale.

- La proprietà **UseIISExpressByDefault** viene aggiunto e impostata su false, in modo che il server web che viene utilizzato per il debug non verranno modificati automaticamente da Internet Information Services (IIS) per IIS Express. IIS Express è il server web predefinito per i progetti creati con le versioni più recenti degli strumenti.

- Se la memorizzazione nella cache di Azure è ospitato in una o più dei ruoli del progetto, alcune proprietà nella configurazione del servizio (file cscfg) e nella definizione di servizio (file .csdef) vengono modificate quando si aggiorna un progetto. Se il progetto è installato il pacchetto NuGet Azure memorizzazione nella cache, il progetto viene aggiornato alla versione più recente del pacchetto. È necessario aprire il file config e verificare che la configurazione di client venga mantenuta correttamente durante il processo di aggiornamento. Se si aggiungono i riferimenti per la memorizzazione nella cache di Azure assembly client senza utilizzare il pacchetto NuGet, questi assembly non verranno aggiornati; è necessario aggiornare manualmente i riferimenti alle nuove versioni.

>[AZURE.IMPORTANT] Per progetti F #, è necessario aggiornare manualmente i riferimenti agli assembly Azure in modo che fanno riferimento le versioni più recenti di tali assembly.

### <a name="how-to-upgrade-an-azure-project-to-the-current-release"></a>Come eseguire l'aggiornamento di un progetto di Azure alla versione corrente

1. Installare la versione corrente di Azure Tools all'installazione di Visual Studio che si desidera utilizzare per il progetto aggiornato e quindi aprire il progetto che si desidera aggiornare. Se il progetto è stato creato con gli strumenti di Azure rilasciare prima 1.6 (November 2011), il progetto viene aggiornato automaticamente con la versione corrente. Se il progetto è stato creato con November 2011 rilasciare e tale versione è ancora installata, verrà aperto il progetto in tale versione.

1. In Esplora risorse, aprire il menu di scelta rapida per il nodo del progetto, scegliere **proprietà**e quindi scegliere la scheda **applicazione** della finestra di dialogo visualizzata.

    Scheda **applicazione** Mostra la versione di strumenti che è associata al progetto. Se viene visualizzata la versione corrente di strumenti di Azure, il progetto è già stato aggiornato. Se è stato installato una versione più recente degli strumenti più la scheda Visualizza, viene visualizzato un pulsante **di aggiornamento** .

1. Fare clic sul pulsante **Aggiorna** per aggiornare un progetto per la versione corrente degli strumenti.

1. Compilare il progetto e quindi risolvere gli eventuali errori che causano da modifiche all'API. Per informazioni su come modificare il codice per la nuova versione, vedere la documentazione per l'API specifica.
