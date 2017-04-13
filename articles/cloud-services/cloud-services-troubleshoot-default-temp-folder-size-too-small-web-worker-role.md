<properties
   pageTitle="Dimensione cartella TEMP predefinita è troppo piccolo per un ruolo | Microsoft Azure"
   description="Un ruolo di servizio cloud ha una quantità di spazio per la cartella TEMP limitata. In questo articolo fornisce alcuni suggerimenti su come evitare spazio esaurito."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Dimensione cartella TEMP predefinita è troppo piccolo per un ruolo di web/lavoro servizio cloud

La directory temporanea predefinita di un ruolo di lavoro o web servizio cloud ha una dimensione massima di 100 MB, che possono diventare completo in un determinato momento. In questo articolo viene descritto come evitare lo spazio per la directory temporanea.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Perché esaurirsi spazio?

Le variabili di ambiente Windows standard TEMP e TMP sono disponibili per il codice in esecuzione nell'applicazione. TEMP e TMP scegliere una singola directory che contiene una dimensione massima di 100 MB. Tutti i dati archiviati in questa directory non viene mantenuti nel ciclo di vita del servizio cloud. Se le istanze di ruolo in un servizio cloud inserimento Cestino, viene pulita la directory.

## <a name="suggestion-to-fix-the-problem"></a>Suggerimento per risolvere il problema

Implementare una delle seguenti alternative:

- Configurare una risorsa di archiviazione locale e accedere direttamente invece di usare TEMP o TMP. Per accedere a una risorsa di archiviazione locale dal codice in esecuzione nell'applicazione, chiamare il metodo [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurare una risorsa di archiviazione locale e scegliere la directory TEMP e TMP in modo che punti al percorso della risorsa archivio locale. Questa modifica deve essere eseguita all'interno del metodo [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

Nell'esempio seguente viene illustrato come modificare le directory di destinazione per TEMP e TMP all'interno del metodo OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

Leggere un blog in cui viene [descritto come aumentare le dimensioni di Azure Web ruolo ASP.NET cartella temporanea](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Visualizzare altri [articoli di risoluzione dei problemi](/?tag=top-support-issue&product=cloud-services) per i servizi cloud.

Per informazioni su come risolvere i problemi relativi al ruolo ricoperto dai partecipanti servizio cloud utilizzando i dati di diagnostica computer PaaS Azure, consente di visualizzare [la serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
