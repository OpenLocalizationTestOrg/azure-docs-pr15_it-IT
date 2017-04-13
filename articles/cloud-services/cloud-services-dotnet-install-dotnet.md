<properties
   pageTitle="Installare .NET in un ruolo di servizio Cloud | Microsoft Azure"
   description="In questo articolo viene descritto come installare manualmente .NET framework nel Cloud servizio Web ruoli e di lavoro"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Installare .NET in un ruolo di servizio Cloud 

In questo articolo viene descritto come installare .NET framework nel Cloud servizio Web ruoli e di lavoro. È possibile utilizzare la procedura seguente per installare .NET 4.6.1 su Azure Guest OS famiglia 4. Per informazioni aggiornate sulle versioni di sistemi operativi Guest vedere [Azure Guest OS rilascia e nella matrice compatibilità SDK](cloud-services-guestos-update-matrix.md).

Il processo di installazione .NET sui ruoli web e lavoro comporta l'inclusi il programma di installazione .NET come parte del progetto Cloud e avviare il programma di installazione come parte di attività di avvio del ruolo.  

## <a name="add-the-net-installer-to-your-project"></a>Aggiungere il programma di installazione di .NET al progetto
- Scaricare il programma di installazione web per .NET framework che si desidera installare
    - [.NET 4.6.1 web programma di installazione](http://go.microsoft.com/fwlink/?LinkId=671729)
- Per un ruolo Web
  1. In **Esplora soluzioni**in **ruoli** del progetto di servizio cloud destro del mouse fare clic sul ruolo e selezionare **Aggiungi > nuova cartella**. Creare una cartella denominata *bin*
  2. Fare clic con il pulsante destro nella cartella **bin** e selezionare **Aggiungi > elemento esistente**. Selezionare il programma di installazione di .NET e aggiungerlo alla cartella bin.
- Per un ruolo di lavoro
  1. Fare clic con il pulsante destro sul ruolo e selezionare **Aggiungi > elemento esistente**. Selezionare il programma di installazione di .NET e aggiungerlo al ruolo. 

Aggiungere file in questo modo nella cartella contenuto ruolo verrà automaticamente aggiunto al pacchetto di servizi cloud e distribuito in una posizione coerente nella macchina virtuale. Ripetere questa procedura per tutti i ruoli web e il servizio Cloud, in modo che tutti i ruoli di avere una copia del programma di installazione.

> [AZURE.NOTE] È necessario installare .NET 4.6.1 sul proprio ruolo di servizio Cloud, anche se l'applicazione è destinata 4.6 .NET. Il sistema operativo Guest Azure include aggiornamenti [3098779](https://support.microsoft.com/kb/3098779) e [3097997](https://support.microsoft.com/kb/3097997). L'installazione di .NET 4.6 nella parte superiore di questi aggiornamenti potrebbe causare problemi durante l'esecuzione di applicazioni .NET, quindi è necessario installare direttamente .NET 4.6.1 anziché .NET 4.6. Per ulteriori informazioni, vedere [3118750 KB](https://support.microsoft.com/kb/3118750).

![Contenuto ruolo con i file di programma di installazione][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definire le attività di avvio per i ruoli
Attività di avvio consentono di eseguire operazioni prima dell'avvio di un ruolo. L'installazione di .NET Framework come parte dell'attività avvio assicurarsi che sia installato il framework prima di eseguire qualsiasi codice dell'applicazione. Per ulteriori informazioni sull'avvio di attività, vedere: [Eseguire attività di avvio in Azure](cloud-services-startup-tasks.md). 

1. Aggiungere il file *ServiceDefinition.csdef* il nodo **WebRole** o **WorkerRole** per tutti i ruoli quanto segue:
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    La configurazione precedente verrà eseguito il comando console *Install* con privilegi di amministratore in modo che è possibile installare .NET framework. La configurazione crea anche un LocalStorage con il nome *NETFXInstall*. Script di avvio opzione consente di impostare la cartella temp per utilizzare questa risorsa di archiviazione locale in modo che il programma di installazione di .NET framework verrà scaricato e installato da questa risorsa. È importante impostare le dimensioni della risorsa a almeno 1024MB per assicurarsi che il framework viene installato correttamente. Per ulteriori informazioni sull'avvio attività, vedere: [servizio Cloud comuni attività di avvio](cloud-services-startup-tasks-common.md) 

2. Creare un file **Install** e aggiungerla a tutti i ruoli da destra fare clic sul ruolo e selezionando **Aggiungi > elemento esistente...**. In modo che tutti i ruoli dovrebbero ora essere il file di programma di installazione di .NET, nonché il file Install.
    
    ![Contenuto ruolo con tutti i file][2]

    > [AZURE.NOTE] Utilizzare un editor di testo semplice come il blocco note per creare il file. Se si utilizza Visual Studio per creare un file di testo e quindi rinominarlo a 'cmd' il file potrebbe contenere ancora un ordine UTF-8 byte ed eseguendo la prima riga dello script verrà generato un errore. Se utilizza Visual Studio per creare lascia file aggiungere una REM (nota) per la prima riga del file in modo che viene ignorato durante l'esecuzione. 

3. Aggiungere il seguente script per il file **Install** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    Lo script di installazione verifica se la versione di .NET framework specificata è già installata nel computer eseguendo il Registro di sistema. Se non è installata la versione di .NET quindi .net programma di installazione Web viene avviata. Per risolvere eventuali problemi lo script verrà registrare tutte le attività in un file denominato *startuptasklog-(currentdatetime) txt* archiviati in archiviazione locale *InstallLogs* .

    > [AZURE.NOTE] Lo script include ancora come installare .NET 4.5.2 o .NET 4.6 per continuità. Non è necessario installare manualmente .NET 4.5.2 come è già disponibile nel sistema operativo Guest Azure. Anziché l'installazione di .NET 4.6 installare direttamente .NET 4.6.1 a causa di [3118750 KB](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurare la diagnostica per trasferire i registri di attività di avvio per nell'archiviazione blob 
Per semplificare la risoluzione dei problemi di installazione è possibile configurare Azure diagnostica per trasferire i file di log generati da script di avvio o il programma di installazione di .NET a archiviazione blob. Con questo approccio è possibile visualizzare i log semplicemente il download dei file di log dallo spazio di archiviazione blob anziché dover desktop remoto al ruolo di.

Per configurare diagnostica Apri *diagnostics.wadcfgx* e aggiungere quanto segue per il nodo **Directory** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Consente di configurare diagnostica Windows azure per trasferire tutti i file nella cartella *log* in risorsa *NETFXInstall* nell'account di archiviazione diagnostica nel contenitore di blob *netfx installa* .

## <a name="deploying-your-service"></a>Distribuzione del servizio 
Quando si distribuisce il servizio le attività di avvio verranno eseguito e installare .NET framework se non è già installato. I ruoli sarà nello stato occupato mentre il framework installa e potrebbe anche riavviare se lo richiede l'installazione di framework. 

## <a name="additional-resources"></a>Risorse aggiuntive

- [Installazione di .NET Framework][]
- [Procedura: determinare quali versioni di .NET Framework sono installati][]
- [Risoluzione dei problemi di installazione di .NET Framework][]

[Procedura: determinare quali versioni di .NET Framework sono installati]: https://msdn.microsoft.com/library/hh925568.aspx
[Installazione di .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Risoluzione dei problemi di installazione di .NET Framework]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 
