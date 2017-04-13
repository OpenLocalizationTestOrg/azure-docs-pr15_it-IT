<properties
   pageTitle="Connettersi al Database SQL di o SQL Data Warehouse mediante l'autenticazione di Azure Active Directory | Microsoft Azure"
   description="Informazioni su come connettersi al Database SQL tramite autenticazione di Azure Active Directory."
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
   ms.date="09/16/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="connecting-to-sql-database-or-sql-data-warehouse-by-using-azure-active-directory-authentication"></a>Connessione al Database SQL di o SQL Data Warehouse tramite autenticazione Azure Active Directory

Autenticazione di Azure Active Directory è un meccanismo di connessione al Database SQL di Microsoft Azure e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) tramite identità di Azure Active Directory (Azure Active Directory). Con l'autenticazione di Azure Active Directory, è possibile gestire centralmente le identità degli utenti del database e altri servizi Microsoft in una posizione centrale. Gestione centrale ID offre una posizione centralizzata per gestire gli utenti del database e semplifica la gestione delle autorizzazioni. I seguenti vantaggi:

- Viene fornita un'alternativa per l'autenticazione di SQL Server.
- Consente di interrompere la proliferazione di identità utente tra server di database.
- Consente la rotazione di password in un'unica posizione
- I clienti possono gestire le autorizzazioni di database mediante gruppi esterni (AAD).
- Perché consente di eliminare le password archiviazione abilitando l'autenticazione di Windows e altre forme di autenticazione supportati da Azure Active Directory.
- Azure Active Directory autenticazione ottimali contenuti database agli utenti di eseguire l'autenticazione delle identità al livello del database.
- Azure Active Directory supporta l'autenticazione basata su token per la connessione al Database SQL di applicazioni.
- Autenticazione di Azure Active Directory supporta ADFS (federazione di domini) o l'autenticazione utente/password nativo per una locali Azure Active Directory senza la sincronizzazione dei domini.  
- Azure Active Directory supporta le connessioni da SQL Server Management Studio utilizzate autenticazione universale di Active Directory, che include l'autenticazione a più fattori (MFA).  MFA include autenticazione avanzata con un intervallo delle opzioni di verifica semplice, ovvero telefonata, messaggio di testo e sulle smart card con pin o notifica di app per dispositivi mobili. Per ulteriori informazioni, vedere [supporto di SQL Server Management Studio per Azure Active Directory MFA con Database SQL e SQL Data Warehouse](sql-database-ssms-mfa-authentication.md).

I passaggi di configurazione includono le procedure seguenti per configurare l'autenticazione di Azure Active Directory.

1. Creare e inserire un Azure Active Directory.
2. Assicurarsi che il database sia in V12 di Database SQL Azure. Questa non è necessaria per SQL Data Warehouse.
3. Facoltativo: Associare o modificare active directory non è attualmente associata all'abbonamento Azure.
4. Creare un amministratore di Azure Active Directory per Azure SQL Server o [Data Warehouse di SQL Azure](https://azure.microsoft.com/services/sql-data-warehouse/).
5. Configurare i computer client.
6. Creare gli utenti del database di contenuti nel database mappato alle identità di Azure Active Directory.
7. Connettersi al database utilizzando le identità di Azure Active Directory.


## <a name="trust-architecture"></a>Architettura di protezione

Nel diagramma di alto livello seguente riepiloga l'architettura di soluzione di mediante l'autenticazione di Azure Active Directory con Database di SQL Azure. Gli stessi concetti si applicano a SQL Data Warehouse. Per supportare le password utente nativa di Azure Active Directory, viene considerato solo la parte Cloud e i Database di SQL Azure Active Directory/Azure. Per supportare l'autenticazione federato (o utente e la password per le credenziali di Windows), è necessaria la comunicazione con ADFS blocco. Le frecce indicano percorsi di comunicazione.

![aAd auth diagramma][1]

Nel diagramma seguente indica la federazione, protezione e l'hosting relazioni che consentono un client di connettersi a un database mediante l'invio di un token. Il token autenticato da un annuncio Azure e attendibili dal database. Il cliente 1 può rappresentare un Azure Active Directory con utenti nativi o un server di Azure Active Directory con utenti federati. Il cliente 2 rappresenta una possibile soluzione, inclusi gli utenti importati; In questo esempio provenienti da un federati Azure Active Directory con ADFS sincronizzata con Azure Active Directory. È importante tenere presente che l'accesso a un database mediante l'autenticazione di Azure Active Directory è necessario che l'abbonamento hosting è associato a Azure Active Directory. Lo stesso abbonamento deve essere utilizzato per creare di SQL Server che ospita i Database SQL Azure o SQL Data Warehouse.

![relazione abbonamento][2]

## <a name="administrator-structure"></a>Struttura di amministratore

Quando si utilizza l'autenticazione di Azure Active Directory, sono disponibili due account di amministratore per il server di Database SQL. l'amministratore di SQL Server originale e l'amministratore di Azure Active Directory. Gli stessi concetti applicano nell'archivio di dati SQL. Solo l'amministratore in base a un account Azure Active Directory è possibile creare il primo utente database Azure Active Directory contenute in un database di utente. L'account di accesso di amministratore di Azure Active Directory può essere un utente di Azure Active Directory o un gruppo di Azure Active Directory. Quando l'amministratore è un account di gruppo, può essere utilizzato da qualsiasi membro del gruppo, che consente agli amministratori di Azure Active Directory più per l'istanza di SQL Server. L'utilizzo di account di gruppo come amministratore migliora la gestibilità consentendo di aggiungere e rimuovere i membri del gruppo in Active Directory Azure senza modificare gli utenti o le autorizzazioni nel Database di SQL in modo centralizzato. Solo un amministratore di Azure Active Directory (un utente o gruppo) può essere configurato in qualsiasi momento.

![struttura di amministrazione][3]

## <a name="permissions"></a>Autorizzazioni

Per creare nuovi utenti, è necessario disporre di `ALTER ANY USER` autorizzazione del database. il `ALTER ANY USER` autorizzazione a qualsiasi utente del database. Il `ALTER ANY USER` autorizzazione viene mantenuto anche gli account degli amministratori di server e gli utenti del database con la `CONTROL ON DATABASE` o `ALTER ON DATABASE` l'autorizzazione per il database e dai membri della `db_owner` ruolo del database.

Per creare un utente del database contenuti nel Database di SQL Azure o SQL Data Warehouse, è necessario connettersi al database mediante un'identità di Azure Active Directory. Per creare il primo utente database indipendente, è necessario connettersi al database tramite un amministratore di Azure Active Directory (che è il proprietario del database). Come illustrato nei passaggi 4 e 5 riportata di seguito. Qualsiasi tipo di autenticazione Azure Active Directory è possibile solo se l'amministratore di Azure Active Directory è stato creato per il Database di SQL Azure o Warehouse di dati di SQL server. Se l'amministratore di Azure Active Directory è stato rimosso dal server, agli utenti di Azure Active Directory creati in precedenza all'interno di SQL Server non più possono connettersi al database utilizzando le proprie credenziali di Azure Active Directory.

## <a name="azure-ad-features-and-limitations"></a>Limitazioni e le funzionalità di azure Active Directory

In Server SQL Azure o SQL Data Warehouse, è possono eseguire il provisioning dei seguenti membri di Azure Active Directory:

- Membri nativi: un membro creato in Azure Active Directory nel dominio gestito o in un dominio cliente. Per ulteriori informazioni, vedere [aggiungere il proprio nome di dominio per Azure Active Directory](../active-directory/active-directory-add-domain.md).

- Membri di un dominio federato: un membro creato in Azure Active Directory con un dominio federato. Per ulteriori informazioni, vedere [Microsoft Azure ora supporta la federazione con Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/).

- Membri importati da altri Azure Active Directory che appartengono dominio nativo o federati.

- Gruppi di Active Directory creati gruppi di sicurezza.

Account Microsoft (ad esempio outlook.com, hotmail.com, live.com) o ad altri account guest (ad esempio gmail.com, yahoo.com) non sono supportate. Se è possibile accedere a [https://login.live.com](https://login.live.com) usando l'account e la password, quindi si utilizza un account Microsoft, non è supportato per l'autenticazione di Azure Active Directory per Database SQL Azure o Warehouse dati di SQL Azure.

### <a name="additional-considerations"></a>Altre considerazioni

- Per migliorare la gestibilità, si consiglia di che provisioning di un gruppo di Azure Active Directory dedicato come amministratore.
- Solo un amministratore di Azure Active Directory (un utente o gruppo) può essere configurato per un Server SQL Azure o Warehouse dati di SQL Azure in qualsiasi momento.
- Solo un amministratore di Azure Active Directory per SQL Server inizialmente è possibile connettersi al Server di SQL Azure o Warehouse dati di SQL Azure con un account Azure Active Directory. Amministratore di Active Directory è possibile configurare gli utenti di database successivi Azure Active Directory.
- È consigliabile impostare il timeout della connessione a 30 secondi.
- SQL Server 2016 Management Studio e SQL Server Data Tools per Visual Studio 2015 (versione 14.0.60311.1April 2016 o versioni successive) supportare l'autenticazione di Azure Active Directory. (L'autenticazione di azure Active Directory è supportato dal **Provider di dati .NET Framework per SQL Server**; nella versione minima .NET Framework 4.6). Pertanto le versioni più recenti di questi strumenti e applicazioni di livello di dati (connessione amministrativa Dedicata e .bacpac) è possono utilizzare l'autenticazione di Azure Active Directory.
- [Versione ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339) supporta l'autenticazione di Azure Active Directory tuttavia `bcp.exe` non riesce a connettersi mediante l'autenticazione di Azure Active Directory perché utilizza un provider ODBC meno recente.
- `sqlcmd`supporta l'inizio di autenticazione di Azure Active Directory con versione 13.1 disponibile dall' [Area Download](http://go.microsoft.com/fwlink/?LinkID=825643).  
- SQL Server Data Tools per Visual Studio 2015 richiede almeno la versione di aprile 2016 degli strumenti di dati (versione 14.0.60311.1). Attualmente gli utenti di Azure Active Directory non vengono visualizzati in Esplora oggetti SSDT. In alternativa, visualizzare gli utenti in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
- [Microsoft JDBC Driver 6.0 per SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=11774) supporta l'autenticazione di Azure Active Directory. Vedere anche [le proprietà di connessione](https://msdn.microsoft.com/library/ms378988.aspx).
- PolyBase non è possibile eseguire l'autenticazione mediante l'autenticazione di Azure Active Directory.
- Alcuni strumenti come BI e in Excel non sono supportate.
- Autenticazione di Azure Active Directory è supportata per Database SQL Azure sollevamento di **Database di importazione** ed **Esporta Database** portale. Importazione / esportazione mediante l'autenticazione di Azure Active Directory è supportata anche rispetto al comando di PowerShell.


## <a name="1-create-and-populate-an-azure-ad"></a>1. creare e inserire un annuncio Azure

Creare un Azure Active Directory e inserirvi gli utenti e gruppi. Azure Active Directory può essere dominio gestito dominio iniziale Azure Active Directory. Azure Active Directory può essere anche un locale servizi di dominio federato con Azure Active Directory.

Per ulteriori informazioni, vedere [integrazione le identità locali con Azure Active Directory](../active-directory/active-directory-aadconnect.md), [aggiungere il proprio nome di dominio per Azure Active Directory](../active-directory/active-directory-add-domain.md), [Microsoft Azure ora supporta la federazione con Windows Server Active Directory](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [amministrazione directory Azure Active Directory](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [gestire Azure Active Directory mediante Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

## <a name="2-ensure-your-sql-database-is-version-12"></a>2. verificare il Database di SQL versione 12

Autenticazione di Azure Active Directory è supportata in ultima V12 di Database di SQL. Per informazioni su SQL Database V12 e per scoprire se è disponibile nella propria area geografica, vedere [che cos'è la più recente V12 di aggiornamento di Database SQL di](sql-database-v12-whats-new.md). Questo passaggio non è necessario per Azure SQL Data Warehouse perché SQL Data Warehouse è disponibile solo in V12.

Se si dispone di un database esistente, verificare che è ospitato in SQL Database V12 connettendosi al database (ad esempio con SQL Server Management Studio) e l'esecuzione di `SELECT @@VERSION;`. L'output previsto per un database SQL Database V12 è presente almeno **Microsoft SQL Azure RTM - 12.0**. Se il database non è ospitato su SQL Database V12, vedere [pianificare e preparare l'aggiornamento a SQL Database V12](sql-database-v12-plan-prepare-upgrade.md), quindi visitare il portale classica Azure per eseguire la migrazione del database di SQL Database V12.

In alternativa, è possibile creare un nuovo database in SQL Database V12 seguendo i passaggi elencati in [creare il primo database di SQL Azure](sql-database-get-started.md). **Suggerimento**: leggere il passaggio successivo prima di selezionare un abbonamento per il nuovo database.

## <a name="3-optional-associate-or-change-the-active-directory-that-is-currently-associated-with-your-azure-subscription"></a>3. facoltativo: Associare o modifica di active directory non è attualmente associata all'abbonamento Azure

Per associare il database nella directory di Azure Active Directory per l'organizzazione, verificare directory di una directory attendibile per l'abbonamento Azure hosting del database. Per ulteriori informazioni, vedere [come Azure sottoscrizioni associate Azure Active Directory](https://msdn.microsoft.com/library/azure/dn629581.aspx).

**Informazioni aggiuntive:** Ogni abbonamento Azure ha una relazione di attendibilità con un'istanza di Azure Active Directory. Di conseguenza, considerare attendibile tale directory per l'autenticazione di utenti, servizi e dispositivi. Più abbonamenti affidabili stessa directory, ma solo una directory e trust di un abbonamento. È possibile vedere quali directory è considerato attendibile dai abbonamento nella scheda **Impostazioni** in [https://manage.windowsazure.com/](https://manage.windowsazure.com/). Questa relazione di attendibilità che dispone di un abbonamento con una directory è diversamente da quanto succede la relazione che dispone di un abbonamento con tutte le altre risorse in Azure (siti Web, database e così via), che sono più simili risorse figlio di un abbonamento. Se un abbonamento scade, quindi accesso alle altre risorse associato all'abbonamento si arresta anche. Nella directory rimane in Azure, ma è possibile associare un'altra sottoscrizione a tale directory e continuare a gestire gli utenti di directory. Per ulteriori informazioni sulle risorse, vedere [accesso alle risorse Understanding in Azure](https://msdn.microsoft.com/library/azure/dn584083.aspx).

Nelle procedure riportate di seguito le istruzioni dettagliate su come modificare la directory associata per una sottoscrizione specificata.

1. Connettere il [Portale classica Azure](https://manage.windowsazure.com/) usando un amministratore di abbonamento Azure.
2. Nell'intestazione sinistra, selezionare **Impostazioni**.
3. Le sottoscrizioni vengono visualizzati nella schermata Impostazioni. Se l'abbonamento desiderato non è visualizzato, fare clic su **abbonamenti** nella parte superiore, elenco a discesa **Filtro dalla DIRECTORY** e selezionare la directory che contiene le sottoscrizioni e quindi fare clic su **Applica**.

    ![Selezionare l'abbonamento][4]
4. Nell'area **Impostazioni** , fare clic su abbonamento e quindi fare clic su **Modifica DIRECTORY** nella parte inferiore della pagina.

    ![portale di impostazioni di Active Directory][5]
5. Nella casella **Modifica DIRECTORY** selezionare Azure Active Directory non è associato a SQL Server o SQL Data Warehouse e quindi fare clic sulla freccia avanti.

    ![selezione di directory modifica][6]
6. Nella finestra di **Conferma** directory Mapping finestra di dialogo, verificare che "**verranno rimossi tutti gli CO-amministratori di.**"

    ![Modifica-directory confirm][7]
7. Fare clic su Controlla per ricaricare il portale.

> [AZURE.NOTE] Quando si modifica la directory, accedere a tutti i coamministratori, gli utenti di Azure Active Directory e gruppi e gli utenti delle risorse di supporto di directory vengono rimossi e non abbia più accesso per la sottoscrizione e le relative risorse. Solo l'utente, come un amministratore del servizio, possono configurare l'accesso per le identità in base alla nuova directory. Questa modifica può richiedere una notevole quantità di tempo propagazione delle modifiche apportate a tutte le risorse. Modifica della directory, anche cambia l'amministratore di Azure Active Directory per Database SQL e SQL Data Warehouse ed è impedire l'accesso al database per tutti gli attuali utenti Azure Active Directory. L'amministratore di Azure Active Directory deve essere Reimposta (come descritto di seguito) e Azure nuovi utenti di Active Directory devono essere creati.

## <a name="4-create-an-azure-ad-administrator-for-azure-sql-server"></a>4. creare un amministratore di Azure Active Directory per SQL Azure

Ogni Azure SQL Server (che ospita un Database SQL o SQL Data Warehouse) inizia con un account di amministratore solo server che è l'amministratore del Server di SQL Azure intera. Un amministratore di SQL Server secondo deve essere creato, che è un account Azure Active Directory. Questo principale viene creato come utente del database contenuti nel database master. Come amministratori, gli account di amministratore del server sono membri del ruolo **db_owner** in tutti i database utente e immettere ciascun database utente come utente **dbo** . Per ulteriori informazioni sugli account di amministratore del server, vedere [gestione dei database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md) e la sezione **utenti e gli account di accesso** di [indicazioni sulla sicurezza di Database SQL Azure e limitazioni](sql-database-security-guidelines.md).

Quando si usa Azure Active Directory con replica geografico, l'amministratore di Azure Active Directory deve essere configurato per primario e secondario server. Se un server non dispone di un amministratore di Azure Active Directory, quindi accessi di Azure Active Directory e gli utenti riceveranno un "non è possibile connettersi" Errore del server.

> [AZURE.NOTE] Gli utenti che non sono basati su un account di Azure Active Directory (tra cui l'account di amministratore di Azure SQL Server), non è possibile creare Azure Active Directory utenti, poiché non dispongono dell'autorizzazione per convalidare gli utenti di database proposta con Azure Active Directory.

### <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-server-by-using-the-azure-portal"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per SQL Server Azure tramite il portale di Azure

1. Nel [portale di Azure](https://portal.azure.com/), nell'angolo superiore destro fare clic sulla connessione a un elenco delle possibili Active Directory a discesa. Scegliere corretto Active Directory come predefinito Azure Active Directory. Questo passaggio collega associazione sottoscrizione con Active Directory con Server di SQL Azure assicurandosi che lo stesso abbonamento viene utilizzato per entrambe Azure Active Directory e SQL Server. (Possibile ospitare Azure SQL Server Database SQL Azure o Data Warehouse di SQL Azure.)

    ![Scegliere Active Directory][8]
2. Nell'intestazione sinistra selezionare **SQL Server**, selezionare di **SQL server**e quindi in e il **Server SQL** nella parte superiore fare clic su **Impostazioni**.

    ![impostazioni di Active Directory][9]
3. In e **l'Impostazioni** , fare clic su * * amministratore di Active Directory.
4. In e **l'amministratore di Active Directory** , fare clic su **amministratore di Active Directory**e quindi, nella parte superiore, fare clic su **Imposta amministratore**.
5. In e il **componente admin** , cercare un utente, selezionare l'utente o gruppo di amministratore e quindi fare clic su **Seleziona**. (E l'amministrazione Active Directory Mostra tutti i membri e i gruppi di Active Directory. Impossibile selezionare utenti o gruppi che non sono disponibili perché non è supportati come amministratori di Azure Active Directory. (Vedere l'elenco di amministratori supportati in **Azure Active Directory funzionalità e limitazioni** sopra). Controllo dell'accesso basato sui ruoli (RBAC) si applica solo al portale e non viene propagato per SQL Server.
6. Nella parte superiore e **l'amministratore di Active Directory** , fare clic su **Salva**.
    ![Scegliere amministratore][10]

    Il processo di modifica l'amministratore può richiedere alcuni minuti. Nuovo amministratore verrà visualizzata nella finestra di **amministrazione di Active Directory** .

> [AZURE.NOTE] Quando si configura l'amministratore di Azure Active Directory, il nuovo nome di amministratore (utente o gruppo) non può essere già presente nel database master virtuale a un utente di autenticazione di SQL Server. Se presente, la configurazione di amministrazione di Azure Active Directory avrà esito negativo; ripristino dello stato precedente la creazione e che indica che tali amministratori (nome) già esistente. Poiché ad esempio un utente di autenticazione di SQL Server non fa parte di Azure Active Directory, qualsiasi tentativo di connettersi al server mediante l'autenticazione di Azure Active Directory ha esito negativo.

In un secondo momento rimuovere un amministratore, nella parte superiore della stessa e **amministratore di Active Directory** , fare clic su **Rimuovi amministrazione**e quindi fare clic su **Salva**.

### <a name="provision-an-azure-ad-administrator-for-azure-sql-server-by-using-powershell"></a>Effettuare il provisioning di un amministratore di Azure Active Directory per Azure SQL Server tramite PowerShell

Per eseguire i cmdlet di PowerShell, è necessario disporre di PowerShell Azure installato e in esecuzione. Per informazioni dettagliate, vedere [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Per eseguire il provisioning di un amministratore di Azure Active Directory, eseguire i comandi di PowerShell Azure seguenti:

- Aggiungere AzureRmAccount
- Selezionare AzureRmSubscription


Cmdlet usati per effettuare il provisioning e gestire Azure AD amministratore:

| Nome cmdlet                                       | Descrizione                                                                                                     |
|---------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| [Set AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603544.aspx)    | Esegue il provisioning di un amministratore di Azure Active Directory per Azure SQL Server o Data Warehouse di SQL Azure. (Deve essere abbonamento corrente). |
| [Rimuovi AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt619340.aspx) | Rimuove un amministratore di Azure Active Directory per Azure SQL Server o Data Warehouse di SQL Azure. |
| [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://msdn.microsoft.com/library/azure/mt603737.aspx)    | Restituisce informazioni sull'amministratore Azure Active Directory è attualmente configurato per il Server di SQL Azure o Data Warehouse di SQL Azure. |

Usare PowerShell comando get-help per visualizzare ulteriori dettagli per ciascuno di questi comandi, ad esempio ``get-help Set-AzureRmSqlServerActiveDirectoryAdministrator``.

Le seguenti disposizioni script un gruppo di amministratori di Azure Active Directory denominata **DBA_Group** (id oggetto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) per **demo_server** server in un gruppo di risorse denominato **23 gruppo**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group"
```

Il parametro di input **DisplayName** accetta il nome visualizzato di Azure Active Directory o il nome dell'entità utente. Ad esempio ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Per i gruppi di Azure Active Directory di Azure AD nome visualizzato è supportato.

> [AZURE.NOTE] Il comando PowerShell Azure ```Set-AzureRmSqlServerActiveDirectoryAdministrator``` non si impedisce il provisioning di amministratori di Azure Active Directory per gli utenti non supportati. Un utente non supportato da destinare, ma non è possibile connettersi a un database. (Vedere l'elenco di amministratori supportati in **Azure Active Directory funzionalità e limitazioni** sopra).

Nell'esempio seguente viene utilizzata facoltativo **ObjectID**:

```
Set-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23"
–ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [AZURE.NOTE] Azure Active Directory **ObjectID** è obbligatorio quando **DisplayName** non sia univoco. Per recuperare i valori **ObjectID** e **DisplayName** , usare la sezione Active Directory del portale classica Azure e visualizzare le proprietà di un utente o gruppo.

Nell'esempio seguente restituisce informazioni relative al corrente amministratore di Azure Active Directory per Azure SQL Server:

```
Get-AzureRmSqlServerActiveDirectoryAdministrator –ResourceGroupName "Group-23" –ServerName "demo_server" | Format-List
```

Nell'esempio seguente consente di rimuovere un amministratore di Azure Active Directory:
```
Remove-AzureRmSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" –ServerName "demo_server"
```

È anche possibile eseguire il provisioning un amministratore di Azure Active Directory utilizzando le API REST. Per ulteriori informazioni, vedere [riferimento API REST al servizio Gestione e operazioni per le operazioni di database SQL Azure per i database di SQL Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx)

## <a name="5-configure-your-client-computers"></a>5. configurare i computer client

In tutti i computer client, da cui le applicazioni o gli utenti connetteranno al Database SQL Azure o Warehouse dati di SQL Azure tramite identità di Azure Active Directory, è necessario installare il software seguente:

- .NET framework 4.6 o versione successiva [dall'https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- Azure Active Directory Authentication Library per SQL Server (**ADALSQL. DLL**) è disponibile in più lingue (x86 e amd64) dall'area download in [Microsoft Active Directory Authentication Library per Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=48742).

### <a name="tools"></a>Strumenti

- L'installazione di [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o [SQL Server Data Tools per Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) soddisfa i requisiti di .NET Framework 4.6.
- Versione di SQL Server Management Studio installazioni il x86 di **ADALSQL. DLL**.
- SSDT installa la versione amd64 di **ADALSQL. DLL**.
- Il più recente di Visual Studio da [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) soddisfa i requisiti di .NET Framework 4.6, ma non installare la versione di amd64 necessari di **ADALSQL. DLL**.

## <a name="6-create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>6. creare gli utenti del database contenuti nel database mappato alle identità di Azure Active Directory

### <a name="about-contained-database-users"></a>Informazioni sugli utenti di database indipendente

Autenticazione di Azure Active Directory gli utenti di database devono essere creati come utenti database indipendente. Un utente di database indipendente in base a un'identità di Azure Active Directory, è un utente del database che non dispone di un account di accesso del database master e che esegue il mapping a un'identità nella directory di Azure Active Directory è associata al database. L'identità di Azure Active Directory può essere un singolo account utente o un gruppo. Per ulteriori informazioni sugli utenti database indipendente, vedere [Gli utenti del Database di contenuto per la il Database portatili](https://msdn.microsoft.com/library/ff929188.aspx).

> [AZURE.NOTE] Gli utenti del database (ad eccezione delle amministratori) non è possibile creare nel portale. Ruoli RBAC non vengono propagati a SQL Server, Database SQL di o SQL Data Warehouse. Ruoli RBAC Azure vengono utilizzati per la gestione delle risorse di Azure e non si applicano alle autorizzazioni di database. Ad esempio, il ruolo di **Collaboratore di SQL Server** non concedere l'accesso per connettersi al Database SQL o SQL Data Warehouse. Concedere l'autorizzazione di accesso direttamente nel database utilizzando istruzioni Transact-SQL.

### <a name="connect-to-the-user-database-or-data-warehouse-by-using-sql-server-management-studio-or-sql-server-data-tools"></a>Connettersi all'utente del database o data warehouse tramite SQL Server Management Studio o SQL Server Data Tools

Per confermare l'amministratore di Azure Active Directory è configurato correttamente, connettersi al database **master** utilizzando l'account di amministratore di Azure Active Directory.
Per eseguire il provisioning di un utente di Azure database contenuto basato su Active Directory (ad eccezione di amministratore del server a cui appartiene il database), connettersi al database con un'identità di Azure Active Directory che abbia accesso al database.

> [AZURE.IMPORTANT] Supporto per l'autenticazione di Azure Active Directory è disponibile con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) in Visual Studio 2015. La versione di agosto 2016 di SQL Server Management Studio include anche il supporto per l'autenticazione universale di Active Directory, che consente agli amministratori di richiedono l'autenticazione a più fattori tramite una chiamata telefonica, il messaggio di testo, sulle smart card con pin o notifica di app per dispositivi mobili.

#### <a name="connect-using-active-directory-integrated-authentication"></a>Connessione mediante l'autenticazione integrata di Active Directory

Utilizzare questo metodo se si è connessi a Windows mediante le credenziali di Azure Active Directory da un dominio federato.

1. Avviare Management Studio o strumenti dati nella casella **autenticazione** della finestra di dialogo **connessione al Server** (o **connettersi a motore di Database**) selezionare **Autenticazione integrata di Active Directory**. Nessuna password è necessaria o possa essere immesse in quanto verranno presentate le credenziali esistenti per la connessione.
    ![Selezionare l'autenticazione di Active Directory][11]

2. Fare clic sul pulsante **Opzioni** e nella pagina **Delle proprietà di connessione** nella casella **Connetti a database** digitare il nome del database utente che si desidera connettersi.
    ![Selezionare il nome del database][13]


#### <a name="connect-using-active-directory-password-authentication"></a>Connettersi tramite autenticazione tramite password di Active Directory

Utilizzare questo metodo quando si connette con un nome dell'entità di Azure Active Directory utilizzando Azure AD gestito dominio. È possibile anche utilizzare per account federato senza accesso al dominio, ad esempio quando si lavora in modalità remota.

Utilizzare questo metodo se si è connessi a Windows mediante le credenziali di un dominio non federato con Azure o mediante l'autenticazione di Azure Active Directory utilizzando Azure Active Directory in base alla prima o dominio client.

1. Avviare Management Studio o strumenti dati nella finestra di **autenticazione** finestra di dialogo **connessione al Server** (o **connessione al motore di Database**) selezionare **Autenticazione tramite Password di Active Directory**.
2. Nella casella **nome utente** digitare il proprio nome utente di Azure Active Directory nel formato **username@domain.com**. Deve trattarsi di un account da Azure Active Directory o un account da un dominio attuare una federazione con Azure Active Directory.
3. Nella casella **Password** digitare la password dell'utente per l'account Azure Active Directory o federato account di dominio.
    ![Selezionare autenticazione tramite Password di Active Directory][12]

4. Fare clic sul pulsante **Opzioni** e nella pagina **Delle proprietà di connessione** nella casella **Connetti a database** digitare il nome del database utente che si desidera connettersi. (Vedere l'immagine nell'opzione precedente).


### <a name="create-an-azure-ad-contained-database-user-in-a-user-database"></a>Creare un utente del database Azure Active Directory contenuti in un database utente

Per creare un utente di Azure database contenuto basato su Active Directory (ad eccezione di amministratore del server a cui appartiene il database), connettersi al database con un'identità di Azure Active Directory, come un utente con almeno dell'autorizzazione di **Modificare qualsiasi utente** . Utilizzare la sintassi Transact-SQL seguente:

    CREATE USER <Azure_AD_principal_name>
    FROM EXTERNAL PROVIDER;


*Azure_AD_principal_name* può essere il nome dell'entità utente di un utente di Azure Active Directory o il nome visualizzato per un gruppo di Azure Active Directory.

**Esempi:** Per creare un database di contenuti utente che rappresenta un annuncio Azure federato oppure gestita utente del dominio:

    CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
    CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;

Per creare un utente di contenuto del database che rappresenta un annuncio Azure o gruppo di dominio federato, specificare il nome visualizzato di un gruppo di sicurezza:

    CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;

Per creare un utente di contenuto del database che rappresenta un'applicazione che si connette tramite un token di Azure Active Directory:

    CREATE USER [appName] FROM EXTERNAL PROVIDER;

Per ulteriori informazioni sulla creazione di database indipendente utenti in base a identità di Azure Active Directory, vedere [Creare utente (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx).


> [AZURE.NOTE] Rimozione di amministratore di Azure Active Directory per SQL Azure impedire agli utenti di autenticazione Azure Active Directory di connettersi al server. Se necessario, inutilizzabile agli utenti di Azure Active Directory possono essere eliminati manualmente da un amministratore di Database SQL.

Quando si crea un utente del database, tale utente viene assegnata l'autorizzazione **CONNECT** e possibile connettersi al database come membro del ruolo **PUBLIC** . Inizialmente solo le autorizzazioni disponibili per l'utente sono tutte le autorizzazioni concesse al ruolo **pubblico** o le autorizzazioni concesse ai gruppi Windows che sono membri di. Una volta che viene effettuato il provisioning di un utente del database contenuta in base AD Azure, è possibile concedere all'utente autorizzazioni aggiuntive, esattamente come si concedono autorizzazioni a un altro tipo di utente. In genere concedere le autorizzazioni per i ruoli di database e aggiungere utenti ai ruoli. Per ulteriori informazioni, vedere [Nozioni fondamentali sulla autorizzazione motore di Database](http://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Per ulteriori informazioni sui ruoli di Database SQL speciale, vedere [gestione dei database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md).
Un utente di dominio federato importati in un dominio di gestire, è necessario utilizzare l'identità del dominio gestito.

> [AZURE.NOTE] Gli utenti di Azure Active Directory vengono contrassegnati nei metadati database con il tipo E (EXTERNAL_USER) e per i gruppi con tipo X (EXTERNAL_GROUPS). Per ulteriori informazioni, vedere [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx).


## <a name="7-connect-by-using-azure-ad-identities"></a>7. connettersi tramite identità di Azure Active Directory

Autenticazione di Azure Active Directory supporta i seguenti metodi di connessione a un database utilizzando l'identità di Azure Active Directory:

- Mediante l'autenticazione di Windows
- Utilizzo di un nome dell'entità di Azure Active Directory e una password
- Mediante l'autenticazione token di applicazione

### <a name="71-connecting-using-integrated-windows-authentication"></a>7.1. Connessione mediante l'autenticazione (Windows)

Per utilizzare l'autenticazione di Windows, è necessario federati Active Directory del dominio con Azure Active Directory. L'applicazione client (o un servizio) la connessione al database deve essere in esecuzione in un computer dominio con le credenziali di dominio dell'utente.

Per connettersi a un database utilizzando l'autenticazione e un'identità di Azure Active Directory, è necessario impostare la parola chiave di autenticazione nella stringa di connessione di database a integrata in Active Directory. Nell'esempio c# codice viene utilizzato ADO.NET.

    string ConnectionString =
    @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Nota che la stringa di connessione parola chiave ``Integrated Security=True`` non è supportata per la connessione al Database SQL Azure.
Si noti che quando si effettua una connessione ODBC sarà necessario rimuovere gli spazi e impostare l'autenticazione a 'ActiveDirectoryIntegrated'.

### <a name="72-connecting-with-an-azure-ad-principal-name-and-a-password"></a>7.2. Connessione con un nome dell'entità di Azure Active Directory e una password
Per connettersi a un database utilizzando l'autenticazione e un'identità di Azure Active Directory, è necessario impostare la parola chiave autenticazione password di Active Directory. La stringa di connessione deve contenere ID utente/UID e parole chiave Password/PWD e valori. Nell'esempio c# codice viene utilizzato ADO.NET.

    string ConnectionString =
      @"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
    SqlConnection conn = new SqlConnection(ConnectionString);
    conn.Open();

Ulteriori informazioni sui metodi di autenticazione di Azure Active Directory utilizzando gli esempi di codice demo disponibili [GitHub Demo autenticazione di Azure Active Directory](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).


### <a name="73-connecting-with-an-azure-ad-token"></a>7.3 connessione con un token di Azure Active Directory
Questo metodo di autenticazione consente intermedio services di connettersi al Database SQL Azure o Data Warehouse di SQL Azure ottenendo un token di Azure Active Directory (AAD). In questo modo scenari sofisticati, tra cui l'autenticazione basata su certificato. È necessario completare quattro passaggi di base per utilizzare l'autenticazione token di Azure Active Directory:

1. Registrare l'applicazione con Azure Active Directory e ottenere l'id client per il codice. 
2. Creare un utente del database che rappresenta l'applicazione. (Completata in precedenza nel passaggio 6).
3. Creare un certificato sul client computer esegue l'applicazione.
4. Aggiungere il certificato come chiave per l'applicazione.

Stringa di connessione di esempio:

```
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
connection.AccessToken = "Your JWT token"
conn.Open();
```

Per ulteriori informazioni, vedere [Il Blog di sicurezza di SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/2016/02/09/token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/).

### <a name="connecting-with-sqlcmd"></a>Connessione con sqlcmd  
Le seguenti istruzioni, connettersi tramite versione 13.1 di sqlcmd, che è disponibile dall' [Area Download](http://go.microsoft.com/fwlink/?LinkID=825643).

```
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="see-also"></a>Vedere anche

[Gestione di database e gli account di accesso di Database SQL Azure](sql-database-manage-logins.md)

[Utenti del Database di contenuti](https://msdn.microsoft.com/library/ff929071.aspx)

[CREARE utente (Transact-SQL)](http://msdn.microsoft.com/library/ms173463.aspx)


<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[9]: ./media/sql-database-aad-authentication/9ad-settings.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/11connect-using-int-auth.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db.png

