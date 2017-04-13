<properties
    pageTitle="Azure AD Connect dell'integrità della cronologia delle versioni"
    description="In questo documento vengono descritte le versioni di Azure Active Directory connettersi salute e quali è stato incluso in queste versioni."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect integrità: Cronologia delle versioni versione

Il team di Azure Active Directory aggiorna regolarmente Azure Active Directory connettersi integrità con nuove funzionalità e funzionalità. Questo articolo elenca le versioni e funzionalità che sono stata pubblicate.

## <a name="october-2016"></a>Ottobre 2016
**Aggiornamento dell'agente:**
- Azure agente di integrità di connettersi Active Directory per AD FS \(versione 2.6.408.0\)
    1. Miglioramenti per rilevare indirizzi IP client in richieste di autenticazione
    2. Correzioni di bug relative agli avvisi
- Azure agente dell'integrità della connessione di Active Directory di Active Directory (versione 2.6.408.0)
    1. Correzioni di bug relative agli avvisi.
- Azure agente di integrità di connettersi Active Directory per la sincronizzazione (versione 2.6.353.0) rilasciata con Azure AD Connect versione 1.1.281.0
    1. Fornire i dati necessari per i report di errore di sincronizzazione
    2. Correzioni di bug relative agli avvisi

**Nuove funzionalità di anteprima:**
- Connettere segnalazioni errori di sincronizzazione di Azure Active Directory

**Nuove caratteristiche:**
- Azure Active Directory connettersi integrità per AD FS - campo dell'indirizzo IP è disponibile nel report sui principali 50 utenti con il nome utente/password errata.

## <a name="july-2016"></a>Luglio 2016

**Nuove funzionalità di anteprima:**

- [Azure AD Connect integrità per Active Directory](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Gennaio 2016


**Aggiornamento dell'agente:**

- Azure agente di integrità di connettersi Active Directory per AD FS (versione 2.6.91.1512)


**Nuove caratteristiche:**

- [Strumento di connettività di test per Azure AD connettersi agenti integrità](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>Novembre 2015


**Nuove caratteristiche:**

- Supporto per [il controllo dell'accesso basato sui ruoli](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nuove funzionalità di anteprima:**

- [Azure Active Directory connettersi integrità per la sincronizzazione](active-directory-aadconnect-health-sync.md).

**Problemi risolti:**

- Gli errori durante le registrazioni agente correzioni degli errori.

## <a name="september-2015"></a>Settembre 2015

**Nuove caratteristiche:**

- Errato report password nome utente per AD FS
- Supporto per configurare un Proxy HTTP non autenticati
- Supporto per configurare l'agente su Server core
- Miglioramenti per gli avvisi per ADFS
- Miglioramenti introdotti in Agente integrità di connettersi Azure Active Directory per AD FS di connettività e dati caricare.


**Problemi risolti:**

- L'uso approfondimenti correzioni degli errori per i tipi di errore di ADFS Active Directory.


## <a name="june-2015"></a>Giugno 2015

**Versione iniziale di Azure Active Directory connettersi integrità per ADFS e Proxy ADFS.**

**Nuove caratteristiche:**

- Avvisi per il monitoraggio dei server ADFS e Proxy ADFS con le notifiche tramite posta elettronica.
- Accedere facilmente topologia di ADFS e modelli di contatori delle prestazioni AD FS.
- Linea di tendenza in richieste di token completate nel server ADFS raggruppati per applicazioni, i metodi di autenticazione, richiedere e così via percorso di rete.
- Tendenze nei richiesta non riuscita nel server ADFS raggruppati per applicazioni, e così via tipi di errore.
- Installazione di più semplice agente utilizzando le credenziali di amministratore globale di Azure Active Directory.  




## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni sui [servizi Monitor le locale identità dell'infrastruttura e sincronizzazione nel cloud](active-directory-aadconnect-health.md).
