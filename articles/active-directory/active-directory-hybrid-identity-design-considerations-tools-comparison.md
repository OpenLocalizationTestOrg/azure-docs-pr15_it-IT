<properties
    pageTitle="Identità ibrido: Gli strumenti di integrazione della Directory confronto | Microsoft Azure"
    description="Questa pagina fornisce una tabella completa effettuare il confronto tra i vari strumenti di integrazione di directory che possono essere usati per l'integrazione di directory."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Integrazione della directory identità ibrido strumenti confronto

Nel corso degli anni di strumenti di integrazione della directory si ampliata e derivato.  In questo documento è consente di ottenere una visualizzazione consolidata di questi strumenti e un confronto delle funzionalità che sono disponibili in ogni.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect include i componenti e funzionalità pubblicate in precedenza con Dirsync e AAD Sync. Questi strumenti non è più pubblicati singolarmente e tutti i miglioramenti futuri verranno inclusi negli aggiornamenti Azure AD Connect, in modo da sapere sempre dove ottenere la funzionalità più recente.
>
>Sincronizzazione di Azure Active Directory e DirSync sono obsoleti. Altre informazioni disponibili in [seguito](active-directory-aadconnect-dirsync-deprecated.md).


Utilizzare la seguente chiave per ciascuna delle tabelle.

● = Disponibili  
FR = versioni Future  
PE = Public Preview  

## <a name="on-premises-to-cloud-synchronization"></a>In locale nel Cloud sincronizzazione

| Caratteristica  | La connessione di Azure Active Directory  | Servizi di sincronizzazione di Azure Active Directory (AAD Sync) | Strumento di sincronizzazione di Azure Active Directory (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Gestione identità Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Connettersi a singola foresta Active Directory locale | ● | ● | ● | ● |● |
| Connettersi a più strutture di Active Directory locale |●  | ● |  | ● |● |
| Connettersi a più organizzazioni di Exchange locale | ● |  |  |  | |
| Connettersi alla directory LDAP locale singola | FR |  |  | ● |● |
| Connettersi a più directory LDAP locale |FR  |  |  | ● |● |
| Connettersi a Active Directory locale e directory LDAP locale |FR  |  |  | ● |● |
| Connettersi a sistemi personalizzati (ad esempio SQL, Oracle, MySQL e così via) | FR |  |  | ● |● |
| Sincronizzare gli attributi definito dall'utente (estensioni directory) | ● |  |  |  |  |
|Connettersi a HR locale (ad esempio, SAP, Oracle eBusiness, PeopleSoft)| FR |  |  | ● |● |
|Supporta le regole di sincronizzazione FIM e connettori per il provisioning di sistemi locali.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Cloud alla sincronizzazione locale

| Caratteristica  | La connessione di Azure Active Directory  | Servizi di sincronizzazione di Azure Active Directory | Strumento di sincronizzazione di Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestione identità Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Writeback dei dispositivi | ● |  | ● |  ||
| Attributo writeback (per la distribuzione ibrida di Exchange) | ● | ● | ● | ● |● |
| Writeback di utenti e gruppi di oggetti |  ●|  | |  ||
| Writeback di password (dalla reimpostazione della password self-service (SSPR) e la modifica della password) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Supporto delle funzioni di autenticazione

| Caratteristica  | La connessione di Azure Active Directory | Servizi di sincronizzazione di Azure Active Directory | Strumento di sincronizzazione di Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestione identità Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| La sincronizzazione delle password per singola locale strutture di Active Directory | ● | ● | ● |  ||
| La sincronizzazione delle password per più strutture di Active Directory in locale |  ●| ● |  |  ||
| Single Sign-on con la federazione | ● | ● | ● | ● |● |
| Writeback di password (da SSPR e la password di modifica) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Installazione e configurazione

| Caratteristica  | La connessione di Azure Active Directory  | Servizi di sincronizzazione di Azure Active Directory | Strumento di sincronizzazione di Azure Active Directory (DirSync) | Gestione identità Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Supporta l'installazione su un Controller di dominio | ● | ● | ● |  |
| Eseguire l'installazione con SQL Express | ● | ● | ● |  |
| Aggiornamento facile da DirSync |● |  |  |  |
| Localizzazione di Admin UX verso lingue di Windows Server | ● | ● | ● |  |
|Localizzazione di utente finale dell'esperienza utente per le lingue di Windows Server| |  |  |● |
| Supporto per Windows Server 2008 e Windows Server 2008 R2 | ● per la sincronizzazione, non per la federazione| ● | ●  | ● |
| Supporto per Windows Server 2012 e Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Applicazione di filtri e configurazione

Caratteristica  | La connessione di Azure Active Directory | Servizi di sincronizzazione di Azure Active Directory | Strumento di sincronizzazione di Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Gestione identità Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Applicare il filtro domini e unità organizzative | ● | ● | ● | ●  | ●
Filtrare i valori degli attributi degli oggetti | ● | ● | ● | ●| ●
Consenti numero minimo di attributi da sincronizzata (MinSync) | ● | ● |  ||
Consentire ai modelli di servizi diversa da applicare per i flussi di attributo |●  | ● |  ||
Consentire la rimozione di attributi da provenienti da Active Directory di Azure Active Directory | ● | ● |  |  |
Consenti personalizzazione avanzata per i flussi di attributo | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Passaggi successivi
Ulteriori informazioni [sull'integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md).
