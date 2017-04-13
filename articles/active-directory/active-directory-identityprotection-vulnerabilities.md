<properties
    pageTitle="Vulnerabilità rilevata da Azure Active Directory identità protezione | Microsoft Azure"
    description="Panoramica dei problemi rilevati da Azure Active Directory identità protezione."
    services="active-directory"
    keywords="protezione dell'identità di Azure active directory, individuazione app cloud, la gestione applicazioni, sicurezza, rischio, il livello di rischio, vulnerabilità, criteri di sicurezza"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilità rilevata da Azure Active Directory identità protezione 

Le vulnerabilità sono punti di debolezza, nell'ambiente che può essere utilizzato da un utente non autorizzato. È consigliabile indirizzo queste vulnerabilità per migliorare le condizioni di protezione dell'organizzazione e impedire che utenti malintenzionati sfruttare loro.
<br><br>
![vulnerabilità](./media/active-directory-identityprotection-vulnerabilities/101.png "vulnerabilities")
<br>

Nelle sezioni seguenti offrono una panoramica di vulnerabilità segnalato per la protezione dell'identità.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registrazione di autenticazione a più fattori non configurata 

Questa vulnerabilità consente di controllare la distribuzione di autenticazione a più fattori Azure all'interno dell'organizzazione. 

Autenticazione a più fattori Azure fornisce un secondo livello di sicurezza all'autenticazione dell'utente. Consente di proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Offre autenticazione tramite un intervallo delle opzioni di verifica semplice, ovvero telefonata, messaggio di testo o app per dispositivi mobili notifica o verifica codice e 3o festa GIURAMENTO token.

È consigliabile richiedono l'autenticazione a più fattori Azure per gli accessi utente. Autenticazione a più fattori svolge un ruolo fondamentale nei criteri di accesso condizionale basati sui rischi disponibili tramite la protezione dell'identità.

Per ulteriori informazioni, vedere [che cos'è l'autenticazione a più fattori Azure?](../multi-factor-authentication/multi-factor-authentication.md)


## <a name="unmanaged-cloud-apps"></a>Applicazioni basate su cloud non gestite

Questa vulnerabilità consente di identificare App cloud non gestito all'interno dell'organizzazione.
 
In una versione recente aziende, i reparti IT spesso di rilevano tutte le applicazioni cloud che utilizzano utenti dell'organizzazione di svolgere il proprio lavoro. È facile vedere perché gli amministratori avranno problemi di accesso non autorizzato ai dati aziendali, perdita di dati possibili o ad altri rischi. 

È consigliabile che l'organizzazione distribuisce Cloud App individuazione per alcune applicazioni cloud non gestito e gestire queste applicazioni con Azure Active Directory.

Per ulteriori informazioni, vedere [ricerca di applicazioni cloud non gestito con individuazione App Cloud](active-directory-cloudappdiscovery-whatis.md).



##<a name="security-alerts-from-privileged-identity-management"></a>Avvisi di sicurezza di gestione delle identità privilegi

Questa vulnerabilità consente di individuare e risolvere gli avvisi sui privilegi identità dell'organizzazione.  

Per consentire agli utenti di eseguire operazioni con privilegi, le organizzazioni devono concedere l'accesso privilegi agli utenti temporaneo o permanente in Azure Active Directory, risorse Azure o Office 365 o altre App SaaS. Ciascuno di questi utenti privilegi aumenta la possibilità di attacchi della propria organizzazione. Questa vulnerabilità consente di identificare gli utenti con privilegi di accesso necessari ed eseguire l'azione appropriata per ridurre o eliminare il rischio che comportano. 

È consigliabile che l'organizzazione Usa Azure Active Directory privilegi di gestione delle identità per gestire, il controllo, e monitor con privilegi identità e accesso alle risorse in Azure Active Directory, nonché altri servizi online di Microsoft Office 365 o Microsoft Intune.

Per ulteriori informazioni, vedere [gestione delle identità privilegi di Azure Active Directory](active-directory-privileged-identity-management-configure.md). 



## <a name="see-also"></a>Vedere anche

 - [Protezione dell'identità di Azure Active Directory](active-directory-identityprotection.md)
