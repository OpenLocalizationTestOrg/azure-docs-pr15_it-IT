<properties
   pageTitle="Risoluzione dei problemi di Client Docker in Windows utilizzando Visual Studio | Microsoft Azure"
   description="Risolvere i problemi riscontrati quando si utilizza Visual Studio per creare e distribuire applicazioni web Docker in Windows tramite Visual Studio."
   services="azure-container-service"
   documentationCenter="na"
   authors="mlearned"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# <a name="troubleshooting-visual-studio-docker-development"></a>Risoluzione dei problemi di sviluppo Docker Visual Studio

Quando si lavora con Visual Studio Tools per Docker Preview, potrebbero verificarsi problemi per le caratteristiche di anteprima.
Ecco alcuni problemi e soluzioni comuni.


## <a name="unable-to-validate-volume-mapping"></a>Impossibile convalidare mapping volume
Mapping volume è necessario per condividere il codice sorgente e file binari dell'applicazione con la cartella app nel contenitore.  Mapping volume specifico sono contenuti in file docker compose.dev.debug.yml e docker compose.dev.release.yml. Come vengono modificati i file sul computer host, i contenitori rifletteranno tali modifiche in una struttura di cartelle simili.

Per abilitare il mapping di volume, aprire **Impostazioni** dall'icona "moby" Docker per Windows e quindi selezionare la scheda **Condiviso unità** .  Assicurarsi che la lettera che ospita il progetto, nonché la lettera di unità in cui si trova % profiloutente % sono condivisi da archiviarli e quindi fare clic su **Applica**.

Per verificare se funziona mapping volume, una volta le unità sono stati condivisi, rigenerazione e F5 da Visual Studio o provare a seguito da un comando Chiedi conferma:

*In un prompt dei comandi di Windows*

*[Nota: questo si presuppone che si trova nella cartella utenti nell'unità "C" e che è stato condiviso.  Se si è condiviso un'unità diversa, aggiornare in base alle esigenze]*
```
docker run -it -v /c/Users/Public:/wormhole busybox
```

*Nel contenitore di Linux*

```
/ # ls
```

Verrà visualizzato un elenco dalla cartella utenti/pubblico di directory.
Se non sono visualizzati file e cartella /c/Users/Public non è vuota, mapping volume non è configurato correttamente. 

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Modificare nella directory spaziale per visualizzare il contenuto del `/c/Users/Public` directory:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

**Nota:** *Quando si lavora con macchine virtuali Linux, il file system contenitore è tra maiuscole e minuscole.*

##<a name="build--prepareforbuild-task-failed-unexpectedly"></a>Compilazione: "PrepareForBuild" Errore imprevisto.

Microsoft.DotNet.Docker.CommandLine.ClientException: Errore durante il tentativo di connessione:

Verificare che l'host docker predefinito è in esecuzione. Aprire un prompt dei comandi ed eseguire:

```
docker info
```

Se viene restituito un errore tentare di avviare l'app desktop **Docker per Windows** .  Se l'app desktop è in esecuzione quindi l'icona di **moby** nella barra delle applicazioni dovrebbe essere visibile. Fare clic con il pulsante destro sull'icona della barra delle applicazioni e aprire **le impostazioni**.  Fare clic su **Reimposta** scheda e quindi **Restart Docker...**.

##<a name="manually-upgrading-from-version-031-to-040"></a>Manualmente l'aggiornamento dalla versione 0.31 a 0,40


1. Eseguire il backup del progetto
1. Eliminare i file seguenti nel progetto:

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. Chiudere la soluzione e rimuovere le righe seguenti del file .xproj:

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. Riaprire la soluzione
1. Rimuovere le righe seguenti del file Properties\launchSettings.json:

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. Rimuovere i file seguenti relativi a Docker da project.json nel publishOptions:

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. Disinstallare la versione precedente e installare nuovamente Docker strumenti 0,40, quindi **Aggiungi -> supporto Docker** dal menu di scelta rapida per la propria Web ASP.Net Core o applicazione Console. I nuovi elementi Docker necessari verrà aggiunto al progetto. 

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Un messaggio di errore si verifica quando si tenta di **aggiungere -> supporto Docker** o Debug (F5) di un'applicazione di base di ASP.NET in un contenitore

In alcuni casi finora la disinstallazione e l'installazione estensioni, la cache MEF (Managed Extensibility Framework) di Visual Studio vengano danneggiata. In questo caso si può provocare errori varie finestre di dialogo durante l'aggiunta di supporto Docker e/o si tenta di eseguire o il Debug (F5) dell'applicazione di base ASP.NET. Per ovviare al temporanei, eseguire la procedura seguente per eliminare e rigenerare la cache MEF.

1. Chiudere tutte le istanze di Visual Studio
1. Aprire %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\
1. Eliminare le cartelle seguenti
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Aprire Visual Studio
1. Ritenta lo scenario 
