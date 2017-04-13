<properties
    pageTitle="Panoramica di Endpoint v 2.0 | Microsoft Azure"
    description="Introduzione alla creazione di applicazioni con l'Account Microsoft e Azure Active Directory accesso."
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
    ms.date="09/27/2016"
    ms.author="dastrock"/>

# <a name="sign-in-microsoft-account--azure-ad-users-in-a-single-app"></a>Accesso Account Microsoft Azure Active Directory utenti e in un'unica app

In passato, uno sviluppatore app che per supportare gli account Microsoft e Azure Active Directory è stato necessario per integrare con due sistemi separati.  È ora stata introdotta una nuova versione dell'API di autenticazione che consente di eseguire l'accesso degli utenti con entrambi i tipi di account con il sistema di Azure Active Directory.  In questo sistema di autenticazione convergente è nota come **endpoint v 2.0**.  Con endpoint v 2.0 un'integrazione semplice consente di raggiungere un gruppo di destinatari che si estende milioni di utenti con account sia personali e di lavoro o dell'istituto di istruzione.

App che usano l'endpoint v 2.0 è anche possibile utilizzare le API REST dalla [Microsoft Graph](https://graph.microsoft.io) e [Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) usando dei due tipi di account.

<!-- For a quick introduction to the v2.0 endpoint, please view the [Getting Started with Microsoft Identities: Enterprise Grade Sign In For Your Apps](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/) video. -->

## <a name="getting-started"></a>Guida introduttiva
[AZURE.VIDEO build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps]

Scegliere la piattaforma preferita dall'elenco seguente per creare un'app usando il nostro raccolte Apri origine e Framework.  In alternativa, è possibile utilizzare la documentazione di protocollo OAuth 2.0 & OpenID connessione invia e Ricevi messaggi del protocollo direttamente senza utilizzare una raccolta di autenticazione.

<!-- TODO: Finalize this table  -->
[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## <a name="whats-new"></a>Quali sono le novità
I concetti fondamentali sulla saranno utili per capire che cos'è e cosa non è possibile con l'endpoint v 2.0.

- Informazioni sui [tipi di App che è possibile creare con l'endpoint v 2.0](active-directory-v2-flows.md).
- Comprendere le [limitazioni, restrizioni e i vincoli](active-directory-v2-limitations.md) con l'endpoint v 2.0.
- Abbiamo creato recente il supporto per [amministrazione limitata ambiti](active-directory-v2-scopes.md) e il [client OAuth2 concedono credenziali](active-directory-v2-protocols-oauth-client-creds.md).  Provarli!

## <a name="reference"></a>Guida di riferimento
Questi collegamenti sono utili per l'esplorazione della piattaforma in modo approfondito:

- Compilazione 2016: [Introduzione a Microsoft identità: Enterprise voto accesso per le app](https://azure.microsoft.com/documentation/videos/build-2016-getting-started-with-microsoft-identities-enterprise-grade-sign-in-for-your-apps/)
- È possibile ottenere assistenza in Overflow dello Stack con [azure active directory](http://stackoverflow.com/questions/tagged/azure-active-directory) o [adal](http://stackoverflow.com/questions/tagged/adal) tag.
- [Riferimento al protocollo v 2.0](active-directory-v2-protocols.md)
- [Guida di riferimento Token v 2.0](active-directory-v2-tokens.md)
- [Riferimento alla libreria v 2.0](active-directory-v2-libraries.md)
- [Ambiti e consenso endpoint v 2.0](active-directory-v2-scopes.md)
- [Il portale di Microsoft App registrazione](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)
- [Riferimento all'API REST di Office 365](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [Microsoft Graph](https://graph.microsoft.io)