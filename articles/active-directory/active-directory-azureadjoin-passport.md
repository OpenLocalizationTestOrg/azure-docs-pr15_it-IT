<properties
    pageTitle="Autenticazione delle identità senza password tramite Microsoft Passport | Microsoft Azure"
    description="Viene fornita una panoramica di Microsoft Passport e altre informazioni sulla distribuzione Microsoft Passport."
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

# <a name="authenticating-identities-without-passwords-through-microsoft-passport"></a>Autenticazione delle identità senza password tramite Microsoft Passport

I metodi di autenticazione tramite password solo correnti non sono sufficienti per impedire agli utenti attendibili. Gli utenti riutilizzare e dimenticano la password. Le password rispettano breachable, phishable soggetta a fessure e identificabili. Ricevono anche difficili da ricordare e soggetto a attacchi come "[passare hash](https://technet.microsoft.com/dn785092.aspx)".

## <a name="about-microsoft-passport"></a>Informazioni su Microsoft Passport
Microsoft Passport è una chiave pubblica/privata o un approccio di autenticazione basata su certificati per le aziende e consumatori che supera le password. Questo tipo di autenticazione si basa su credenziali coppia possono sostituire le password e si prova a violazioni, furti e phishing.

 Microsoft Passport consente di eseguire l'autenticazione a un account Microsoft, un account di Windows Server Active Directory, un account di Microsoft Azure Active Directory (Azure Active Directory) o un servizio non Microsoft che supporta l'autenticazione rapidamente identità Online (FIDO). Dopo una verifica in due fasi iniziali durante la registrazione di Microsoft Passport, Microsoft Passport è stato installato sul dispositivo dell'utente e l'utente imposta un movimento, che può essere Hello Windows o un PIN. L'utente offre la combinazione per verificarne l'identità. Windows utilizza quindi Passport Microsoft per autenticare l'utente e la Guida loro di accedere ai servizi e risorse protette.

La chiave privata è disponibile esclusivamente tramite un movimento"utente" ad esempio un PIN, biometrica o un dispositivo remoto come una smart card che l'utente usa per accedere al dispositivo. Queste informazioni sono collegate a un certificato o una coppia di chiavi asimmetrica. La chiave privata è hardware elevati attestati se il dispositivo ha un chip piattaforma modulo TPM (Trusted). La chiave privata non lascia mai il dispositivo.

Chiave pubblica è registrata con Azure Active Directory e Windows Server Active Directory (per locale). Provider di identità (IDPs) convalidare l'utente mediante la mappatura di chiave pubblica dell'utente per la chiave privata e forniscono informazioni di accesso tramite una sola volta Password (strumento), PhoneFactor o meccanismo di notifica diversi.

## <a name="why-enterprises-should-adopt-microsoft-passport"></a>Perché aziende adottare Microsoft Passport

Abilitando Microsoft Passport aziende possono apportare le proprie risorse ancora più sicura per:

* Impostazione di Microsoft Passport con l'opzione preferita hardware. Questo errore indica che verranno generate chiavi TPM 1.2 o 2.0 TPM quando disponibili. Quando TPM non è disponibile, software genererà la chiave.

* Definire la complessità e la lunghezza del PIN, e se è attivata l'uso di introduzione all'interno dell'organizzazione.

* Configurazione di Passport Microsoft per supportare scenari mi piace della smart card con protezione basata su certificati.

## <a name="how-microsoft-passport-works"></a>Funzionamento di Microsoft Passport
1. Chiavi vengono generate sull'hardware TPM o software. Molti dispositivi presentano un chip TPM predefinito che consente di proteggere l'hardware mediante l'integrazione di crittografia in dispositivi. TPM 1.2 o 2.0 TPM genera chiavi o certificati creati da chiavi generate.

2. Il TPM dichiara questi tasti hardware associato.

3. Un movimento Sblocca singola Sblocca il dispositivo. Questa azione consente di accedere a più risorse se il dispositivo è Azure o dominio fanno parte di Active Directory.

## <a name="how-the-microsoft-passport-lifecycle-works"></a>Come funziona il ciclo di vita del Microsoft Passport

![Ciclo di vita Microsoft Passport](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png)

Il diagramma precedente illustra la coppia di chiavi private/pubbliche e la convalida dal provider di identità. Ognuno di questi passaggi verrà illustrato in dettaglio di seguito:

1. L'utente prova la propria identità tramite più i metodi di correzione incorporati (movimenti, fisica smart card, l'autenticazione a più fattori) e invia queste informazioni al Provider di identità (IDP) come Azure Active Directory o Active Directory locale.

2. Il dispositivo quindi creata la chiave, dichiara la chiave, accetta la parte di questa chiave pubblica, si connette con le istruzioni di espansione, accede e invia a IDP per registrare la chiave.

4. Non appena il IDP registra la parte della chiave pubblica, il IDP richiede il dispositivo per accedere con la parte della chiave privata.

5. Il IDP quindi convalida problemi e il token di autenticazione che consente all'utente e al dispositivo l'accesso alle risorse protette. IDPs possibile scrivere multipiattaforma App o utilizzare il supporto browser (tramite API JavaScript/Webcrypto) per creare e utilizzare credenziali Microsoft Passport per gli utenti.

## <a name="the-deployment-requirements-for-microsoft-passport"></a>Requisiti di distribuzione per Microsoft Passport
### <a name="at-the-enterprise-level"></a>Livello aziendale

* L'organizzazione ha un abbonamento a Azure.

### <a name="at-the-user-level"></a>Livello di utente

* Computer dell'utente esegue Windows 10 Professional o Enterprise.

Per istruzioni dettagliate sulla distribuzione, vedere [Abilitare Microsoft Passport per il lavoro dell'organizzazione](active-directory-azureadjoin-passport-deployment.md).


## <a name="additional-information"></a>Ulteriori informazioni

* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
