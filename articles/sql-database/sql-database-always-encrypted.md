<properties
    pageTitle="Sempre crittografate: Proteggere i dati sensibili nel Database di SQL Azure con la crittografia del database | Microsoft Azure"
    description="Proteggere i dati sensibili nel database di SQL in minuti."
    keywords="crittografare dati, la crittografia di sql, la crittografia del database, i dati sensibili sempre crittografato"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/18/2016"
    ms.author="sstein"/>

# <a name="always-encrypted-protect-sensitive-data-in-sql-database-and-store-your-encryption-keys-in-the-windows-certificate-store"></a>Sempre crittografati: Proteggere i dati sensibili nel Database di SQL e archiviare le chiavi di crittografia nell'archivio certificati di Windows

> [AZURE.SELECTOR]
- [Archivio chiave Azure](sql-database-always-encrypted-azure-key-vault.md)
- [Archivio certificati di Windows](sql-database-always-encrypted.md)


In questo articolo viene illustrato come proteggere i dati riservati in un database SQL con la crittografia del database mediante la [Guidata sempre crittografati](https://msdn.microsoft.com/library/mt459280.aspx) in [SQL Server Management Studio (Express)](https://msdn.microsoft.com/library/hh213248.aspx). Viene inoltre descritto come archiviare le chiavi di crittografia nell'archivio certificati di Windows.

Sempre crittografia è una nuova tecnologia di crittografia di dati nel Database di SQL Azure e SQL Server che consente di proteggere i dati sensibili inattivi sul server, durante il movimento tra client e server e quando i dati in uso, verificare che i dati sensibili mai viene visualizzata come testo non crittografato all'interno del sistema di database. Dopo aver crittografato dati, solo le applicazioni client o server app che hanno accesso alle chiavi possono accedere dati in formato testo. Per informazioni dettagliate, vedere [Sempre crittografato (Database Engine)](https://msdn.microsoft.com/library/mt163865.aspx).

Dopo aver configurato il database per utilizzare sempre crittografate, si creerà un'applicazione client c# con Visual Studio per lavorare con i dati crittografati.

Seguire i passaggi descritti in questo articolo per informazioni su come configurare sempre crittografati per un database SQL Azure. In questo articolo viene spiegato eseguire le operazioni seguenti:

- Utilizzare la procedura guidata sempre crittografate in SQL Server Management Studio per creare [Sempre crittografati tasti](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
    - Creare una [Chiave Master di colonna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
    - Creare una [colonna chiave di crittografia (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
- Creare una tabella di database e crittografare le colonne.
- Creare un'applicazione che consente di inserire, seleziona e visualizza i dati dalle colonne crittografate.

## <a name="prerequisites"></a>Prerequisiti

Per questa esercitazione, è necessario:

- Un account Azure e sottoscrizione. Se non si ha uno, iscriversi a una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versione 13.0.700.242 o versione successiva.
- [.NET framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) o versione successiva (computer client).
- [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).



## <a name="create-a-blank-sql-database"></a>Creare un database SQL vuoto
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Nuovo** > **dati + spazio di archiviazione** > **Database SQL**.
3. Creare un database **vuoto** denominato **corso** su un server nuovo o esistente. Per istruzioni dettagliate sulla creazione di un database nel portale di Azure, vedere [creare un database SQL in minuti](sql-database-get-started.md).

    ![Creare un database vuoto](./media/sql-database-always-encrypted/create-database.png)

È necessario la stringa di connessione in un secondo momento nell'esercitazione. Dopo aver creato il database, passare al database di nuovo corso e copiare la stringa di connessione. È possibile ottenere la stringa di connessione in qualsiasi momento, ma è facile copiarlo quando si è nel portale di Azure.

1. Fare clic su **database SQL** > **corso** > **Mostra le stringhe di connessione di database**.
2. Copiare la stringa di connessione per **ADO.NET**.

    ![Copiare la stringa di connessione](./media/sql-database-always-encrypted/connection-strings.png)


## <a name="connect-to-the-database-with-ssms"></a>Connettersi al database con SQL Server Management Studio

Aprire SQL Server Management Studio e connettersi al server con il database del corso.


1. Aprire SQL Server Management Studio. (Fare clic su **Connetti** > **Motore di Database** per aprire la finestra di **connettersi al Server** se non è aperto).
2. Immettere il nome del server e le credenziali. Il nome del server sono disponibili nel e database SQL e nella stringa di connessione copiato in precedenza. Digitare il nome completo del server inclusi *database.windows.net*.

    ![Copiare la stringa di connessione](./media/sql-database-always-encrypted/ssms-connect.png)

Se viene visualizzata la finestra **Nuova regola Firewall** , accedere a Azure e consentire a SQL Server Management Studio di creare una nuova regola firewall dell'utente.


## <a name="create-a-table"></a>Creare una tabella

In questa sezione, è necessario creare una tabella per mettere in attesa dei dati. Questo sarà inizialmente: una tabella normale viene configurata la crittografia nella sezione successiva.

1. Espandere **database**.
1. Il pulsante destro del database **corso** e fare clic su **Nuova Query**.
2. Incollare il seguente Transact-SQL (Transact-SQL) la nuova finestra query ed **esecuzione** essa.


        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO


## <a name="encrypt-columns-configure-always-encrypted"></a>Crittografare le colonne (configurare sempre crittografate)

SQL Server Management Studio fornisce una procedura guidata per configurare con facilità sempre crittografato tramite la configurazione di colonne crittografate, CMK e CEK dell'utente.

1. Espandere **database** > **corso** > **tabelle**.
2. Pulsante destro del mouse nella tabella **pazienti** e selezionare **Crittografa colonne** per aprire la procedura guidata sempre crittografati:

    ![Crittografare le colonne](./media/sql-database-always-encrypted/encrypt-columns.png)

La procedura guidata sempre crittografate include le sezioni seguenti: **Selezione delle colonne**, **Configurazione di chiave Master** (CMK), **convalida**e **Riepilogo**.

### <a name="column-selection"></a>Selezione della colonna ###

Fare clic su **Avanti** nella pagina di **Introduzione** per aprire la pagina di **Selezione della colonna** . In questa pagina, selezionate le colonne che si desidera crittografare, [il tipo di crittografia e quali chiave di crittografia di colonna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) da utilizzare.

Crittografare le informazioni di **Previdenza** e **Data di nascita** per ogni attesa. La colonna **Previdenza** utilizzerà la crittografia deterministica, che supporta le ricerche di uguaglianza, join e raggruppamento. La colonna **Data di nascita** utilizzerà la crittografia casuale, che non supporta operazioni.

Impostare il **Tipo di crittografia** per la colonna **Previdenza** **deterministiche** e la colonna **Data di nascita** a **Randomized**. Fare clic su **Avanti**.

![Crittografare le colonne](./media/sql-database-always-encrypted/column-selection.png)

### <a name="master-key-configuration"></a>Configurazione di chiave master###

La pagina di **Configurazione della chiave Master** è nel punto in cui si imposta il CMK e selezionare il provider di archivio di chiavi in cui verrà archiviata la CMK. Attualmente, è possibile archiviare un CMK nell'archivio certificati di Windows Azure chiave archivio o un modulo sicurezza hardware (). In questa esercitazione viene illustrato come archiviare le chiavi nell'archivio certificati di Windows.

Verificare che sia selezionata **l'archivio certificati di Windows** e fare clic su **Avanti**.

![Configurazione di chiave master](./media/sql-database-always-encrypted/master-key-configuration.png)


### <a name="validation"></a>Convalida###

È possibile crittografare le colonne a questo punto o salvare uno script di PowerShell da eseguire in un secondo momento. Per questa esercitazione, selezionare **Continua per completare ora** e fare clic su **Avanti**.

### <a name="summary"></a>Riepilogo###

Verificare che le impostazioni siano tutti correggere e fare clic su **Fine** per completare la configurazione di sempre crittografato.

![Riepilogo](./media/sql-database-always-encrypted/summary.png)


### <a name="verify-the-wizards-actions"></a>Verificare le azioni della procedura guidata

Al termine della procedura, il database sia configurato per sempre crittografato. La procedura guidata eseguite le operazioni seguenti:

- Creare un CMK.
- Creare un CEK.
- Configurare le colonne selezionate per la crittografia. La tabella **pazienti** attualmente non contiene dati, ma tutti i dati esistenti nelle colonne selezionate sono ora crittografati.

È possibile verificare la creazione delle chiavi di SQL Server Management Studio facendo clic su **corso** > **protezione** > **Chiavi sempre crittografato**. È ora possibile visualizzare i tasti di nuovi la procedura guidata generata automaticamente.


## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Creare un'applicazione client che interagisce con i dati crittografati

Ora che sempre crittografati è impostato, è possibile creare un'applicazione che esegue *inserisce* e *Seleziona* colonne crittografato. Per eseguire l'applicazione di esempio, è necessario eseguirlo nello stesso computer in cui è stato eseguito la procedura guidata sempre crittografato. Per eseguire l'applicazione di un altro computer, è necessario distribuire i certificati sempre crittografati al computer che esegue l'applicazione client.  

> [AZURE.IMPORTANT] L'applicazione deve utilizzare gli oggetti [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) quando si passa a dati in formato testo al server con colonne sempre crittografato. Il passaggio di valori letterali senza utilizzare gli oggetti SqlParameter genereranno un'eccezione.


1. Aprire Visual Studio e creare una nuova applicazione console c#. Verificare che il progetto è impostato su **.NET Framework 4.6** o versione successiva.
2. Nome del progetto **AlwaysEncryptedConsoleApp** e fare clic su **OK**.

![Nuova applicazione console](./media/sql-database-always-encrypted/console-app.png)



## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificare la stringa di connessione per abilitare sempre crittografato

In questa sezione viene spiegato come abilitare sempre crittografato nella stringa di connessione di database. Verrà modificato l'app console che appena creata nella sezione successiva, "Sempre crittografate applicazione console di esempio".


Per abilitare sempre crittografati, è necessario aggiungere la parola chiave **Impostazione di crittografia di colonna** per la stringa di connessione e impostarla su **attivato**.

È possibile impostare questa direttamente nella stringa di connessione oppure è possibile impostare tramite un [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). L'applicazione di esempio nella sezione seguente viene illustrato come utilizzare **SqlConnectionStringBuilder**.

> [AZURE.NOTE] Si tratta la modifica sola nelle applicazioni client specifiche per sempre crittografato. Se si dispone di un'applicazione esistente che archivia la stringa di connessione esternamente (ovvero, in un file di configurazione), potrebbe essere possibile abilitare sempre crittografati senza modificare il codice.


### <a name="enable-always-encrypted-in-the-connection-string"></a>Abilitare sempre crittografato nella stringa di connessione

Aggiungere la parola chiave seguente la stringa di connessione:

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-a-sqlconnectionstringbuilder"></a>Abilita sempre crittografata con un SqlConnectionStringBuilder

Il codice seguente viene illustrato come abilitare sempre crittografate mediante l'impostazione [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) su [attivato](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;



## <a name="always-encrypted-sample-console-application"></a>Sempre Encrypted applicazione console di esempio

In questo esempio viene illustrato come:

- Modificare la stringa di connessione per abilitare sempre crittografato.
- Inserire dati nelle colonne crittografate.
- Selezionare un record da un filtro per un valore specifico in una colonna crittografata.

Sostituire il contenuto di **Program.cs** con il codice riportato di seguito. Sostituire la stringa di connessione per la variabile globale connectionString nella riga direttamente sopra il metodo Main con la stringa di connessione valida dal portale di Azure. Questo è l'unica modifica che è necessario apportare al codice.

Eseguire l'app per visualizzare sempre crittografati in azione.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;

    namespace AlwaysEncryptedConsoleApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"Replace with your connection string";

        static void Main(string[] args)
        {
            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient() {
                SSN = "999-99-0001", FirstName = "Orlando", LastName = "Gee", BirthDate = DateTime.Parse("01/04/1964") });
            InsertPatient(new Patient() {
                SSN = "999-99-0002", FirstName = "Keith", LastName = "Harris", BirthDate = DateTime.Parse("06/20/1977") });
            InsertPatient(new Patient() {
                SSN = "999-99-0003", FirstName = "Donna", LastName = "Carreras", BirthDate = DateTime.Parse("02/09/1973") });
            InsertPatient(new Patient() {
                SSN = "999-99-0004", FirstName = "Janet", LastName = "Gates", BirthDate = DateTime.Parse("08/31/1985") });
            InsertPatient(new Patient() {
                SSN = "999-99-0005", FirstName = "Lucy", LastName = "Harrington", BirthDate = DateTime.Parse("05/06/1993") });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now let's locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 123-45-6789):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
         VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }


## <a name="verify-that-the-data-is-encrypted"></a>Verificare che i dati vengono crittografati

È possibile verificare rapidamente che i dati effettivi nel server sono crittografati tramite una query di dati **pazienti** con SQL Server Management Studio. Utilizzare la connessione corrente in cui l'impostazione di crittografia di colonna non è ancora abilitata.

Eseguire la query seguente nel database del corso.

    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

È possibile visualizzare le colonne crittografate non contengono i dati di testo normale.

   ![Nuova applicazione console](./media/sql-database-always-encrypted/ssms-encrypted.png)


Per usare SQL Server Management Studio per accedere ai dati di testo normale, è possibile aggiungere il **impostazione di crittografia colonna = abilitato** parametro per la connessione.

1. In SQL Server Management Studio, mouse sul server in **Esplora oggetti**e quindi fare clic su **Disconnetti**.
2. Fare clic su **Connetti** > **Motore di Database** per aprire la finestra di **connettersi al Server** e quindi fare clic su **Opzioni**.
3. Fare clic su **Altri parametri di connessione** e digitare **impostazione di crittografia colonna = abilitato**.

    ![Nuova applicazione console](./media/sql-database-always-encrypted/ssms-connection-parameter.png)

4. Eseguire la query seguente nel database **corso** .

        SELECT FirstName, LastName, SSN, BirthDate FROM Patients;

     È ora possibile visualizzare i dati in formato testo in colonne crittografate.


    ![Nuova applicazione console](./media/sql-database-always-encrypted/ssms-plaintext.png)



> [AZURE.NOTE] Se ci si connette con SQL Server Management Studio (o tutti i client) da un altro computer, non avranno accesso alle chiavi di crittografia e non saranno in grado di decrittografare i dati.



## <a name="next-steps"></a>Passaggi successivi
Dopo aver creato un database che utilizza sempre crittografati, è consigliabile eseguire le operazioni seguenti:

- Eseguire l'esempio da un altro computer. Esso non è possibile disporre le chiavi di crittografia, in modo che non avranno accesso ai dati in testo normale e non verrà eseguita correttamente.
- [Ruota e pulire le chiavi](https://msdn.microsoft.com/library/mt607048.aspx).
- [Eseguire la migrazione dati che sono già crittografati con sempre crittografato](https://msdn.microsoft.com/library/mt621539.aspx).
- [Distribuire sempre crittografate certificati per gli altri computer client](https://msdn.microsoft.com/library/mt723359.aspx#Anchor_1) (vedere la sezione "Effettua certificati disponibili per le applicazioni e gli utenti").

## <a name="related-information"></a>Informazioni correlate

- [Sempre crittografate (sviluppo client)](https://msdn.microsoft.com/library/mt147923.aspx)
- [Crittografia dati trasparente](https://msdn.microsoft.com/library/bb934049.aspx)
- [Crittografia di SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
- [Creazione guidata sempre crittografato](https://msdn.microsoft.com/library/mt459280.aspx)
- [Blog sempre crittografato](http://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)
