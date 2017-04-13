<properties
    pageTitle="Registrare la protezione dei dati Analitica | Microsoft Azure"
    description="Informazioni su come Analitica Log sulla protezione della privacy e consente di proteggere i dati."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="banders"/>

# <a name="log-analytics-data-security"></a>Registrare la protezione dei dati Analitica

Microsoft si impegna a proteggere la privacy e proteggere i dati, durante l'esecuzione del software e servizi che consentono di gestire l'infrastruttura IT dell'organizzazione. Abbiamo riconoscere quando si affidare i dati ad altri utenti, tale protezione richiede sicurezza rigido. Microsoft aderisce alle linee guida di sicurezza e conformità restrittive, ovvero dalla codifica alla operativo un servizio.

Impostazione della sicurezza e protezione dei dati è una priorità Microsoft. Contatta l'assistenza clienti con domande, suggerimenti o problemi relativi a una delle seguenti informazioni, inclusi i criteri di sicurezza in [Opzioni di supporto di Azure](http://azure.microsoft.com/support/options/).

In questo articolo viene spiegato come di raccolte, elaborati e protetto da Analitica Log in operazioni di gestione famiglia di prodotti (OMS) dati. È possibile utilizzare gli agenti per connettersi al servizio web, utilizzare System Center Operations Manager per raccogliere dati operativi o recuperare dati da Azure diagnostica per l'utilizzo da Log Analitica. I dati raccolti vengono inviati tramite Internet utilizzando l'autenticazione basata su certificato e 3 SSL al servizio di Log Analitica è ospitato in Microsoft Azure. Dati viene compresso dall'agente prima di inviarlo.

Il servizio di Log Analitica gestisce i dati basate su cloud in modo sicuro usando i metodi seguenti:

- separazione di dati
- conservazione dei dati
- protezione fisica
- incidente
- conformità
- certificazioni standard di protezione


## <a name="data-segregation"></a>Separazione di dati

Cliente vengono mantenuti in modo logico distinta per ogni componente in tutto il servizio Outlook Mobile. Tutti i dati è contrassegnato per l'organizzazione. In questo tag persiste nel ciclo di vita dei dati e viene applicata ogni livello del servizio. Ogni cliente ha un blob Azure dedicato contenente i dati a lungo termine

## <a name="data-retention"></a>Conservazione dei dati

Dati di ricerca indicizzati log sono memorizzati e mantenuti in base al piano prezzo. Per ulteriori informazioni, vedere [Log Analitica prezzi](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft consente di eliminare dati dei clienti 30 giorni dopo l'area di lavoro OMS è chiuso. Microsoft si elimina anche l'Account di archiviazione di Azure in cui risiedono i dati. Rimuovendo i dati dei clienti, non vengono eliminati unità fisiche.

Nella tabella seguente vengono elencate alcune delle soluzioni disponibili in OMS ed esempi i tipi di dati raccolti.

| **Soluzione** | **Tipi di dati** |
| --- | --- |
| Configurazione dei rischi | Dati di configurazione, metadati e dati relativi allo stato |
| Pianificazione delle capacità | Metadati e dati relativi alle prestazioni |
| Antimalware | Metadati e dati di configurazione |
| Valutazione di aggiornamento di sistema | Dati dello stato e dei metadati |
| Gestione dei registri | Definite dall'utente i registri eventi, i registri eventi di Windows e/o i registri di IIS |
| Rilevamento delle modifiche | Inventario software e dei metadati del servizio di Windows |
| Valutazione di Active Directory e SQL | Dati WMI, dati del Registro di sistema, dati sulle prestazioni e gestione dinamica di SQL Server consente di visualizzare i risultati |

Nella tabella seguente è illustrati esempi di tipi di dati:

| **Tipo di dati** | **Campi** |
| --- | --- |
| Invia avviso | Invia avviso nome, descrizione dell'avviso, BaseManagedEntityId, ID problema, IsMonitorAlert, ID regola, ResolutionState, priorità, gravità, categoria, proprietario, risolto da, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configurazione | IDCliente AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, Valorecorrente, ChangeDate |
| Evento | EventId, EventOriginalID, BaseManagedEntityInternalId, ID regola, PublisherId, PublisherName, FullNumber, numero, categoria, ChannelLevel, LoggingComputer, EventData, oggetti EventParameter inclusi, TimeGenerated, TimeAdded <br>**Nota:** Quando si accede eventi con i campi personalizzati a registro eventi di Windows, OMS raccoglie loro. |
| Metadati | BaseManagedEntityId, ObjectStatus, unità organizzativa, ActiveDirectoryObjectSid, PhysicalProcessors, risorse NetworkName, indirizzo IP, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, indirizzo IP, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Prestazioni | Nome oggetto, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Stato | StateChangeEventId, StateId, NewHealthState, OldHealthState, contesto, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Protezione fisica

Analitica Log nel servizio Outlook Mobile è presidiato da personale di Microsoft e tutte le attività vengono registrate e possono essere controllate. Il servizio viene eseguito completamente in Azure e soddisfa i criteri di progettazione comuni Azure. È possibile visualizzare i dettagli di protezione fisica dei beni Azure nella pagina 18 della [Panoramica sulla protezione di Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Diritti di accesso fisica per proteggere aree vengono modificati entro un giorno per tutti gli utenti che non dispone più responsabilità per il servizio Outlook, tra cui trasferimento e terminazione. Per ulteriori informazioni l'infrastruttura fisica globale che serve al [Data Center di Microsoft](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Incidente

OMS è un processo di gestione dell'incidente che soddisfano i requisiti di tutti i servizi Microsoft. Per riepilogare, è:

- Usare un modello di responsabilità condiviso in una parte di responsabilità di protezione appartiene Microsoft durante la parte appartiene al cliente
- Gestire i problemi di protezione Azure
  - Rileva un incidente prima indicazione, per avviare un'analisi
  - Valutare l'impatto e la gravità di un incidente da un membro del team di risposta autorizzato alla risposta. In base a prove, la valutazione potrebbe o potrebbe non venire ulteriormente escalation al team di risposta di sicurezza.
  - Diagnosticare un evento imprevisto da esperti di risposta di sicurezza per condurre indagini tecniche o legali, identificare strategie contenimento, attenuazione e soluzione. Se il team di protezione ritiene che dati del cliente possono diventare esposti a un utente non autorizzato o illecito, l'esecuzione in parallelo del processo di notifica di evento imprevisto dei clienti inizia in parallelo.  
  - Stabilizzare e recupero del problema. Il team di intervento crea un piano di ripristino per limitare il problema. Procedura di contenimento crisi, ad esempio la messa in quarantena sistemi interessati può verificarsi immediatamente e in parallelo con la diagnosi. Potrebbero previste più riduzioni termini che si verificano rischio immediato è già trascorsa.  
  - Chiudere l'evento imprevisto relativi e condurre un post-mortem. Il team di intervento crea un post-mortem che circonda i dettagli dell'evento, allo scopo di modificare i criteri, procedure e processi per impedire un analoghi dell'evento.
- Informare i clienti dei problemi di protezione
  - Determinare l'ambito dei clienti interessati e fornire chiunque impatto più dettagliato un avviso possibili
  - Creare un avviso per fornire i clienti con sufficienti informazioni dettagliate in modo da poter eseguire un'analisi i loro e soddisfare gli impegni apportate mentre non ritardare il processo di notifica agli utenti finali.
  - Confermare e dichiarare incidente, in base alle esigenze.
  - Informare i clienti con una notifica ai problemi immediatamente e secondo qualsiasi impegno legale e contrattuali. Notifica dei problemi di protezione verrà recapitata a uno o più degli amministratori del cliente con qualsiasi mezzo che seleziona Microsoft, tra cui tramite posta elettronica.
- Preparazione del team di presentare e formazione
  - Sono necessario per completare la sicurezza e formazione, che consente di identificare e segnalare problemi della sicurezza personale di Microsoft.  
  - Gli operatori di usare il servizio di Microsoft Azure hanno obblighi di formazione aggiunta racchiuso tra loro l'accesso a sistemi riservati hosting dati dei clienti.
  - Microsoft risposta personale ricevano formazione specializzata per i ruoli


In caso di perdita di dati di un cliente, è una notifica ogni cliente all'interno di un giorno. Tuttavia, la perdita di dati dei clienti non è mai verificato con OMS. Inoltre, è mantenere copie dei dati che è stati creati e aree geografiche, questo verrà distribuito.

Per ulteriori informazioni sulle modalità di risposta ai problemi di protezione di Microsoft, vedere [Risposta di protezione di Microsoft Azure nel Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformità

Programma di sicurezza e la governance delle informazioni OMS software development e servizio del team supporta aziendali e aderisce alle e leggi come descritto nel [Centro protezione di Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e [Conformità di Centro protezione di Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Come OMS stabilisce requisiti di sicurezza, identifica i controlli di sicurezza, gestione ed esegue il monitoraggio dei rischi sono descritte anche non esiste. Una volta all'anno, si verifica un di criteri, standard, procedure e linee guida.

Ogni membro del team di sviluppo OMS riceve la formazione sulla protezione applicazione formale. Internamente, serve un sistema di controllo delle versioni per lo sviluppo di software. Ogni progetto software è protetto tramite il sistema di controllo delle versioni.

Microsoft dispone di un team di sicurezza e conformità che controlla e valuta tutti i servizi Microsoft. I funzionari responsabili della sicurezza informazioni costituiscono il team e non sono associati con i reparti ingegneristica sviluppano OMS. I funzionari responsabili della sicurezza ha le proprie catena di gestione e condurre valutazioni indipendenti di prodotti e servizi per garantire sicurezza e conformità.

Consiglio di Microsoft di amministrazione riceve una notifica tramite e relazione annuale sulle tutta la sicurezza delle informazioni dei programmi Microsoft.

Il team di sviluppo e servizio software OMS attivamente sta collaborando con il team Microsoft legali e conformità e altri partner per acquisire una varietà di certificazioni.

## <a name="security-standards-certifications"></a>Certificazioni standard di protezione

Registro Analitica in OMS attualmente soddisfa i criteri di sicurezza seguenti:

- [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm) e [ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498) compatibile
- Pagamento scheda (PCI conforme) dati protezione Standard di settore (PCI DSS) dal Consiglio PCI sicurezza standard.
- [Tipo di servizio organizzazione controlli (SOC) 1 1 e SOC 2 digitare 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) conforme
- Criteri di progettazione comuni di Windows
- Certificazione Microsoft attendibile Computing
- Come servizio Azure componenti utilizzati OMS rispettare i requisiti di conformità Azure. È possibile leggere informazioni su [Microsoft attendibile centro conformità](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).


## <a name="cloud-computing-security-data-flow"></a>Il cloud computing flusso di dati di sicurezza
Il diagramma seguente illustra un'architettura di sicurezza cloud come flusso delle informazioni della società e come è protetto come si sposta il servizio di Log Analitica, infine visualizzato dall'utente nel portale di OMS. Ulteriori informazioni su ogni passaggio successivo nel diagramma.

![Immagine della raccolta di dati OMS e sicurezza](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. iscriversi Analitica Log e raccogliere dati

Per l'organizzazione inviare dati a Log Analitica, configurare gli agenti di Windows, agenti in esecuzione in macchine virtuali di Azure o di agenti OMS per Linux. Se si usano agenti Operations Manager, è necessario utilizzare una configurazione guidata nella console per configurarli. Gli utenti, che potrebbero essere si, altri utenti singoli o un gruppo di persone, creano uno o più account OMS (aree di lavoro OMS) e registrare gli agenti utilizzando uno degli account seguenti:


- [ID dell'organizzazione](../active-directory/sign-up-organization.md)

- [Account Microsoft, Outlook, Office Live, MSN](http://www.microsoft.com/account/default.aspx)

Un'area di lavoro OMS è nel punto in cui di raccolte, aggregati, analizzare e presentati dati. Un'area di lavoro viene utilizzato principalmente allo scopo di dati delle partizioni e ogni area di lavoro sia univoco. Ad esempio, è consigliabile che i dati di produzione gestiti con un'area di lavoro OMS e i dati di test gestiti con un'altra area di lavoro. Le aree di lavoro consente anche un amministratore controllare utente l'accesso ai dati. Ogni area di lavoro può avere più account utente associato e tutti gli account utente può accedere più area di lavoro OMS. Creare le aree di lavoro in base a Data Center area. Ogni area di lavoro è replicata in altri Data Center nell'area principalmente OMS disponibilità del servizio.

Per Operations Manager, dopo avere completato la configurazione guidata, ogni gruppo di gestione Operations Manager stabilisce una connessione con il servizio di Log Analitica. Utilizzare quindi l'installazione guidata computer per scegliere quali computer nel gruppo Gestione consentiti per inviare dati al servizio. Per altri tipi di agente, ciascuna collega in modo sicuro per il servizio Outlook Mobile.

Tutte le comunicazioni tra sistemi connessi e il servizio Analitica Log sono crittografata.  Il protocollo TLS (HTTPS) viene utilizzato per la crittografia.  Al processo SDL Microsoft è seguito per garantire che Analitica Log sia aggiornato con i protocolli di crittografia più recente.

Ogni tipo di agente raccoglie i dati per Log Analitica. Tipi di soluzioni utilizzate dipende il tipo di dati raccolti. È possibile visualizzare un riepilogo della raccolta di dati su [solutions aggiungere Analitica Log dalla raccolta soluzioni](log-analytics-add-solutions.md). Inoltre, per informazioni più dettagliate insieme sono disponibile per la maggior parte delle soluzioni. Una soluzione è un gruppo di visualizzazioni predefinite, le query di ricerca di log, le regole di raccolta dati e logica di elaborazione. Solo gli amministratori possono utilizzare Analitica Log di importare una soluzione. Dopo l'importazione della soluzione, questo viene spostato al server di gestione Operations Manager (se utilizzato), quindi di tutti gli agenti che si sono scelto. In seguito, gli agenti di raccolgono i dati.

## <a name="2-send-data-from-agents"></a>2. inviare i dati da parte di agenti

Registrare tutti i tipi di agente con un tasto di registrazione e una connessione sicura tra l'agente e il servizio di Log Analitica utilizzando l'autenticazione basata su certificati e SSL con la porta 443. OMS utilizza un archivio segreto per generare e mantenere tasti. Chiavi private vengono ruotate ogni 90 giorni e archiviate in Azure e vengono gestite tramite le operazioni di Azure che seguono strict consigliate alle normative e conformità.

Con Operations Manager, si registra un'area di lavoro con il servizio di Log Analitica e sicura HTTPS connessione tra il server di gestione Operations Manager.

Per gli agenti di Windows in esecuzione in macchine virtuali di Azure la chiave di archiviazione di sola lettura viene utilizzata per leggere eventi di diagnostica nelle tabelle Azure.

Se un agente non riesce a comunicare al servizio per qualsiasi motivo, i dati raccolti vengono archiviati in locale in una cache temporanea e il server di gestione tenta di inviare di nuovo i dati ogni 8 minuti per 2 ore. Dati memorizzati nella cache dell'agente sono protetto da archivio credenziali del sistema operativo. Se il servizio non è possibile elaborare i dati dopo 2 ore, gli agenti accoderà i dati. Se coda è pieno, viene avviato OMS eliminazione di tipi di dati, a partire da dati sulle prestazioni. Il limite di coda agente è una chiave del Registro di sistema in modo che è possibile modificarla, se necessario. Dati raccolti compressi e inviati al servizio, ignorando i database locale, in modo che non aggiungervi qualsiasi carico. Dopo aver inviati i dati raccolti, viene rimosso dalla cache.

Come descritto in precedenza, dati dagli agenti vengono inviati tramite SSL al Data Center di Microsoft Azure. Facoltativamente, è possibile utilizzare ExpressRoute per garantire una maggiore protezione per i dati. ExpressRoute è un modo per connettersi direttamente al Azure dalla rete WAN esistente, ad esempio un protocollo più etichetta commutazione VPN (MPLS), fornite da un provider di servizi di rete. Per ulteriori informazioni, vedere [ExpressRoute](https://azure.microsoft.com/services/expressroute/).


## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. il servizio di Log Analitica riceve e consente di elaborare dati

Il servizio di Log Analitica garantisce che dati provenienti da una fonte attendibile per convalidare i certificati e l'integrità dei dati con l'autenticazione di Azure. I dati non elaborati vengono quindi archiviati con un blob di [Spazio di archiviazione di Microsoft Azure](../storage/storage-introduction.md) e non è crittografati. Tuttavia, ogni blob Azure dello spazio di archiviazione con un set di set di chiavi univoche accessibile solo all'utente. Il tipo di dati memorizzati dipende da tipi di soluzioni che sono state importate e utilizzate per raccogliere dati. Quindi, il servizio di Log Analitica elabora i dati non elaborati per il blog di archiviazione Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. utilizzare Analitica Log per accedere ai dati

È possibile accedere a Log Analitica nel portale di OMS utilizzando l'account aziendale o l'account Microsoft che ha configurato in precedenza. Tutto il traffico tra il portale OMS e Analitica Log in OMS viene inviato tramite un canale HTTPS protetto. Quando si usa il portale OMS, viene generato un ID sessione nel client utente (browser web) e vengono memorizzati nella cache locale fino a quando non termina la sessione. Al termine, la cache viene eliminata. I cookie sul lato client, che non contengono informazioni personali, non vengono rimosse automaticamente. I cookie sessione contrassegnati HTTPOnly e vengono protetti. Dopo un periodo di inattività predeterminato, la sessione portale OMS è terminata.

Tramite il portale OMS, è possibile esportare dati in un file CSV e si può accedere ai dati utilizzando API di ricerca. Esportazione CSV è limitato a 50.000 righe per esportare e dati API sono limitati a 5.000 righe per ricerca.

## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva a Analitica Log](log-analytics-get-started.md) per informazioni su Analitica Log e get funzionanti in minuti.
