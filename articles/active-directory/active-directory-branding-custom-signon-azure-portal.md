<properties
pageTitle="Personalizzare la pagina di accesso nell'anteprima di Azure Active Directory | Microsoft Azure"
description="Informazioni su come aggiungere una marchio alla pagina di accesso Azure della società"
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
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Aggiungere società personalizzare la pagina di accesso nell'anteprima di Azure Active Directory

Per evitare confusione, da applicare un aspetto coerente in tutti i siti Web e i servizi che gestiscono molte società. Anteprima di Azure Active Directory fornisce questa funzionalità consente di personalizzare l'aspetto della pagina di accesso con il logo della società e le combinazioni di colori personalizzata. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) La pagina di accesso è la pagina che viene visualizzato quando si accede a Office 365 o altre applicazioni basate sul web che utilizzano Azure Active Directory come provider di identità. È interagire con questa pagina per immettere le credenziali.

Se si desidera visualizzare il marchio dell'azienda, colori e altri elementi personalizzabili in questa pagina, vedere le immagini seguenti la differenza tra le due esperienze.

Screenshot che mostra le seguenti esempio per la pagina di accesso di Office 365 in un computer desktop **prima di** una personalizzazione:

![Pagina accesso di Office 365 prima di personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

Screenshot che mostra le seguenti esempio per la pagina di accesso di Office 365 in un computer desktop **dopo** una personalizzazione:

![Pagina accesso di Office 365 dopo la personalizzazione](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personalizzazione della pagina di accesso

In genere, se è necessario basate su browser accedere ai servizi che l'organizzazione si iscrive a applicazioni basate su cloud, utilizzare la pagina di accesso.

Se è stato applicato modifiche per la pagina di accesso, può richiedere fino a un'ora visualizzare le modifiche.

Una pagina di accesso personalizzata viene visualizzata solo quando si visita un servizio con un URL specifico tenant, ad esempio**contoso**https://outlook.com/. com o https://mail. **Contoso**. com.

Quando si visita un servizio con URL specifici non tenant (ad esempio: https://mail.office365.com), viene visualizzata una pagina accesso non personalizzata. In questo caso, il marchio visualizzato dopo avere immesso il proprio ID utente o è stato selezionato il riquadro di un utente.

> [AZURE.NOTE]
>
- Il nome del dominio deve essere visualizzata come "Attivo" nella parte di **domini** del portale di Azure in cui è stato configurato personalizzazione. Per ulteriori informazioni, vedere [aggiungere i nomi di dominio personalizzato](active-directory-domains-add-azure-portal.md).
- Pagina di accesso personalizzazioni non trasferite al segno consumer nella pagina di Microsoft. Se l'accesso con un account Microsoft, viene visualizzato un elenco personalizzato di riquadri di utente esegue il rendering Azure Active Directory, ma la personalizzazione dell'organizzazione non è valida per la pagina di accesso dell'account Microsoft.

La pagina di accesso, la casella di controllo **Mantieni la connessione** consente agli utenti rimanere connessi quando chiudere e riaprire il browser. 

   ![Mantieni la firma in](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Effetto non la durata della sessione. È possibile nascondere la casella di controllo nella pagina di accesso di Azure Active Directory.
Se viene visualizzata la casella di controllo dipende dall'impostazione di **Mantieni la connessione disabilitato**.

   ![Mantieni la firma in](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Per nascondere la casella di controllo, configurare questa impostazione su **Sì**. 

> [AZURE.NOTE] Alcune caratteristiche di SharePoint Online e Office 2010 dipendono da utenti la possibilità di selezionare questa casella di controllo. Se si configura questa impostazione per nascosta, gli utenti venga visualizzata istruzioni aggiuntive e imprevisti effettuare l'accesso.




**Per aggiungere marchio alla directory della società:**

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **gli utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

    ![Gestione degli utenti di apertura](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Scegliere **personalizzazione società**e **l'utenti e gruppi** .

4. Nel **utenti e gruppi - società branding** blade, selezionare il comando **Modifica** .

    ![Modificare la personalizzazione del marchio](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modificare gli elementi che si desidera personalizzare. Tutti gli elementi sono facoltativi.

6. Fare clic su **Salva**.

Può essere necessaria fino a un'ora per tutte le modifiche alla pagina Personalizzazione per visualizzare accesso.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere marchio della società specifiche della lingua](active-directory-branding-localize-azure-portal.md)
