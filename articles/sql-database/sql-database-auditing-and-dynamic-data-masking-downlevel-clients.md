<properties
    pageTitle="Client di livello inferiore di Database SQL di supporto ed endpoint IP impostato per il controllo | Microsoft Azure"
    description="Informazioni sui Database SQL client supporto e IP modifiche endpoint per il controllo."
    services="sql-database"
    documentationCenter=""
    authors="ronitr"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/10/2016"
    ms.author="ronitr"/>

# <a name="sql-database----downlevel-clients-support-and-ip-endpoint-changes-for-auditing"></a>Database SQL - supporto per i client e sulle modifiche apportate all'endpoint IP per il controllo


[Controllo](sql-database-auditing-get-started.md) funziona automaticamente con i clienti SQL che supporta il reindirizzamento td.


##<a id="subheading-1"></a>Supporto per i clienti

Qualsiasi client t:System.Runtime.Serialization.XmlObjectSerializer td 7.4 deve supportare anche il reindirizzamento. Le eccezioni a questa includono JDBC 4.0 in cui la caratteristica di reindirizzamento non è completamente supportata e Tedious per Node in cui il reindirizzamento non è stata implementata.

"Client di livello inferiore", ovvero quali supporto td 7.3 e sotto - al server FQDN nella connessione stringa versione deve essere modificato:

Server originale FQDN nella stringa di connessione: <*nome server*>. database.windows.net

Server modificato FQDN nella stringa di connessione: <*nome server*> .database. **sicura**. windows.net

Include un elenco parziale di "Client di livello inferiore":

- .NET 4.0 e versioni precedenti,
- ODBC 10.0 e riportata di seguito.
- JDBC (mentre JDBC supportano 7.4 td, la caratteristica di reindirizzamento td non è completamente supportata)
- Noiosa (per Node)

**Nota:** Il server modifica FDQN può essere utile anche per l'applicazione dei criteri il controllo di SQL Server livello senza la necessità di un passaggio di configurazione per ogni database (attenuazione temporaneo).

##<a id="subheading-2"></a>Endpoint IP viene modificato quando si Abilita controllo

Si noti che, quando si Abilita controllo, vengono modificati endpoint IP del database. Se si dispone di impostazioni del firewall restrittive, aggiornare le impostazioni del firewall di conseguenza.

Il nuovo endpoint IP database dipenderà area database:

| Area database | Possibili endpoint IP |
|----------|---------------|
| America del Nord Cina  | 139.217.29.176, 139.217.28.254 |
| Cina est  | 42.159.245.65, 42.159.246.245 |
| Australia orientale  | 104.210.91.32, 40.126.244.159, 191.239.64.60, 40.126.255.94 |
| Australia sudorientale | 191.239.184.223, 40.127.85.81, 191.239.161.83, 40.127.81.130 |
| Brasile sud  | 104.41.44.161, 104.41.62.230, 23.97.99.54, 104.41.59.191 |
| Centrale USA  | 104.43.255.70, 40.83.14.7, 23.99.128.244, 40.83.15.176 |
| Asia orientale   | 23.99.125.133, 13.75.40.42, 23.97.71.138, 13.94.43.245 |
| Stati Uniti orientali 2 | 104.209.141.31, 104.208.238.177, 191.237.131.51, 104.208.235.50 |
| Stati Uniti orientali   | 23.96.107.223, 104.41.150.122, 23.96.38.170, 104.41.146.44 |
| India centrale  | 104.211.98.219, 104.211.103.71 |
| India sud   | 104.211.227.102, 104.211.225.157 |
| India ovest  | 104.211.161.152, 104.211.162.21 |
| Giappone est   | 104.41.179.1, 40.115.253.81, 23.102.64.207, 40.115.250.196 |
| Giappone ovest    | 104.214.140.140, 104.214.146.31, 191.233.32.34, 104.214.146.198 |
| America del Nord centrale USA  | 191.236.155.178, 23.96.192.130, 23.96.177.169, 23.96.193.231 |
| Europa Nord America  | 104.41.209.221, 40.85.139.245, 137.116.251.66, 40.85.142.176 |
| Sud centrale USA  | 191.238.184.128, 40.84.190.84, 23.102.160.153, 40.84.186.66 |
| Area Asia sudorientale  | 104.215.198.156, 13.76.252.200, 23.97.51.109, 13.76.252.113 |
| Europa occidentale  | 104.40.230.120, 13.80.23.64, 137.117.171.161, 13.80.8.37, 104.47.167.215, 40.118.56.193, 104.40.176.73, 40.118.56.20 |
| Usa ovest  | 191.236.123.146, 138.91.163.240, 168.62.194.148, 23.99.6.91 |
| Canada centrale  | 13.88.248.106 |
| Canada orientale  |  40.86.227.82 |
