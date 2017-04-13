<properties
    pageTitle="Servizi di dominio Active Directory Azure: Confrontare Azure Active Directory dominio servizi a controller di dominio formattazione | Microsoft Azure"
    description="Confronto tra servizi di dominio di Azure Active Directory a controller di formattazione"
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
    ms.date="10/01/2016"
    ms.author="maheshu"/>

# <a name="how-to-decide-if-azure-ad-domain-services-is-right-for-your-use-case"></a>Come decidere se servizi di dominio Active Directory di Azure più adatto per il caso di utilizzo
Azure servizi di dominio Active Directory consente di distribuire i carichi di lavoro di servizi di infrastruttura di Windows Azure, senza dover preoccuparsi di manutenzione dell'infrastruttura di identità. Questo servizio gestito è diverso da una distribuzione di Windows Server Active Directory più comuni per la distribuzione di amministrare autonomamente. Il servizio è progettato per facilitare la distribuzione, il monitoraggio dell'integrità automatizzato e monitoraggio e un'infrastruttura di identità semplice per il cloud. Si sta evoluzione il servizio per aggiungere il supporto per gli scenari comuni.

Decidere se usare i servizi di dominio Active Directory Azure o aggiornarsi e gestire la propria infrastruttura di Active Directory (fai da te) in Azure:

- Visualizzare l'elenco delle [funzionalità offerte da servizi di dominio Active Directory Azure](active-directory-ds-features.md).

- Esaminare comuni [scenari di distribuzione per i servizi di dominio di Azure Active Directory](active-directory-ds-scenarios.md).

- Infine, [confrontare i servizi di dominio Active Directory di Azure un'opzione di Active Directory fai da te](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## <a name="compare-azure-ad-domain-services-to-diy-ad-domain-in-azure"></a>Confronto tra servizi di dominio Active Directory di Azure al dominio di Active Directory formattazione in Azure
Nella tabella seguente consente di decidere tra l'utilizzo dei servizi di dominio Active Directory Azure e gestire l'infrastruttura di Active Directory in Azure.

|**Caratteristica**|**Servizi di dominio Active Directory Azure**|**Annuncio "Fai da te" in macchine virtuali di Azure**|
|---|:---:|:---:|
|[**Servizio gestito**](active-directory-ds-comparison.md#managed-service)|**& #x 2713;**|**& #x 2715;**|
|[**Distribuzioni protette**](active-directory-ds-comparison.md#secure-deployments)|**& #x 2713;**|Amministratore deve proteggere la distribuzione.|
|[**Server DNS**](active-directory-ds-comparison.md#dns-server)|**& #x 2713;** (servizio gestito)|**& #x 2713;**|
|[**Privilegi di amministratore di dominio o dell'organizzazione**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|**& #x 2715;**|**& #x 2713;**|
|[**Aggiunta a un dominio**](active-directory-ds-comparison.md#domain-join)|**& #x 2713;**|**& #x 2713;**|
|[**Autenticazione di dominio tramite NTLM e Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|**& #x 2713;**|**& #x 2713;**|
|[**Struttura di unità Organizzativa personalizzata**](active-directory-ds-comparison.md#custom-ou-structure)|**& #x 2713;**|**& #x 2713;**|
|[**Estensioni dello schema**](active-directory-ds-comparison.md#schema-extensions)|**& #x 2715;**|**& #x 2713;**|
|[**Considera attendibile il dominio di Active Directory o della foresta**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|**& #x 2715;**|**& #x 2713;**|
|[**Lettura LDAP**](active-directory-ds-comparison.md#ldap-read)|**& #x 2713;**|**& #x 2713;**|
|[**LDAP sicuro (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|**& #x 2713;**|**& #x 2713;**|
|[**Scrittura LDAP**](active-directory-ds-comparison.md#ldap-write)|**& #x 2715;**|**& #x 2713;**|
|[**Criteri di gruppo**](active-directory-ds-comparison.md#group-policy)|Semplice|Completa|
|[**Distribuzioni distribuito geografico**](active-directory-ds-comparison.md#geo-dispersed-deployments)|**& #x 2715;**|**& #x 2713;**|


#### <a name="managed-service"></a>Servizio gestito
Azure domini di servizi di dominio Active Directory vengono gestiti da Microsoft. Non occorre preoccuparsi di patch, aggiornamenti, monitoraggio, l'esecuzione di backup e garantire la disponibilità del dominio. Queste attività di gestione sono disponibili come servizio da Microsoft Azure per i propri domini gestiti.

#### <a name="secure-deployments"></a>Distribuzioni protette
Il dominio gestito in modo sicuro è bloccato in base alle procedure consigliate Microsoft per le distribuzioni di Active Directory. Queste procedure consigliate derivano dall'esperienza di progettazione e il supporto di distribuzioni di Active Directory di pluriennale del team di prodotto Active Directory. Per distribuzioni fai da te, è necessario eseguire operazioni di distribuzione per bloccare/sicura verso il basso la distribuzione.

#### <a name="dns-server"></a>Server DNS
Un dominio gestito di servizi di dominio Active Directory Azure include gestito provider di servizi DNS. I membri del gruppo 'AAD controller di dominio Administrators' possono gestire DNS nel dominio gestito. Membri di questo gruppo figurano tutti i privilegi di amministrazione di DNS per il dominio gestito. Gestione del DNS può essere eseguita con 'DNS console di amministrazione' inclusa nel pacchetto remoto Server strumenti di amministrazione.

#### <a name="domain-or-enterprise-administrator-privileges"></a>Privilegi del dominio o amministratore dell'organizzazione
Questi privilegi elevati non sono disponibili su un dominio gestito AAD-DS. Le applicazioni che richiedono questi privilegi elevati per essere installato/Esegui non è possibile eseguire domini gestiti. Un sottoinsieme più piccolo di privilegi amministrativi è disponibile per i membri del gruppo Amministrazione delegata denominato 'AAD controller di dominio Administrators'. Questi privilegi includono i privilegi per configurare il sistema DNS, configurare i criteri di gruppo, ottenere privilegi di amministratore nel computer di dominio e così via.

#### <a name="domain-join"></a>Aggiunta a un dominio
È possibile partecipare macchine virtuali per il dominio gestito simile a come si partecipa a computer a un dominio di Active Directory.

#### <a name="domain-authentication-using-ntlm-and-kerberos"></a>Autenticazione di dominio tramite NTLM e Kerberos
Con i servizi di dominio Active Directory di Azure, è possibile utilizzare le credenziali aziendale per eseguire l'autenticazione con il dominio gestito. Le credenziali vengono mantenute sincronizzate con il tenant di Azure Active Directory. Per i tenant sincronizzati, Azure AD Connect garantisce che le modifiche apportate a credenziali apportate locale vengono sincronizzate con Azure Active Directory. Con una configurazione del dominio formattazione, è necessario impostare una relazione di attendibilità dominio con una foresta degli account locali per gli utenti autenticati con le proprie credenziali aziendale. In alternativa, è necessario impostare la replica di Active Directory per garantire che le password degli utenti sincronizzati alle macchine virtuali controller di dominio Azure.

#### <a name="custom-ou-structure"></a>Struttura di unità Organizzativa personalizzata
Membri del gruppo 'AAD controller di dominio Administrators' possono creare unità organizzative personalizzate all'interno del dominio gestito.

#### <a name="schema-extensions"></a>Estensioni dello schema
Non è possibile estendere lo schema di base di un dominio gestito di servizi di dominio Active Directory Azure. Di conseguenza, non è possibile elevate e spostate ai domini AAD-DS applicazioni basate su estensioni allo schema di Active Directory (ad esempio nuovi attributi nell'oggetto utente).

#### <a name="ad-domain-or-forest-trusts"></a>Dominio di Active Directory o trust
Domini gestiti non sono configurati per impostare le relazioni di trust (in ingresso/uscita) con altri domini. Pertanto, scenari, ad esempio distribuzioni foresta risorse o nei casi in cui non si desidera sincronizzare le password di Azure Active Directory non è possibile utilizzare servizi di dominio Active Directory Azure.

#### <a name="ldap-read"></a>Lettura LDAP
Dominio gestito supporta LDAP leggere carichi di lavoro. Pertanto non è possibile distribuire le applicazioni di eseguono operazioni di lettura LDAP dominio gestito.

#### <a name="secure-ldap"></a>LDAP sicuro
È possibile configurare servizi di dominio Active Directory di Azure per fornire l'accesso sicuro LDAP al proprio dominio gestito, nonché da internet.

#### <a name="ldap-write"></a>Scrittura LDAP
Dominio gestito è di sola lettura per gli oggetti utente. Le applicazioni di eseguono le operazioni di scrittura LDAP per gli attributi dell'oggetto utente di conseguenza, non funzionano in un dominio gestito. Inoltre, è Impossibile modificare le password degli utenti da all'interno del dominio gestito. Un altro esempio può essere la modifica di appartenenza ai gruppi o gli attributi di gruppo all'interno del dominio gestito, ma non è consentita. Tuttavia, le modifiche apportate agli attributi dell'utente o password creati in Azure Active Directory (tramite il portale di PowerShell/Azure) o Active Directory vengono sincronizzati con Active Directory AAD del dominio gestito in locale.

#### <a name="group-policy"></a>Criteri di gruppo
Costrutti di criteri di gruppo sofisticate non sono supportati in Active Directory AAD del dominio gestito. Ad esempio, non è possibile creare e distribuire gruppo distinti per ogni unità Organizzativa personalizzato nel dominio o utilizzare WMI il filtro per l'assegnazione dei criteri di gruppo. Esiste un incorporato oggetto Criteri di gruppo ogni per i contenitori "AADDC computer" e "AADDC utenti", può essere personalizzato per configurare i criteri di gruppo.

#### <a name="geo-dispersed-deployments"></a>Distribuzioni discostino geografico
Sono disponibili in un'unica rete virtuale in Azure Azure domini gestiti di servizi di dominio Active Directory. Per scenari che richiedono controller di dominio sia disponibile in più aree geografiche Azure in tutto il mondo, l'impostazione controller in macchine virtuali di Azure IaaS potrebbe essere alternativa migliore.


## <a name="do-it-yourself-diy-ad-deployment-options"></a>Opzioni di distribuzione "Fai da te" (DIY) Active Directory
Potrebbe essere casi di utilizzo di distribuzione in cui è necessario alcune delle funzionalità offerte da un'installazione di Windows Server Active Directory. In questi casi, è possibile utilizzare una delle seguenti opzioni (DIY) fai da te:

- **Dominio cloud autonomo:** È possibile impostare un'autonoma 'cloud dominio' utilizzando Azure macchine virtuali che sono state configurate come controller di dominio. Questa infrastruttura non è integrato con le locale ambiente di Active Directory. Questa opzione richiede un insieme diverso di 'cloud credenziali' amministrare login/macchine virtuali nel cloud.

- **Distribuzione foresta risorsa:** È possibile impostare un dominio nella topologia di strutture delle risorse utilizzando Azure macchine virtuali configurate come controller di dominio. Successivamente, è possibile configurare una relazione di trust di Active Directory con le locale ambiente di Active Directory. È possibile partecipare a dominio computer (Azure macchine virtuali) all'insieme di strutture risorse nel cloud. Autenticazione dell'utente si dispone di uno una connessione VPN/ExpressRoute alla directory locale.

- **Estendere il dominio locale in Azure:** È possibile connettersi una rete virtuale Azure alla rete locale tramite una connessione VPN/ExpressRoute, in modo che è possibile unire ai propri locali macchine virtuali di Azure Active Directory. In alternativa è possibile alzare di livello controller di dominio di replica del dominio locale in Azure come una macchina virtuale. È possibile impostarlo replicare tramite una connessione VPN/ExpressRoute alla directory locale. Questa modalità di distribuzione estende in modo efficiente il dominio locale in Azure.

> [AZURE.NOTE] È possibile stabilire che un'opzione di formattazione è più adatto per i casi di utilizzo distribuzione. Valutare la possibilità di [condividere feedback](active-directory-ds-contact-us.md) per comprendere caratteristiche Consente si è scelto di servizi di dominio Active Directory Azure in futuro. Questo feedback consente di sviluppare il servizio in modo da adattarlo alle specifiche esigenze di distribuzione e casi di utilizzo.

Microsoft ha pubblicato [linee guida per la distribuzione di Active Directory di Windows Server in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) per semplificare le installazioni di formattazione.


## <a name="related-content"></a>Contenuti correlati
- [Caratteristiche - servizi di dominio Azure](active-directory-ds-features.md)

- [Scenari di distribuzione - servizi di dominio Active Directory di Azure](active-directory-ds-scenarios.md)

- [Linee guida per la distribuzione di Windows Server Active Directory in macchine virtuali di Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx)
