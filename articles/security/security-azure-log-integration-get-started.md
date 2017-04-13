<properties
   pageTitle="Guida introduttiva a integrazione log Azure | Microsoft Azure"
   description="Informazioni su come installare il servizio di integrazione registro Azure e integrare i registri di archiviazione Azure, i log di controllo Azure e gli avvisi di Centro protezione di Azure."
   services="security"
   documentationCenter="na"
   authors="TomShinder"
   manager="MBaldwin"
   editor="TerryLanfear"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2016"
   ms.author="TomSh"/>

# <a name="get-started-with-azure-log-integration-preview"></a>Guida introduttiva a integrazione di Azure log (Preview)

Integrazione di Azure log consente di integrare i registri non elaborati dalle risorse Azure i sistemi di informazioni sulla protezione e gestione di evento (SIEM) locale. Questa integrazione fornisce un dashboard unificato per tutte le attività, in locale o nel cloud, in modo da aggregare, correlare, analizzare e un avviso per gli eventi di protezione associati alle applicazioni.

In questa esercitazione viene illustrato come installare l'integrazione di Azure log e integrare i registri di archiviazione Azure, i log di controllo Azure e gli avvisi di Centro protezione di Azure. Durata prevista per completare questa esercitazione è un'ora.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario disporre le operazioni seguenti:

- Un computer (locale o nel cloud) per installare il servizio di integrazione di Azure log. In questo computer deve essere eseguito un sistema operativo Windows a 64 bit con .net 4.5.1 installato. In questo computer è chiamato **Azlog Integrator**.
- Azure abbonamento. Se non hai uno, è possibile iscriversi a un [account gratuito](https://azure.microsoft.com/free/).
- Diagnostica Windows Azure abilitata per il Azure macchine (). Per abilitare la diagnostica per i servizi Cloud, vedere [Attivazione di Azure diagnostica in servizi Cloud Windows Azure](../cloud-services/cloud-services-dotnet-diagnostics.md). Per attivare la diagnostica per un sistema operativo Windows macchine Virtuali di Azure, vedere [Usare PowerShell per attivare la diagnostica in macchina virtuale in esecuzione Windows Azure](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md).
- Connettività da integratore Azlog per lo spazio di archiviazione Azure e per eseguire l'autenticazione e autorizzare alla sottoscrizione Azure.
- Per i registri di macchine Virtuali di Azure, l'agente SIEM (ad esempio inoltro universale Splunk, agente di raccolta eventi di Windows ArcSight HP o IBM QRadar WinCollect) deve essere installato integratore Azlog.

## <a name="deployment-considerations"></a>Considerazioni sulla distribuzione

È possibile eseguire più istanze di integratore Azlog se evento volume sia sufficientemente alto. Il bilanciamento del carico tra gli account di archiviazione di Azure diagnostica per Windows *(tampone)* e il numero delle sottoscrizioni per fornire le istanze di basare la capacità.

In un computer 8 processori (core), una sola istanza di Azlog integratore può elaborare eventi 24 milioni al giorno (~1M/hour).

In un computer processore 4 (core), una sola istanza di Azlog integratore può elaborare eventi di 1,5 milioni al giorno (~62.5K/hour).

## <a name="install-azure-log-integration"></a>Installare l'integrazione di Azure log

Scaricare [Azure accedere integrazione](https://www.microsoft.com/download/details.aspx?id=53324).

Il servizio di integrazione log Azure raccoglie i dati di telemetria dal computer in cui è installato.  Dati di telemetria raccolti sono:

- Eccezioni che si verificano durante l'esecuzione dell'integrazione di Azure log
- Metrica sul numero di query e gli eventi di elaborazione
- Statistiche sulle quali Azlog.exe vengono utilizzate le opzioni della riga di comando

> [AZURE.NOTE] È possibile disattivare la raccolta di dati di telemetria deselezionando questa opzione.

## <a name="integrate-azure-vm-logs-from-your-azure-diagnostics-storage-accounts"></a>Integrare i registri di macchine Virtuali di Azure dagli account Azure diagnostica lo spazio di archiviazione

1. Controllare i prerequisiti elencati sopra per assicurarsi che l'account di archiviazione tampone raccoglie registri prima di continuare l'integrazione di Azure log. Se l'account di archiviazione tampone non è la raccolta di log, non eseguire la procedura seguente.

2. Aprire il prompt dei comandi e **cd** in **c:\Programmi\Microsoft c:\Programmi\Microsoft Azure Log integrazione**.

3. Eseguire il comando

        azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>

      Sostituire StorageAccountName con il nome dell'account di archiviazione Azure configurato per ricevere gli eventi di diagnostica della macchina virtuale.

        azlog source add azlogtest WAD azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==

      Se si vuole che l'id di abbonamento per la visualizzazione degli eventi XML, aggiungere il nome descrittivo ID abbonamento:

        azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>

4. Attendere 30-60 minuti (questa operazione potrebbe richiedere fino a un'ora) e quindi visualizzare gli eventi che vengono estratti dall'account di archiviazione. Per visualizzare, aprire **Visualizzatore eventi > registri di Windows > eventi inoltrati** su integratore Azlog.

5. Assicurarsi che il connettore SIEM standard è installato nel computer sia configurato per selezionare gli eventi dalla cartella **Eventi inoltrati** e basta inviare i dati all'istanza SIEM. Verificare la configurazione specifica SIEM per configurare e visualizzare i log integrazione.

## <a name="what-if-data-is-not-showing-up-in-the-forwarded-events-folder"></a>Cosa fare se dati non viene visualizzata nella cartella eventi di inoltro delle chiamate?

Se dopo un'ora dati non viene visualizzata nella cartella **Eventi inoltrati** , quindi:

1. Controllare il computer e verificare che possono accedere a Azure. Per testare la connettività, provare ad aprire il [portale di Azure](http://portal.azure.com) dal browser.

2. Verificare che l' account utente **azlog** dispone dell'autorizzazione di scrittura per la cartella **users\azlog**.

3. Verificare che l'account di archiviazione aggiunto comando **Aggiungi origine azlog** è presente nell'elenco quando si esegue il comando **elenco di origine azlog**.
4. Passare a **Visualizzatore eventi > registri di Windows > applicazione** per rilevare eventuali errori segnalati dall'integrazione log Azure.

Se ancora non è visibile agli eventi, quindi:

1. Scaricare [Microsoft Azure archiviazione Explorer](http://storageexplorer.com/).

2. Connettersi all'account di archiviazione aggiunto il comando **Aggiungi origine azlog**.

3. In Esplora risorse lo spazio di archiviazione di Azure, passare alla tabella **WADWindowsEventLogsTable** per controllare se è presente uno qualsiasi dei dati. In caso contrario, quindi diagnostica nella macchina virtuale non è configurata correttamente.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrare i log di controllo Azure e gli avvisi di Centro protezione

1. Aprire il prompt dei comandi e **cd** in **c:\Programmi\Microsoft c:\Programmi\Microsoft Azure Log integrazione**.

2. Eseguire il comando

        azlog createazureid

      Questo comando richiede l'accesso Azure. Il comando crea quindi un [Principale del servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) in Azure tenant di Active Directory che ospitano le sottoscrizioni Azure in cui l'utente ha effettuato l'accesso è un amministratore, un amministratore condivisa o un proprietario. Il comando avrà esito negativo se l'utente ha effettuato l'accesso è solo un utente Guest nel Tenant Azure Active Directory. Autenticazione di Azure viene eseguita tramite Azure Active Directory (AD).  Creazione di un servizio principale per l'integrazione di Azlog crea l'identità di Azure Active Directory che è possibile ottenere accesso in lettura da Azure abbonamenti.

3. Eseguire il comando

        azlog authorize <SubscriptionID>

      Per l'assegnazione di accesso in lettura nella sottoscrizione al servizio principale creato nel passaggio 2. Se non si specifica un SubscriptionID, tenta di assegnare il ruolo di lettore dell'entità servizio a tutte le sottoscrizioni a cui si ha l'accesso.

        azlog authorize 0ee9d577-9bc4-4a32-a4e8-c29981025328

      > [AZURE.NOTE] Se si esegue il comando **autorizzare** immediatamente dopo il comando **createazureid** , sarà possibile visualizzare avvisi. Non esiste alcune latenza tra quando viene creato l'account Azure Active Directory e quando è disponibile per l'uso dell'account. Se si attendere circa 10 secondi dopo l'esecuzione del comando **createazureid** per eseguire il comando **autorizzare** , non è necessario verificare questi avvisi.

4. Controllare le cartelle seguenti per verificare che i file di JSON log di controllo sono disponibili:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Controllare le cartelle seguenti per verificare l'esistenza di Centro protezione avvisa che:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Scegliere il standard SIEM server d'inoltro del connettore file nella cartella appropriata per inviare i dati per l'istanza SIEM. Potrebbe essere necessario alcuni mapping dei campi in base al prodotto SIEM in uso.

Se hai domande sull'integrazione di Log di Azure, inviare un messaggio di posta elettronica a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si appreso installare l'integrazione di Azure log e integrare registri dallo spazio di archiviazione Azure. Per ulteriori informazioni, vedere gli articoli seguenti:

- [Integrazione di Microsoft Azure Log per i registri Azure (Preview)](https://www.microsoft.com/download/details.aspx?id=53324) -download per informazioni dettagliate, requisiti di sistema e istruzioni di installazione sull'integrazione di Azure log.
- [Introduzione all'integrazione dei log Azure](security-azure-log-integration-overview.md) : il documento viene presentato l'integrazione di Azure log, le relative funzionalità chiave e sul suo funzionamento.
- [Passaggi di configurazione per partner](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) : questo post di blog viene illustrato come configurare l'integrazione di Azure log per l'uso con le soluzioni partner Splunk ArcSight HP e IBM QRadar.
- [Registro azure integrazione domande frequenti domande frequenti](security-azure-log-integration-faq.md) - domande frequenti su questo fornisce le risposte alle domande sull'integrazione di Azure log.
- [Gli avvisi di Centro protezione di integrazione con Azure accedere integrazione](../security-center/security-center-integrating-alerts-with-log-integration.md) : il documento viene illustrato come sincronizzare gli avvisi di Centro protezione, insieme agli eventi di sicurezza macchina virtuale raccolti da Azure diagnostica e i log di controllo Azure, con i log analitica o soluzione SIEM.
- [Nuove funzionalità di diagnostica Windows Azure e i log di controllo Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) : questo post di blog viene presentato il log di controllo di Azure e altre caratteristiche che consentono di ottengono informazioni approfondite nelle operazioni delle risorse di Azure.
