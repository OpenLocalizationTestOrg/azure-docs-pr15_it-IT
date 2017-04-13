<properties
    pageTitle="ADFS Azure Active Directory e MFA | Microsoft Azure"
    description="Questa è la pagina di autenticazione a più fattori Azure che descrive come iniziare a utilizzare MFA Azure e ADFS."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na" ms.topic="get-started-article"
    ms.date="10/17/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-azure-multi-factor-authentication-and-active-directory-federation-services"></a>Guida introduttiva di autenticazione a più fattori Azure e Active Directory Federation Services



<center>![Cloud](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Se l'organizzazione ha federati di Active Directory locali con Azure Active Directory tramite ADFS, sono disponibili due opzioni per l'uso di autenticazione a più fattori Azure.

- Proteggere le risorse cloud usando l'autenticazione a più fattori Azure o Active Directory Federation Services
- Proteggere le risorse di cloud e locali con Azure Server di autenticazione a più fattori

La tabella seguente riepiloga l'esperienza di verifica tra protezione di risorse con autenticazione a più fattori Azure e ADFS

|Esperienza di verifica - applicazioni basate su Sfoglia|Esperienza di verifica - App Non basati su Browser
:------------- | :------------- | :------------- |
Protezione delle risorse di Azure Active Directory mediante l'autenticazione a più fattori di Azure |<li>Il primo passaggio di verifica viene eseguito tramite ADFS in locale.</li> <li>Il secondo passaggio è un metodo telefonica effettuato mediante l'autenticazione di cloud.</li>|Gli utenti finali possono utilizzare app password effettuare l'accesso.
Protezione di Azure Active Directory risorse con Active Directory Federation Services |<li>Il primo passaggio di verifica viene eseguito tramite ADFS in locale.</li><li>Il secondo passaggio viene eseguito in locale per rispettare la richiesta.</li>|Gli utenti finali possono utilizzare app password effettuare l'accesso.

Avvertenze con password di app per gli utenti federati:

- Password dell'App vengono verificati mediante l'autenticazione di cloud, in modo che evitano la federazione. Federazione viene utilizzata in modo attivo solo quando si configura una password per l'app.
- Impostazioni di controllo dell'accesso Client locale non vengono rispettate tramite password dell'app.
- Si perderanno locale la funzionalità di registrazione di autenticazione per le password di app.
- Disabilita/eliminazione dell'account potrebbe richiedere fino a tre ore per la sincronizzazione della directory, ritardare disabilita / l'eliminazione della password dell'app identità cloud.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'impostazione autenticazione a più fattori Azure o il Server di autenticazione a più fattori Azure con ADFS vedere gli articoli seguenti:

- [Proteggere risorse cloud usando l'autenticazione a più fattori Azure e ADFS](multi-factor-authentication-get-started-adfs-cloud.md)
- [Proteggere le risorse locali e cloud con Server di autenticazione a più fattori Azure con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
- [Proteggere le risorse cloud e locali con Azure a più fattori autenticazione Server ADFS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)
