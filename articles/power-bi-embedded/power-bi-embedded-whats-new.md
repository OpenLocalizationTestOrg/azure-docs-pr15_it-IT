<properties
   pageTitle="Novità di Power BI incorporata"
   description="Ottenere informazioni aggiornate sui quali sono le novità di Power BI incorporata"
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

# <a name="whats-new-in-power-bi-embedded"></a>Novità di Power BI incorporata

Gli aggiornamenti di **Power BI incorporata** vengono rilasciati a intervalli regolari. Tuttavia, non ogni versione include nuove funzionalità di utente rivolta; alcune versioni riguardano la funzionalità di servizio back-end. È necessario evidenziare nuove funzionalità di esposti all'utente di seguito. Assicurarsi di controllare spesso.

## <a name="august-31st-2016"></a>31 agosto 2016

In questa versione, inclusi:

- Tutti i nuovi SDK JavaScript che supporta [il filtro avanzato e l'esplorazione pagine](power-bi-embedded-interact-with-reports.md).
- Power BI incorporata è attualmente supportata in Data Center del Canada centrale. Controllare [lo stato di Data Center](https://azure.microsoft.com/status/).

## <a name="july-11th-2016"></a>11 luglio 2016

In questa versione, inclusi:

-    **Novità!** Il servizio Power BI incorporato non è più in anteprima - è ora GA (in genere disponibile).  
-    Tutte le API REST sono state spostate da **/beta** a **/v1.0**.
-    .NET e JavaScript SDK è stato aggiornato per **versione 1.0**.
-    Power BI API chiamate possono essere autenticate ora direttamente utilizzando i tasti di API. I token di App sono necessari solo per l'incorporamento. Durante il processo, i token di provisioning e dev sono deprecati nella versione 1.0 API, ma verrà continuano a funzionare nella versione beta fino a 30/12/2016. Per ulteriori informazioni, vedere [Authenticating e Authorizing con Power BI incorporato](power-bi-embedded-app-token-flow.md).
-    Supporto di protezione a livello (RLS) riga per i token di app e report incorporati. Per ulteriori informazioni, vedere [protezione a livello di riga con Power BI incorporato](power-bi-embedded-rls.md).
-    Applicazione di esempio per tutte le chiamate **v 1.0** API aggiornata.
-    Power BI incorporato il supporto Azure SDK, PowerShell e CLI.
-    È possibile esportare dati di visualizzazione in un **file CSV**.
-    Power BI incorporata è attualmente supportata in tutte le stesse lingue/impostazioni internazionali come Microsoft Azure. Per ulteriori informazioni, vedere [Azure - lingue](http://social.technet.microsoft.com/wiki/contents/articles/4234.windows-azure-extent-of-localization.aspx).
