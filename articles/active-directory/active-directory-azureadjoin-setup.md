<properties
    pageTitle="Impostazione di Azure Active Directory Join per gli utenti | Microsoft Azure"
    description="Spiega come gli amministratori possono configurare Azure Active Directory partecipare di directory locale e di registrazione di dispositivo."
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
    ms.topic="get-started-article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="setting-up-azure-ad-join-in-your-organization"></a>La configurazione di Azure Active Directory partecipare all'interno dell'organizzazione

Prima di configurare Azure Active Directory partecipare (Azure Active Directory Join), è necessario sincronizzare la directory locale degli utenti nel cloud o creare manualmente gli account gestiti in Azure Active Directory.

Istruzioni dettagliate per la sincronizzazione agli utenti locali di Azure Active Directory è coperto integrazione [le identità locali con Azure Active Directory](active-directory-aadconnect.md).


Per creare e gestire gli utenti in Azure Active Directory manualmente, fare riferimento alla [gestione degli utenti in Azure Active Directory](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## <a name="set-up-device-registration"></a>Configurare la registrazione periferica
1. Accedere al portale di Azure come amministratore.
2. Nel riquadro sinistro selezionare **Active Directory**.
3. Nella scheda **Directory** selezionare directory.
4. Selezionare la scheda **Configura** .
5. Passare alla sezione **dispositivi** .
6. Nella scheda **dispositivi** , impostare le operazioni seguenti:  
   * **Massimo numero di dispositivi PER utente**: selezionare il numero massimo di dispositivi che un utente può avere in Azure Active Directory.  Se un utente raggiunge la quota, vengano non sarà possibile aggiungere altri dispositivi fino a quando non vengono rimossi uno o più dispositivi esistenti.
   * **RICHIEDI AUTH a più fattori per dispositivi di JOIN**: specificare se gli utenti devono specificare un secondo fattore di autenticazione per unire il dispositivo di Azure Active Directory. Per ulteriori informazioni sull'autenticazione a più fattori Azure, vedere [Guida introduttiva a autenticazione a più fattori Azure nel cloud](..\multi-factor-authentication\multi-factor-authentication-get-started-cloud.md).
   * **Gli utenti possono AZURE Active Directory JOIN dispositivi**: selezionare gli utenti e gruppi che possono partecipare a dispositivi per Azure Active Directory.
   * **Altri amministratori via AZURE Active Directory AGGIUNGONO dispositivi**: con Azure Active Directory Premium o Enterprise mobilità famiglia di prodotti (EMS), è possibile scegliere gli utenti che dispongono di diritti di amministratore locale nel dispositivo. Per impostazione predefinita, gli amministratori globali e i proprietari dei dispositivi sono concesso diritti di amministratore locale.

<center>![Configurare apposito dispositivo](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png)</center>

Dopo aver configurato partecipare di Azure Active Directory per gli utenti, possono essere connessi a Azure Active Directory tramite dispositivi aziendali o personali.

Di seguito sono i tre scenari che è possibile utilizzare per consentire agli utenti di configurare Azure Active Directory Join:

- Gli utenti di aggiungere un dispositivo appartenenti alla società direttamente a Azure Active Directory.
- Un dispositivo della società di proprietà per il servizio Active Directory locale connessione al dominio utenti e quindi estendere il dispositivo di Azure Active Directory.
- Gli utenti di aggiungere gli account aziendale o dell'istituto di istruzione di Windows in un dispositivo personale

## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
