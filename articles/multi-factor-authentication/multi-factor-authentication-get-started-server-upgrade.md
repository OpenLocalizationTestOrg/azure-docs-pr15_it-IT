<properties 
    pageTitle="Aggiornamento dell'agente PhoneFactor al Server Azure autenticazione a più fattori"
    description="In questo documento viene descritto come iniziare a utilizzare Azure MFA Server e come eseguire l'aggiornamento da agente di phonefactor meno recente."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Aggiornamento dell'agente PhoneFactor al Server Azure autenticazione a più fattori

L'aggiornamento da PhoneFactor agente V5. x o versioni precedenti al Server di autenticazione a più fattori Azure richiede l'agente di PhoneFactor e collegati componenti da disinstallare prima che il Server di autenticazione a più fattori e dei relativi componenti collegate possono essere installati.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Per aggiornare l'agente PhoneFactor al Server di autenticazione a più fattori Azure
<ol>
<li>Per eseguire il backup del file di dati PhoneFactor. Percorso di installazione predefinito è c:\Programmi\Microsoft Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Se è installato il portale utente:</li>
<ol>
<li>Passare alla cartella di installazione e drill-up config. Percorso di installazione predefinito è C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se sono stati aggiunti temi personalizzati al portale, eseguire il backup la cartella personalizzata di sotto della directory C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Disinstallare User Portal tramite l'agente di PhoneFactor (disponibile solo se è installato nello stesso server agente PhoneFactor) o Windows programmi e funzionalità.</li></ol>




<li>Se è installato il servizio Web App Mobile:
<ol>
<li>Passare alla cartella di installazione e drill-up config. Percorso di installazione predefinito è C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Disinstallare il servizio Web App per dispositivi mobili tramite Windows programmi e funzionalità.</li></ol>

<li>Se è installato il servizio Web SDK, disinstallarlo tramite l'agente PhoneFactor o Windows programmi e funzionalità.

<li>Disinstallare l'agente PhoneFactor tramite Windows programmi e funzionalità.

<li>Installare il Server di autenticazione a più fattori. Si noti che il percorso di installazione è ritirare il Registro di sistema di installazione PhoneFactor agente precedente, in modo che dovrebbe essere installato nello stesso percorso (ad esempio c:\Programmi\Microsoft Files\PhoneFactor). Le nuove installazioni avrà una predefinita diversa installare percorso (ad esempio, c:\Programmi Files\Multi fattore autenticazione Server). Il file di dati a sinistra per l'agente PhoneFactor precedente deve essere aggiornato durante l'installazione, in modo che gli utenti e impostazioni dovrebbero essere ancora presente dopo l'installazione di nuovo Server di autenticazione a più fattori.

<li>Se richiesto, attivare il Server di autenticazione a più fattori e assicurarsi che sia assegnata al gruppo di replica corretto.

<li>Se in precedenza è stato installato il servizio Web SDK, installare il nuovo SDK servizio Web tramite l'interfaccia utente del Server di autenticazione a più fattori. Si noti che il nome di directory virtuale predefinito è "MultiFactorAuthWebServiceSdk" anziché "PhoneFactorWebServiceSdk". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, se si consente l'installazione di utilizzare il nuovo nome predefinito, sarà necessario modificare l'URL in tutte le applicazioni che fanno riferimento a Web Service SDK ad esempio l'utente portale e al servizio Web App Mobile in modo che puntino nella posizione corretta.

<li>Se il portale di utente in precedenza è stato installato nel Server agente PhoneFactor, installare il nuovo portale utente di autenticazione a più fattori tramite l'interfaccia utente del Server di autenticazione a più fattori. Si noti che il nome di directory virtuale predefinito è "MultiFactorAuth" anziché "PhoneFactor". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, se si consente l'installazione di utilizzare il nuovo nome predefinito, è necessario fare clic sull'icona User Portal nel Server di autenticazione a più fattori e aggiornare l'URL del portale di utente nella scheda Impostazioni.

<li>Se il portale di utente e/o servizio Web App Mobile in precedenza è stato installato in un server diverso da agente di PhoneFactor:
<ol>
<li>Passare al percorso di installazione (ad esempio c:\Programmi\Microsoft Files\PhoneFactor) e copiare installer(s) appropriato a altro server. Sono disponibili i programmi di installazione a 32 bit e 64 bit per il portale di utente e il servizio Web App Mobile. Sono chiamati MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi rispettivamente.</li>
<li>Per installare il portale utente sul server web, aprire un prompt dei comandi come amministratore ed eseguire il MultiFactorAuthenticationUserPortalSetupXX.msi. Si noti che il nome di directory virtuale predefinito è "MultiFactorAuth" anziché "PhoneFactor". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. In caso contrario, se si consente l'installazione di utilizzare il nuovo nome predefinito, è necessario fare clic sull'icona User Portal nel Server di autenticazione a più fattori e aggiornare l'URL del portale di utente nella scheda Impostazioni. Saranno necessario agli utenti di comunicare il nuovo URL.</li>
<li>Accedere al portale di utente installare percorso (ad esempio C:\inetpub\wwwroot\MultiFactorAuth) e modificare il file config. Copiare i valori nelle sezioni appSettings e applicationSettings presenti nel file config originale che è stato eseguito il backup prima dell'aggiornamento nel nuovo file config. Se il nuovo nome directory virtuale predefinita è stato mantenuto durante l'installazione di SDK di servizio Web, modificare l'URL nella sezione applicationSettings in modo che puntino nella posizione appropriata. Se tutti gli altri valori predefiniti sono stati modificati in config precedente, applicare le stesse modifiche al nuovo file config.</li>
<li>Per installare il servizio Web App Mobile su un server web, aprire un prompt dei comandi come amministratore ed eseguire il MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Si noti che il nome di directory virtuale predefinito è "MultiFactorAuthMobileAppWebService" anziché "PhoneFactorPhoneAppWebService". Se si desidera utilizzare il nome precedente, è necessario modificare il nome della directory virtuale durante l'installazione. È consigliabile scegliere un nome più breve per rendere più semplice per gli utenti finali al testo su dispositivi mobili. In caso contrario, se si consente l'installazione di utilizzare il nuovo nome predefinito, è necessario fare clic sull'icona dell'App Mobile nel Server di autenticazione a più fattori e aggiornare l'URL del servizio Web App Mobile.</li>
<li>Accedere al servizio Web App Mobile installare percorso (ad esempio C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e modificare il file config. Copiare i valori nelle sezioni appSettings e applicationSettings presenti nel file config originale che è stato eseguito il backup prima dell'aggiornamento nel nuovo file config. Se il nuovo nome directory virtuale predefinita è stato mantenuto durante l'installazione di SDK di servizio Web, modificare l'URL nella sezione applicationSettings in modo che puntino nella posizione appropriata. Se tutti gli altri valori predefiniti sono stati modificati in config precedente, applicare le stesse modifiche al nuovo file config.</li></ol>
