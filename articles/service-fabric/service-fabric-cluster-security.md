<properties
   pageTitle="Proteggere un cluster di servizio tessuti | Microsoft Azure"
   description="Vengono descritti gli scenari di sicurezza per un cluster di tessuti di servizio e le tecnologie diversi utilizzate per implementare questi scenari."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Scenari di protezione dell'infrastruttura di servizio cluster

Un cluster di servizio tessuti è una risorsa che si è proprietari. Cluster devono sempre essere protetto per impedire a utenti non autorizzati di connettersi al cluster, in particolare se dispone di carichi di lavoro di produzione in esecuzione su di esso. Sebbene sia possibile creare un cluster non protetto, eseguire in modo consentirà utenti anonimi di connettersi se vengono esposti i punti finali gestione a Internet. 

In questo articolo viene fornita una panoramica degli scenari di sicurezza per i cluster in esecuzione in Azure o autonoma e diverse tecnologie utilizzate per implementare questi scenari. I cluster sicurezza scenari seguenti:

- Sicurezza a nodi
- Sicurezza a nodo client
- Controllo dell'accesso basato sui ruoli (RBAC)

## <a name="node-to-node-security"></a>Sicurezza a nodi
Consente di proteggere le comunicazioni tra macchine virtuali o computer del cluster. In questo modo che solo i computer autorizzati al cluster possono partecipare hosting di applicazioni e servizi inclusi nei cluster.

![Diagramma di comunicazione per nodo][Node-to-Node]

Cluster che eseguono Azure o autonomo cluster che eseguono Windows è possibile usare [Certificato di sicurezza](https://msdn.microsoft.com/library/ff649801.aspx) o [Protezione di Windows](https://msdn.microsoft.com/library/ff649396.aspx) per i Server di Windows.
### <a name="node-to-node-certificate-security"></a>Sicurezza a nodi certificato
Servizio tessuti utilizza 509 server che specificano come parte delle configurazioni di tipo di nodo quando si crea un cluster. Alla fine di questo articolo viene fornita una rapida panoramica delle quali sono i certificati e come è possibile acquisire o crearli.

Certificato sicurezza è configurata durante la creazione di cluster tramite il portale di Azure, modelli di gestione di risorse Azure o un modello JSON autonomo. È possibile specificare un certificato principale e un certificato secondario facoltativo utilizzato per rollover certificato. I certificati primari e secondari specificati devono essere diversi da client di amministrazione e i certificati di sola lettura client specificate per [la protezione a nodo Client](#client-to-node-security).

Per Azure leggere informazioni su come configurare la protezione certificato in un cluster di [configurare un cluster utilizzando un modello di gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md) .

Per autonomo Windows Server leggere [sicura un cluster autonomo in Windows certificati x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Sicurezza di windows a nodi
Per autonomo Windows Server leggere [sicura un cluster autonomo in Windows la sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Sicurezza a nodo client
Autentica i client e protegge le comunicazioni tra un client e singoli nodi del cluster. Questo tipo di sicurezza autentica e protegge le comunicazioni client, che assicura che possono accedere solo gli utenti autorizzati cluster e le applicazioni distribuite nel cluster. Client identificati in modo univoco tramite le proprie credenziali di sicurezza di Windows o le proprie credenziali di sicurezza di certificato.

![Diagramma di comunicazione per nodo client][Client-to-Node]

Cluster che eseguono Azure o autonomo cluster che eseguono Windows è possibile usare [Certificato di sicurezza](https://msdn.microsoft.com/library/ff649801.aspx) o [Protezione di Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Protezione da client a nodo certificati
 Certificato client da nodo sicurezza è configurata durante la creazione del cluster tramite il portale di Azure, modelli di Manager delle risorse o un modello JSON autonoma specificando un certificato client amministratore e/o un certificato client utente.  L'amministratore client e degli utenti client certificati specificati devono essere diversi da quello i certificati principali e secondari specificate per [la protezione a nodi](#node-to-node-security).

Client che si connettono a cluster utilizzando il certificato di amministrazione avere accesso completo alle funzionalità di gestione.  Client che si connettono a cluster utilizzando il certificato client di sola lettura utente dispone di accesso in lettura solo per le funzionalità di gestione. In altre parole questi certificati vengono utilizzati per il controllo dell'accesso basi di ruoli (RBAC) descritto più avanti in questo articolo.

Per Azure leggere informazioni su come configurare la protezione certificato in un cluster di [configurare un cluster utilizzando un modello di gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md) .

Per autonomo Windows Server leggere [sicura un cluster autonomo in Windows certificati x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Sicurezza di Azure Active Directory (AAD) per nodo client su Azure
Cluster che eseguono Azure anche possibile proteggere l'accesso per gli endpoint di gestione con Azure Active Directory (AAD). Per informazioni su come creare gli elementi AAD necessari, sull'inserimento di dati durante la creazione di cluster e come connettersi a tali cluster in un secondo momento, vedere [configurare un cluster di base usando un modello di gestione risorse di Azure](service-fabric-cluster-creation-via-arm.md) .

## <a name="security-recommendations"></a>Suggerimenti per la sicurezza
Per i cluster di Azure, si consiglia di utilizzare protezione AAD per autenticare i client e certificati per la protezione a nodi.

Per autonomo Windows Server, è consigliabile utilizzare la sicurezza di Windows con gruppo gestire cluster account (GMA) se si dispone di Windows Server 2012 R2 e Active Directory. In caso contrario ancora utilizzare la sicurezza di Windows con un account di Windows.

## <a name="role-based-access-control-rbac"></a>Controllo dell'accesso basato sui ruoli (RBAC)
Controllo dell'accesso consente all'amministratore cluster limitare l'accesso a determinate operazioni cluster per diversi gruppi di utenti, rendere più sicuri cluster. Due tipi di controllo di accesso diverso sono supportati per i client la connessione a un cluster: ruoli di amministratore e utente.

Gli amministratori hanno accesso completo alle funzionalità di gestione (incluse funzionalità di lettura/scrittura). Gli utenti, per impostazione predefinita, hanno solo accesso in lettura alla funzionalità di gestione (ad esempio le funzionalità di query) e la possibilità di risolvere applicazioni e servizi.

Specificare i ruoli di amministratore e utente client al momento della creazione di cluster fornendo identità separate (certificati, AAD e così via) per ognuno. Per ulteriori informazioni sulle impostazioni di controllo di accesso predefinito e su come modificare le impostazioni predefinite, vedere [il controllo di accesso per i client tessuti servizio basato sui ruoli](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Certificati x. 509 e tessuti di servizio
Certificati digitali x. 509 in genere utilizzati per eseguire l'autenticazione client e server e la crittografia e firma digitale dei messaggi. Per ulteriori informazioni su questi certificati, vedere [lavorare con i certificati](http://msdn.microsoft.com/library/ms731899.aspx).

Alcuni aspetti importanti da considerare:

- I certificati usati in cluster che eseguono carichi di lavoro di produzione devono creati usando un servizio di certificato di Windows Server configurato correttamente o ottenuti da un' approvata [Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority).
- Non usare mai qualsiasi temporaneo o certificati di prova di produzione creati con strumenti come MakeCert.exe.
- È possibile usare un certificato autofirmato, ma dovrebbe eseguire questa operazione solo per i cluster di test e non in produzione.

### <a name="server-x509-certificates"></a>Certificati x. 509 server

I certificati server hanno il compito principale di autenticazione di un server (nodo) ai client o l'autenticazione di un server (nodo) a un server (nodo). Uno dei controlli iniziali quando un client o nodo autentica un nodo è controllare il valore di nome comune nel campo oggetto. Questo nome comune o uno dei nomi alternativi oggetto i certificati deve essere presenta nell'elenco dei nomi comuni consentiti.

L'articolo seguente viene descritto come generare i certificati con nomi alternativi oggetto (SAN): [come aggiungere un nome alternativo soggetto a un certificato LDAP protetto](http://support.microsoft.com/kb/931351).

Nel campo oggetto può contenere più valori, ognuno preceduto da un'inizializzazione per indicare il tipo di valore. In genere, l'inizializzazione riguarda "CN" nome comune; ad esempio, "CN = www.contoso.com". È inoltre possibile che sia vuoto il campo oggetto. Se il campo nome alternativo soggetto facoltativo non è vuoto, deve contenere il nome del certificato comune e una voce per il nome alternativo. Questi sono stati immessi come valori di nome DNS.

Il valore del campo scopi del certificato deve includere un valore appropriato, ad esempio "Autenticazione Server" o "Client".

### <a name="client-x509-certificates"></a>Certificati x. 509 di client

I certificati client non sono in genere rilasciati da un'autorità di certificazione di terze parti. Se, tuttavia, l'archivio personale del percorso dell'utente corrente contiene in genere i certificati client inseriti da un'autorità radice, con lo scopo di "Autenticazione Client". Il client può utilizzare tale certificato quando è necessaria l'autenticazione reciproca.

>[AZURE.NOTE] Tutte le operazioni di gestione in un cluster di servizio tessuti richiedono i certificati server. I certificati client non è possibile utilizzare per la gestione.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Passaggi successivi

In questo articolo fornisce informazioni sulla protezione cluster. Successivamente, [creare un cluster di Azure utilizzando un modello di Manager delle risorse](service-fabric-cluster-creation-via-arm.md) o tramite il [portale di Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
