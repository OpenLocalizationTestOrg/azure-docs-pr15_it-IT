<properties
    pageTitle="Assegnare agli utenti di un dominio personalizzato in Azure Active Directory | Microsoft Azure"
    description="Come inserire un dominio personalizzato in Azure Active Directory con gli account utente."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Assegnare agli utenti di un dominio personalizzato

Dopo aver aggiunto il dominio personalizzato in Azure Active Directory, è necessario aggiungere l'account utente per il dominio, in modo che è possibile iniziare tuttavia l'autenticazione.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Utenti sincronizzati da una directory della rete aziendale

Se è stata già configurata una connessione tra le locale Active Directory e Azure Active Directory, è possibile inserire la sincronizzazione dei dati gli account. Per ulteriori informazioni sulla sincronizzazione di Azure Active Directory con Active Directory locale, vedere [integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Gli utenti aggiunti e gestiti nel cloud

Per modificare il dominio per un account utente esistente:

1.  Aprire il portale classico Azure usando un account che non è un amministratore globale o un amministratore utenti.

2.  Aprire la directory.

3.  Selezionare la scheda **utenti** .

4.  Selezionare l'utente dall'elenco.

5.  Modificare il dominio per l'utente e quindi selezionare **Salva**.

Questa operazione può essere eseguita anche tramite [Microsoft PowerShell](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) o l' [API di grafico](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Selezionare un dominio personalizzato quando si crea un nuovo utente

1.  Aprire il portale classico Azure usando un account che non è un amministratore globale o un amministratore utenti.

2.  Aprire la directory.

3.  Selezionare la scheda **utenti** .

4.  Nella barra dei comandi, selezionare **Aggiungi**.

5.  Quando si aggiunge il nome utente, selezionare il dominio personalizzato nell'elenco di dominio.

6.  Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

-   [Utilizzo dei nomi di dominio personalizzato per semplificare le operazioni di accesso per gli utenti](active-directory-add-domain.md)

-   [Gestire i nomi di dominio personalizzato](active-directory-add-manage-domain-names.md)

-   [Informazioni sui concetti relativi alla gestione dominio Azure Active Directory](active-directory-add-domain-concepts.md)
