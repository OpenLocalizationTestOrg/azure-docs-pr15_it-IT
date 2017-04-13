<properties
    pageTitle="Esercitazione di Database SQL: creare un database SQL | Microsoft Azure"
    description="Informazioni su come configurare un server di Database SQL logico, regola firewall server, database SQL e i dati di esempio. Inoltre, informazioni su come connettersi con gli strumenti client, configurare gli utenti e configurare una regola firewall di database."
    keywords="esercitazione con database SQL di creare un database sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Esercitazione di Database SQL: creare un database SQL in minuti tramite il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

In questa esercitazione si imparerà a usare il portale di Azure per:

- Creare un database SQL Azure con dati di esempio.
- Creare una regola firewall a livello di server per un solo indirizzo IP o per un intervallo di indirizzi IP.

È possibile eseguire queste attività utilizzando [c#](sql-database-get-started-csharp.md) o [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Creare il primo database di SQL Azure 

1. Se non è attualmente connessi, connettersi al [portale di Azure](http://portal.azure.com).
2. Fare clic su **Nuovo**, fare clic su **dati + spazio di archiviazione**e quindi individuare **Il Database di SQL**.

    ![Nuovo database sql di 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Fare clic su **Database SQL** per aprire e il Database di SQL. Il contenuto di questo blade varia a seconda del numero delle sottoscrizioni e gli oggetti esistenti (ad esempio server esistenti).

    ![Nuovo database sql di 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Nella casella di testo **Nome Database** specificare un nome per il primo database, ad esempio "risorse del database". Un segno di spunta verde indica che è stato specificato un nome valido.

    ![Nuovo database sql di 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Se si hanno più abbonamenti, selezionare un abbonamento.
6. Nel **gruppo di risorse**, fare clic su **Crea nuovo** e immettere un nome per il primo gruppo di risorse, ad esempio "personale di gruppo risorse". Un segno di spunta verde indica che è stato specificato un nome valido.

    ![Nuovo database sql di 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. In **selezionare origine**, fare clic su **campione** e quindi su **AdventureWorksLT [V12]**nella casella **Selezionare campione** .

    ![Nuovo database sql di 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. In **Server**, selezionare **Configura impostazioni**.

    ![Nuovo database sql di 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. Scegliere **Crea un nuovo server**e il Server. Viene creato un database SQL Azure all'interno di un oggetto server, che può essere un nuovo server o un server esistente.

    ![Nuovo database sql di 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Esaminare e il **nuovo server** per comprendere le informazioni che necessarie per fornire per il nuovo server.

    ![Nuovo database sql di 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Nella casella di testo **nome del Server** , specificare un nome per il primo server, ad esempio "my-nuovo-server-object". Un segno di spunta verde indica che è stato specificato un nome valido.

    ![Nuovo database sql di 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. In **accesso amministrazione Server**, specificare un nome utente per l'account di amministratore per il server, ad esempio "personale di account amministratore". Questo accesso è noto come account di accesso principale del server. Un segno di spunta verde indica che è stato specificato un nome valido.

    ![Nuovo database sql di 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Nelle caselle **Password** e **Conferma password**, fornire una password per il server account di accesso principale, ad esempio "p@ssw0rd1". Un segno di spunta verde indica che sia stata specificata una password valida.

    ![Nuovo database sql di 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. In **posizione**, selezionare un data center adeguato per la posizione, ad esempio "Australia est".

    ![Nuovo database di sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. In * * server creare V12 (aggiornamento più recente), si noterà contenere solo l'opzione per creare una versione corrente di SQL Azure.

    ![Nuovo database sql di 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Si noti che, per impostazione predefinita, la casella di controllo **Consenti azure servizi per accedere a server** sia selezionata e non è possibile modificare. Si tratta di un'opzione avanzata. È possibile modificare questa impostazione nelle impostazioni del firewall server per l'oggetto server, anche se maggior parte dei casi non è necessario.

    ![Nuovo database sql di 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. Nella nuova pala di server, esaminare le opzioni desiderate e quindi fare clic su **Seleziona** per selezionare il nuovo server per il nuovo database.

    ![Nuovo database di sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. Nella e Database SQL, in **prezzi livello**, fare clic su **S2 Standard** e quindi fare clic su **base** per scegliere il livello di prezzo meno costoso per il primo database. È sempre possibile cambiare il livello prezzo in un secondo momento.

    ![Nuovo database di sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. Nella e Database SQL, esaminare le opzioni desiderate e quindi fare clic su **Crea** per creare il primo server e database. I valori immessi vengono convalidati e distribuzione inizia.

    ![Nuovo database di sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Sulla barra degli strumenti del portale, selezionare gli elementi di **notifiche** per controllare lo stato della distribuzione.

    ![Nuovo database di sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Al termine del processo di distribuzione, il nuovo Azure SQL server e database vengono creati in Azure. Non sarà possibile connettersi a un nuovo server o database usando gli strumenti di SQL Server finché non si crea una regola del firewall server per aprire il firewall di Database SQL per le connessioni da all'esterno di Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Passaggi successivi
Ora che è stata completata questa esercitazione di Database SQL e creato un database con alcuni dati di esempio, si è pronti a esplorare tramite strumenti personalizzati.

- Se si ha familiarità con Transact-SQL e SQL Server Management Studio (Express), informazioni su come [connettersi e query un database SQL con SQL Server Management Studio](sql-database-connect-query-ssms.md).

- Se si conosce Excel, informazioni su come [connettersi a un database SQL Azure con Excel](sql-database-connect-excel.md).

- Se è pronti per avviare la codifica, scegliere il linguaggio di programmazione in [raccolte di connessioni per Database SQL e SQL Server](sql-database-libraries.md).

- Se si desidera trasferire i database di SQL Server locale in Azure, vedere [la migrazione di un database al Database SQL](sql-database-cloud-migrate.md) per ottenere ulteriori informazioni.

- Se si desidera caricare alcuni dati in una nuova tabella da un file CSV utilizzando lo strumento della riga di comando BCP, vedere [il caricamento dei dati nel Database di SQL da un file CSV tramite BCP](sql-database-load-from-csv-with-bcp.md).

- Se si desidera iniziare a esplorare la protezione del Database di SQL Azure, vedere [Guida introduttiva alla sicurezza](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Risorse aggiuntive

[Che cos'è il Database di SQL?](sql-database-technical-overview.md)
