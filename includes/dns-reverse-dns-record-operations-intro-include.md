## <a name="what-is-reverse-dns"></a>Che cos'è DNS inverso?

Record DNS convenzionale Abilita il mapping da un nome DNS (ad esempio "www.contoso.com") a un indirizzo IP (ad esempio 64.4.6.100).  DNS inverso consente la conversione di un indirizzo IP (64.4.6.100) a un nome ('www.contoso.com').

Record DNS inverso vengono usati nelle diverse situazioni. Ad esempio, i record DNS inverso ampiamente utilizzati contro la posta indesiderata di posta elettronica verificando il mittente del messaggio di posta elettronica.  Il server della posta ricezione verrà recuperare il record DNS inverso dell'indirizzo IP del server di invio e verificare se tale host è autorizzato a inviare posta elettronica dal dominio di origine. (Si noti tuttavia che [Azure calcolare services non supportano invio messaggi di posta elettronica ai domini esterni](https://blogs.msdn.microsoft.com/mast/2016/04/04/sending-e-mail-from-azure-compute-resource-to-external-domains/).)

## <a name="how-reverse-dns-works"></a>Come inverso works DNS

Record DNS inverso sono ospitati in aree DNS speciali, note come zone 'ARPA'.  Queste aree formano una gerarchia DNS separata in parallelo con la gerarchia normale che ospitano domini, ad esempio 'contoso.com'.

Ad esempio, il DNS record 'www.contoso.com' è implementata utilizzando un record DNS 'A' con nome "www" nell'area 'contoso.com'.  Questo record a punti importanti per l'indirizzo IP corrispondente, in questo caso 64.4.6.100.  Ricerca inversa è implementata separatamente, con un record di 'PTR' denominato '100' nell'area '6.4.64.in-addr.arpa' (si noti che gli indirizzi IP vengono annullati nelle aree ARPA).  Questo record PTR, se è stata configurata correttamente, punti il nome "www.contoso.com".

Quando un'organizzazione viene assegnata un blocco di indirizzi IP, acquisiscono il diritto di gestire l'area ARPA corrispondente. Le zone ARPA corrispondenti a blocchi di indirizzi IP utilizzati da Azure sono ospitate e gestite da Microsoft. ISP possono ospitare zona ARPA per i propri indirizzi IP dell'utente o è possibile che consenta la zona ARPA in un servizio DNS preferito, ad esempio Azure DNS.

>[AZURE.NOTE] Ricerche dirette DNS e le ricerche DNS inverse sono implementate in gerarchie DNS separate, in parallelo. La ricerca inversa per 'www.contoso.com' è **non** ospitato nell'area 'contoso.com', è ospitato nell'area ARPA per il blocco di indirizzi IP corrispondente.

Per ulteriori informazioni su DNS inverso, vedere [Ricerca DNS inverso](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).

## <a name="azure-support-for-reverse-dns"></a>Azure supporto per il DNS inverso

Azure supporta due scenari separati relative per invertire DNS:

1. L'area ARPA corrispondente per il blocco di indirizzi IP di hosting.
2. Consente di configurare il record DNS inverso per l'indirizzo IP assegnato al servizio Azure.

Per supportare il DNS ex, Azure utilizzabile per ospitare le zone ARPA e gestire i record PTR per ogni DNS inversa.  Il processo di creazione area ARPA, configurare la delega e configurare i record PTR è identica a quella di regolare zone DNS.  Solo le differenze sono la delega deve essere configurata tramite il provider di servizi Internet anziché il proprio DNS registrar che deve essere utilizzato solo il tipo di record PTR.

Per supportare quest'ultimo, Azure consente di configurare la ricerca inversa per l'indirizzo IP allocato al servizio.  Questa ricerca inversa è configurata con Azure come record PTR nell'area ARPA corrispondente.  Queste aree ARPA corrispondente a tutti gli intervalli di indirizzi IP utilizzati da Azure, sono ospitate da Microsoft. **Il resto di questo articolo viene descritto in questo scenario in modo dettagliato.**
