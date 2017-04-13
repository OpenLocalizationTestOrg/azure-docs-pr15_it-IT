<properties 
   pageTitle="Preparazione di Database SQL Azure tramite il portale di Azure | Microsoft Azure" 
   description="È possibile utilizzare la preparazione di Database SQL Azure nel portale di Azure per esaminare e implementare consigli per il database di SQL esistenti che consentono di migliorare le prestazioni delle query corrente." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Preparazione di Database SQL tramite il portale di Azure

> [AZURE.SELECTOR]
- [Panoramica su Database SQL](sql-database-advisor.md)
- [Portale](sql-database-advisor-portal.md)

È possibile utilizzare la preparazione di Database SQL Azure nel portale di Azure per esaminare e implementare consigli per il database di SQL esistenti che consentono di migliorare le prestazioni delle query corrente.

## <a name="viewing-recommendations"></a>Visualizzazione di suggerimenti

La pagina consigli è nel punto in cui si visualizza i consigli superiore in base alle loro impatto per migliorare le prestazioni. È anche possibile visualizzare lo stato delle operazioni nella cronologia. Selezionare un suggerimento o lo stato per visualizzare ulteriori dettagli.

Per visualizzare e applicare raccomandazioni, sono necessarie le autorizzazioni di [controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-configure.md) in Azure. **Utilità per la lettura**, le autorizzazioni di **Collaborazione DB SQL** sono necessari per i suggerimenti di visualizzazione e **proprietario**, le autorizzazioni di **Collaborazione DB SQL** è necessario eseguire le azioni; creare o eliminare indici e annullare la creazione dell'indice.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **altri servizi** > **database SQL**e selezionare il database.
5. Fare clic su **recommendation delle prestazioni** per visualizzare suggerimenti disponibili per il database selezionato.

> [AZURE.NOTE] Per ottenere suggerimenti un database deve disporre di un giorno di utilizzo ed è necessario aggiungere alcune attività. È necessario anche alcune attività coerenti. Preparazione di Database SQL più semplice ottimizzare modelli di query coerente rispetto a possibile per casuale burst granulato dell'attività. Se non sono disponibili i suggerimenti, pagina **recommendation prestazioni** dovrebbe fornire un messaggio che indica il motivo.

![Consigli](./media/sql-database-advisor-portal/recommendations.png)

Ecco un esempio di "Risolvere il problema dello schema" recommendation nel portale di Azure.

![Risolvere il problema dello Schema](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Consigli vengono ordinati per il potenziale impatto sulle prestazioni in quattro categorie seguenti:

| Impatto | Descrizione |
| :--- | :--- |
| Elevato | Suggerimenti di grande impatto dovrebbero fornire l'impatto sulle prestazioni più significativo. |
| Media | Impatto medio consigli dovrebbero migliorare le prestazioni, ma in modo sostanziale. |
| Bassa | Consigli basso impatto dovrebbero fornire prestazioni migliori senza ma miglioramenti potrebbero non essere significativi. 


### <a name="removing-recommendations-from-the-list"></a>Rimozione di consigli dall'elenco

Se l'elenco di suggerimenti include gli elementi che si desidera rimuovere dall'elenco, è possibile ignorare il suggerimento:

1. Selezionare un suggerimento nell'elenco dei **suggerimenti**.
2. Fare clic su **Annulla** nella e **Dettagli** .


Se lo si desidera, è possibile aggiungere elementi ignorati tornare all'elenco di **suggerimenti** :

1. Scegliere **Visualizza eliminato**e il **consigli** .
1. Selezionare un elemento ignorato dall'elenco per visualizzarne i dettagli.
1. Facoltativamente, fare clic su **Annulla Annulla** per aggiungere l'indice nell'elenco principale delle **raccomandazioni**.



## <a name="applying-recommendations"></a>Applicazione delle indicazioni

Preparazione di Database SQL offre controllo completo su come i consigli sono abilitate utilizzando uno dei seguenti tre opzioni: 

- Applicare singoli suggerimenti uno alla volta.
- Abilitare advisor applicare automaticamente suggerimenti (al momento si applica a solo consigli indice).
- Per implementare un suggerimento manualmente, eseguire lo script T-SQL consigliato il database.

Selezionare qualsiasi suggerimento per visualizzarne i dettagli e quindi fare clic su **Visualizza script** per esaminare i dettagli esatti della creazione il suggerimento.

Il database rimane in linea mentre il consulente applica recommendation, ossia l'utilizzo di preparazione di Database SQL mai richiede un database offline.

### <a name="apply-an-individual-recommendation"></a>Applicare un suggerimento singoli

È possibile esaminare e accettare consigli uno alla volta.

1. Scegliere un suggerimento e il **consigli** .
2. Scegliere **Applica**e il **Dettagli** .

    ![Applicare recommendation](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Attivare la gestione di indice automatico

È possibile impostare la preparazione di Database SQL di implementare consigli automaticamente. Come suggerimenti diventano disponibile automaticamente essere applicate. Come con tutte le operazioni di indice gestite dal servizio se l'impatto sulle prestazioni è un valore negativo il suggerimento verrà ripristinato.

1. Scegliere **automatizzare**e il **suggerimenti** :

    ![Impostazioni verificato](./media/sql-database-advisor-portal/settings.png)

2. Impostare il advisor automaticamente gli indici di **creare** o **eliminare** :

    ![Gli indici consigliati](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Eseguire manualmente lo script T-SQL consigliato

Selezionare qualsiasi suggerimento e quindi fare clic su **Visualizza script**. Eseguire questo script per il database per applicare manualmente il suggerimento.

*Gli indici che vengono eseguiti manualmente non vengono monitorati e convalidati per impatto sulle prestazioni dal servizio* in modo che si consiglia di monitorare tali indici dopo la creazione di verificare forniscono miglioramenti delle prestazioni e regolare o eliminarli se necessario. Per informazioni dettagliate sulla creazione di indici, vedere [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Annullamento consigli

Suggerimenti in uno stato **in sospeso**, **verifica**o **successo** possono essere annullati. Non possono essere annullati consigli con lo stato di **esecuzione** .

1. Selezionare un suggerimento nell'area **Ottimizzazione cronologia** per aprire e il **Dettagli consigli** .
2. Fare clic su **Annulla** per interrompere il processo di applicazione il suggerimento.



## <a name="monitoring-operations"></a>Monitoraggio delle operazioni

L'applicazione di un suggerimento potrebbe non avvenire immediatamente. Il portale rappresenta i dettagli relativi allo stato di operazioni recommendation. Di seguito sono stati in cui può essere un indice:

| Stato | Descrizione |
| :--- | :--- |
| In attesa | Applicare recommendation comando è stata ricevuta e pianificata l'esecuzione. |
| Esecuzione | Viene applicato il suggerimento. |
| Successo | Suggerimento è stata applicata. |
| Errore | Si è verificato un errore durante il processo di applicazione il suggerimento. Può trattarsi di un problema temporaneo o eventualmente uno schema modificare alla tabella e lo script non è più valido. |
| Ripristino | Il suggerimento è stato applicato, ma è stato considerato meno efficienti e viene viene ripristinato automaticamente. |
| Ripristinato | È stato ripristinato il suggerimento. |

Fare clic su un suggerimento in corso dall'elenco per visualizzare ulteriori dettagli:

![Gli indici consigliati](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Ripristino di un suggerimento

Se è stata utilizzata il advisor per applicare il suggerimento (ovvero che non manualmente è stato eseguito lo script T-SQL) verrà automaticamente ripristinata se trova l'impatto sulle prestazioni come negativo. Se per qualche motivo si desidera ripristinare un suggerimento è possibile eseguire le operazioni seguenti:


1. Selezionare un suggerimento stata applicata nell'area **ottimizzazione della cronologia** .
2. Fare clic su **Ripristina** nella e **Dettagli recommendation** .

![Gli indici consigliati](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Monitoraggio delle prestazioni di suggerimenti di indice analitico

Dopo aver consigli sono correttamente implementati (al momento indicizzare operazioni e impostare i parametri solo i suggerimenti di query) è possibile scegliere **Query approfondimenti** e dettagli il suggerimento di aprire [Query prestazioni approfondimenti](sql-database-query-performance.md) ed esaminare l'impatto sulle prestazioni delle query superiore.

![Impatto sulle prestazioni di monitor](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Riepilogo

Preparazione di Database SQL vengono forniti suggerimenti per migliorare le prestazioni di database SQL. Fornendo script T-SQL, nonché singoli e completamente automatica (al momento solo indice), il consulente fornisce assistenza utile ottimizzazione del database e infine migliorare le prestazioni delle query.



## <a name="next-steps"></a>Passaggi successivi

Monitorare i suggerimenti, puoi continuare ad applicare in modo da ottimizzare le prestazioni. Carichi di lavoro di database sono dinamiche e modificare continuamente. Database SQL proporrà continuerà a monitorare e consigli per il che è possibile migliorare le prestazioni del database. 

 - Per una panoramica della preparazione di Database SQL, vedere [Preparazione di Database SQL](sql-database-advisor.md) .
 - Visualizzare [Informazioni dettagliate sulle prestazioni di Query](sql-database-query-performance.md) per informazioni su come visualizzare l'impatto sulle prestazioni delle query superiore.

## <a name="additional-resources"></a>Risorse aggiuntive

- [Archivio di query](https://msdn.microsoft.com/library/dn817826.aspx)
- [CREARE L'INDICE](https://msdn.microsoft.com/library/ms188783.aspx)
- [Controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-configure.md)






