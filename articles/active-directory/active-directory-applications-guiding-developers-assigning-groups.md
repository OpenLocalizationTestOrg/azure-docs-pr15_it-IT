<properties
    pageTitle="Azure Active Directory e applicazioni: assegnazione di gruppi a un'applicazione | Microsoft Azure"
    description="Procedura per implementare l'assegnazione al gruppo per le applicazioni Azure."
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/03/2015"
    ms.author="inhenk"/>

# <a name="azure-ad-and-applications-assigning-groups-to-an-application"></a>Azure Active Directory e applicazioni: assegnazione di gruppi a un'applicazione
Prima di assegnare utenti e gruppi per un'applicazione, è necessario richiedere assegnazione utente. Per informazioni su come richiedere assegnazione utente, vedere l'articolo [Che richiede l'assegnazione di utente](active-directory-applications-guiding-developers-requiring-user-assignment.md) .

In questo articolo si presuppone che già stato creato gruppi di active directory in uso per questa applicazione.

## <a name="assigning-groups-to-an-application"></a>Assegnazione di gruppi a un'applicazione
1. Accedere al portale di Azure con un account di amministratore.
2. Fare clic sull'elemento **Tutti gli elementi** nel menu principale.
3. Scegliere la directory in uso per l'applicazione.
4. Fare clic sulla scheda **applicazioni** .
5. Selezionare l'applicazione dall'elenco di applicazioni associate alla directory.
6. Fare clic sulla scheda **Utenti e gruppi** .
7. Filtrare l'elenco di gruppi di active directory utilizzando l'elenco a discesa di **gruppi** .
8. Selezionare il gruppo.
9. Fare clic su **Assegna**.
10. Fare clic su **Sì** quando richiesto.

## <a name="next-steps"></a>Passaggi successivi
[AZURE.INCLUDE [active-directory-applications-guiding-developers-for-lob-applications-toc.md](../../includes/active-directory-applications-guiding-developers-for-lob-applications-toc.md)]
