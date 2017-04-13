<properties
    pageTitle="Piattaforme Analitica: confronto eccesso Apache a flusso Analitica | Microsoft Azure"
    description="È possibile ottenere indicazioni per la scelta di una piattaforma analitica cloud usando un confronto di Apache eccesso a flusso Analitica. Informazioni su funzionalità e le differenze."
    keywords="piattaforma analitica, piattaforme analitica, piattaforma analitica cloud, confronto eccesso"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Guida scegliendo una piattaforma analitica flusso: confronto Apache eccesso da Azure flusso Analitica

È possibile ottenere indicazioni per la scelta di una piattaforma analitica cloud usando questo confronto Apache eccesso da Azure flusso Analitica. Comprendere le proposte di valore del flusso Analitica confronto eccesso Apache come servizio gestito su Azure HDInsight, che consente di scegliere la soluzione ideale per l'azienda use case.

Sia piattaforme analitica vantaggi di una soluzione PaaS, esistono alcuni principali funzionalità distintiva distinguerle. Funzionalità e le limitazioni dei servizi seguenti sono elencate di seguito consentono di pagina iniziale sulla soluzione che è necessario raggiungere gli obiettivi del.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Tempesta confronto a flusso Analitica: caratteristiche generali ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flusso Azure Analitica</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Eccesso Apache in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Apri origine</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
No, Azure flusso Analitica è un proprietario di Microsoft che offre.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì, Apache eccesso è una tecnologia Apache concesso in licenza.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft supportati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sì </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Requisiti hardware</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non esistono requisiti hardware. Azure flusso Analitica è un servizio di Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Non esistono requisiti hardware. Apache eccesso è un servizio di Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unità di livello superiore</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Con i clienti di Azure flusso Analitica distribuire e monitorare i processi di flussi.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Con il Apache sui clienti HDInsight distribuire e monitorare un intero cluster che possono ospitare più processi eccesso, nonché altri carichi di lavoro (batch inclusa).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prezzo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flusso Analitica del prezzo volume dei dati elaborati e il numero di unità di trasmissione (orarie che il processo è in esecuzione) necessari.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Ulteriori informazioni sui prezzi sono disponibili qui.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Per eccesso Apache su HDInsight, l'unità di acquisto è basato su cluster e viene addebitata in base alla data che cluster è in esecuzione, indipendentemente dalla processi di distribuzione.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Ulteriori informazioni sui prezzi sono disponibili qui.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Creazione condivisa in ogni piattaforma analitica##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flusso Azure Analitica</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Eccesso Apache in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funzionalità: SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Sì, è disponibile un supporto di linguaggio SQL facile da usare.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
No, gli utenti devono scrivere codice in linguaggio c# o utilizzare API Trident.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Funzionalità: Operatori temporali</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Aggregazioni in finestre e join temporale sono supportati all'esterno della casella.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Operatori temporali necessario implementare dall'utente.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ambiente di sviluppo</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interattiva creazione e debug tramite il portale di Azure sui dati di esempio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sviluppo, debug e monitoraggio esperienza viene fornito tramite Visual Studio per gli utenti di .NET, mentre per Java e altri sviluppatori lingue possono utilizzare IDE scelta dall'utente.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto per il debug</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flusso Analitica offre lo stato del processo di base e registri delle operazioni allo scopo di debug, ma attualmente non offre flessibilità in cosa accade/come molto includere nei registri ie: modalità dettagliata.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Registri dettagliati sono disponibili per il debug. Esistono due modi per superficie registri per utente, tramite visual Studio o l'utente può RDP nel cluster per accedere a registri.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Supporto per utente (funzioni definite dall'utente)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Attualmente non è disponibile alcun supporto per funzioni definite dall'utente.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Funzioni definite dall'utente possono essere scritti in c#, Java o la lingua desiderata.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Estensibile - codice personalizzato</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non è disponibile alcun supporto per extensible codice in flusso Analitica.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sì, non c'è disponibilità per scrivere codice personalizzato in c#, Java o altre lingue supportate in eccesso.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Origini dati e output##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flusso Azure Analitica</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Eccesso Apache in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Origini dati di input</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Origini di input supportate sono Azure evento hub e BLOB Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Sono disponibili i connettori per evento hub, Bus di servizio, Kafka e così via. Connettori non supportati possono essere applicati tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formati di dati di input</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formati supportati sono Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualsiasi formato può essere applicato tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Output</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Un processo di flusso, potrebbe essere più output. Supportati output: Evento Azure hub, archiviazione Blob Azure, tabelle Azure, DB SQL Azure e ottenere informazioni.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Supporto per molti output in una topologia, ogni output abbia logica personalizzata per l'elaborazione downstream. All'esterno della casella eccesso include connettori per ottenere informazioni, Azure evento hub, archivio Blob Azure, DocumentDB Azure, SQL e HBase. Connettori non supportati possono essere applicati tramite codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formati di codifica di dati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flusso Analitica richiede formato dati UTF-8 da utilizzare.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Qualsiasi formato dati può essere applicata tramite codice personalizzato.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Gestione e operazioni##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flusso Azure Analitica</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Eccesso Apache in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modello di processo distribuzione</strong>
                </p>
                <p>
                    - <strong>Portale di Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Distribuzione è implementata tramite il portale di Azure, PowerShell e le API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment è implementata tramite il portale di Azure, PowerShell, Visual Studio e le API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Monitoraggio (Stat, contatori e così via)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Monitoraggio è implementato tramite il portale di Azure e le API REST.
                </p>
                <p>
L'utente può inoltre configurare avvisi Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Monitoraggio è implementato tramite eccesso dell'interfaccia utente e le API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Scalabilità</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Numero di unità di flusso per ogni processo. Ogni unità Streaming elabora fino a 1 MB/s. Numero massimo di 50 unità per impostazione predefinita. Chiamata per ingrandire le dimensioni limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Numero dei nodi cluster HDI eccesso. Nessun limite numero dei nodi (limite superiore definito dalla quota di Azure). Chiamata per ingrandire le dimensioni limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limiti di elaborazione dei dati</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Gli utenti possono scalare verso l'alto o verso il basso numero di unità di flusso per aumentare l'elaborazione dei dati o ottimizzare i costi.
                </p>
                <p>
Scala fino a 1 GB/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Utente ridimensionare verso l'alto o verso il basso dimensioni cluster in base alle esigenze.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Interrompere e riprendere</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Interrompere e riprendere dall'ultimo luogo interrotto.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Interrompere e riprendere dall'ultimo posizionare arrestato in base alla filigrana.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Aggiornamento del servizio e framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
La correzione automatica con senza tempi di inattività.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
La correzione automatica con senza tempi di inattività.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuità aziendale tramite un servizio altamente con garantito contratto di servizio</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Contratto di servizio di inattività 99,9 </p>
                <p>
Ripristino automatico da errori </p>
                <p>
Recupero di informazioni sullo stato operatori temporali è incorporato.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Contratto di servizio di inattività 99,9 del cluster eccesso. Apache eccesso è una piattaforma di trasmissione tolleranza di errore, tuttavia è responsabilità del cliente per assicurarsi che il proprio flusso di lavoro eseguito senza interruzioni.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Caratteristiche avanzate##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flusso Azure Analitica</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Eccesso Apache in HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrivo in ritardo e la gestione degli eventi nell'ordine corretto</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Criteri configurabili incorporati per riordinare, eliminare eventi o regolare l'ora dell'evento.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Utente deve implementare la logica per gestire questo scenario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Dati di riferimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dati di riferimento disponibili da BLOB Azure con dimensioni massime di 100 MB di cache di ricerca in memoria. Aggiornamento dei dati di riferimento viene gestita dal servizio.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nessun limiti per la dimensione dei dati. Connettori disponibili per HBase, DocumentDB, SQL Server e Azure. Connettori non supportati possono essere applicati tramite codice personalizzato.
                </p>
                <p>
Aggiornamento dei dati di riferimento deve essere gestita dal codice personalizzato.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Integrazione con apprendimento</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Configurando pubblicati i modelli di apprendimento Azure come funzioni durante ASA processo creazione <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(versione di anteprima privato)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponibile tramite bulloni eccesso.
                </p>
            </td>
        </tr>
    </tbody>
</table>
