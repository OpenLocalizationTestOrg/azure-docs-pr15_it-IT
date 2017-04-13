<properties
    pageTitle="Gestire le autorizzazioni alle risorse per ogni utente in pila Azure (amministratore del servizio e tenant) | Microsoft Azure"
    description="Come amministratore del servizio o tenant, informazioni su come gestire le autorizzazioni alle risorse per ogni utente."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Gestire le autorizzazioni utente

Un utente in pila Azure può essere un lettore, il proprietario o collaboratore per ogni istanza di un abbonamento, gruppo di risorse o servizio. Ad esempio, l'utente potrebbe dispone delle autorizzazioni di lettura 1 sottoscrizione, ma dispone delle autorizzazioni di proprietario 7 macchina virtuale.

-   Lettore: Utente può visualizzare tutti gli elementi, ma non è possibile apportare le modifiche.

-   Collaboratori: Utente può gestire tutti gli elementi ad eccezione di accesso alle risorse.

-   Proprietario: Utente può gestire tutti gli elementi, tra cui l'accesso alle risorse.


## <a name="set-access-permissions-for-a-user"></a>Impostare le autorizzazioni di accesso per un utente

1.  Accedere con un account dotato di autorizzazioni di proprietario per la risorsa che si desidera gestire.

2.  In blade per la risorsa, fare clic sull'icona di **accesso** ![](media/azure-stack-manage-permissions/image1.png).

3.  Selezionare **i ruoli**e **l'utenti** .

4.  In e il **ruoli** , fare clic su **Aggiungi** per aggiungere le autorizzazioni per l'utente.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere un tenant dello Stack di Azure](azure-stack-add-new-user-aad.md)
