<properties
    pageTitle="Come ottenere un tenant di Azure Active Directory | Microsoft Azure"
    description="Come ottenere un tenant di Azure Active Directory per la registrazione e la creazione di applicazioni."
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/28/2015"
    ms.author="dastrock"/>

# <a name="how-to-get-an-azure-active-directory-tenant"></a>Come ottenere un tenant di Azure Active Directory

In Azure Active Directory (Azure Active Directory), un [tenant](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) è rappresentante dell'organizzazione.  È un'istanza dedicata del servizio di Azure Active Directory che un'organizzazione riceve e possiede quando iscrive a un servizio cloud di Microsoft, ad esempio Azure, Microsoft Intune o Office 365.  Ogni tenant di Azure Active Directory è distinto e separato da altri tenant di Azure Active Directory.  

Un tenant include gli utenti dell'azienda e le informazioni relative ad esse - loro password, i dati dei profili utente, autorizzazioni e così via.  Contiene inoltre gruppi, applicazioni e altre informazioni relative a un'organizzazione e la protezione.

Per consentire agli utenti di Azure Active Directory accedere all'applicazione, è necessario registrare l'applicazione nel tenant di personalizzati.  La pubblicazione di un'applicazione in un tenant di Azure Active Directory è **gratuitamente**.  Infatti, maggior parte dei casi creerà diverse tenant e applicazioni per sperimentazione, sviluppo, in prova e la verifica.  Le organizzazioni che iscriversi e utilizzano l'applicazione possono scegliere di acquistare licenze se si desidera usufruire delle caratteristiche avanzate directory.

Pertanto, come procedere per ottenere un tenant di Azure Active Directory?  Il processo potrebbe essere una piccola se diversi è:

- [Ha un abbonamento a Office 365 esistente](#use-an-existing-office-365-subscription)
- [Ha un abbonamento esistente Azure associato a un Account Microsoft](#use-an-msa-azure-subscription)
- [Ha un abbonamento esistente Azure associato a un account aziendale](#use-an-organizational-azure-subscription)
- [Dispone di tutte le risposte sopra e iniziare da zero](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>Utilizzare un abbonamento a Office 365 esistente
Se si dispone di un abbonamento a Office 365 esistente, si dispone già di un tenant di Azure Active Directory! È possibile accedere al [portale di Azure](https://portal.azure.com) con l'account di Office 365 e iniziare a usare Azure Active Directory.

## <a name="use-an-msa-azure-subscription"></a>Utilizzare un abbonamento a MSA Azure
Se già stata effettuata sottoscrizione di Azure con l'Account Microsoft singoli, si dispone già di un tenant!  Quando si accede al [Portale di Azure](https://portal.azure.com), verrà automaticamente eseguito al tenant di predefinito. Si può utilizzare questo tenant per adattarlo - ma è possibile creare un account di amministratore dell'organizzazione.

A tale scopo, seguire questa procedura.  In alternativa, è consigliabile creare un nuovo tenant e creare un amministratore nel tenant seguendo un processo simile.

1.  Accedere al [Portale di Azure](https://portal.azure.com) con l'account singoli
2.  Passare alla sezione "Azure Active Directory" del portale (disponibile nella barra di spostamento sinistro, in **Altri servizi**)
3.  Automaticamente deve essere firmato "Directory predefinito", se non è possibile passare directory facendo clic sul proprio nome di account in alto a destra.
4.  Nella sezione **Attività rapide** , scegliere **Aggiungi un utente**.
5.  In Aggiungi Form utente specificare quanto segue:

    - Nome: (scegliere un valore appropriato)
    - Nome utente: (scegliere un nome utente per l'amministratore)
    - Profilo: (compilare i valori appropriati per nome, cognome, la posizione e reparto)
    - Ruolo: Amministratore globale

6.  Quando si completato Aggiungi Form utente e si riceve la password temporanea per il nuovo utente di amministrazione, assicurarsi di registrare la password come sarà necessario eseguire l'accesso con il nuovo utente per cambiare la password. È anche possibile inviare la password direttamente all'utente, con un messaggio di posta elettronica alternativo.
7.  Fare clic su **Crea** per creare il nuovo utente.
8.  Per modificare la password temporanea, accedere a [https://login.microsoftonline.com](https://login.microsoftonline.com) con il nuovo account utente e modificare la password quando richiesto.


## <a name="use-an-organizational-azure-subscription"></a>Utilizzare una sottoscrizione di Azure dell'organizzazione
Se già stata effettuata sottoscrizione di Azure con l'account aziendale, si dispone già di un tenant!  Nel [Portale di Azure](https://portal.azure.com), è necessario trovare un tenant quando si passa a "Più servizi" e "Azure Active Directory"  Si può utilizzare questo tenant per adattarlo. 


## <a name="start-from-scratch"></a>Iniziare da zero
Se tutte le risposte sopra incomprensibile all'utente, non è importante.  È sufficiente visitare [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) di iscriversi a Azure con una nuova organizzazione.  Dopo aver completato il processo, si verificherà il proprio tenant di Azure Active Directory con il nome di dominio di che scelto durante l'iscrizione.  Nel [Portale di Azure](https://portal.azure.com), è possibile trovare il tenant, passare a "Azure Active Directory" esplorazione a sinistra

Come parte del processo di iscrizione a Azure, sarà necessario fornire dettagli sulla carta di credito.  È possibile procedere con fiducia - non ti per la pubblicazione di applicazioni in Azure Active Directory o la creazione di nuovi tenant.
