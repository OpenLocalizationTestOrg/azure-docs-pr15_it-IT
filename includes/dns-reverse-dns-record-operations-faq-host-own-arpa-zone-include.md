<BR> 
## <a name="faq---hosting-your-arpa-zone-in-azure-dns"></a>Domande frequenti - Hosting della zona ARPA nel DNS Azure

### <a name="can-i-host-arpa-zones-for-my-isp-assigned-ip-blocks-on-azure-dns"></a>È possibile ospitare zone ARPA per i blocchi IP assegnato ISP Azure DNS?
Sì. Hosting di zone ARPA per intervalli IP in Azure DNS è completamente supportata.

È sufficiente [creare l'area DNS Azure](dns-getstarted-create-dnszone.md), quindi rilevare ISP delegare [l'area](dns-domain-delegation.md).  È quindi possibile gestire i record PTR per ogni ricerca inversa nello stesso modo altri tipi di record.

È anche possibile [importare una zona inversa esistente usando CLI Azure](dns-import-export.md).

### <a name="how-much-does-hosting-my-arpa-zone-cost"></a>Quanto costa hosting il costo di zona ARPA?
Blocco di Azure DNS zone ARPA di hosting per l'indirizzo IP assegnato al provider di servizi Internet viene calcolato [tariffe standard DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="can-i-host-arpa-zones-for-both-ipv4-and-ipv6-addresses-in-azure-dns"></a>È possibile ospitare zone ARPA per gli indirizzi IPv4 e IPv6 in Azure DNS?
Sì.