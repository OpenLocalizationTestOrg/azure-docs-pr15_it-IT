<properties
    pageTitle="Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory | Microsoft Azure"
    description="Fornisce una panoramica dettagliata di come è possono utilizzare i dispositivi Windows 10 Azure Active Directory Join da registrarsi su Azure Active Directory."
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

# <a name="extending-cloud-capabilities-to-windows-10-devices-through-azure-active-directory-join"></a>Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory

## <a name="what-is-azure-active-directory-join"></a>Che cos'è Azure Active Directory partecipare?
Azure Active Directory partecipare (Azure Active Directory partecipare) è una funzionalità che registra un dispositivo appartenenti alla società di Azure Active Directory per la gestione centralizzata del dispositivo. Rende possibile per gli utenti, ad esempio i dipendenti e gli studenti a cui connettersi nel cloud dell'organizzazione tramite Azure Active Directory. In questo modo semplificate distribuzioni di Windows e l'accesso a App dell'organizzazione e le risorse da qualsiasi dispositivo con Windows, sia aziendali che privati (BYOD).

Azure Active Directory Join è per le organizzazioni che sono cloud-prima/basata solo su cloud, in genere piccole e medie imprese che non dispongono di un'infrastruttura di Windows Server Active Directory locale. I Join di Active Directory di Azure possibile e verranno anche verranno utilizzate dalla organizzazioni di grandi dimensioni nei dispositivi che sono in grado di eseguire un join dominio tradizionale (dispositivi mobili, ad esempio) o per gli utenti che hanno l'esigenza principalmente per accedere a Office 365 o altre app di Azure Active Directory SaaS.

Sebbene il join dominio tradizionale offre che la migliore locale esperienza nei dispositivi che sono in grado di partecipazione a dominio, Azure Active Directory Join è adatto per i dispositivi che non è possibile partecipare di dominio. Azure Active Directory Join anche è appropriata per la gestione di utenti nel cloud. Non con funzionalità di gestione di dispositivi mobili anziché mediante gli strumenti di gestione del dominio tradizionale come criteri di gruppo e System Center Configuration Manager (SCCM).

![Panoramica dei dispositivi aziendali e personale con Active Directory locale e Azure Active Directory](./media/active-directory-azureadjoin/active-directory-azureadjoin-overview.png)


## <a name="why-should-enterprises-adopt-azure-ad-join"></a>Perché aziende adottare Azure Active Directory partecipare?

* **Aziende che sono ampiamente nel cloud**: se è stato spostato o spostamento di un modello in cui si sta riducendo il consumi sull'ambiente locale e si vuole operare più nel cloud, Azure Active Directory partecipare potrebbero trarre vantaggio si. Se sono stati creati account Azure Active Directory manualmente o mediante la sincronizzazione di Active Directory locale. In entrambi i casi, si dispone di un account in Azure Active Directory e usarlo per accedere a Windows 10. Gli utenti possono partecipare i computer di Azure Active Directory tramite l'esperienza di pronte (della configurazione guidata) o tramite il menu impostazioni. Dopo l'aggiunta, gli utenti utilizzeranno single sign-on (SSO) accesso alle risorse cloud come Office 365 nel browser o nelle applicazioni di Office.

* **Istituti di istruzione**: uno degli scenari sono a conoscenza spesso è che istituti di istruzione due tipi di utente: docenti e studenti. I membri di istituti di istruzione sono considerati più lungo termine membri dell'organizzazione, è opportuno creare account locali per tali. Ma studenti shorter-term appartengono all'organizzazione e pertanto possono essere gestiti in Azure Active Directory. Questo errore indica che è possibile inserire scala directory nel cloud anziché archiviati in locale. Inoltre, significa che gli studenti possono accedere a Windows con il proprio account Azure Active Directory e per accedere alle risorse di Office 365 nel browser o nelle applicazioni di Office.

* **Rivenditori**: abbiamo avanzate dai clienti anche hanno maggiori possibilità di gestire più facilmente stagionale.  Nuovo account per i dipendenti a lungo termine, tempo pieno in genere vengono create come account di dominio computer locale. Ma stagionale sono shorter-term membri dell'organizzazione, pertanto è opportuno gestirli nel punto in cui le licenze utente possono essere più facilmente spostate. Creazione di questi account utente nel cloud con licenze di Office 365 consente agli utenti ottenere vantaggi dell'accesso a Windows e nelle applicazioni Office con un account Azure Active Directory. Nel frattempo, conservare una maggiore flessibilità con le licenze una volta terminata.
* **Altre aziende**: anche se si gestiscono gli utenti in Active Directory locale, potrebbero comunque trarre vantaggio da consentire agli utenti di essere aggiunti a Azure Active Directory. Ciò avviene perché Azure Active Directory offre un'esperienza join semplificata, gestione di dispositivi efficiente, registrazione gestione automatica dispositivo mobile e la funzionalità di single sign-on per Azure Active Directory e le risorse locali.  

## <a name="what-capabilities-does-azure-ad-join-offer"></a>Quali funzionalità di Azure Active Directory partecipare offre?
Con Azure Active Directory partecipare, viene visualizzato il seguente:

* **Il provisioning automatico dei dispositivi di proprietà dell'azienda**: con Windows 10, gli utenti possono configurare un nuovo, il dispositivo di fuori della configurazione guidata, senza il coinvolgimento di personale.


* **Supporto per fattori di forma moderno**: Azure Active Directory Join funziona nei dispositivi che non aggiungono il dominio tradizionale funzionalità.  


* **Supporto per gli account dell'organizzazione esistenti**: gli utenti non è più necessario creare e gestire un un account Microsoft personale per ottenere la migliore esperienza nei dispositivi rilasciati società come avveniva con Windows 8. Gli account di lavoro esistente possono utilizzare invece di Azure Active Directory. Per molte organizzazioni, ciò significa sostanzialmente che gli utenti possono configurare e accedere a Windows con le stesse credenziali utilizzate per accedere a Office 365.


* **Registrazione di gestione automatica dispositivo mobile**: dispositivi possono essere iscritti automaticamente gestione di dispositivi mobili quando la connessione di Azure Active Directory. Questo processo funziona con soluzioni di gestione di dispositivi mobili Microsoft Intune e partner. Al termine del gestione di dispositivi con Intune, gli amministratori IT possono monitor/gestire Azure fanno parte di Active Directory dispositivi insieme a dispositivi dominio nella console di SCCM.


* **Accesso singolo risorse aziendali**: gli utenti potranno usufruire servizio single sign-on dal desktop di Windows per App e le risorse nel cloud, ad esempio Office 365 e migliaia di applicazioni aziendali che si basano su Azure Active Directory per l'autenticazione tramite [Azure AD Connect](active-directory-azureadjoin-deployment-aadjoindirect.md). Dispositivi appartenenti alla società che fanno parte di Azure Active Directory sfrutteranno anche Single Sign-on per le risorse locali quando il dispositivo è in una rete aziendale e da qualsiasi posizione quando queste risorse vengono esposte tramite il [Proxy di applicazione Azure Active Directory](https://msdn.microsoft.com/library/azure/Dn768219.aspx).


* **Roaming stato del sistema operativo**: impostazioni di accessibilità, siti, le password di rete Wi-Fi e altre impostazioni vengono sincronizzati tra i dispositivi di proprietà dell'azienda senza un account Microsoft personale.


* **Aziendale di Windows Store**: di Windows Store supporta app acquisizione e gestione delle licenze con un account di Azure Active Directory. Le organizzazioni possono App contratto multilicenza e renderli disponibili per gli utenti della propria organizzazione.

## <a name="how-do-different-devices-work-with-azure-ad-join"></a>Funzionano di diversi dispositivi con Azure Active Directory partecipare

| Dispositivo aziendale (membro di dominio locale)                                                                                                                                                                                         | Dispositivo aziendale (unita nel cloud)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | Dispositivo personale                                                                                                         |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Gli utenti possano accedere a Windows con le credenziali di lavoro (come accade attualmente).                                                                                                                                                                        | Gli utenti possono accedere a Windows con le credenziali di lavoro che vengono gestite in Azure Active Directory. Nel caso di dispositivi aziendali in tre casi: 1) all'organizzazione non dispone di Active Directory locale (ad esempio una piccola azienda). 2) organizzazione non crea tutti gli account utente in Active Directory (ad esempio gli account per studenti, consulenti o stagionale non vengono creati in Active Directory). 3) l'organizzazione ha aziendali dispositivi che non possono essere associati a un dominio (locale), ad esempio telefoni o Tablet PC che eseguono uno SKU Mobile (ad esempio, un dispositivo secondario a un piano factory/retail). Azure Active Directory Join supporta l'aggiunta dei dispositivi aziendali per le organizzazioni federati e non gestito. | Utenti di accedere a Windows con le proprie credenziali di account Microsoft personale (nessun cambiamento).                                                |
| Gli utenti hanno accesso alle impostazioni di roaming ed enterprise di Windows Store. Questi servizi compatibile con gli account di lavoro e non richiedono un account Microsoft personale. È necessario alle organizzazioni di connettersi loro Active Directory locale Azure Active Directory.                                        | Gli utenti possono eseguire il programma di installazione modalità self-service. Può essere eseguita per la prima esecuzione (FRX) tramite il proprio account di lavoro come alternativa alla disposizione IT dispositivi, anche se entrambi i metodi sono supportati.                                                                                                                                                                                                                                                                                                                                                                             | Gli utenti possono facilmente aggiungere un account di lavoro che viene gestito in Active Directory o Azure Active Directory.                                                      |
| Gli utenti hanno la possibilità di Single Sign-on dal desktop per l'uso di App, siti e risorse, incluse le risorse locali e applicazioni basate su cloud che utilizzano Azure Active Directory per l'autenticazione.                                                                                                            | Dispositivi automaticamente registrati nella directory aziendale (Azure Active Directory) e automaticamente registrati in Gestione di dispositivi mobili. (Funzione di azure Active Directory Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                                  | Gli utenti hanno possibilità di Single Sign-on tra App e a siti Web/risorse con l'account di lavoro.                                              |
| Gli utenti possono aggiungere il proprio account Microsoft personale per accedere alle immagini personali e i file senza impatto dati aziendali. (Impostazioni comuni continuano a lavorare con il proprio account di lavoro). L'account Microsoft consente di Single Sign-on e non è più unità il roaming delle impostazioni.  | Gli utenti possono eseguire reimpostazione della password self-service (SSPR) su winlogon, vale a dire che possono reimpostare una password dimenticata. (Funzione di azure Active Directory Premium).                                                                                                                                                                                                                                                                                                                                                                                                                                   | Gli utenti hanno accesso alla rete aziendale di Windows Store in modo che possano acquisire e utilizzare le applicazioni line-of-business su dispositivi personali. |                                                               |


## <a name="additional-information"></a>Ulteriori informazioni
* [Windows 10 per le aziende: modalità di utilizzo di dispositivi per il lavoro](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estensione delle funzionalità cloud per dispositivi Windows 10 tramite Join Azure Active Directory](active-directory-azureadjoin-user-upgrade.md)
* [Autenticazione delle identità senza password tramite Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Informazioni su scenari di utilizzo per partecipare a Azure Active Directory](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Connettere dispositivi dominio Azure Active Directory per Windows 10 esperienze](active-directory-azureadjoin-devices-group-policy.md)
* [Impostare i Join di Azure Active Directory](active-directory-azureadjoin-setup.md)
