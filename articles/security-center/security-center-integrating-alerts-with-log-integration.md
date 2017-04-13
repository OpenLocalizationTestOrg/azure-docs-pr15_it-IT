<properties
   pageTitle="Integrazione di avvisi di Centro protezione di Azure con integrazione di Azure log (Preview) | Microsoft Azure"
   description="In questo articolo consente di introduzione all'integrazione di Centro protezione Avvisa con l'integrazione di Azure log."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# <a name="integrating-security-center-alerts-with-azure-log-integration-preview"></a>Integrazione di avvisi nel Centro sicurezza con integrazione di Azure log (Preview)

Molte operazioni di sicurezza e ai team di risposta si basano su una soluzione di informazioni sulla protezione e gestione di evento (SIEM) come punto di partenza per la valutazione e l'analisi di avvisi di sicurezza. Con l'integrazione di Azure log clienti possono sincronizzare gli avvisi di Centro protezione di Azure, insieme agli eventi di sicurezza macchina virtuale raccolti da Azure diagnostica e i log di controllo Azure, con i log analitica o soluzione SIEM in quasi in tempo reale.

Integrazione di Azure log funziona con HP ArcSight, Splunk, IBM QRadar e altri utenti.

## <a name="what-logs-can-i-integrate"></a>Quali log è possibile integrare?

Azure produce registrazione completa per ogni servizio. Questi registri sono classificati come:

- **Log di controllo/Gestione** che forniscono visibilità operazioni creare Manager delle risorse Azure, aggiornamento ed eliminazione.
- **I registri di piano di dati** che forniscono visibilità gli eventi generati quando si utilizza una risorsa Azure. Un esempio è il registro eventi di Windows - sicurezza e registri applicazione in una macchina virtuale.

Integrazione di Azure log supporta attualmente l'integrazione di:

- Registri delle macchine Virtuali di Azure
- Log di controllo Azure
- Avvisi di Centro protezione di Azure

## <a name="install-azure-log-integration"></a>Installare l'integrazione di Azure log

Scaricare [Azure accedere integrazione](https://www.microsoft.com/download/details.aspx?id=53324).

Il servizio di integrazione log Azure raccoglie i dati di telemetria dal computer in cui è installato.  Dati di telemetria raccolti sono:

- Eccezioni che si verificano durante l'esecuzione dell'integrazione di Azure log
- Metrica sul numero di query e gli eventi di elaborazione
- Statistiche sulle quali Azlog.exe vengono utilizzate le opzioni della riga di comando

> [AZURE.NOTE] È possibile disattivare la raccolta di dati di telemetria deselezionando questa opzione.

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrare i log di controllo Azure and Security Center avvisi

1. Aprire il prompt dei comandi e **cd** in **c:\Programmi\Microsoft c:\Programmi\Microsoft Azure Log integrazione**.

2. Eseguire il comando **azlog createazureid** per creare un [Principale del servizio di Azure Active Directory](../active-directory/active-directory-application-objects.md) nel tenant di Azure Active Directory (AD) che ospitano le sottoscrizioni Azure.

    Verrà richiesto per l'account di accesso Azure.

    > [AZURE.NOTE] È necessario essere il proprietario di sottoscrizione o un amministratore di creazione della sottoscrizione.

    L'autenticazione in Azure viene eseguita tramite Azure Active Directory.  Creazione di un servizio principale per l'integrazione di Azure log creerà l'identità di Azure Active Directory che è possibile ottenere accesso in lettura da Azure abbonamenti.

3. Eseguire il **azlog autorizzare <SubscriptionID> ** comando per assegnare l'accesso in lettura dell'abbonamento a principale del servizio creato nel passaggio 2. Se non si specifica un **SubscriptionID**, quindi principale del servizio verrà assegnato il ruolo di lettore per tutte le sottoscrizioni a cui si ha accesso.

    > [AZURE.NOTE] Se si esegue il comando **autorizzare** immediatamente dopo il comando **createazureid** perché esiste alcune latenza tra quando viene creato l'account Azure Active Directory e quando è disponibile per l'uso dell'account, sarà possibile visualizzare avvisi. Se si attendere circa 10 secondi dopo l'esecuzione del comando **createazureid** per eseguire il comando **autorizzare** , non è necessario verificare questi avvisi.

4. Controllare le cartelle seguenti per verificare che i file di JSON log di controllo sono disponibili:

  - **c:\Users\azlog\AzureResourceManagerJson**
  - **c:\Users\azlog\AzureResourceManagerJsonLD**

5. Controllare le cartelle seguenti per verificare l'esistenza di Centro protezione avvisa che:

  - **c:\Users\azlog\ AzureSecurityCenterJson**
  - **c:\Users\azlog\AzureSecurityCenterJsonLD**

6. Scegliere il standard SIEM server d'inoltro del connettore file nella cartella appropriata per inviare i dati per l'istanza SIEM. Fare riferimento alle [configurazioni SIEM](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm) alla configurazione SIEM.

Se hai domande sull'integrazione di Log di Azure, inviare un messaggio di posta elettronica a [AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Azure i log di controllo e le definizioni delle proprietà, vedere:

- [Controllare le operazioni con Gestione risorse](../resource-group-audit.md)
- [Elenco degli eventi di gestione di un abbonamento](https://msdn.microsoft.com/library/azure/dn931934.aspx) - per recuperare gli eventi del log di controllo.

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Blog di sicurezza di Windows Azure](http://blogs.msdn.com/b/azuresecurity/) , ottenere le ultime notizie di sicurezza di Azure e informazioni.
