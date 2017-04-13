<properties
    pageTitle="Configurare un dispositivo Windows 10 con Azure Active Directory dalle impostazioni | Microsoft Azure"
    description="Spiega come gli utenti possono partecipare in Azure Active Directory tramite il menu impostazioni."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""
    tags="azure-classic-portal"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="set-up-a-windows-10-device-with-azure-ad-from-settings"></a>Configurare un dispositivo Windows 10 con Azure Active Directory dalle impostazioni
Se si usa già Windows 7 o Windows 8 e il computer o dispositivo è stato aggiornato a Windows 10, è possibile partecipare a Azure Active Directory (Azure Active Directory) tramite il menu impostazioni.

## <a name="to-join-to-azure-ad-from-the-settings-menu"></a>Per partecipare a Azure Active Directory dal menu impostazioni


1. Dal menu **Start** fare clic su accesso alle **Impostazioni** .
2. In **Impostazioni**selezionare **sistema**->**sui**->**partecipare Azure Active Directory**.
<center>
![Partecipare a Azure Active Directory dal menu impostazioni](./media/active-directory-azureadjoin/active-directory-azureadjoin-settings.png)</center>

3. Fare clic su **Continua** nella finestra di messaggio Azure Active Directory partecipare.
<center>
![Finestra di messaggio join Azure Active Directory](./media/active-directory-azureadjoin/active-directory-azureadjoin-message.png)</center>
4. Specificare le credenziali di accesso. L'esperienza di accesso verrà inclusi tutti i passaggi necessari per completare l'autenticazione. Se fanno parte di un tenant federato, l'amministratore verrà visualizzato l'esperienza di federazione ospitati dall'organizzazione.
<center>
![Specificare le credenziali di accesso](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)</center>
5. Se l'organizzazione ha configurato l'autenticazione a più fattori Azure per partecipare a Azure Active Directory, fornire il secondo fattore prima di continuare.
6. Fare clic su **accetta** nella schermata **Consenti al dispositivo da gestire** .
7. Verrà visualizzato il messaggio "il dispositivo è ora unito all'organizzazione di Azure Active Directory".


## <a name="additional-information"></a>Ulteriori informazioni
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
* [Autenticazione delle identità senza password tramite Microsoft Passport](active-directory-azureadjoin-passport.md)
