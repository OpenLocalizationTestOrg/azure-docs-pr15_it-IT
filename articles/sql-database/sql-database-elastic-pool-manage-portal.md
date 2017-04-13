<properties
    pageTitle="Monitorare e gestire un pool di database flessibile con il portale di Azure | Microsoft Azure"
    description="Informazioni su come usare il portale di Azure e intelligenti del Database di SQL per gestire, monitorare e un pool di database flessibile scalable per ottimizzare le prestazioni del database e gestire i costi di dimensioni a destra."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="ninarn"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/22/2016"
    ms.author="ninarn"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="monitor-and-manage-an-elastic-database-pool-with-the-azure-portal"></a>Monitorare e gestire un pool di database flessibile con il portale di Azure

> [AZURE.SELECTOR]
- [Portale di Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


È possibile utilizzare il portale di Azure per monitorare e gestire un pool di database flessibile e i database nel pool. Dal portale, è possibile monitorare l'utilizzo di un pool flessibile e i database all'interno di tale pool. È possibile creare un insieme di modifiche al pool flessibile e inviare tutte le modifiche contemporaneamente. Queste modifiche includono aggiunta o rimozione dei database, modificare le impostazioni del pool flessibile o modificare le impostazioni del database.

Nell'immagine seguente viene mostrato un pool flessibile di esempio. La visualizzazione include:

*  Grafici per il monitoraggio di utilizzo delle risorse del pool di flessibile e database contenuti nel pool.
*  Pulsante **Configura** pool di apportare modifiche al pool flessibile.
*  Pulsante **Crea database** che crea un nuovo database e viene aggiunto al pool di flessibile corrente.
*  Processi flessibile che consentono di gestire un numero elevato di database mediante l'esecuzione di script Transact SQL in tutti i database in un elenco.

![Visualizzazione del pool][2]

Per usare i passaggi descritti in questo articolo, è necessario un server SQL Azure con almeno un database e un pool flessibile. Se non si dispone di un pool flessibile, vedere [creare un pool](sql-database-elastic-pool-create-portal.md). Se non si dispone di un database, vedere l' [esercitazione con database di SQL](sql-database-get-started.md).

## <a name="elastic-pool-monitoring"></a>Monitoraggio pool flessibile

È possibile accedere a un determinato pool per visualizzare il relativo utilizzo delle risorse. Per impostazione predefinita, il pool è configurato per visualizzare l'uso di spazio di archiviazione ed eDTU per l'ultima ora. Il grafico può essere configurato in modo da visualizzare metriche diverse rispetto a varie finestre di tempo.

1. Selezionare un pool per l'uso con.
2. In **Monitoraggio flessibile Pool di** un grafico viene segnalato **utilizzo delle risorse**. Fare clic sul grafico.

    ![Monitoraggio pool flessibile][3]

    Verrà aperto e **l'unità di misura metriche** , che mostra una visualizzazione dettagliata di metriche specificate sopra la finestra di tempo specificato.   

    ![Metrica blade][9]

### <a name="to-customize-the-chart-display"></a>Per personalizzare la visualizzazione del grafico

È possibile modificare il grafico ed e il metrico per visualizzare altre metriche come percentuale di CPU, percentuale IO dati e percentuale IO log utilizzata.

2. Scegliere **Modifica**e il metrico.

    ![Fare clic su Modifica][6]

- In e il **Grafico di modifica** , selezionare un nuovo intervallo di tempo (ore, oggi, o ultima settimana) o fare clic su **personalizzato** per selezionare un intervallo di date nelle ultime due settimane. Selezionare il tipo di grafico (barra o riga), quindi selezionare le risorse da controllare.

> Nota: Solo metriche con la stessa unità di misura possono essere visualizzate nel grafico nello stesso momento. Ad esempio, se si seleziona "eDTU percentuale" quindi solo sarà possibile selezionare altre metriche con percentuale come unità di misura.

    ![Click edit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Fare clic su **OK**.


## <a name="elastic-database-monitoring"></a>Monitoraggio database flessibile

Singoli database possono anche verificare la presenza di problemi potenziali.

1. In **Monitoraggio Database flessibile**, esiste un grafico che mostra la metrica per cinque database. Per impostazione predefinita, il grafico visualizza superiore 5 database nel pool di tramite l'utilizzo di eDTU Media nell'ultima ora. Fare clic sul grafico.

    ![Monitoraggio pool flessibile][4]

2. Verrà visualizzata e il **Database di utilizzo delle risorse** . In questo modo una descrizione dettagliata dell'utilizzo del database nel pool. Utilizzo della griglia nella parte inferiore della stessa e, è possibile selezionare qualsiasi database nel pool di visualizzare il relativo uso nel grafico (fino a 5 database). È inoltre possibile personalizzare la finestra metriche e l'ora visualizzata nel grafico, fare clic su **Modifica grafico**.

    ![Blade di utilizzo delle risorse di database][8]

### <a name="to-customize-the-view"></a>Per personalizzare la visualizzazione

1. In e **l'utilizzo delle risorse Database** , fare clic su **Modifica grafico**.

    ![Fare clic su Modifica grafico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. In e il grafico **Modifica** , selezionare un nuovo intervallo di tempo (oltre all'ora o ultime 24 ore) o fare clic su **personalizzato** per selezionare un giorno diverso nelle ultime 2 settimane da visualizzare.

    ![Fare clic su personalizzato](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Fare clic sulla freccia **confronto dei database da** per selezionare una metrica diversa da utilizzare per confrontare i database.

    ![Modificare il grafico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Per selezionare database che si desidera eseguire il monitoraggio

Nell'elenco database e il **Database di utilizzo delle risorse** è possibile trovare database specifico, sfogliare le pagine nell'elenco oppure digitare il nome di un database. Utilizzare la casella di controllo per selezionare il database.

![Ricerca di database che si desidera eseguire il monitoraggio][7]


## <a name="add-an-alert-to-a-pool-resource"></a>Aggiungere un avviso per una risorsa di pool

È possibile aggiungere regole per le risorse che inviano la posta elettronica a utenti o stringhe avvisi per i punti finali URL quando la risorsa raggiunge una soglia di utilizzo che sono stati impostati.

> [AZURE.IMPORTANT]Utilizzo delle risorse monitoraggio per i pool flessibile ha un ritardo di almeno 20 minuti. Impostazione degli avvisi di meno di 30 minuti per i pool flessibile non è attualmente supportato. Impostare gli avvisi per pool flessibile con un punto (parametro denominato "-WindowSize" PowerShell API) di meno di 30 minuti non può essere avviata. Assicurarsi che tutti gli avvisi che è possibile definire per i pool flessibile utilizzano un periodo (WindowSize) di 30 minuti o più.

**Per aggiungere un avviso a qualsiasi risorsa:**

1. Fare clic sul grafico di **utilizzo delle risorse** per aprire e **l'unità di misura metriche** , fare clic su **Aggiungi avviso**e quindi compilare le informazioni e **l'aggiungere una regola di avviso** (**risorsa** sia automaticamente configurato per il pool di applicazioni che si lavora con).
2. Digitare un **nome** e una **Descrizione** che identifica l'avviso verso il mittente e il destinatario.
3. Scegliere un' **unità di misura metriche** che si desidera un avviso nell'elenco.

    Il grafico mostra in modo dinamico utilizzo delle risorse per tale metrica consente di selezionare una determinata soglia.

4. Scegliere una **condizione** (maggiore, minore, e così via) e un **limite**.
5. Fare clic su **OK**.



## <a name="move-a-database-into-an-elastic-pool"></a>Spostare un database in un pool flessibile

È possibile aggiungere o rimuovere i database da un pool esistente. I database possono essere altri pool. Tuttavia, è possibile aggiungere solo database disponibili nello stesso server logici.

1. Fare clic su **Configura pool**in **database flessibile** blade per il pool.

    ![Fare clic su Configura pool][1]

2. In e il **pool di configurazione** , fare clic su **Aggiungi al pool**.

    ![Fare clic su Aggiungi al pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)


3. In e il **database di Aggiungi** , selezionare il database o database che si desidera aggiungere al pool di. Fare clic su **Seleziona**.

    ![Selezionare i database da aggiungere](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    E il **pool di configurare** elenchi ora il database che si è scelto di aggiungere, con lo stato impostato su **in sospeso**.

    ![Aggiunte pool in sospeso](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. In **blade pool Configura**, fare clic su **Salva**.

    ![Fare clic su Salva](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="move-a-database-out-of-an-elastic-pool"></a>Spostare un database da un pool flessibile

1. In e il **pool di configurare** , selezionare il database o database che si desidera rimuovere.

    ![database di elenco](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Fare clic su **Rimuovi dal pool**.

    ![database di elenco](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    E il **pool di configurare** elenchi ora il database che si è scelto di essere rimosso con lo stato impostato su **in sospeso**.

    ![Anteprima database aggiunta e rimozione](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. In **blade pool Configura**, fare clic su **Salva**.

    ![Fare clic su Salva](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## <a name="change-performance-settings-of-a-pool"></a>Modificare le impostazioni delle prestazioni di un pool

Come si controlla l'utilizzo delle risorse di un pool, si potrebbe scoprire che alcune modifiche sono necessarie. Forse il pool deve una modifica in limiti delle prestazioni o lo spazio di archiviazione. Probabilmente si desidera modificare le impostazioni del database nel pool. È possibile modificare la configurazione del pool in qualsiasi momento per ottenere il miglior equilibrio di prestazioni e costi. Vedere [quando deve essere utilizzato un pool di database flessibile?](sql-database-elastic-pool-guidance.md) per ulteriori informazioni.

**Per modificare i limiti di eDTUs o lo spazio di archiviazione al pool ed eDTUs per ogni database:**

1. Aprire e il **pool di configurazione** .

    In **impostazioni del pool di database flessibile**, usare uno dei due dispositivo di scorrimento per modificare le impostazioni del pool.

    ![Utilizzo delle risorse del pool flessibile](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Quando viene modificata l'impostazione, la visualizzazione viene visualizzato il costo mensile stimato della modifica.

    ![Aggiornamento del pool e un nuovo costo mensile](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## <a name="create-and-manage-elastic-jobs"></a>Creare e gestire i processi flessibile

Processi flessibile consentono di eseguire qualsiasi numero di database nel pool di script Transact-SQL. È possibile creare nuovi processi o gestire i processi esistenti tramite il portale.

![Creare e gestire i processi flessibile][5]


Prima di utilizzare processi, installare componenti di processi flessibile e specificare le credenziali. Per ulteriori informazioni, vedere [Panoramica di processi di database flessibile](sql-database-elastic-jobs-overview.md).

Vedere [proporzioni fuori con Database di SQL Azure](sql-database-elastic-scale-introduction.md): utilizzare strumenti database flessibile per scalabilità, spostare i dati della query o creare le transazioni.



## <a name="additional-resources"></a>Risorse aggiuntive

- [Pool flessibile di Database SQL](sql-database-elastic-pool.md)
- [Creare un pool di database flessibile con il portale](sql-database-elastic-pool-create-csharp.md)
- [Creare un pool di database flessibile con PowerShell](sql-database-elastic-pool-create-powershell.md)
- [Creare un pool di database flessibile con c#](sql-database-elastic-pool-create-csharp.md)
- [Considerazioni sulla prezzo e prestazioni per i pool di database flessibile](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png
