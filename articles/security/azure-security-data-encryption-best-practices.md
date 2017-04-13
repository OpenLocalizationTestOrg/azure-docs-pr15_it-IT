<properties
   pageTitle="Procedure consigliate per la protezione dei dati e la crittografia | Microsoft Azure"
   description="In questo articolo fornisce un set di procedure consigliate per la protezione dei dati e funzionalità Azure utilizzando la crittografia integrate."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yuridio"/>

#<a name="azure-data-security-and-encryption-best-practices"></a>Procedure consigliate per la protezione dei dati di Azure e della crittografia

Uno dei tasti per la protezione dei dati nel cloud è contabilità per i possibili stati in cui i dati possono verificarsi e quali controlli sono disponibili per tale stato. Allo scopo di dati di Azure protezione e crittografia consigliate i consigli saranno intorno stati dei dati seguenti:

- A riposo: Sono inclusi gli oggetti di archiviazione, contenitori e tipi presenti in modo statico supporti fisici, essere magnetica oppure disco ottico tutte le informazioni.

- In transito: Quando dati vengono trasferiti tra componenti, posizioni o programmi, ad esempio tramite la rete, su un bus di servizio (da locale nel cloud e viceversa, incluse le connessioni ibrida, ad esempio ExpressRoute), o durante il processo di ingresso/uscita, si ritiene di come in animazione.

In questo articolo verranno illustrati una raccolta di dati di Azure protezione e crittografia consigliate. Queste procedure consigliate sono derivate dai esperienza con la protezione dei dati di Azure e la crittografia e le esperienze dei clienti come se stessi.

Per ogni ottimale, spiegheremo:

- Che cos'è la procedura consigliata
- Perché si desidera attivare la procedura consigliata
- Cosa può essere il risultato se non si riesce ad attivare la procedura consigliata
- Possibili soluzioni alternative per la procedura consigliata
- Come è possibile informazioni su come abilitare la procedura consigliata

In questo articolo la protezione dei dati di Azure e la crittografia procedure consigliate si basa su un parere il consenso e set di caratteristiche e funzionalità di piattaforma Azure in cui si trovano in fase di che questo articolo è stato scritto. Selezionare una variante e le tecnologie cambiano nel tempo e in questo articolo verrà aggiornato regolarmente per riflettere le modifiche.

Dati di Azure protezione e crittografia procedure consigliate descritte in questo articolo includono:

- Applicare l'autenticazione a più fattori
- Controllo di accesso (RBAC) basato sui ruoli di utilizzo
- Crittografare macchine virtuali di Azure
- Usare i modelli di protezione hardware
- Gestire con workstation protette
- Abilitare la crittografia dei dati SQL
- Proteggere i dati in transito
- Applicare la crittografia dei dati a livello di file


## <a name="enforce-multi-factor-authentication"></a>Applicare l'autenticazione a più fattori

Il primo passaggio per l'accesso ai dati e controllo in Microsoft Azure è possibile autenticare l'utente. [Autenticazione a più fattori Azure (MFA)](../multi-factor-authentication/multi-factor-authentication.md) è un metodo di verifica dell'identità utente tramite un metodo diverso solo un nome utente e la password. Questo autenticazione metodo consente di proteggere l'accesso ai dati e alle applicazioni durante la riunione richiesta utente per un semplice processo.

Abilitando Azure MFA per gli utenti, si sta aggiungendo un secondo livello di sicurezza per gli accessi utente e le transazioni. In questo caso, una transazione potrebbe accedere un documento che si trova in un file server o il contestuale. Consente anche di Azure MFA IT per ridurre la probabilità che credenziali compromessa avranno accesso ai dati dell'organizzazione.

Ad esempio: se si applica Azure MFA per gli utenti e configurarlo per l'utilizzo di un messaggio di testo o una chiamata telefonica come verifica, in caso di violazione delle credenziali dell'utente, il pirata informatico non sarà possibile accedere a qualsiasi risorsa poiché non sarà possibile accedere al telefono dell'utente. Le organizzazioni che non aggiungono questo livello aggiuntivo di protezione dell'identità sono più soggetti gli attacchi furto delle credenziali, che potrebbero rimandare a violazione di dati.

Un'alternativa per le organizzazioni che vogliono mantenere i controllo di autenticazione in locale consiste nell'usare [Server di autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication-get-started-server.md), detti anche MFA locale. Con questo metodo è ancora sarà possibile applicare l'autenticazione a più fattori, e mantenendo al contempo il MFA server locale.

Per ulteriori informazioni su Azure MFA, leggere l'articolo [Guida introduttiva a autenticazione a più fattori Azure nel cloud](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

## <a name="use-role-based-access-control-rbac"></a>Controllo di accesso (RBAC) basato sui ruoli di utilizzo
Limitare l'accesso in base a principi di protezione [utili](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege) . Questo è fondamentale per le organizzazioni che vogliono applicare i criteri di sicurezza per l'accesso ai dati. Azure basato sui ruoli accesso controllo (RBAC) può essere utilizzato per assegnare autorizzazioni a utenti, gruppi e le applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere un abbonamento, un gruppo di risorse o una singola risorsa.

È possibile sfruttare [ruoli RBAC incorporati](../active-directory/role-based-access-built-in-roles.md) in Azure per assegnare i privilegi agli utenti. Utilizzare *Lo spazio di archiviazione per i collaboratori Account* per gli operatori cloud che devono gestire gli account di archiviazione e ruolo di *Collaboratore di Account classica lo spazio di archiviazione* per gestire gli account di archiviazione classica. Per gli operatori cloud che devono essere gestiti macchine virtuali e account di archiviazione, è consigliabile aggiungerli al ruolo di *Collaboratore macchina virtuale* .

Le organizzazioni che non si applicano il controllo di accesso di dati, sfruttando funzionalità, ad esempio RBAC potrebbero dato più privilegi del necessario per gli utenti. Questo può comportare violazione di dati mediante la ricezione di alcuni utenti hanno accesso ai dati che non hanno nella prima posizione.

Si può acquisiscono informazioni sul RBAC Azure, leggere l'articolo [Controllo dell'accesso Azure Role-Based](../active-directory/role-based-access-control-configure.md).

## <a name="encrypt-azure-virtual-machines"></a>Crittografare macchine virtuali di Azure
Per molte organizzazioni, [la crittografia dei dati inattivi](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) è obbligatoria verso la privacy dei dati, conformità e sovranità dei dati. Crittografia disco Azure consente agli amministratori IT crittografare dischi di Windows e Linux IaaS virtuale macchina. Crittografia disco Azure utilizza la funzionalità di BitLocker standard di settore di Windows e la funzionalità di crittografia di data mining di Linux per fornire la crittografia del volume per il sistema operativo e dischi di dati.

È possibile sfruttare la crittografia disco Azure per proteggere e proteggere i dati per soddisfare i requisiti di conformità e la sicurezza dell'organizzazione. Organizzazioni anche necessario prendere in considerazione Usa la crittografia per ridurre i rischi l'accesso ai dati correlati a non autorizzato. È anche consigliabile crittografare unità prima di scrivere dati riservati.

Assicurarsi che crittografare il Virtual Machine di dati e il volume di avvio per proteggere i dati resto nell'account di archiviazione Azure. Proteggere le chiavi di crittografia e informazioni riservate sfruttando [Azure chiave archivio](../key-vault/key-vault-whatis.md).

Per i server di Windows in locale, prendere in considerazione la crittografia seguente procedure consigliate:

- Utilizzare [BitLocker](https://technet.microsoft.com/library/dn306081.aspx) per la crittografia dei dati
- Archiviare le informazioni di ripristino di dominio Active Directory.
- Se esiste un problema che chiavi BitLocker compromessa, è consigliabile che è formattare l'unità per rimuovere tutte le istanze dei metadati BitLocker dall'unità o decrittografare e crittografare nuovamente l'intera unità.

Le organizzazioni che non si applicano la crittografia dei dati sono maggiormente esposti a problemi di integrità dei dati, ad esempio utenti malintenzionati o non autorizzati furto di dati e compromesso gli account di accesso non autorizzato ai dati in formato non crittografato. Oltre a tali rischi, società che deve essere conforme alle normative del settore, deve provare che siano accurati e Usa i controlli di protezione corretto per migliorare la protezione dei dati.

Sono disponibili ulteriori informazioni sulla crittografia disco Azure, leggere l'articolo [Azure disco crittografia per Windows e macchine virtuali IaaS Linux](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Utilizzare i moduli di protezione Hardware

Soluzioni di crittografia utilizzano tasti segreti per crittografare i dati. Di conseguenza, è fondamentale che questi tasti sono archiviati in modo sicuro. Gestione delle chiavi diventa parte integrante della protezione dei dati, dal momento che verranno utilizzata per la memorizzazione di chiavi segrete che vengono utilizzate per crittografare i dati.

Crittografia disco Azure utilizza [Azure chiave archivio](https://azure.microsoft.com/services/key-vault/) consentono di controllare e gestire le chiavi di crittografia disco e informazioni riservate nel proprio abbonamento archivio chiave, assicurandosi che tutti i dati nei dischi macchina virtuale vengono crittografati inattivi in archiviazione Azure. È necessario utilizzare Azure chiave archivio per controllare le chiavi e l'uso dei criteri.

Sono numerosi i rischi inerenti correlati che i controlli di protezione appropriate per proteggere i tasti segreti utilizzato per crittografare i dati. Se gli utenti malintenzionati può accedere ai tasti segreti, saranno grado di decrittografare i dati e potenzialmente dispone dell'accesso alle informazioni riservate.

Altre informazioni sulle indicazioni generali sulla gestione dei certificati in Azure, leggere l'articolo [la gestione dei certificati in Azure: accorgimenti e gli errori](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Per ulteriori informazioni su Azure chiave archivio, leggere [Guida introduttiva di Azure chiave archivio](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Gestire con workstation protette

Poiché la maggior parte degli attacchi destinazione degli utenti finali, l'endpoint viene convertito in uno dei principali punti di attacco. Se un pirata informatico compromettere il punto finale, è possibile sfruttare le credenziali dell'utente per accedere ai dati dell'organizzazione. Maggior parte degli attacchi endpoint sono in grado di sfruttare i vantaggi del fatto che gli utenti finali siano amministratori delle rispettive postazioni locale.

È possibile ridurre i rischi utilizzando una workstation gestione protetta. È consigliabile utilizzare un [Privilegi accesso workstation (IMPRONTE)](https://technet.microsoft.com/library/mt634654.aspx) per ridurre la possibilità di attacchi nelle workstation. Queste workstation gestione protetta consente di ridurre alcuni di questi attacchi aiuta a mantenere i dati più sicuri. Assicurarsi di usare IMPRONTE per rafforzare la protezione e bloccare le workstation. Si tratta di un passaggio importante per fornire garanzie maggiore sicurezza per gli account riservati, attività e protezione dei dati.

Mancanza di endpoint protection può inserire i dati a rischio, assicurarsi di applicare criteri di protezione da tutti i dispositivi che consentono di utilizzare dati, indipendentemente dalla posizione dati (cloud o locale).

È possibile ottenere che altre informazioni su privilegi accede a di lavoro, leggere l'articolo [Protezione dell'accesso ai privilegi](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Abilitare la crittografia dei dati SQL

[Crittografia dati trasparente Database SQL Azure](https://msdn.microsoft.com/library/dn948096.aspx) (TDE) consente di evitare il rischio di attività dannoso eseguendo in tempo reale crittografia e decrittografia del database, backup associati e file di registro delle transazioni inattivi senza apportare modifiche all'applicazione.  TDE Crittografa lo spazio di archiviazione di un intero database utilizzando una chiave simmetrica denominata la chiave di crittografia del database.

Anche quando lo spazio di archiviazione intera è crittografato, è molto importante crittografare il database stesso. Si tratta di un'implementazione di difesa approccio per la protezione dei dati. Se si utilizza [Il Database di SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) e si desidera proteggere i dati sensibili, ad esempio carta di credito o numeri di previdenza sociale, è possibile crittografare i database con la crittografia AES 256 bit di 140-2 convalidato FIPS che soddisfi i requisiti di molti standard (ad esempio, HIPAA, PCI).

È importante tenere presente che i file correlati all' [interno del pool di buffer](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) non sono crittografati quando un database crittografato utilizzando TDE. È necessario utilizzare strumenti per la crittografia a livello file system come BitLocker o la [Crittografia File System](https://technet.microsoft.com/library/cc700811.aspx) (crittografia file System) per BPE i file correlati.

Poiché un utente autorizzato ad esempio un amministratore della sicurezza o un amministratore del database può accedere ai dati anche se il database crittografato con TDE, è necessario seguire anche i suggerimenti riportati di seguito:

- Autenticazione di SQL Server a livello di database
- Autenticazione di Azure Active Directory utilizzando ruoli RBAC
- Gli utenti e le applicazioni devono essere utilizzate account separati per l'autenticazione. In questo modo è possibile limitare le autorizzazioni a utenti e applicazioni e ridurre i rischi di attività dannoso
- Implementare la protezione a livello di database utilizzando i ruoli del database (ad esempio db_datareader o db_datawriter) o è possibile creare ruoli personalizzati per l'applicazione di concedere autorizzazioni esplicite agli oggetti di database selezionate

Le organizzazioni che non si usano la crittografia a livello di database possono essere più sensibili per attacchi che potrebbero compromettere dati che si trovano nei database SQL.

Sono disponibili ulteriori informazioni sulla crittografia SQL TDE, leggere l'articolo [Trasparente la crittografia dei dati con il Database di SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Proteggere i dati in transito

Protezione dei dati in transito deve essere essenziali parte di una strategia di protezione dei dati. Dal momento che i dati verranno spostate avanti e indietro più posizioni, in generale è consigliabile è di usare sempre i protocolli SSL/TLS per lo scambio di dati in posizioni diverse. In alcuni casi può essere necessario isolare il canale di comunicazione intera tra il locali e cloud infrastruttura tramite una rete privata virtuale (VPN).

Per spostarsi tra l'infrastruttura di locale e Azure i dati, è necessario prendere in considerazione garanzie appropriate, ad esempio HTTPS o VPN.

Per le organizzazioni che è necessario per proteggere l'accesso da più workstation trovare locale in Azure, usare [Azure VPN da sito](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Per le organizzazioni con un accesso sicuro da una workstation trova locale alle Azure, è necessario utilizzare [punto-sito VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Collegamento di dati più grandi set possono essere spostati in una rete WAN ad alta velocità dedicata, ad esempio [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Se si sceglie di utilizzare ExpressRoute, è inoltre possibile crittografare i dati a livello di applicazione tramite [SSL/TLS](https://support.microsoft.com/kb/257591) o altri protocolli per aggiungere la protezione.

Se si interagisce con l'archiviazione Azure tramite il portale di Azure, tutte le transazioni verificano tramite HTTPS. [API REST di spazio di archiviazione](https://msdn.microsoft.com/library/azure/dd179355.aspx) su HTTPS possono anche essere utilizzata per interagire con [Lo spazio di archiviazione di Azure](https://azure.microsoft.com/services/storage/) e [Database di SQL Azure](https://azure.microsoft.com/services/sql-database/).

Le organizzazioni che non riescono a proteggere i dati durante il transito sono più sensibili per [attacchi uomo diretti](https://technet.microsoft.com/library/gg195821.aspx), [intercettazione](https://technet.microsoft.com/library/gg195641.aspx) e intercettazione di sessioni. Questi attacchi possono essere il primo passaggio per l'accesso ai dati riservati.

Sono disponibili ulteriori informazioni sulle opzioni di Azure VPN, leggere l'articolo [pianificazione e progettazione per Gateway VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Applicare la crittografia dei dati a livello di file

Un altro livello di protezione che può aumentare il livello di sicurezza per i dati di crittografia dei file, indipendentemente dal percorso del file.

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) utilizza i criteri di crittografia, identità e l'autorizzazione per proteggere i file e un messaggio di posta elettronica. Azure RMS funziona su più dispositivi, telefoni e Tablet PC per la protezione all'interno dell'organizzazione e all'esterno dell'organizzazione. Questa funzionalità è possibile perché Azure RMS aggiunge un livello di protezione rimane con i dati, anche quando lascia limiti dell'organizzazione.

Quando si utilizza Azure RMS per proteggere i file, si usa la crittografia standard di settore con supporto completo di [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Quando si utilizzano Azure RMS per la protezione dei dati, è necessario la certezza che la protezione viene mantenuto il file, anche se è stato copiato allo spazio di archiviazione che non si trova il controllo di IT, ad esempio un servizio di archiviazione cloud. Lo stesso avviene per i file condivisi tramite posta elettronica, il file è protetto come allegato a un messaggio di posta elettronica, con le istruzioni su come aprire l'allegato protetto.

Durante la pianificazione di adozione di Azure RMS è consigliabile le operazioni seguenti:

- Installare [RMS condivisione app](https://technet.microsoft.com/library/dn339006.aspx). Questa app si integra con Office applicazioni per l'installazione di Office un componente aggiuntivo in modo che gli utenti possono facilmente proteggere i file direttamente.
- Configurare le applicazioni e servizi per supportare Azure RMS
- Creare [modelli personalizzati](https://technet.microsoft.com/library/dn642472.aspx) che riflettono le esigenze aziendali. Ad esempio: un modello di dati segreti superiore che devono essere applicati in tutte le prime segreto relativi messaggi di posta elettronica.

Le organizzazioni che sono deboli sulla protezione di [classificazione di dati](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) e file possono essere più soggetti a perdita di dati. Senza protezione di file corretto, le organizzazioni non sarà possibile ottenere informazioni aziendali, verificare la presenza di abusi e impedire l'accesso non autorizzato ai file.

Ulteriori informazioni su Azure RMS informazioni, leggere l'articolo [Guida introduttiva di Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
