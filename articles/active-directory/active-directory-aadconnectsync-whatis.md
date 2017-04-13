<properties
    pageTitle="Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione | Microsoft Azure"
    description="Spiega come Azure AD Connect sincronizzare works e su come personalizzare."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione
I servizi di sincronizzazione di Azure Active Directory connettersi (sincronizzazione di Azure AD Connect) è un componente principale di Azure AD Connect. Si occupa di tutte le operazioni correlate a sincronizzare i dati di identità tra l'ambiente locale e Azure Active Directory. Sincronizzazione di Azure AD Connect è il successore di DirSync, sincronizzazione di Azure Active Directory e Forefront Identity Manager con Azure Active Directory Connector configurato.

In questo argomento è la home page per la **sincronizzazione di Azure AD Connect** (denominato anche **motore di sincronizzazione**) e vengono forniti collegamenti a tutti gli altri argomenti relativi a tale. Per i collegamenti a Azure AD Connect, vedere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).

Servizio di sincronizzazione è costituito da due componenti, il componente di **sincronizzazione di Azure AD Connect** locale e lato del servizio in Azure Active Directory denominato **servizio di sincronizzazione di Azure AD Connect**. Il servizio in genere per DirSync, sincronizzazione di Azure Active Directory e Azure AD Connect.

## <a name="azure-ad-connect-sync-topics"></a>Argomenti della sincronizzazione di Azure AD Connect

Argomento | Vengono illustrate e quando da leggere
----- | -----
**Nozioni fondamentali sulla sincronizzazione di Azure AD Connect** |
[Introduzione all'architettura](active-directory-aadconnectsync-understanding-architecture.md) | Per gli utenti che non conoscono il motore di sincronizzazione e si desidera conoscere l'architettura e i termini utilizzati.
[Concetti tecnici](active-directory-aadconnectsync-technical-concepts.md) | Una versione breve dell'argomento architettura e brevemente illustra i termini usati.
[Connettere topologie per Azure Active Directory](active-directory-aadconnect-topologies.md) | Vengono descritti i diversi topologie e scenari che supporta il motore di sincronizzazione.
**Configurazione personalizzata** |
[Eseguire di nuovo l'installazione guidata](active-directory-aadconnectsync-installation-wizard.md) | Vengono illustrate le opzioni disponibili quando si esegue nuovamente l'installazione guidata di Azure AD Connect.
[Informazioni sulle Provisioning dichiarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)| Descrive il modello di configurazione denominato provisioning dichiarativo.
[Informazioni sulle espressioni di Provisioning dichiarative](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) | Vengono illustrati la sintassi per il linguaggio delle espressioni utilizzata per il provisioning dichiarativo.
[Informazioni sulla configurazione predefinita](active-directory-aadconnectsync-understanding-default-configuration.md)| Descrive le regole di fuori della casella e la configurazione predefinita. Descrive anche le regole di interagiscono per gli scenari di pronte per l'uso.
[Contatti e gli utenti di informazioni](active-directory-aadconnectsync-understanding-users-and-contacts.md) | Continua nella sezione precedente e descrive come la configurazione per gli utenti e i contatti lavora insieme, in particolare in un ambiente con più insiemi di strutture.
[Come apportare modifiche alla configurazione predefinita](active-directory-aadconnectsync-change-the-configuration.md) | Viene illustrato come cambiare una configurazione comune di flussi di attributo.
[Procedure consigliate per la modifica della configurazione predefinita](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) | Limitazioni di supporto e per apportare modifiche alla configurazione di fuori della casella.
[Configurare il filtro](active-directory-aadconnectsync-configure-filtering.md) | Descrive le diverse opzioni per la procedura limitare gli oggetti che vengono sincronizzati Azure Active Directory e istruzioni dettagliate sulla configurazione di queste opzioni.
**Funzionalità e scenari** |
[Evitare che le eliminazioni accidentali](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) | Vengono illustrati la caratteristica *impedisce eliminazioni accidentali* e su come configurarlo.
[Utilità di pianificazione](active-directory-aadconnectsync-feature-scheduler.md) | Descrive l'utilità di pianificazione predefiniti, l'importazione, la sincronizzazione e l'esportazione di dati.
[Implementare la sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md) | Descrive come funziona la sincronizzazione delle password, come implementare e come funzionano e risoluzione dei problemi.
[Writeback di dispositivo](active-directory-aadconnect-feature-device-writeback.md) | Descrive come funziona il dispositivo writeback in Azure AD Connect.
[Estensioni directory](active-directory-aadconnectsync-feature-directory-extensions.md) | In questo articolo viene descritto come estendere lo schema di Azure Active Directory con attributi personalizzati.
**Servizio di sincronizzazione** |
[Caratteristiche di Azure AD Connect sincronizzazione del servizio](active-directory-aadconnectsyncservice-features.md) | Viene descritto come modificare le impostazioni di sincronizzazione di Azure Active Directory e lato del servizio di sincronizzazione.
[Adattabilità attributo duplicato](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) | In questo articolo viene descritto come attivare e utilizzare sulla resilienza valori duplicati attributo **userPrincipalName** e **proxyAddresses** .
**Operazioni e dell'interfaccia utente** |
[Gestione di servizi di sincronizzazione](active-directory-aadconnectsync-service-manager-ui.md) | Descrive UI gestore servizio di sincronizzazione, tra cui [le operazioni](active-directory-aadconnectsync-service-manager-ui-operations.md), [connettori](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)e schede di [Ricerca Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) .
[Considerazioni e attività operative](active-directory-aadconnectsync-operations.md) | Vengono descritti aspetti operativi, ad esempio il ripristino di emergenza.
**Come si fa...** |
[Reimpostare l'account Azure Active Directory](active-directory-aadconnectsync-howto-azureadaccount.md) | Come reimpostare le credenziali dell'account del servizio utilizzato per la connessione di sincronizzazione di Azure AD Connect di Azure Active Directory.
**Ulteriori informazioni e riferimenti** |
[Porte](active-directory-aadconnect-ports.md) | Elenca le porte, è necessario aprire tra il motore di sincronizzazione e la directory locale e Azure Active Directory.
[Attributi sincronizzati con Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) | Elenca tutti gli attributi sincronizzati tra locale Active Directory e Azure Active Directory.
[Guida di riferimento funzioni](active-directory-aadconnectsync-functions-reference.md) | Elenca tutte le funzioni disponibili per il provisioning dichiarativo.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
