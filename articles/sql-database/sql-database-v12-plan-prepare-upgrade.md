<properties
    pageTitle="Pianificare l'aggiornamento a SQL Database V12 | Microsoft Azure"
    description="Descrive le operazioni preparatorie e limitazioni per l'aggiornamento alla versione V12 del Database di SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Pianificare e preparare l'aggiornamento a SQL Database V12


Questo argomento illustra la pianificazione e preparati da eseguire per aggiornare i database di SQL Azure dalla versione V11 alla V12.


Un nuovo [Portale Azure](https://portal.azure.com/) è disponibile per il supporto dell'aggiornamento a V12.


Nella tabella seguente sono elencati altri argomenti della Guida per V12.


| Titolo e il collegamento | Descrizione del contenuto |
| :--- | :--- |
| [Quali sono le novità in SQL Database V12](sql-database-v12-whats-new.md) | Viene descritto in dettaglio come V12 porta Database SQL Azure più vicino intero analoga a Microsoft SQL Server. |
| [Creare un database di SQL Database V12](sql-database-get-started.md) | Descrive come è possibile creare un nuovo database SQL Azure versione V12. Descrive varie opzioni oltre solo un database vuoto. |


## <a name="plan-ahead"></a>Pianificare in anticipo


Nelle sottosezioni seguenti descrivono la formazione e processi decisionali che è necessario risolvere prima verso l'aggiornamento del database di SQL Azure a V12 un agire.

### <a name="version-clarification"></a>Chiarimenti versione


In questo documento riguarda l'aggiornamento del Database SQL di Microsoft Azure dalla versione V11 a V12. Più formale sono i numeri di versione seguenti due valori, come riportato dall'istruzione Transact-SQL **selezionare @@version; ** :


- 12.0.2000.8 *(o versioni successive, bit V12)*
- 11.0.9228.18 *(V11)*
 - V11 è stata inoltre definito talvolta SAWA v2.

### <a name="service-tier-planning"></a>Servizio di pianificazione di livello


Per fare in modo supporta solo i livelli di servizio denominati Basic, Standard e Premium a partire da V12, Database SQL Azure. Il livello di servizio Web e Business non è supportato in V12. Se si prevede di eseguire l'aggiornamento del database di SQL Azure che si trova il livello di servizio Web o azienda, è necessario decidere quale deve essere il nuovo livello di servizio.


Per informazioni dettagliate sui livelli di servizio Basic, Standard e Premium, vedere:

- [Livelli di servizio di Database SQL](sql-database-service-tiers.md)
- [Aggiornare i database SQL Database Web/Business a nuovi livelli di servizio](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Esaminare la configurazione della replica geografico


Se il database di SQL Azure è configurato per la replica geografico, si deve configurazione corrente del documento e interrompere geografico replica, prima di iniziare le azioni di preparazione aggiornamento. Al termine dell'aggiornamento è necessario riconfigurare il database per la replica geografico.


La strategia consiste nel lasciare invariata l'origine e verificare una copia del database.


## <a name="preparation-actions"></a>Azioni di preparazione


Dopo avere completato la pianificazione, è possibile eseguire i passaggi di azione descritti nelle sottosezioni seguenti per preparare l'ambiente per la fase di aggiornamento finale.


Descrizioni dettagliate della fase di aggiornamento finale sono disponibili negli argomenti della Guida sono collegate nella parte superiore di questo argomento della Guida.


### <a name="service-tier-actions"></a>Azioni di livello del servizio


Il servizio Web e Business prezzi livello non è supportato in V12.


Se il database di SQL Azure V11 è un database Web o azienda, il processo di aggiornamento consente di passare il database a un livello supportato. L'aggiornamento consigliato un livello che soddisfa la cronologia di carico di lavoro del database. Tuttavia, è possibile scegliere qualsiasi livello supportato desiderato.


È possibile ridurre i passaggi necessari durante l'aggiornamento passando il database V11 lontano dal livello Web e Business prima di iniziare l'aggiornamento. È possibile eseguire questa operazione utilizzando il nuovo [Portale Azure](https://portal.azure.com/).


Se non si conosce il livello di servizio per passare a, il livello di S2 del livello Standard può essere una scelta iniziale sensibile. Qualsiasi livello inferiore avrà meno risorse rispetto al livello Web e Business era.


### <a name="suspend-geo-replication-during-upgrade"></a>Sospendere la replica geografico durante l'aggiornamento


Non è possibile eseguire l'aggiornamento a V12 se geografico replica è attiva il database. Riconfigurare il database per interrompere l'uso della replica geografico.


Al termine dell'aggiornamento è possibile configurare il database per utilizzare di nuovo la replica geografico.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Porte aperte in una macchina virtuale Azure per la connettività dei client


Se il programma client si connette a SQL Database V12 mentre il client viene eseguito su una Azure macchine (), è necessario aprire gli intervalli di porta seguenti nella macchina virtuale:

- 11000 11999
- 14000 14999


Fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md) per informazioni dettagliate sulle porte per V12 di Database SQL. Le porte sono necessarie per miglioramenti delle prestazioni in SQL Database V12.


##<a id="limitations"></a>Limitazioni durante e dopo l'aggiornamento a V12


### <a name="portals-for-v12"></a>Portali per V12


Esistono tre portali per Azure, ognuno con particolari esigenze relative V12 di Database SQL.


- [http://Portal.Azure.com/](https://portal.azure.com/)<br/>Il portale di Azure sono le novità ed è ancora in uno stato di anteprima. Questo portale non è ancora in completo generale disponibilità (GA). Il portale:
 - Gestire il server V12 e il database.
 - Possibile aggiornare il database V11 a V12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Questo portale classica Azure non hanno un limite potrebbe essere gradualmente. Il portale:
 - Gestire il server V12 e il database.
 - Consente di *non* aggiornamento il V11 database a V12.


- (http://*nomeserver*. database.windows.net)<br/>Portale classica di Database SQL Azure:
 - Può*non* gestire V12 server.


È consigliabile connettersi al database SQL Azure con Visual Studio 2015 (VS2015). VS2015 può essere usata per attività, ad esempio le operazioni seguenti:


- Per eseguire un'istruzione Transact-SQL.
- Per specificare uno schema.
- Per lo sviluppo di un database, online o offline.


In alternativa, gratuitamente possibile scaricare [Visual Studio Community 2015](https://www.visualstudio.com/vs/community/), ovvero una versione completa di VS2015.


Nel portale classica Azure meno recenti, nella pagina database è possibile fare clic su **Apri in Visual Studio** per avviare VS2015 nel computer in uso per connessione al Database SQL Azure.


Per un'altra alternativa, è possibile usare SQL Server Management Studio (SQL Server Management Studio) 2014 con [CU6](http://support.microsoft.com/kb/3031047/) per connettersi al Database SQL Azure. Per ulteriori informazioni siano presenti questo post di blog:<br/>[Client utensili aggiornamenti di Database SQL Azure](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] Si consiglia di utilizzare sempre la versione più recente di Management Studio per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitazione *durante* l'aggiornamento a V12


Il database V11 rimane disponibile per l'accesso ai dati durante l'aggiornamento a V12. Esistono ancora sono riportate alcune limitazioni da prendere in considerazione.


| Limitazione | Descrizione |
| :--- | :--- |
| Durata dell'aggiornamento | La durata dell'aggiornamento dipende dalle dimensioni, edition e il numero di database nel server di. Il processo di aggiornamento è possibile eseguire per ore a giorni per server soprattutto per i server che contiene i database:<br/><br/>*Maggiore di 50 GB, o<br/> * In un livello di servizio non premium<br/><br/>Creazione di nuovi database nel server durante l'aggiornamento è inoltre possibile aumentare la durata di aggiornamento. |
| Nessuna replica geografico | La replica geografico non è supportata in un server V12 attualmente coinvolti in un aggiornamento da V11. |
| Database è temporaneamente non disponibile nella fase finale di eseguire l'aggiornamento a V12 | I database che appartengono al server V11 restano disponibili durante il processo di aggiornamento. Tuttavia, la connessione al server e database è temporaneamente non disponibile nella fase finale, quando il parametro su inizia da V11 a V12 pronti.<br/><br/>L'opzione periodo può variare da 40 secondi a 5 minuti. Per la maggior parte dei server, passare sopra deve essere completata entro 90 secondi. Cambia nel tempo aumenta per i server che hanno un numero elevato di database oppure quando i database carichi di lavoro di scrittura elevato. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitazione *dopo* l'aggiornamento alla V12


| Limitazione | Descrizione |
| :--- | :--- |
| Non è possibile ripristinare V11 | Dopo un aggiornamento sul posto, il risultato non può essere ripristinato o annullato. |
| Livello Web o Business | Dopo l'aggiornamento, verrà avviato il server per il nuovo database V12 non riconosce accettare o il livello di servizio Web o Business. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Esportare e importare *dopo* l'aggiornamento a V12


È possibile esportare o importare un database V12 tramite il [Portale di Azure](https://portal.azure.com/). Oppure è possibile esportare o importare utilizzando uno dei seguenti strumenti:


- SQL Server Management Studio (SQL Server Management Studio)
- Visual Studio 2015
- Framework di applicazione livello dati (DacFx)


Tuttavia, per usare gli strumenti, è necessario innanzitutto disporre o installare gli aggiornamenti più recenti per garantire che supportano le nuove caratteristiche di V12:


- [Aggiornamento cumulativo 6 per SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Aggiornamento di febbraio 2015 per Database di SQL Server utensili in Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Framework applicazione livello dati di febbraio 2015 (DacFx) per V12 di Database SQL Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Sono stati aggiornati i collegamenti strumento precedenti o successiva al 2 marzo 2015. È consigliabile utilizzare questi aggiornamenti più recenti di questi strumenti.


#### <a name="automated-export"></a>Esportazione automatica


Esportazione automatica continua a essere disponibile come anteprima.  Nessun aggiornamento dello strumento client è necessario quando si usa automatizzato di esportazione.  Se si sceglie di eseguire il file risultante e importare in un server in locale, gli aggiornamenti di utensili elencati sopra sono necessari per importare correttamente.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Ripristinare V12 di un database V11 eliminato

Lo scenario seguente illustra che è possibile ripristinare un database SQL Azure V11 eliminate in un server di Database SQL Azure V12.

1. Si supponga di che avere un server di Database SQL Azure V11. <br/> Il server è disponibile un database al livello di servizio Web e Business obsoleto.
2. Eliminare il database.
3. Aggiornare il server a V12.
4. Successivamente si ripristinare il database nel server. <br/> Il database in questo modo viene aggiornato a V12 livello S0 del livello di servizio Standard.
5. È possibile passare il database a qualsiasi livello di servizio supportati, se S0 non è la preferenza.


### <a name="powershell-cmdlets"></a>Cmdlet di PowerShell


Cmdlet di PowerShell disponibili iniziare, interrompere o controllare l'aggiornamento a V12 di Database SQL Azure dalla V11 o qualsiasi altra versione pre-V12.

- [Eseguire l'aggiornamento a SQL Database V12 tramite PowerShell](sql-database-upgrade-server-powershell.md)

Per la documentazione della Guida di riferimento su questi cmdlet di PowerShell, vedere:


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Inizio AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Interrompi AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Il cmdlet Interrompi significa annullare, posizionare il puntatore non. Non è possibile riprendere l'aggiornamento, ad eccezione di avviare nuovamente dall'inizio. Il cmdlet Interrompi Pulisce e rilascia tutte le risorse appropriate.


## <a name="failure-resolution"></a>Risoluzione di errori


Se l'aggiornamento ha esito negativo per qualsiasi motivo dispari, il database V11 rimane attivo e disponibile come di consueto.


## <a name="related-links"></a>Collegamenti correlati


- [Funzionalità di anteprima](https://azure.microsoft.com/services/preview/) di Microsoft Azure


<!--Anchors-->
[Subheading 1]: #subheading-1
