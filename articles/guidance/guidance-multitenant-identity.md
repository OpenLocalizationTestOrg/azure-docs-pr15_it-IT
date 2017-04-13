<properties
   pageTitle="Gestione delle identità per le applicazioni multi-tenant | Microsoft Azure"
   description="Procedure consigliate per l'autenticazione, autorizzazioni, gestione delle identità e nelle applicazioni multi-tenant."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/02/2016"
   ms.author="mwasson"/>

# <a name="identity-management-for-multitenant-applications-in-microsoft-azure"></a>Gestione delle identità per le applicazioni multi-tenant in Microsoft Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Questa serie descrive le procedure consigliate per rappresentato, quando si usa Azure Active Directory per la gestione di autenticazione e identità.

Quando si crea un'applicazione multi-tenant, uno dei primi problemi usata per la gestione delle identità utente, in quanto a questo punto ogni utente appartiene a un tenant. Per esempio:

- Utenti di accedere con le credenziali dell'organizzazione.
- Gli utenti devono hanno accesso ai dati della propria organizzazione, ma non i dati che appartengono ad altri tenant.
- Un'organizzazione possa iscriversi per l'applicazione e quindi assegnare i ruoli di applicazione ai membri.

Azure Active Directory (Azure Active Directory) presenta alcune funzionalità che supportano tutti questi scenari.

Per accompagnare questa serie di articoli, anche creato completa, [implementazione - to-end] [ tailspin] di un'app multi-tenant. Gli articoli rifletteranno acquisito nel processo di creazione dell'applicazione. Per iniziare a utilizzare l'applicazione, vedere [eseguire l'applicazione di sondaggi](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md).

Ecco l'elenco di articoli in questa serie:

- [Introduzione alla gestione delle identità per le applicazioni multi-tenant](guidance-multitenant-identity-intro.md)
- [Informazioni sull'applicazione Tailspin sondaggi](guidance-multitenant-identity-tailspin.md)
- [Autenticazione tramite Azure Active Directory e connettere OpenID](guidance-multitenant-identity-authenticate.md)
- [Utilizzo delle identità basato sulle attestazioni](guidance-multitenant-identity-claims.md)
- [Per l'abbonamento e integrazione del tenant](guidance-multitenant-identity-signup.md)
- [Ruoli applicazione](guidance-multitenant-identity-app-roles.md)
- [Autorizzazione basato sui ruoli e basate sulle risorse](guidance-multitenant-identity-authorize.md)
- [Proteggere dell'API web back-end](guidance-multitenant-identity-web-api.md)
- [Token di accesso OAuth2 memorizzazione nella cache](guidance-multitenant-identity-token-cache.md)
- [La federazione con ADFS del cliente per le app multi-tenant in Azure](guidance-multitenant-identity-adfs.md)
- [Utilizzo di asserzione client per ottenere i token di accesso da Azure Active Directory](guidance-multitenant-identity-client-assertion.md)
- [Utilizzo di chiave archivio per proteggere le informazioni riservate applicazione](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps
