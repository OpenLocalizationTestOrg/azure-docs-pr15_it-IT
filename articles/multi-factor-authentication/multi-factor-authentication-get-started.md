<properties
    pageTitle="Server di Azure MFA cloud vs | Microsoft Azure"
    description="Scegliere la soluzione secutiry autenticazione a più fattori destra si chiedendo quali am i tentando di proteggere e dove si trovano gli utenti che si trova.  Scegliere quindi cloud MFA Server o ADFS."
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
    ms.date="10/14/2016"
    ms.author="kgremban"/>

#<a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Scegliere la soluzione di autenticazione a più fattori Azure dell'utente

Perché sono disponibili diversi tipi di autenticazione a più fattori Azure (MFA), è necessario rispondere ad alcune domande per stabilire quale versione è quella appropriata da utilizzare.  Queste domande sono:

-   [Che si sta cercando di protezione](#what-am-i-trying-to-secure)
-   [Dove si trovano gli utenti](#where-are-the-users-located)
- [Quali funzionalità hanno bisogno?](#what-featured-do-i-need)

Le sezioni seguenti includono indicazioni su come determinare ognuna di queste risposte.

## <a name="what-am-i-trying-to-secure"></a>Che si sta cercando di protezione

Per determinare la soluzione di verifica in due passaggi corretto, prima di tutto è necessario rispondere alla domanda di operazioni da si sta tentando di proteggere con un secondo metodo di autenticazione.  Si tratta di un'applicazione Azure?  O un sistema di accesso remoto?  Per determinare ciò che si sta tentando di proteggere, in grado di rispondere alla domanda nel punto in cui deve essere abilitata l'autenticazione a più fattori.  


Cosa si sta tentando di proteggere| Autenticazione a più fattori nel cloud|Server di autenticazione a più fattori
------------- | :-------------: | :-------------: |
App di terze parti prima Microsoft|● |● |
App SaaS nella raccolta app|● |● |
Applicazioni di IIS pubblicate tramite Azure Active Directory App Proxy|● |● |
Applicazioni di IIS non pubblicate tramite Azure Active Directory App Proxy | |● |
Accesso remoto, ad esempio VPN, RDG| |● |



## <a name="where-are-the-users-located"></a>Dove si trovano gli utenti

Successivamente, esamina in cui si trovano gli utenti consente di determinare la soluzione corretta da utilizzare, nel cloud o locale utilizzando il Server MFA.



Percorso utente| Autenticazione a più fattori nel cloud|Server di autenticazione a più fattori
------------- | :-------------: | :-------------: |
Azure Active Directory|● | |
Azure Active Directory e Active Directory utilizzando la federazione con ADFS in locale|● |● |
Azure Active Directory e Active Directory utilizzando DirSync, sincronizzazione di Azure Active Directory, Azure AD Connect - senza sincronizzazione delle password locale|● |● |
Azure Active Directory e Active Directory utilizzando DirSync, sincronizzazione di Azure Active Directory, Azure AD Connect - con la sincronizzazione delle password locale|● | |
Active Directory in locale| |● |

## <a name="what-features-do-i-need"></a>Quali funzionalità hanno bisogno?

Nella tabella seguente è un confronto delle caratteristiche disponibili con l'autenticazione a più fattori nel cloud e con il Server di autenticazione a più fattori.

 | Autenticazione a più fattori nel cloud | Server di autenticazione a più fattori
------------- | :-------------: | :-------------: |
Notifica di app per dispositivi mobili come un fattore secondo | ● | ● |
Codice di verifica app per dispositivi mobili come un fattore secondo | ● | ●
Telefonata come secondo fattore | ● | ●
SMS unidirezionale come secondo fattore | ● | ●
SMS bidirezionale come secondo fattore |  | ●
Token hardware come secondo fattore |  | ●
Password dell'App per i client che non supportano MFA | ● |  
Controllo amministrazione i metodi di autenticazione | ● | ●
Modalità PIN |  | ●
Avviso di frode | ● | ●
Report MFA | ● | ●
Ignora occasionale |  | ●
Messaggio di saluto personalizzati per le chiamate telefoniche | ● | ●
ID chiamante personalizzabile per le chiamate telefoniche | ● | ●
IP di attendibile | ● | ●
Ricordare MFA per i dispositivi attendibili  | ● |  
Accesso condizionato | ● | ●
Cache |  | ●

Ora che è stato rilevato se si desidera utilizzare l'autenticazione a più fattori cloud o la MFA il Server in locale, è possibile iniziare configurazione e mediante l'autenticazione a più fattori Azure. **Selezionare l'icona che rappresenta lo scenario!**

<center>




[![Cloud](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Proofup](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>
