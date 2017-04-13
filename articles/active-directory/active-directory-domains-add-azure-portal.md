<properties
    pageTitle="Aggiungere il proprio nome di dominio personalizzato per l'anteprima di Azure Active Directory | Microsoft Azure"
    description="Come aggiungere i nomi di dominio della società a Azure Active Directory e come verificare il nome di dominio."
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
    ms.date="10/17/2016"
    ms.author="curtand"/>

# <a name="add-a-custom-domain-name-to-azure-active-directory-preview"></a>Aggiungere un nome di dominio personalizzato per l'anteprima di Azure Active Directory

> [AZURE.SELECTOR]
- [Portale di Azure](active-directory-domains-add-azure-portal.md)
- [Portale classica Azure](active-directory-add-domain.md)

Hai uno o più nomi di dominio che l'organizzazione utilizza per intrattengono rapporti commerciali e agli utenti l'accesso alla rete aziendale con il proprio nome di dominio aziendale. Utilizzo dell'anteprima di Azure Active Directory (Azure Active Directory), è possibile aggiungere il nome del dominio aziendale di Azure Active Directory anche. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) In questo modo è possibile assegnare i nomi nella directory degli utenti familiari agli utenti, ad esempio ‘alice@contoso.com.’ il processo è semplice:

1. Aggiungere il nome di dominio personalizzato alla directory
2. Aggiungere una voce del DNS per il nome di dominio presso il registrar di nomi di dominio
3. Verificare il nome di dominio personalizzato in Azure Active Directory

## <a name="how-do-i-add-a-domain-name"></a>Come è possibile aggiungere un nome di dominio?

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.

    ![Gestione degli utenti di apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Scegliere **i nomi di dominio**e il ***nome di directory*** .

4. Scegliere il comando **Aggiungi** e il ** *nome di directory* - i nomi di dominio** .

  ![Selezionare il comando Aggiungi](./media/active-directory-domains-add-azure-portal/add-command.png)

5. In e il **nome di dominio** , immettere il nome del dominio personalizzato nella casella, ad esempio 'contoso.com' e quindi selezionare **Aggiungi dominio**. Assicurarsi di includere il COM, .net o altra estensione principale.

6. In (ovvero, e il che verrà visualizzato con il nuovo nome di dominio nel titolo) e il ***NomeDominio*** informazioni DNS voce utilizzate Azure Active Directory per verificare che l'organizzazione appartiene il nome di dominio personalizzato.

  ![ottenere informazioni voce DNS](./media/active-directory-domains-add-azure-portal/get-dns-info.png)

Dopo aver aggiunto il nome di dominio, è necessario Azure Active Directory verificare che l'organizzazione appartiene il nome di dominio. Azure Active Directory poter eseguire la verifica, è necessario aggiungere una voce DNS nel file di zona DNS per il nome di dominio. Questa operazione viene eseguita nel sito Web per registrar di nomi di dominio per il nome di dominio.

## <a name="add-the-dns-entry-at-the-domain-name-registrar-for-the-domain"></a>Aggiungere la voce DNS presso il registrar di nomi di dominio per il dominio

Il passaggio successivo per usare il nome di dominio personalizzato con Azure Active Directory si aggiorna il file di zona DNS relativa al dominio. In questo modo Azure Active Directory verificare che l'organizzazione appartiene il nome di dominio personalizzato.

1.  Accedere al proprio registrar per il dominio. Se non si dispone di accesso per aggiornare la voce DNS, chiedere la persona o un team che ha questo accesso per completare il passaggio 2 e per comunicare al completamento.

2.  Aggiornare il file di zona DNS relativa al dominio aggiungendo la voce DNS fornita da Azure Active Directory. Questa voce DNS attiva Azure Active Directory verificare la proprietà del dominio. La voce DNS non modifica qualsiasi comportamenti, ad esempio routing della posta elettronica o il Web.

Per informazioni relative a questa aggiunta la voce DNS, leggere [le istruzioni per l'aggiunta di una voce del DNS in più diffusi Registrar DNS](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## <a name="verify-the-domain-name-with-azure-ad"></a>Verificare il nome di dominio con Azure Active Directory

Dopo aver aggiunto la voce DNS, si è pronti verificare il nome di dominio con Azure Active Directory.

È possibile verificare un nome di dominio solo dopo essere propagate i record DNS. Questa propagazione spesso bastano pochi secondi, ma in alcuni casi può richiedere più di un'ora. Se la verifica non funziona per la prima volta, e riprovare.

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **Sfoglia**, immettere la gestione degli utenti nella casella di testo e quindi premere **INVIO**.

    ![Gestione degli utenti di apertura](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Scegliere il nome di dominio non verificato che si desidera verificare e il **gestione degli utenti, i nomi di dominio** .

4. Scegliere **verifica** per completare la verifica e il ***NomeDominio*** (ovvero, e il che verrà visualizzato con il nuovo nome di dominio nel titolo).

A questo punto è possibile [assegnare i nomi utente che includono il nome di dominio personalizzato](active-directory-users-create-azure-portal.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non è possibile verificare un nome di dominio personalizzato, provare la soluzione seguente. Abbiamo verrà iniziare con la più comune e lavorare per difetto meno comuni.

1.  **Attendere un'ora**. I record DNS necessari propagare prima Azure Active Directory verificare il dominio. Questa operazione può richiedere più di un'ora.

2.  **Accertarsi che il record DNS è stata immessa, e che sia corretto**. Completare questo passaggio nel sito Web per il registrar di nomi di dominio per il dominio. Azure Active Directory non riesce a verificare il nome di dominio se la voce DNS non è presenta nel file di zona DNS o se non è una corrispondenza esatta con la voce DNS che Azure AD a disposizione. Se non si dispone di accesso per aggiornare i record DNS per il dominio presso il registrar di nomi di dominio, condividere la voce DNS con l'utente o il team dell'organizzazione che ha questo accesso e chiedere loro di aggiungere la voce DNS.

3.  **Eliminare il nome di dominio da un'altra directory in Azure Active Directory**. È possibile verificare un nome di dominio in un'unica directory. Se un nome di dominio in precedenza è stato verificato in un'altra directory, eliminarla sono prima che può essere verificato nella directory di nuovo. Per ulteriori informazioni sull'eliminazione di nomi di dominio, leggere [gestire i nomi di dominio personalizzato](active-directory-domains-manage-azure-portal.md).    

## <a name="add-more-custom-domain-names"></a>Aggiungere altri nomi di dominio personalizzato

Se l'organizzazione Usa più nomi di dominio personalizzato, ad esempio 'contoso.com' e 'contosobank.com', è possibile aggiungere fino a un massimo di 900 nomi di dominio. Utilizzare gli stessi passaggi in questo articolo per aggiungere i singoli nomi di dominio.

## <a name="next-steps"></a>Passaggi successivi

[Gestire i nomi di dominio personalizzato](active-directory-domains-manage-azure-portal.md)
