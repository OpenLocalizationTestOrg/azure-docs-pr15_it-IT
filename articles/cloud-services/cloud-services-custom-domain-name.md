<properties
    pageTitle="Configurare un nome di dominio personalizzato in servizi Cloud | Microsoft Azure"
    description="Informazioni su come esporre l'applicazione Azure o dati in un dominio personalizzato per la configurazione delle impostazioni DNS."
    services="cloud-services"
    documentationCenter=".net"
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="adegeo"/>

# <a name="configuring-a-custom-domain-name-for-an-azure-cloud-service"></a>Configurazione di un nome di dominio personalizzato per un servizio cloud Azure

> [AZURE.SELECTOR]
- [Portale di Azure](cloud-services-custom-domain-name-portal.md)
- [Portale classica Azure](cloud-services-custom-domain-name.md)


Quando si crea un servizio Cloud, Azure viene assegnato un sottodominio di cloudapp.net. Ad esempio, se il servizio Cloud è denominato "contoso", gli utenti potranno accedere all'applicazione su un URL, ad http://contoso.cloudapp.net. Azure assegna anche un indirizzo IP virtuale.

È inoltre possibile esporre l'applicazione sul proprio nome di dominio, ad esempio contoso.com. In questo articolo viene spiegato come prenotare o configurare un nome di dominio personalizzato per i ruoli di web servizio Cloud.

Come è già undestand quali record CNAME e sono? [Andare oltre la spiegazione](#add-a-cname-record-for-your-custom-domain).

> [AZURE.NOTE]
> Iniziare più rapidamente più! Utilizzare Azure [risoluzione guidata dei problemi](http://support.microsoft.com/kb/2990804). Rende associazione di un nome di dominio personalizzato e proteggere la comunicazione (SSL) con servizi Cloud Windows Azure o siti Web di Azure velocemente.

<p/>

> [AZURE.NOTE]
> Le procedure descritte in questa attività si applicano ai servizi Cloud Azure. Per i servizi di App, vedere [questo](../app-service-web/web-sites-custom-domain-name.md). Per gli account di archiviazione, vedere [questo](../storage/storage-custom-domain-name.md).


## <a name="understand-cname-and-a-records"></a>Comprendere i record CNAME e

CNAME (alias record o) e un record è possibile associare un nome di dominio a un server specifico (o servizio in questo caso) tuttavia funzionano in modo leggermente diverso. Esistono alcune considerazioni specifiche quando si utilizza un record con i servizi Cloud Azure che è necessario prendere in considerazione prima di decidere quale utilizzare.

### <a name="cname-or-alias-record"></a>Record CNAME o Alias

Un record CNAME esegue il mapping di un dominio *specifico* , ad esempio **contoso.com** o **www.contoso.com**, un nome di dominio canonico. In questo caso, il nome di dominio canonico è il **.cloudapp [applicazione] .net** nome di dominio il Azure ospitato applicazione. Una volta creato, il record CNAME crea un alias per il **.cloudapp [applicazione] .net**. La voce CNAME risolverà all'indirizzo IP del **.cloudapp [applicazione] .net** service automaticamente, se l'indirizzo IP del servizio cloud cambia, non è necessario eseguire altre azioni.

> [AZURE.NOTE]
> Alcuni Registrar Consenti solo eseguire il mapping sottodomini quando si utilizza un record CNAME, ad esempio www.contoso.com e non i nomi di radice, ad esempio contoso.com. Per ulteriori informazioni sui record CNAME, vedere la documentazione fornita dal registrar, [la voce di Wikipedia sul record CNAME](http://en.wikipedia.org/wiki/CNAME_record)o il documento di [Nomi di dominio IETF - implementazione e specifiche](http://tools.ietf.org/html/rfc1035) .

### <a name="a-record"></a>Un record

Un record a associa un dominio, ad esempio **contoso.com** o **www.contoso.com**, *o di un dominio con caratteri jolly* come ** \*. contoso.com**, a un indirizzo IP. In caso di un servizio Cloud di Azure, l'indirizzo IP virtuale del servizio. Il vantaggio di un record su un record CNAME è possibile avere una voce che utilizza un carattere jolly, ad esempio \* **. contoso.com**, che si gestisce le richieste di più sottodomini, ad esempio **mail.contoso.com**, **login.contoso.com**o **www.contso.com**.

> [AZURE.NOTE]
> Poiché un record è associato a un indirizzo IP statico, non può risolvere automaticamente le modifiche apportate all'indirizzo IP del servizio Cloud. L'indirizzo IP utilizzato dal servizio Cloud allocata la prima volta che si distribuisce un intervallo di aperto vuoto (produzione o gestione temporanea.) Se si elimina la distribuzione per l'intervallo di aperto, l'indirizzo IP viene rilasciato da Azure e qualsiasi distribuzioni future a che possono essere specificate un nuovo indirizzo IP.
>
> In modo appropriato, l'indirizzo IP del spazio distribuzione specificata (produzione o gestione temporanea) viene mantenuto quando si passa tra gestione temporanea e distribuzioni di produzione o esecuzione di un aggiornamento sul posto di una distribuzione esistente. Per ulteriori informazioni su come eseguire queste azioni, vedere [sulla gestione dei servizi cloud](cloud-services-how-to-manage.md).


## <a name="add-a-cname-record-for-your-custom-domain"></a>Aggiungere un record CNAME per il dominio personalizzato

Per creare un record CNAME, è necessario aggiungere una nuova voce nella tabella DNS per il dominio personalizzato con gli strumenti disponibili in registrar. Ogni registrar dispone di un metodo simile ma leggermente diverso per specificare un record CNAME, ma i concetti sono identici.

1. Utilizzare uno di questi metodi per trovare il **. cloudapp.net** nome di dominio assegnato al servizio cloud.

    * Accesso al [portale classica Azure], selezionare il servizio cloud, selezionare **Dashboard**e quindi individuare la voce **Dell'URL del sito** nella sezione **visualizzazione rapida** .
    
        ![sezione visualizzazione rapida che mostra l'URL del sito][csurl]
    
        **OR**  
    
    * Installare e configurare [Powershell Azure](../powershell-install-configure.md)e quindi usare il comando seguente:
        
        ```powershell
        Get-AzureDeployment -ServiceName yourservicename | Select Url
        ```
    
    Salvare il nome di dominio utilizzato in URL restituito da dei metodi descritti, sarà necessario durante la creazione di un record CNAME.

1.  Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**.

2.  Ora trova nel punto in cui è possibile selezionare o immettere del record CNAME. È necessario selezionare il tipo di record da un elenco a discesa verso il basso o passare a una pagina di impostazioni avanzate. È necessario cercare le parole **CNAME** **Alias**o **sottodomini**.

3.  È anche necessario fornire il dominio o sottodominio l'alias per il record CNAME, ad esempio **www** se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare un alias per il dominio radice, potrebbe essere elencato come il '**@**' simbolo di strumenti DNS del registrar.

4. Quindi, è necessario specificare un nome host canonico, ossia **cloudapp.net** dominio dell'applicazione in questo caso.

Ad esempio, il record CNAME seguente inoltra tutto il traffico da **www.contoso.com** a **contoso.cloudapp.net**, il nome di dominio personalizzato dell'applicazione distribuita:

| Nome Host o alias/sottodominio | Dominio canonico     |
| ------------------------- | -------------------- |
| www                       | Contoso.cloudapp.NET |

Un visitatore di **www.contoso.com** mai vedranno host true (contoso.cloudapp.net), in modo che il processo di inoltro non è visibile all'utente finale.

> [AZURE.NOTE]
> Nell'esempio sopra solo per il traffico sottodominio **www** . Poiché i record CNAME, è possibile utilizzare caratteri jolly, è necessario creare un record CNAME per ogni dominio/sottodominio. Se si desidera indirizzare il traffico da sottodomini, ad esempio \*. contoso.com, all'indirizzo cloudapp.net, è possibile configurare una voce di **Reindirizzamento URL** o **URL in avanti** nella sezione impostazioni DNS o creare un record.


## <a name="add-an-a-record-for-your-custom-domain"></a>Aggiungere un record per il dominio personalizzato

Per creare un record, è necessario trovare l'indirizzo IP virtuale del servizio cloud. Aggiungere una nuova voce nella tabella DNS per il dominio personalizzato usando gli strumenti disponibili in registrar. Ogni registrar dispone di un metodo simile ma leggermente diverso per specificare un record, ma i concetti sono identici.

1. Utilizzare uno dei metodi seguenti per ottenere l'indirizzo IP del servizio cloud.
    
    * accesso al [portale classica Azure], selezionare il servizio cloud, selezionare **Dashboard**e quindi individuare la voce della **Rubrica pubblica virtuale IP (VIP)** nella sezione **visualizzazione rapida** .
    
        ![sezione occhio con l'indirizzo VIP][vip]
    
        **OR**  
    
    * Installare e configurare [Powershell Azure](../powershell-install-configure.md)e quindi usare il comando seguente:
    
        ```powershell
        get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip
        ```
    
    Se si dispone di più endpoint associato al servizio cloud, si riceverà più righe che contengono l'indirizzo IP, ma tutti deve essere visualizzato lo stesso indirizzo.
    
    Salvare l'indirizzo IP, come sarà necessario durante la creazione di un record.

1.  Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**.

2.  Ora trova nel punto in cui è possibile selezionare o immettere un record. È necessario selezionare il tipo di record da un elenco a discesa verso il basso o passare a una pagina di impostazioni avanzate.

3. Selezionare o immettere il dominio o sottodominio che verrà utilizzato il record A. Selezionare **www** , ad esempio, se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__'. Questa operazione verrà illustrate le caratteristiche tutti i sottodomini, ad esempio **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

    Se si desidera creare un record per il dominio radice, potrebbe essere elencato come il '**@**' simbolo di strumenti DNS del registrar.

4. Immettere l'indirizzo IP del servizio cloud nel campo fornito. In questo modo viene usata nel record con l'indirizzo IP della distribuzione di servizio cloud la voce di dominio.

Ad esempio, che un record inoltra tutto il traffico da **contoso.com** a **137.135.70.239**, l'indirizzo IP dell'applicazione distribuita:

| Nome host/sottodominio | Indirizzo IP     |
| ------------------- | -------------- |
| @                   | 137.135.70.239 |



In questo esempio viene illustrata la creazione di un record a per il dominio radice. Se si desidera creare una voce con caratteri jolly per coprire tutti i sottodomini, è necessario immettere '__*__' come il sottodominio.

>[AZURE.WARNING]
>Indirizzi IP in Azure sono dinamici per impostazione predefinita. Probabilmente desiderata usare un [indirizzo IP riservato](../virtual-network/virtual-networks-reserved-public-ip.md) per assicurare che l'indirizzo IP non modificata.

## <a name="next-steps"></a>Passaggi successivi

* [Come gestire servizi Cloud](cloud-services-how-to-manage.md)
* [Come eseguire il mapping CDN contenuto a un dominio personalizzato](../cdn/cdn-map-content-to-custom-domain.md)
* [Configurazione generale del servizio cloud](cloud-services-how-to-configure.md).
* Informazioni su come [distribuire un servizio cloud](cloud-services-how-to-create-deploy.md).
* Configurare [i certificati ssl](cloud-services-configure-ssl-certificate.md).




[Expose Your Application on a Custom Domain]: #access-app
[Add a CNAME Record for Your Custom Domain]: #add-cname
[Expose Your Data on a Custom Domain]: #access-data
[VIP swaps]: http://msdn.microsoft.com/library/ee517253.aspx
[Create a CNAME record that associates the subdomain with the storage account]: #create-cname
[Portale classica Azure]: https://manage.windowsazure.com
[Validate Custom Domain dialog box]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
[vip]: ./media/cloud-services-custom-domain-name/csvip.png
[csurl]: ./media/cloud-services-custom-domain-name/csurl.png
 