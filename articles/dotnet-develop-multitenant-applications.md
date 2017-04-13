<properties
    pageTitle="Modello di applicazione Web multi-Tenant | Microsoft Azure"
    description="Trovare panoramiche architettonica e modelli di progettazione che descrivono come implementare un'applicazione web multi-tenant in Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Applicazioni multi-tenant in Azure

Un'applicazione multi-tenant è una risorsa condivisa che consente a utenti distinti o "tenant," visualizzare l'applicazione come se fosse le proprie. Uno scenario tipico che consente a un'applicazione multi-tenant corrisponde a uno in cui tutti gli utenti dell'applicazione desideri personalizzare l'esperienza utente, ma in caso contrario sono gli stessi requisiti aziendali di base. Esempi di applicazioni multi-tenant di grandi dimensioni sono Office 365, Outlook.com e visualstudio.com.

Dal punto di vista dello sviluppatore di un'applicazione, ai vantaggi dei rappresentato riguardano prevalentemente efficienza operativa e costo. Una versione dell'applicazione può soddisfare le esigenze di molti tenant/clienti consentendo il consolidamento del sistema di attività amministrative come monitoraggio, ottimizzazione delle prestazioni, manutenzione del software e backup dei dati.

Di seguito comprende un elenco degli obiettivi e requisiti dal punto di vista dello sviluppatore più significative.

- **Provisioning**: deve essere in grado di effettuare il provisioning di nuovo tenant per l'applicazione.  Per applicazioni multi-tenant con un numero elevato di tenant, si tratta in genere necessario per automatizzare il processo, consentendo di provisioning self-service.
- **Gestibilità**: deve essere in grado di eseguire l'aggiornamento dell'applicazione ed eseguire altre attività di manutenzione mentre Usa più tenant.
- **Monitoraggio**: deve essere in grado di controllare gli in qualsiasi momento per identificare gli eventuali problemi e risolverli. Include monitoraggio come ogni tenant è tramite l'applicazione.

Un'applicazione multi-tenant implementata correttamente fornisce i seguenti vantaggi per gli utenti.

- **Isolamento**: le attività dei singoli tenant non influiscono l'utilizzo della domanda da altri tenant. Tenant possono di accesso di altri dati. Viene visualizzato nel tenant di come se hanno esclusivo dell'applicazione.
- **Disponibilità**: tenant singoli desidera un'applicazione di essere costantemente disponibili, ad esempio con garanzie definite in un contratto di servizio. Di nuovo le attività di altri tenant influire negativamente sulla disponibilità dell'applicazione.
- **Scalabilità**: l'applicazione si adatta a qualsiasi richiesta di singoli tenant. La presenza e le azioni di altri tenant non influiscono le prestazioni dell'applicazione.
- **I costi**: i costi sono inferiori esecuzione di un'applicazione dedicata, single-tenant perché multi-affitto consente di condividere risorse.
- **Personalizzazione**. La possibilità di personalizzare l'applicazione di un tenant di singoli in vari modi, ad esempio l'aggiunta o la rimozione delle funzionalità, cambiare i colori e loghi o anche aggiungere il proprio codice o script.

In breve, sebbene esistano numerosi considerazioni che è necessario prendere in considerazione per fornire un servizio scalabilità, sono disponibili anche numerosi gli obiettivi e requisiti comuni a molte applicazioni multi-tenant. Alcune potrebbero non essere pertinenti in scenari specifici e l'importanza di singoli obiettivi e requisiti variano in ogni scenario. Come provider di applicazione multi-tenant, sarà necessario anche obiettivi e requisiti, ad esempio il tenant obiettivi e requisiti, redditività, di fatturazione, più livelli di servizio, il provisioning, gestibilità monitoraggio e automazione della riunione.

Per ulteriori informazioni su ulteriori considerazioni sulla progettazione di un'applicazione multi-tenant, vedere [Hosting di un'applicazione multi-Tenant in Azure][]. Per informazioni sui modelli di architettura dati comune multi-tenant software come servizio (SaaS) delle applicazioni di database, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant con Database di SQL Azure](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure offre molte caratteristiche che consentono di risolvere i problemi principali durante la progettazione di un sistema multi-tenant.

**Isolamento**

- Sito Web segmento tenant dalle intestazioni Host con o senza le comunicazioni SSL
- Sito Web segmento tenant dai parametri Query
- Servizi Web di ruoli di lavoro
    - Ruoli di lavoro. in genere che elaborare i dati sul back-end di un'applicazione.
    - Ruoli Web che in genere fungono da front-end per le applicazioni.

**Spazio di archiviazione**

Gestione dei dati, ad esempio servizi di Database SQL Azure o lo spazio di archiviazione di Azure, ad esempio il servizio di tabella che fornisce servizi per l'archiviazione di grandi quantità di dati non strutturati e il servizio Blob che fornisce servizi per la memorizzazione di grandi quantità di testo non strutturato o dati binari, ad esempio video, audio e immagini.

- Protezione dei dati tenant nel Database di SQL appropriato accessi di SQL Server per tenant.
- Utilizzare le tabelle di Azure per applicazione risorse specificando un criterio di accesso a livello di contenitore, è possibile la possibilità di modificare le autorizzazioni senza la necessità di rilasciare il nuovo URL per le risorse è protette con le firme di accesso condiviso.
- Azure code per code di applicazione risorse Azure generalmente utilizzata per elaborazione di unità per conto di tenant, ma possono essere utilizzate anche per distribuire il lavoro necessario per il provisioning o gestione.
- Servizio Bus code per le risorse di applicazioni che inserisce lavorare a un oggetto condiviso un servizio, è possibile utilizzare una singola coda nel punto in cui ogni mittente tenant solo disponga delle autorizzazioni (derivato sulle attestazioni emesse da ACS) per inviare a tale coda, mentre solo i destinatari dal servizio di disporre dell'autorizzazione per estrarre dalla coda i dati provenienti da più tenant.


**Connessione e i servizi di sicurezza**

- Azure servizio Bus, un'infrastruttura di messaggistica che si trova tra le applicazioni consentendo per scambiare messaggi in modo accoppiamento per scala migliorata e adattabilità.

**Servizi di rete**

Azure offre diversi servizi di rete che supportano l'autenticazione e migliorare la gestibilità delle applicazioni ospitate. Questi servizi includono le seguenti:

- Azure consente di rete virtuale è effettuare il provisioning e gestire le reti private virtuali (VPN) in Azure, nonché collegarli in modo sicuro con infrastruttura IT in locale.
- Gestione il traffico di rete virtuale consente di caricare il traffico in ingresso saldo più servizi Azure ospitato se sono in esecuzione in Data Center del stesso o più Data Center diversi in tutto il mondo.
- Azure Active Directory (Azure Active Directory) è un servizio moderno, basate su REST che offre funzionalità di controllo di accesso e gestione di identità per le applicazioni cloud. Utilizzo di Azure Active Directory per le risorse di applicazioni Azure Active Directory a fornisce un modo semplice di autenticazione e autorizzazione degli utenti per accedere a servizi e applicazioni web consentendo le caratteristiche di autenticazione e autorizzazione essere suddivise fuori del codice.
- Azure Service Bus fornisce una messaggistica sicura e distribuiti capacità del flusso di dati per applicazioni ibride, ad esempio le comunicazioni tra Azure ospitato applicazioni locale applicazioni e servizi e, senza richiedere infrastrutture firewall e protezione avanzate. Per i servizi esposti come endpoint utilizzando servizio Bus inoltro per le risorse dell'applicazione può appartenere al tenant (ad esempio ospitato all'esterno di sistema, ad esempio in locale), o possono essere servizi viene completato il provisioning specificamente per il tenant (perché dati sensibili, tenant specifici percorre tra di loro).



**Il provisioning di risorse**

Azure offre diversi modi tenant nuovo provisioning per l'applicazione. Per applicazioni multi-tenant con un numero elevato di tenant, si tratta in genere necessario per automatizzare il processo, consentendo di provisioning self-service.

- Ruoli di lavoro consentono di provisioning e la disposizione per ogni tenant risorse (ad esempio quando un nuovo tenant segni-up o Annulla), raccogliere metriche per misurazione utilizzare e gestire scala in base alla determinati pianificazione o in risposta con il passaggio delle soglie degli indicatori di prestazioni chiave. Questo ruolo stesso può essere utilizzato anche per inviare gli aggiornamenti e aggiornamenti per la soluzione.
- BLOB Azure può essere usato per effettuare il provisioning di calcolo o già inizializzato risorse di archiviazione per nuovo tenant fornendo criteri di accesso a livello di contenitore proteggere il computer service pacchetti disco rigido virtuale immagini e altre risorse.
- Opzioni per il provisioning di risorse del Database di SQL per un tenant includono:

    -   DDL negli script o incorporati come risorse all'interno di assembly
    -   SQL Server 2008 R2 DAC distribuire i pacchetti a livello di programmazione.
    -   Copiare da un database di riferimento master
    -   Utilizzo di database Importa ed Esporta per effettuare il provisioning di nuovi database da un file.



<!--links-->

[Hosting di un'applicazione multi-Tenant su Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
