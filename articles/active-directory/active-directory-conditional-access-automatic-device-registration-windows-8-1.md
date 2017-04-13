<properties
    pageTitle="Configurare la registrazione di dispositivo automatico per i dispositivi di dominio Windows 8.1 | Microsoft Azure"
    description=" Procedura per configurare i criteri di gruppo per i dispositivi Windows 8.1 dominio registrare automaticamente con Azure Active Directory. "
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
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurare la registrazione di dispositivo automatico per i dispositivi di dominio Windows 8.1

È possibile utilizzare un criteri di gruppo di Active Directory per configurare i dispositivi di dominio Windows 8.1 per registrare automaticamente con Azure Active Directory. Per configurare i criteri di gruppo, è necessario disporre almeno un dominio uniti Windows Server 2012 R2 o Windows 8.1 computer con la funzionalità di gestione dei criteri di gruppo installata. Una volta criteri di gruppo è abilitato per il dominio, qualsiasi utente che accede alla macchina verrà automaticamente e automaticamente registrato con un oggetto di dispositivo in Azure Active Directory. Si verificherà un oggetto di dispositivo in Azure Active Directory per ogni utente registrato del dispositivo fisico. Assicurarsi di leggere e completare i prerequisiti dalla registrazione dispositivo automatica con i dispositivi Azure Active Directory for Windows Domain-Joined.

>[AZURE.NOTE]
 Per vedere più recente istruzioni su come configurare la registrazione di dispositivo automatico, [come configurare la registrazione automatica del dominio di Windows aggiungono dispositivi con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurare i criteri di gruppo per i dispositivi di dominio Windows 8.1

1. Aprire Gestione Server e accedere a **Strumenti** > **Gestione criteri di gruppo**.
2. Da Gestione di criteri di gruppo, esplorare il nodo del dominio che corrisponde al dominio in cui si desidera attiva **Join automatico luogo di lavoro**.
3. Pulsante destro del mouse **Dei criteri di gruppo** e selezionare **Nuovo**. Assegnare un nome, ad esempio un **Join automatici luogo di lavoro**l'oggetto Criteri di gruppo. Fare clic su **OK**.
4. Pulsante destro del mouse sull'oggetto Criteri di gruppo nuovo e quindi selezionare **Modifica**.
5. Passare a **Configurazione Computer** > **criteri** > **modelli amministrativi** > **componenti di Windows** > **partecipare luogo di lavoro**.
6. Pulsante destro del mouse automaticamente i computer client partecipa alla rete aziendale e quindi selezionare **Modifica**.
7. Selezionare il pulsante di opzione abilitato e quindi fare clic su Applica. Fare clic su **OK**.
8. Oggetto Criteri di gruppo possono ora collegarsi a un percorso che preferisci. Per abilitare questa impostazione per tutti i dispositivi Windows 8.1 dominio nell'organizzazione, collegare i criteri di gruppo al dominio.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Annullamento della registrazione del dominio di Windows 8.1 eseguito il join di dispositivi

È possibile annullare la registrazione dei dispositivi Windows 8.1 dominio eseguendo le operazioni seguenti: modificare le impostazioni di criteri di gruppo partecipare luogo di lavoro create nella sezione precedente. Impostare l'automaticamente luogo di lavoro join computer i criteri client su disattivato. Ciò impedirà nuovi dispositivi di aggiungersi automaticamente sul luogo di lavoro.

Annullare la registrazione i computer Windows 8.1 dominio esistenti seguendo una delle due opzioni seguenti:

* Opzione 1: **annullamento della registrazione Windows 8.1 dominio dispositivo che utilizza impostazioni PC**
  1. Sul dispositivo Windows 8.1, passare a **Impostazioni PC** > **rete** > **luogo di lavoro**
  2. Selezionare **abbandona**.
Questa procedura deve essere ripetuta per ogni utente del dominio che ha effettuato l'accesso al computer ed è stato automaticamente luogo di lavoro eseguito il join.

* Opzione 2: Annullare la registrazione di un dispositivo collegato tramite join dominio di Windows 8.1 con uno script
    1. Aprire un prompt dei comandi sul computer Windows 8.1 ed eseguire il comando seguente:` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Questo comando deve essere eseguito nel contesto di ogni utente del dominio che ha effettuato l'accesso al computer.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Visualizzatore eventi e gli errori per Windows 8.1 dispositivi di dominio

Registro eventi di Windows in un computer Windows 8.1 vengono visualizzati messaggi relativi alla registrazione di dispositivo. Sono disponibili i messaggi per gli eventi riusciti e non. 

Il registro eventi possono essere disponibili in Visualizzatore eventi in applicazioni e servizi **registri** > **Microsoft** > **Windows > area di lavoro partecipare**.

##<a name="additional-details"></a>Ulteriori informazioni

I criteri di gruppo consente di un'attività programmata del computer che esegue nel contesto dell'utente e attivata per accesso degli utenti. L'attività di registrazione viene eseguita automaticamente l'utente e un dispositivo con Azure Active Directory dopo l'accesso è stata completata. Attività programmata sono disponibili in dispositivi Windows 8.1 libreria utilità di pianificazione in **Microsoft** > **Windows** > **Partecipare luogo di lavoro**. L'attività verrà eseguito e registrare tutte le agli utenti di Active Directory che accesso-in. 

## <a name="additional-topics"></a>Argomenti aggiuntivi
- [Panoramica di Azure Active Directory dispositivo registrazione](active-directory-conditional-access-device-registration-overview.md)
- [Registrazione automatica dispositivo con i dispositivi di dominio di Azure Active Directory per Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurare la registrazione di dispositivo automatico per i dispositivi di dominio di Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

