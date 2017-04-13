<properties
    pageTitle="Visualizzazione delle assegnazioni risorse Azure accesso | Microsoft Azure"
    description="Visualizzare e gestire tutte le assegnazioni di controllo dell'accesso basato sui ruoli per qualsiasi utente o gruppo nel portale di Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Visualizzare le assegnazioni di accesso per gli utenti e gruppi nel portale di Azure - anteprima pubblica

> [AZURE.SELECTOR]
- [Gestire l'accesso utente o gruppo](role-based-access-control-manage-assignments.md)
- [Gestire l'accesso da risorsa](role-based-access-control-configure.md)

Con basato sui ruoli accesso controllo (RBAC) nel riquadro di anteprima di Azure Active Directory, è possibile gestire l'accesso alle risorse Azure. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md)

Accesso assegnato con RBAC è diffusamente perché esistono due modi per limitare le autorizzazioni:

- **Ambito:** Assegnazione di ruolo RBAC sono limitate a una sottoscrizione specifica, gruppo di risorse o delle risorse. Un utente con accesso a un'unica risorsa non è possibile accedere risorse nello stesso abbonamento.
- **Ruolo:** Nell'ambito dell'assegnazione è restringere l'accesso ulteriormente mediante l'assegnazione di un ruolo. Ruoli possono essere alto livello, ad esempio proprietario o specifici, ad esempio lettore macchina virtuale.

Ruoli possono essere assegnati solo all'interno della sottoscrizione, gruppo di risorse o delle risorse che costituisce l'ambito dell'assegnazione. Ma è possibile visualizzare tutte le assegnazioni di accesso per un determinato utente o un gruppo in un'unica posizione.

È possibile ottenere altre informazioni su come alle [assegnazioni di ruolo usare per gestire l'accesso alle risorse dell'abbonamento Azure](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Visualizzare le assegnazioni di access

Per cercare le assegnazioni di accesso per un singolo utente o un gruppo, avviare in Azure Active Directory nel [portale di Azure](http://portal.azure.com).

1. Selezionare **Azure Active Directory**. Se questa opzione non è visibile nel proprio elenco di navigazione, selezionare **Altri servizi** e quindi scorrere fino a trovare **Azure Active Directory**.
2. Selezionare **utenti e gruppi**e quindi **tutti gli utenti** o **tutti i gruppi**. In questo esempio abbiamo focalizzare l'attenzione su singoli utenti.
    ![Gestire utenti e gruppi di Azure Active Directory - schermata](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Eseguire una ricerca per l'utente tramite nome o il nome utente.
4. Selezionare **risorse Azure** e l'utente. Vengono visualizzate tutte le assegnazioni di accesso per tale utente.

### <a name="read-permissions-to-view-assignments"></a>Autorizzazioni di lettura per visualizzare le assegnazioni

Questa pagina vengono visualizzate solo le assegnazioni di accesso che si è autorizzati a leggere. Ad esempio, si dispone di accesso in lettura alla sottoscrizione A e passare a e il Azure risorse per verificare le assegnazioni dell'utente. Si possono vedere la sua assegnazioni di accesso per un abbonamento, mentre non ha anche è assegnazioni di access nell'abbonamento a B.

## <a name="delete-access-assignments"></a>Eliminare le assegnazioni di access

Da questa blade, è possibile eliminare le assegnazioni di access che sono state assegnate direttamente a un utente o gruppo. Se l'assegnazione di accesso è stato ereditato da un gruppo padre, è necessario passare alla risorsa o all'abbonamento e gestire l'assegnazione non esiste.

1. Nell'elenco di tutte le assegnazioni di accesso per un utente o gruppo, selezionare quello che si desidera eliminare.
2. Selezionare **Rimuovi** e quindi su **Sì** per confermare.
    ![Rimuovere l'assegnazione di Microsoft access - schermata](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Argomenti correlati

- Guida introduttiva a controllo dell'accesso basato sui ruoli alle [assegnazioni di ruolo usare per gestire l'accesso alle risorse dell'abbonamento Azure](role-based-access-control-configure.md)
- Vedere i [ruoli predefiniti RBAC](role-based-access-built-in-roles.md)
