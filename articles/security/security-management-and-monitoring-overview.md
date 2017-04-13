<properties
   pageTitle="Gestione della sicurezza Azure e il monitoraggio Panoramica | Microsoft Azure"
   description=" Azure offre meccanismi di protezione per facilitare la gestione e il monitoraggio di servizi cloud Azure e macchine virtuali.  In questo articolo viene fornita una panoramica di queste funzionalità di sicurezza di base e servizi. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="terrylan"/>

# <a name="azure-security-management-and-monitoring-overview"></a>Gestione della sicurezza Azure e il monitoraggio di panoramica

Azure offre meccanismi di protezione per facilitare la gestione e il monitoraggio di servizi cloud Azure e macchine virtuali. In questo articolo viene fornita una panoramica di queste funzionalità di sicurezza di base e servizi. Vengono forniti collegamenti ad articoli che otterranno i dettagli di ogni in modo che si acquisiscono informazioni.

Sicurezza dei servizi cloud Microsoft è una relazione e la responsabilità condivisa tra il mittente e Microsoft. Responsabilità condivisa occorre Microsoft è responsabile di Microsoft Azure e Allinea al Centro protezione fisica dei dati (mediante l'utilizzo di protezioni, ad esempio badge bloccato voce porte, temporali e le protezioni). Inoltre, Azure offre sicuri livelli di protezione cloud il livello di software che soddisfa le esigenze di sicurezza, privacy e conformità dei propri clienti complesse.

Si è proprietari dei dati e delle identità, la responsabilità per la protezione delle loro, la protezione delle risorse in locale e la sicurezza dei componenti cloud su cui si ha un controllo. Microsoft offre funzionalità per proteggere i dati e le applicazioni e controlli di protezione. La gestione della sicurezza è basata sul tipo di servizio cloud.

Il diagramma seguente vengono riepilogati il saldo di responsabilità per Microsoft e il cliente.

![Responsabilità condivise][1]

Per ulteriori approfondimenti nella gestione della sicurezza, vedere [gestione di sicurezza in Azure](azure-security-management.md).

Ecco le funzionalità di base per contenute in questo articolo:

- Controllo dell'accesso basato sui ruoli
- Antimalware
- Autenticazione a più fattori
- ExpressRoute
- Gateway di rete virtuale
- Gestione delle identità privilegi
- Protezione dell'identità
- Centro protezione

## <a name="role-based-access-control"></a>Controllo dell'accesso basato sui ruoli

Controllo di accesso basato sui ruoli (RBAC) consente di gestire l'accesso diffusamente per le risorse Azure. Usa RBAC, è possibile concedere agli utenti solo la quantità di accesso necessari per eseguire le operazioni.  RBAC consente inoltre a garantire che se persone lasciano l'organizzazione perdono l'accesso alle risorse nel cloud.

Ulteriori informazioni:

- [Blog del team di Active Directory su RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Controllo dell'accesso basato sui ruoli Azure](../active-directory/role-based-access-control-configure.md)

## <a name="antimalware"></a>Antimalware

Con Azure è possibile utilizzare software antimalware di fornitori di protezione principali, ad esempio Microsoft, Symantec, Trend Micro, McAfee e Kaspersky per proteggere le macchine virtuali da file dannosi, adware e altri rischi.

Microsoft Antimalware offre la possibilità di installare un agente antimalware per PaaS ruoli e macchine virtuali. In base a System Center Endpoint Protection, questa caratteristica porta locale consolidate grazie alla tecnologia di protezione nel cloud.

Offre integrazione per [Sicurezza complete di Trend](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ e [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ prodotti della piattaforma Azure. DeepSecurity è una soluzione Antivirus e SecureCloud è una soluzione di crittografia. DeepSecurity verrà distribuito all'interno di macchine virtuali utilizzando un modello di estensione. Tramite il portale dell'interfaccia utente e PowerShell, è possibile scegliere di utilizzare DeepSecurity all'interno di nuove macchine virtuali che disattivate backup o macchine virtuali esistente che si è già distribuite.

Symantec Endpoint Protection (set) è supportata anche in Azure. Tramite l'integrazione del portale, hanno la possibilità di specificare che si prevede di utilizzare set all'interno di una macchina virtuale. Segnalatore può essere installato in una nuova macchina virtuale tramite il portale di Azure o può essere installato in una macchina virtuale esistente tramite PowerShell.

Ulteriori informazioni:

- [Distribuire soluzioni Antimalware in macchine virtuali di Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft Antimalware per i servizi Cloud Azure e macchine virtuali](../security/azure-security-antimalware.md)
- [Come installare e configurare Trend Micro complete Security come servizio in una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Come installare e configurare Symantec Endpoint Protection in una macchina virtuale di Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nuove opzioni di Antimalware per la protezione delle macchine virtuali Azure – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Autenticazione a più fattori

Autenticazione a più fattori Azure (MFA) è un metodo di autenticazione che richiede l'uso di più di un metodo di verifica e si aggiunge un critico secondo livello di sicurezza per gli accessi utente e le transazioni. MFA consente per proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo. Offre autenticazione tramite un intervallo delle opzioni di verifica, telefonata, messaggio di testo o app per dispositivi mobili notifica o verifica codice e 3o festa GIURAMENTO token.

Ulteriori informazioni:

- [Autenticazione a più fattori](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Che cos'è l'autenticazione a più fattori Azure?](../multi-factor-authentication/multi-factor-authentication.md)
- [Funzionamento dell'autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## <a name="expressroute"></a>ExpressRoute

Microsoft Azure ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata dedicata mediante un provider di integrazione applicativa. Con ExpressRoute, è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure, Office 365 e CRM Online. Connettività può essere da una qualsiasi a qualsiasi rete (IP VPN), una rete Ethernet punto o una connessione tra virtuale tramite un provider di integrazione applicativa in una struttura di posizione condivisa. Connessioni ExpressRoute va tramite Internet. In questo modo connessioni ExpressRoute per l'offerta più affidabilità, velocità, latenza inferiore e protezione più elevata rispetto alle connessioni tipiche tramite Internet.

Ulteriori informazioni:

- [Panoramica tecnica ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Gateway di rete virtuale

Gateway VPN, detto anche gateway Network virtuale di Azure, vengono utilizzati per inviare il traffico di rete tra reti virtuali e le posizioni in locale. Vengono inoltre utilizzati per inviare il traffico tra più reti virtuali all'interno di Azure (VNet-VNet).  Gateway VPN fornire connettività locale tra sicura tra Azure e infrastruttura.

Ulteriori informazioni:

- [Su gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Cenni preliminari sulla protezione di rete Azure](security-network-overview.md)

## <a name="privileged-identity-management"></a>Gestione delle identità privilegi

Può succedere che gli utenti devono eseguire operazioni con privilegi di Azure risorse o altre applicazioni SaaS. Spesso sono organizzazioni concedere l'accesso con privilegi permanente di Azure Active Directory (Azure Active Directory). In questo modo crescenti rischi di protezione per le risorse cloud ospitato organizzazioni non sono sufficientemente eseguire il monitoraggio delle operazioni con i privilegi di accesso tali utenti.
Inoltre, se un account utente con privilegi di accesso è compromessa, tale una violazione potrebbe avere un impatto la sicurezza cloud complessiva. Azure Active Directory privilegi di gestione delle identità consente di risolvere il rischio, ridurre il tempo di esposizione dei privilegi e aumentare la visibilità sull'uso.  

Privilegi di gestione delle identità introduce il concetto di un amministratore temporaneo per un ruolo o "just in time" accesso come amministratore, che è un utente deve completare il processo di attivazione per tale ruolo. Il processo di attivazione modifica l'assegnazione dell'utente a un ruolo di Azure Active Directory da periodo inattivo su attivo per un periodo specificato, ad esempio 8 ore.

Ulteriori informazioni:

- [Gestione delle identità privilegi di Azure Active Directory](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Guida introduttiva di Azure Active Directory privilegi di gestione delle identità](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Protezione dell'identità

La protezione dell'identità di Azure Active Directory (AD) fornisce una visualizzazione consolidata di attività di accesso sospetta e potenziali vulnerabilità per proteggere l'azienda. Protezione dell'identità rileva attività sospetta per gli utenti e dell'identità dei privilegi (amministratori), in base a segnali come attacchi, perdite delle credenziali e componenti aggiuntivi di accesso da postazioni familiarità e virus dispositivi.

Fornendo consigliati risoluzione di problemi e le notifiche, la protezione dell'identità consente di ridurre i rischi in tempo reale. Il calcolo gravità dei rischi utente e configurare i criteri basati sul rischio ne faciliti automaticamente per proteggere applicazione accedere da minacce future.

Ulteriori informazioni:

- [Protezione dell'identità di Azure Active Directory](../active-directory/active-directory-identityprotection.md)
- [Canale 9: Azure AD e Mostra identità: anteprima di protezione di identità](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Centro protezione

Azure Security Center consente di impedire, rilevare e rispondere a rischi e offre che maggiore visibilità e controllare i la protezione delle risorse di Azure. Che consente di gestire il monitoraggio e criteri di sicurezza integrata tra le proprie sottoscrizioni Azure, consente di rilevare minacce che in caso contrario non verrebbero rilevate e funziona con un insieme di soluzioni di protezione offre un ampio.

Centro protezione consente di ottimizzare e monitorare la protezione delle risorse di Azure da:

- Consente di definire i criteri per le risorse di Azure abbonamento in base alle esigenze di protezione della società e il tipo di applicazioni o riservatezza del contenuto dei dati in ciascuna sottoscrizione.
- Monitorare lo stato del macchine virtuali Azure, rete e le applicazioni.
- Fornire un elenco di avvisi di sicurezza priorità, inclusi gli avvisi di soluzioni dei partner integrate, oltre a rendere le informazioni che necessarie per analizzare rapidamente e suggerimenti su come risolvere un attacco.

Ulteriori informazioni:

- [Introduzione al Centro protezione di Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
