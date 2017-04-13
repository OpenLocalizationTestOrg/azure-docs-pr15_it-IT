<properties
    pageTitle="Risoluzione dei problemi di diagnostica Windows Azure"
    description="Risoluzione dei problemi quando si utilizza diagnostica Windows Azure in servizi Cloud di Windows Azure, macchine virtuali e "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Risoluzione dei problemi di diagnostica Windows Azure
Risoluzione dei problemi di informazioni relative all'utilizzo di Azure diagnostica. Per ulteriori informazioni sulla diagnostica Windows Azure, vedere [Panoramica di diagnostica Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Diagnostica Windows Azure non viene avviato

Diagnostica è costituita da due componenti: un agente di guest plug-in e l'agente di monitoraggio. È possibile controllare i file di log **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** per informazioni su perché non è possibile avviare diagnostica.  
  
In un ruolo di servizio Cloud, file di log per il plug-in agente guest si trovano: 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

In un computer di virtuale di Azure, file di log per il plug-in agente guest si trovano: 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
L'ultima riga del file di log conterrà il codice di uscita.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

Il plug-in restituisce i codici di uscita seguenti:

Codice di uscita|Descrizione
---|---
0|Success.
-1|Errore generico.
-2|Se non è possibile caricare il file rcf.<p>Si tratta di un errore interno che dovrà essere solo attivata se in modo errato, l'icona di avvio delle plug-in di agente guest manualmente viene richiamato nella macchina virtuale.
-3|Non è possibile caricare il file di configurazione di diagnostica.<p><p>Soluzione: Questo è il risultato di un file di configurazione non passando convalida dello schema. La soluzione è fornire un file di configurazione conformità con lo schema.
-4|Un'altra istanza dell'agente di monitoraggio diagnostica sta già utilizzando la directory di risorse locale.<p><p>Soluzione: Specificare un valore diverso per **LocalResourceDirectory**.
-6|Avvio di plug-in dell'agente di guest tentato di diagnostica di avvio con una riga di comando non valida.<p><p>Si tratta di un errore interno che dovrà essere solo attivata se in modo errato, l'icona di avvio delle plug-in di agente guest manualmente viene richiamato nella macchina virtuale.
-10|Plug-in di diagnostica terminato con un'eccezione non gestita.
-11|Agente di guest: Impossibile creare il processo responsabile per l'avvio e monitorare l'agente di monitoraggio.<p><p>Soluzione: Verificare che siano disponibili per avviare nuovi processi sufficienti risorse di sistema.<p>
-101|Argomenti non validi quando si chiama il plug-in di diagnostica.<p><p>Si tratta di un errore interno che dovrà essere solo attivata se in modo errato, l'icona di avvio delle plug-in di agente guest manualmente viene richiamato nella macchina virtuale.
-102|Il processo di plug-in: Impossibile inizializzare stesso.<p><p>Soluzione: Verificare che siano disponibili per avviare nuovi processi sufficienti risorse di sistema.
-103|Il processo di plug-in: Impossibile inizializzare stesso. In particolare non è possibile creare l'oggetto logger.<p><p>Soluzione: Verificare che siano disponibili per avviare nuovi processi sufficienti risorse di sistema.
-104|Se non è possibile caricare il file rcf fornito dall'operatore guest.<p><p>Si tratta di un errore interno che dovrà essere solo attivata se in modo errato, l'icona di avvio delle plug-in di agente guest manualmente viene richiamato nella macchina virtuale.
-105|Plug-in di diagnostica non è possibile aprire il file di configurazione di diagnostica.<p><p>Si tratta di un errore interno che dovrà essere solo attivata se in modo non corretto, il plug-in di diagnostica manualmente viene richiamato nella macchina virtuale.
-106|Non è possibile leggere il file di configurazione di diagnostica.<p><p>Soluzione: Questo è il risultato di un file di configurazione non passando convalida dello schema. Pertanto la soluzione è fornire un file di configurazione conformità con lo schema. È possibile trovare il codice XML che viene inviato per l'estensione di diagnostica nella cartella più *%SystemDrive%\WindowsAzure\Config* nella macchina virtuale. Aprire il file XML e la ricerca per **Microsoft.Azure.Diagnostics**, quindi per il campo **xmlCfg** appropriato. I dati sono base 64 codificato in modo che è necessario decodificare [viene](http://www.bing.com/search?q=base64+decoder) visualizzato il codice XML caricati dalla diagnostica.<p>
-107|Il passaggio di directory delle risorse per l'agente di monitoraggio non è valido.<p><p>Si tratta di un errore interno che dovrà essere solo attivata se in modo errato, l'agente di monitoraggio manualmente viene richiamato nella macchina virtuale.</p>
-108    |Impossibile convertire il file di configurazione di diagnostica nel file di configurazione dell'Agente monitoraggio.<p><p>Si tratta di un errore interno che dovrà essere attivata solo se il plug-in di diagnostica viene richiamato manualmente con un file di configurazione non validi.
-110|Errore di configurazione di diagnostica generale.<p><p>Si tratta di un errore interno che dovrà essere attivata solo se il plug-in di diagnostica viene richiamato manualmente con un file di configurazione non validi.
-111|Impossibile avviare l'agente di monitoraggio.<p><p>Soluzione: Verificare che siano disponibili sufficienti risorse di sistema.
-112|Errore generico


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dati di diagnostica non sono ha eseguito l'accesso allo spazio di archiviazione di Azure
Diagnostica Windows Azure archivia tutti i dati in archiviazione Azure.

La causa più comune di evento dati mancanti è informazioni sull'account di archiviazione definita in modo non corretto.

Soluzione: Correggere il file di configurazione di diagnostica e reinstallare diagnostica.
Se il problema persiste dopo la reinstallazione l'estensione di diagnostica quindi potrebbe essere necessario eseguire il debug ulteriormente riconoscibili scorrere gli eventuali errori Agente monitoraggio. Prima che i dati dell'evento viene caricati al proprio account di archiviazione vengono archiviato nel LocalResourceDirectory.

Per il ruolo di servizio Cloud di LocalResourceDirectory è:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Per macchine virtuali di LocalResourceDirectory è:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se non sono presenti file nella cartella LocalResourceDirectory, l'agente di monitoraggio in grado di avvio. Questo è in genere causato da un file di configurazione non valido, un evento che deve essere segnalato nel CommandExecution.log.

Se l'agente di monitoraggio correttamente è la raccolta di dati dell'evento che verranno visualizzati i file .tsf per ogni evento definito nel file di configurazione. L'agente di monitoraggio registri suoi errori nel file MaEventTable.tsf. Per controllare il contenuto del file è possibile utilizzare l'applicazione tabel2csv per convertire il file .tsf in un file di values(.csv) separati da virgola:

In un ruolo di servizio Cloud:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% SystemDrive %* su un ruolo di servizio Cloud è in genere d:

In un computer virtuale:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

I comandi sopra genera log file *maeventtable.csv*, che è possibile aprire e verificare la presenza per i messaggi di errore.    


## <a name="diagnostics-data-tables-not-found"></a>Dati di diagnostica tabelle non trovate
Le tabelle di archiviazione Azure contenente dati di diagnostica Windows Azure vengono assegnate il codice riportato di seguito:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Ecco un esempio:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Che verrà generato 4 tabelle:

Evento|Nome della tabella
---|---
provider = "prov1" &lt;id evento = "1" /&gt;|WADEvent + MD5("prov1") + "1"
provider = "prov1" &lt;id evento = "2" eventDestination = "dest1" /&gt;|WADdest1
provider = "prov1" &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
provider = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt;|WADdest2
