Il sistema DNS (Domain Name) viene utilizzato per individuare elementi su internet. Ad esempio, quando si immettere un indirizzo nel browser o fare clic sul collegamento in una pagina web, utilizza DNS per tradurre un indirizzo IP del dominio. È l'indirizzo IP del tipo di come un indirizzo stradale, ma non è molto umano descrittivo. Ad esempio, è molto più facile da ricordare un nome DNS ad esempio **contoso.com** , piuttosto che memorizza un indirizzo IP, ad esempio 192.168.1.88 o 2001:0:4137:1f67:24a2:3888:9cce:fea3.

Il sistema DNS si basa sui *record*. Record associano una specifica *nome*, ad esempio **contoso.com**, a un indirizzo IP o un altro nome DNS. Quando un'applicazione, ad esempio un web browser, Cerca un nome nel sistema DNS, viene rilevato il record e Usa qualsiasi elemento a cui punta all'indirizzo. Se il valore che a cui punta un indirizzo IP, il browser utilizzerà il valore. Se fa riferimento a un altro nome DNS, l'applicazione deve eseguire nuovamente la risoluzione. Infine, risoluzione dei nomi di tutti terminerà l'indirizzo IP.

Quando si crea un sito Web di Azure, viene automaticamente assegnato un nome DNS per il sito. Questo nome assume la forma di ** &lt;yoursitename&gt;. azurewebsites.net**. Quando si aggiunge il sito Web come endpoint Azure il traffico Manager, il sito Web sarà possibile accedere mediante il ** &lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** dominio.

> [AZURE.NOTE] Quando il sito Web è configurato come un endpoint di gestore del traffico, utilizzare la **. trafficmanager.net** indirizzo durante la creazione dei record DNS.

> È possibile utilizzare solo i record CNAME con il traffico Manager

Sono disponibili anche più tipi di record, ognuna con le proprie funzioni e limitazioni, ma per i siti Web configurato per come gestore del traffico endpoint, è solo importante circa uno. Record *CNAME* .

###<a name="cname-or-alias-record"></a>Record CNAME o Alias

Un record CNAME associa un nome DNS *specifico* , ad esempio **mail.contoso.com** o **www.contoso.com**, a un altro nome di dominio (canonico). Nel caso dei siti Web Azure con il traffico Manager, il nome del dominio canonico è il ** &lt;applicazione >. trafficmanager.net** nome di dominio del proprio profilo di gestore del traffico. Una volta creato, il record CNAME crea un alias per il ** &lt;applicazione >. trafficmanager.net** nome di dominio. La voce CNAME risolverà all'indirizzo IP del ** &lt;applicazione >. trafficmanager.net** dominio nome automaticamente, se l'indirizzo IP del sito Web cambia, non è necessario eseguire altre azioni.

Dopo il traffico arriva al gestore del traffico instrada il traffico al sito Web usando il metodo per che è configurato bilanciamento del carico. Questo è completamente trasparente ai visitatori del sito Web. Vedranno solo il nome di dominio personalizzato nel browser.

> [AZURE.NOTE] Alcuni Registrar Consenti solo eseguire il mapping sottodomini quando si utilizza un record CNAME, ad esempio **www.contoso.com**e non i nomi di radice, ad esempio **contoso.com**. Per ulteriori informazioni sui record CNAME, vedere la documentazione fornita dal registrar, <a href="http://en.wikipedia.org/wiki/CNAME_record">la voce di Wikipedia sul record CNAME</a>o il documento di <a href="http://tools.ietf.org/html/rfc1035">Nomi di dominio IETF - implementazione e specifiche</a> .
