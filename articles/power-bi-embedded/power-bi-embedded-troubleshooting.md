<properties
   pageTitle="Risoluzione dei problemi di anteprima incorporata di Microsoft Power BI"
   description="Risoluzione dei problemi di anteprima incorporata di Microsoft Power BI"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="microsoft-power-bi-embedded-preview-troubleshooting"></a>Risoluzione dei problemi di anteprima incorporata di Microsoft Power BI
In questo articolo vengono fornite le risposte per la procedura di risoluzione dei problemi di **Power BI incorporato**.

<a name="connection-string"/>
## <a name="setting-sql-server-connection-strings"></a>Impostare le stringhe di connessione di SQL Server
Per impostare una stringa di connessione di SQL Server, è necessario seguire un formato specifico. Di seguito è una stringa di connessione di esempio per SQL Server.

```
"Persist Security Info=False;Integrated Security=true;Initial Catalog=Northwind;server=(local)"
```

Per ulteriori informazioni sulle stringhe di connessione di SQL Server, vedere gli articoli seguenti:

-   [Stringhe di connessione di SQL Server](https://msdn.microsoft.com/library/jj653752.aspx)
-   [SqlConnection](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnection.connectionstring.aspx)

<a name="credentials"/>
## <a name="setting-credentials"></a>Impostare le credenziali
Nel caso in cui si dispone delle credenziali per un ambiente di sviluppo o gestione temporanea, ad esempio nome utente e password, potrebbe essere necessario aggiornare le credenziali che corrispondono a una soluzione di produzione.

## <a name="see-also"></a>Vedere anche
- [Guida introduttiva di esempio](power-bi-embedded-get-started-sample.md)
- [Che cos'è Power BI incorporato](power-bi-embedded-what-is-power-bi-embedded.md)
