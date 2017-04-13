<properties
    pageTitle="Quali sono le novità in pila Azure | Microsoft Azure"
    description="Quali sono le novità in pila di Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/25/2016"
    ms.author="helaw"/>

# <a name="whats-new-in-azure-stack-technical-preview-2"></a>Novità di Azure Stack Technical Preview 2
Questa versione sono disponibili nuove caratteristiche per gli amministratori e i tenant.

## <a name="network"></a>Rete   
   - [IDN](azure-stack-understanding-dns-in-tp2.md) offre la registrazione del nome di rete interna e la risoluzione del sistema DNS (Domain Name) senza l'infrastruttura DNS aggiuntiva.
   - [Gateway di rete virtuali](azure-stack-create-vpn-connection-one-node-tp2.md) forniscono le opzioni di integrazione applicativa VPN alle risorse di Azure o locale.
   - Indirizza definiti dall'utente possono indirizzare il traffico di rete tramite firewall, sicurezza, altri dispositivi e altri servizi.
   - È possibile creare risorse di rete di Marketplace.   

## <a name="storage"></a>Spazio di archiviazione
 - [Azure code](https://msdn.microsoft.com/library/dd179353.aspx) attiva messaggistica service affidabile e permanente.
 - Dati sulle prestazioni di spazio di archiviazione acquisizione di [analitica lo spazio di archiviazione](https://msdn.microsoft.com/library/azure/hh343270.aspx) . È possibile usare questi dati per le richieste di analisi, analizzare le tendenze di utilizzo e diagnosticare i problemi con l'account di archiviazione.
 - Archiviazione BLOB supporta [l'operazione di blocco di Accodamento](https://msdn.microsoft.com/library/azure/mt427365.aspx).
 - API di archiviazione account supporto.
 - Supporto del servizio di archiviazione per comuni strumenti e SDK, ad esempio CLI Azure, PowerShell, .NET, Python e linguaggio SDK del tenant. 
 - [Spazio di archiviazione Account condiviso accesso firma](https://msdn.microsoft.com/library/azure/mt584140.aspx) abilitare la delega granulare dell'accesso ai servizi lo spazio di archiviazione senza che sia necessario condividere la chiave account completo.  
 - Servizi di archiviazione ora utilizzano [Gruppo gestiti gli account di servizio](https://technet.microsoft.com/library/hh831477.aspx) per la protezione avanzata con gestionale bassa.
 - È possibile recuperare risorse tenant inutilizzati su richiesta.
 - Archiviazione eliminate account conservazione lunghezza può essere configurato.
 - È possibile recuperare gli account di archiviazione tenant eliminati.

## <a name="compute"></a>Calcolare
- È possibile rilasciare macchine virtuali.
- È possibile ridistribuire le estensioni macchina virtuale ai fini della configurazione e risoluzione dei problemi di gestione.
- È possibile ridimensionare dischi macchina virtuale.
- Macchine virtuali può contenere più interfacce di rete.

### <a name="portal-experience"></a>Esperienza portale
 - Le aree dello Stack Azure sono un'unità logica di gestione all'interno dello Stack di Azure e scala. In questa versione di anteprima, è possibile visualizzare informazioni sui servizi come elaborazione, di rete e di archiviazione per area geografica.
 - È ora possibile visualizzare in anteprima l'interfaccia di [azure-stack-updates.md] (aggiornamenti).

## <a name="key-vault"></a>Archivio di chiave
- [Archivio di chiave in pila Azure](azure-stack-kv-intro.md) consente la gestione sicura dei tasti e le password per applicazioni basate su cloud.
- È possibile controllare e monitorare l'utilizzo della chiave da App e macchine virtuali.

## <a name="billing-and-usage"></a>Fatturazione e l'uso
 - [Fatturazione e consumo API](azure-stack-billing-and-chargeback.md) esporre i dati nel modo in cui vengono utilizzati i servizi.  
 - Provider delegato consentono rivenditori offrire i servizi di Azure Stack ai propri clienti.

## <a name="monitoring-and-health"></a>Monitoraggio e integrità
 - Nuove funzionalità disponibili nel portale e le API di monitoraggio consentono di tempestiva consente di visualizzare e gestire gli avvisi nel proprio ambiente.  

## <a name="next-steps"></a>Passaggi successivi
- [Comprendere l'architettura di prova Stack Azure](azure-stack-architecture.md)      
- [Comprendere i prerequisiti per la distribuzione](azure-stack-deploy.md)
- [Distribuire Stack Azure](azure-stack-run-powershell-script.md)

  
