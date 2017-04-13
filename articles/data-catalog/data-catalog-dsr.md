<properties
   pageTitle="Origini dati supportate catalogo dati di Azure | Microsoft Azure"
   description="Specifica delle origini dati attualmente supportati."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="jstrauss"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/15/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-supported-data-sources"></a>Catalogo dati di Azure origini dati supportate

Gli utenti del catalogo dati Azure possono pubblicare metadati tramite un'API pubblica un clic-una volta registrazione strumento o immettendo manualmente le informazioni direttamente al catalogo dati web portale. La griglia seguente sono riepilogate tutte le origini supportate da oggi il catalogo e le funzionalità di pubblicazione per ognuno.  Sono inoltre elencati gli strumenti di dati esterni in grado di avviare ogni origine dall'esperienza portale "Apri aggiuntivo". Nella seconda griglia nell'articolo include più tecniche specifiche di ogni proprietà di connessione di origini dati.


## <a name="list-of-supported-data-sources"></a>Elenco delle origini dati supportate

<table>

    <tr>
       <td><b>Oggetto di origine dati</b></td>
       <td><b>API</b></td>
       <td><b>Immissione manuale</b></td>
       <td><b>Strumento per la registrazione</b></td>
       <td><b>Strumenti di apertura</b></td>
       <td><b>Note</b></td>
    </tr>

    <tr>
      <td>Directory Lake dell'archivio dati di Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File di archivio Lake dati di Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivio Blob Azure dello spazio di archiviazione</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory di archiviazione Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di archiviazione Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Directory HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione di hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione di MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di Database Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione di un Database Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Altro (risorse generiche)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione di Warehouse di dati SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensione di SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services degli indicatori KPI</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Analysis Services misura</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>SQL Server Reporting Services Report</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Solo server modalità nativa. Modalità di SharePoint non è supportata.</font></td>
    </tr>

    <tr>
      <td>Tabella di SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, ottenere informazioni, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella di Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione di Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione Hana SAP</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Ottenere informazioni</font></td>
      <td><font size=2>Visualizzazioni di calcolo e visualizzazioni analitiche di sola lettura. Non sono supportate le visualizzazioni degli attributi.</font></td>
    </tr>

    <tr>
      <td>Tabella DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione DB2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File di sistema</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directory FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Report http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Punto finale http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>File http</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Set di entità OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Funzione OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabella PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Visualizzazione Hana SAP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Oggetto di Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Elenco di SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Se è necessario il supporto per le altre origini, inviare una richiesta di funzionalità utilizzando il [Catalogo dati di Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Specifica di riferimento origine dati
> [AZURE.NOTE] Colonna "DSL struttura" nella seguente tabella solo elenchi proprietà connessione per l'elenco di proprietà di "indirizzo" utilizzati dal catalogo dati di Azure (ad esempio elenco di proprietà di "indirizzo" può contenere altre proprietà di connessione dell'origine dati che catalogo dati di Azure persiste, ma non utilizza).
<table>
    <tr>
       <td><b>Tipo di origine</b></td>
       <td><b>Tipo di risorsa</b></td>
       <td><b>Tipi di oggetto</b></td>
       <td><b>Struttura DSL<b></td>
    </tr>
    <tr>
      <td>Archivio Lake dati di Azure</td>
      <td>Contenitore</td>
      <td>Dati Lake</td>
      <td>
        <font size=2>protocollo: webhdfs <br>autenticazione: {oauth di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Archivio Lake dati di Azure</td>
      <td>tavolo</td>
      <td>Directory, File</td>
      <td>
        <font size=2>protocollo: webhdfs <br>autenticazione: {oauth di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Spazio di archiviazione Azure</td>
      <td>Contenitore</td>
      <td>Contenitore</td>
      <td>
        <font size=2>protocollo: BLOB azure <br>autenticazione: {tasto di scelta azure} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contenitore</font>
      </td>
    </tr>
    <tr>
      <td>Spazio di archiviazione Azure</td>
      <td>tavolo</td>
      <td>BLOB, Directory</td>
      <td>
        <font size=2>protocollo: BLOB azure <br>autenticazione: {tasto di scelta azure} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;contenitore <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Spazio di archiviazione Azure</td>
      <td>Contenitore</td>
      <td>Contenitore</td>
      <td>
        <font size=2>protocollo: tabelle azure <br>autenticazione: {tasto di scelta azure} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;account</font>
      </td>
    </tr>
    <tr>
      <td>Spazio di archiviazione Azure</td>
      <td>tavolo</td>
      <td>tavolo</td>
      <td>
        <font size=2>protocollo: tabelle azure <br>autenticazione: {tasto di scelta azure} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;dominio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;account <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;nome</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contenitore</td>
      <td>Cluster virtuale</td>
      <td>
        <font size=2>protocollo: cosmos <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>tavolo</td>
      <td>Visualizzazione flusso, Set di flusso</td>
      <td>
        <font size=2>protocollo: cosmos <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Report</td>
      <td>Report del Dashboard</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: db2 <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>DB2</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: db2 <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema</font>
      </td>
    </tr>
    <tr>
      <td>File System</td>
      <td>tavolo</td>
      <td>File</td>
      <td>
        <font size=2>protocollo: file <br>autenticazione: {Nessuno, windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;percorso</font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>tavolo</td>
      <td>Directory, File</td>
      <td>
        <font size=2>protocollo: ftp <br>autenticazione: {Nessuno, windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>Contenitore</td>
      <td>Cluster</td>
      <td>
        <font size=2>protocollo: webhdfs <br>autenticazione: {oauth di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Hadoop Distributed File System</td>
      <td>tavolo</td>
      <td>Directory, File</td>
      <td>
        <font size=2>protocollo: webhdfs <br>autenticazione: {oauth di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: hive <br>autenticazione: {username di base, hdinsight, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: hive <br>autenticazione: {username di base, hdinsight, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;serverProtocol: {hive2}</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Report</td>
      <td>Report del Dashboard</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>tavolo</td>
      <td>Punto finale, File</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: mysql <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: mysql <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contenitore</td>
      <td>Contenitore di entità</td>
      <td>
        <font size=2>protocollo: odata <br>autenticazione: {Nessuno, windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>tavolo</td>
      <td>Set di entità, funzione</td>
      <td>
        <font size=2>protocollo: odata <br>autenticazione: {Nessuno, windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;risorsa</font>
      </td>
    </tr>
    <tr>
      <td>Database Oracle</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: oracle <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Database Oracle</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: oracle <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: postgresql <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>PostgreSQL</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: postgresql <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contenitore</td>
      <td>Sito</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Report</td>
      <td>Report del Dashboard</td>
      <td>
        <font size=2>protocollo: http <br>autenticazione: {Nessuno, basic, windows, oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>tavolo</td>
      <td>Mashup di dati</td>
      <td>
        <font size=2>protocollo: power query <br>autenticazione: {oauth} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>Forza vendita</td>
      <td>tavolo</td>
      <td>Oggetto</td>
      <td>
        <font size=2>protocollo: salesforce com <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;itemName</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>
        <font size=2>protocollo: sap-hana-sql <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server</font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>tavolo</td>
      <td>Visualizzazione</td>
      <td>
        <font size=2>protocollo: sap-hana-sql <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>tavolo</td>
      <td>Elenco</td>
      <td>
        <font size=2>protocollo: elenco di sharepoint <br>autenticazione: {windows di base,} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Comando</td>
      <td>Stored Procedure</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Funzione con valori di tabella</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Stored Procedure</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Funzione con valori di tabella</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: td <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;schema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>Multidimensionali di SQL Server Analysis Services</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello</font>
      </td>
    </tr>
    <tr>
      <td>Multidimensionali di SQL Server Analysis Services</td>
      <td>INDICATORE KPI</td>
      <td>INDICATORE KPI</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>Multidimensionali di SQL Server Analysis Services</td>
      <td>Misura</td>
      <td>Misura</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {misura}</font>
      </td>
    </tr>
    <tr>
      <td>Multidimensionali di SQL Server Analysis Services</td>
      <td>tavolo</td>
      <td>Dimensione</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {dimensione}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulare di SQL Server Analysis Services</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello</font>
      </td>
    </tr>
    <tr>
      <td>Tabulare di SQL Server Analysis Services</td>
      <td>INDICATORE KPI</td>
      <td>INDICATORE KPI</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {KPI}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulare di SQL Server Analysis Services</td>
      <td>Misura</td>
      <td>Misura</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {misura}</font>
      </td>
    </tr>
    <tr>
      <td>Tabulare di SQL Server Analysis Services</td>
      <td>tavolo</td>
      <td>tavolo</td>
      <td>
        <font size=2>protocollo: analysis services <br>autenticazione: {windows, di base, anonima, nessuno} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;objectType: {Table}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenitore</td>
      <td>Server</td>
      <td>
        <font size=2>protocollo: reporting services <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versione: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Report</td>
      <td>Report</td>
      <td>
        <font size=2>protocollo: reporting services <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;percorso <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;versione: {ReportingService2010}</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contenitore</td>
      <td>Database</td>
      <td>
        <font size=2>protocollo: teradata <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database</font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>tavolo</td>
      <td>Visualizzazione tabella</td>
      <td>
        <font size=2>protocollo: teradata <br>autenticazione: {protocollo, windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Server <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;database <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;oggetto</font>
      </td>
    </tr>
    <tr>
      <td>Servizi di dati SQL Server Master</td>
      <td>Contenitore</td>
      <td>Modello</td>
      <td>
        <font size="2">protocollo: strategia di download minima mssql <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versione</font>
      </td>
    </tr>
    <tr>
      <td>Servizi di dati SQL Server Master</td>
      <td>tavolo</td>
      <td>Entità</td>
      <td>
        <font size="2">protocollo: strategia di download minima mssql <br>autenticazione: {windows} <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;URL <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;modello <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Versione <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;entità</font>
      </td>
    </tr>
    <tr>
      <td>Altro (non le risposte sopra)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2>protocollo: risorse generiche <br>indirizzo: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;idargomento</font>
      </td>
    </tr>
</table>
