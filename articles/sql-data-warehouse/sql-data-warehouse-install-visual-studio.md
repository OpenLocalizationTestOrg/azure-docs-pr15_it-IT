<properties
   pageTitle="Installare Visual Studio e SSDT per SQL Data Warehouse | Microsoft Azure"
   description="Installare Visual Studio e strumenti di sviluppo di SQL Server (SSDT) per SQL Azure Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="sonyama;barbkess"/>

# <a name="install-visual-studio-2015-and-ssdt-for-sql-data-warehouse"></a>Installare Visual Studio 2015 e SSDT per SQL Data Warehouse

Per lo sviluppo di applicazioni per SQL Data Warehouse, è consigliabile utilizzare Visual Studio 2015 con la versione più recente di SQL Server Data Tools (SSDT).  Visual Studio 2013 aggiornamento 5 con SSDT è supportata anche per garantire la compatibilità.  

Utilizzare Visual Studio con SSDT consentono di utilizzare Esplora oggetti di SQL Server per visivamente esplorare tabelle, viste, stored procedure e molti altri oggetti in un Data Warehouse SQL, nonché eseguire query.

> [AZURE.NOTE] SQL Data Warehouse non supporta ancora progetti di Visual Studio Database.  Questa caratteristica verrà aggiunto in una versione futura.

## <a name="step-1-install-visual-studio-2015"></a>Passaggio 1: Installare Visual Studio 2015

Seguire questi collegamenti per scaricare e installare Visual Studio 2015. Se si dispone già di Visual Studio 2013 o 2015 installato, è possibile andare al passaggio 2, installare SSDT.

1. [Scaricare Visual Studio 2015][].
2. Seguire la Guida di [Installazione di Visual Studio][] su MSDN e scegliere le configurazioni predefinite.

## <a name="step-2-install-ssdt"></a>Passaggio 2: Installare SSDT

Per installare SSDT per Visual Studio è sufficiente cercare aggiornamento SSDT dall'interno di Visual Studio attenendosi alla procedura seguente.

1. In Visual Studio fare clic su **Strumenti** / **Extensions e aggiornamenti...**  /  **Aggiornamenti**
2. Selezionare **Gli aggiornamenti** e quindi cercare **l'aggiornamento di Microsoft SQL Server per database utensili**

Se un aggiornamento non viene trovato, quindi è la versione più recente installata.  Per confermare la SSDT è installato, fare clic su **Guida** / **Su Microsoft Visual Studio** e cercare SQL Server Data Tools nell'elenco.  L'ultima versione di SSDT è 14.0.60525.0.  Se l'opzione per l'installazione non è disponibile da Visual Studio, in alternativa è possibile visitare la pagina [SSDT Download][] per scaricare e installare SSDT manualmente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato l'ultima versione di SSDT, si è pronti per [connettersi][] alla warehouse di dati SQL.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[la connessione]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Scaricare Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Installazione di Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Download SSDT]: https://msdn.microsoft.com/library/mt204009.aspx
