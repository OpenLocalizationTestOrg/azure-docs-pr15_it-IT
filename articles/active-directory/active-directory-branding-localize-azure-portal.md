<properties
pageTitle="Aggiungere marchio per la pagina di accesso nell'anteprima di Azure Active Directory della società specifiche della lingua | Microsoft Azure"
description="Informazioni su come aggiungere una società specifica lingua personalizzazione immagini e testo in una pagina di accesso Azure"
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
ms.date="09/12/2016"
ms.author="curtand"/>

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Aggiungere società specifiche della lingua personalizzare la pagina di accesso nell'anteprima di Azure Active Directory

Per evitare confusione, da applicare un aspetto coerente in tutti i siti Web e i servizi che gestiscono molte società. Anteprima di Azure Active Directory fornisce questa funzionalità consente di personalizzare l'aspetto della pagina di accesso con il logo della società e le combinazioni di colori personalizzata. [Che cos'è nel riquadro di anteprima?](active-directory-preview-explainer.md) La pagina di accesso è la pagina che viene visualizzato quando si accede a Office 365 o altre applicazioni basate sul web che utilizzano Azure Active Directory come provider di identità. È interagire con questa pagina per immettere le credenziali.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personalizzazione della pagina di accesso per un'altra lingua

È possibile aggiungere elementi specifici della lingua per la pagina di accesso personalizzata solo se è già stato creato una pagina di accesso personalizzata come descritto nella [aggiungere marchio per la pagina di accesso della società](active-directory-branding-custom-signon-azure-portal.md). È possibile configurare una pagina di accesso per directory con un set predefinito di elementi personalizzabili. Dopo aver configurato il set predefinito di elementi della pagina, è possibile configurare le altre versioni per impostazioni locali diverse. È anche possibile combinare e corrispondono a vari elementi. Ad esempio, è possibile:

- Creare un' **immagine di pagina di accesso** che funziona per tutte le lingue e quindi Crea versioni specifiche per l'inglese e francese predefinita. Quando si imposta il browser a uno di questi due lingue, viene visualizzata l'immagine specifiche della lingua, anche se viene visualizzata l'illustrazione predefinito per tutte le altre lingue.

- Configurare un logo diverso per l'organizzazione (ad esempio le versioni di ebraico o giapponese).

È consigliabile mantenere il numero di varianti di lingua bassa, per motivi di manutenzione e prestazioni.

**Per aggiungere marchio alla directory della società:**

1.  Accedere al [portale di Azure](https://portal.azure.com) con un account è un amministratore globale per la directory.

2.  Selezionare **altri servizi**, immettere **gli utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

    ![Gestione degli utenti di apertura](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. Scegliere **personalizzazione società**e **l'utenti e gruppi** .

4. Nel **utenti e gruppi - società branding** blade, selezionare il comando **Aggiungi una lingua** .

    ![Aggiungere elementi di personalizzazione specifiche della lingua](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modificare gli elementi che si desidera personalizzare. Tutti gli elementi sono facoltativi.

6. Fare clic su **Salva**.

Può essere necessaria fino a un'ora per tutte le modifiche alla pagina Personalizzazione per visualizzare accesso.

## <a name="next-steps"></a>Passaggi successivi

[Aggiungere marchio per la pagina di accesso della società](active-directory-branding-custom-signon-azure-portal.md)
