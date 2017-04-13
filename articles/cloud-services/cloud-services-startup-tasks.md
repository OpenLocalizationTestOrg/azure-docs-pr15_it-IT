<properties 
pageTitle="Eseguire attività di avvio in servizi Cloud Azure | Microsoft Azure" 
description="Attività di avvio sono utili per preparare l'ambiente di servizi cloud per un'app. Spiega che funzionamento delle attività di avvio e su come renderle" 
services="cloud-services" 
documentationCenter="" 
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



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Come configurare ed eseguire attività di avvio di un servizio cloud

È possibile utilizzare attività di avvio per eseguire operazioni prima dell'avvio di un ruolo. Le operazioni che è possibile eseguire includono l'installazione di un componente, la registrazione dei componenti COM, l'impostazione di chiavi del Registro di sistema o avviare un processo lunga.

>[AZURE.NOTE] Attività di avvio non sono applicabili a macchine virtuali, solo a Cloud servizio Web ruoli e di lavoro.

## <a name="how-startup-tasks-work"></a>Funzionamento delle attività di avvio

Avvio attività sono azioni che vengono eseguite prima che i ruoli iniziano e sono definiti nel file [ServiceDefinition.csdef] utilizzando l'elemento [attività] all'interno dell'elemento di [avvio] . Attività di avvio sono spesso file batch, ma è anche possibile applicazioni console o file batch che inizino gli script di PowerShell.

Variabili di ambiente passano informazioni in un'attività di avvio e lo spazio di archiviazione locale può essere utilizzato per passare le informazioni di un'attività di avvio. Ad esempio una variabile di ambiente può specificare il percorso di un programma che si desidera installare e i file possono essere scritti allo spazio di archiviazione locale che può essere letta in un secondo momento, i ruoli.

L'attività di avvio può registrare informazioni ed errori nella directory specificata dalla variabile di ambiente **TEMP** . Durante l'attività di avvio, la variabile di ambiente **TEMP** restituisce *c:\\risorse\\temp\\[guid]. [ roleName]\\RoleTemp* directory durante l'esecuzione nel cloud.

Attività di avvio può anche essere eseguita più volte tra il riavvio. Ad esempio, l'attività di avvio verrà eseguita ogni volta che il ruolo Ricicla e ruolo ricicli non includano sempre riavviare il computer. Attività di avvio devono essere scritti in modo che consente di eseguire più volte senza problemi.

Attività di avvio deve terminare con un **errorlevel** (o codice di uscita) pari a zero per il processo di avvio per completare. Se un'attività di avvio termina con un diverso da zero **errorlevel**, non verrà avviato il ruolo.


## <a name="role-startup-order"></a>Ordine di avvio ruolo

Di seguito sono elencati la procedura di avvio ruolo in Azure:

1. L'istanza è contrassegnato come **iniziale** e non riceve il traffico.

2. Tutte le attività di avvio vengono eseguite in base alle loro attributo **taskType** .
    - **Semplici** operazioni vengono eseguite in modo sincrono, uno alla volta.
    - Le attività di ** **primo piano** e** vengono avviate in modo asincrono, parallela per l'attività di avvio.  
       
    > [AZURE.WARNING] IIS potrebbe non essere completamente configurato durante la fase di attività di avvio del processo di avvio, in modo che tali dati potrebbero non essere disponibili. Attività di avvio che richiedono dati specifici del ruolo è necessario utilizzare [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Il processo di host del ruolo viene avviato e viene creato il sito in IIS.

4. Il metodo [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) è chiamato.

5. L'istanza è contrassegnata come **pronti** e il traffico è indirizzato all'istanza.

6. Il metodo [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) è chiamato.


## <a name="example-of-a-startup-task"></a>Esempio di un'attività di avvio

Avvio attività sono definite nel file [ServiceDefinition.csdef] , nell'elemento **attività** . L'attributo di **riga di comando** specifica il nome e i parametri del file batch avvio o comando console, l'attributo **executionContext** specifica il livello di privilegi per l'attività di avvio e l'attributo **taskType** specifica la modalità di esecuzione dell'attività.

In questo esempio, una variabile di ambiente, **MyVersionNumber**, viene creata per l'attività di avvio e impostare il valore "**1.0.0.0**".

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Nell'esempio seguente, il file batch **Startup.cmd** scritta la riga "la versione corrente non è 1.0.0.0" al file StartupLog.txt nella directory specificata dalla variabile di ambiente TEMP. Il `EXIT /B 0` riga assicura che l'attività di avvio termina con un **errorlevel** pari a zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] In Visual Studio, impostare la proprietà **Copia nella Directory di Output** per il file batch di avvio su **Copia sempre** per assicurarsi che il file batch avvio sia correttamente distribuito per il progetto in Azure (**approot\\collocazione** per ruoli Web e **approot** per i ruoli di lavoro).

## <a name="description-of-task-attributes"></a>Descrizione degli attributi delle attività

Di seguito vengono descritti gli attributi dell'elemento **attività** nel file [ServiceDefinition.csdef] :

**riga di comando** - specifica la riga di comando per l'attività di avvio:

- Il comando con parametri facoltativi della riga di comando, che inizia con l'attività di avvio.
- Spesso si tratta del nome di un file batch cmd o bat.
- L'attività è relativo il AppRoot\\cartella Bin per la distribuzione. Le variabili di ambiente non vengono espanse per determinare il percorso e il file dell'attività. Se è necessaria l'espansione di ambiente, è possibile creare uno script cmd piccoli che chiama l'attività di avvio.
- Può essere un'applicazione console o un file batch che avvia un [script di PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - specifica il livello di privilegi per l'attività di avvio. Il livello di privilegi può essere limitato o elevato:

- **limitato**  
L'attività di avvio viene eseguita con gli stessi privilegi equivalenti al ruolo. Se l'attributo **executionContext** per l'elemento [Runtime] anche è **limitato**, vengono usati privilegi.

- **con privilegi elevati**  
L'attività di avvio viene eseguita con privilegi di amministratore. In questo modo attività di avvio per installare i programmi, apportare modifiche alla configurazione IIS, eseguire le modifiche del Registro di sistema e altre attività di livello amministratore senza aumentare il livello di privilegi di ruolo stesso.  

> [AZURE.NOTE] Non è necessario che il livello di privilegi di un'attività di avvio corrispondere al ruolo stesso.

**taskType** - specifica la modalità di esecuzione di un'attività di avvio.

- **semplice**  
Le attività vengono eseguite in modo sincrono, uno alla volta, nell'ordine specificato nel file [ServiceDefinition.csdef] . Quando un'attività di avvio **semplice** termina con un **errorlevel** pari a zero, viene eseguita l'operazione di avvio **semplice** successiva. Se non sono presenti altre attività avvio **semplice** da eseguire, verrà avviato il ruolo stesso.   

    > [AZURE.NOTE] Se l'attività **semplice** termina con un diverso da zero **errorlevel**, verrà bloccata l'istanza. Attività successive di tipo **semplice** di avvio e il ruolo, non verrà avviato.

    Per assicurarsi che il file batch termina con un **errorlevel** pari a zero, eseguire il comando `EXIT /B 0` al termine del processo di file batch.

- **sfondo**  
Le attività vengono eseguite in modo asincrono, in parallelo con l'avvio del ruolo.

- **in primo piano**  
Le attività vengono eseguite in modo asincrono, in parallelo con l'avvio del ruolo. La chiave differenza tra un **piano** e un'attività **in background** è che un'attività **in primo piano** impedisce il ruolo dal riciclaggio o arresto fino al termine dell'attività. Le attività in **background** non dispone di questa limitazione.

## <a name="environment-variables"></a>Variabili di ambiente

Le variabili di ambiente sono un modo per passare informazioni a un'attività di avvio. Ad esempio, è possibile inserire il percorso a un blob che contiene un programma di installazione, i numeri di porta che verrà utilizzato il proprio ruolo o impostazioni alle funzionalità di controllo dell'attività avvio.

Esistono due tipi di variabili per attività di avvio. variabili statiche e variabili in base a membri della classe [RoleEnvironment] . Si verificano entrambe nella sezione [ambiente] del file [ServiceDefinition.csdef] e utilizzano l'attributo [variabile] elemento e il **nome** .

Le variabili di ambiente statico utilizza l'attributo **value** dell'elemento [variabile] . L'esempio precedente consente di creare la variabile di ambiente **MyVersionNumber** che presenta un valore statico di "**1.0.0.0**". Un altro esempio, è possibile creare una variabile di ambiente **StagingOrProduction** che è possibile impostare manualmente i valori della "**di gestione temporanea**" o "**produzione**" per eseguire azioni di avvio diverso in base al valore della variabile di ambiente **StagingOrProduction** .

Variabili in base a membri della classe RoleEnvironment non utilizzano l'attributo **value** dell'elemento [variabile] . Se, tuttavia, l'elemento figlio [RoleInstanceValue] , con il valore dell'attributo **XPath** appropriato, vengono utilizzati per creare una variabile di ambiente in base a un membro specifico della classe [RoleEnvironment] . Valori per l'attributo **XPath** accedere a diversi valori [RoleEnvironment] sono disponibili [qui](cloud-services-role-config-xpath.md).



Per creare una variabile di ambiente è "**true**" se l'istanza è in esecuzione in calcolo emulatore e "**false**" durante l'esecuzione nel cloud, ad esempio, utilizzare il [variabile] e [RoleInstanceValue] gli elementi seguenti:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come eseguire alcune [attività comuni di avvio](cloud-services-startup-tasks-common.md) con il servizio Cloud.

[Pacchetto](cloud-services-model-and-package.md) il servizio Cloud.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Attività]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Avvio]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Fase di esecuzione]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variabile]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx