<properties 
    pageTitle="Funzionalità del sistema operativo del servizio di App Azure" 
    description="Informazioni sulle funzionalità del sistema operativo disponibili per web apps, app per dispositivi mobili back-end e App API del servizio di App Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="mollybos"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/01/2016" 
    ms.author="cephalin"/>

# <a name="operating-system-functionality-on-azure-app-service"></a>Funzionalità del sistema operativo del servizio di App Azure #

Questo articolo descrive le funzioni di sistema operativo previsto comuni che sono disponibile per tutte le App in esecuzione in [Azure App servizio](http://go.microsoft.com/fwlink/?LinkId=529714). Questa funzionalità include file, rete e accesso del Registro di sistema e registri di diagnostica e gli eventi. 

<a id="tiers"></a>
## <a name="app-service-plan-tiers"></a>Livelli di piano di servizio di App

Servizio App viene eseguito App cliente in un ambiente di hosting multi-tenant. App distribuita in livelli **gratuito** e **condivisi** attive in processi di lavoro in macchine virtuali condivise, durante l'esecuzione di App distribuita in **Premium** livelli e **Standard** in macchine virtuali dedicato specificamente per le app associate a un singolo cliente.

Poiché servizio App supporta un'esperienza scala tra diversi livelli, la configurazione di sicurezza per le applicazioni di servizio App rimarrà invariato. In questo modo che le applicazioni non improvvisamente hanno un comportamento diverso, non funziona in modo imprevisto, al piano di servizio App passa da un livello a un altro.

<a id="developmentframeworks"></a>
## <a name="development-frameworks"></a>Piattaforme di sviluppo

Livelli di servizio App prezzi controllano la quantità di risorse di elaborazione (CPU, spazio su disco, memoria e uscita di rete) disponibili per le applicazioni. Tuttavia, l'ampiezza della funzionalità framework disponibile alle App rimarrà invariato indipendentemente dalle proporzioni livelli.

Servizio di App supporta una varietà di piattaforme di sviluppo, tra cui ASP.NET, ASP classico, Node, PHP e Python - che eseguono tutti come estensioni in IIS. Per semplificare e normalizzare configurazione della protezione, le applicazioni di servizio App in genere eseguire varie piattaforme di sviluppo con le impostazioni predefinite. Un approccio per la configurazione di App potrebbe sono state per personalizzare la superficie API e funzionalità per ogni framework sviluppo singoli. Servizio App ha invece un approccio più generico, consentendo una previsione comune funzionalità del sistema operativo indipendentemente dalla struttura di sviluppo dell'applicazione.

Nelle sezioni seguenti sono riepilogano tipi generali di sistema operativo funzionalità disponibili per le applicazioni di servizio di App.

<a id="FileAccess"></a>
##<a name="file-access"></a>Accesso ai file

All'interno del servizio di App, tra cui unità locale e unità di rete sono presenti varie unità.

<a id="LocalDrives"></a>
### <a name="local-drives"></a>Unità locale

Di base App è un servizio in esecuzione nella parte superiore dell'infrastruttura di Azure PaaS (piattaforma come servizio). Di conseguenza, le unità locale "collegati" a una macchina virtuale sono gli stessi tipi di unità disponibili per qualsiasi ruolo di lavoro in esecuzione in Azure. Ad esempio un'unità di sistema operativo (unità D:\), un'unità di applicazione che contiene il pacchetto di Azure cspkg file usato esclusivamente dal servizio App (e accessibile ai clienti) e un'unità di "utente" (unità C:\), la cui dimensione varia a seconda delle dimensioni della macchina virtuale.

<a id="NetworkDrives"></a>
### <a name="network-drives-aka-unc-shares"></a>Unità di rete (o UNC condivide)

Uno degli aspetti del servizio di App che rende semplice manutenzione e la distribuzione app è che tutto il contenuto utente viene archiviato in un set di condivisioni UNC. Questo modello cartine perfettamente al modello comuni di archiviazione del contenuto utilizzato per gli ambienti che dispone di più server di bilanciamento del carico di hosting web locale. 

All'interno del servizio di App, sono disponibili numerose condivisioni UNC create in ogni data center. Una percentuale del contenuto per tutti i clienti in ogni centro dati utente viene assegnata a ogni condivisione UNC. Inoltre, tutti i file di contenuto per abbonamento del cliente singola viene sempre inserito nella stessa UNC condividere. 

Si noti che causa la modalità di servizi cloud, la macchina virtuale specifica responsabile per l'hosting una condivisione UNC cambierà nel tempo. Garantisce che condivisioni UNC verranno installate da diverse macchine virtuali come vengono visualizzati su e giù durante le normali operazioni cloud. Per questo motivo, App non apportare mai ipotesi hardcoded che le informazioni sul computer in un percorso UNC rimarrà stabile nel tempo. Se, tuttavia, è necessario utilizzare il comodo *simulato* assoluto **D:\home\site** che contiene un servizio di App. Questo percorso assoluto simulato fornisce un metodo portatile e indipendente di app e utente per fare riferimento ai propri app. Utilizzando **D:\home\site**uno trasferire file condivisi da app app senza che sia necessario configurare un nuovo percorso assoluto per ogni trasferimento.

<a id="TypesOfFileAccess"></a>
### <a name="types-of-file-access-granted-to-an-app"></a>Tipi di accesso ai file concesso a un'app

Abbonamento ogni cliente ha una struttura di directory riservati in una condivisione UNC specifica all'interno di un data center. Un cliente, potrebbe essere più App create all'interno di un centro di dati specifici, in modo che tutte le directory che appartengono a un abbonamento singolo cliente vengono create nella stessa UNC condividere. L'opzione Condividi possono includere directory, ad esempio quelli per il contenuto, errore e registri diagnostici e versioni precedenti di app creati dal controllo origine. Come previsto, directory app del cliente sono disponibili per l'accesso in lettura e scrittura in fase di esecuzione dal codice dell'applicazione dell'applicazione.

Nelle unità locale collegata al computer virtuale che viene eseguita un'app, servizio App riserva un blocco di spazio sull'unità C:\ per l'archiviazione locale temporaneo specifici di app. Sebbene un'app ha accesso completo in lettura/scrittura alla propria temporanea locale, che lo spazio di archiviazione veramente non deve essere utilizzato direttamente dal codice dell'applicazione. Lo scopo è invece fornire spazio di archiviazione file temporanei per IIS e web Framework applicazione. Servizio App limita anche lo spazio di archiviazione locale temporanea disponibili per ogni app per impedire alle singole applicazioni di utilizzo quantità eccessiva di spazio di archiviazione file locale.

Due esempi delle modalità di utilizzo temporaneo archivio locale del servizio di App sono la directory dei file temporanei ASP.NET e file compressi nella directory di IIS. Il sistema di compilazione ASP.NET viene utilizzata la directory "File temporanei ASP.NET" come un percorso della cache di compilazione temporanea. IIS viene utilizzata la directory "IIS compressi temporanei" per archiviare l'output di risposta compressa. Entrambi i tipi di file utilizzo (, nonché gli altri utenti) vengono rimappati nel servizio di App nell'archivio locale temporaneo-app. Questo mapping garantisce che funzionalità continui come previsto.

Ogni app nel servizio App viene eseguito come un'identità di processo di lavoro di privilegi di basso livello univoco casuale denominata il "identità pool di applicazioni", descritto di seguito ulteriormente: [http://www.iis.net/learn/manage/configuring-security/application-pool-identities](http://www.iis.net/learn/manage/configuring-security/application-pool-identities). Il codice dell'applicazione utilizza tale identità di accesso in sola lettura base per l'unità di sistema operativo (unità D:\). Questo significa che il codice dell'applicazione possa elenco strutture di directory comuni e leggere file comuni in unità di sistema operativo. Anche se questo potrebbe sembrare un po' livello di accesso, la stessa directory e i file sono accessibili durante il provisioning un ruolo di lavoro in un Azure ospitato servizio e leggerne il contenuto di unità. 

<a name="multipleinstances"></a>
### <a name="file-access-across-multiple-instances"></a>Accesso ai file in più istanze

La home directory contenuto dell'applicazione e possibile scrivere codice dell'applicazione. Se un'app viene eseguita su più istanze, la home directory verrà condivise tra tutte le istanze in modo che tutte le istanze di visualizzare la stessa directory. Pertanto, ad esempio, se un'app Salva file caricati alla home directory, tali file sono immediatamente disponibili per tutte le istanze. 

<a id="NetworkAccess"></a>
## <a name="network-access"></a>Accesso alla rete
Il codice dell'applicazione può usare TCP/IP e le connessioni a endpoint accessibile Internet che espongono servizi esterni di rete protocolli UDP in base a rendere in uscita. App è possono utilizzare questi stessi protocolli per connettersi ai servizi all'interno di Azure & #151, ad esempio attivando HTTPS connessioni al Database SQL.

È inoltre disponibile una funzionalità limitate per le applicazioni di una connessione loopback locale, un'app ascolto socket loopback locale. Questa caratteristica esiste principalmente per abilitare l'App ascolto sockets loopback locale come parte delle funzionalità. Si noti che ogni app rileva una connessione loopback "privato". app "A" non è possibile ascoltare un socket loopback locale stabilito dall'app "B".

Named pipes sono supportati anche come meccanismo di tra processi comunicazione tra processi diversi che eseguono collettivamente un'app. Ad esempio, il modulo IIS FastCGI si basa su named pipes per coordinare singoli processi che eseguono pagine PHP.


<a id="Code"></a>
## <a name="code-execution-processes-and-memory"></a>Esecuzione di codice, processi e della memoria
Come indicato in precedenza, App eseguono all'interno di processi di lavoro di privilegi di basso livello utilizzando un'identità pool di applicazioni casuali. Il codice dell'applicazione ha accesso allo spazio di memoria associato il processo di lavoro, nonché tutti i processi figlio potrebbero generate dai processi CGI o altre applicazioni. Tuttavia, un'app non è possibile accedere al memoria o dati di un'altra app anche se si tratta nello stesso computer virtuale.

App è possono eseguire script o pagine scritte con piattaforme di sviluppo di web supportato. Servizio di App non configurare le impostazioni di framework web alla modalità più limitata. Ad esempio le applicazioni ASP.NET in esecuzione del servizio di App eseguire "attendibilità" anziché una modalità di protezione più limitata. Framework Web, inclusi classico ASP e ASP.NET, è possibile chiamare componenti COM in corso (ma non in uscita componenti del processo COM) come ADO (ActiveX Data Objects) registrati per impostazione predefinita nel sistema operativo Windows.

App possono generare ed eseguire il codice non autorizzato. È consentito per un'app eseguire operazioni come creare una shell comandi o eseguire uno script di PowerShell. Anche se codice non autorizzato e processi possono essere generati da un'app, script e programmi sono ancora limitate per i privilegi concessi al pool di applicazioni padre. Ad esempio un'app può generare un file eseguibile che effettua una chiamata in uscita HTTP, ma che stesso eseguibile non è possibile provare a separare l'indirizzo IP di una macchina virtuale dalla relativa scheda. Effettuare una chiamata di rete in uscita consentito al codice di privilegi di basso livello, ma se si tenta di riconfigurare le impostazioni di rete in un computer virtuale richiede privilegi di amministratore.


<a id="Diagnostics"></a>
## <a name="diagnostics-logs-and-events"></a>Log di diagnostica ed eventi
Informazioni del log sono un'altra coppia di dati che alcune applicazioni tentano di accedere. I tipi di file di log disponibili nel codice eseguito nel servizio di App include diagnostici e registrare le informazioni generate da un'app che è possibile accedere facilmente all'app. 

Ad esempio, i registri W3C HTTP generati da un'app active sono disponibili sia in una cartella log il percorso di condivisione di rete creato per l'app o disponibili in archiviazione blob se un cliente ha configurato l'archiviazione registrazione W3C allo spazio di archiviazione. La seconda opzione Abilita grandi quantità di registri per raccogliere senza il rischio di superiori ai limiti di spazio di archiviazione dei file associati a una condivisione di rete.

Infine proposta, informazioni di diagnostica in tempo reale da applicazioni .NET possono anche essere eseguito l'accesso usando l'analisi .NET e infrastruttura di diagnostica, con le opzioni per scrivere le informazioni di traccia di condivisione di rete dell'app, oppure a una posizione di archiviazione blob.

Aree di diagnostica registrazione e analisi che non è disponibile per App sono gli eventi di Windows ETW e registri eventi di Windows comuni (ad esempio, sistema, applicazione e protezione i registri eventi). Poiché le informazioni di traccia ETW potrebbero essere visti dall'utente per l'intero computer (con il diritto ACL), accesso in lettura e scrittura gli eventi ETW vengono bloccati. Gli sviluppatori potrebbero verificarsi che l'API chiama per leggere e scrivere ETW eventi e comuni i registri eventi di Windows sembra funzionare, ma il motivo è che il servizio di App è "falsificare" le chiamate in modo che vengano visualizzate per completare. In realtà, il codice dell'applicazione non ha accesso a questi dati dell'evento.

<a id="RegistryAccess"></a>
## <a name="registry-access"></a>Accesso del Registro di sistema
App dispongano di accesso in sola lettura per la maggior parte (anche se non tutti) del Registro di sistema del computer virtuale in esecuzione nel. In pratica, ciò significa chiavi del Registro di sistema di accesso in sola lettura al gruppo di utenti locale sono accessibili da applicazioni. Un'area del Registro di sistema non è attualmente supportata per la lettura o scrittura è HKEY\_corrente\_hive dell'utente.

Scrittura nel Registro di sistema è bloccato, incluso l'accesso a tutte le chiavi del Registro di sistema per utente. Dal punto di vista dell'app, scrittura per il Registro di sistema non pertanto mai basarsi nell'ambiente di Azure dall'App (possano) vengono migrati tra diverse macchine virtuali. L'archiviazione solo persistente scrivibile dipendente da un'app è la struttura di directory contenuto-app archiviata in condivisioni UNC servizio App. 

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 
 
