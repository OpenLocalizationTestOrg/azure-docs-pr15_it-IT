<properties
    pageTitle="Assegnare licenze per MFA Azure | Microsoft Azure"
    description="Informazioni su come assegnare licenze utente per l'autenticazione a più fattori di Microsoft Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users"></a>Assegnazione di una licenza di Azure MFA, Azure Active Directory Premium o mobilità aziendale agli utenti

Se è stato acquistato MFA Azure, Azure Active Directory Premium o Enterprise mobilità Suite licenze, non è necessario creare un provider di autenticazione a più fattori. Dopo aver assegnato le licenze agli utenti, è possibile iniziare consentendo per MFA.

## <a name="to-assign-a-license"></a>Per assegnare una licenza

1. Accedere al [portale classica Azure](https://manage.windowsazure.com) come amministratore.
2. A sinistra, selezionare **Active Directory**.
3. Nella pagina Active Directory, fare doppio clic nella directory che contiene gli utenti che si desidera attivare.
4. Nella parte superiore della pagina della directory, selezionare **licenze**.
![Assegnare licenze](./media/multi-factor-authentication-get-started-assign-licenses/assign1.png)
5. Nella pagina licenze selezionare **Autenticazione a più fattori Azure**, **Active Directory Premium**o **Enterprise mobilità Suite**.  Se è una sola, quindi dovrebbe essere selezionata automaticamente.
6. Nella parte inferiore della pagina, fare clic su **Assegna**.
![Assegnare licenze](./media/multi-factor-authentication-get-started-assign-licenses/assign3.png)
6. Nella finestra che viene visualizzata, fare clic su accanto a utenti o gruppi a a che cui assegnare licenze.  Verrà visualizzato un segno di spunta verde visualizzato.
7. Fare clic sull'icona di segno di spunta per salvare le modifiche.
![Assegnare licenze](./media/multi-factor-authentication-get-started-assign-licenses/assign4.png)
8. È visualizzato un messaggio che indica quante licenze sono state assegnate e quante potrebbe non essere riuscita.  Fare clic su **Ok**.
![Assegnare licenze](./media/multi-factor-authentication-get-started-assign-licenses/assign5.png)
