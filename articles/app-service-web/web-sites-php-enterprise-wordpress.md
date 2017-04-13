<properties
    pageTitle="WordPress aziendale del servizio di App Azure | Microsoft Azure"
    description="Informazioni su come pubblicare un sito di WordPress aziendale del servizio di App Azure"
    services="app-service\web"
    documentationCenter=""
    authors="sunbuild"
    manager="yochayk"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.devlang="php"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="10/24/2016"
    ms.author="sumuth"/>

# <a name="enterprise-class-wordpress-on-azure-app-service"></a>WordPress aziendali Azure del servizio di App

Servizio di App Azure offre un ambiente di scalabilità, sicuro e facile da usare per missione critiche di grandi dimensioni scala [WordPress] [ wordpress] siti. Microsoft ha eseguito siti di livello aziendale, ad esempio [Office] [ officeblog] e [Bing] [ bingblog] blog. In questo documento viene visualizzato come è possibile utilizzare Azure App servizio Web Apps per stabilire e mantenere un livello aziendale, sito WordPress basato su cloud in grado di gestire un volume elevato di visitatori.

## <a name="architecture-and-planning"></a>Architettura e pianificazione

Un'installazione WordPress base è sufficienti due requisiti.

* **MySQL Database** - disponibili tramite [ClearDB in Azure Marketplace][cdbnstore], oppure è possibile gestire l'installazione di MySQL in macchine virtuali di Azure utilizzando uno dei due [Windows] [ mysqlwindows] o [Linux][mysqllinux].

  > [AZURE.NOTE] ClearDB sono disponibili varie configurazioni MySQL, con caratteristiche diverse per ogni configurazione. Vedere l' [Archivio di Azure] [ cdbnstore] per informazioni sulle offerte fornito tramite l'archivio di Azure o direttamente visto nel [sito Web ClearDB](http://www.cleardb.com/pricing.view).

* **PHP 5.2.4 o versione successiva** -servizio App Azure fornisce attualmente [versioni PHP 5.4, 5,5 e 5.6][phpwebsite].

    > [AZURE.NOTE] È consigliabile eseguire sempre alla versione più recente di PHP avere a disposizione le correzioni di protezione più recenti.

### <a name="basic-deployment"></a>Distribuzione di base

Usa i requisiti di base, è possibile creare una soluzione di base all'interno di un'area di Azure.

![un'app web Azure e MySQL Database inserito in una singola regione Azure][basic-diagram]

Mentre in questo modo è possibile scalabilità dell'applicazione tramite la creazione di più istanze di applicazioni Web del sito, tutti gli elementi è ospitato all'interno di centri dati in una specifica località geografica. Ai visitatori di all'esterno di quest'area venga visualizzata tempi di risposta lunghi quando si usa il sito e i data center in quest'area passare verso il basso, quindi non l'applicazione.


### <a name="multi-region-deployment"></a>Distribuzione di più paesi

Con Azure [Gestore del traffico][trafficmanager], è possibile modificare la scala del sito WordPress in più aree geografiche diverse fornendo solo un URL per i visitatori. Tutti i visitatori del risultare tramite il traffico Manager e quindi indirizzati a un'area in base alla configurazione di bilanciamento del carico.

![un'app web Azure ospitata in più aree, utilizzando router CDBR disponibilità per il routing a MySQL tra le aree][multi-region-diagram]

All'interno di ogni regione, il sito WordPress sarebbe comunque ridimensionato tra più istanze di applicazioni Web, ma il ridimensionamento è area specifica; aree di traffico elevato possono essere ridimensionate in modo diverso rispetto a quelle di traffico ridotto.

Replica e routing a più database MySQL possono essere eseguiti utilizzando del ClearDB [CDBR alta disponibilità Router] [ cleardbscale] (visualizzata a sinistra) o [Esegue questa operazione Cluster MySQL][cge].

### <a name="multi-region-deployment-with-media-storage-and-caching"></a>Distribuzione di più aree con lo spazio di archiviazione di elementi multimediali e la memorizzazione nella cache
Se il sito accetta i caricamenti o i file multimediali host, utilizzare lo spazio di archiviazione Blob Azure. Se è necessaria la memorizzazione nella cache, è consigliabile [Redis cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/)o una delle altre offerte memorizzazione nella cache nell' [Archivio di Azure](http://azure.microsoft.com/gallery/store/).

![un'app web Azure ospitata in più aree, utilizzando router CDBR disponibilità per MySQL, con Cache gestiti, archiviazione Blob e CDN][performance-diagram]

Archiviazione BLOB è geografico distribuiti in aree geografiche per impostazione predefinita, in modo da non dover preoccuparsi di file di replica in tutti i siti. È inoltre possibile abilitare Azure [Rete di distribuzione del contenuto (CDN)] [ cdn] per l'archiviazione Blob, che distribuisce file alla fine nodi per i visitatori.

### <a name="planning"></a>Pianificazione

#### <a name="additional-requirements"></a>Requisiti aggiuntivi

Per eseguire questa operazione... | Utilizzare questa...
------------------------|-----------
**Caricare o archiviare i file di grandi dimensioni** | [Plug-in WordPress per l'uso di archiviazione Blob][storageplugin]
**Inviare posta elettronica** | [SendGrid] [ storesendgrid] e [plug-in WordPress per l'uso di SendGrid][sendgridplugin]
**Nomi di dominio personalizzato** | [Configurare un nome di dominio personalizzato nel servizio di App Azure][customdomain]
**HTTPS** | [Abilitare HTTPS per un'app web nel servizio App Azure][httpscustomdomain]
**Convalida pre-produzione** | [Configurare gli ambienti per web apps in Azure App servizio di gestione temporanea][staging] <p>Cambio di un'app web di gestione temporanea di produzione anche Sposta configurazione WordPress. È necessario assicurarsi che vengono aggiornate tutte le impostazioni per i requisiti per l'app di produzione prima di passare a fasi app nell'ambiente di produzione.</p>
**Monitoraggio e risoluzione dei problemi** | [Attivare la registrazione diagnostica per web apps in Azure App servizio] [ log] e [Monitor Web Apps nel servizio App Azure][monitor]
**Distribuire il sito** | [Distribuire un'app web nel servizio App Azure][deploy]

#### <a name="availability-and-disaster-recovery"></a>Disponibilità e ripristino di emergenza

Per eseguire questa operazione... | Utilizzare questa...
------------------------|-----------
**Siti di bilanciamento carico** o **geografico-distribuire siti** | [Instradare il traffico con Azure il traffico Manager][trafficmanager]
**Eseguire il backup e ripristino** | [Eseguire il backup di un'app web nel servizio App Azure] [ backup] e [ripristinare un'app web nel servizio App Azure][restore]

#### <a name="performance"></a>Prestazioni

Le prestazioni nel cloud è possibile principalmente tramite la memorizzazione nella cache e scalabilità; Tuttavia la memoria, della larghezza di banda e altri attributi di hosting Web Apps in considerazione.

Per eseguire questa operazione... | Utilizzare questa...
------------------------|-----------
**Informazioni sulle funzionalità di istanza del servizio di App** | [Informazioni sui prezzi, incluse le funzionalità dei livelli di servizio di App][websitepricing]
**Nella cache le risorse** | [Redis cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/)o una delle altre offerte memorizzazione nella cache nell' [Archivio di Azure](/gallery/store/)
**Ridimensionare l'applicazione** | [Ridimensionare un'app web nel servizio App Azure] [ websitescale] e [Il Routing disponibilità ClearDB alta][cleardbscale]. Se si sceglie di ospitare e gestire l'installazione di MySQL, è necessario prendere in considerazione [Esegue questa operazione Cluster MySQL] [ cge] per scalabilità

#### <a name="migration"></a>Migrazione

Esistono due metodi per la migrazione di un sito esistente WordPress al servizio App Azure.

* ** [WordPress esportare] [ export] ** -consente di esportare il contenuto del blog, può essere importato in un nuovo sito WordPress Azure App servizio usando il [plug-in utilità di importazione WordPress][import].

    > [AZURE.NOTE] Durante questo processo consente di eseguire la migrazione del contenuto, non vengono migrati eventuali plug-in, temi o altre personalizzazioni. Deve essere installati nuovo manualmente.

* **Migrazione manuale** - [eseguire il backup del sito] [ wordpressbackup] e [database][wordpressdbbackup], quindi manualmente ripristinarlo in un'app web nel servizio App Azure e associato database MySQL per eseguire la migrazione di siti altamente personalizzati ed evitare la necessità di installare manualmente plug-in, temi e altre personalizzazioni.

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

### <a name="create-a-wordpress-site"></a>Creare un sito WordPress

1. Utilizzo di [Azure Marketplace] [ cdbnstore] per creare un database MySQL delle dimensioni che sono stati identificati nella sezione [architettura e pianificazione](#planning) nella zona che ospitare il sito.

2. Seguire la procedura descritta in [creare un'app web WordPress nel servizio App Azure] [ createwordpress] per creare un'app web WordPress. Quando si crea l'applicazione web, selezionare **Usa un MySQL Database esistente** e selezionare il database creato nel passaggio 1.

Se si esegue la migrazione di un sito WordPress esistente, vedere [eseguire la migrazione di un sito WordPress esistente in Azure](#Migrate-an-existing-WordPress-site-to-Azure) dopo aver creato una nuova app web.

### <a name="migrate-an-existing-wordpress-site-to-azure"></a>Eseguire la migrazione di un sito WordPress esistente in Azure

Come indicato nella sezione [architettura e pianificazione](#planning) , esistono due modi per eseguire la migrazione di un sito WordPress.

* **esportare e importare** - per i siti senza troppa personalizzazione o nel punto in cui si vuole semplicemente spostare il contenuto.

* **eseguire il backup e ripristino** - per i siti con numerose personalizzazione nel punto in cui si desidera spostare tutti gli elementi.

Usare una delle sezioni seguenti per eseguire la migrazione del sito.

#### <a name="the-export-and-import-method"></a>Il metodo di importazione ed esportazione

1. Scegliere [Esporta WordPress] [ export] esportare sito esistente.

2. Creare un'app web usando la procedura descritta nella sezione [creare un sito WordPress](#Create-a-new-WordPress-site) .

3. Accedere al sito WordPress nella Web App e fare clic su **plug-in** -> **Add New**. Cercare e installare il plug-in **WordPress importazione** .

4. Dopo aver installato il plug-in di importazione, fare clic su **Strumenti** -> **importazione**e quindi selezionare **WordPress** a usare il plug-in utilità di importazione WordPress.

5. Nella pagina **Importa WordPress** , fare clic su **Scegli File**. Individuare il file WXR esportato dal sito WordPress esistente e quindi scegliere **Carica file e importare**.

6. Fare clic su **Invia**. Verrà richiesto di importazione completata.

8. Dopo aver completato tutte queste operazioni, riavviare il sito dalla relativa blade app web nel [portale di Azure][mgmtportal].

Dopo avere importato il sito, potrebbe essere necessario eseguire la procedura seguente per abilitare le impostazioni non contenute nel file di importazione.

Se si utilizzava... | Operazione da eseguire …
------------------ | ----------
**Collegamenti permanenti** | Dal dashboard WordPress del nuovo sito, fare clic su **Impostazioni** -> **permanenti** e quindi aggiornare la struttura permanenti
**collegamenti immagine/multimediali** | Per aggiornare i collegamenti nella nuova posizione, utilizzare il [plug-in di Velluto blu aggiornare URL][velvet], uno strumento di ricerca e sostituzione o manualmente nel database
**Temi** | Passare **all'aspetto** -> **il tema** e aggiornare il tema del sito in base alle esigenze
**Menu** | Se il tema supporta i menu, i collegamenti alla home page potrebbero essere presenti nella directory secondari vecchia incorporata. Passare **all'aspetto** -> **menu** e li aggiorna

#### <a name="the-backup-and-restore-method"></a>Il metodo di backup e ripristino

1. Eseguire il backup del WordPress esistente del sito mediante le informazioni relative al [backup WordPress][wordpressbackup].

2. Eseguire il backup del database esistente mediante le informazioni relative al [backup del database][wordpressdbbackup].

3. Creare un database e ripristinare il backup.

    1. Acquistare un nuovo database da [Azure Marketplace][cdbnstore], o configurazione di un database MySQL in un [Windows] [ mysqlwindows] o [Linux] [ mysqllinux] macchine Virtuali.

    2. Usare un client MySQL come [Area di lavoro MySQL][workbench], connettersi al database nuovo e importare il database WordPress.

    3. Aggiornare il database per modificare le voci di dominio per il nuovo dominio, servizio App Azure. Ad esempio mywordpress.azurewebsites.net. Utilizzare la [ricerca e sostituzione di Script di database WordPress] [ searchandreplace] in modo sicuro modificare tutte le istanze.

4. Creare un'app web nel portale di Azure e pubblicare il backup WordPress.

    1. Creare un'app web nel [portale di Azure] [ mgmtportal] con un database con **Nuovo** -> **Web + Mobile** -> **Azure Marketplace** -> **Web Apps** -> **Web app + SQL** (o **app Web + MySQL**) -> **Crea**. Configurare le impostazioni necessarie per creare un'app web vuoto.

    2. Nel processo di backup WordPress, individuare il file **config.php wp** e aprirlo in un editor. Sostituire le seguenti voci con le informazioni per il nuovo database MySQL.

        * **DB_NAME** - il nome utente del database

        * **DB_USER** - il nome utente utilizzato per accedere al database

        * **DB_PASSWORD** - la password dell'utente

        Dopo aver modificato queste voci, salvare e chiudere il file **config.php wp** .

    3. Utilizzare [distribuire un'app web nel servizio App Azure] [ deploy] informazioni per abilitare il metodo di distribuzione che si desidera utilizzare e distribuire il backup WordPress un'app web di Azure App servizio.

5. Dopo la distribuzione di siti WordPress, sarà possibile accedere di nuovo sito (come un'applicazione servizio web app) utilizzando il *. azurewebsite.net URL del sito.

### <a name="configure-your-site"></a>Configurare il sito

Dopo aver creato il sito WordPress o viene eseguita la migrazione, utilizzare le seguenti informazioni per migliorare le prestazioni o di attivare la funzionalità aggiuntiva.

Per eseguire questa operazione... | Utilizzare questa...
------------- | -----------
**Impostare la modalità di piano di servizio App, le dimensioni e proporzioni dei caratteri attiva** | [Ridimensionare un'app web nel servizio App Azure][websitescale]
**Attivare le connessioni di database permanente** | Per impostazione predefinita, WordPress non utilizza connessioni al database persistente, che possono causare la connessione al database per diventare limitato dopo più connessioni. Per abilitare le connessioni permanenti, installare il (connessioni permanenti scheda plug-in) [https://wordpress.org/plugins/persistent-database-connection-updater/installation/]. 
**Migliorare le prestazioni** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Disabilitare i cookie ARR</a> : migliorare le prestazioni durante l'esecuzione di WordPress su più istanze di Web Apps</p></li><li><p>Abilitare la memorizzazione nella cache. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis cache</a> (preview) da utilizzare con il <a href="https://wordpress.org/plugins/redis-object-cache/">Redis oggetto cache WordPress plug-in</a>oppure utilizzare una delle altre offerte di memorizzazione nella cache dall' <a href="/gallery/store/">Archivio di Azure</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Come rendere più velocemente WordPress con Wincache</a> - Wincache è attivata per impostazione predefinita per le applicazioni Web</p></li><li><p><a href="../web-sites-scale/">Scala a un'app web nel servizio App Azure</a> e usare <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB alta disponibilità</a> in ingresso o <a href="http://www.mysql.com/products/cluster/">Esegue questa operazione Cluster MySQL</a></p></li></ul>
**Usare BLOB per lo spazio di archiviazione** | <ol><li><p><a href="../storage-create-storage-account/">Creare un account di archiviazione Azure</a></p></li><li><p>Informazioni su come <a href="../cdn-how-to-use/">usare la rete di distribuzione del contenuto (CDN)</a> a geografico-distribuire i dati archiviati nei blob.</p></li><li><p>Installare e configurare l' <a href="https://wordpress.org/plugins/windows-azure-storage/">Archiviazione di Azure WordPress del plug-in</a>.</p><p>Per impostazione dettagliata e informazioni di configurazione per il plug-in, vedere la <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Guida dell'utente</a>.</p> </li></ol>
**Abilitare posta elettronica** | Abilitare <a href="https://azure.microsoft.com/en-us/marketplace/partners/sendgrid/sendgrid-azure/">SendGrid</a> tramite l'archivio di Azure. Installare il <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified">plug-in SendGrid</a> per WordPress.
**Configurare un nome di dominio personalizzato** | [Configurare un nome di dominio personalizzato nel servizio di App Azure][customdomain]
**Abilitare HTTPS per un nome di dominio personalizzato** | [Abilitare HTTPS per un'app web nel servizio App Azure][httpscustomdomain]
**Caricare il saldo o geografico-distribuire il sito** | [Indirizzare il traffico con Azure il traffico Manager][trafficmanager]. Se si utilizza un dominio personalizzato, vedere [configurare un nome di dominio personalizzato in Azure App servizio] [ customdomain] per informazioni sull'utilizzo di gestore del traffico con i nomi di dominio personalizzato
**Attivare backup automatici** | [Eseguire il backup un'app web nel servizio App Azure][backup]
**Attivare la registrazione diagnostica** | [Attivare la registrazione diagnostica per web apps nel servizio App Azure][log]

## <a name="next-steps"></a>Passaggi successivi

* [Ottimizzazione WordPress](http://codex.wordpress.org/WordPress_Optimization)

* [Convertire WordPress multisito nel servizio App Azure](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB aggiornamento guidato per Azure](http://www.cleardb.com/store/azure/upgrade)

* [In una sottocartella di un'app web di Azure App servizio di hosting WordPress](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Procedura dettagliata: Creare un sito di WordPress utilizzando Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Inserire il blog di WordPress esistente in Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Attivazione abbastanza permanenti in WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Come eseguire la migrazione ed eseguire il blog di WordPress Azure App servizio](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Come eseguire gratuitamente WordPress sul servizio App Azure](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress su Azure 2 minuti o meno](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Spostamento di un blog WordPress in Azure - parte 1: creare un blog WordPress su Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Spostamento di un blog WordPress in Azure - parte 2: il trasferimento del contenuto](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Spostamento di un blog WordPress in Azure - parte 3: configurare il dominio personalizzato](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Spostamento di un blog WordPress in Azure - parte 4: belle permanenti e le regole di riscrittura URL](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Spostamento di un blog WordPress in Azure - parte 5: da una sottocartella nella cartella radice](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Come configurare un'applicazione web WordPress nell'account Azure](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping la WordPress su Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Suggerimenti per WordPress su Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- URL List -->

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: ../traffic-manager/traffic-manager-overview.md
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: https://azure.microsoft.com/documentation/services/redis-cache/
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: ../virtual-machines/virtual-machines-windows-classic-mysql-2008r2.md
[mysqllinux]: ../virtual-machines/virtual-machines-linux-classic-mysql-on-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: ../powershell-install-configure.md
[Azure CLI]: ../xplat-cli-install.md
[storesendgrid]: https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/
[cdn]: ../cdn/cdn-overview.md
