

<properties
    pageTitle="Partecipare a un dispositivo personale per l'organizzazione | Microsoft Azure"
    description="Spiega come gli utenti possono eseguire la registrazione ai dispositivi Windows 10 personali per la rete aziendale e fornisce i passaggi di distribuzione per uno scenario BYOD."
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

# <a name="join-a-personal-device-to-your-organization"></a>Partecipare a un dispositivo personale dell'organizzazione

## <a name="to-join-a-windows-10-device-to-your-organization"></a>Per partecipare a un dispositivo con Windows 10 per l'organizzazione

1.  Scegliere **Impostazioni**dal menu **Start** .
2.  Selezionare **account**e quindi fare clic su **account**.
3.  Fare clic su **Aggiungi lavoro o dell'istituto di istruzione account**e quindi digitare nell'account dell'organizzazione.
4.  Nella pagina di accesso per l'organizzazione, immettere il nome utente e la password e quindi fare clic su **OK**.
5.  Verrà richiesto di una richiesta di autenticazione a più fattori. (Questo problema è configurabile da un amministratore IT).
6.  Azure Active Directory (Azure Active Directory) verifica se il dispositivo richiede la registrazione di gestione di dispositivi mobili.
7.  Windows registra il dispositivo nella directory dell'organizzazione in Azure Active Directory e registra nella gestione di dispositivi mobili, se appropriato.
8.  Se si è un utente gestito, Windows consente di accedere al desktop tramite l'automatica accesso.
9.  Se si è un utente federato, sarà accedere a una schermata di accesso di Windows per immettere le credenziali.

## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password tramite Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
