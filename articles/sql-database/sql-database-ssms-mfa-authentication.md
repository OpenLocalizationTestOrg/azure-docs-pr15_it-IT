<properties
   pageTitle="Supporto di SQL Server Management Studio per Azure Active Directory MFA con Database SQL e SQL Data Warehouse | Microsoft Azure"
   description="Utilizzare l'autenticazione inseriti più con SQL Server Management Studio per Database SQL e SQL Data Warehouse."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Supporto di SQL Server Management Studio per Azure Active Directory MFA con Database SQL e SQL Data Warehouse

Azure SQL Database e Azure SQL Data Warehouse supporta anche connessioni da SQL Server Management Studio (SQL Server Management Studio) utilizza *l'Autenticazione universale di Active Directory*. Autenticazione universale di Active Directory è un flusso di lavoro interattivi che supporta l' *Autenticazione a più fattori Azure* (MFA). Azure MFA consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Offre autenticazione avanzata con un intervallo delle opzioni di verifica semplice, ovvero telefonata, messaggio di testo e sulle smart card con pin o notifica di app per dispositivi mobili, consentendo agli utenti di scegliere il metodo preferito. Per una descrizione di autenticazione a più fattori, vedere [Autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication.md).

SQL Server Management Studio supporta ora:

- Interattiva MFA con Azure Active Directory con il potenziale per la convalida casella finestra di dialogo popup.
- Non interattivi Active Directory Password e autenticazione integrata di Active Directory metodi che possono essere usati in diverse applicazioni (ADO.NET, JDBC, ODBC e così via). Questi due metodi non risultato mai finestre di dialogo popup.

Quando è configurato l'account utente per MFA il flusso di lavoro di autenticazione interattiva richiede interazione di altro utente tramite finestre di dialogo popup, l'utilizzo di smart card e così via. Quando l'account utente è configurato per MFA, è necessario selezionare Azure universale autenticazione per la connessione. Se l'account utente non richiede MFA, l'utente può comunque usare altre due opzioni di autenticazione di Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitazioni relative all'autenticazione universale per Database SQL e SQL Data Warehouse

- SQL Server Management Studio è l'unico strumento attivata per MFA tramite autenticazione universale di Active Directory.
- Un singolo account Azure Active Directory può accedere per un'istanza di SQL Server Management Studio mediante l'autenticazione universale. Per eseguire l'accesso come un altro account Azure Active Directory, è necessario utilizzare un'altra istanza di SQL Server Management Studio. (Questa limitazione è limitata ai autenticazione universale di Active Directory, è possibile accedere a diversi server con autenticazione tramite Password di Active Directory, autenticazione integrata di Active Directory o autenticazione di SQL Server).
- SQL Server Management Studio supporta l'autenticazione universale di Active Directory per la visualizzazione di Esplora oggetti, dell'Editor di Query e archivio di Query.
- DacFx né progettazione Schema supportare l'autenticazione universale.
- Account MSA non sono supportati per l'autenticazione universale di Active Directory.
- Autenticazione universale di Active Directory non è supportata in SQL Server Management Studio per gli utenti che vengono importati corrente Active Directory da altri Azure Active Directory. Questi utenti non sono supportati, poiché richiede un ID tenant convalidare gli account e non è possibile specificare che.
- Non esistono requisiti software aggiuntivo per l'autenticazione universale di Active Directory ad eccezione del fatto che sia necessario utilizzare una versione supportata di SQL Server Management Studio.

## <a name="configuration-steps"></a>Passaggi di configurazione

Implementazione dell'autenticazione a più fattori richiede quattro passaggi di base.

1. **Configurare un Azure Active Directory** : per altre informazioni, vedere [integrazione le identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [aggiungere il proprio nome di dominio per Azure Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure ora supporta la federazione con Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [amministrazione directory Azure Active Directory](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [gestire Azure Active Directory mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurare MFA** – per istruzioni dettagliate, vedere [Configurazione di autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Database SQL di configurare o SQL Data Warehouse per l'autenticazione di Azure Active Directory** -per istruzioni dettagliate, vedere [connessione a Database SQL o dati Warehouse da con Azure Active Directory autenticazione di SQL](sql-database-aad-authentication.md).

4. **Scaricare SQL Server Management Studio** – computer client scaricare SQL Server Management Studio più recente (almeno 2016 agosto), da [Scaricare SQL Server Management Studio (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Connessione mediante l'autenticazione universale con SQL Server Management Studio

La procedura seguente viene illustrato come connettersi al Database SQL o SQL Data Warehouse tramite SQL Server Management Studio più recente.

1. Per connettersi mediante l'autenticazione universale, nella finestra di dialogo **connessione al Server** , selezionare **Autenticazione universale di Active Directory**.
![la connessione universal 1mfa][1]

2. Come di consueto per Database SQL e SQL Data Warehouse è necessario fare clic su **Opzioni** e specificare il database nella finestra di dialogo **Opzioni** . Fare clic su **Connetti**.
3. Quando viene visualizzata la finestra di dialogo **accesso al proprio account** , specificare l'account e la password dell'identità di Azure Active Directory.
![accesso in 2mfa][2]

    > [AZURE.NOTE] Per l'autenticazione universale con un account che non richiede MFA, è possibile connettersi a questo punto. Per gli utenti che richiedono MFA, continuare con la procedura seguente.
 
4. Due finestre di dialogo Imposta MFA potrebbero essere visualizzati. Questa volta operazione dipende dall'amministratore MFA impostazione e pertanto potrebbe essere facoltativo. Per un dominio MFA abilitato questo passaggio in alcuni casi è definito (ad esempio, il dominio richiede agli utenti di utilizzare una smart card e pin).  
![programma di installazione 3mfa][3]

5. Le possibili seconda una volta nella finestra di dialogo consente di selezionare i dettagli del metodo di autenticazione. Le opzioni possibili sono configurate dall'amministratore.
![4mfa-verifica-1][4]
 
6. Azure Active Directory invia le informazioni di conferma all'utente. Quando si riceve il codice di verifica, immetterlo nella casella **Immettere il codice verifica** e fare clic su **Accedi**.
![5mfa-verifica-2][5]

Una volta completata la verifica, SQL Server Management Studio connette presumendo che in genere l'accesso firewall o credenziali valide.

##<a name="next-steps"></a>Passaggi successivi  

Concedere l'accesso al database di altri utenti: [SQL Database di autenticazione e l'autorizzazione: concessione dell'accesso](sql-database-manage-logins.md)  
Assicurarsi che gli altri utenti possono connettersi attraverso il firewall: [configurare una regola firewall a livello di server di Database SQL Azure tramite il portale di Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

