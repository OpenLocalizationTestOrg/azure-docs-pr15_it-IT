<properties
    pageTitle="Connettersi al Database SQL tramite Python | Microsoft Azure"
    description="Presenta un esempio di codice Python che è possibile utilizzare per connettersi al Database SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="drivers"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="meetb"/>


# <a name="connect-to-sql-database-by-using-python"></a>Connettersi al Database SQL tramite Python


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)] 


In questo argomento viene illustrato come connettersi ed eseguire query di un Database di SQL Azure con Python. In questo esempio è possibile eseguire da piattaforme di Windows, Ubuntu Linux o Mac.


## <a name="step-1-create-a-sql-database"></a>Passaggio 1: Creare un database SQL

Vedere la [pagina Introduzione a](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante che seguire la Guida per creare un **modello di database AdventureWorks**. Negli esempi viene illustrati di seguito funzionano solo con lo **schema di AdventureWorks**. Dopo aver creato la creazione di database che si abilita l'accesso al proprio indirizzo IP abilitando le regole del firewall come descritto nella [pagina Introduzione a](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Passaggio 2: Configurare l'ambiente di sviluppo

### <a name="mac-os"></a>**Mac OS**   
### <a name="install-the-required-modules"></a>Installare i moduli necessari
Aprire il terminale e installare

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql=2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Aprire il terminale e passare a una directory in cui si intende creare il python script. Immettere i comandi seguenti per installare **FreeTDS** e **pymssql**. pymssql utilizza FreeTDS per connettersi al database SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql=2.1.1
    
### <a name="windows"></a>**Windows**

Installare pymssql da [**qui**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Verificare che si sceglie file whl corretto. Ad esempio: scegliere se si utilizza Python 2.7 in un computer a 64 bit: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Dopo avere scaricato il file .whl inserirlo nella cartella c: / Python27.

A questo punto installarlo pymssql utilizzando pip dalla riga di comando. CD in c: / Python27 ed eseguire le operazioni seguenti
    
    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Sono disponibili istruzioni per l'attivazione pip usare [qui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## <a name="step-3-run-sample-code"></a>Passaggio 3: Eseguire il codice di esempio

Creare un file denominato **sql_sample.py** e incollare il codice seguente all'interno. È possibile eseguire questa operazione dalla riga di comando mediante:
    
    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Connettersi al Database di SQL

La funzione [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) viene utilizzata per connettersi al Database SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Eseguire un'istruzione SQL SELECT

La funzione [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) può essere utilizzata per recuperare un set di risultati di una query di database SQL. Questa funzione essenzialmente accetta tutte le query e restituisce che un set di risultati che possono essere scorso mediante l'utilizzo di [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])   
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserire una riga, passare i parametri e recuperare la chiave primaria generata

Nel Database di SQL è possibile utilizzare la proprietà di [identità](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [sequenza](https://msdn.microsoft.com/library/ff878058.aspx) per generare automaticamente valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx) . 


    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transazioni


In questo esempio viene illustrato l'utilizzo delle transazioni in cui è:

* Avviare una transazione
* Inserire una riga di dati
* Ripristino di annullare l'inserimento della transazione 

Incollare il codice seguente sql_sample.py.
    
    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Passaggi successivi

* Esaminare la [Panoramica di sviluppo di Database SQL](sql-database-develop-overview.md)
* Ulteriori informazioni su [Microsoft Python Driver di SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visitare il [Centro per sviluppatori di Python](/develop/python/).

## <a name="additional-resources"></a>Risorse aggiuntive 

* [Modelli di progettazione per applicazioni multi-tenant SaaS con Database SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità di Database SQL](https://azure.microsoft.com/services/sql-database/)
