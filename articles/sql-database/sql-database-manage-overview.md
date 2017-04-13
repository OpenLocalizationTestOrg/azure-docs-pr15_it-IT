<properties
    pageTitle="Panoramica: strumenti di Database SQL | Microsoft Azure"
    description="Confronta strumenti e opzioni per la gestione di Database SQL Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="sstein"/>

# <a name="overview-management-tools-for-sql-database"></a>Panoramica: strumenti di Database SQL

In questo argomento illustra e confronta strumenti e opzioni per la gestione di database SQL Azure, pertanto è possibile selezionare quello più adatto per il processo, l'azienda e si. Scegliere quello più adatto dipende il numero di database si gestiscono, l'attività e con quale frequenza viene eseguita un'attività.

## <a name="azure-portal"></a>Portale di Azure

Il [portale di Azure](https://portal.azure.com) è un'applicazione basata sul web in cui è possibile creare, aggiornare ed Elimina database e server logici e monitorare l'attività di database. Questo strumento è ideale se si ha ancora esperienza nel con Azure, la gestione di alcuni database, o necessario eseguire rapidamente.

Per ulteriori informazioni sull'uso del portale, vedere [gestione di database SQL tramite il portale di Azure](sql-database-manage-portal.md).

## <a name="sql-server-management-studio-and-sql-server-data-tools-in-visual-studio"></a>SQL Server Management Studio e SQL Server Data Tools in Visual Studio

SQL Server Management Studio (Express) e SQL Server Data Tools (SSDT) sono strumenti client che eseguono nel computer in uso per la gestione e lo sviluppo di database nel cloud. Se si usa uno sviluppatore di applicazioni familiarità con Visual Studio o in altri ambienti di sviluppo integrato (IDE), [provare a utilizzare SSDT in Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Molti amministratori di database hanno familiari con SQL Server Management Studio, che può essere utilizzata con i database SQL Azure. [Scaricare l'ultima versione di SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e Usa sempre la versione più recente quando si lavora con Database di SQL Azure. Per ulteriori informazioni sulla gestione dei database di SQL Azure con SQL Server Management Studio, vedere [gestire SQL database con SQL Server Management Studio](sql-database-manage-azure-ssms.md).

> [AZURE.IMPORTANT] Utilizzare sempre l'ultima versione di [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) per rimanere sincronizzati con gli aggiornamenti a Microsoft Azure e Database SQL.


## <a name="powershell"></a>PowerShell

È possibile usare PowerShell per gestire i database e pool di database flessibile e per automatizzare le distribuzioni di Azure delle risorse. Si consiglia di questo strumento per la gestione di un numero elevato di database e automazione di distribuzione e modifiche delle risorse in un ambiente di produzione.

Per ulteriori informazioni, vedere [Gestione di Database SQL con PowerShell](sql-database-manage-powershell.md)

## <a name="elastic-database-tools"></a>Strumenti di Database flessibile
Usare gli strumenti di database flessibile per eseguire azioni quali 

* Esecuzione di uno script T-SQL rispetto a un set di database mediante un [processo flessibile](sql-database-elastic-jobs-overview.md)
* Spostare i database modello multi-tenant a un modello singolo tenant con lo [strumento di unione di divisione](sql-database-elastic-scale-overview-split-and-merge.md)
* Gestire i database in un modello singolo tenant o un multi-tenant utilizzando la [libreria client scalabilità flessibile](sql-database-elastic-database-client-library.md).
 

## <a name="additional-resources"></a>Risorse aggiuntive

- [Gestione risorse di Azure](https://azure.microsoft.com/features/resource-manager/)
- [Automazione Azure](https://azure.microsoft.com/documentation/services/automation/)
- [Utilità di pianificazione Azure](https://azure.microsoft.com/documentation/services/scheduler/)