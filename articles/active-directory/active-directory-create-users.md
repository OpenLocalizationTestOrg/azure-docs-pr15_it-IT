<properties
    pageTitle="Aggiungere nuovi utenti di Azure Active Directory | Microsoft Azure"
    description="In questo articolo viene spiegato come aggiungere nuovi utenti o modificare le informazioni di Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>Aggiungere nuovi utenti o gli utenti con un account di Microsoft Azure Active Directory

Aggiungere gli utenti per popolare la directory. In questo articolo viene spiegato come aggiungere nuovi utenti all'interno dell'organizzazione e come aggiungere utenti che hanno un account Microsoft. Per ulteriori informazioni sull'aggiunta di utenti da altre directory di Azure Active Directory o aggiunta di utenti dalla società partner, vedere [aggiungere utenti da altre directory o una società partner di Azure Active Directory](active-directory-create-users-external.md). Utenti aggiunti non dispongono delle autorizzazioni di amministratore per impostazione predefinita, ma è possibile assegnare i ruoli a tali in qualsiasi momento.

## <a name="add-a-user"></a>Aggiungere un utente

1. Accedere al [portale classica Azure](https://manage.windowsazure.com) con un account è un amministratore globale per la directory.
2. Selezionare **Active Directory**e quindi selezionare il nome della directory dell'organizzazione.
3. Selezionare la scheda **utenti** e quindi, nella barra dei comandi, selezionare **Aggiungi utente**.
4. Nella pagina **inviare commenti e suggerimenti su questo utente** , in **tipo di utente**, selezionare:

    - **Nuovo utente nell'organizzazione** : aggiunge un nuovo account utente nella directory.
    - **Gli utenti che hanno un account Microsoft** : aggiunge un account di consumer Microsoft esistente alla directory (ad esempio un account di Outlook)

5. In base al **tipo di utente**, immettere un nome utente (per i nuovi utenti) o un indirizzo di posta elettronica (per un utente con un account Microsoft).
6. Nella pagina del **profilo** utente, specificare un nome e cognome, un nome descrittivo e un ruolo di utente dall'elenco **ruoli** . Per ulteriori informazioni sui ruoli di amministratore e utente, vedere [assegnazione di ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md). Specificare se si desidera **Abilitare l'autenticazione a più fattori** per l'utente.
7. Nella pagina **Guida password temporanea** selezionare **Crea**.

> [AZURE.IMPORTANT] Se l'organizzazione Usa più domini, è necessario conoscere i problemi seguenti quando si aggiunge un account utente:
>
> - Per aggiungere gli account utente con lo stesso nome dell'entità utente (UPN) in domini, **prima** aggiungere, ad esempio geoffgrisso@contoso.onmicrosoft.com, **seguito da** geoffgrisso@contoso.com.
> - **Non** aggiungere geoffgrisso@contoso.com prima di aggiungere geoffgrisso@contoso.onmicrosoft.com. In questo ordine è importante e può essere difficile da annullare.

## <a name="change-user-information"></a>Modificare le informazioni utente

È possibile modificare l'attributo qualsiasi utente ad eccezione di ID dell'oggetto.

1. Aprire la directory.
2. Selezionare la scheda **utenti** e quindi selezionare il nome visualizzato dell'utente che si desidera modificare.
3. Completare le modifiche e quindi fare clic su **Salva**.

Se l'utente che si desidera modificare è sincronizzato con il servizio Active Directory locale, è possibile modificare le informazioni utente mediante questa procedura. Per modificare l'utente, utilizzare gli strumenti di gestione di Active Directory locale.

## <a name="guest-user-management-and-limitations"></a>Limitazioni e gestione di utenti Guest

Gli account guest sono gli utenti da altre directory che sono stati invitati alla directory per accedere a documenti di SharePoint, applicazioni o altre risorse Azure. Un account guest nella directory è l'attributo UserType sottostante impostata su "Guest". Gli utenti regolari (in particolare, i membri della directory) hanno l'attributo UserType "Membro".

Ospiti di un numero limitato di diritti nella directory. Questi diritti limitano la possibilità degli utenti abilitati individuare informazioni sugli altri utenti nella directory. Tuttavia, gli utenti guest possono comunque interagire con gli utenti e gruppi associati alle risorse che cui stanno lavorando. Gli utenti guest possono:

- Visualizzare altri utenti e gruppi associati a un abbonamento Azure a cui sono state assegnate
- Vedere i membri dei gruppi a cui appartengono
- Cercare informazioni su altri utenti nella directory, se si conosce l'indirizzo di posta elettronica completo dell'utente
- Visualizzare solo un numero limitato di attributi degli utenti che cercare - limitati per visualizzare nome, indirizzo di posta elettronica, nome dell'entità utente (UPN) e foto di anteprima
- Ottenere un elenco di domini verificati nella directory
- Consenso alle applicazioni, concedere loro l'accesso stesso di membri nella directory

## <a name="set-guest-user-access-policies"></a>Impostare i criteri di accesso utente guest

La scheda **configurazione** di una directory include opzioni per controllare l'accesso per gli utenti guest. Queste opzioni possono essere modificate solo nel portale classica Azure da un amministratore globale di directory. Attualmente non è in alcun modo PowerShell o API.

Per aprire la scheda **Configura** nel portale di classica Azure, selezionare **Active Directory**e quindi selezionare il nome della directory.

![Configurare la scheda in Azure Active Directory][1]

È possibile modificare le opzioni per controllare l'accesso per gli utenti guest.

![opzioni di controllo di accesso per gli utenti guest][2]


## <a name="whats-next"></a>Che cos'è successiva

- [Aggiungere gli utenti di altre directory o una società partner di Azure Active Directory](active-directory-create-users-external.md)
- [Amministrazione di Azure Active Directory](active-directory-administer.md)
- [Gestire le password in Azure Active Directory](active-directory-manage-passwords.md)
- [Gestire i gruppi in Azure Active Directory](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png
