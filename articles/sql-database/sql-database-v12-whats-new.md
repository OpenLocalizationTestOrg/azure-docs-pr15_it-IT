<properties
    pageTitle="Quali sono le novità in SQL Database V12 | Microsoft Azure"
    description="Descrive il motivo per cui usufruire sistemi aziendali che utilizzano il Database di SQL Azure nel cloud, eseguire l'aggiornamento alla versione V12."
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
    ms.date="08/15/2016"
    ms.author="genemi"/>


# <a name="whats-new-in-sql-database-v12"></a>Quali sono le novità in SQL Database V12


Questo argomento illustra i molti vantaggi che presenta la nuova versione V12 di Database SQL Azure rispetto alla versione V11.


È stato possibile continuare a aggiungere caratteristiche alla V12. In modo consigliamo a visitare la pagina Web degli aggiornamenti del servizio per Azure e utilizzare i filtri:


- Filtrati in base ai [Database SQL di servizio](https://azure.microsoft.com/updates/?service=sql-database).
- Filtrati in base ai disponibilità generale [annunci (GA)](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) per le caratteristiche di Database SQL.


Informazioni aggiornate sui limiti delle risorse per Database SQL descritte in:<br/>[Limiti di risorse del Database SQL azure](sql-database-resource-limits.md).


## <a name="increased-application-compatibility-with-sql-server"></a>Compatibilità delle applicazioni maggiore con SQL Server


Una chiave per SQL Database V12 doveva per migliorare la compatibilità con Microsoft SQL Server 2014 e per garantire la compatibilità non sono disponibili nuove versioni di SQL Server. Tra le altre aree V12 raggiunge uniformità con SQL Server nell'area importante della programmazione. Per esempio:

- [Supporto JSON incorporato](https://msdn.microsoft.com/library/dn921897.aspx)

- [Funzioni della finestra](http://msdn.microsoft.com/library/ms189798.aspx), con [sopra](http://msdn.microsoft.com/library/ms189461.aspx)

- [Gli indici XML](http://msdn.microsoft.com/library/bb934097.aspx) e [selettivi indici XML](http://msdn.microsoft.com/library/jj670104.aspx)

- [Rilevamento delle modifiche](http://msdn.microsoft.com/library/bb933875.aspx)

- [SELEZIONA... IN](http://msdn.microsoft.com/library/ms188029.aspx)

- [Ricerca full-text](http://msdn.microsoft.com/library/ms142571.aspx)

- [MODIFICARE la configurazione nell'ambito di DATABASE (Transact-SQL)](http://msdn.microsoft.com/library/mt629158.aspx)

Vedere [di seguito](sql-database-transact-sql-information.md) per il set di piccole dimensioni delle funzionalità non ancora supportate nel Database di SQL.


### <a name="compatibility-level-130"></a>Livello di compatibilità 130


> [AZURE.IMPORTANT] *Appena* creato database nel V12 di Database SQL Azure a partire da **giugno 2016**, impostare il livello di compatibilità partono 130, che corrisponde a GA. 2016 di Microsoft SQL Server
> 
> È possibile utilizzare `ALTER DATABASE YourDatabase SET COMPATIBILITY_LEVEL = 120` se si preferisce.
> 
> Database creati prima giugno 2016 non hanno il livello di compatibilità modificato da questa modifica del valore predefinito. Né il livello di un database modificata per l'aggiornamento da V11 a V12.



Per visualizzare una spiegazione di come è possibile confrontare le query più importanti tra le ultime rispetto a livello di compatibilità precedente, vedere:

- [Prestazioni migliorate Query con il livello di compatibilità 130 nel Database SQL Azure](sql-database-compatibility-level-query-performance-130.md)



## <a name="more-premium-performance-new-performance-levels"></a>Altre prestazioni ottimali, nuovi livelli di prestazioni


In V12, abbiamo aumentato le unità di transazione di Database (DTUs) assegnata a tutti i livelli di prestazioni Premium del 25% senza costi aggiuntivi. Miglioramenti delle prestazioni maggiori possono essere ottenuti con nuove caratteristiche quali:


- Supporto per in memoria [columnstore indici](http://msdn.microsoft.com/library/gg492153.aspx).
- [Partizione delle tabelle per le righe](http://msdn.microsoft.com/library/ms187802.aspx) con i miglioramenti correlati alla [Tabella TRONCARE](http://msdn.microsoft.com/library/ms177570.aspx).
- La disponibilità di gestione dinamica visualizzazioni [(viste)](http://msdn.microsoft.com/library/ms188754.aspx) per aiutare a monitorare e ottimizzare le prestazioni.


### <a name="reliable-performance"></a>Prestazioni affidabili


Se il programma client si connette a SQL Database V12 mentre il client viene eseguito su una Azure macchine (), è necessario aprire gli intervalli di porta seguenti nella macchina virtuale:

- 11000 11999
- 14000 14999


Fare clic [qui](sql-database-develop-direct-route-ports-adonet-v12.md) per informazioni dettagliate sulle porte per V12 di Database SQL. Le porte sono necessarie per miglioramenti delle prestazioni in SQL Database V12.


## <a name="better-support-for-cloud-saas-vendors"></a>Supporto migliorato per il cloud SaaS fornitori


Solo in V12, è stato rilasciato il nuovo livello di prestazioni Standard S3 e l'anteprima pubblica del [pool di database flessibile](sql-database-elastic-pool.md). Pool di database flessibile è una soluzione progettato per il cloud SaaS fornitori.  Con pool di database flessibile, è possibile:


- Condividere DTUs database per ridurre i costi per un numero elevato di database.
- Eseguire i [processi di database flessibile](sql-database-elastic-jobs-overview.md) per gestire i database in scala.


## <a name="security-enhancements"></a>Miglioramenti della protezione


Protezione costituisce un problema principale per tutti gli utenti che viene eseguita la loro attività nel cloud. Le caratteristiche di protezione più recenti rilasciate V12 includono:


- [Protezione a livello di riga](http://msdn.microsoft.com/library/dn765131.aspx) (RLS)
- [Maschera dati dinamici](sql-database-dynamic-data-masking-get-started.md)
- [Database indipendenti](http://msdn.microsoft.com/library/ff929188.aspx)
- [Ruoli applicazione](http://msdn.microsoft.com/library/ms190998.aspx) gestito con GRANT, Nega, revocare
- [Crittografia dati trasparente](http://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) (TDE)
- [La connessione al Database SQL mediante l'autenticazione di Azure Active Directory](sql-database-aad-authentication.md)
 - Database SQL supporta l'autenticazione di Azure Active Directory, un meccanismo di connessione al Database SQL tramite identità di Azure Active Directory (Azure Active Directory). Con l'autenticazione di Azure Active Directory è possibile gestire centralmente l'identità degli utenti del database e altri servizi Microsoft in una posizione centrale.
- [Sempre crittografato](https://msdn.microsoft.com/library/mt163865.aspx) (nella versione di anteprima) rende la crittografia trasparente alle applicazioni e consente ai client crittografare dati riservati all'interno di applicazioni client senza condividere le chiavi di crittografia con Database di SQL.


## <a name="increased-business-continuity-when-recovery-is-needed"></a>Maggiore continuità aziendale quando è necessaria una ripristino


Ripristino stimato sempre (ERTs) e V12 offre ripristino migliorato punto obiettivi (RPOs):


| Caratteristica di continuità aziendale | Versione precedente | V12 |
| :-- | :-- | :-- |
| Ripristino geografico | • Rilasciato < 24 ore.<br/>• Inserisci < 12 ore. | • Rilasciato < 1 ora.<br/>• Inserisci < 12 ore. |
| Replica di geografico Active | • Rilasciato < 5 minuti.<br/>• Inserisci < 1 ora. | • Rilasciato < 5 secondi.<br/>• Inserisci < 30 secondi. |


Per ulteriori informazioni, vedere [la continuità aziendale di Database SQL](sql-database-business-continuity.md) .


## <a name="more-reasons-to-upgrade-now"></a>Altri motivi per eseguire l'aggiornamento


Sono disponibili molti motivi perché i clienti devono Aggiorna a V12 di Database SQL Azure da V11:


- SQL Database V12 ha un lungo elenco di caratteristiche oltre le caratteristiche di V11.
- È stato possibile continuare ad aggiungere nuove funzionalità per V12, ma nessuna nuova caratteristica verrà tutti aggiunti al V11.
- La maggior parte delle nuove caratteristiche rilasciate SQL Database V12 prima vengono rilasciati per Microsoft SQL Server.


## <a name="are-you-using-v12-already"></a>Si sta utilizzando V12 già?


Un modo semplice per verificare se si dispone di un database o server logico in esecuzione in una versione precedente del servizio di Database SQL consiste nell'eseguire le operazioni seguenti:


1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia**.
3. Fare clic su **SQL Server**.
4. L'icona accanto al server o database indica la storia:
 - ![Icona per un server v12](./media/sql-database-v12-whats-new/v12_icon.png) **server logico V12**
 - ![Icona per server della versione precedente](./media/sql-database-v12-whats-new/earlier_icon.png) **server logico a versioni precedenti**


Un'altra tecnica per determinare la versione consiste nell'eseguire il `SELECT @@version;` istruzione nel database e visualizzare i risultati simili a:


- **12**.0.2000.10 &nbsp; *(versione V12)*
- **11**.0.9228.18 &nbsp; *(versione V11)*


Un database V12 può essere ospitato solo su un server logico V12. E un server V12 può contenere solo V12 database.


Se non si è ancora eseguendo su V12, è possibile aggiornare il server logico eseguendo la procedura descritta in [eseguire l'aggiornamento a V12 Database di SQL in posizione](sql-database-v12-plan-prepare-upgrade.md).


## <a name="V12AzureSqlDbPreviewGaTable"></a>Aree di disponibilità generale


- Entro il 31 luglio 2015 tutte le aree avevano passa alla disponibilità generale (GA).
- V12 è stata rilasciata in dicembre 2014, ma solo allo stato dell'anteprima.

[Condizioni aggiuntive d'uso per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
