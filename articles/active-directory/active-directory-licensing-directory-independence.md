<properties
   pageTitle="Aggiungere e gestire più directory di Azure Active Directory | Microsoft Azure"
   description="Istruzioni e procedure consigliate per l'aggiunta e gestione della directory di Azure Active Directory, che spiega che directory come risorse un completamente indipendenti"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Aggiungere e gestire più directory di Azure Active Directory

In Azure Active Directory (Azure Active Directory), ogni directory è una risorsa completamente indipendente: un peer completa e logicamente indipendente di altre directory da gestire. È non presente alcuna relazione padre / figlio tra directory. Questa indipendenza tra directory include l'indipendenza delle risorse, indipendenza amministrativo e l'indipendenza di sincronizzazione.

##<a name="resource-independence"></a>Indipendenza delle risorse

Se si creano o si elimina una risorsa in una directory, ha alcun effetto su tutte le risorse in un'altra directory, con l'eccezione parziale di utenti esterni, descritto di seguito. Se si usa un dominio personalizzato 'contoso.com' con una directory, non può essere utilizzato con qualsiasi altra directory.

##<a name="administrative-independence"></a>Indipendenza amministrativo

Se un utente non amministrativo della directory 'Contoso' Crea una directory di test "Test" quindi:
- Per impostazione predefinita, l'utente che crea una directory aggiunto come un utente esterno in quella nuova directory è assegnato il ruolo di amministratore globale nella directory.
- Gli amministratori della directory 'Contoso' non abbiano privilegi amministrativi diretti alla directory "Test" a meno che un amministratore di 'Test' specificamente loro concesso questi privilegi. Gli amministratori di 'Contoso' possono controllare l'accesso alla directory "Test" se consentono di controllare l'account utente che ha creato "Test".
- Se si cambia (aggiunta o rimozione di) un ruolo di amministratore per un utente in una sola cartella, la modifica non ha alcun effetto qualsiasi ruolo di amministratore che l'utente potrebbe avere in un'altra directory.

##<a name="synchronization-independence"></a>Indipendenza di sincronizzazione

È possibile configurare ogni Azure Active directory in modo indipendente recupera dati sincronizzati da una singola istanza di uno:
  - Lo strumento di sincronizzazione della Directory (DirSync), per sincronizzare i dati con una singola foresta Active Directory.
  - Azure Active Directory Connector per gestione di identità di Forefront per sincronizzare i dati con uno o più insiemi di strutture locale e/o le origini dati non Azure Active Directory.

##<a name="add-an-azure-ad-directory"></a>Aggiungere una directory di Azure Active Directory

Per aggiungere una directory di Azure Active Directory nel portale di classica Azure, selezionare l'estensione di Azure Active Directory a sinistra e toccare **Aggiungi**.

> [AZURE.NOTE]   A differenza di altre risorse Azure, la directory non sono risorse figlio di un abbonamento Azure. Se si annulla o consentire Azure scadere l'abbonamento a, è comunque possibile accedere ai dati di directory con PowerShell Azure, l'API di grafico Azure o altre interfacce, ad esempio interfaccia di amministrazione di Office 365. È inoltre possibile associare un'altra sottoscrizione con il servizio directory.

Per un'ampia panoramica dei problemi di gestione delle licenze di Azure Active Directory e procedure consigliate, vedere [che cos'è licenze Azure Active Directory?](active-directory-licensing-what-is.md).
