<properties
    pageTitle="Gestione dei nomi di dominio personalizzato nell'anteprima di Azure Active Directory | Microsoft Azure"
    description="Concetti relativi alla gestione e procedure per la gestione di un nome di dominio di Azure Active Directory"
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
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Gestione dei nomi di dominio personalizzato nell'anteprima di Azure Active Directory

Un nome di dominio è una parte importante dell'identificatore per molte risorse directory: si tratta di parte di un indirizzo di posta elettronica o nome utente per un utente, parte dell'indirizzo di un gruppo, possono far parte della finestra dell'app ID URI per un'applicazione. Una risorsa in anteprima di Azure Active Directory (Azure Active Directory) è possibile includere un nome di dominio già verificato per la directory che contiene la risorsa di proprietà. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) Solo un amministratore globale può eseguire le attività di gestione di dominio in Azure Active Directory.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Impostare il nome di dominio primario per la directory di Azure Active Directory

Dopo avere creata la directory, il nome di dominio iniziale, ad esempio 'contoso.onmicrosoft.com', è anche il nome di dominio primario. Dominio primario è il nome di dominio predefinito per un nuovo utente quando si crea un nuovo utente. Consente di semplificare il processo per un amministratore creare nuovi utenti nel portale. Per modificare il nome di dominio primario:

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.

    ![Gestione degli utenti di apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Scegliere **i nomi di dominio**e il ***nome di directory*** .

4. Scegliere il nome di dominio che si desidera rendere il nome di dominio primario e il ** *nome di directory* - i nomi di dominio** .

5.  Scegliere il comando **rendere principale** e il ***NomeDominio*** (ovvero, e il che verrà visualizzato con il nuovo nome di dominio nel titolo). Confermare la scelta quando richiesto.

    ![Effettuare un nome di dominio primario](./media/active-directory-domains-manage-azure-portal/make-primary.png)

È possibile modificare il nome di dominio primario per la directory da qualsiasi dominio personalizzato verificato che non è federato. Modificare il dominio primario per la directory, i nomi utente per qualsiasi utente esistente rimarrà invariato.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Aggiungere i nomi di dominio personalizzato a un annuncio Azure

È possibile aggiungere fino a 900 nomi di dominio personalizzato per ogni directory di Azure Active Directory. La procedura per [aggiungere un nome di dominio personalizzato aggiuntivo](active-directory-domains-add-azure-portal.md) corrisponde al primo nome di dominio personalizzato.

## <a name="add-subdomains-of-a-custom-domain"></a>Aggiungere sottodomini di un dominio personalizzato

Se si desidera aggiungere un nome di dominio di terzo livello, ad esempio 'europe.contoso.com' alla directory, è necessario aggiungere e verificare il dominio di secondo livello, ad esempio contoso.com. Verrà verificato automaticamente il sottodominio da Azure Active Directory. Per vedere che si è verificato il sottodominio aggiunto, aggiornare la pagina nel browser in cui sono elencati i domini.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Cosa fare se si cambia il registrar DNS per il nome di dominio personalizzato

Se si cambia il registrar DNS per il nome di dominio personalizzato, è possibile continuare a usare il nome di dominio personalizzato con Azure Active Directory stessa senza interruzioni e senza ulteriori operazioni di configurazione. Se si usa il nome di dominio personalizzato con Office 365, Intune o altri servizi che si basano su nomi di dominio personalizzato in Azure Active Directory, consultare la documentazione relativa a tali servizi.

## <a name="delete-a-custom-domain-name"></a>Eliminazione di un nome di dominio personalizzato

Se l'organizzazione non usa tale nome di dominio o se è necessario utilizzare tale nome di dominio con un altro pubblicitario Azure, è possibile eliminare un nome di dominio personalizzato da un annuncio Azure.

Per eliminare un nome di dominio personalizzato, è necessario assicurarsi che nessuna risorsa nella directory si basa sul nome di dominio. Non è possibile eliminare un nome di dominio dalla directory se:

-   Qualsiasi utente ha un nome utente, un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.

-   Qualsiasi gruppo ha un indirizzo di posta elettronica o un indirizzo proxy che include il nome di dominio.

-   Qualsiasi applicazione di un annuncio Azure include un'app URI ID che include il nome di dominio.

È necessario modificare o eliminare tale risorsa nella directory di Azure Active Directory prima di poter eliminare il nome di dominio personalizzato.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Usare PowerShell o API di grafico per gestire i nomi di dominio

La maggior parte delle attività di gestione dei nomi di dominio di Azure Active Directory possono essere eseguite anche mediante Microsoft PowerShell o a livello di programmazione API di Azure Active Directory grafico (in anteprima pubblico).

-   [Uso di PowerShell per gestire i nomi di dominio in Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [Uso di API di grafico per gestire i nomi di dominio in Azure Active Directory](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Passaggi successivi

-   [Aggiungere i nomi di dominio personalizzato](active-directory-domains-add-azure-portal.md)
