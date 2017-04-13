<properties
    pageTitle="Configurare un dispositivo con Azure Active Directory durante la configurazione del nuovo | Microsoft Azure"
    description="Un argomento che illustra come gli utenti possono configurare partecipare di Azure Active Directory durante la prima esperienza Esegui."
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

# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>Configurare un nuovo dispositivo con Azure Active Directory durante l'installazione

In Windows 10, gli utenti possono partecipare i dispositivi di Azure Active Directory (Azure Active Directory) nella prima esecuzione (FRX). Questa sezione consente alle organizzazioni di distribuire i dispositivi per i dipendenti o gli studenti o consentire loro di scegliere i propri dispositivi (CYOD).
Se le edizioni di Windows 10 Professional o Windows 10 Enterprise è installato in un dispositivo, l'esperienza di valori predefiniti per il processo di configurazione per i dispositivi appartenenti alla società.

## <a name="to-join-a-device-to-azure-ad"></a>Per partecipare a un dispositivo per Azure Active Directory


1. Quando si attiva nuovi dispositivi e avvia il processo di configurazione, verrà visualizzato il messaggio **Operazioni preliminari** . Seguire le istruzioni visualizzate per configurare il dispositivo.
2. Iniziare a personalizzare il paese e lingua. Quindi accetta le condizioni di licenza Software Microsoft.
![Personalizzare per il proprio paese](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. Selezionare la rete che si desidera utilizzare per la connessione a Internet.
4. Selezionare se si usa un dispositivo personale o un dispositivo appartenenti alla società. Se si tratta della società di proprietà, fare clic su **questo dispositivo appartiene all'organizzazione**. Verrà avviata l'esperienza di Azure Active Directory partecipare. Di seguito è riportato uno schermo che verrà visualizzato se si usa Windows 10 Professional.
<center>
![A chi appartengono questo schermo del PC](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)

5.  Immettere le credenziali sono state fornite dall'organizzazione.
<center>
![Schermata di accesso](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6.  Dopo aver immesso il nome utente, è disponibile un tenant corrispondente in Azure Active Directory. Se trovano in un dominio federato, verrà reindirizzato al server locale servizio Token (sicurezza), ad esempio, Active Directory Federation Services (ADFS).
7. Se si è un utente in un dominio non federato, immettere le credenziali direttamente nella pagina di Azure Active Directory ospitato. Se è stata configurata marchio della società, verrà anche visualizzato il logo dell'organizzazione e il supporto di testo.
8.  Viene richiesto per una richiesta di autenticazione a più fattori. Questo problema può essere configurato da un amministratore IT.
9.  Azure Active Directory viene verificato se questo utente/dispositivo richiede la registrazione in Gestione di dispositivi mobili.
10. Windows registra il dispositivo nella directory dell'organizzazione in Azure Active Directory e registra nella gestione di dispositivi mobili, se appropriato.
11. Se si è un utente gestito, Windows verrà visualizzata sul desktop tramite la procedura di accesso automatico.
12. Se si è un utente federato, viene richiesto di schermata di accesso di Windows per immettere le credenziali.

> [AZURE.NOTE] Non è possibile partecipare a un dominio di Windows Server Active Directory locale all'esperienza di fuori della casella di Windows. Pertanto, se si prevede di partecipare a un computer a un dominio, è necessario selezionare il collegamento **configurare Windows con un account locale** . Per partecipare dominio dalle impostazioni nel computer in uso quando si esegue questa operazione prima.

## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password tramite Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
