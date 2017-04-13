<properties 
pageTitle="Gestire gli eventi del ciclo di vita di servizio Cloud | Microsoft Azure" 
description="Informazioni su come utilizzare i metodi di ciclo di vita di un ruolo di servizio Cloud in .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizzare il ciclo di vita di un ruolo Web o lavoro .NET

Quando si crea un ruolo di lavoro, si estende la classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) che fornisce metodi per la sovrascrittura che consentono di rispondono agli eventi del ciclo di vita. Per i ruoli web questa classe è facoltativa, pertanto è necessario utilizzare per rispondere a eventi del ciclo di vita.

## <a name="extend-the-roleentrypoint-class"></a>Estendere la classe RoleEntryPoint

La classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) include i metodi chiamati da Azure quando si **Avvia**, **viene eseguito**o **interrompere** un ruolo web o lavoro. Facoltativamente, è possibile ignorare questi metodi per gestire inizializzazione ruolo, sequenze di arresto ruoli o al thread di esecuzione del ruolo. 

Quando si estende **RoleEntryPoint**, tenere presente i comportamenti dei metodi seguenti:

-   I metodi [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) e [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) restituiscono un valore boolean, pertanto è possibile restituisca **false** da questi metodi.

     Se il codice restituisce **Falso**, il processo del ruolo viene interrotta improvvisamente, senza eseguire qualsiasi sequenza di arresto che potrebbe essere in posizione. In generale, è consigliabile evitare restituisca **false** dal metodo **OnStart** .
     
-   Qualsiasi eccezione non rilevata all'interno di un overload di un metodo **RoleEntryPoint** viene considerata come un'eccezione non gestita.

     Se si verifica un'eccezione all'interno di uno dei metodi del ciclo di vita, Azure genererà l'evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) e quindi il processo è terminato. Dopo il proprio ruolo è stata disattivata, verrà riavviata da Azure. Quando si verifica un'eccezione non gestita, [l'arresto](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) non viene generato e il metodo **OnStop** non è chiamato.

Se il proprio ruolo non viene avviato o riciclaggio tra l'inizializzazione, occupato e gli stati di arresto, il codice potrebbe generare un'eccezione non gestita all'interno di uno degli eventi del ciclo di vita si riavvia il ruolo di ogni volta. In questo caso, utilizzare l'evento [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) per determinare la causa dell'eccezione e gestire in modo appropriato. Il proprio ruolo potrebbe anche la restituzione dal metodo di [esecuzione](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , che determina il ruolo di riavviare. Per ulteriori informazioni sugli stati di distribuzione, vedere [Problemi che causano ruoli comuni Cestino](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Se si utilizza **Azure Tools per Microsoft Visual Studio** per sviluppare l'applicazione, i modelli di progetto ruolo estendono automaticamente la classe **RoleEntryPoint** , il file *WebRole.cs* e *WorkerRole.cs* .

## <a name="onstart-method"></a>Metodo OnStart

Il metodo **OnStart** è chiamato quando l'istanza del ruolo linea da Azure. Durante l'esecuzione di codice OnStart, l'istanza di ruolo è contrassegnato come **occupato** e non il traffico esterno verrà indirizzato al dal servizio di bilanciamento del carico. È possibile ignorare questo metodo per eseguire operazioni di inizializzazione, ad esempio implementazione gestori e avvio [Della diagnostica Azure](cloud-services-how-to-monitor.md).

Se **OnStart** restituisce **true**, l'inizializzazione dell'istanza correttamente e Azure chiama il metodo **RoleEntryPoint.Run** . Se **OnStart** restituisce **Falso**, il ruolo verrà interrotto immediatamente, senza eseguire qualsiasi sequenza di arresto pianificato.

Nell'esempio seguente viene illustrato l'override del metodo **OnStart** . Questo metodo consente di configurare e avvia un monitor diagnostico quando l'istanza di ruoli viene avviato e configura automaticamente un trasferimento di registrazione dei dati a un account di archiviazione:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Metodo OnStop

Il metodo **OnStop** è chiamato dopo un'istanza del ruolo è stata disattivata da Azure e prima della chiusura del processo. È possibile ignorare questo metodo per chiamare il codice necessario per un'istanza del ruolo per arrestato.

> [AZURE.IMPORTANT] Il codice in esecuzione nel metodo **OnStop** dispone di un periodo di tempo limitato alla fine quando si chiama per motivi diversi da una chiusura avviata dall'utente. Il tempo trascorso, il processo è terminato, pertanto è necessario assicurarsi che il **OnStop** metodo può eseguire rapidamente o tollerata non è in esecuzione fino al completamento. **OnStop** viene chiamato dopo **l'interruzione** dell'evento.


## <a name="run-method"></a>Esegui metodo

È possibile ignorare il metodo **Run** per implementare un thread di esecuzione prolungata per l'istanza del ruolo.

Si esegue l'override il metodo **Run** non è necessaria. l'implementazione predefinita avvia un thread inattivo continuo. Se si esegue l'override metodo **eseguire** il codice necessario bloccare tempo indefinito. Se il metodo **Run** restituisce, il ruolo è automaticamente normalmente inserimento Cestino; in altre parole, Azure genera l'evento di **interruzione** e chiama il metodo **OnStop** in modo che le sequenze di arresto possono essere eseguite prima che il ruolo viene disattivato.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementazione di metodi di ciclo di vita ASP.NET per un ruolo web

È possibile utilizzare i metodi di ciclo di vita ASP.NET, oltre a quelli forniti dalla classe **RoleEntryPoint** per gestire inizializzazione e sequenze di chiusura per un ruolo web. Può essere utile per garantire la compatibilità se si trasferisce un'applicazione ASP.NET esistente in Azure. I metodi di ciclo di vita ASP.NET sono chiamati all'interno dei metodi di **RoleEntryPoint** . Il **applicazione\_avviare** chiamato dopo il metodo **RoleEntryPoint.OnStart** completa. Il **applicazione\_fine** chiamato prima che venga chiamato il metodo **RoleEntryPoint.OnStop** .

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un pacchetto di servizio cloud](cloud-services-model-and-package.md).