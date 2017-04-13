<properties
    pageTitle="# Configurare la registrazione di dispositivo automatico per i dispositivi di dominio di Windows 7 | Microsoft Azure"
    description="Procedura per configurare il dominio di Windows 7 il join di dispositivi per registrare automaticamente con Azure Active Directory. e i passaggi per distribuire il pacchetto di software di registrazione di dispositivo per il dominio di Windows 7 aggiungono dispositivi usando un sistema di distribuzione di software, ad esempio System Center Configuration Manager."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurare la registrazione di dispositivo automatico per i dispositivi di dominio di Windows 7

Gli amministratori IT, è possibile configurare i dispositivi di dominio di Windows 7 per registrare automaticamente con Azure Active Directory. A tale scopo, è necessario distribuire il pacchetto di software di registrazione di dispositivo per il dominio di Windows 7 il join di dispositivi usando un sistema di distribuzione di software, ad esempio System Center Configuration Manager. Assicurarsi di leggere e completare i prerequisiti elencati nella registrazione dispositivo automatica con i dispositivi di Azure Active Directory per Windows dominio aggiunto.

>[AZURE.NOTE]
 Per vedere più recente istruzioni su come configurare la registrazione di dispositivo automatico, [come configurare la registrazione automatica del dominio di Windows aggiungono dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Installazione del pacchetto di software di registrazione di dispositivo in Windows 7 dominio dispositivi

Registrazione di dispositivo per Windows 7 è disponibile come [pacchetto MSI scaricabile](https://connect.microsoft.com/site1164). Il pacchetto deve essere installato nel computer Windows 7 che fanno parte di un dominio Active Directory. Distribuire il pacchetto utilizzando un sistema di distribuzione di software, ad esempio System Center Configuration Manager. Il pacchetto MSI supporta le opzioni di installazione interattiva standard usando la /quiet parametro.
Il pacchetto di software è disponibile per il [sito Web Microsoft Connect](https://connect.microsoft.com/site1164). Qui è possibile selezionare e quindi scaricare luogo di lavoro partecipare per Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Partecipa alla rete aziendale con Azure Active Directory
Registrazione di dispositivo per i dispositivi di dominio di Windows 7 non richiede o includere un'interfaccia utente. Dopo aver installato nel computer, qualsiasi utente che accede alla macchina verrà automaticamente e automaticamente registrato con un oggetto di dispositivo in Azure Active Directory. Si verificherà un oggetto di dispositivo in Azure Active Directory per ogni utente registrato del dispositivo fisico.

Il programma di installazione crea un'attività programmata viene eseguito nel contesto dell'utente che è attivata per l'accesso degli utenti. L'attività consente di registrare automaticamente l'utente e dispositivi con Azure Active Directory dopo segni aggiuntivo l'utente sono stato completo.
Attività programmata sono disponibili nella libreria utilità di pianificazione in **Microsoft** > **Partecipare luogo di lavoro**.
L'operazione verrà eseguita e registrare tutte le agli utenti di Active Directory che accessohttp al computer.
La figura seguente sono elencati la procedura guidata per la registrazione di dispositivo automatico.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Un utente (lavoro informazioni) accede a un computer client di Windows 7 con le credenziali di dominio Active Directory.
1. L'attività pianificata partecipare luogo di lavoro viene eseguito.
1. L'utente viene autenticato automaticamente con ADFS usando l'autenticazione di Windows.
1. PC Windows 7 è registrato all'utente in Active Directory Azure.
1. Un certificato e un oggetto di dispositivo viene creata in Azure Active Directory. L'oggetto rappresenta il user@device.
1. Il certificato di partecipare a luogo di lavoro archiviato nel computer.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Annullamento della registrazione del dominio di Windows 7 eseguito il join di dispositivi

È possibile annullare la registrazione dei dispositivi Windows 7 dominio eseguendo le operazioni seguenti: disinstallare il luogo di lavoro Join pacchetto software dal proprio dominio di Windows 7 aggiungono dispositivi usando un sistema di distribuzione di software, ad esempio System Center Configuration Manager.

Aprire un prompt dei comandi sul computer Windows 7, quindi eseguire il seguente comando per annullare la registrazione il dispositivo:

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Questo comando deve essere eseguito nel contesto di ogni utente del dominio che ha effettuato l'accesso al computer.
Dispositivi di dominio in Visualizzatore eventi e gli errori per Windows 7.

Registro eventi di Windows nel computer Windows 7 verranno visualizzati i messaggi correlati a partecipare luogo di lavoro. È possibile trovare i messaggi per gli eventi riusciti e non partecipare luogo di lavoro. Il registro eventi possono essere disponibili in Visualizzatore eventi in registri applicazioni e servizi > area di lavoro Microsoft partecipare.

## <a name="additional-topics"></a>Argomenti aggiuntivi

- [Panoramica di Azure Active Directory dispositivo registrazione](active-directory-conditional-access-device-registration-overview.md)
- [Registrazione automatica dispositivo con i dispositivi Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurare la registrazione di dispositivo automatico per i dispositivi di dominio Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registrazione automatica dispositivo con i dispositivi di dominio di Azure Active Directory per Windows 10](active-directory-azureadjoin-devices-group-policy.md)
