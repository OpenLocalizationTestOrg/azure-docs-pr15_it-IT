<properties
    pageTitle="Impostazione di Azure Active Directory per la gestione di accesso applicazioni self-service | Microsoft Azure"
    description="Gestione dei gruppi modalità self-service consente agli utenti di creare e gestire gruppi di sicurezza o i gruppi di Office 365 in Azure Active Directory e offre agli utenti la possibilità di gruppo di sicurezza richiesta o appartenenze ai gruppi di Office 365"
    services="active-directory"
    documentationCenter=""
  authors="curtand"
    manager="femila"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/10/2016"
    ms.author="curtand"/>

# <a name="setting-up-azure-active-directory-for-self-service-group-management"></a>La configurazione di Azure Active Directory per la gestione del gruppo modalità self-service

Gestione dei gruppi modalità self-service consente agli utenti di creare e gestire gruppi di sicurezza o i gruppi di Office 365 di Azure Active Directory (Azure Active Directory). Gli utenti possono inoltre richiedere appartenenze ai gruppi di Office 365 o gruppo di sicurezza e quindi il proprietario del gruppo può approvare o respingere l'appartenenza. In questo modo, il controllo quotidiano dell'appartenenza al gruppo può essere delegato agli utenti che conoscono il contesto di business per l'appartenenza. Funzionalità di gestione di gruppo modalità self-service sono disponibili solo per i gruppi di sicurezza e gruppi di Office 365, ma non per i gruppi di sicurezza abilitati alla posta elettronica o liste di distribuzione.

Gestione dei gruppi modalità self-service attualmente comprende due scenari essenziali: delega di gestione dei gruppi modalità self-service e gestione dei gruppi.

- **Delegato la gestione del gruppo** 
   un esempio è un amministratore che gestisce l'accesso a un'applicazione di SaaS che utilizza la società. Gestione di questi diritti di accesso è sempre complessa, in modo che l'amministratore viene richiesto il proprietario di business per creare un nuovo gruppo. L'amministratore assegna l'accesso per l'applicazione al nuovo gruppo e aggiunge al gruppo di tutte le persone già accedendo all'applicazione. Il proprietario di business quindi possibile aggiungere più utenti e vengono automaticamente effettuato il provisioning di tali utenti all'applicazione. Il proprietario di business non è necessario attendere che l'amministratore gestire l'accesso per gli utenti. Se l'amministratore concede la stessa autorizzazione a un responsabile di un gruppo di business diverso, tale persona anche gestire l'accesso per le proprie utenti. Il proprietario di business né gestore può visualizzare o gestire di altri utenti. L'amministratore può comunque a vedere tutti gli utenti che hanno accesso all'applicazione e diritti di accesso blocco se necessario.

- **Gestione dei gruppi modalità self-service** 
   un esempio di questo scenario è due utenti che dispongono di siti di SharePoint Online che vengono impostati in modo indipendente. Si desidera l'accesso di altri team ai propri siti. A tale scopo, è possibile creare un gruppo in Azure Active Directory e in SharePoint Online ciascuno di essi viene selezionato il gruppo per consentire l'accesso a siti. Quando un utente richiede l'accesso, richiedono dal Pannello di accesso e dopo l'approvazione ricevono automaticamente l'accesso a entrambi i siti SharePoint Online. In un secondo momento, uno di essi decide che tutti gli utenti di accedere al sito anche dovrebbero ottenere accesso a una determinata applicazione SaaS. L'amministratore dell'applicazione SaaS può aggiungere diritti di accesso per l'applicazione al sito di SharePoint Online. In poi, tutte le richieste di ottenere approvazione consente di accedere a due siti di SharePoint Online e di eseguire questa applicazione SaaS.

## <a name="making-a-group-available-for-end-user-self-service"></a>Disponibilità di un gruppo per utente finale modalità self-service

1. Nel [portale classica Azure](https://manage.windowsazure.com), aprire la directory di Azure Active Directory.

2. Nella scheda **Configura** impostare **Gestione gruppo delegata** su attivato.

3. Impostare **gli utenti possono creare gruppi di sicurezza** o **gli utenti possono creare gruppi di Office** su attivato.

Quando **che gli utenti possono creare gruppi di sicurezza** è attivata, tutti gli utenti nella directory sono autorizzati a creare nuovi gruppi di sicurezza e aggiungere membri a questi gruppi. Questi nuovi gruppi verranno inoltre visualizzati nel Pannello di accesso per tutti gli altri utenti. Se l'impostazione del criterio nel gruppo lo consente, altri utenti possono creare richieste di partecipazione a questi gruppi. Se **gli utenti possono creare gruppi di sicurezza** è disattivata, gli utenti non è possibile creare gruppi e non è possibile modificare gruppi esistenti di cui sono proprietario. Tuttavia, possono comunque gestire le appartenenze dei gruppi e approvare le richieste da altri utenti a partecipare i gruppi.

È anche possibile utilizzare **gli utenti utilizzano Self-Service per i gruppi di sicurezza** per ottenere un controllo di accesso più diffusamente sulla gestione di gruppo modalità self-service per gli utenti. Quando **che gli utenti possono creare gruppi** è attivata, tutti gli utenti nella directory sono consentiti per creare nuovi gruppi e aggiungere membri a questi gruppi. Impostando anche **gli utenti utilizzano Self-Service per i gruppi di sicurezza** ad alcune, si limita gestione a un gruppo ristretto di utenti di gruppo. Quando questa opzione è impostata su alcune, è necessario aggiungere utenti al gruppo SSGMSecurityGroupsUsers prima di poter creare nuovi gruppi e aggiungere membri. Mediante l'impostazione a tutti **gli utenti utilizzano Self-Service per i gruppi di sicurezza** , si attiva tutti gli utenti nella directory per creare nuovi gruppi.

È anche possibile utilizzare la casella di **gruppo che può utilizzare Self-Service per i gruppi di sicurezza** per specificare un nome personalizzato per un gruppo di cui i membri possono utilizzare Self-Service.

## <a name="additional-information"></a>Ulteriori informazioni

Gli articoli seguenti forniscono informazioni aggiuntive su Azure Active Directory.

* [Gestione dell'accesso alle risorse dei gruppi di Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory cmdlet per la configurazione delle impostazioni dei gruppi](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Indice articolo per la gestione applicazioni in Azure Active Directory.](active-directory-apps-index.md)

* [Che cos'è Azure Active Directory?](active-directory-whatis.md)

* [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
