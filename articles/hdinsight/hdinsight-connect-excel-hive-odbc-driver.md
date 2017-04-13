<properties
   pageTitle="Connettere Excel a Hadoop con il Driver ODBC Hive | Microsoft Azure"
   description="Informazioni su come configurare e usare il driver ODBC Hive Microsoft per Excel per eseguire query sui dati in un cluster di HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="jhubbard"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

#<a name="connect-excel-to-hadoop-with-the-microsoft-hive-odbc-driver"></a>Connettere Excel a Hadoop con il driver ODBC Hive Microsoft

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft dati si integra componenti Microsoft Business Intelligence (BI) con i cluster Hadoop Apache distribuiti da Azure HDInsight. Esempio di questa integrazione è la possibilità di connettersi Excel all'archivio di dati Hive di un cluster di Hadoop in HDInsight con il Driver Microsoft Hive connettività ODBC (Open Database).

È anche possibile connettere i dati associati a un cluster di HDInsight e altre origini dati, inclusi altri cluster Hadoop (non HDInsight), da Excel utilizzando il componente aggiuntivo di Microsoft Power Query per Excel. Per informazioni sull'installazione e utilizzo di Power Query, vedere [Connettere Excel a HDInsight con Power Query][hdinsight-power-query].

> [AZURE.NOTE] Durante la procedura descritta in questo articolo è utilizzabile con cluster Linux o HDInsight basato su Windows, Windows è necessario per la workstation client.

**Prerequisiti**:

Prima di iniziare questo articolo, è necessario disporre le operazioni seguenti:

- **HDInsight un cluster**. Per creare uno, vedere [Guida introduttiva di Azure HDInsight][hdinsight-get-started].
- **Risposte workstation** con Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 autonomo o Office 2010 Professional Plus.


##<a name="install-microsoft-hive-odbc-driver"></a>Installare driver ODBC Hive Microsoft

Scaricare e installare Driver ODBC Hive Microsoft dall' [Area Download][hive-odbc-driver-download].

Questo può essere installato nelle versioni a 32 bit o 64 bit di Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 e Windows Server 2012 e consentirà di connettersi a Azure HDInsight (versione 1.6 e versioni successive) e Azure HDInsight emulatore (v.1.0.0.0 e versioni successive). È necessario installare la versione che corrisponde alla versione dell'applicazione in cui si userà il driver ODBC. Per questa esercitazione verrà utilizzato il driver da Office Excel.

##<a name="create-hive-odbc-data-source"></a>Creare l'origine dati ODBC Hive

La procedura seguente mostra come creare un'origine dati ODBC Hive.

1. In Windows 8 o Windows 10, premere il tasto Windows per aprire la schermata Start e quindi digitare **le origini dati**.
2. Fare clic su **configurare dati ODBC origini (32 bit)** o **impostare le origini dati ODBC (64 bit)** , a seconda della versione di Office. Se si utilizza Windows 7, scegliere **Origini dati ODBC (32 bit)** o **Origini dati ODBC (64 bit)** da **Strumenti di amministrazione**. Verrà avviata la finestra di dialogo **Amministratore origine dati ODBC** .

    ![Amministratore di origine dati OBDC][img-hdi-simbahiveodbc-datasource-admin]

3. Da DNS utente, fare clic su **Aggiungi** per aprire la procedura guidata **Crea nuova origine dati** .
4. Selezionare **Driver ODBC Hive Microsoft**e quindi fare clic su **Fine**. Verrà avviata la finestra di dialogo **Hive ODBC Driver DNS installazione di Microsoft** .

5. Digitare o selezionare i valori seguenti:

    Proprietà|Descrizione
    ---|---
    Nome dell'origine dati|Assegnare un nome all'origine dati
    Host|Immettere &lt;HDInsightClusterName >. azurehdinsight.net. Ad esempio myHDICluster.azurehdinsight.net
    Porta|Utilizzare <strong>443</strong>. (Questa porta è stata modificata da 563 alla porta 443.)
    Database|Utilizzare <strong>predefinito</strong>.
    Tipo di Server hive|Selezionare <strong>Hive Server 2</strong>
    Meccanismo|Selezionare <strong>Il servizio Azure HDInsight</strong>
    Percorso HTTP|Lasciare vuota.
    Nome utente|Immettere nome utente cluster HDInsight. Questo è il nome utente creato durante il processo di provisioning cluster. Se è stata utilizzata l'opzione Crea rapido, il nome utente predefinito è <strong>admin</strong>.
    Password|Immettere la password utente cluster HDInsight.
    </table>

    Esistono alcuni parametri importanti da tenere presenti quando si fa clic su **Opzioni avanzate**:

    Parametro|Descrizione
    ---|---
    Utilizzare Query nativa|Quando è selezionata, il driver ODBC non tenterà di convertire TSQL in HiveQL. Questa è usata solo se si è 100% che si siano inviando pure HiveQL istruzioni. Quando ci si connette al Database SQL Azure o SQL Server, è necessario lasciare tutte deselezionata.
    Righe inserite per blocco|Quando si recupera un numero elevato di record, ottimizzazione questo parametro potrebbe essere necessario garantire prestazioni ottimali.
    La lunghezza della colonna predefinita stringa, lunghezza della colonna binaria, scala di una colonna decimale|Il tipo di dati lunghezze e mobile possono influire come dati vengono restituiti. Provocano informazioni non corrette da restituire a causa di perdita di precisione e/o troncato.


    ![Opzioni avanzate][img-HiveOdbc-DataSource-AdvancedOptions]

6. Fare clic su **Test** per verificare l'origine dati. Quando l'origine dati è configurata correttamente, viene visualizzato *test completata!*.
7. Fare clic su **OK** per chiudere la finestra di dialogo di Test. La nuova origine dati a questo punto dovrebbe essere elencata all' **Amministratore origine dati ODBC**.
8. Fare clic su **OK** per chiudere la procedura guidata.

##<a name="import-data-into-excel-from-hdinsight"></a>Importare dati in Excel da HDInsight

La procedura indicata di seguito illustra il modo per importare dati da una tabella hive in una cartella di lavoro di Excel usando l'origine dati ODBC creata in precedenza.

1. Aprire una cartella di lavoro nuovo o esistente in Excel.
2. Nella scheda **dati** fare clic su **Da altre origini dati**e quindi fare clic su **Da connessione guidata dati** per avviare la **Connessione guidata dati**.

    ![Connessione guidata dati aperto][img-hdi-simbahiveodbc.excel.dataconnection]

3. Selezionare **L'errore DSN ODBC** come origine dati e quindi fare clic su **Avanti**.
4. Origini dati ODBC, selezionare il nome di origine dati creata nel passaggio precedente e quindi fare clic su **Avanti**.
5. Immettere nuovamente la password per il raggruppamento nella creazione guidata e quindi fare clic su **Test** per verificare la configurazione del nuovo, se necessario.
6. Fare clic su **OK** per chiudere la finestra di dialogo di test.
7. Fare clic su **OK**. Attendere che la finestra di dialogo **Seleziona Database e tabella** aprire. Possono essere necessari alcuni secondi.
8. Selezionare la tabella che si desidera importare e quindi fare clic su **Avanti**. *Hivesampletable* è un esempio di tabella hive fornito con i cluster HDInsight.  È possibile sceglierlo se sono stati creati uno. Per ulteriori informazioni sull'esecuzione Hive query e creare tabelle Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].
8. Fare clic su **Fine**.
9. Nella finestra di dialogo **Importa dati** , è possibile modificare o specificare la query. A tale scopo, fare clic su **proprietà**. Possono essere necessari alcuni secondi.
10. Fare clic sulla scheda **definizione** e quindi aggiungerlo **200 limite** per l'istruzione select Hive nella casella di **testo del comando** testo. La modifica, si limita il set di record restituito su 200.

    ![Proprietà connessione][img-hdi-simbahiveodbc-excel-connectionproperties]

11. Fare clic su **OK** per chiudere la finestra di dialogo Proprietà connessione.
12. Fare clic su **OK** per chiudere la finestra di dialogo **Importa dati** .  
13. Immettere nuovamente la password e quindi fare clic su **OK**. Bastano pochi secondi prima ottiene importare i dati in Excel.

##<a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come utilizzare il driver ODBC Hive Microsoft per recuperare dati da HDInsight Service in Excel. Analogamente, è possibile recuperare dati da HDInsight Service nel Database di SQL. È inoltre possibile caricare dati in un HDInsight Service. Per ulteriori informazioni, vedere:

- [Analizzare i dati dei ritardi volo utilizzando HDInsight][hdinsight-analyze-flight-data]
- [Caricare dati HDInsight][hdinsight-upload-data]
- [Utilizzare Sqoop con HDInsight] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png
