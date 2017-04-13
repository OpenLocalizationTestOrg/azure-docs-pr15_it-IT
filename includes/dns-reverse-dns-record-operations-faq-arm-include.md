<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Domande frequenti - DNS inverso per l'indirizzo IP assegnato Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Quantità invertire il costo di record DNS?
Fanno gratuiti!  Non esiste costi aggiuntivi per i record DNS inverso o query.

### <a name="will-the-reverse-dns-records-for-my-azure-assigned-public-ip-address-resolve-from-the-internet"></a>I record DNS inverso per assegnate Azure indirizzo IP pubblico risolverà da internet?
Sì. Dopo aver impostato la proprietà DNS inversa per l'indirizzo IP pubblico, Azure gestisce tutte le aree DNS necessarie per garantire che consente di risolvere i record DNS inverso per tutti gli utenti di internet e deleghe DNS.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-public-ip-addresses"></a>Verrà creato un record DNS inverso predefinito per il pubblico gli indirizzi IP?
No. DNS inverso è una caratteristica di consenso esplicito. Nessun record DNS inverso predefiniti vengono creati se si sceglie di non configurarli.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Che cos'è il formato per il nome di dominio completo (FQDN)?
FQDN vengono specificati in avanti e deve terminare con un punto (ad esempio, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Cosa accade se i controlli di convalida per il DNS inverso specificati non riesce?
Nel punto in cui i controlli di convalida per il DNS inverso esito negativo, l'operazione di gestione del servizio non verrà completata. Per correggere il valore DNS inverso in base alle esigenze e riprovare.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>È possibile gestire DNS inverso per il proprio sito Web di Azure?
Inversa DNS non è supportata per i siti Web di Azure. Inversa DNS è supportata per macchine virtuali di Azure.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-public-ip-address"></a>È possibile configurare i record DNS inverso multipli per l'indirizzo IP pubblico?
No. Azure supporta un singolo record DNS inverso per ogni indirizzo IP pubblico. Ogni indirizzo IP pubblico tuttavia possono avere propri record DNS inverso.

### <a name="can-i-configure-reverse-dns-records-for-an-ipv6-public-ip-address"></a>È possibile configurare i record DNS inverso per un indirizzo IP pubblico IPv6?
No.  Al momento i record DNS inverso sono supportati solo per gli indirizzi IP pubblici IPv4.

### <a name="can-i-configure-a-reverse-dns-record-for-my-public-ip-address-without-having-a-domainnamelabel-specified"></a>È possibile configurare un record DNS inverso per l'indirizzo IP pubblico senza un DomainNameLabel specificato?
No. Per sfruttare i record DNS inverso per gli indirizzi IP pubblico, è necessario specificare la proprietà DomainNameLabel.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>È possibile inviare messaggi di posta elettronica ai domini esterni da miei servizi Azure calcolare?
No. [Calcolare azure services non supportano invio messaggi di posta elettronica ai domini esterni](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
