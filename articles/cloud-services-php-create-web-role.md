<properties
    pageTitle="Ruoli web e lavoro PHP | Microsoft Azure"
    description="Guida alla creazione di ruoli di lavoro e web PHP in un servizio cloud Azure e configurazione di runtime PHP."
    services=""
    documentationCenter="php"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

#<a name="how-to-create-php-web-and-worker-roles"></a>Come creare ruoli web e lavoro PHP

## <a name="overview"></a>Panoramica

Questa guida illustra come creare PHP web o lavoro ruoli in un ambiente di sviluppo di Windows, scegliere una versione specifica di PHP delle versioni "predefinite" disponibili, modificare la configurazione di PHP, attivare le estensioni e infine distribuire Azure. Viene inoltre descritto come configurare un ruolo web o lavoro per usare una fase di esecuzione PHP (con configurazione personalizzata ed estensioni) specificati.

## <a name="what-are-php-web-and-worker-roles"></a>Quali sono i ruoli di lavoro e web PHP?

Azure offre tre calcolare modelli per l'esecuzione di applicazioni: servizio App Azure, macchine virtuali di Azure e servizi Cloud Windows Azure. Tutti i modelli di tre supportano PHP. Servizi cloud, che include i ruoli di lavoro e web, fornisce *piattaforma come servizio (PaaS)*. All'interno di un servizio cloud, un ruolo web fornisce un server web Internet Information Services (IIS) dedicato per ospitare applicazioni web front-end. Un ruolo di lavoro possono essere eseguiti asincrona, lunga o perpetue attività indipendenti di input o interazione dell'utente.

Per ulteriori informazioni su queste opzioni, vedere [calcolare le opzioni di hosting fornite da Azure](./cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Scaricare il SDK Azure per PHP

[Azure SDK per PHP] è costituito da diversi componenti. In questo articolo verranno utilizzati due delle quali: PowerShell Azure e Azure emulatori. Questi due componenti possono essere installati tramite l'installazione guidata piattaforma Web Microsoft. Per ulteriori informazioni, vedere [come installare e configurare Azure PowerShell](powershell-install-configure.md).

## <a name="create-a-cloud-services-project"></a>Creare un progetto di servizi Cloud

Creazione di un ruolo web o lavoro PHP il primo passaggio consiste nel creare un progetto di servizio di Azure. un progetto di Azure servizio funge da contenitore logico per i ruoli web e di lavoro e contiene il file di [definizione di servizio (.csdef)] e la [configurazione del servizio (. cscfg)] del progetto.

Per creare un nuovo progetto di servizio Azure, eseguire PowerShell Azure come amministratore ed eseguire il comando seguente:

    PS C:\>New-AzureServiceProject myProject

Questo comando verrà creata una nuova directory (`myProject`) a cui è possibile aggiungere ruoli web e di lavoro.

## <a name="add-php-web-or-worker-roles"></a>Aggiungere PHP web o lavoro ruoli

Per aggiungere un ruolo web PHP a un progetto, eseguire il seguente comando da qualsiasi directory radice del progetto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Per un ruolo di lavoro, usare questo comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [AZURE.NOTE] Il `roleName` parametro è facoltativo. Se viene omesso, verrà generato automaticamente il nome del ruolo. Il primo ruolo web creato sarà `WebRole1`, la seconda sarà `WebRole2`e così via. Il ruolo di lavoro primo creato sarà `WorkerRole1`, la seconda sarà `WorkerRole2`e così via.

## <a name="specify-the-built-in-php-version"></a>Specificare la versione PHP incorporata

Quando si aggiunge un ruolo PHP web o lavoro a un progetto, file di configurazione del progetto vengono modificati in modo che PHP verrà installato in ogni istanza web o lavoro dell'applicazione quando viene distribuito. Per visualizzare la versione di PHP installati per impostazione predefinita, eseguire il comando seguente:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

L'output del comando precedente sarà simile a quello mostrato sotto. In questo esempio, il `IsDefault` è impostato su `true` per PHP 5.3.17, che indica che questo sia installata la versione PHP predefinito.

    Runtime Version     PackageUri                      IsDefault
    ------- -------     ----------                      ---------
    Node 0.6.17         http://nodertncu.blob.core...   False
    Node 0.6.20         http://nodertncu.blob.core...   True
    Node 0.8.4          http://nodertncu.blob.core...   False
    IISNode 0.1.21      http://nodertncu.blob.core...   True
    Cache 1.8.0         http://nodertncu.blob.core...   True
    PHP 5.3.17          http://nodertncu.blob.core...   True
    PHP 5.4.0           http://nodertncu.blob.core...   False

È possibile impostare la versione di runtime PHP alle versioni PHP sono elencati. Ad esempio, per impostare la versione PHP (per un ruolo con il nome `roleName`) 5.4.0, usare il comando seguente:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [AZURE.NOTE] Versioni PHP disponibili possono cambiare in futuro.

## <a name="customize-the-built-in-php-runtime"></a>Personalizzare il runtime PHP predefinito

È possibile controllare completare la configurazione di runtime PHP installati quando si esegue la procedura precedente, compresa la modifica di `php.ini` impostazioni e l'attivazione delle estensioni.

Per personalizzare il runtime PHP predefinito, procedere come segue:

1. Aggiungere una nuova cartella denominata `php`, per il `bin` directory del ruolo web. Per un ruolo di lavoro, aggiungerlo alla directory radice del ruolo.
2. Nel `php` cartella, creare un'altra cartella denominata `ext`. Inserire alcun `.dll` file di estensione (ad esempio, `php_mongo.dll`) che si desidera abilitare in questa cartella.
3. Aggiungere un `php.ini` file per il `php` cartella. Abilitare le estensioni personalizzate e impostare le eventuali istruzioni PHP in questo file. Ad esempio, se si desidera attivare `display_errors` nella e abilitare il `php_mongo.dll` estensione, il contenuto del `php.ini` file sarà il seguente:

        display_errors=On
        extension=php_mongo.dll

> [AZURE.NOTE] Le impostazioni che non si imposta in, in modo esplicito i `php.ini` file fornire verranno automaticamente impostare valori predefiniti. Tuttavia, tenere presente che è possibile aggiungere un completo `php.ini` file.

## <a name="use-your-own-php-runtime"></a>Utilizzare il proprio runtime PHP
In alcuni casi, anziché la selezione di un runtime PHP predefinito e configurarla come descritto in precedenza, è consigliabile fornire il proprio runtime PHP. Ad esempio, è possibile utilizzare lo stessa runtime PHP in un ruolo web o lavoro utilizzate nel proprio ambiente di sviluppo. In questo modo più semplice per assicurarsi che l'applicazione non verrà modificati comportamento nel proprio ambiente di produzione.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurare un ruolo web per utilizzare il proprio runtime PHP

Per configurare un ruolo web per l'utilizzo di un runtime PHP che fornisce, procedere come segue:

1. Creare un progetto di servizio di Azure e aggiungere un ruolo web PHP, come descritto in precedenza in questo argomento.
2. Creare un `php` cartella la `bin` cartella in cui si trova nella directory radice del ruolo web e quindi aggiunta il runtime PHP (tutti i file binari, file di configurazione, sottocartelle, e così via) la `php` cartella.
3. (FACOLTATIVO) Se il runtime PHP utilizza i [Driver di Microsoft per PHP per SQL Server][sqlsrv drivers], sarà necessario configurare il proprio ruolo web per installare [SQL Server Native Client 2012] [ sql native client] quando eseguito il provisioning. A tale scopo, aggiungere il [programma di installazione di sqlncli.msi x64] per il `bin` cartella nella directory radice del ruolo web. Script di avvio descritto nel passaggio successivo verrà eseguito automaticamente il programma di installazione quando viene eseguito il provisioning il ruolo. Se il runtime PHP non utilizza Drivers Microsoft per PHP per SQL Server, è possibile rimuovere la riga seguente dallo script illustrato nel passaggio successivo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definire un'attività di avvio per la configurazione di [Internet Information Services (IIS)] [ iis.net] come utilizzare il runtime PHP per gestire le richieste di `.php` pagine. A tale scopo, aprire la `setup_web.cmd` file (nel `bin` file della directory radice del ruolo web) in un editor di testo e sostituire il contenuto con il seguente script:

        @ECHO ON
        cd "%~dp0"

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
        SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
        %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"

5. Aggiungere i file dell'applicazione nella radice del ruolo web. Questo sarà directory radice del server web.

6. Pubblicare l'applicazione, come descritto nella sezione [pubblica l'applicazione](#how-to-publish-your-application) .

> [AZURE.NOTE] Il `download.ps1` script (nel `bin` cartella della directory radice del ruolo web) possono essere eliminati dopo l'esecuzione la procedura descritta in precedenza per usare il proprio runtime PHP.

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurare un ruolo di lavoro per usare il proprio runtime PHP

Per configurare un ruolo di lavoro per usare un runtime PHP che fornisce, procedere come segue:

1. Creare un progetto di servizio di Azure e aggiungere un ruolo di lavoro PHP, come descritto in precedenza in questo argomento.
2. Creare un `php` cartella nella directory radice del ruolo di lavoro e quindi aggiungere il runtime PHP (tutti i file binari, file di configurazione, sottocartelle, e così via) la `php` cartella.
3. (FACOLTATIVO) Se il runtime PHP utilizza [Driver Microsoft per PHP per SQL Server][sqlsrv drivers], sarà necessario configurare il proprio ruolo di lavoro per installare [SQL Server Native Client 2012] [ sql native client] quando eseguito il provisioning. A tale scopo, aggiungere il [programma di installazione di sqlncli.msi x64] nella radice del ruolo di lavoro. Script di avvio descritto nel passaggio successivo verrà eseguito automaticamente il programma di installazione quando viene eseguito il provisioning il ruolo. Se il runtime PHP non utilizza Drivers Microsoft per PHP per SQL Server, è possibile rimuovere la riga seguente dallo script illustrato nel passaggio successivo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

4. Definire un'attività di avvio che aggiunge il `php.exe` eseguibile alla variabile di ambiente PATH del ruolo di lavoro quando viene eseguito il provisioning il ruolo. A tale scopo, aprire la `setup_worker.cmd` dei file (nella directory radice del ruolo di lavoro) in un editor di testo e sostituire il contenuto con il seguente script:

        @echo on

        cd "%~dp0"

        echo Granting permissions for Network Service to the web root directory...
        icacls ..\ /grant "Network Service":(OI)(CI)W
        if %ERRORLEVEL% neq 0 goto error
        echo OK

        if "%EMULATED%"=="true" exit /b 0

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

        setx Path "%PATH%;%~dp0php" /M

        if %ERRORLEVEL% neq 0 goto error

        echo SUCCESS
        exit /b 0

        :error

        echo FAILED
        exit /b -1

5. Aggiungere i file dell'applicazione nella radice del ruolo di lavoro.

6. Pubblicare l'applicazione, come descritto nella sezione [pubblica l'applicazione](#how-to-publish-your-application) .

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Eseguire l'applicazione in emulatori di elaborazione e di archiviazione

Emulatori Azure forniscono un ambiente locale in cui è possibile testare l'applicazione Azure prima di distribuirlo nel cloud. Esistono alcune differenze tra gli emulatori e l'ambiente di Azure. Per comprendere meglio questo, vedere [usare emulatore Azure dello spazio di archiviazione per sviluppo e la verifica](./storage/storage-use-emulator.md).

Si noti che è necessario disporre PHP installato in locale per utilizzare l'emulatore di calcolo. Emulatore calcolo utilizzerà l'installazione di PHP locale per eseguire l'applicazione.

Per eseguire il progetto negli emulatori, eseguire il comando seguente dalla directory radice del progetto:

    PS C:\MyProject> Start-AzureEmulator

Verrà visualizzato output simile alla seguente:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

È possibile visualizzare l'applicazione in esecuzione nell'emulatore aprendo un web browser e cercare l'indirizzo locale illustrato nell'output (`http://127.0.0.1:81` nell'output dell'esempio precedente).

Per interrompere l'emulatori, eseguire il comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Pubblicare l'applicazione

Per pubblicare l'applicazione, è necessario innanzitutto importare le impostazioni di pubblicazione utilizzando il cmdlet [AzurePublishSettingsFile importazione](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Quindi è possibile pubblicare l'applicazione utilizzando il cmdlet [AzureServiceProject pubblica](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Per informazioni sull'accesso, vedere [come installare e configurare Azure PowerShell](powershell-install-configure.md).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere il [Centro per sviluppatori PHP](/develop/php/).

[Azure SDK per PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definizione di servizio (.csdef)]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configurazione del servizio (. cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[programma di installazione di SQLNCLI.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
