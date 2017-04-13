<properties
     pageTitle="Come eseguire il mapping di contenuto della rete (CDN) Azure contenuti in un dominio personalizzato | Microsoft Azure"
     description="In questo argomento viene illustrato come eseguire il mapping di un contenuto CDN a un dominio personalizzato."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Come eseguire il mapping di dominio personalizzato al punto finale di contenuti rete (CDN)
È possibile associare un dominio personalizzato a un endpoint CDN per usare il nome di dominio nell'URL contenuto della cache, invece che tramite un sottodominio di azureedge.net.

Esistono due modi per eseguire il mapping di un dominio personalizzato a un endpoint CDN:

1. [Creare un record CNAME con il registrar e mappare il dominio personalizzato e sottodominio all'endpoint CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Un record CNAME è una funzionalità DNS che esegue il mapping di un dominio di origine, ad esempio `www.contosocdn.com` o `cdn.contoso.com`, a un dominio di destinazione. In questo caso, il dominio di origine è il dominio personalizzato e sottodominio (un sottodominio, ad esempio **www** o **cdn** è sempre necessario). Il dominio di destinazione è l'endpoint CDN.  

    Il processo di mapping di un dominio personalizzato per l'endpoint CDN, tuttavia, causando un breve periodo di tempo di inattività per il dominio mentre si sta registrando il dominio nel portale di Azure.

2. [Aggiungere un passaggio intermedio registrazione con **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Se il dominio personalizzato è attualmente in grado di supportare un'applicazione con un contratto di servizio (contratto di servizio) che è necessario alcun tempo di inattività, è possibile utilizzare il sottodominio Azure **cdnverify** per fornire un passaggio intermedio registrazione in modo che gli utenti saranno in grado di accedere al dominio durante il mapping avviene sistema DNS.  

Dopo aver registrato il dominio personalizzato con una delle procedure descritte in precedenza, sarà possibile [verificare che il sottodominio personalizzato fa riferimento il punto finale CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] È necessario creare un record CNAME con il registrar per eseguire il mapping di un dominio per l'endpoint CDN. Record CNAME della mappa sottodomini specifici, ad esempio `www.contoso.com` o `cdn.contoso.com`. Non è possibile eseguire il mapping di un record CNAME per un dominio radice, ad esempio `contoso.com`.
>    
> Può essere associato a un punto finale CDN solo un sottodominio. Il record CNAME creati indirizza tutto il traffico indirizzato al sottodominio all'endpoint specificato.  Ad esempio, se si associa `www.contoso.com` con l'endpoint CDN, quindi non è possibile associare con gli altri endpoint Azure, ad esempio un endpoint di account di archiviazione o un endpoint del servizio cloud. Tuttavia, è possibile utilizzare diversi sottodomini dallo stesso dominio per gli endpoint servizio diverso. Eseguire il mapping diversi sottodomini allo stesso endpoint CDN.
>
> Per gli endpoint **CDN Azure da Verizon** (Standard e Premium), tenere presente che occupa a **90 minuti** per propagarsi ai nodi di un bordo CDN modifiche di dominio personalizzato.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registrare un dominio personalizzato per un endpoint di rete CDN di Azure

1.  Accedere al [portale di Azure](https://portal.azure.com/).
2.  Fare clic su **Sfoglia**, quindi **CDN profili**, quindi il profilo CDN con endpoint che si desidera eseguire il mapping a un dominio personalizzato.  
3.  Selezionare l'endpoint CDN con cui si desidera associare il sottodominio e il **Profilo CDN** .
4.  Nella parte superiore e il punto finale, fare clic sul pulsante **Aggiungi dominio personalizzato** .  In e **l'aggiungere un dominio personalizzato** , verrà visualizzato il nome dell'endpoint host, derivato dall'endpoint di rete CDN, da utilizzare per creare un nuovo record CNAME. Il formato dell'indirizzo di nome host verrà visualizzato come ** &lt;EndpointName >. azureedge.net**.  È possibile copiare questo nome host da utilizzare per creare il record CNAME.  
5.  Passare al sito web del registrar del dominio e individuare la sezione per la creazione di record DNS. È possibile in una sezione, ad esempio **Nome di dominio**, **DNS**o **Gestione di nome Server**.
6.  Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole, CNAME, Alias o sottodomini.
7.  Creare un nuovo record CNAME che esegue il mapping prescelto sottodominio (ad esempio **www** o **cdn**) per il nome host fornito e **l'aggiungere un dominio personalizzato** .
8.  Tornare a e **l'aggiungere un dominio personalizzato** e immettere il dominio personalizzato, inclusi il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato `www.contoso.com` o `cdn.contoso.com`.   

    Azure consente di verificare che il record CNAME è disponibile per il nome di dominio che è stato immesso. Se il record CNAME è corretto, il dominio personalizzato viene convalidato.  Per gli endpoint **CDN Azure da Verizon** (Standard e Premium), può richiedere fino a 90 minuti per le impostazioni di dominio personalizzato propagazione delle modifiche apportate a tutti i nodi bordo CDN, tuttavia.  

    Si noti che in alcuni casi potrebbe richiedere tempo per il record CNAME propagazione delle modifiche apportate ai server dei nomi su Internet. Se il dominio non viene convalidato immediatamente e si ritiene che il record CNAME è corretto, attendere qualche minuto e riprovare.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registrare un dominio personalizzato per un endpoint di Azure CDN utilizzando il sottodominio cdnverify intermedia  

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Sfoglia**, quindi **CDN profili**, quindi il profilo CDN con endpoint che si desidera eseguire il mapping a un dominio personalizzato.  
3. Selezionare l'endpoint CDN con cui si desidera associare il sottodominio e il **Profilo CDN** .
4. Nella parte superiore e il punto finale, fare clic sul pulsante **Aggiungi dominio personalizzato** .  In e **l'aggiungere un dominio personalizzato** , verrà visualizzato il nome dell'endpoint host, derivato dall'endpoint di rete CDN, da utilizzare per creare un nuovo record CNAME. Il formato dell'indirizzo di nome host verrà visualizzato come ** &lt;EndpointName >. azureedge.net**.  È possibile copiare questo nome host da utilizzare per creare il record CNAME.
5. Passare al sito web del registrar del dominio e individuare la sezione per la creazione di record DNS. È possibile in una sezione, ad esempio **Nome di dominio**, **DNS**o **Gestione di nome Server**.
6. Individuare la sezione per la gestione dei record CNAME. Potrebbe essere necessario passare a una pagina di impostazioni avanzate e cercare le parole **CNAME** **Alias**o **sottodomini**.
7. Creare un nuovo record CNAME e fornire un alias di sottodominio che include il sottodominio **cdnverify** . Ad esempio, il sottodominio specificato sarà nel formato **cdnverify.www** o **cdnverify.cdn**. Quindi specificare il nome host, che rappresenta l'endpoint di rete CDN, in formato **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Tornare a e **l'aggiungere un dominio personalizzato** e immettere il dominio personalizzato, inclusi il sottodominio, nella finestra di dialogo. Ad esempio, immettere il nome di dominio nel formato `www.contoso.com` o `cdn.contoso.com`. Si noti che in questo passaggio, sarà necessario anteporre il sottodominio con **cdnverify**.  

    Azure necessario verificare che il record CNAME esista cdnverify nome di dominio che è stato immesso.
9. A questo punto, il dominio personalizzato è stato verificato da Azure, ma il traffico al proprio dominio non è ancora stato instradato all'endpoint CDN. Dopo attesa troppo lungo per consentire le impostazioni di dominio personalizzato propagazione delle modifiche apportate ai nodi di bordo CDN (90 minuti per **CDN Azure da Verizon**, 1-2 minuti prima di **Azure CDN da Akamai**), tornare al sito web del registrar DNS e creare un altro record CNAME che esegue il mapping di sottodominio per l'endpoint CDN. Ad esempio, specificare il sottodominio come **www** o **cdn**e il nome host come ** &lt;EndpointName >. azureedge.net**. Con questo passaggio, la registrazione del dominio personalizzato è stata completata.
10. Infine, è possibile eliminare il record CNAME è stato creato mediante **cdnverify**, che venisse eseguito necessaria solo di un passaggio intermedio.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Verificare che il sottodominio personalizzato fa riferimento il punto finale CDN

- Dopo avere completato la registrazione del dominio personalizzato, è possibile accedere contenuto memorizzati nella cache per l'endpoint CDN usando il dominio personalizzato.
Prima di tutto, verificare di disporre di contenuti pubblici cache all'endpoint. Ad esempio, se l'endpoint CDN è associata a un account di archiviazione, la rete CDN memorizza contenuto nei contenitori di blob pubblico. Per verificare il dominio personalizzato, assicurarsi che il contenitore sia impostato per consentire l'accesso pubblico e che sia presente almeno un blob.
- Nel browser passare all'indirizzo di blob usando il dominio personalizzato. Ad esempio, se il dominio personalizzato è `cdn.contoso.com`, l'URL per un blob memorizzati nella cache sarà simile al seguente URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Vedere anche

[Come attivare la rete di distribuzione di contenuti (CDN) per Azure](./cdn-create-new-endpoint.md)  
