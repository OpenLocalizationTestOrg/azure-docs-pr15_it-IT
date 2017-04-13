<properties
   pageTitle="Configurazione dell'applicazione dipendenza Monitor (ADM) in operazioni Management Suite (OMS) | Microsoft Azure"
   description="Applicazione dipendenza Monitor (ADM) è una soluzione di operazioni gestione famiglia di prodotti (OMS) che individua componenti di applicazioni nei sistemi Windows e Linux e mappe le comunicazioni tra servizi automaticamente.  Questo articolo fornisce informazioni dettagliate per la distribuzione ADM nel proprio ambiente e usarlo in diversi scenari."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configurazione soluzione applicazione dipendenza Monitor in operazioni di gestione famiglia di prodotti (OMS)
![Icona di avviso di gestione](media/operations-management-suite-application-dependency-monitor/icon.png) Applicazione dipendenza Monitor (ADM) automaticamente individua componenti di applicazioni nei sistemi Windows e Linux e mappe le comunicazioni tra i servizi. Consente di visualizzare i server come devono essere considerati – come sistemi interconnessi che offrono servizi critici.  Applicazione dipendenza Monitor mostra le connessioni tra server, processi, e porte in qualsiasi architettura connessi TCP senza configurazioni richieste ad eccezione di installazione di un agente.

In questo articolo vengono illustrati i dettagli della configurazione di agenti Monitor dipendenza dell'applicazione e l'adozione.  Per informazioni sull'utilizzo ADM, vedere [utilizzo di Monitor dipendenza applicazione soluzione in operazioni di gestione famiglia di prodotti (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Applicazione dipendenza Monitor è attualmente in anteprima privato.  È possibile richiedere l'accesso all'anteprima privato ADM in [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Durante l'anteprima privato, tutti gli account OMS avere accesso illimitato per adm.  I nodi ADM sono gratuiti, ma verranno contatore dati Analitica Log per i tipi di AdmComputer_CL e AdmProcess_CL come qualsiasi altra soluzione.
>
>Dopo aver ADM immette anteprima pubblica, sarà disponibile solo per i clienti gratuiti e a pagamento di informazione e Analitica nel OMS prezzi piano.  Account di livello gratuito saranno limitati a 5 nodi ADM.  Se si partecipa nel riquadro di anteprima privato e non registrato nel piano di prezzi di OMS quando ADM entra anteprima pubblica, ADM saranno disabilitate in quel momento. 



## <a name="connected-sources"></a>Origini connesse
Nella tabella seguente vengono descritte le origini connesse supportate da soluzione ADM.

| Origine connesso | Supportati | Descrizione |
|:--|:--|:--|
| [Agenti di Windows](../log-analytics/log-analytics-windows-agents.md) | Sì | ADM analizza e raccoglie dati dal computer agente di Windows.  <br><br>Si noti che oltre agente OMS agenti Windows richiedono Microsoft dipendenza Agent.  Vedere i [sistemi operativi supportati](#supported-operating-systems) per un elenco completo delle versioni del sistema operativo. |
| [Agenti Linux](../log-analytics/log-analytics-linux-agents.md) | Sì | ADM analizza e raccoglie dati dal computer agente Linux.  <br><br>Si noti che oltre agente OMS agenti Linux richiedono Microsoft dipendenza Agent.  Vedere i [sistemi operativi supportati](#supported-operating-systems) per un elenco completo delle versioni del sistema operativo. |
| [Gruppo di gestione SCOM](../log-analytics/log-analytics-om-agents.md) | Sì | ADM analizza e consente di raccogliere dati da Windows e Linux agenti in un gruppo di gestione SCOM connesso. <br><br>È necessaria una connessione diretta dal computer agente SCOM OMS. Dati vengono inviati direttamente da inoltrate dal gruppo di gestione archivio di OMS.|
| [Account di archiviazione Azure](../log-analytics/log-analytics-azure-storage.md) | No | ADM raccoglie dati dal computer agente, in modo che nessun dato da essa per raccogliere dallo spazio di archiviazione Azure. |

Si noti che i ADM supporta solo piattaforme a 64 bit.

In Windows, Microsoft monitoraggio agente (MMA) usato SCOM e OMS per raccogliere e inviare il monitoraggio dei dati.  (Questo agente è denominato SCOM agente, agente OMS, MMA o agente diretta, a seconda di contesto.)  SCOM e OMS offrono diversi fuori le versioni di casella di MMA, ma queste versioni possono ogni rapporto a SCOM per OMS o a entrambi.  In Linux, l'agente OMS per Linux raccoglie e invia dati a OMS di monitoraggio.  È possibile utilizzare i ADM su server con agenti diretto OMS o su server collegati a OMS tramite SCOM gestione dei gruppi.  Per questa documentazione si farà riferimento a tutti di questi agenti – su Linux o Windows, se connessi a una MG SCOM o direttamente a OMS-come "Agente OMS", a meno che il nome di distribuzione specifica dell'agente è necessaria per contesto.

Agente di ADM non trasmette dati stesso e non richiede le modifiche alle porte o firewall.  Dati di ADM vengono sempre trasmessi dall'agente OMS a OMS, direttamente o tramite il Gateway OMS.

![ADM agenti](media/operations-management-suite-application-dependency-monitor/agents.png)

Se si è un cliente SCOM con un gruppo di gestione connessi a OMS:

- Se gli agenti SCOM possono accedere a internet a cui connettersi OMS, non è necessaria alcuna configurazione aggiuntiva.  
- Se gli agenti SCOM non è possibile accedere a OMS tramite internet, sarà necessario configurare il Gateway OMS per l'uso con SCOM.
  
Se si utilizza l'agente diretto OMS, è necessario configurare l'agente OMS per connettersi a OMS o al gateway OMS.  Il Gateway OMS può essere scaricato dal [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Come evitare dati duplicati

Se si è un cliente SCOM, non è necessario configurare gli agenti SCOM per comunicare direttamente a OMS o dati verranno segnalati due volte.  In ADM, verrà creato nel computer visualizzati due volte nell'elenco a discesa computer.

Configurazione di OMS da eseguire solo in uno dei seguenti percorsi:

- Il riquadro SCOM Console operazioni gestione applicazioni per computer gestiti
- Configurazione di approfondimenti operative Azure nelle proprietà MMA

Utilizzo di entrambe le configurazioni con l'area di lavoro stesso in ogni impedirà la duplicazione dei dati. Utilizzo combinato con diverse aree di lavoro causando conflitto configurazione (quello con soluzione ADM abilitato e l'altro senza) che potrebbe impedire che scorre in ADM completamente di dati.  

Si noti che anche se il computer stesso non viene specificato nella configurazione di OMS della Console SCOM, se un gruppo di istanza, ad esempio "Gruppo di istanze di Windows Server" è attivo, potrebbero comunque risultare la ricezione OMS configurazione del computer tramite SCOM.



## <a name="management-packs"></a>Management Pack
Quando è attivata ADM in un'area di lavoro OMS, 300KB Management Pack viene inviato a tutto il monitoraggio agenti Microsoft nell'area di lavoro.  Se si usa agenti SCOM in un [gruppo di gestione connesso](../log-analytics/log-analytics-om-agents.md), ADM Management Pack verrà distribuito da SCOM.  Se gli agenti sono connessi direttamente, il pannello di gestione verrà recapitato da OMS.

Pannello di gestione denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Si tratta *%Programfiles%\Microsoft monitoraggio Agent\Agent\Health State\Management SP\*.  L'origine dati utilizzata da management pack *% Program files%\Microsoft monitoraggio Agent\Agent\Health servizio State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configurazione
Oltre a Windows e Linux computer dispongano di un agente installato e collegato al OMS, il programma di installazione agente della dipendenza deve essere scaricato dalla soluzione ADM e quindi installato come radice o amministratore su ciascun server gestito.  Dopo aver installato l'agente ADM in un server reporting OMS, mappe dipendenza ADM appariranno in 10 minuti.  Se si verificano problemi, vedere inviare tramite posta elettronica [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migrazione da BlueStripe FactFinder
Applicazione dipendenza Monitor fornirà BlueStripe tecnologia in OMS in fasi. FactFinder ancora supportate per i clienti esistenti ma non è più disponibile per l'acquisto singoli.  Questa versione di anteprima dell'agente di dipendenza può comunicare solo con OMS.  Se si è un cliente FactFinder corrente, identificare un insieme di un server di prova per ADM che non sono gestiti FactFinder. 

### <a name="download-the-dependency-agent"></a>Scaricare l'agente dipendenza
Oltre a Microsoft Management agente (MMA) e OMS Linux agente che forniscono la connessione tra il computer e OMS, tutti i computer analizzati dall'applicazione dipendenza Monitor devono avere installato l'agente di relazione.  Su Linux, è necessario installare l'agente OMS per Linux prima dell'agente di relazione. 

![Riquadro dell'applicazione di dipendenza Monitor](media/operations-management-suite-application-dependency-monitor/tile.png)

Per scaricare l'agente di dipendenza, fare clic su **Configura soluzione** nel riquadro **Applicazione dipendenza Monitor** per aprire e **l'Agente della dipendenza** .  E il dipendenza agente include collegamenti per le finestre e gli agenti Linux. Fare clic sul collegamento appropriato per scaricare ogni agente. Vedere le sezioni seguenti per informazioni dettagliate sull'installazione agente di sistemi diversi.

### <a name="install-the-dependency-agent"></a>Installare l'agente dipendenza

#### <a name="microsoft-windows"></a>Microsoft Windows
Per installare o disinstallare l'agente sono necessari privilegi di amministratore.

Agente di dipendenza è installato nel computer Windows con ADM-agente-Windows.exe. Se si esegue questo eseguibile senza opzioni, quindi inizierà una procedura guidata che è possibile eseguire per eseguire l'installazione in modo interattivo.  

Utilizzare la procedura seguente per installare l'agente di dipendenza in ogni computer Windows.

1.  Assicurarsi che l'agente OMS sia installato usando le istruzioni incluse nei computer di connettersi direttamente a OMS.
2.  Scaricare l'agente di Windows ed eseguire con il comando seguente.<br>*ADM-agente-Windows.exe*
3.  Seguire la procedura guidata per installare l'agente.
4.  Se l'agente di dipendenza non è possibile avviare, controllare i registri per informazioni dettagliate sull'errore. Per gli agenti di Windows, la directory log è *C:\Programmi\Microsoft Files\BlueStripe\Collector\logs*. 

È possibile disinstallare l'agente di dipendenza per Windows da un amministratore tramite il pannello di controllo.


#### <a name="linux"></a>Linux
Accesso alla directory radice è necessario installare o configurare l'agente.

Agente di dipendenza è installato nel computer Linux con uno script di shell con un file binario estrazione ADM-agente-Linux64.bin. È possibile eseguire il file con m o aggiungere le autorizzazioni per il file di esecuzione.
 
Utilizzare la procedura seguente per installare l'agente di dipendenza su ciascun computer Linux.

1.  Assicurarsi che l'agente OMS sia installato seguendo le istruzioni al [raccogliere e gestire i dati da computer Linux.  Questa operazione deve essere installato prima dell'agente di dipendenza Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installare l'agente Linux dipendenza come radice utilizzando il comando seguente.<br>*Mostra Linux64.bin di agente ADM*.
3.  Se l'agente di dipendenza non è possibile avviare, controllare i registri per informazioni dettagliate sull'errore. Su agenti Linux, la directory log è */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Disinstallazione agente dipendenza su Linux
Per disinstallare completamente l'agente dipendenza da Linux, è necessario rimuovere l'agente stesso e il proxy che viene installato automaticamente con l'agente.  È possibile disinstallare entrambe con il seguente comando singolo.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>L'installazione dalla riga di comando
La sezione precedente istruzioni sull'installazione l'agente di Monitor dipendenza utilizzando le opzioni predefinite.  Nelle sezioni seguenti forniscono indicazioni relative all'installazione dell'agente dalla riga di comando usando le opzioni personalizzate.

#### <a name="windows"></a>Windows
Consente di eseguire l'installazione dalla riga di comando Opzioni nella tabella seguente. Per visualizzare un elenco dell'installazione di contrassegni di eseguono il programma di installazione con la /? contrassegnare come indicato di seguito.

    ADM-Agent-Windows.exe /?

| Contrassegno | Descrizione |
|:--|:--|
| /S | Eseguire un'installazione automatica senza richiedere intervento utente. |

File per l'agente di Windows dipendenza vengono inseriti in *C:\Programmi\Microsoft Files\BlueStripe\Collector* per impostazione predefinita.


#### <a name="linux"></a>Linux
Utilizzare le opzioni nella tabella seguente per eseguire l'installazione. Per visualizzare un elenco di installazione contrassegni di eseguire l'installazione delle applicazioni - assistenza contrassegna come indicato di seguito.

    ADM-Agent-Linux64.bin -help

| Descrizione indicatore
|:--|:--|
| -s | Eseguire un'installazione automatica senza richiedere intervento utente. |
| -controllare | Controlla autorizzazioni e il sistema operativo, ma non installare l'agente. |

File per l'agente di dipendenza vengono inseriti nella directory riportate di seguito.

| File | Posizione |
|:--|:--|
| File di base | /usr/lib/bluestripe-Collector |
| File di log | /var/OPT/Microsoft/Dependency-Agent/log |
| File di configurazione | /etc/OPT/Microsoft/Dependency-Agent/config |
| File eseguibili dei servizi | /sbin/bluestripe-Collector<br>/sbin/bluestripe-Collector-Manager |
| File di archivio binario | /var/OPT/Microsoft/Dependency-Agent/Storage |



## <a name="troubleshooting"></a>Risoluzione dei problemi
Se si verificano problemi con l'applicazione dipendenza Monitor, è possibile raccogliere le informazioni sulla risoluzione dei problemi da più componenti utilizzando le seguenti informazioni.

### <a name="windows-agents"></a>Agenti di Windows

#### <a name="microsoft-dependency-agent"></a>Dipendenza Microsoft Agent
Per generare dati sulla risoluzione dei problemi da agente di dipendenza, aprire un prompt dei comandi come amministratore ed eseguire lo script CollectBluestripeData.vbs utilizzando il comando seguente.  È possibile aggiungere flag--Guida per visualizzare altre opzioni.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

Il pacchetto di dati di supporto viene salvato nella directory % profiloutente % per l'utente corrente.  È possibile utilizzare--file <filename> opzione per salvare in un percorso diverso.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Dipendenza Microsoft Agent Management Pack per MMA
Dipendenza agente Management Pack viene eseguita all'interno dell'agente di gestione di Microsoft.  Riceve dati da agente di dipendenza e inoltra al servizio cloud ADM.
  
Verificare che il management pack viene scaricato eseguendo la procedura seguente.

1.  Cercare un file denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp in c:\Programmi\Microsoft c:\Programmi\Microsoft monitoraggio Agent\Agent\Health State\Management SP.  
2.  Se il file non è presenta e l'agente sia connesso a un gruppo di gestione SCOM, verificare che è stata importata in SCOM selezionando Management Pack nell'area di lavoro di amministrazione della Console.

Pannello di gestione ADM scrive gli eventi di Operations Manager registro eventi di Windows.  Il log può essere [eseguita la ricerca in OMS](../log-analytics/log-analytics-log-searches.md) tramite la soluzione del Registro di sistema, dove è possibile configurare i file di log da caricare.  Se sono attivati gli eventi di debug, vengono scritte nel registro eventi applicazione, con l'origine di eventi *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agente di monitoraggio di Microsoft
Per raccogliere tracce diagnostiche, aprire un prompt dei comandi come amministratore ed eseguire i comandi seguenti: 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Tracce vengono scritte in c:\Windows\Logs\OpsMgrTrace.  È possibile interrompere la traccia con StopTracing.cmd.


### <a name="linux-agents"></a>Agenti Linux

#### <a name="microsoft-dependency-agent"></a>Dipendenza Microsoft Agent
Per generare dati sulla risoluzione dei problemi da agente di dipendenza, eseguire l'accesso con un account che dispone di privilegi sudo o radice ed eseguire il comando seguente.  È possibile aggiungere flag--Guida per visualizzare altre opzioni.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

Il pacchetto di dati di supporto viene salvato in /var/opt/microsoft/dependency-agent/log (se radice) nella directory di installazione dell'agente o alla home directory dell'utente che esegue lo script (se non di primo livello).  È possibile utilizzare--file <filename> opzione per salvare in un percorso diverso.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dipendenza Microsoft Agent Fluentd plug-in per Linux
Il plug-in dipendenza agente Fluentd viene eseguita all'interno dell'agente di Linux OMS.  Riceve dati da agente di dipendenza e inoltra al servizio cloud ADM.  

Registri in due file seguenti.

- /var/OPT/Microsoft/omsagent/log/omsagent.log
- /var/log/Messages

#### <a name="oms-agent-for-linux"></a>Agente OMS per Linux
Una risorsa sulla risoluzione dei problemi per la connessione server Linux a OMS sono disponibili qui: [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

I registri per l'agente OMS Linux risiede in */var/acconsentire/microsoft/omsagent/log/*.  

I registri di omsconfig (la configurazione dell'agente) si trovano in */var/acconsentire/microsoft/omsconfig/log/*.
 
Log per i componenti OMI e SCX che forniscono dati sulle prestazioni metriche risiede in */var/acconsentire/omi/log/* e */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Raccolta di dati
È probabile che ogni agente per la trasmissione di circa 25MB al giorno, a seconda complessità le dipendenze del sistema.  Dati sulle dipendenze ADM viene inviati da ogni agente ogni 15 secondi.  

Agente di ADM in genere implica l'uso di 0,1% della memoria di sistema e 0,1% di CPU sistema.


## <a name="supported-operating-systems"></a>Sistemi operativi supportati
Nelle sezioni seguenti sono elencati i sistemi operativi supportati per l'agente di relazione.   architetture a 32 bit non sono supportate per sistemi operativi.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows Desktop
- Nota: Windows 10 non è ancora supportato
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux, CentOS Linux e Linux Oracle (con RHEL Kernel)
- Sono supportate solo predefinito e versioni kernel Linux SMP.
- Rilascia kernel non standard, ad esempio PAE e Xen, non sono supportati per distribuzioni Linux. Ad esempio un sistema con la stringa di rilascio di "2.6.16.21-0.8-xen" non è supportato.
- Vengono personalizzati, tra cui ricompilazioni. x standard, non è supportati
- Kernel centos Plus non è supportata.
- Oracle estremamente affidabile e Kernel (UEK) viene descritto in un'altra sezione riportata di seguito.


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6,7 | 2.6.32-573 |
| 6,8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| Versione del sistema operativo | Versione del kernel |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux con Kernel estremamente affidabile e (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 6.2 | 2.6.32-300 Oracle (UEK R1) |
| 6.3 | 2.6.39-200 Oracle (UEK R2) |
| 6.4 | 2.6.39-400 Oracle (UEK R2) |
| 6.5 | 2.6.39-400 Oracle (UEK i386 R2) |
| 6.6 | 2.6.39-400 Oracle (UEK i386 R2) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 5.8 | 2.6.32-300 Oracle (UEK R1) |
| 5,9 | 2.6.39-300 Oracle (UEK R2) |
| 5.10 | 2.6.39-400 Oracle (UEK R2) |
| 5.11 | 2.6.39-400 Oracle (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| 11 | 2.6.27 |
| SP1 11 | 2.6.32 |
| SP2 11 | 3.0.13 |
| SP3 11 | 3.0.76 |
| SP4 11 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Versione del sistema operativo | Versione del kernel
|:--|:--|
| SP4 10 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e l'uso
Microsoft raccoglie automaticamente i dati di utilizzo e le prestazioni tramite l'utilizzo del servizio Monitor dipendenza dell'applicazione. Microsoft utilizza questi dati per fornire e migliorare la qualità, sicurezza e l'integrità del servizio Monitor dipendenza dell'applicazione. Dati includono le informazioni di configurazione del software del sistema operativo e versione e indirizzo IP, nome DNS e nome Workstation per fornire funzionalità di risoluzione dei problemi accurata ed efficiente. Raccolte non nomi, indirizzi o altre informazioni di contatto.

Per ulteriori informazioni sulla raccolta di dati e l'utilizzo, vedere l' [Informativa sulla Privacy di servizi Online Microsoft](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [utilizzare applicazione dipendenza Monitor](operations-management-suite-application-dependency-monitor.md) una volta è stato distribuito e configurato.
