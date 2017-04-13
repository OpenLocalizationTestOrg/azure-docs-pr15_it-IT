<properties 
   pageTitle="DNS zone e record | Microsoft Azure" 
   description="Panoramica del supporto per l'hosting zone DNS e i record DNS di Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="10/17/2016"
   ms.author="jtuliani"/>

# <a name="dns-zones-and-records"></a>Zone DNS e record

Questa pagina illustra i concetti fondamentali dei domini, DNS zones e record DNS e set di record e come sono supportati in Azure DNS.

## <a name="domain-names"></a>Nomi di dominio
 
Domain Name System è una gerarchia di domini. Inizia in corrispondenza della gerarchia da dominio "principale", il cui nome è semplicemente**.**.  Di sotto si provenire domini di primo livello, ad esempio "com", "rete", 'org', 'uk' o 'jp'.  Sotto questi sono i domini di secondo livello, ad esempio 'org.uk' o 'co.jp'. I domini nella gerarchia DNS sono globalmente distribuiti, ospitato da server dei nomi DNS in tutto il mondo.

Un registrar è un'organizzazione che consente di acquistare un nome di dominio, ad esempio 'contoso.com'.  Acquisto di un nome di dominio offre il diritto di controllare la gerarchia DNS in tale nome, ad esempio consente di indicare a usare il nome "www.contoso.com' al sito web della società. Il registrar può ospitare il dominio nel proprio server dei nomi per proprio conto o in alternativa è possibile specificare i server dei nomi alternativi.

Azure DNS fornisce un'infrastruttura di server nome distribuita a livello globale, disponibilità, è possibile utilizzare per l'hosting del dominio. Che ospita i domini nel sistema DNS Azure, è possibile gestire i propri record DNS con le stesse credenziali, API, strumenti, fatturazione e supporto come degli altri servizi Azure.

Azure DNS attualmente non supporta l'acquisto di nomi di dominio. Se si vuole acquistare un nome di dominio, è necessario usare un registrar di nomi di dominio di terze parti. Il registrar addebiterà in genere una piccola annuali. I domini possono quindi essere ospitati in Azure DNS per la gestione dei record DNS. Per informazioni dettagliate, vedere [delegato un dominio di Azure DNS](dns-domain-delegation.md) .

## <a name="dns-zones"></a>Zone DNS 

Una zona DNS viene utilizzata per ospitare i record DNS per un determinato dominio. Per avviare l'hosting del dominio in DNS Azure, è necessario creare una zona DNS per tale nome di dominio. Ogni record DNS per il dominio viene quindi creato all'interno di quest'area DNS. 

Ad esempio, il dominio 'contoso.com' può contenere più record DNS, ad esempio "mail.contoso.com" (per un server di posta elettronica) e "www.contoso.com" (per un sito web).

Quando si crea una zona DNS nel DNS Azure, il nome dell'area deve essere univoco all'interno del gruppo di risorse. Lo stesso nome zona può essere riutilizzato in un altro gruppo di risorse o una sottoscrizione di Azure diversa. Se più fusi orari condividono lo stesso nome, ogni istanza verrà assegnato indirizzi di server un nome diverso. Solo un set di indirizzi può essere configurato con il registrar di nomi di dominio.

>[AZURE.NOTE] Non è necessario un nome di dominio per creare una zona DNS con il nome di dominio in Azure DNS. Tuttavia, è necessario il proprietario del dominio per configurare i server dei nomi di Azure DNS come i server dei nomi corretti per il nome di dominio con il registrar di nomi di dominio.

Per ulteriori informazioni, vedere [delegato un dominio di Azure DNS](dns-domain-delegation.md).

## <a name="dns-records"></a>Record DNS

### <a name="record-types"></a>Tipi di record

Un nome e un tipo di ogni record DNS. I record sono organizzati in vari tipi in base ai dati contenuti. Il tipo più comune è un record 'A', che associa un nome a un indirizzo IPv4. Un altro tipo comune è un record 'MX', che associa un nome a un server di posta elettronica.

Azure DNS supporta tutti i tipi di record DNS comuni: A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT.

### <a name="record-names"></a>Nomi dei record

Nel sistema DNS Azure, i record vengono specificati utilizzando nomi relativi. Un nome di dominio *completo* (FQDN) include il nome di zona, mentre non un nome *relativo* . Ad esempio, il relativo nome record "www" nell'area 'contoso.com' fornisce il nome completo record 'www.contoso.com'.

Un record di *apice* è un record DNS principale (o *apice*) di una zona DNS. Ad esempio, in DNS zone 'contoso.com' un record di apice include anche il nome completo del 'contoso.com' (a volte detta un dominio *naked* ).  Per convenzione, il relativo nome '@' utilizzato per creare record di apice.

### <a name="record-sets"></a>Set di record
A volte è necessario creare più di un record DNS con un nome e tipo specificati. Si supponga ad esempio che il sito web 'www.contoso.com' è ospitato in due diversi indirizzi IP. Il sito Web richiede due diversi record, uno per ogni indirizzo IP. Questo è un esempio di un set di record:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS vengono gestiti i record DNS utilizzando *il set di record*. Un set di record (noto anche come un *risorsa* set di record) è l'insieme di record DNS in un'area che hanno lo stesso nome e sono dello stesso tipo. La maggior parte dei set di record contengono un singolo record, ma non sono rari esempi come questo, in cui un set di record contiene più di un record.

Ad esempio, si supponga che nell'area 'contoso.com' è già stato creato un record "www", che punta all'indirizzo IP indirizzi '134.170.185.46' (il primo record precedente).  Per creare il secondo record si aggiungere tale record per il set di record esistente, anziché creare un nuovo set di record.

I tipi di record CNAME e SOA sono eccezioni. Standard DNS non consentono di utilizzare più record con lo stesso nome per questi tipi, pertanto questi set di record possono contenere solo un singolo record.

### <a name="time-to-live"></a>Time to live

Il periodo di validità o TTL, specifica quanto tempo ogni record nella cache dal client prima nuovamente ricercato. Nell'esempio precedente, il valore TTL è 3.600 secondi o 1 ora.

Nel sistema DNS Azure, il valore TTL specificato per il set di record, non per ogni record, in modo che lo stesso valore utilizzato per tutti i record all'interno del set di record.  È possibile specificare un valore TTL compreso tra 1 e 2.147.483.647 secondi.

### <a name="wildcard-records"></a>Record con caratteri jolly

Azure DNS supporta [i record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Record con caratteri jolly vengono restituiti in risposta a una query con un nome corrispondente (a meno che non è adatto da un set di record non jolly). Set di record con caratteri jolly sono supportati per tutti i tipi di record ad eccezione di NS e SOA.  

Per creare un set di record con caratteri jolly, utilizzare il nome del set di record '\*'. In alternativa, è anche possibile utilizzare un nome con "\*'quadratino di ridimensionamento a sinistra, ad esempio, '\*Foo '.

### <a name="cname-records"></a>Record CNAME

Set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, è possibile creare un record CNAME impostata con il relativo nome "www" e un record con il relativo nome "www" nello stesso momento.

Poiché apice zona (nome = ‘@’) contiene sempre la NS e SOA record imposta che sono stati creati quando è stata creata l'area, non è possibile creare un record CNAME impostare al vertice zona.

Queste limitazioni si verificano da standard DNS e non sono le limitazioni di Azure DNS.

### <a name="ns-records"></a>Record NS

Un set di record NS viene creato automaticamente in cima ogni area (nome = ‘@’), e vengono eliminati automaticamente quando viene eliminato l'area (non può essere eliminata separatamente).  È possibile modificare il valore TTL di questo set di record, ma non è possibile modificare i record, vengono preconfigurati fare riferimento ai server DNS Azure di nome assegnato all'area.

È possibile creare ed eliminare altri record NS all'interno dell'area, non all'apice zona.  In questo modo è possibile configurare zone figlio (vedere [delega sottodomini nel sistema DNS Azure](dns-domain-delegation.md#delegating-sub-domains-in-azure-dns)).

### <a name="soa-records"></a>Record SOA

Viene creato automaticamente un set di record SOA in cima ogni area (nome = ‘@’), e vengono eliminati automaticamente quando viene eliminato l'area.  Record SOA non è possibile essere creati o eliminati separatamente. 

È possibile modificare tutte le proprietà del record SOA ad eccezione della proprietà 'host', preconfigurato per fare riferimento al nome del server primario nome fornito da Azure DNS.

### <a name="spf-records"></a>Record SPF

Sender Policy Framework (SPF) record vengono utilizzati per specificare quali server di posta elettronica è consentita a inviare posta elettronica per conto di un nome di dominio.  Correggere la configurazione della record SPF è importante per impedire ai destinatari contrassegnare il messaggio di posta elettronica come 'indesiderati'.

RFC DNS originariamente introdotto un nuovo tipo di record 'SPF' per supportare questo scenario. Per supportare i server dei nomi meno recenti, sono consentiti anche l'utilizzo del tipo di record TXT per specificare i record SPF.  Questa ambiguità condotto confusione, è stato risolto da [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1).  Questo specificato che devono essere creati solo usando il tipo di record TXT record SPF e deprecata il tipo di record SPF. 

**Record SPF sono supportati da Azure DNS e devono essere creati usando il tipo di record TXT.** Il tipo di record SPF obsoleto non è supportato. Quando [l'importazione di un DNS zone file](dns-import-export.md), i record SPF utilizzando il tipo di record SPF vengono convertiti nel tipo di record TXT. 

### <a name="srv-records"></a>Record SRV

[Record SRV](https://en.wikipedia.org/wiki/SRV_record) vengono utilizzati da vari servizi per specificare i percorsi server. Quando si specifica un record SRV DNS Azure:

- Il *servizio* e *protocollo* deve essere specificati come parte del nome del set di record, preceduto da caratteri di sottolineatura.  Ad esempio, '\_sip. \_tcp.name'.  Per un record all'apice zona, non è necessario specificare '@' nel nome del record, è sufficiente usare il servizio e protocollo, ad esempio "\_sip. \_tcp'.
- La *priorità*, lo *spessore*, *porta*e *destinazione* vengono specificati come parametri di tutti i record di set di record. 

## <a name="tags-and-metadata"></a>Tag e dei metadati

### <a name="tags"></a>Tag
Tag sono un elenco di coppie di nome e vengono utilizzati da Gestione risorse di Azure alle risorse di etichetta.  Gestione risorse di Azure utilizza tag per consentire di visualizzazioni filtrate la fattura Azure e consente di impostare un criterio necessari tag. Per ulteriori informazioni sui tag, vedere [usare i contrassegni per organizzare le risorse Azure](../resource-group-using-tags.md).

Azure DNS supporta l'utilizzo di gestione risorse Azure tag sulle risorse zona DNS.  Non supporta i contrassegni nel set di record DNS.

### <a name="metadata"></a>Metadati

In alternativa ai tag di set di record DNS Azure supporta annotazione set di record utilizzando 'metadata'.  Analogamente ai tag, metadati consentono di associare coppie di nome a ogni set di record.  Può essere utile, ad esempio per registrare lo scopo di ogni set di record.  A differenza dei tag, metadati non possono essere utilizzati per fornire una visualizzazione filtrata la fattura Azure e non è possibile specificare un criterio di gestione di risorse Azure.

## <a name="limits"></a>Limiti

Predefinito limitazioni seguenti quando si utilizza Azure DNS:

[AZURE.INCLUDE [dns-limits](../../includes/dns-limits.md)] 

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare a utilizzare DNS Azure, informazioni su come [creare una zona DNS](dns-getstarted-create-dnszone-portal.md) e [creare record DNS](dns-getstarted-create-recordset-portal.md).
- Per eseguire la migrazione di una zona DNS esistente, informazioni su come [importare e file di zona DNS](dns-import-export.md).
