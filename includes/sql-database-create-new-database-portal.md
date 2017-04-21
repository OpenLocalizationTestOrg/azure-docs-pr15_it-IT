
<!--
includes/sql-database-create-new-database-portal.md

Latest Freshness check:  2016-04-11 , carlrab.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started-tutorial.md

-->
## <a name="create-a-new-azure-sql-database"></a>Creare un nuovo database di SQL Azure

Utilizzare la procedura seguente nel portale di Azure per creare un nuovo database SQL Azure su un server logico Database SQL Azure nuovo o esistente.

1. Se non è attualmente connessi, connettersi al [portale di Azure](http://portal.azure.com).
2. Fare clic su **Nuovo**, digitare **Database SQL**e quindi fare clic su **Database SQL (database nuovo)**.

     ![Nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-1.png)

3. Fare clic su **Database SQL (database nuovo)**.

     ![Nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-2.png)

4. Fare clic su **Crea** per creare un nuovo database nel servizio di Database SQL.

     ![Nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-3.png)

5. Fornire i valori della proprietà del server seguenti:

 - Nome del database
 - Abbonamento: Si applica solo se si hanno più abbonamenti.
 - Gruppo risorse: se si sta ancora, usare il gruppo di risorse del server logico.
 - Seleziona origine: È possibile scegliere un database vuoto, i dati di esempio o un backup del database Azure. Per eseguire la migrazione di un database di SQL Server locale o caricare i dati utilizzando lo strumento della riga di comando BCP, vedere i collegamenti alla fine di questo articolo.
 - Server: Nuovi o esistenti server logico.
 - Accesso Amministrazione server
 - Password
 - Livello prezzo: se si sta ancora, usare il valore predefinito S0.
 - Regole di confronto: Si applica solo se è stato scelto un database vuoto.

        ![New database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-4.png)

6.  Fare clic su **Crea**. Nell'area di notifica, è possibile vedere che si è iniziata la distribuzione.

     ![Nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-5.png)

7. Attendere per la distribuzione completare prima di continuare con il passaggio successivo.

     ![Nuovo database](./media/sql-database-create-new-database-portal/sql-database-create-new-database-portal-6.png)
