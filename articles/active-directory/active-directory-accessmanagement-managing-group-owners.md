
<properties
    pageTitle="Passaggi successivi per la gestione di access con i gruppi | Microsoft Azure"
    description="Modalità avanzata-per del gestione dei gruppi di sicurezza e su come utilizzare questi gruppi per gestire l'accesso a una risorsa."
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
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="curtand"/>

# <a name="managing-owners-for-a-group"></a>Gestione dei proprietari di un gruppo
Una volta un proprietario della risorsa è assegnata l'accesso a una risorsa a un gruppo di Azure Active Directory, l'appartenenza del gruppo viene gestita da al proprietario del gruppo. Proprietario della risorsa delega in modo efficace l'autorizzazione per assegnare gli utenti per la risorsa al proprietario del gruppo.

## <a name="assigning-group-ownership"></a>Assegnare la proprietà di gruppo

**Per aggiungere un proprietario a un gruppo**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi aprire la directory dell'organizzazione.

2. Selezionare la scheda **gruppi** e quindi aprire il gruppo che si desidera aggiungere proprietari da.

3. Selezionare **Aggiungi proprietari**.

4. Nella pagina **Aggiungi proprietari** , selezionare l'utente che si desidera aggiungere come proprietario del gruppo e verificare che il nome viene aggiunto al riquadro **selezionato** .


**Per rimuovere un proprietario da un gruppo**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi aprire la directory dell'organizzazione.

2. Selezionare la scheda **gruppi** e quindi aprire il gruppo che si desidera rimuovere un proprietario dal.

4. Selezionare la scheda **proprietari** .

5. Selezionare il proprietario che si desidera rimuovere dal gruppo e quindi scegliere **Rimuovi**.

## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)
* [Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi](active-directory-accessmanagement-groups-settings-cmdlets.md)
* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)
* [Che cos'è Azure Active Directory?](active-directory-whatis.md)
* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
