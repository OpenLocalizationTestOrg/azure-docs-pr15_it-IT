<properties
    pageTitle="registrazione di app v 2.0 | Microsoft Azure"
    description="Come registrare un'applicazione di Microsoft per l'attivazione accesso e accesso ai servizi Microsoft utilizzando l'endpoint v 2.0"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Come registrare un'app all'endpoint v 2.0

Per creare un'applicazione che accetta MSA e Azure Active Directory accessohttp, è prima necessario registrare un'applicazione di Microsoft.  In questa fase, non sarà possibile usare qualsiasi App esistente potrebbe essere con Azure Active Directory o MSA - è necessario crearne uno nuovo.

> [AZURE.NOTE]
    Non tutti gli scenari di Azure Active Directory e funzionalità supportate da endpoint v 2.0.  Per determinare se è necessario utilizzare l'endpoint v 2.0, informazioni sulle [limitazioni v 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitare il portale di registrazione app Microsoft
Innanzitutto passare prima di tutto - al [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Si tratta di un nuovo portale di registrazione app in cui è possibile gestire le applicazioni di Microsoft.

Effettuare l'accesso con uno personale o aziendale o dell'istituto di istruzione account Microsoft.  Se non si è in, effettuare l'iscrizione per un nuovo account personale. Procedere e non richiedono tempo, è necessario attendere qui.

È stato completato? A questo punto occorre all'elenco di applicazioni di Microsoft, che è probabile che sia vuoto.  Di seguito modificare questa impostazione.

Fare clic su **Aggiungi un'app**e assegnarle un nome.  Il portale assegna l'app un Id univoco globale di applicazione in uso in un punto successivo del codice.  Se l'app include un componente sul lato server che necessita di token di accesso per chiamare le API (pensare: Office, Azure o il proprio web API), sarà necessario creare un' **Applicazione segreto** qui anche.
<!-- TODO: Link for app secrets -->

Aggiungere le piattaforme utilizzate l'app.

- Per le applicazioni basate sul web, fornire un **URI reindirizzare** nel punto in cui è possibile inviare messaggi di accesso.
- Per App per dispositivi mobili, copiare verso il basso l'uri di reindirizzamento predefinito creato automaticamente.

Facoltativamente, è possibile personalizzare l'aspetto della pagina di accesso nella sezione del profilo.  Assicurarsi di fare clic su **Salva** prima di continuare.

> [AZURE.NOTE] Quando si crea un'applicazione tramite [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l'applicazione verrà registrata nel tenant di iniziale dell'account che consente di accedere al portale.  Questo errore indica che non è possibile registrare un'applicazione nel tenant di Azure Active Directory mediante un account Microsoft personale.  Se si desidera esplicitamente registrare un'applicazione di un particolare tenant, accedere con un account creato originariamente in tenant.

## <a name="build-a-quick-start-app"></a>Creare un'app di Guida introduttiva
Dopo aver creato un'applicazione di Microsoft, è possibile eseguire una delle nostro esercitazioni introduttive v 2.0.  Ecco alcuni suggerimenti:

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]
