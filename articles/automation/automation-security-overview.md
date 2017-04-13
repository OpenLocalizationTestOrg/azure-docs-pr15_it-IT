<properties
   pageTitle="Protezione di automazione Azure | Microsoft Azure"
   description="In questo articolo viene fornita una panoramica della protezione di automazione e i diversi metodi di autenticazione disponibili per gli account di automazione di automazione di Azure."
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="protezione di automazione, automazione sicura" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="magoedte" />

# <a name="azure-automation-security"></a>Sicurezza di automazione Azure
Automazione Azure consente di automatizzare le attività in base alle risorse in Azure, in locale e con altri provider di servizi cloud, ad esempio servizi Web di Amazon (AWS).  Affinché runbook eseguire le operazioni necessarie, è necessario disporre delle autorizzazioni per accedere in modo sicuro le risorse con diritti minimi necessari all'interno della sottoscrizione.  
In questo articolo verrà illustrate le caratteristiche dei diversi scenari di autenticazione supportati dall'automazione Azure e illustra come iniziare base sull'ambiente o ambienti che è necessario gestire.  

## <a name="automation-account-overview"></a>Panoramica di Account di automazione
Quando si avvia l'automazione Azure per la prima volta, è necessario creare almeno un account di automazione. Gli account di automazione consentono di isolare le risorse di automazione (runbook, attività, le configurazioni) dalle risorse contenute in altri account di automazione. È possibile utilizzare gli account di automazione per separare le risorse in ambienti logici separati. Ad esempio, è possibile utilizzare un account per lo sviluppo, un altro per produzione e un altro per l'ambiente locale.  Un account Azure automazione è diverso dall'account Microsoft o creata nell'abbonamento Azure.

Le risorse di automazione per ogni account di automazione sono associate a una singola regione Azure, ma gli account di automazione possono gestire le risorse in un'area. Il motivo principale per creare gli account di automazione in diverse aree geografiche sarebbe se si dispone di criteri che richiedono dati e risorse per isolare un'area specifica.

>[AZURE.NOTE]Account di automazione e le risorse contengono che vengono creati nel portale di Azure, non sono accessibili nel portale di classica Azure. Se si desidera gestire questi account o le risorse con Windows PowerShell, è necessario utilizzare i moduli di gestione di risorse Azure.

Tutte le attività che è possibile eseguire con risorse utilizzando Gestione risorse di Azure e i cmdlet Azure in Azure automazione deve eseguire l'autenticazione Azure mediante l'autenticazione basata su credenziali identità organizzativa di Azure Active Directory.  Autenticazione basata su certificato è stato il metodo di autenticazione originale con la modalità di gestione dei servizi di Azure, ma è difficile da installare.  Eseguire l'autenticazione in Azure con utente di Azure Active Directory è stato introdotto indietro nel 2014 non solo semplificare il processo per configurare un account di autenticazione, ma supporta anche la possibilità di autenticazione non interattivo in Azure con un singolo account utente che ha esito positivo con Gestione risorse di Azure e risorse classiche.   

Attualmente quando si crea un nuovo account di automazione nel portale di Azure, viene creato automaticamente:

-  Esegui come account che crea una nuova identità di servizio di Azure Active Directory, un certificato e viene assegnato il controllo di accesso in base al ruolo di collaboratore (RBAC), che verrà utilizzato per gestire le risorse di gestione risorse utilizzando runbook.
-  Classica Esegui come account per il caricamento di un certificato di gestione, verrà usato per gestire la gestione dei servizi Azure o risorse classiche usando runbook.  

Controllo dell'accesso basato sui ruoli è disponibile con Gestione risorse Azure per concedere operazioni consentite a un account utente di Azure Active Directory ed Esegui come e quella principale del servizio di autenticazione.  Leggere [controllo dell'accesso basato sui ruoli in Azure automazione articolo](../automation/automation-role-based-access-control.md) per ulteriori informazioni per contribuire allo sviluppo di un modello per la gestione delle autorizzazioni di automazione.  

Runbook in esecuzione su un lavoro Runbook ibrido nel centro dati o contro computing servizi in AWS non è possibile utilizzare lo stesso metodo utilizzato in genere per l'autenticazione di runbook alle risorse di Azure.  Ciò avviene perché le risorse sono in esecuzione all'esterno di Azure e di conseguenza, saranno necessario le proprie credenziali di sicurezza all'automazione per eseguire l'autenticazione a risorse che avranno accesso a livello locale.  

## <a name="authentication-methods"></a>Metodi di autenticazione

Nella tabella seguente vengono riepilogati i metodi di autenticazione diverso per ogni ambiente supportato da Azure automazione e l'articolo che descrive come configurare l'autenticazione per il runbook.

Metodo  |  Ambiente  | Articolo
----------|----------|----------
Account utente di Azure Active Directory | Gestione risorse di Azure e gestione dei servizi Azure | [Eseguire l'autenticazione runbook con account utente di Azure Active Directory](../automation/automation-sec-configure-aduser-account.md)
Azure esecuzione come Account | Gestione risorse di Azure | [Eseguire l'autenticazione runbook con account Azure Esegui come](../automation/automation-sec-configure-azure-runas-account.md)
Azure classica esecuzione come Account | Gestione dei servizi Azure | [Eseguire l'autenticazione runbook con account Azure Esegui come](../automation/automation-sec-configure-azure-runas-account.md)
Autenticazione di Windows | Data Center locale | [Eseguire l'autenticazione runbook per ibrido Runbook colleghi](../automation/automation-hybrid-runbook-worker.md)
Credenziali AWS | Servizi Web di Amazon | [Eseguire l'autenticazione runbook con i servizi Web di Amazon (AWS)](../automation/automation-sec-configure-aws-account.md)



