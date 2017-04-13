<properties
    pageTitle="Considerazioni di progettazione identità di distribuzione ibrida di Azure Active Directory - Panoramica | Microsoft Azure"
    description="Panoramica e mappa del contenuto della Guida Considerazioni sulla progettazione di identità ibrido"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerazioni sulla progettazione di identità ibrido di Azure Active Directory

Dispositivi basati su consumer sono proliferating tutto il mondo aziendale e applicazioni basate su cloud di software come servizio (SaaS) facili da adottare. Di conseguenza, Gestione controllo accesso degli utenti applicazione piattaforme interno Data Center e cloud è un'operazione difficile.  Identità le soluzioni Microsoft occupare locale e le funzionalità basate su cloud, la creazione di una singola identità utente per l'autenticazione e l'autorizzazione per tutte le risorse, indipendentemente dalla posizione. Si chiama questa identità ibrida. Esistono vari e opzioni di configurazione per identity ibrido utilizzando le soluzioni Microsoft e in alcuni casi di che può risultare difficile determinare maggiormente la combinazione soddisfano le esigenze dell'organizzazione. Questa Guida di considerazioni sulla progettazione di identità ibrido aiuta a capire come per progettare una soluzione di identità ibrida che meglio dell'azienda e tecnologia esigenze dell'organizzazione.  Questa guida specificano una serie di passaggi e attività che è possibile eseguire per progettare una soluzione di identità ibrida che soddisfa esigenze dell'organizzazione. Nel corso della procedura e attività, la Guida presenterà le tecnologie rilevanti e le opzioni di funzionalità disponibili per le aziende per incontrarsi funzionale e la qualità del servizio (ad esempio disponibilità, scalabilità, prestazioni, gestibilità e protezione) requisiti del livello. In particolare, il distribuzione ibrida identità considerazioni sulla Guida obiettivi sono per rispondere alle domande seguenti: 

- Quali domande necessari per porre domande e risposte per indirizzare un design di identità specifiche ibrido per un dominio tecnologia o problema che meglio soddisfi i requisiti
- La sequenza delle attività è necessario completare per progettare una soluzione di identità ibrida per il dominio tecnologia o problema 
- Le opzioni di tecnologia e configurazione di identità ibrido disponibili soddisfare i requisiti? Quali sono compromesso tra le opzioni in modo che è possibile selezionare l'opzione ottimale per l'attività aziendale?


## <a name="who-is-this-guide-intended-for"></a>Chi deve essere in questa Guida?
 CIO, CITO, progettisti di identità di amministratore, progettisti dell'organizzazione e progettisti IT responsabile per la progettazione di una soluzione di identità ibrida per le organizzazioni medie-grandi.

## <a name="how-can-this-guide-help-you"></a>Come questa guida consentono di? 
È possibile usare questa guida per capire come progettare una soluzione di identità ibrida in grado di integrare un sistema di gestione delle identità basato sul cloud con la soluzione di identità locale corrente. Indicato nella figura seguente esempio una soluzione di identità ibrida che consente agli amministratori IT per la gestione di integrare la soluzione di Windows Server Active Directory corrente Trova locale con Microsoft Azure Active Directory per consentire agli utenti di utilizzare Single Sign-On (SSO) tra le applicazioni presenti nel cloud e locale.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


La figura riportata sopra è illustrato un esempio di una soluzione di identità ibrido che sfruttando servizi cloud per l'integrazione con funzionalità locale per assicurare un'esperienza singola per il processo di autenticazione degli utenti finali e per facilitare la gestione di tali risorse IT. Anche se può trattarsi di uno scenario molto comune, design identità ibrido ogni dell'organizzazione è probabile che sia diverso da quello nell'esempio illustrato nella figura 1 a causa di requisiti differenti. Questa guida fornisce una serie di passaggi e attività che è possibile eseguire per progettare una soluzione di identità ibrida che soddisfa esigenze dell'organizzazione. Nell'intera le seguenti operazioni e le attività, la Guida presenta le tecnologie rilevanti e le opzioni di funzionalità disponibili per soddisfare requisiti livello di qualità del servizio per l'organizzazione e funzionalità.

**Ipotesi**: acquisire familiarità con Windows Server, servizi di dominio Active Directory e Azure Active Directory. In questo documento si presuppone che si sta cercando come queste soluzioni possono soddisfare esigenze aziendali autonomamente o in una soluzione integrata.

## <a name="design-considerations-overview"></a>Cenni preliminari sulla considerazioni di progettazione
Questo documento offre un set di passaggi e attività che è possibile eseguire per progettare una soluzione di identità ibrida più affine alle proprie esigenze. I passaggi sono suddivise in una sequenza ordinata. Considerazioni sulla progettazione di che informazioni nei passaggi successivi potrebbe essere necessario modificare scelte effettuate nella procedura precedente, tuttavia, a causa di scelte di progettazione in conflitto. Ogni tentato di potenziali conflitti di progettazione in tutto il documento avviso. 

Verranno recapitati la struttura che più si adatta ai propri requisiti solo dopo lo scorrimento passaggi le volte che è necessario incorporare tutte le considerazioni all'interno del documento. 

| Fase di identità ibrido                                             | Elenco di argomenti                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Determinare i requisiti di identità                                   | [Determinare le esigenze aziendali](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Requisiti di sincronizzazione della directory](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinare i requisiti di autenticazione a più fattori](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definire una strategia di adozione di identità ibrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Piano per il miglioramento della protezione dei dati tramite soluzione identità sicura | [Determinare i requisiti di protezione dei dati](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinare i requisiti di gestione dei contenuti](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Requisiti di controllo di accesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinare i requisiti di risposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definire una strategia di protezione dei dati](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Piano per ciclo di vita identità ibrido                                | [Determinare l'attività di gestione delle identità ibrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestione della sincronizzazione](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinare la strategia di adozione gestione delle identità ibrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Scaricare questa Guida
È possibile scaricare una versione pdf della Guida per i considerazioni sulla progettazione di identità ibrido nella [raccolta Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             
