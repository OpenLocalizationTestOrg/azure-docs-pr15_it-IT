<properties
   pageTitle="Gestire la directory per l'abbonamento a Office 365 in Azure | Microsoft Azure"
   description="Gestione di una directory di abbonamento a Office 365 tramite il portale classico Azure e Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gestire la directory per l'abbonamento a Office 365 in Azure

In questo articolo viene descritto come gestire una directory che è stata creata per un abbonamento a Office 365, tramite il portale classico Azure. È necessario essere l'amministratore del servizio o un amministratore di creazione di un abbonamento Azure ad accedere al portale di classica Azure. Se ancora non si dispone un abbonamento a Azure, è possibile iscriversi a un [periodo di prova di 30 giorni gratuito](https://azure.microsoft.com/trial/get-started-active-directory/) oggi e distribuire la soluzione di cloud prima in 5 minuti, usando questo collegamento. Assicurarsi di usare l'account aziendale o dell'istituto di istruzione che consente di accedere a Office 365.

Dopo aver completato la sottoscrizione di Azure, è possibile accedere al portale di classica Azure e accedere ai servizi Azure. Fare clic sull'estensione di Active Directory per gestire la stessa directory che autentica gli utenti di Office 365.

Se si dispone già di un abbonamento a Azure, la procedura per gestire in una directory aggiuntiva anche è semplice. Ad esempio Michael Smith potrebbero avere un abbonamento a Office 365 per Contoso.com. Ha anche un abbonamento Azure che ha effettuato l'iscrizione a utilizzando il suo account Microsoft, msmith@hotmail.com. In questo caso, gestisce due directory.

  Abbonamento |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nome visualizzato |  Contoso  |     Directory di Azure Active Directory (Azure Active Directory) predefinita
  Nome di dominio  |  Contoso.com  | msmithhotmail.onmicrosoft.com

Si desidera gestire le identità utente nella directory di Contoso mentre si è effettuato l'accesso a Azure con il suo account Microsoft, in modo che è possibile abilitare la funzionalità di Azure Active Directory, ad esempio l'autenticazione. Nel diagramma seguente illustra meglio il processo.

![Diagramma per gestire due directory indipendenti](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

In questo caso, due directory sono indipendenti tra loro.

## <a name="to-manage-two-independent-directories"></a>Per gestire due directory indipendenti
Affinché Michael Smith gestire entrambe le directory mentre si è effettuato l'accesso a Azure come msmith@hotmail.com, è necessario completare la procedura seguente:

> [AZURE.NOTE]
> Solo quando un utente è stato effettuato l'accesso con un account Microsoft, è possono eseguire la procedura seguente. Se si è eseguito l'accesso con un lavoro o dell'istituto di istruzione account, l'opzione per **utilizzare directory esistente** non è disponibile. Un account aziendale o dell'istituto di istruzione può essere autenticato solo da home directory (ovvero la directory in cui è archiviato l'account aziendale o dell'istituto di istruzione e proprietario dell'azienda o dell'istituto di istruzione).

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com) come msmith@hotmail.com.
2.  Fare clic su **Nuovo** > **servizi App** > **Active Directory** > **Directory** > **Creare personalizzata**.
3.  Fare clic su utilizza directory esistente e selezionare la casella di controllo **sono pronto per essere disconnessi ora** .
4.  Accedere al portale di classica Azure come amministratore globale di Contoso.onmicrosoft.com (ad esempio msmith@contoso.com).
5.  Quando viene richiesto di **utilizzare la directory di Contoso con Azure?**, fare clic su **Continua**.
6.  Fare clic su **Esci**.
7.  Accedere al portale di classica Azure come msmith@hotmail.com. L'estensione di Active Directory vengono visualizzati nella directory di Contoso e la directory predefinita.

Al termine della procedura msmith@hotmail.com è un amministratore globale nella directory di Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Amministrare le risorse come amministratore globale
A questo punto si supponga che Olga Ferraro deve amministrare le risorse di siti Web e database presenti associate all'abbonamento Azure per msmith@hotmail.com. Prima effettuare questa operazione, Anna può Michael Smith deve completare questi passaggi aggiuntivi:

1.  Accedere al [portale classica Azure](https://manage.windowsazure.com) usando l'account di amministratore del servizio per la sottoscrizione Azure (in questo esempio, msmith@hotmail.com).
2.  Trasferire la sottoscrizione a nella directory di Contoso: fare clic su **Impostazioni** > **abbonamenti** > selezionare l'abbonamento > **Modifica Directory** > selezionare **Contoso (Contoso.com)**. Durante il trasferimento, eventuali modifiche o dell'istituto di istruzione vengono rimossi gli account coamministratori della sottoscrizione.
3.  Aggiungere Olga Ferraro come amministratore della creazione della sottoscrizione: fare clic su **Impostazioni** > **amministratori** > selezionare l'abbonamento > **Aggiungi** > tipo **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulla relazione tra le sottoscrizioni e directory, vedere [come una sottoscrizione è associata a una directory](active-directory-how-subscriptions-associated-directory.md).
