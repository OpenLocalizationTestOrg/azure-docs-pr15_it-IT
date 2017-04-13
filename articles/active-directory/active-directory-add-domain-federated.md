<properties
    pageTitle="Aggiungere il proprio nome di dominio personalizzato e configurare federati sign-on di Azure Active Directory | Microsoft Azure"
    description="Come aggiungere i nomi di dominio della società di Azure Active Directory e come configurare federati sign-on tra Azure Active Directory e la soluzione federazione locale."
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
    ms.topic="get-started-article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="add-your-custom-domain-name-to-azure-active-directory"></a>Aggiungere il proprio nome di dominio personalizzato in Azure Active Directory

È possibile configurare un nome di dominio personalizzato, ad esempio 'contoso.com', in modo che gli utenti in contoso.com possono avere una federati esperienza single sign-on dalla rete aziendale. Se si dispone già di Active Directory Federation Services (ADFS) o un server federativo diversi in esecuzione nella rete aziendale, è possibile configurare Azure Active Directory per usare il nome di dominio personalizzato tramite lo strumento di Azure AD Connect. È anche possibile utilizzare Azure AD Connect per distribuire un nuovo ambiente di ADFS e configurare che federati single sign-on di Azure Active Directory.

Se si dispone e non si prevede di distribuire ADFS o un altro server federativo, seguire queste istruzioni: [aggiungere un nome di dominio personalizzato in Azure Active Directory](active-directory-add-domain.md).

## <a name="add-a-custom-domain-name-to-your-directory"></a>Aggiungere un nome di dominio personalizzato alla directory

1. Accedere al [portale classica Azure](https://manage.windowsazure.com/) con un account utente che è un amministratore globale della directory di Azure Active Directory.

2. In **Active Directory**, aprire la directory e selezionare la scheda **domini** .

3. Barra dei comandi, selezionare **Aggiungi**e quindi immettere il nome di dominio personalizzato, ad esempio 'contoso.com'. Assicurarsi di includere il COM, .net o altra estensione principale.

4. Selezionare la casella di controllo **intende configurare il dominio per single sign-on con il servizio Active Directory locale** .

5. Selezionare **Aggiungi**.

Eseguire lo strumento di Azure AD Connect per ottenere la voce DNS che verrà utilizzato Azure Active Directory per verificare il dominio. Verrà visualizzata la voce DNS nel passaggio della procedura guidata **Dominio di Azure Active Directory** . Si può vedere cosa passaggio della procedura guidata è simile [in queste istruzioni](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). Se non è lo strumento di Azure AD Connect, è possibile [scaricarlo qui](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Aggiungere la voce DNS presso il registrar di nomi di dominio per il dominio

Il passaggio successivo per usare il nome di dominio personalizzato con Azure Active Directory si aggiorna il file di zona DNS relativa al dominio. In questo modo Azure Active Directory verificare che l'organizzazione appartiene il nome di dominio personalizzato.

1. Accedere al sito Web per registrar di nomi di dominio per il nome di dominio. Se non si dispone di accesso per eseguire questa operazione, chiedere la persona o un team all'interno dell'organizzazione che ha questo accesso per completare il passaggio 2 e per comunicare al completamento.

2. Aggiornare il file di zona DNS relativa al dominio aggiungendo la voce DNS fornita da Azure Active Directory. Questa voce DNS attiva Azure Active Directory verificare la proprietà del dominio. La voce DNS non modifica qualsiasi comportamenti, ad esempio routing della posta elettronica o il Web.

Per informazioni su questo passaggio, leggere [le istruzioni per l'aggiunta di una voce del DNS in più diffusi Registrar DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificare il nome di dominio con Azure Active Directory

Dopo aver aggiunto la voce DNS, si è pronti verificare il nome di dominio con Azure Active Directory.

Per verificare il dominio, selezionare **Avanti** nel passaggio della procedura guidata Azure AD Connect **Dominio di Azure Active Directory** . Azure Active Directory vengono rappresentati per la voce DNS nel file di zona DNS relativa al dominio. Azure Active Directory verificare il nome di dominio solo dopo essere propagate i record DNS. Propagazione spesso bastano pochi secondi, ma in alcuni casi può richiedere più di un'ora. Se la verifica non funziona per la prima volta, e riprovare.

Procedere con la procedura guidata Azure AD Connect. Verranno sincronizzati agli utenti di Windows Server Active Directory di Azure Active Directory. Utenti sincronizzati nel dominio è configurato per la federazione sarà possibile ottenere una federati esperienza single sign-on dalla rete aziendale per Azure Active Directory.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non è possibile verificare un nome di dominio personalizzato, provare la soluzione seguente. Abbiamo verrà iniziare con la più comune e lavorare per difetto meno comuni.

1.  **Attendere un'ora**. I record DNS necessari propagare prima Azure Active Directory verificare il dominio. Questa operazione può richiedere più di un'ora.

2.  **Accertarsi che il record DNS è stata immessa, e che sia corretto**. Completare questo passaggio nel sito Web per il registrar di nomi di dominio per il dominio. Azure Active Directory non riesce a verificare il nome di dominio se la voce DNS non è presenta nel file di zona DNS o se non è una corrispondenza esatta con la voce DNS che Azure AD a disposizione. Se non si dispone di accesso per aggiornare i record DNS per il dominio presso il registrar di nomi di dominio, condividere la voce DNS con l'utente o il team dell'organizzazione che ha questo accesso e chiedere loro di aggiungere la voce DNS.

3.  **Eliminare il nome di dominio da un'altra directory in Azure Active Directory**. È possibile verificare un nome di dominio in un'unica directory. Se un nome di dominio in precedenza è stato verificato in un'altra directory, eliminarla sono prima che può essere verificato nella directory di nuovo. Per ulteriori informazioni sull'eliminazione di nomi di dominio, leggere [gestire i nomi di dominio personalizzato](active-directory-add-manage-domain-names.md).

## <a name="add-more-custom-domain-names"></a>Aggiungere altri nomi di dominio personalizzato

Se l'organizzazione Usa più nomi di dominio personalizzato, ad esempio 'contoso.com' e 'contosobank.com', è possibile aggiungere fino a un massimo di 900 nomi di dominio. Utilizzare gli stessi passaggi in questo articolo per aggiungere i singoli nomi di dominio.

## <a name="next-steps"></a>Passaggi successivi

-   [Gestire i nomi di dominio personalizzato](active-directory-add-manage-domain-names.md)
-   [Informazioni sui concetti relativi alla gestione dominio Azure Active Directory](active-directory-add-domain-concepts.md)
-   [Mostra che la personalizzazione della società quando l'accesso per gli utenti](active-directory-add-company-branding.md)
-   [Usare PowerShell per gestire i nomi di dominio in Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)
