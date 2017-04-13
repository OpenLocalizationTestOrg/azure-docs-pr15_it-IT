<properties 
    pageTitle="Convertire WordPress multisito nel servizio App Azure" 
    description="Informazioni su come scrivere un'app web WordPress esistente creata mediante la raccolta in Azure e convertirla in WordPress multisito" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>



# <a name="convert-wordpress-to-multisite-in-azure-app-service"></a>Convertire WordPress multisito nel servizio App Azure

## <a name="overview"></a>Panoramica

*Da [Ben Lobaugh][ben-lobaugh], [Microsoft Apri Technologies Inc.][ms-open-tech]*

In questa esercitazione si imparerà creare un'app web WordPress esistente creata mediante la raccolta in Azure e convertire in un'installazione WordPress multisito. Inoltre, si apprenderà come assegnare un dominio personalizzato a tutti i siti secondari all'interno dell'installazione.

Si presuppone che si dispone di un'installazione esistente di WordPress. In caso contrario, seguire le indicazioni fornite in [creare un sito web WordPress nella raccolta Azure][website-from-gallery].

La conversione di un WordPress esistente installa singolo sito per la funzionalità multisito in genere è semplice e molte delle fasi iniziali provenire direttamente da [Creare una rete] [ wordpress-codex-create-a-network] pagina [WordPress Codex](http://codex.wordpress.org).

Iniziamo.

## <a name="allow-multisite"></a>Consentire la funzionalità multisito

Prima di tutto necessario abilitare la funzionalità multisito tramite il `wp-config.php` file con il **WP\_Consenti\_MULTISITO** costante. Esistono due metodi per modificare i file di applicazione web: la prima consiste nell'usare FTP e il secondo attraverso operazioni. Se si ha familiarità con uno di questi metodi di configurazione, vedere le esercitazioni seguenti:

* [Sito web PHP con MySQL e FTP][website-w-mysql-and-ftp-ftp-setup]

* [Sito web PHP con MySQL e fra][website-w-mysql-and-git-git-setup]

Aprire la `wp-config.php` file con l'editor di propria scelta e aggiungere quanto segue sopra la `/* That's all, stop editing! Happy blogging. */` riga.

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

Assicurarsi di salvare il file e caricare al server.

## <a name="network-setup"></a>Configurazione di rete

Registra nell'area *wp amministrazione* del web app per verificare una nuova voce del menu **Strumenti** denominata **Configurazione di rete**. Fare clic su **Configurazione di rete** e inserire i dettagli della rete.

![Schermata di installazione di rete][wordpress-network-setup]

In questa esercitazione utilizza lo schema di sito *sottodirectory* poiché funziona sempre e si prevede di configurare domini personalizzati per ogni sito secondario in un secondo momento nell'esercitazione. Tuttavia, dovrebbe essere possibile per installare un sottodominio, se si esegue il mapping di un dominio tramite il [Portale di Azure](https://portal.azure.com) e configurazione con caratteri jolly DNS correttamente la configurazione.

Per ulteriori informazioni su sottodominio e impostazioni sottodirectory vedere i [tipi di rete multisito] [ wordpress-codex-types-of-networks] articolo su WordPress Codex.

## <a name="enable-the-network"></a>Attivare la rete

La rete è ora configurata nel database, ma esiste un unico passaggio rimanente per abilitare la funzionalità di rete. Finalizzare il `wp-config.php` impostazioni e assicurarsi che `web.config` correttamente indirizza ogni sito.


Dopo aver fatto clic sul pulsante **Installa** nella pagina *Configurazione di rete* , WordPress tenterà di aggiornare la `wp-config.php` e `web.config` file. Tuttavia, controllare sempre i file per assicurarsi che gli aggiornamenti riusciti. In caso contrario, questa schermata verrà presentati con gli aggiornamenti necessari. Modificare e salvare i file.


Dopo aver apportato gli aggiornamenti che sarà necessario disconnettersi e accedere nuovamente al dashboard di amministrazione di wp.

In Amministrazione barra etichetta **Siti personali**a questo punto dovrebbe essere un altro menu. In questo menu consente di controllare la nuova rete tramite il dashboard di **Amministratore di rete** .

## <a name="adding-custom-domains"></a>Aggiunta di domini personalizzati

[WordPress MU Domain Mapping] [ wordpress-plugin-wordpress-mu-domain-mapping] plug-in facilita aggiungere domini personalizzati in un sito qualsiasi della rete. Affinché il plug-in per il corretto funzionamento, è necessario eseguire alcune impostazioni aggiuntive nel portale e presso il registrar.

## <a name="enable-domain-mapping-to-the-web-app"></a>Abilitare il mapping di dominio in web app

La modalità di piano di [Servizio App](http://go.microsoft.com/fwlink/?LinkId=529714) **gratuito** non supporta l'aggiunta di domini personalizzati alle applicazioni Web. È necessario passare alla modalità **condivisa** o **Standard** . Per procedere come segue:

* Accedere al portale di Azure e individuare l'app web. 
* Fare clic sulla scheda **scalare** in **Impostazioni**.
* In **Generale**, selezionare *condiviso* o *STANDARD*
* Fare clic su **Salva**

Si potrebbe ricevere un messaggio che chiede di verificare la modifica e confermare che l'app web ora può causare un costo in base all'utilizzo e la configurazione di altra che si imposta.

Bastano pochi secondi per elaborare le nuove impostazioni, a questo punto è il momento migliore per avviare la configurazione del dominio.

## <a name="verify-your-domain"></a>Verificare il dominio

Prima di Azure Web Apps consente di eseguire il mapping di un dominio al sito, è innanzitutto necessario verificare di avere l'autorizzazione per eseguire il mapping del dominio. A tale scopo, è necessario aggiungere un nuovo record CNAME per la voce DNS.

* Accedere a gestore DNS del dominio
* Creare un nuovo record CNAME *awverify*
* Scegliere *awverify* *awverify. YOUR_DOMAIN.azurewebsites.NET*

Può richiedere del tempo per le modifiche DNS vigore completo, pertanto se la procedura seguente non funziona immediatamente, passare svolgere altre attività, quindi tornare e riprovare.

## <a name="add-the-domain-to-the-web-app"></a>Aggiungere il dominio per il web app

Tornare all'app web tramite il portale di Azure, fare clic su **Impostazioni**e quindi fare clic su **domini personalizzati e SSL**.

Quando vengono visualizzate le *impostazioni SSL* , verranno visualizzati i campi in cui immessi tutti i domini che si desiderano assegnare all'app web. Se un dominio non è presente nell'elenco, non saranno disponibile per il mapping all'interno di WordPress, indipendentemente da qual è la configurazione del DNS del dominio.

![Finestra di dialogo domini personalizzati gestione][wordpress-manage-domains]

Dopo aver digitato il dominio nella casella di testo, Azure consente di verificare il record CNAME che è stato creato in precedenza. Se il DNS non è completamente propagato, verrà visualizzato un indicatore rosso. Se è stata eseguita correttamente, verrà visualizzato un segno di spunta verde. 

Prendere nota dell'indirizzo IP elencato nella parte inferiore della finestra di dialogo. Sarà necessario questa opzione per installare il record per il dominio.

## <a name="setup-the-domain-a-record"></a>Configurazione del dominio un record

Se gli altri passaggi hanno avuto esito positivo, è ora possibile assegnare il dominio all'app web Azure tramite un record DNS A. 

È importante tenere presente qui che App web Azure accetta CNAME e un record, ma è *necessario* usare un record per consentire il mapping di dominio corretto. Un record CNAME non possa essere inoltrato a un altro record CNAME, ovvero quali Azure creato con YOUR_DOMAIN.azurewebsites.net.

Usando l'indirizzo IP ottenuto nel passaggio precedente, tornare al gestore DNS e configurare il record in modo che puntino a tale IP.


## <a name="install-and-setup-the-plugin"></a>Installare e configurare il plug-in

La funzionalità multisito WordPress non dispone di un metodo incorporato per eseguire il mapping domini personalizzati. Esiste un plug-in denominato [WordPress MU Domain Mapping] [ wordpress-plugin-wordpress-mu-domain-mapping] che aggiunge la funzionalità dell'utente. Accedere alla parte amministratore di rete del sito e installare il plug-in **WordPress MU Domain Mapping** .

Dopo l'installazione e attivazione plug-in, visitare **Impostazioni** > **Domain Mapping** a configurare il plug-in. Nella prima casella di testo *Indirizzo IP del Server*, immettere l'indirizzo IP utilizzato per configurare il record per il dominio. Impostare le *Opzioni di dominio* desiderato (i valori predefiniti sono spesso di fine) e fare clic su **Salva**.

## <a name="map-the-domain"></a>Eseguire il mapping del dominio

Visitare il **Dashboard** per il sito che si desidera mappare il dominio. Fare clic su **Strumenti** > **Domain Mapping** e digitare il nuovo dominio nella casella di testo e fare clic su **Aggiungi**.

Per impostazione predefinita, il nuovo dominio verrà riscrittura per il dominio generata automaticamente. Se si vuole avere tutto il traffico inviato al nuovo dominio, selezionare la casella *dominio primario per il blog* prima del salvataggio. È possibile aggiungere un numero illimitato di domini in un sito, ma solo una può essere principale.

## <a name="do-it-again"></a>Eseguire nuovamente

Azure Web Apps consente di aggiungere un'app web di un numero illimitato di domini. Per aggiungere un altro dominio, è necessario eseguire le sezioni di **verificare il dominio** e **configurare il dominio un record** per ogni dominio.  

>[AZURE.NOTE] Se si desidera iniziare a utilizzare il servizio di App Azure prima di iscriversi a un account Azure, accedere al [Servizio App provare](http://go.microsoft.com/fwlink/?LinkId=523751), in cui è possibile creare immediatamente un'app web starter breve nel servizio di App. Nessun carte di credito obbligatorio; Nessun impegni.

## <a name="whats-changed"></a>Novità
* Per una Guida per la modifica da siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sulla esistente servizi di Windows Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 
