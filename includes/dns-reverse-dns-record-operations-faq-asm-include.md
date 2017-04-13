<BR> 
## <a name="faq---reverse-dns-for-your-azure-assigned-ip-address"></a>Domande frequenti - DNS inverso per l'indirizzo IP assegnato Azure

### <a name="how-much-do-reverse-dns-records-cost"></a>Quantità invertire il costo di record DNS?
Fanno gratuiti!  Non esiste costi aggiuntivi per i record DNS inverso o query.

### <a name="will-my-reverse-dns-records-resolve-from-the-internet"></a>I record DNS inverso risolverà da internet?
Sì. Dopo aver impostato la proprietà DNS inversa per il servizio Cloud, Azure gestisce tutte le aree DNS necessarie per garantire che consente di risolvere i record DNS inverso per tutti gli utenti di internet e deleghe DNS.

### <a name="will-a-default-reverse-dns-record-be-created-for-my-cloud-services"></a>Verrà creato un record DNS inverso predefinito per i servizi Cloud?
No. DNS inverso è una caratteristica di consenso esplicito. Nessun record DNS inverso predefiniti vengono creati se si sceglie di non configurarli.

### <a name="what-is-the-format-for-the-fully-qualified-domain-name-fqdn"></a>Che cos'è il formato per il nome di dominio completo (FQDN)?
FQDN vengono specificati in avanti e deve terminare con un punto (ad esempio, "app1.contoso.com.").

### <a name="what-happens-if-the-validation-checks-for-the-reverse-dns-ive-specified-fail"></a>Cosa accade se i controlli di convalida per il DNS inverso specificati non riesce?
Nel punto in cui i controlli di convalida per il DNS inverso esito negativo, l'operazione di gestione del servizio non verrà completata. Per correggere il valore DNS inverso in base alle esigenze e riprovare.

### <a name="can-i-manage-reverse-dns-for-my-azure-website"></a>È possibile gestire DNS inverso per il proprio sito Web di Azure?
Inversa DNS non è supportata per i siti Web di Azure. DNS inverso è supportata per i ruoli di Azure PaaS e macchine virtuali IaaS.

### <a name="can-i-configure-multiple-reverse-dns-records-for-my-cloud-service"></a>È possibile configurare i record DNS inverso multipli per il servizio Cloud?
No. Azure supporta un singolo record DNS inverso per ogni servizio Cloud di Azure. Ogni servizio Cloud di Azure, tuttavia, può avere propri record DNS inverso.

### <a name="can-i-send-emails-to-external-domains-from-my-azure-compute-services"></a>È possibile inviare messaggi di posta elettronica ai domini esterni da miei servizi Azure calcolare?
No. [Calcolare azure services non supportano invio messaggi di posta elettronica ai domini esterni](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).
