<properties
   pageTitle="Connessioni DataSource | Microsoft Azure"
   description="Descrive connessioni all'origine dati per i modelli di dati di Azure Analysis Services."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="datasource-connections"></a>Connessioni di origine dati

Modelli di dati di Azure Analysis Services possono richiedere diversi provider di dati quando ci si connette a determinate origini dati. In alcuni casi, modelli tabulari di connessione a origini dati tramite provider nativi, ad esempio SQL Server Native Client (SQLNCLI11) potrebbero restituire un errore.

Ad esempio; Se si dispone di una in memoria o Query diretta del modello di dati che si connette a un'origine dati cloud, ad esempio il Database di SQL Azure, se si usa un provider nativi diversa da quella SQLOLEDB, potrebbe essere visualizzato il messaggio di errore: **"Il provider 'SQLNCLI11.1' non è registrato"**.

Oppure, se si dispone di un modello di DirectQuery la connessione a origini dati locali, se si utilizza provider nativi venga visualizzato il messaggio di errore: **"Errore durante la creazione di set di righe OLE DB. Sintassi errata vicino 'Limite' "**.

## <a name="data-source-providers"></a>Provider delle origini dati

Provider di origine dati seguenti sono supportati per in memoria o per indicare a usare i modelli di Query quando ci si connette a locale o nel cloud origini dati:

|               | **Origine dati**                     | **In memoria**                            |  **Query diretta**                                           |
|---------------------------|-------------------------------|---------------------------------------------|---------------------------------------------|
| **Cloud**                     | SQL Azure Data Warehouse      | Provider di dati .NET framework per SQL Server | Provider di dati .NET framework per SQL Server |
|                           | Database SQL Azure            | Provider di dati .NET framework per SQL Server | Provider di dati .NET framework per SQL Server |
| **Locale** (tramite Gateway) | SQL Server                    | SQL Server Native Client 11.0               | Provider di dati .NET framework per SQL Server |
|                           |  SQL Server                             | Provider Microsoft OLE DB per SQL Server    |   Provider di dati .NET framework per SQL Server                                          |
|                           |  SQL Server                             | Provider di dati .NET framework per SQL Server |  Provider di dati .NET framework per SQL Server                                           |
|                           | Oracle                        | Provider Microsoft OLE DB per Oracle        | Provider di dati Oracle per .NET               |
|                           |  Oracle                             | Provider di dati Oracle per .NET               | Provider di dati Oracle per .NET                                            |
|                           | Teradata                      | Provider OLE DB per Teradata                | Provider di dati di Teradata per .NET             |
|                           |  Teradata                             | Provider di dati di Teradata per .NET             |  Provider di dati di Teradata per .NET                                            |
|                           | Sistema di piattaforma Analitica | Provider di dati .NET framework per SQL Server | Provider di dati .NET framework per SQL Server |


> [AZURE.NOTE] Assicurarsi che siano installati provider 64 bit quando si usa Gateway locale.

Durante la migrazione di un modello tabulare di SQL Server Analysis Services in locale a Azure Analysis Services, potrebbe essere necessario modificare il provider.

**Per specificare un provider di origine dati**

1. In SSDT > **Esplora modelli tabulari** > **Origini dati**, pulsante destro del mouse una connessione all'origine dati e quindi fare clic su **Modifica origine dati**.

2. In **Modifica connessione**fare clic su **Impostazioni avanzate** per aprire la finestra delle proprietà avanzate.

3. In **Impostare proprietà avanzate** > **provider**e quindi selezionare il provider appropriato.

## <a name="impersonation"></a>Rappresentazione
In alcuni casi, potrebbe essere necessario specificare un account di rappresentazione diverse. È possibile specificare l'account di rappresentazione SSDT o SQL Server Management Studio.

Per le origini dati locali:

- Se mediante l'autenticazione di SQL, la rappresentazione dovrebbe essere degli Account del servizio.
- Se mediante l'autenticazione di Windows, impostare password/utente di Windows. Per SQL Server, l'autenticazione di Windows con un account di rappresentazione specifici è supportata solo per i modelli di dati in memoria.

Per le origini dati cloud:

- Se mediante l'autenticazione di SQL, la rappresentazione dovrebbe essere degli Account del servizio.


## <a name="next-steps"></a>Passaggi successivi

Se si dispone di origini dati locali, assicurarsi di installare il [gateway locale](analysis-services-gateway.md). Per ulteriori informazioni sulla gestione del server in SSDT o SQL Server Management Studio, vedere [gestire il server](analysis-services-manage.md).
