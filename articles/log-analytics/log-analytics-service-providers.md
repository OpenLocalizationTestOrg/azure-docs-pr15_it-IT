<properties
    pageTitle="Accedere Analitica caratteristiche per i provider di servizi | Microsoft Azure"
    description="Registro Analitica può contribuire a gestite servizio (msp), grandi aziende, fornitori di software indipendenti (indipendenti) e provider di servizi di hosting gestire e monitorare i server in locale del cliente o infrastruttura cloud."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Registro Analitica caratteristiche per i provider di servizi

Registro Analitica consente di servizi gestiti (msp), grandi aziende, produttori software indipendenti () e provider di servizi hosting gestire e monitorare i server in locale del cliente o infrastruttura cloud. 

Grandi aziende condividano analogie molti provider di servizi, soprattutto quando un team IT centralizzato che è responsabile della gestione IT per molti diverse business unit. Per semplicità, questo documento utilizza il termine *provider di servizi* , ma la stessa funzionalità è disponibile per le aziende e gli altri utenti.

## <a name="cloud-solution-provider"></a>Provider di soluzioni cloud

Per i partner e provider di servizi che fanno parte della finestra del programma [Cloud soluzione Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , Log Analitica corrisponde a uno dei servizi Azure disponibili in un abbonamento CSP. 

Le seguenti funzionalità sono abilitate per Log Analitica, negli abbonamenti a *Provider di soluzioni Cloud* .

Come *Provider di soluzioni Cloud* , è possibile:

+ Creare aree di lavoro Analitica Log nell'abbonamento a un tenant (cliente).
+ Aree di lavoro Access autore tenant. 
+ Aggiungere e rimuovere l'accesso degli utenti nell'area di lavoro tramite la gestione degli utenti Azure. Quando nell'area di lavoro del tenant nel portale di OMS pagina Gestione degli utenti in impostazioni non è disponibile
  - Registro Analitica non supporta l'accesso basato sui ruoli ancora, concessione a un utente `reader` autorizzazione nel portale di Azure consente di apportare modifiche alla configurazione nel portale di OMS

Per accedere all'abbonamento del tenant, è necessario specificare l'identificatore tenant. L'identificatore tenant è spesso quest'ultima parte dell'indirizzo di posta elettronica usato per accedere.

+ Nel portale OMS aggiungere `?tenant=contoso.com` l'URL per il portale. Per esempio`mms.microsoft.com/?tenant=contoso.com`
+ In PowerShell, utilizzare la `-Tenant contoso.com` parametro quando si usa `Add-AzureRmAccount` cmdlet
+ L'identificatore tenant viene aggiunto automaticamente quando si utilizza il `OMS portal` collegamento dal portale di Azure per aprire e accedere al portale di OMS per area di lavoro selezionata

In un *cliente* di un Provider di soluzioni Cloud è possibile:

+ Creare file di log delle aree di lavoro analitica in una sottoscrizione CSP
+ Aree di lavoro di Access creati dal CSP
  -  Utilizzare la `OMS portal` collegamento dal portale di Azure per aprire e accedere al portale di OMS per area di lavoro selezionata
+ Visualizzare e utilizzare la pagina di gestione di utenti in impostazioni nel portale di OMS

>[AZURE.NOTE] Soluzioni di Backup e il ripristino del sito per Analitica registro non sono in grado di connettersi a un archivio di servizi di recupero e non possono essere configurate in una sottoscrizione CSP.

## <a name="managing-multiple-customers-using-log-analytics"></a>Gestire più clienti utilizzando Analitica Log 

È consigliabile creare un'area di lavoro Analitica Log per ogni cliente che vengono gestiti. Fornisce un'area di lavoro Analitica Log:

+ Un'area geografica per archiviare i dati. 
+ Granularità per la fatturazione 
+ Isolamento dei dati 
+ Configurazione univoche

Tramite la creazione di un'area di lavoro per ogni cliente, comunque in grado di mantenere separati i dati di ogni cliente e anche tenere traccia dell'uso di ogni cliente.

Ulteriori informazioni su quando e sui motivi per creare più aree di lavoro viene descritto in [gestire l'accesso per registrare analitica] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Creazione e la configurazione delle aree di lavoro clienti possono essere automatizzati usando [PowerShell](log-analytics-powershell-workspace-configuration.md), [modelli di Manager delle risorse](log-analytics-template-workspace-configuration.md), oppure tramite l' [API REST](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

L'utilizzo dei modelli di Manager delle risorse per la configurazione dell'area di lavoro consente di avere una configurazione master che può essere utilizzata per creare e configurare le aree di lavoro. È possibile essere certi che non vengono create le aree di lavoro per i clienti vengono configurate automaticamente alle proprie esigenze. Quando si aggiornano i requisiti, il modello viene aggiornato e quindi riapplicata le aree di lavoro esistente. Questo processo assicura che le aree di lavoro esistente anche soddisfano i nuovi standard.    

Quando si gestiscono più aree di lavoro Log Analitica, è consigliabile l'integrazione di ogni area di lavoro con il sistema di ticket esistente console operazioni usando la funzionalità di [avviso](log-analytics-alerts.md) . Mediante l'integrazione con i sistemi esistenti, personale di supporto può continuare a seguire processi familiari. Registro Analitica regolarmente controlla ogni area di lavoro in base ai criteri di avviso specificati e genera un avviso quando è necessaria un'azione.

Per i report del livelli executive che riepilogare i dati nelle aree di lavoro è possibile utilizzare l'integrazione tra Analitica Log e [ottenere informazioni](log-analytics-powerbi.md). Se è necessario per l'integrazione con un altro sistema di creazione di report, è possibile utilizzare l'API di ricerca (tramite PowerShell o [resto](log-analytics-log-search-api.md)) per eseguire query ed esportare i risultati della ricerca.

## <a name="next-steps"></a>Passaggi successivi

+ Automatizzare la creazione e la configurazione delle aree di lavoro utilizzando [i modelli di Manager delle risorse](log-analytics-template-workspace-configuration.md)
+ Automatizzare la creazione di aree di lavoro tramite [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Usare [gli avvisi](log-analytics-alerts.md) per l'integrazione con i sistemi esistenti
+ Creazione di report Riepilogo utilizzo [ottenere informazioni](log-analytics-powerbi.md)
