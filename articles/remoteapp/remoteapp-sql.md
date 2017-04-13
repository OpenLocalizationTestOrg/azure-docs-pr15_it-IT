<properties
   pageTitle="SQL Azure con RemoteApp Azure | Microsoft Azure"
   description="Informazioni su come usare SQL Azure con RemoteApp di Azure."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure con RemoteApp Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Spesso quando i clienti scelgono pubblicare le applicazioni di Windows nel cloud con Azure RemoteApp preferiscono anche eseguire la migrazione i dati, ad esempio SQL Server nel cloud per una distribuzione cloud intera. In questo modo per soluzione intera cloud ospitato che è possibile accedervi in qualsiasi momento da qualsiasi dispositivo ovunque con RemoteApp di Azure. Di seguito sono riportati collegamenti e riferimenti insieme indicazioni per semplificare il processo.  

## <a name="migrate-your-sql-data"></a>Eseguire la migrazione dei dati SQL

Iniziare la migrazione di [un database di SQL Server di Database SQL Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurare RemoteApp Azure
Ospitare l'applicazione di Windows in Azure RemoteApp. Di seguito è un livello molto elevato procedura dettagliata:

1.     Creare il [modello di Azure RemoteApp macchine Virtuali](remoteapp-imageoptions.md). 
2.     Installare l'applicazione necessaria nella macchina virtuale.
3.     Configurare l'applicazione in modo che si connette al database SQL e verificare il funzionamento.
4.     Sysprep e chiusura la macchina virtuale. Acquisire ciò come un'immagine per l'utilizzo con Azure. **Nota:** È necessario assicurarsi che l'applicazione è in grado di mantenere le informazioni di connettività DB tramite il processo sysprep. Se l'applicazione è in grado di mantenere le informazioni di connessione DB, è consigliabile sceglie fornitore dell'applicazione per controllare come è possibile specificare la stringa di connessione.
5.     Importare l'immagine personalizzata in una raccolta di Azure RemoteApp selezionando la posizione geografica corretta che si trova la distribuzione di SQL Azure. 
6.     Distribuire un insieme di RemoteApp nell'interfaccia di dati stesso come la distribuzione di SQL Azure utilizzando il modello sopra e pubblicare l'applicazione. Distribuzione di Azure RemoteApp nell'interfaccia di dati stesso come il SQL Azure distribuzione consente di verificare la velocità di connessione più veloce e ridurre la latenza. 

## <a name="app-and-sql-configuration-considerations"></a>Considerazioni sulla configurazione di App e SQL:
Esistono alcuni aspetti da considerare quando si utilizza SQL Azure con RemoteApp:

Informazioni su [come configurare un firewall di database SQL Azure](../sql-database/sql-database-firewall-configure.md). Una porzione degli stati articolo "inizialmente, tutti gli accessi al server di Database SQL Azure sono bloccato dal firewall. Per iniziare a utilizzare il server di Database SQL Azure, è necessario accedere al portale di classica e specificare una o più regole firewall a livello di server che consentono di accedere al server di Database SQL Azure. Utilizzare le regole del firewall per specificare quali intervalli di indirizzi IP da Internet consentiti e se impostare o meno applicazioni Azure provare a connettersi al server di Database SQL Azure".

Inoltre, quando un computer tenta di connettersi al server di database da Internet, il firewall controlla indirizzo IP di origine della richiesta rispetto al set completo del livello di server e (se necessario) le regole firewall a livello di database. "Se l'indirizzo IP della richiesta in uno degli intervalli specificati regole del firewall a livello di server, la connessione viene concesso al server di Database SQL Azure." Di conseguenza, è possibile far usare di intervalli di indirizzi IP e non solo indirizzi IP di origine singoli.

Seguire le istruzioni dettagliate [How to: configurare le impostazioni del firewall nel Database di SQL tramite il portale di Azure](../sql-database/sql-database-configure-firewall-settings.md) per specificare l'intervallo di indirizzi IP. Quando si siano configurando le regole del Firewall SQL, specificare l'intervallo di indirizzi IP di subnet specificata per la raccolta di Azure RemoteApp. In questo modo server ARA la connessione al database SQL, anche se verrà dinamicamente-assegnati indirizzi IP.

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se l'esperienza dell'utilizzo di un'applicazione client ospitato in Azure RemoteApp che si connette a un SQL database in cui ospitati in Azure o locale è lenta pochi i motivi possono essere perché.  

- Latenza di rete dal dispositivo in Azure sia sufficientemente alta. Spostare la connessione di rete migliori e più veloce che è possibile per ottenere prestazioni ottimali. Utilizzare [azurespeed.com](http://azurespeed.com/) come strumento generico per verificare la latenza di dispositivi al Azure data center.  
- App client ospitato in Azure RemoteApp è in condizioni di carico. Selezione di un altro piano di fatturazione, ad esempio la fatturazione Premium consente di migliorare le prestazioni. Un'altra soluzione consiste nell'eseguire il monitoraggio delle risorse dell'applicazione: durante una sessione attiva eseguire una sequenza di tasti ctrl + alt + fine che la schermata sa di avvio, selezionare Gestione attività e osservare l'utilizzo delle risorse per l'app.
- SQL server in condizioni di carico o non è ottimizzato. Attenersi alle istruzioni SQL per la risoluzione dei problemi. 

