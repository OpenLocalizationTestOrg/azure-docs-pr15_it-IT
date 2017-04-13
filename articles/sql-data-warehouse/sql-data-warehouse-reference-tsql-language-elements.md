<properties
   pageTitle="Elementi del linguaggio SQL dati Warehouse Transact-SQL | Microsoft Azure"
   description="Elenco di collegamenti a contenuto di riferimento per gli elementi del linguaggio Transact-SQL utilizzata per SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/08/2016"
   ms.author="barbkess;sonyama;kevin"/>

# <a name="language-elements"></a>Elementi del linguaggio

## <a name="core-elements"></a>Elementi principali

- [convenzioni di sintassi](https://msdn.microsoft.com/library/ms177563.aspx)
- [regole dei nomi di oggetti](https://msdn.microsoft.com/library/ms175874.aspx)
- [parole chiave riservate](https://msdn.microsoft.com/library/ms189822.aspx)
- [regole di confronto](https://msdn.microsoft.com/library/ff848763.aspx)
- [commenti](https://msdn.microsoft.com/library/ms181627.aspx)
- [costanti](https://msdn.microsoft.com/library/ms179899.aspx)
- [tipi di dati](https://msdn.microsoft.com/library/ms187752.aspx)
- [ESEGUIRE](https://msdn.microsoft.com/library/ms188332.aspx)
- [espressioni](https://msdn.microsoft.com/library/ms190286.aspx)
- [INTERRUZIONE](https://msdn.microsoft.com/library/ms173730.aspx)
- [Soluzione alternativa proprietà identità](https://msdn.microsoft.com/library/ms186775.aspx)
- [STAMPA](https://msdn.microsoft.com/library/ms176047.aspx)
- [UTILIZZO](https://msdn.microsoft.com/library/ms188366.aspx)

## <a name="batches-control-of-flow-and-variables"></a>Batch, il controllo di flusso e variabili

- [INIZIARE A... FINE](https://msdn.microsoft.com/library/ms190487.aspx)
- [INTERRUZIONE](https://msdn.microsoft.com/library/ms181271.aspx)
- [DICHIARARE@local_variable](https://msdn.microsoft.com/library/ms188927.aspx)
- [SE... ALTRO](https://msdn.microsoft.com/library/ms182717.aspx)
- [RAISERROR](https://msdn.microsoft.com/library/ms178592.aspx)
- [SET@local_variable](https://msdn.microsoft.com/library/ms189484.aspx)
- [GENERARE](https://msdn.microsoft.com/library/ee677615.aspx)
- [PROVARE... RILEVARE](https://msdn.microsoft.com/library/ms175976.aspx)
- [PO' DI TEMPO](https://msdn.microsoft.com/library/ms178642.aspx)

## <a name="operators"></a>Operatori
- [+ (Aggiungi)](https://msdn.microsoft.com/library/ms178565.aspx)
- [+ (Concatenazione)](https://msdn.microsoft.com/library/ms177561.aspx)
- [-(Negativo)](https://msdn.microsoft.com/library/ms189480.aspx)
- [-(Sottrazione)](https://msdn.microsoft.com/library/ms189518.aspx)
- [* (Moltiplicazione)](https://msdn.microsoft.com/library/ms176019.aspx)
- [/ (Divisione)](https://msdn.microsoft.com/library/ms175009.aspx)
- [Modulo](https://msdn.microsoft.com/library/ms190279.aspx)

## <a name="wildcard-characters-to-match"></a>Caratteri jolly in modo che corrispondano
- [= (Uguale)](https://msdn.microsoft.com/library/ms175118.aspx)
- [> (Maggiore di)](https://msdn.microsoft.com/library/ms178590.aspx)
- [< (Minore di)](https://msdn.microsoft.com/library/ms179873.aspx)
- [> = (grande maggiore o uguale a)](https://msdn.microsoft.com/library/ms181567.aspx)
- [< = (minore o uguale a)](https://msdn.microsoft.com/library/ms174978.aspx)
- [<> (Diverso da)](https://msdn.microsoft.com/library/ms176020.aspx)
- [! = (Diverso da)](https://msdn.microsoft.com/library/ms190296.aspx)
- [E](https://msdn.microsoft.com/library/ms188372.aspx)
- [TRA](https://msdn.microsoft.com/library/ms187922.aspx)
- [ESISTE](https://msdn.microsoft.com/library/ms188336.aspx)
- [IN](https://msdn.microsoft.com/library/ms177682.aspx)
- [[NON] È NULL](https://msdn.microsoft.com/library/ms188795.aspx)
- [AD ESEMPIO](https://msdn.microsoft.com/library/ms179859.aspx)
- [NON](https://msdn.microsoft.com/library/ms189455.aspx)
- [OR](https://msdn.microsoft.com/library/ms188361.aspx)

### <a name="bitwise-operators"></a>Operatori bit per bit

- [& (AND bit per bit)](https://msdn.microsoft.com/library/ms174965.aspx)
- [| (OR bit per bit)](https://msdn.microsoft.com/library/ms186714.aspx)
- [^ (Bit per bit OR esclusivo)](https://msdn.microsoft.com/library/ms190277.aspx)
- [~ (NOT bit per bit)](https://msdn.microsoft.com/library/ms173468.aspx)
- [^ = (Uguale a o esclusivo bit per bit)](https://msdn.microsoft.com/library/cc627413.aspx)
- [| = (Uguale a OR bit per bit)](https://msdn.microsoft.com/library/cc627409.aspx)
- [& = (uguale AND bit per bit)](https://msdn.microsoft.com/library/cc627427.aspx)

## <a name="functions"></a>Funzioni

- [@@DATEFIRST](https://msdn.microsoft.com/library/ms187766.aspx)
- [@@ERROR](https://msdn.microsoft.com/library/ms188790.aspx)
- [@@LANGUAGE](https://msdn.microsoft.com/library/ms177557.aspx)
- [@@SPID](https://msdn.microsoft.com/library/ms189535.aspx)
- [@@TRANCOUNT](https://msdn.microsoft.com/library/ms187967.aspx)
- [@@VERSION](https://msdn.microsoft.com/library/ms177512.aspx)
- [ASS](https://msdn.microsoft.com/library/ms189800.aspx)
- [ARCCOS](https://msdn.microsoft.com/library/ms178627.aspx)
- [ASCII](https://msdn.microsoft.com/library/ms177545.aspx)
- [ARCSEN](https://msdn.microsoft.com/library/ms181581.aspx)
- [ARCTAN](https://msdn.microsoft.com/library/ms181746.aspx)
- [ATN2](https://msdn.microsoft.com/library/ms173854.aspx)
- [BINARY_CHECKSUM](https://msdn.microsoft.com/library/ms173784.aspx)
- [MAIUSCOLE/MINUSCOLE](https://msdn.microsoft.com/library/ms181765.aspx)
- [CAST e CONVERT](https://msdn.microsoft.com/library/ms187928.aspx)
- [ARROTONDA](https://msdn.microsoft.com/library/ms189818.aspx)
- [CARATT](https://msdn.microsoft.com/library/ms187323.aspx)
- [CHARINDEX](https://msdn.microsoft.com/library/ms186323.aspx)
- [CHECKSUM](https://msdn.microsoft.com/library/ms189788.aspx)
- [RIUNIRE](https://msdn.microsoft.com/library/ms190349.aspx)
- [COL_NAME](https://msdn.microsoft.com/library/ms174974.aspx)
- [COLLATIONPROPERTY](https://msdn.microsoft.com/library/ms190305.aspx)
- [CONCATENA](https://msdn.microsoft.com/library/hh231515.aspx)
- [COS](https://msdn.microsoft.com/library/ms188919.aspx)
- [COT](https://msdn.microsoft.com/library/ms188921.aspx)
- [CONTA. NUMERI](https://msdn.microsoft.com/library/ms175997.aspx)
- [COUNT_BIG](https://msdn.microsoft.com/library/ms190317.aspx)
- [CUME_DIST](https://msdn.microsoft.com/library/hh231078.aspx)
- [CURRENT_TIMESTAMP](https://msdn.microsoft.com/library/ms188751.aspx)
- [CURRENT_USER](https://msdn.microsoft.com/library/ms176050.aspx)
- [DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx)
- [DATALENGTH](https://msdn.microsoft.com/library/ms173486.aspx)
- [DATEADD](https://msdn.microsoft.com/library/ms186819.aspx)
- [DATEDIFF](https://msdn.microsoft.com/library/ms189794.aspx)
- [FUNZIONE DATEFROMPARTS](https://msdn.microsoft.com/library/hh213228.aspx)
- [DATENAME](https://msdn.microsoft.com/library/ms174395.aspx)
- [DATEPART](https://msdn.microsoft.com/library/ms174420.aspx)
- [DATETIME2FROMPARTS](https://msdn.microsoft.com/library/hh213312.aspx)
- [DATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213233.aspx)
- [DATETIMEOFFSETFROMPARTS](https://msdn.microsoft.com/library/hh231077.aspx)
- [GIORNO](https://msdn.microsoft.com/library/ms176052.aspx)
- [DB_ID](https://msdn.microsoft.com/library/ms186274.aspx)
- [DB_NAME](https://msdn.microsoft.com/library/ms189753.aspx)
- [GRADI](https://msdn.microsoft.com/library/ms178566.aspx)
- [DENSE_RANK](https://msdn.microsoft.com/library/ms173825.aspx)
- [DIFFERENZA](https://msdn.microsoft.com/library/ms188753.aspx)
- [MESE](https://msdn.microsoft.com/library/hh213020.aspx)
- [ERROR_MESSAGE](https://msdn.microsoft.com/library/ms190358.aspx)
- [NUMERO_ERRORE](https://msdn.microsoft.com/library/ms175069.aspx)
- [ERROR_PROCEDURE](https://msdn.microsoft.com/library/ms188398.aspx)
- [ERROR_SEVERITY](https://msdn.microsoft.com/library/ms178567.aspx)
- [ERROR_STATE](https://msdn.microsoft.com/library/ms180031.aspx)
- [EXP](https://msdn.microsoft.com/library/ms179857.aspx)
- [FIRST_VALUE](https://msdn.microsoft.com/library/hh213018.aspx)
- [ARROTONDA](https://msdn.microsoft.com/library/ms178531.aspx)
- [GETDATE](https://msdn.microsoft.com/library/ms188383.aspx)
- [GETUTCDATE](https://msdn.microsoft.com/library/ms178635.aspx)
- [HAS_DBACCESS](https://msdn.microsoft.com/library/ms187718.aspx)
- [HASHBYTES](https://msdn.microsoft.com/library/ms174415.aspx)
- [INDEXPROPERTY](https://msdn.microsoft.com/library/ms187729.aspx)
- [ISDATE](https://msdn.microsoft.com/library/ms187347.aspx)
- [ISNULL](https://msdn.microsoft.com/library/ms184325.aspx)
- [ISNUMERIC](https://msdn.microsoft.com/library/ms186272.aspx)
- [RITARDO](https://msdn.microsoft.com/library/hh231256.aspx)
- [LAST_VALUE](https://msdn.microsoft.com/library/hh231517.aspx)
- [CLIENTE POTENZIALE](https://msdn.microsoft.com/library/hh213125.aspx)
- [A SINISTRA](https://msdn.microsoft.com/library/ms177601.aspx)
- [LUNGHEZZA](https://msdn.microsoft.com/library/ms190329.aspx)
- [LOG](https://msdn.microsoft.com/library/ms190319.aspx)
- [LOG10](https://msdn.microsoft.com/library/ms175121.aspx)
- [INFERIORE](https://msdn.microsoft.com/library/ms174400.aspx)
- [FUNZIONI LTRIM](https://msdn.microsoft.com/library/ms177827.aspx)
- [MAX](https://msdn.microsoft.com/library/ms187751.aspx)
- [MIN](https://msdn.microsoft.com/library/ms179916.aspx)
- [MESE](https://msdn.microsoft.com/library/ms187813.aspx)
- [NCHAR](https://msdn.microsoft.com/library/ms182673.aspx)
- [NTILE](https://msdn.microsoft.com/library/ms175126.aspx)
- [NULLIF](https://msdn.microsoft.com/library/ms177562.aspx)
- [OBJECT_ID](https://msdn.microsoft.com/library/ms190328.aspx)
- [NOME_OGGETTO](https://msdn.microsoft.com/library/ms186301.aspx)
- [OBJECTPROPERTY](https://msdn.microsoft.com/library/ms176105.aspx)
- [OIBJECTPROPERTYEX](https://msdn.microsoft.com/library/ms188390.aspx)
- [Funzioni scalari ODBCS](https://msdn.microsoft.com/library/bb630290.aspx)
- [RISPETTO a clausola](https://msdn.microsoft.com/library/ms189461.aspx)
- [PARSENAME](https://msdn.microsoft.com/library/ms188006.aspx)
- [PATINDEX](https://msdn.microsoft.com/library/ms188395.aspx)
- [PERCENTILE_CONT](https://msdn.microsoft.com/library/hh231473.aspx)
- [PERCENTILE_DISC](https://msdn.microsoft.com/library/hh231327.aspx)
- [PERCENT_RANK](https://msdn.microsoft.com/library/hh213573.aspx)
- [PI GRECO](https://msdn.microsoft.com/library/ms189512.aspx)
- [POTENZA](https://msdn.microsoft.com/library/ms174276.aspx)
- [QUOTENAME](https://msdn.microsoft.com/library/ms176114.aspx)
- [RADIANTI](https://msdn.microsoft.com/library/ms189742.aspx)
- [CASUALE](https://msdn.microsoft.com/library/ms177610.aspx)
- [RANGO](https://msdn.microsoft.com/library/ms176102.aspx)
- [SOSTITUISCI](https://msdn.microsoft.com/library/ms186862.aspx)
- [LA REPLICA](https://msdn.microsoft.com/library/ms174383.aspx)
- [INVERTIRE](https://msdn.microsoft.com/library/ms180040.aspx)
- [Ok](https://msdn.microsoft.com/library/ms177532.aspx)
- [ARROTONDA](https://msdn.microsoft.com/library/ms175003.aspx)
- [ROW_NUMBER](https://msdn.microsoft.com/library/ms186734.aspx)
- [RTRIM](https://msdn.microsoft.com/library/ms178660.aspx)
- [SCHEMA_ID](https://msdn.microsoft.com/library/ms188797.aspx)
- [NOME_SCHEMA](https://msdn.microsoft.com/library/ms175068.aspx)
- [SERVERPROPERTY](https://msdn.microsoft.com/library/ms174396.aspx)
- [SESSION_USER](https://msdn.microsoft.com/library/ms177587.aspx)
- [SEGNO](https://msdn.microsoft.com/library/ms188420.aspx)
- [SEN](https://msdn.microsoft.com/library/ms188377.aspx)
- [SMALLDATETIMEFROMPARTS](https://msdn.microsoft.com/library/hh213396.aspx)
- [SOUNDEX](https://msdn.microsoft.com/library/ms187384.aspx)
- [SPAZIO](https://msdn.microsoft.com/library/ms187950.aspx)
- [SQL_VARIANT_PROPERTY](https://msdn.microsoft.com/library/ms178550.aspx)
- [RADQ](https://msdn.microsoft.com/library/ms176108.aspx)
- [QUADRATO](https://msdn.microsoft.com/library/ms173569.aspx)
- [STATS_DATE](https://msdn.microsoft.com/library/ms190330.aspx)
- [FUNZIONE STDEV](https://msdn.microsoft.com/library/ms190474.aspx)
- [DEV.ST.POP](https://msdn.microsoft.com/library/ms176080.aspx)
- [STR](https://msdn.microsoft.com/library/ms189527.aspx)
- [OGGETTI](https://msdn.microsoft.com/library/ms188043.aspx)
- [SOTTOSTRINGA](https://msdn.microsoft.com/library/ms187748.aspx)
- [SOMMA](https://msdn.microsoft.com/library/ms187810.aspx)
- [SUSER_SNAME](https://msdn.microsoft.com/library/ms174427.aspx)
- [SWITCHOFFSET](https://msdn.microsoft.com/library/bb677244.aspx)
- [SYSDATETIME](https://msdn.microsoft.com/library/bb630353.aspx)
- [SYSDATETIMEOFFSET](https://msdn.microsoft.com/library/bb677334.aspx)
- [SYSTEM_USER](https://msdn.microsoft.com/library/ms179930.aspx)
- [SYSUTCDATETIME](https://msdn.microsoft.com/library/bb630387.aspx)
- [TAN](https://msdn.microsoft.com/library/ms190338.aspx)
- [TERTIARY_WEIGHTS](https://msdn.microsoft.com/library/ms186881.aspx)
- [FUNZIONE TIMEFROMPARTS](https://msdn.microsoft.com/library/hh213398.aspx)
- [TODATETIMEOFFSET](https://msdn.microsoft.com/library/bb630335.aspx)
- [TYPE_ID](https://msdn.microsoft.com/library/ms181628.aspx)
- [TYPE_NAME](https://msdn.microsoft.com/library/ms189750.aspx)
- [TYPEPROPERTY](https://msdn.microsoft.com/library/ms188419.aspx)
- [UNICODE](https://msdn.microsoft.com/library/ms180059.aspx)
- [SUPERIORE](https://msdn.microsoft.com/library/ms180055.aspx)
- [UTENTE](https://msdn.microsoft.com/library/ms186738.aspx)
- [NOME_UTENTE](https://msdn.microsoft.com/library/ms188014.aspx)
- [VAR](https://msdn.microsoft.com/library/ms186290.aspx)
- [VAR](https://msdn.microsoft.com/library/ms188735.aspx)
- [ANNO](https://msdn.microsoft.com/library/ms186313.aspx)
- [XACT_STATE](https://msdn.microsoft.com/library/ms189797.aspx)

## <a name="transactions"></a>Transazioni

- [transazioni](https://msdn.microsoft.com/library/mt204031.aspx)

## <a name="diagnostic-sessions"></a>Sessioni di diagnostiche

- [CREARE UNA SESSIONE DI DIAGNOSTICA](https://msdn.microsoft.com/library/mt204029.aspx)

## <a name="procedures"></a>Procedure

- [sp_addrolemember](https://msdn.microsoft.com/library/ms187750.aspx)
- [sp_columns](https://msdn.microsoft.com/library/ms176077.aspx)
- [sp_configure](https://msdn.microsoft.com/library/ms188787.aspx)
- [sp_datatype_info_90](https://msdn.microsoft.com/library/mt204014.aspx)
- [sp_droprolemember](https://msdn.microsoft.com/library/ms188369.aspx)
- [sp_execute](https://msdn.microsoft.com/library/ff848746.aspx)
- [sp_executesql](https://msdn.microsoft.com/library/ms188001.aspx)
- [sp_fkeys](https://msdn.microsoft.com/library/ms175090.aspx)
- [sp_pdw_add_network_credentials](https://msdn.microsoft.com/library/mt204011.aspx)
- [sp_pdw_database_encryption](https://msdn.microsoft.com/library/mt219360.aspx)
- [sp_pdw_database_encryption_regenerate_system_keys](https://msdn.microsoft.com/library/mt204033.aspx)
- [sp_pdw_log_user_data_masking](https://msdn.microsoft.com/library/mt204023.aspx)
- [sp_pdw_remove_network_credentials](https://msdn.microsoft.com/library/mt204038.aspx)
- [sp_pkeys](https://msdn.microsoft.com/library/ms189813.aspx)
- [sp_prepare](https://msdn.microsoft.com/library/ff848808.aspx)
- [sp_spaceused](https://msdn.microsoft.com/library/ms188776.aspx)
- [sp_special_columns_100](https://msdn.microsoft.com/library/mt204025.aspx)
- [viene](https://msdn.microsoft.com/library/ms182705.aspx)
- [sp_statistics](https://msdn.microsoft.com/library/ms173842.aspx)
- [sp_tables](https://msdn.microsoft.com/library/ms186250.aspx)
- [sp_unprepare](https://msdn.microsoft.com/library/ff848735.aspx)



## <a name="set-statements"></a>Istruzioni SET

- [SET ANSI_DEFAULTS](https://msdn.microsoft.com/library/ms188340.aspx)
- [ALTRI SET](https://msdn.microsoft.com/library/ms187356.aspx)
- [SET ANSI_NULL_DFLT_ON](https://msdn.microsoft.com/library/ms187375.aspx)
- [SET ANSI_NULLS](https://msdn.microsoft.com/library/ms188048.aspx)
- [SET ANSI_PADDING](https://msdn.microsoft.com/library/ms187403.aspx)
- [SET ANSI_WARNINGS](https://msdn.microsoft.com/library/ms190368.aspx)
- [SET ARITHABORT](https://msdn.microsoft.com/library/ms190306.aspx)
- [SET ARITHIGNORE](https://msdn.microsoft.com/library/ms184341.aspx)
- [SET CONCAT_NULL_YIELDS_NULL](https://msdn.microsoft.com/library/ms176056.aspx)
- [SET DATEFIRST](https://msdn.microsoft.com/library/ms181598.aspx)
- [IMPOSTARE FORMATODATA](https://msdn.microsoft.com/library/ms189491.aspx)
- [SET FMTONLY](https://msdn.microsoft.com/library/ms173839.aspx)
- [SET IMPLICIT_TRANSACITONS](https://msdn.microsoft.com/library/ms187807.aspx)
- [SET LOCK_TIMEOUT](https://msdn.microsoft.com/library/ms189470.aspx)
- [SET NUMBERIC_ROUNDABORT](https://msdn.microsoft.com/library/ms188791.aspx)
- [SET QUOTED_IDENTIFIER](https://msdn.microsoft.com/library/ms174393.aspx)
- [CONTEGGIO DELLE RIGHE SET](https://msdn.microsoft.com/library/ms188774.aspx)
- [SET TEXTSIZE](https://msdn.microsoft.com/library/ms186238.aspx)
- [LIVELLO DI ISOLAMENTO TRANSAZIONE SET](https://msdn.microsoft.com/library/ms173763.aspx)
- [SET XACT_ABORT](https://msdn.microsoft.com/library/ms188792.aspx)


## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Panoramica di riferimento SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Panoramica di riferimento SQL Data Warehouse]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->
