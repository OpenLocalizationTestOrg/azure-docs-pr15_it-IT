<properties
    pageTitle="Gestione dei gruppi di Azure Active Directory | Microsoft Azure"
    description="Informazioni su come creare e gestire i gruppi per gestire gli utenti di Azure con Azure Active Directory."
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
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Gestione dei gruppi di Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-groups-create-azure-portal.md)
- [Portale classica Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Una delle funzionalità di gestione degli utenti di Azure Active Directory (Azure Active Directory) è la possibilità di creare gruppi di utenti. Utilizzare un gruppo per eseguire attività di gestione, ad esempio l'assegnazione di licenze o le autorizzazioni per un numero di utenti contemporaneamente. È anche possibile utilizzare i gruppi per assegnare l'autorizzazione di accesso per

- Risorse, ad esempio gli oggetti della directory
- Risorse esterne alla directory, ad esempio applicazioni SaaS, servizi di Azure, siti di SharePoint o risorse locale

Inoltre, un proprietario della risorsa inoltre possibile assegnare l'accesso a una risorsa a un gruppo di Azure Active Directory di proprietà di un altro utente. L'assegnazione concede i membri del gruppo l'accesso alla risorsa. Quindi, il proprietario del gruppo consente di gestire l'appartenenza al gruppo. Proprietario della risorsa delega al proprietario del gruppo in modo efficace, l'autorizzazione per assegnare gli utenti per le risorse.

## <a name="how-do-i-create-a-group"></a>Creazione di un gruppo

A seconda dei servizi a cui si è abbonata l'organizzazione, è possibile creare un gruppo in una delle operazioni seguenti:
- il portale classico Azure
- il portale di account Office 365
- portale di account di Windows Intune

Verranno descritti attività come avviene nel portale di classica Azure. Per ulteriori informazioni sull'uso non Azure portali per gestire la directory di Azure Active Directory, vedere [amministrazione directory Azure Active Directory](active-directory-administer.md).

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi selezionare il nome della directory dell'organizzazione.

2. Selezionare la scheda **gruppi** .

3. Selezionare **Aggiungi gruppo**.

4. Nella finestra **Aggiungi gruppo** specificare il nome e la descrizione di un gruppo.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Come aggiungere o rimuovere singoli utenti in un gruppo di sicurezza

**Per aggiungere un singolo utente a un gruppo**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi selezionare il nome della directory dell'organizzazione.

2. Selezionare la scheda **gruppi** .

3. Aprire il gruppo a cui si vuole aggiungere membri. Aprire la scheda **membri** del gruppo selezionato se che non è già visualizzata.

4. Selezionare **Aggiungi membri**.

5. Nella pagina **Aggiungi membri** selezionare il nome dell'utente o un gruppo a cui si desidera aggiungere come membro del gruppo. Assicurarsi che il nome viene aggiunto al riquadro **selezionato** .


**Per rimuovere un singolo utente da un gruppo**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi selezionare il nome della directory dell'organizzazione.

2. Selezionare la scheda **gruppi** .

3. Aprire il gruppo dal quale si desidera rimuovere membri.

4. Selezionare la scheda **membri** , selezionare il nome del membro che si desidera rimuovere dal gruppo e quindi fare clic su **Rimuovi**.

6. Al prompt di confermare che si desidera rimuovere il membro del gruppo.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Come è possibile gestire l'appartenenza a un gruppo in modo dinamico?

In Azure Active Directory, è possibile facilmente impostare una regola semplice per determinare gli utenti che devono essere membri del gruppo. Una regola semplice corrisponde a uno che produca un singolo confronto. Ad esempio, se un gruppo è assegnato a un'applicazione di SaaS, è possibile impostare una regola per aggiungere gli utenti che hanno una posizione di "Agente di vendita". Questa regola quindi concede l'accesso all'applicazione SaaS a tutti gli utenti con tale titolo nella directory.

Quando gli attributi di una modifica utente, il sistema valuta tutte le regole di gruppo dinamico in una directory per verificare se la modifica degli attributi dell'utente verrà generato un gruppo di aggiunge o Elimina. Se un utente soddisfa una regola in un gruppo, vengono aggiunti come membri al gruppo. Se non è più soddisfano la regola di un gruppo di di che un membro, questi vengono rimossi come un membri da tale gruppo.

> [AZURE.NOTE] È possibile impostare una regola per l'appartenenza dinamica in gruppi di sicurezza o i gruppi di Office 365. Appartenenze annidata attualmente non sono supportate per l'assegnazione al gruppo in base alle applicazioni.
>
> Appartenenze dinamiche per i gruppi di richiedono una licenza di Azure Active Directory Premium da assegnare alla
>
> - L'amministratore che gestisce la regola su un gruppo
> - Tutti i membri del gruppo

**Per abilitare l'appartenenza dinamico per un gruppo**

1. Nel [portale classica Azure](https://manage.windowsazure.com), selezionare **Active Directory**e quindi selezionare il nome della directory dell'organizzazione.

2. Selezionare la scheda **gruppi** e aprire il gruppo che si desidera modificare.

3. Selezionare la scheda **Configura** e quindi impostare **Abilitare dinamico appartenenze** su **Sì**.

4. Configurare una regola singola semplice per il gruppo di controllare l'appartenenza dinamico per questa funzioni di gruppo. Verificare che il **aggiungere utenti in** opzione è selezionata e quindi selezionare una proprietà dell'utente dall'elenco (ad esempio, reparto, jobTitle, ecc.)

5. Selezionare una condizione (non è uguale a, uguale a, non inizia con, inizia con, non contiene, contiene, non corrispondono, maiuscole).

6. Specificare un valore di confronto per la proprietà utente selezionato.

Per informazioni su come creare regole *Avanzate* (regole che possono contenere più confronti) per l'appartenenza ai gruppi dinamici, vedere [attributi di utilizzo per creare regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)

* [Che cos'è Azure Active Directory?](active-directory-whatis.md)

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
