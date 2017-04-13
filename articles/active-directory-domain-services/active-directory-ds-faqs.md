<properties
    pageTitle="Domande frequenti - servizi di dominio Active Directory Azure | Microsoft Azure"
    description="Domande frequenti sui servizi di dominio di Azure Active Directory"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="azure-active-directory-domain-services-frequently-asked-questions-faqs"></a>Servizi di dominio Azure Active Directory: Domande frequenti domande frequenti

Questa pagina fornisce le risposte alle domande più frequenti relative ai servizi di dominio Azure Active Directory. Mantenere il controllo per gli aggiornamenti.

### <a name="troubleshooting-guide"></a>Guida alla risoluzione dei problemi
Consultare la [Guida alla risoluzione dei problemi](active-directory-ds-troubleshooting.md) per le soluzioni ai problemi comuni durante la configurazione o l'amministrazione dei servizi di dominio di Azure Active Directory.


### <a name="configuration"></a>Configurazione

#### <a name="can-i-create-multiple-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini per un singolo directory di Azure Active Directory?
No. È possibile creare solo un unico dominio gestito da servizi di dominio Active Directory Azure per una singola directory di Azure Active Directory.  

#### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile attivare servizi di dominio Active Directory di Azure in una rete virtuale Manager delle risorse di Azure?
No. Azure servizi di dominio Active Directory possono essere attivati solo in una rete virtuale Azure classica. È possibile connettersi alla rete virtuale classica a una rete Manager delle risorse utilizzando virtuali peering per usare il dominio gestito in una rete virtuale Manager delle risorse.

#### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile effettuare servizi di dominio Active Directory Azure disponibili in più reti virtuali all'interno dell'abbonamento?
Il servizio non supporta direttamente questo scenario. Azure servizi di dominio Active Directory è disponibile in un solo virtuali alla volta. Tuttavia, è possibile configurare la connettività tra più reti virtuali per esporre i servizi di dominio Active Directory di Azure ad altre reti virtuale. Questo articolo descrive come è possibile [connettere reti virtuali in Azure](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

#### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile attivare servizi di dominio Active Directory di Azure tramite PowerShell?
Non è attualmente disponibile PowerShell/automatizzato di distribuzione di servizi di dominio Active Directory Azure.

#### <a name="is-azure-ad-domain-services-available-in-the-new-azure-portal"></a>È disponibile nel portale di Azure nuovi servizi di dominio Active Directory Azure?
No. Azure servizi di dominio Active Directory possono essere configurati solo [portale classica Azure](https://manage.windowsazure.com). Si prevede che estendere il supporto per il [portale di Azure](https://portal.azure.com) in futuro.

#### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio di servizi di dominio Active Directory Azure gestito?
No. Il dominio fornito da servizi di dominio Active Directory Azure è un dominio gestito. Non è necessario eseguire il provisioning, configurare o in caso contrario gestire controller di dominio per questo dominio - queste attività di gestione vengono fornite come servizio da Microsoft. Di conseguenza, è possibile aggiungere ulteriori controller (lettura / scrittura o sola lettura) per il dominio gestito.

### <a name="administration-and-operations"></a>Amministrazione e attività operative

#### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile collegare al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni per connettersi al controller di dominio per il dominio gestito tramite Desktop remoto. I membri del gruppo 'AAD controller di dominio Administrators' amministrare dominio gestito tramite gli strumenti di amministrazione di Active Directory, ad esempio il centro di amministrazione Active Directory (ADAC) o PowerShell di Active Directory. Questi strumenti vengono installati utilizzando la caratteristica 'Strumenti di amministrazione Server remoto' in Windows server al dominio gestito.

#### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Attivazione di servizi di dominio Active Directory Azure. Quali account utente è necessario usare a macchine join del dominio per questo dominio?
Gli utenti che aggiunti al gruppo amministrativo (ad esempio, ' AAD controller di dominio amministratori") possono macchine connessione al dominio. Inoltre, gli utenti in questo gruppo vengono concesse accesso desktop remoto ai computer collegato al dominio.

#### <a name="can-i-wield-domain-administrator-privileges-for-the-domain-provided-by-azure-ad-domain-services"></a>È possibile wield privilegi di amministratore di dominio per il dominio forniti da servizi di dominio Active Directory Azure?
No. Non si disponga privilegi di amministratore nel dominio gestito. Privilegi "amministratore di dominio" e ' amministratore dell'organizzazione ' non sono disponibili per l'uso all'interno del dominio. Amministratore di dominio esistente o gruppi di amministratore dell'organizzazione all'interno della directory di Azure Active Directory anche non vengono concessi privilegi di amministratore di dominio/enterprise nel dominio.

#### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-domains-provided-by-azure-ad-domain-services"></a>È possibile modificare l'appartenenza ai gruppi in domini forniti da servizi di dominio Active Directory Azure LDAP o altri strumenti di amministrazione di Active Directory?
No. Appartenenze ai gruppi non possono essere modificate in domini serviti da servizi di dominio Active Directory Azure. Lo stesso vale per gli attributi dell'utente. È tuttavia possibile modificare appartenenze ai gruppi o gli attributi dell'utente in Azure Active Directory o nel dominio locale. Queste modifiche vengono sincronizzate automaticamente in servizi di dominio Active Directory Azure.

#### <a name="can-i-extend-the-schema-of-the-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio fornito da servizi di dominio Active Directory Azure?
No. Lo schema è gestito da Microsoft per il dominio gestito. Estensioni dello schema non sono supportate dai servizi di dominio di Azure Active Directory.

#### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere i record DNS del dominio gestito?
Sì. Gli utenti che appartengono al gruppo 'AAD controller di dominio Administrators' vengono concesse ' DNS' privilegi, per modificare i record DNS nel dominio gestito. Questi utenti è possono utilizzare la console di gestore DNS in un computer che eseguono Windows Server al dominio gestiti per la gestione DNS. Per utilizzare la console DNS Manager, installare "DNS Server Tools", che fa parte della caratteristica facoltativa 'Strumenti di amministrazione Server remoto' nel server. Ulteriori informazioni [sull'utilità per la gestione, monitoraggio e risoluzione dei problemi DNS](https://technet.microsoft.com/library/cc753579.aspx) sono disponibile nel sito TechNet.


### <a name="billing-and-availability"></a>Fatturazione e la disponibilità

#### <a name="is-azure-ad-domain-services-a-paid-service"></a>È che un servizio a pagamento di servizi di dominio di Active Directory di Azure?
Sì. Per ulteriori informazioni, vedere la [pagina prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

#### <a name="is-there-a-free-trial-for-the-service"></a>Esiste una versione di valutazione gratuita per il servizio?
Questo servizio è incluso nella versione di valutazione gratuita per Azure. È possibile iscriversi per una [versione di valutazione gratuita mensile di Azure](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems"></a>È possibile ottenere servizi di dominio Active Directory Azure come parte della famiglia di prodotti mobilità Enterprise (EMS)?
#### <a name="do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È necessario Azure Active Directory Premium per utilizzare i servizi di dominio Active Directory Azure?
No. Azure servizi di dominio Active Directory è un servizio di Azure uso prepagato e non fa parte di EMS. Servizi di dominio Active Directory Azure sono disponibili per tutte le edizioni di Azure Active Directory (gratuito, Basic e, Premium) vengono addebitate su base oraria, a seconda dell'utilizzo.

#### <a name="what-azure-regions-is-the-service-available-in"></a>Quali aree Azure è disponibile nel servizio?
Fare riferimento alla pagina [Servizi di Windows Azure per area geografica](https://azure.microsoft.com/regions/#services/) per visualizzare un elenco delle regioni Azure in cui sono disponibili i servizi di dominio Active Directory Azure.
