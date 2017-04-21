#<a name="configuring-a-custom-domain-name-for-an-azure-website"></a>Configurazione di un nome di dominio personalizzato per un sito Web di Azure

Quando si crea un sito Web, Azure fornisce un sottodominio descrittivo nel dominio azurewebsites.net in modo che gli utenti possono accedere al sito Web tramite un URL, ad esempio http://&lt;sito personale >. azurewebsites.net. Tuttavia, se si configura i siti Web per Shared o modalità Standard, è possibile mappare il sito Web per il proprio nome di dominio.

Facoltativamente, è possibile utilizzare gestore del traffico Azure per caricare il traffico in ingresso saldo al sito Web. Per ulteriori informazioni sul funzionamento di gestore del traffico di siti Web, vedere [Controllare il traffico di siti Web di Azure con Azure il traffico Manager][trafficmanager].

> [AZURE.NOTE] Le procedure in questa attività si applicano ai siti Web di Azure; per i servizi Cloud, vedere <a href="/develop/net/common-tasks/custom-dns/">configurazione di un nome di dominio personalizzato in Azure</a>.

> [AZURE.NOTE] La procedura descritta in questa attività è necessario configurare i siti Web per Shared o modalità Standard, che può cambiare quantità vengono addebitate per l'abbonamento. Per ulteriori informazioni, vedere <a href="/pricing/details/web-sites/">Dettagli prezzi siti Web</a> .

In questo articolo:

-   [Informazioni sui record CNAME e](#understanding-records)
-   [Configurare i siti web per la modalità condivisa o standard](#bkmk_configsharedmode)
-   [Aggiungere i siti web per il traffico Manager](#trafficmanager)
-   [Aggiungere un record CNAME per il dominio personalizzato](#bkmk_configurecname)
-   [Aggiungere un record per il dominio personalizzato](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>Comprendere i record CNAME e</h2>

CNAME (alias record o) e un record è possibile associare un nome di dominio a un sito Web, ma ognuna di esse hanno un funzionamento diverso.

###<a name="cname-or-alias-record"></a>Record CNAME o Alias

Un record CNAME esegue il mapping di un dominio *specifico* , ad esempio **contoso.com** o **www.contoso.com**, un nome di dominio canonico. In questo caso, il nome di dominio canonico è l'il ** &lt;applicazione >. azurewebsites.net** nome di dominio del sito Web Azure o ** &lt;applicazione >. trafficmgr.com** nome di dominio del proprio profilo di gestore del traffico. Una volta creato, il record CNAME crea un alias per il ** &lt;applicazione >. azurewebsites.net** o ** &lt;applicazione >. trafficmgr.com** nome di dominio. La voce CNAME risolverà all'indirizzo IP del ** &lt;applicazione >. azurewebsites.net** o ** &lt;applicazione >. trafficmgr.com** dominio nome automaticamente, se l'indirizzo IP del sito Web cambia, non è necessario eseguire altre azioni.

> [AZURE.NOTE] Alcuni Registrar Consenti solo eseguire il mapping sottodomini quando si utilizza un record CNAME, ad esempio www.contoso.com e non i nomi di radice, ad esempio contoso.com. Per ulteriori informazioni sui record CNAME, vedere la documentazione fornita dal registrar, <a href="http://en.wikipedia.org/wiki/CNAME_record">la voce di Wikipedia sul record CNAME</a>o il documento di <a href="http://tools.ietf.org/html/rfc1035">Nomi di dominio IETF - implementazione e specifiche</a> .

###<a name="a-record"></a>Un record

Un record a associa un dominio, ad esempio **contoso.com** o **www.contoso.com**, *o di un dominio con caratteri jolly* come ** \*. contoso.com**, a un indirizzo IP. In caso di un sito Web Azure, l'indirizzo IP virtuale del servizio o un indirizzo IP specifico indirizzo a cui si è acquistato per il sito Web. Il vantaggio di un record su un record CNAME è possibile avere una voce che utilizza un carattere jolly, ad esempio * **. contoso.com**, che da gestire le richieste di più sottodomini, ad esempio * *mail.contoso.com**, * *login.contoso.com**, o * *www.contso.com**.

> [AZURE.NOTE] Poiché un record è associato a un indirizzo IP statico, non può risolvere automaticamente le modifiche apportate all'indirizzo IP del sito Web. Viene fornito un indirizzo IP per l'utilizzo con un record per configurare le impostazioni di nome di dominio personalizzato per il sito Web; Questo valore può variare, tuttavia, se si elimina e ricreare il sito Web o modificare la modalità di sito Web per eseguire il backup per rendere disponibile.

> [AZURE.NOTE] Un record non può essere utilizzato per con Manager il traffico di bilanciamento del carico. Per ulteriori informazioni, vedere [Controllare il traffico di siti Web di Azure con Azure il traffico Manager][trafficmanager].

<a name="bkmk_configsharedmode"></a><h2>Configurare i siti Web per la modalità condivisa o standard</h2>

Impostazione di un nome di dominio personalizzato in un sito Web è disponibile solo per le modalità Standard per i siti Web Azure condivisi. Prima di passare a un sito Web dalla modalità di sito Web gratuito per condivisi o sito Web Standard, è necessario rimuovere spesa BLOC MAIUSC in posizione per l'abbonamento a sito Web. Per ulteriori informazioni sulla condivisione e Standard modalità prezzi, vedere [I dettagli prezzi][PricingDetails].

1. Nel browser, aprire il [Portale di gestione][portal].
2. Nella scheda **siti Web** , fare clic sul nome del sito.

    ![][standardmode1]

3. Fare clic sulla scheda **scala** .

    ![][standardmode2]


4. Nella sezione **Generale** , impostare la modalità di sito Web, fare clic su **condivisi**.

    ![][standardmode3]

    > [AZURE.NOTE] Se si userà il traffico Manager con il sito Web, è necessario utilizzare selezionare modalità Standard anziché condivisi.

5. Fare clic su **Salva**.
6. Quando viene chiesto di aumento dei costi per modalità condivisa (o per la modalità Standard se si sceglie Standard), fare clic su **Sì** se l'utente acconsente.

    <!--![][standardmode4]-->

    **Nota**<br />
   Se si riceve un errore di "Scala di configurazione per sito Web 'nome del sito Web' non è riuscita", è possibile utilizzare il pulsante Dettagli per ottenere ulteriori informazioni.

<a name="trafficmanager"></a><h2>(Facoltativo) Aggiungere i siti Web per il traffico Manager</h2>

Se si desidera utilizzare il sito Web con il traffico Manager, eseguire la procedura seguente.

1. Se non si dispone già di un profilo di gestore del traffico, usare le informazioni in [creare un profilo di gestore del traffico utilizzando Creazione rapida] [ createprofile] crearne uno. Nota la **. trafficmgr.com** nome di dominio associato il profilo di gestore del traffico. Questa verrà utilizzata in un passaggio successivo.

2. Usare le informazioni di [aggiungere o eliminare i punti finali] [ addendpoint] per aggiungere il sito Web come un endpoint nel profilo di gestore del traffico.

    > [AZURE.NOTE] Se il sito Web non è disponibile durante l'aggiunta di un estremo, verificare che sia configurato per la modalità Standard. È necessario utilizzare la modalità Standard per il sito Web per lavorare con il traffico Manager.

3. Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**.

4. Ora trova nel punto in cui è possibile selezionare o immettere i record CNAME. È necessario selezionare il tipo di record da un elenco a discesa verso il basso o passare a una pagina di impostazioni avanzate. È necessario cercare le parole **CNAME** **Alias**o **sottodomini**.

5. È anche necessario fornire il dominio o sottodominio l'alias per il record CNAME. Ad esempio **www** se si desidera creare un alias per **www.customdomain.com**.

5. È anche necessario specificare un nome host che rappresenta il nome di dominio canonico per questo alias CNAME. Si tratta di **. trafficmgr.com** nome per il sito Web.

Ad esempio, il record CNAME seguente inoltra tutto il traffico da **www.contoso.com** a **contoso.trafficmgr.com**, il nome di dominio di un sito Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome Host o alias/sottodominio</strong></td>
<td><strong>Dominio canonico</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.trafficmgr.com</td>
</tr>
</table>

Un visitatore di **www.contoso.com** mai vedranno host true (contoso.azurewebsite.net), in modo che il processo di inoltro non è visibile all'utente finale.

> [AZURE.NOTE] Se si utilizza il traffico di gestione di un sito Web, non occorre seguire la procedura descritta nelle sezioni seguenti '**Aggiungi un record CNAME per il dominio personalizzato**e '**Aggiungi un record per il dominio personalizzato**. Il record CNAME creato in precedenza indirizza il traffico in arrivo per il traffico Manager, quindi indirizza il traffico al endpoint sito Web.

<a name="bkmk_configurecname"></a><h2>Aggiungere un record CNAME per il dominio personalizzato</h2>

Per creare un record CNAME, è necessario aggiungere una nuova voce nella tabella DNS per il dominio personalizzato con gli strumenti forniti dal registrar. Ogni registrar dispone di un metodo simile ma leggermente diverso per specificare un record CNAME, ma i concetti sono identici.

1. Utilizzare uno di questi metodi per trovare il **. azurewebsite.net** nome di dominio assegnato al sito Web.

    * Accesso al [Portale di gestione Azure][portal], selezionare il sito Web, selezionare **Dashboard**e quindi individuare la voce **Dell'URL del sito** nella sezione **visualizzazione rapida** .

    * Installare e configurare [Powershell Azure](/manage/install-and-configure-windows-powershell/)e quindi usare il comando seguente:

            get-azurewebsite yoursitename | select hostnames

    * Installare e configurare l' [interfaccia della riga di comando Azure](/manage/install-and-configure-cli/)e quindi usare il comando seguente:

            azure site domain list yoursitename

    Consente di salvare questo **. azurewebsite.net** name, come verrà utilizzato nei passaggi seguenti.

3. Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**.

4. Ora trova nel punto in cui è possibile selezionare o immettere i record CNAME. È necessario selezionare il tipo di record da un elenco a discesa verso il basso o passare a una pagina di impostazioni avanzate. È necessario cercare le parole **CNAME** **Alias**o **sottodomini**.

5. È anche necessario fornire il dominio o sottodominio l'alias per il record CNAME. Ad esempio **www** se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare un alias per il dominio radice, potrebbe essere elencato come il '**@**' simbolo di strumenti DNS del registrar.

5. È anche necessario specificare un nome host che rappresenta il nome di dominio canonico per questo alias CNAME. Si tratta di **. azurewebsite.net** nome per il sito Web.

Ad esempio, il record CNAME seguente inoltra tutto il traffico da **www.contoso.com** a **contoso.azurewebsite.net**, il nome di dominio di un sito Web:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>Nome Host o alias/sottodominio</strong></td>
<td><strong>Dominio canonico</strong></td>
</tr>
<tr>
<td>www</td>
<td>Contoso.azurewebsite.NET</td>
</tr>
</table>

Un visitatore di **www.contoso.com** mai vedranno host true (contoso.azurewebsite.net), in modo che il processo di inoltro non è visibile all'utente finale.

> [AZURE.NOTE] Nell'esempio sopra solo per il traffico sottodominio __www__ . Poiché i record CNAME, è possibile utilizzare caratteri jolly, è necessario creare un record CNAME per ogni dominio/sottodominio. Se si desidera indirizzare il traffico da sottodomini, ad esempio *. contoso.com, all'indirizzo azurewebsite.net, è possibile configurare una voce di __Reindirizzamento URL__ o __URL in avanti__ nella sezione impostazioni DNS o creare un record.

> [AZURE.NOTE] È possibile richiedere del tempo per il record CNAME per propagarsi nel sistema DNS. Fino a quando il record CNAME è propagato non è possibile impostare il record CNAME per il sito Web. È possibile utilizzare un servizio, ad esempio <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare che il record CNAME è disponibile.

###<a name="add-the-domain-name-to-your-website"></a>Aggiungere il nome di dominio al sito Web

Dopo il record CNAME per nome di dominio è propagato, è necessario associare il sito Web. È possibile aggiungere il nome di dominio personalizzati definito per il record CNAME per il sito Web mediante l'interfaccia a riga di comando Azure (Azure CLI) o tramite il portale di gestione di Azure.

**Per aggiungere un nome di dominio l'utilizzo della riga di comando degli strumenti**

Installare e configurare l' [interfaccia di Azure riga di comando](/manage/install-and-configure-cli/)e quindi usare il comando seguente:

    azure site domain add customdomain yoursitename

Ad esempio, le operazioni seguenti aggiungerà un nome di dominio personalizzato di **www.contoso.com** al sito Web **contoso.azurewebsite.net** :

    azure site domain add www.contoso.com contoso

È possibile verificare che il nome di dominio personalizzato è stato aggiunto al sito Web usando il comando seguente:

    azure site domain list yoursitename

L'elenco restituito da questo comando deve contenere il nome di dominio personalizzato, come il valore predefinito **. azurewebsite.net** voce.

**Per aggiungere un nome di dominio tramite il portale di gestione di Azure**

1. Nel browser, aprire il [Portale di gestione Azure][portal].

2. Nella scheda **siti Web** , fare clic sul nome del sito, selezionare **Dashboard**e quindi selezionare **Manage Domains** dalla parte inferiore della pagina.

    ![][setcname2]

6. Nella casella di testo **I nomi di dominio** digitare il nome di dominio è stato configurato.

    ![][setcname3]

6. Fare clic sul segno di spunta per accettare il nome di dominio.

Al termine di configurazione, il nome di dominio personalizzato verrà elencato nella sezione **nomi di dominio** della pagina di **configurazione** del sito Web.

<a name="bkmk_configurearecord"></a><h2>Aggiungere un Record per il dominio personalizzato</h2>

Per creare un record, è necessario trovare l'indirizzo IP del sito Web. Aggiungere una voce nella tabella DNS per il dominio personalizzato usando gli strumenti disponibili in registrar. Ogni registrar dispone di un metodo simile ma leggermente diverso per specificare un record, ma i concetti sono identici. Oltre a creare un record, è necessario creare anche un record CNAME Azure utilizzato da per verificare il record.

1. Nel browser, aprire il [Portale di gestione Azure][portal].

2. Nella scheda **siti Web** , fare clic sul nome del sito, selezionare **Dashboard**e quindi selezionare **Manage Domains** dal basso verso l'alto.

    ![][setcname2]

5. Nella finestra di dialogo **gestione dei domini personalizzati** , individuare **L'indirizzo IP da utilizzare durante la configurazione di un record**. Copiare l'indirizzo IP. Verrà utilizzato quando si crea un record.

5. Nella finestra di dialogo **gestione dei domini personalizzati** , nota nome di dominio awverify alla fine del testo nella parte superiore della finestra di dialogo. Dovrebbe essere **awverify.mysite.azurewebsites.net** dove **sito personale** è il nome del sito Web. Copiare la verifica, in quanto è il nome di dominio utilizzato per la creazione di verifica record CNAME.

6. Accedere al sito Web del registrar DNS e passare alla pagina per la gestione DNS. Cercare i collegamenti o aree del sito etichettata con **Nome di dominio**, **DNS**o **Gestione di nome Server**.

6. Trova nel punto in cui è possibile selezionare o immettere record CNAME e. È necessario selezionare il tipo di record da un elenco a discesa o passare a una pagina di impostazioni avanzate.

7. Per creare il record, procedere come segue:

    1. Selezionare o immettere il dominio o sottodominio che verrà utilizzato il record. Selezionare **www** , ad esempio, se si desidera creare un alias per **www.customdomain.com**. Se si desidera creare una voce con caratteri jolly per tutti i sottodomini, immettere '__*__'. Questa operazione verrà illustrate le caratteristiche tutti i sottodomini, ad esempio **mail.customdomain.com**, **login.customdomain.com**e **www.customdomain.com**.

        Se si desidera creare un record per il dominio radice, potrebbe essere elencato come il '**@**' simbolo di strumenti DNS del registrar.

    2. Immettere l'indirizzo IP del servizio cloud nel campo fornito. In questo modo viene usata nel record con l'indirizzo IP della distribuzione di servizio cloud la voce di dominio.

        Ad esempio, che un record inoltra tutto il traffico da **contoso.com** a **137.135.70.239**, l'indirizzo IP dell'applicazione distribuito:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>Nome host/sottodominio</strong></td>
        <td><strong>Indirizzo IP</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        In questo esempio viene illustrata la creazione di un record a per il dominio radice. Se si desidera creare una voce con caratteri jolly per coprire tutti i sottodomini, è necessario immettere '__*__' come il sottodominio.

7. Creare un record CNAME con un alias di **awverify**e un dominio canonico di **awverify.mysite.azurewebsites.net** ottenuto in precedenza.

    > [AZURE.NOTE] Mentre un alias di awverify potrebbe essere disponibile per alcuni Registrar in altri può richiedere il nome di dominio completo di alias di awverify.www.customdomainname.com o awverify.customdomainname.com.

    Ad esempio, le operazioni seguenti creerà un record CNAME Azure consente di verificare la configurazione di un record.

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>Nome Host o alias/sottodominio</strong></td>
    <td><strong>Dominio canonico</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.NET</td>
    </tr>
    </table>

> [AZURE.NOTE] È possibile richiedere del tempo awverify CNAME a propagarsi nel sistema DNS. Non è possibile impostare il nome di dominio personalizzato definito da record a per il sito Web fino a quando non è propagato awverify CNAME. È possibile utilizzare un servizio, ad esempio <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> per verificare che il record CNAME è disponibile.

###<a name="add-the-domain-name-to-your-website"></a>Aggiungere il nome di dominio al sito Web

Dopo il record CNAME **awverify** per nome di dominio è propagato, è possibile associare quindi il dominio personalizzato definito per il record con il sito Web. È possibile aggiungere il nome di dominio personalizzato definito dal record al sito Web tramite CLI Azure o se tramite il portale di gestione di Azure.

**Per aggiungere un nome di dominio tramite l'interfaccia della riga di comando di Azure (Azure CLI)**

Installare e configurare [CLI Azure](/manage/install-and-configure-cli/)e quindi usare il comando seguente:

    azure site domain add customdomain yoursitename

Ad esempio, le operazioni seguenti aggiungerà un nome di dominio personalizzato di **contoso.com** al sito Web **contoso.azurewebsite.net** :

    azure site domain add contoso.com contoso

È possibile verificare che il nome di dominio personalizzato è stato aggiunto al sito Web usando il comando seguente:

    azure site domain list yoursitename

L'elenco restituito da questo comando deve contenere il nome di dominio personalizzato, come il valore predefinito **. azurewebsite.net** voce.

**Per aggiungere un nome di dominio tramite il portale di gestione di Azure**

1. Nel browser, aprire il [Portale di gestione Azure][portal].

2. Nella scheda **siti Web** , fare clic sul nome del sito, selezionare **Dashboard**e quindi selezionare **Manage Domains** dalla parte inferiore della pagina.

    ![][setcname2]

6. Nella casella di testo **I nomi di dominio** digitare il nome di dominio è stato configurato.

    ![][setcname3]

6. Fare clic sul segno di spunta per accettare il nome di dominio.

Al termine di configurazione, il nome di dominio personalizzato verrà elencato nella sezione **nomi di dominio** della pagina di **configurazione** del sito Web.

> [AZURE.NOTE] Dopo aver aggiunto il nome di dominio personalizzato definito dal record al sito Web, è possibile rimuovere il record CNAME awverify gli strumenti disponibili per il registrar. Tuttavia, se si desidera aggiungere A un altro record in futuro, è necessario ricreare i awverify record prima di poter associare il nuovo nome di dominio definiti per il nuovo record con il sito Web.

## <a name="next-steps"></a>Passaggi successivi

-   [Come gestire i siti web](/manage/services/web-sites/how-to-manage-websites/)

-   [Configurare un certificato SSL per i siti Web](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png
