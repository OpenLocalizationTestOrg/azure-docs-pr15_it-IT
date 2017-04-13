<properties
    pageTitle="Panoramica dei servizi di dominio di Azure Active Directory | Microsoft Azure"
    description="Panoramica dei servizi di dominio di Azure Active Directory"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services"></a>Servizi di dominio Active Directory Azure

## <a name="overview"></a>Panoramica
Servizi di infrastruttura di Azure consente di distribuire un'ampia gamma di soluzioni informatiche, in modo agile. Con macchine virtuali di Azure, è possibile distribuire quasi istantaneamente e pagare solo tramite il minuto. Utilizzando il supporto per Windows, Linux, SQL Server, Oracle, IBM, SAP e BizTalk, è possibile distribuire qualsiasi carico di lavoro, qualsiasi lingua, praticamente qualsiasi sistema operativo. Questi vantaggi consentono di eseguire la migrazione di applicazioni legacy distribuite in locale in Azure, per salvare in spese operative.

Un aspetto chiave di migrazione delle applicazioni in locale in Azure gestisce le esigenze di identità di queste applicazioni. Applicazioni basate su directory si basano su LDAP per l'accesso in lettura o scrittura alla directory aziendale o si basano su autenticazione di Windows (autenticazione Kerberos o NTLM) per eseguire l'autenticazione degli utenti finali. Line-of-business applicazioni line eseguono Windows Server vengono in genere distribuite nei computer di dominio, in modo che possano essere gestiti in modo sicuro tramite criteri di gruppo. Alle applicazioni di 'ascensore e MAIUSC' locale nel cloud, è necessario risolvere questi dipendenza l'infrastruttura di identità aziendale.

Gli amministratori spesso agevolare una delle seguenti soluzioni per soddisfare le esigenze di identità delle applicazioni distribuite in Azure:

- Distribuire una connessione VPN da sito tra carichi di lavoro in esecuzione in servizi di infrastruttura di Windows Azure e la directory aziendale locale.
- Estendere l'infrastruttura di/delle strutture di Active Directory aziendale installando e configurando controller di dominio replica tramite macchine virtuali di Azure.
- Distribuire un dominio autonomo in Azure utilizzano controller di dominio distribuito come macchine virtuali di Azure.

Tutti questi approcci soggetta a costo elevato e il sovraccarico amministrativo. Gli amministratori sono necessarie per distribuire controller di dominio tramite macchine virtuali in Azure. Inoltre, è necessario gestire, proteggere, patch, monitoraggio, eseguirne il backup e risolvere i problemi di queste macchine virtuali. Dipendenza connessioni VPN alla directory locale causa carichi di lavoro distribuite in Azure esposti a anomalie di rete temporanei o interruzioni. Le interruzioni della rete, a sua volta come risultato inferiore tempi di attività e riduzione dell'affidabilità queste applicazioni.

Abbiamo progettato servizi di dominio Active Directory di Azure per fornire un'alternativa più semplice.


## <a name="introducing-azure-ad-domain-services"></a>Introduzione ai servizi di dominio Active Directory Azure
Azure servizi di dominio Active Directory offre servizi di dominio gestito, ad esempio aggiunta al dominio, l'autenticazione Kerberos/NTLM di criteri, LDAP, gruppo completamente compatibili con Windows Server Active Directory. È possibile utilizzare questi servizi di dominio senza la necessità di distribuire, gestire e patch controller di dominio nel cloud. Azure servizi di dominio Active Directory si integra con l'attuale tenant di Azure Active Directory, consentendo agli utenti di accedere usando le proprie credenziali aziendale. Inoltre, è possibile utilizzare i gruppi esistenti e gli account utente per proteggere l'accesso alle risorse, in modo da garantire un più fluido 'ascensore di spostamento e' di risorse locale di servizi di infrastruttura di Windows Azure.

Funzionalità di servizi di dominio Active Directory Azure funziona perfettamente indipendentemente se il tenant di Azure Active Directory è basata solo su cloud o sincronizzati con Active Directory locale.

### <a name="azure-ad-domain-services-for-cloud-only-organizations"></a>Azure servizi di dominio Active Directory per le organizzazioni basata solo su cloud
Un basata solo su cloud tenant di Azure Active Directory (spesso in 'tenant gestito') non ha alcun ingombro identità locale. In altre parole, gli account utente, le password e appartenenze ai gruppi sono tutti elementi native nel cloud, vale a dire creati e gestiti in Azure Active Directory. Considerare che Contoso è una basata solo su cloud tenant di Azure Active Directory. Come mostrato nella figura seguente, l'amministratore di Contoso ha configurato una rete virtuale in servizi di infrastruttura di Windows Azure. Le applicazioni e carichi di lavoro server vengono distribuiti in questa rete virtuale in macchine virtuali di Azure. Poiché Contoso è un tenant basata solo su cloud, tutte le identità utente, le proprie credenziali e appartenenza ai gruppi vengono creati e gestiti in Azure Active Directory.

![Panoramica di servizi di dominio Azure](./media/active-directory-domain-services-overview/aadds-overview.png)

Contoso amministratore IT può abilitare Servizi di dominio di Azure Active Directory per il tenant di Azure Active Directory e scelga di renderlo disponibile in questa rete virtuale servizi di dominio. Successivamente, servizi di dominio Active Directory Azure esegue il provisioning di un dominio gestito e rende disponibili in rete virtuale. Tutti gli account utente, appartenenze ai gruppi e le credenziali dell'utente disponibili nel tenant di Azure Active Directory di Contoso sono disponibili anche in questo dominio appena creato. Questa caratteristica consente agli utenti dell'organizzazione accedere al dominio utilizzando le proprie credenziali aziendale, ad esempio, quando la connessione remota a dominio computer tramite Desktop remoto. Gli amministratori possono effettuare il provisioning di accesso alle risorse nel dominio utilizzando l'appartenenza ai gruppi esistenti. Le applicazioni distribuite in macchine virtuali nella rete virtuale è possono usare caratteristiche quali aggiunta al dominio LDAP lettura, LDAP associazione, l'autenticazione Kerberos e NTLM e criteri di gruppo.

Alcuni aspetti salienti del dominio gestito viene eseguito il provisioning da servizi di dominio Active Directory Azure sono i seguenti:

- Contoso amministratore IT non è necessario gestire, patch o monitorare questo dominio o controller di dominio per questo dominio gestito.
- Non è necessario per gestire la replica di Active Directory per questo dominio. Account utente, appartenenze ai gruppi e le credenziali di tenant di Azure Active Directory di Contoso sono automaticamente disponibili in questo dominio gestito.
- Poiché il dominio viene gestito da Azure Active Directory servizi di dominio, Contoso amministratore IT non dispone di privilegi di amministratore di dominio o amministratore dell'organizzazione in questo dominio.


### <a name="azure-ad-domain-services-for-hybrid-organizations"></a>Azure servizi di dominio Active Directory per le organizzazioni ibrido
Organizzazioni con un ibrido infrastruttura IT utilizzano una combinazione di risorse cloud e locale. Tali organizzazioni sincronizzare le informazioni di identità da loro directory locale a tenant di Azure Active Directory. Come organizzazioni ibrido Cerca per eseguire la migrazione più delle applicazioni in locale nel cloud, in particolare legacy directory applicazioni, possono essere utili a tali servizi di dominio Active Directory Azure.

Litware Corporation è distribuito [Azure AD Connect](../active-directory/active-directory-aadconnect.md)per sincronizzare le informazioni di identità da loro directory locale a tenant di Azure Active Directory. Le informazioni sull'identità che viene sincronizzate include gli account utente, gli hash delle credenziali per l'autenticazione (la sincronizzazione delle password) e appartenenze ai gruppi.

> [AZURE.NOTE] **La sincronizzazione delle password è obbligatoria per le organizzazioni ibrido utilizzare i servizi di dominio di Azure Active Directory**. Questo requisito è perché sono necessarie le credenziali degli utenti nel dominio gestito fornito da servizi di dominio Active Directory di Azure, per eseguire l'autenticazione questi utenti tramite i metodi di autenticazione NTLM o Kerberos.

![Servizi di dominio di Active Directory Azure per Litware S.p.a](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

La figura precedente illustra come le organizzazioni con un ibrido infrastruttura IT, ad esempio Litware Corporation, possono utilizzare servizi di dominio Active Directory Azure. Applicazioni e carichi di lavoro di server che richiedono servizi di dominio del Litware vengono distribuiti in una rete virtuale in servizi di infrastruttura di Windows Azure. Del Litware amministratore IT può abilitare Servizi di dominio di Azure Active Directory per il tenant di Azure Active Directory e scelga di renderlo disponibile in questa rete virtuale un dominio gestito. Poiché Litware è un'organizzazione con una distribuzione ibrida di infrastruttura IT, le credenziali, gruppi e gli account utente vengono sincronizzate con tenant di Azure Active Directory da loro directory locale. Questa caratteristica consente agli utenti di accedere al dominio utilizzando le proprie credenziali aziendale, ad esempio, quando la connessione remota a computer aggiunto al dominio tramite Desktop remoto. Gli amministratori possono effettuare il provisioning di accesso alle risorse nel dominio utilizzando l'appartenenza ai gruppi esistenti. Le applicazioni distribuite in macchine virtuali nella rete virtuale è possono usare caratteristiche quali aggiunta al dominio LDAP lettura, LDAP associazione, l'autenticazione Kerberos e NTLM e criteri di gruppo.

Alcuni aspetti salienti del dominio gestito viene eseguito il provisioning da servizi di dominio Active Directory Azure sono i seguenti:

- Il dominio gestito è un dominio autonomo. Non è un'estensione del dominio locale del Litware.
- Del Litware amministratore IT non è necessario gestire, patch, o controllare controller di dominio per questo dominio gestito.
- Non è necessario per gestire la replica di Active Directory per questo dominio. Account utente, appartenenze ai gruppi e le credenziali di directory locale del Litware vengono sincronizzate con Azure Active Directory tramite Azure AD Connect. Gli account utente, l'appartenenza ai gruppi e le credenziali sono automaticamente disponibili all'interno del dominio gestito.
- Poiché il dominio viene gestito da Azure Active Directory servizi di dominio, Litware amministratore IT non dispone di privilegi di amministratore di dominio o amministratore dell'organizzazione in questo dominio.


## <a name="benefits"></a>Vantaggi
Con i servizi di dominio Active Directory di Azure, è possibile usufruire dei seguenti vantaggi:

-   **Semplice** : È in grado di soddisfare le esigenze di identità macchine virtuali distribuito ai servizi di infrastruttura di Azure con pochi semplici. Non è necessario distribuire e gestire l'infrastruttura di identità in Azure o imposta la connettività dell'infrastruttura di identità locale.

-   **Integrata** : servizi di dominio Active Directory Azure è integrato con il tenant di Azure Active Directory. È ora possibile usare Azure Active Directory di una directory aziendale integrato basato su cloud che si in base alle esigenze dei più recenti applicazioni e applicazioni basate su directory tradizionali.

-   **Compatibile** : servizi di dominio Active Directory Azure si basa sull'infrastruttura di voto consolidate enterprise di Windows Server Active Directory. Di conseguenza, le applicazioni possono basarsi su una maggiore di compatibilità con le funzionalità di Windows Server Active Directory. Non tutte le caratteristiche disponibili in Windows Server Active Directory sono attualmente disponibili in servizi di dominio Active Directory Azure. Tuttavia, funzionalità disponibili sono compatibili con le funzionalità di Windows Server Active Directory corrispondente che si utilizzano normalmente dell'infrastruttura locale. Funzioni di join LDAP, Kerberos, NTLM, criteri di gruppo e dominio costituiscono un'offerta consolidata testata e notte su diverse versioni di Windows Server.

-   **Economico** – con servizi di dominio Active Directory Azure, è possibile evitare il carico e la gestione associato gestione infrastruttura delle identità per supportare le applicazioni compatibili con directory tradizionale. È possibile spostare le applicazioni di servizi di infrastruttura di Windows Azure e trarre vantaggio da un maggiore risparmio di spese operative.
