## <a name="about-records"></a>Informazioni sui record

Un nome e un tipo di ogni record DNS. I record sono organizzati in vari tipi in base ai dati contenuti. Il tipo più comune è un record "Un", che associa un nome a un indirizzo IPv4. Un altro tipo è un record "MX", che associa un nome a un server di posta elettronica.

Azure DNS supporta tutti comuni tipi di record DNS, incluso A, AAAA, CNAME, MX, NS, PTR, SOA, SRV e TXT. Si noti che:
- Set di record SOA vengono creati automaticamente con ogni area, non possono essere creati separatamente.
- Record SPF devono creati utilizzando il tipo di record TXT. Per ulteriori informazioni, vedere [la pagina](http://tools.ietf.org/html/rfc7208#section-3.1).

Nel sistema DNS Azure, i record vengono specificati utilizzando nomi relativi. Un "" nome di dominio completo (FQDN) include il nome di zona, mentre non un nome "relativo". Ad esempio, il relativo nome record "www" nell'area "contoso.com" offre www.contoso.com nome record completo.

## <a name="about-record-sets"></a>Sui set di record

A volte è necessario creare più di un record DNS con un nome e tipo specificati. Si supponga ad esempio che il sito web "www.contoso.com" è ospitato in due diversi indirizzi IP. Il sito Web richiede due diversi record, uno per ogni indirizzo IP. Questo è un esempio di un set di record:

    www.contoso.com.        3600    IN  A   134.170.185.46
    www.contoso.com.        3600    IN  A   134.170.188.221

Azure DNS vengono gestiti i record DNS utilizzando set di record. Un set di record è l'insieme di record DNS in un'area che hanno lo stesso nome e sono dello stesso tipo. La maggior parte dei set di record contengono un singolo record, ma non sono rari esempi come questo, in cui un set di record contiene più di un record.

Set di record CNAME e SOA sono eccezioni. Standard DNS non consentono di utilizzare più record con lo stesso nome per questi tipi.

Il periodo di validità o TTL, specifica quanto tempo memorizzati nella cache dai client ogni record prima nuovamente ricercato. In questo esempio, il valore TTL è 3.600 secondi o 1 ora. Il valore TTL specificato per il set di record, non per ogni record, in modo che lo stesso valore utilizzato per tutti i record all'interno del set di record.

#### <a name="wildcard-record-sets"></a>Set di record con caratteri jolly

Azure DNS supporta [i record con caratteri jolly](https://en.wikipedia.org/wiki/Wildcard_DNS_record). Questi dati vengono restituiti per le query con un nome corrispondente (a meno che non è adatto da un set di record non jolly). Set di record con caratteri jolly sono supportati per tutti i tipi di record ad eccezione di NS e SOA.  

Per creare un set di record con caratteri jolly, utilizzare il nome del set di record "\*". In alternativa, utilizzare un nome con l'etichetta "\*", ad esempio,"\*foo".

#### <a name="cname-record-sets"></a>Set di record CNAME

Set di record CNAME non possono coesistere con altri set di record con lo stesso nome. Ad esempio, è possibile creare un record CNAME impostata con il relativo nome "www" e un record con il relativo nome "www" nello stesso momento. Poiché apice zona (nome = ‘@’) contiene sempre la NS e SOA record imposta che sono stati creati quando è stata creata l'area, non è possibile creare un record CNAME impostare al vertice zona. Queste limitazioni si verificano da standard DNS e non le limitazioni di Azure DNS.
