<properties
    pageTitle="Componenti aggiuntivi di accesso dopo più errori"
    description="Report che indica gli utenti che hanno effettuato dopo più errori consecutivi errore di accesso in tentativi."
    services="active-directory"
    documentationCenter=""
    authors="SSalahAhmed"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/04/2016"
    ms.author="saah;kenhoff"/>

# <a name="sign-ins-after-multiple-failures"></a>Accessi dopo più errori
Segnalare agli utenti che hanno effettuato dopo più errori consecutivi errore di accesso in tentativi. Le possibili cause includono:

- Utente dimenticato la password</li><li>Utente è vittima di una password corretta indovinare brute forzare attacco

Risultati da questo report mostra il numero di tentativi consecutivi non accesso apportati prima dell'accesso aggiuntivo ha esito positivo e un timestamp associato il primo completato accesso aggiuntivo.

**Le impostazioni del report**: È possibile configurare il numero minimo di accesso non riuscito errori consecutivi di tentativi che è necessario eseguire prima che possono essere visualizzato nel report. Quando si apportano modifiche a questa impostazione è importante tenere presente che queste modifiche non essere applicate a qualsiasi accesso non riuscito esistente aggiuntivi attualmente visualizzati nel report esistente. Tuttavia, verrà applicati a tutti accessi futuri. Le modifiche apportate a questo report possono essere eseguite solo da amministratori concesso in licenza.


![Componenti aggiuntivi di accesso dopo più errori](./media/active-directory-reporting-sign-ins-after-multiple-failures/signInsAfterMultipleFailures.PNG)
