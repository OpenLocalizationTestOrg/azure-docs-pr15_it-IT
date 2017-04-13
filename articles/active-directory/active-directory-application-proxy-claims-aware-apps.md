<properties
    pageTitle="Utilizzo delle attestazioni App presente nel Proxy di applicazione"
    description="Viene illustrato come iniziare a utilizzarlo con Azure Active Directory applicazione Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>



# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Utilizzo delle attestazioni App presente nel Proxy di applicazione

App presente sulle attestazioni, eseguire un reindirizzamento per la sicurezza servizio (token), che a sua volta richiede le credenziali dell'utente in un token prima di reindirizzare l'utente all'applicazione. Per abilitare l'applicazione Proxy per l'uso con questi reindirizzamenti, è necessario completare i passaggi seguenti da adottare.

## <a name="prerequisites"></a>Prerequisiti
Prima di eseguire questa procedura, assicurarsi che il servizio token di sicurezza reindirizza l'app presente sulle attestazioni è disponibile di fuori rete locale.

## <a name="azure-classic-portal-configuration"></a>Configurazione del portale classica Azure

1. Pubblicare l'applicazione seguendo le istruzioni nelle [applicazioni di pubblicazione con Proxy dell'applicazione](active-directory-application-proxy-publish.md).
2. Nell'elenco delle applicazioni, selezionare l'app presente sulle attestazioni e fare clic su **Configura**.
3. Se si sceglie **pass-through** come il **Metodo di autenticazione preliminare**, assicurarsi di selezionare **HTTPS** come la combinazione di **URL esterno** .
4. Se si decide di **Azure Active Directory** come il **Metodo di autenticazione preliminare**, selezionare **Nessuno** come il **Metodo di autenticazione interno**.


## <a name="adfs-configuration"></a>Configurazione di ADFS

1. Aprire Gestione ADFS.
2. Passare **all'Attendibilità componente**, fare clic con il pulsante destro app di pubblicazione con Proxy di applicazione, quindi scegliere **proprietà**.  
  ![Componente attendibilità destra fare clic sul nome dell'app - screentshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Nella scheda **endpoint** , in **tipo di Endpoint**, selezionare **WS-Federation**.
4. In **Attendibili URL** immettere l'URL immesso nel Proxy applicazione in **URL esterno** e fare clic su **OK**.  
  ![Aggiungere un punto finale - Imposta valore attendibili URL - schermata](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## <a name="see-also"></a>Vedere anche

- [Pubblicare applicazioni con Proxy dell'applicazione](active-directory-application-proxy-publish.md)
- [Abilitare single sign-in](active-directory-application-proxy-sso-using-kcd.md)
- [Risoluzione dei problemi che si verificano con Proxy dell'applicazione](active-directory-application-proxy-troubleshoot.md)
- [Attivare le applicazioni client nativi interagire con le applicazioni proxy](active-directory-application-proxy-native-client.md)

Per essere sempre informati e gli aggiornamenti più recenti, visitare il [blog di Proxy di applicazione](http://blogs.technet.com/b/applicationproxyblog/)
