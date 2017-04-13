<properties
   pageTitle="Logica App installare locale dati gateway | Microsoft Azure"
   description="Informazioni su come installare il gateway di dati in locale per l'utilizzo in un'app di logica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installare il gateway di dati locali per le applicazioni di logica

## <a name="installation-and-configuration"></a>Installazione e configurazione

### <a name="prerequisites"></a>Prerequisiti

Requisiti minimi:

* 4.5 .NET framework
* versione a 64 bit di Windows 7 o Windows Server 2008 R2 (o versioni successive)

Consigliata:

* CPU core 8
* 8 GB di memoria
* versione a 64 bit di Windows 2012 R2 (o versioni successive)

Considerazioni:

* Non è possibile installare un gateway su un controller di dominio.
* Non devono installare un gateway su un computer, ad esempio un computer portatile, che può essere spento, in, o non è connesso a Internet perché il gateway non è possibile eseguire in uno di questi casi. Inoltre, le prestazioni di gateway potrebbero subire in una rete wireless.

### <a name="install-a-gateway"></a>Installare un gateway

È possibile ottenere il [programma di installazione per il gateway di dati di locale](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Specificare **gateway dati locali** come modalità, accedere con il proprio lavoro o dell'istituto di istruzione account e quindi configurare un nuovo gateway o eseguire la migrazione, ripristinare oppure subentrare un gateway esistente.

* Per configurare un gateway, digitare un **nome** e una **chiave di ripristino**, quindi fare clic o toccare **configurato automaticamente**.

    Specificare una chiave di ripristino che contiene almeno otto caratteri e mantenerlo in un luogo sicuro. È necessario questo tasto se si desidera eseguire la migrazione, ripristinare o subentrare il gateway.

* Per eseguire la migrazione, ripristinare o subentrare un gateway esistente, immettere il codice di ripristino che è stato specificato quando è stato creato il gateway.

### <a name="restart-the-gateway"></a>Riavviare il gateway

Il gateway viene eseguito come servizio Windows e, come per qualsiasi altro servizio di Windows, è possibile avviare e arrestare in molti modi diversi. Ad esempio, è possibile aprire un prompt dei comandi con privilegi elevate autorizzazioni nel computer in cui è in esecuzione il gateway e quindi eseguire uno di questi comandi:

* Per arrestare il servizio, eseguire il comando:

    `net stop PBIEgwService`

* Per avviare il servizio, eseguire il comando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurare un proxy o un firewall

Per informazioni su come fornire informazioni sul proxy per il gateway, vedere [configurare le impostazioni del proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

È possibile verificare se il firewall o proxy, potrebbe essere bloccato le connessioni eseguendo il seguente comando da un prompt dei comandi PowerShell. Il test verrà eseguito connettività a Bus di servizio Azure. Solo questo test di connettività di rete e non è disponibile alcuna di utilizzo del servizio cloud di server o il gateway. Può essere utile per determinare se il computer può accedervi a internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

I risultati dovrebbe essere simile a questo esempio. Se **TcpTestSucceeded** non è vero, potrebbe essere bloccato da un firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se si vuole essere completo, sostituire i valori **nomecomputer** e la **porta** con quelli elencati nella casella [Configura porte](#configure-ports) più avanti in questo argomento.

Il firewall può anche Blocca le connessioni Bus di servizio Azure consente ai Azure data center. In tal caso, è consigliabile proprietà consentite (sbloccare) tutti gli indirizzi IP per il proprio paese per tali centri dati. È possibile ottenere un elenco di [indirizzi IP di Azure qui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurare le porte

Il gateway consente di creare una connessione in uscita a Bus di servizio Azure. Comunica su porte in uscita: TCP 443 (impostazione predefinita), 5671, 5672, 9350 e 9354. Il gateway non sono necessarie le porte in ingresso.

Ulteriori informazioni sulle [soluzioni ibride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMI DI DOMINIO | PORTE IN USCITA | DESCRIZIONE |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671 5672 | Avanzate Accodamento Protocol (AMQP) |
| *. servicebus.windows.net | 443, 9350 9354 | Listener nel servizio Bus inoltro su TCP (richiede 443 per l'acquisizione di token di controllo dell'accesso) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| Login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Consente di verificare la connettività internet se il gateway non è raggiungibile dal servizio Power BI. |

Se è necessario bianco elenco indirizzi IP al posto dei domini, è possibile scaricare e usare l' [elenco di intervalli di indirizzi IP di Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653). In alcuni casi, sarà connessioni Bus di servizio Azure con indirizzo IP al posto dei nomi di dominio completo.

### <a name="sign-in-account"></a>Account di accesso

Gli utenti verranno effettuare l'accesso con uno un lavoro o dell'istituto di istruzione account. Questo è l'account aziendale. Se si ha effettuato l'iscrizione per un'offerta di Office 365 e non ha fornito alla posta elettronica di lavoro effettivo, potrebbe apparire come jeff@contoso.onmicrosoft.com. L'account, all'interno di un servizio cloud, è archiviato all'interno di un tenant di Azure Active Directory (AAD). Nella maggior parte dei casi, UPN del proprio account AAD corrisponderanno l'indirizzo di posta elettronica.

### <a name="windows-service-account"></a>Account del servizio Windows

Il gateway di dati in locale è configurato per l'utilizzo SERVICE\PBIEgwService NT per le credenziali di accesso del servizio di Windows. Per impostazione predefinita, ha il diritto di Log come servizio. Verrà visualizzata nel contesto del computer in cui si sta eseguendo l'installazione del gateway.

Non è l'account usato per connettersi a origini dati locali o l'account aziendale o dell'istituto di istruzione con cui si accede a servizi cloud.

##<a name="frequently-asked-questions"></a>Domande frequenti

### <a name="general"></a>Generale

**Domanda**: quali origini dati supporta il gateway?<br/>
**Answer**: al momento di questo scrittura, SQL Server.

**Domanda**: necessario un gateway per le origini dati nel cloud, ad esempio SQL Azure? <br/>
**Risposta**: No. Un gateway si connette a origini dati locali solo.

**Domanda**: cosa si chiama il servizio Windows effettivo?<br/>
**Risposta**: In servizi, il gateway è denominato servizio Gateway di Power BI dell'organizzazione.

**Domanda**: esistono tutte le connessioni in ingresso al gateway dal cloud? <br/>
**Risposta**: No. Il gateway utilizza le connessioni in uscita a Bus di servizio Azure.

**Domanda**: cosa fare se si bloccano le connessioni in uscita? Che cos'è necessario aprire? <br/>
**Answer**: vedere le porte e protocolli host che utilizza il gateway.


**Domanda**: gateway deve essere installata nello stesso computer come file di dati? <br/>
**Risposta**: No. Il gateway si connetterà alla fonte dati utilizzando le informazioni di connessione fornito. Pensare del gateway come applicazione client in tal senso. È necessario solo in grado di connettersi al nome del server fornito.


**Domanda**: che cos'è la latenza per l'esecuzione di query a un'origine dati dal gateway? Che cos'è l'architettura migliore? <br/>
**Risposta**: per ridurre la latenza di rete, installare il gateway quanto più vicino l'origine di dati possibile. Se è possibile installare il gateway dell'origine dati effettivi, si ridurrà la latenza introdotta. Prendere in considerazione i data center. Ad esempio, se il servizio è mediante l'interfaccia di dati uniti ovest e si dispone di SQL Server ospitato in una macchina virtuale Azure, verrà vuole è Azure negli Stati Uniti ovest anche. Per ridurre al minimo la latenza ed evitare i costi di uscita nella macchina virtuale Azure.


**Domanda**: esistono requisiti di larghezza di banda di rete? <br/>
**Answer**: è consigliabile mantenere velocità ottimale per la connessione di rete. Ogni ambiente è diverso, e la quantità di dati inviati viene influenzata dai risultati. Utilizzo ExpressRoute potrebbe contribuire a garantire un livello di velocità tra locale e Azure data center.

È possibile usare l'app di Test di velocità Azure strumenti di terze parti per stimare che cos'è la velocità.


**Domanda**: eseguita il gateway servizio Windows con un account Azure Active Directory? <br/>
**Risposta**: No. Il servizio Windows deve avere un account di Windows valido. Per impostazione predefinita, verrà eseguito con il servizio, NT SERVICE\PBIEgwService.


**Domanda**: come risultati inviati al cloud? <br/>
**Risposta**: questa operazione viene eseguita in Azure Service Bus. Per ulteriori informazioni, vedere come funziona.


**Domanda**: nel punto in cui sono archiviate le credenziali? <br/>
**Risposta**: le credenziali immesse per un'origine dati sono archiviate vengono crittografati durante il servizio cloud di gateway. Le credenziali sono decrittografare nei gateway locale.

### <a name="high-availabilitydisaster-recovery"></a>Disponibilità elevata/di emergenza

**Domanda**: sono disponibili i piani per l'attivazione di scenari di disponibilità con il gateway? <br/>
**Risposta**: questa impostazione è attiva la Guida di orientamento, ma non si dispone ancora di una sequenza temporale.


**Domanda**: quali opzioni sono disponibili per il ripristino di emergenza? <br/>
**Answer**: È possibile utilizzare la chiave di ripristino per ripristinare o spostare un gateway. Quando si installa il gateway, specificare la chiave di ripristino.


**Domanda**: qual è il vantaggio della chiave di ripristino? <br/>
**Risposta**: consente di migrare o ripristinare le impostazioni del gateway dopo un'emergenza.

### <a name="troubleshooting"></a>Risoluzione dei problemi

**Domanda**: dove si trovano i registri di gateway? <br/>
**Answer**: Vedere strumenti più avanti in questo argomento.


**Domanda**: come è possibile visualizzare le query vengono inviate all'origine dati locale? <br/>
**Answer**: È possibile abilitare la traccia di query, che include le query inviate. È necessario modificare nuovamente il valore originale dopo completato la risoluzione dei problemi. Uscire dalla query attivata causeranno i log diventa di dimensioni superiori.

È anche possibile visualizzare strumenti contenente l'origine dati per le query di analisi. Ad esempio, è possibile utilizzare gli eventi estesa o SQL Profiler per SQL Server e Analysis Services.

## <a name="how-the-gateway-works"></a>Come funziona il gateway

Quando un utente interagisce con un elemento è collegato a un'origine dati locale:

1. Il servizio cloud di creare una query, con le credenziali crittografate per l'origine dati e invia la query alla coda per il gateway per l'elaborazione.
1. Il servizio analizza la query e inserisce la richiesta di Azure Service Bus.
1. Il gateway di dati in locale polling Bus di servizio Azure per le richieste in sospeso.
1. Il gateway Ottiene la query, decrittografa le credenziali e si connette a origini dati con le credenziali.
1. Il gateway invia la query all'origine dati per l'esecuzione.
1. I risultati vengono inviati dall'origine dati al gateway e quindi su servizio cloud. Il servizio utilizza quindi i risultati.

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="update-to-the-latest-version"></a>L'aggiornamento alla versione più recente

Quando la versione del gateway non è aggiornata, possono fornire l'interfaccia utente numerosi problemi.  È buona norma generale verificare che siano presenti la versione più recente.  Se non è stato aggiornato il gateway per un mese o più lungo, è consigliabile valutare la possibilità di installare l'ultima versione del gateway e verificare se è possibile riprodurre il problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Errore: Impossibile aggiungere l'utente al gruppo. (-2147463168 PBIEgwService Performance Log Users)

Questo errore si verifichi se si sta tentando di installare il gateway su un controller di dominio, non è supportato. È necessario distribuire il gateway su un computer che non è un controller di dominio.

## <a name="tools"></a>Strumenti

### <a name="collecting-logs-from-the-gateway-configurator"></a>Raccogliere i log dalla configurazione di gateway

È possibile raccogliere diversi registri per il gateway. Iniziano sempre con i log!

#### <a name="installer-logs"></a>Programma di installazione registri

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Registri di configurazione

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Registri del servizio gateway Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Registri eventi

I registri di Gateway di gestione dati e PowerBIGateway sono disponibili **nell'applicazione registri e servizi**.

### <a name="fiddler-trace"></a>Traccia Fiddler

[Fiddler](http://www.telerik.com/fiddler) è uno strumento gratuito di Telerik che controlla il traffico HTTP.  È possibile vedere sul retro e indietro con Power BI assistenza dal computer client. In questo può visualizzare errori e altre informazioni correlate.

## <a name="next-steps"></a>Passaggi successivi
- [Creare una connessione locale alle App logica](app-service-logic-gateway-connection.md)
- [Caratteristiche di integrazione Enterprise](app-service-logic-enterprise-integration-overview.md)
- [Connettori App logica](../connectors/apis-list.md)