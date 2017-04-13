<properties
   pageTitle="Panoramica della protezione di archivio di dati Lake | Microsoft Azure"
   description="Capire come archivio Lake Azure in un archivio di dati più sicuro"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Sicurezza nell'archivio Lake dati di Azure

Molte organizzazioni sono a trarre vantaggio da analitica di dati per informazioni dettagliate sui business agli utenti per prendere decisioni appropriate. Un'organizzazione potrebbe avere un ambiente complesso e regolamentato, con un numero crescente di utenti diversi. È fondamentale per un'organizzazione per assicurarsi che essenziali i dati archiviati in modo più sicuro, con il livello di accesso ai singoli utenti corretto. Archivio Lake dati di Azure è progettato per consentire di soddisfare i requisiti di sicurezza. In questo articolo viene illustrato sulle funzionalità di sicurezza di archivio di dati Lake, tra cui:

* Autenticazione
* Autorizzazione
* Isolamento della rete
* Protezione dei dati
* Il controllo

## <a name="authentication-and-identity-management"></a>Gestione delle identità e autenticazione

Autenticazione è il processo per il quale viene verificato identità dell'utente quando l'utente interagisce con dati Lake Store o con qualsiasi altro servizio che si connette ai dati Lake Store. Per la gestione delle identità e l'autenticazione, l'archivio dati Lake utilizza [Azure Active Directory](../active-directory/active-directory-whatis.md), una soluzione di cloud gestione di access che semplifica la gestione di utenti e gruppi e identità completa.

Ogni abbonamento Azure può essere associato a un'istanza di Azure Active Directory. Solo gli utenti e identità di servizio definiti nel servizio di Azure Active Directory possono accedere all'account di archivio di dati Lake, tramite il portale di Azure, gli strumenti della riga di comando, o tramite applicazioni client organizzazione compila utilizzando Azure dati Lake archivio SDK. Vantaggi dell'utilizzo di Azure Active Directory come meccanismo di controllo accesso centralizzato sono:

* Semplificare la gestione del ciclo di vita delle identità. L'identità di un utente o un servizio (un'identità dell'entità servizio) può essere creato rapidamente e rapidamente revocato semplicemente eliminando o la disattivazione dell'account nella directory.

* Autenticazione a più fattori. [Autenticazione a più fattori](../multi-factor-authentication/multi-factor-authentication.md) fornisce un ulteriore livello di sicurezza per gli accessi utente e le transazioni.

* Autenticazione da qualsiasi client tramite un protocollo aperto standard, ad esempio OAuth o OpenID.

* Federazione con i servizi directory dell'organizzazione e provider di identità cloud.

## <a name="authorization-and-access-control"></a>Controllare le autorizzazioni e l'accesso

Dopo aver Azure Active Directory autentica un utente in modo che l'utente può accedere archivio Lake dati di Azure, controlli di autorizzazione accedere le autorizzazioni per dati Lake archivio. Archivio dati Lake separa autorizzazione per le attività correlate account e correlato ai dati nel modo seguente:

* [Controllo dell'accesso basato sui ruoli](../active-directory/role-based-access-control-what-is.md) (RBAC) fornita da Azure per la gestione di account
* POSIX ACL per accedere ai dati nell'archivio


### <a name="rbac-for-account-management"></a>RBAC per la gestione di account

Quattro ruoli di base sono definiti per l'archivio di dati Lake per impostazione predefinita. I ruoli consentono diverse operazioni su un account di archivio Lake dati tramite il portale di Azure, i cmdlet di PowerShell e le API REST. Ruoli del proprietario e collaboratori possono eseguire una varietà di funzioni di amministrazione dell'account. È possibile assegnare il ruolo di lettore agli utenti solo interagiscono con i dati.

![Ruoli RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Ruoli RBAC")

Si noti che, anche se i ruoli assegnati per la gestione degli account, alcuni ruoli influenzano l'accesso ai dati. È necessario utilizzare ACL per controllare l'accesso alle operazioni che un utente può eseguire nel file system. Nella tabella seguente mostra un riepilogo dei diritti di accesso di dati per i ruoli predefiniti e rights management.

| Ruoli                    | Rights Management               | Diritti di accesso di dati | Spiegazione |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Nessun ruolo         | Nessuno                            | All'interno disciplinata dai ACL    | L'utente non è possibile usare il portale di Azure e i cmdlet di PowerShell Azure per esplorare dati Lake Store. L'utente può usare solo gli strumenti della riga di comando.
| Proprietario  | Tutti  | Tutti  | Il ruolo di proprietario è un utente avanzato. Questo ruolo può gestire tutti gli elementi e dispone dell'accesso completo ai dati.
| Utilità per la lettura   | Sola lettura  | All'interno disciplinata dai ACL    | Il ruolo di lettore può visualizzare tutti gli elementi relativi alla gestione di account, ad esempio che utente è assegnato il ruolo. Ruolo di lettore non è possibile apportare le modifiche.   |
| Per i collaboratori              | Tutto eccetto Aggiungi e Rimuovi ruoli | All'interno disciplinata dai ACL    | Il ruolo di collaboratore può gestire alcuni aspetti di un account, ad esempio distribuzioni e la creazione e gestione degli avvisi. Il ruolo di collaboratore non è possibile aggiungere o rimuovere i ruoli.
| Amministratore di accesso utente | Aggiungere e rimuovere i ruoli            | All'interno disciplinata dai ACL    | Il ruolo di amministratore di accesso di utenti può gestire l'accesso agli account degli utenti. |

Per ulteriori informazioni, vedere [assegnare utenti o gruppi di sicurezza per gli account di archivio di dati Lake](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Uso degli ACL per le operazioni nel file System

Archivio Lake in un file system gerarchico ad esempio Hadoop Distributed File System (HDFS) e supporta [ACL POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controlli di lettura (r), scrittura (w) ed eseguire (le autorizzazioni alle risorse per il ruolo di proprietario, per il gruppo di proprietari e per altri utenti e gruppi x). In dati Lake archivio pubblico Preview (la versione corrente), ACL abilitati per la cartella principale, le sottocartelle e singoli file. Gli elenchi di controllo applicabili nella cartella radice applicate anche a tutti i file e le cartelle figlio.

È consigliabile definire ACL per più utenti con i [gruppi di sicurezza](../active-directory/active-directory-accessmanagement-manage-groups.md). Aggiungere utenti a un gruppo di sicurezza e quindi assegnare gli elenchi di controllo per un file o una cartella a quel gruppo di sicurezza. Questo è utile quando si desidera fornire accesso personalizzato, poiché sono limitate all'aggiunta di un massimo di nove voci per l'accesso personalizzato. Per ulteriori informazioni su come migliorare la protezione dei dati memorizzate nell'archivio Lake dati mediante i gruppi di sicurezza di Azure Active Directory, vedere [assegnare gli utenti o gruppo di sicurezza come ACL nel file System archivio Lake dati di Azure](data-lake-store-secure-data.md#filepermissions).

![Accesso standard e personalizzato di elenco] (./media/data-lake-store-security-overview/adl.acl.2.png "Accesso standard e personalizzato di elenco")

## <a name="network-isolation"></a>Isolamento della rete

Usare dati Lake archivio consentono di controllare l'accesso all'archivio dati livello di rete. È possibile stabilire firewall e definire un intervallo di indirizzi IP per i client attendibili. Con un indirizzo IP, solo i clienti che hanno un indirizzo IP all'interno dell'intervallo definito possono connettersi all'archivio Lake dati.

![Impostazioni del firewall e accesso IP] (./media/data-lake-store-security-overview/firewall-ip-access.png "Impostazioni del firewall e l'indirizzo IP")

## <a name="data-protection"></a>Protezione dei dati

Organizzazioni desiderano assicurarsi che i dati di importanza cruciale siano per tutta la durata ciclo di vita. Per i dati durante il transito, archivio dati Lake il protocollo standard di settore protezione TLS (Transport Layer) per proteggere i dati che consente di spostarsi tra un client e archivio Lake dati.

Protezione dei dati inattivi dati saranno disponibile nelle versioni future.

## <a name="auditing-and-diagnostic-logs"></a>Log di controllo e diagnostico

È possibile utilizzare i registri di controllo o diagnostici, a seconda che si sta cercando dei registri per relativi a Gestione attività o attività correlate ai dati.

*  Attività in materia di gestione utilizzano API di gestione risorse Azure e vengono resi disponibili nel portale di Azure tramite i log di controllo.
*  Attività correlate ai dati utilizzare le API REST WebHDFS e vengono resi disponibili nel portale di Azure tramite registri diagnostici.

### <a name="auditing-logs"></a>Registri

Per conformità alle normative, un'organizzazione potrebbe richiedere adeguata audit trail se è necessario approfondire problemi specifici. Archivio dati Lake ha monitoraggio incorporato e controllo Registra tutte le attività di gestione di account.

Per gli audit trail gestione di account, visualizzare e scegliere le colonne che si desidera registrare. È anche possibile esportare i log di controllo allo spazio di archiviazione Azure.

![Log di controllo] (./media/data-lake-store-security-overview/audit-logs.png "Log di controllo")

### <a name="diagnostic-logs"></a>Registri diagnostici

È possibile impostare l'accesso ai dati audit trail nel portale di Azure (nelle impostazioni di diagnostica) e creare un account di archiviazione Blob Azure in cui sono archiviati i log.

![Registri diagnostici] (./media/data-lake-store-security-overview/diagnostic-logs.png "Registri diagnostici")

Dopo aver configurato le impostazioni di diagnostiche, è possibile visualizzare i registri scheda **Log diagnostici** .

Per ulteriori informazioni sull'utilizzo dei registri diagnostici con Azure dati Lake Store, vedere [log diagnostici di Access per archivio Lake dati](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Riepilogo

Clienti aziendali richiedono una piattaforma cloud analitica dati sicuro e facile da usare. Archivio Lake dati di Azure è progettata per aiutare indirizzo che questi requisiti tramite la gestione delle identità e l'autenticazione tramite l'integrazione di Azure Active Directory, autorizzazione basata su ACL, isolamento della rete, la crittografia dei dati in transito e posizionare (disponibile a breve in futuro) e il controllo.

Se si desidera visualizzare nuove caratteristiche di archivio di dati Lake, inviare feedback nel [forum dati Lake archivio Uservoicehttp](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Vedere anche

- [Panoramica dell'archivio Lake dati di Azure](data-lake-store-overview.md)
- [Guida introduttiva a Lake archivio](data-lake-store-get-started-portal.md)
- [Proteggere i dati nell'archivio dati Lake](data-lake-store-secure-data.md)
