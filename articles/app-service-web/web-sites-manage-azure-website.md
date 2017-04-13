<properties 
    pageTitle="Gestire un'app web nel servizio App Azure" 
    description="Collegamenti a risorse per la gestione di un'app web nel servizio App Azure." 
    services="app-service\web" 
    documentationCenter="" 
    authors="erikre" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="rachelap"/>

# <a name="manage-a-web-app-in-azure-app-service"></a>Gestire un'app web nel servizio App Azure

In questo argomento contiene collegamenti alle risorse per la gestione di un'app web nel [Servizio App Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Gestione include tutte le attività che consentono di mantenere un'app web di funzionamento. 

Per tutta la durata di un'app web, si eseguirà diverse operazioni di gestione, si sposta da distribuzione iniziale per gli aggiornamenti, manutenzione e il normale funzionamento.

Molte attività di gestione app web può essere eseguita nel portale di Azure.

## <a name="before-you-deploy-your-web-app-to-production"></a>Prima di distribuire un'app web di produzione

### <a name="choose-a-tier"></a>Scegliere un livello

Azure App servizio è disponibile in cinque livelli: condivisi, base, Standard e Premium gratuiti. Per informazioni sulle funzionalità e prezzi per ogni livello, vedere [informazioni sui prezzi](/pricing/details/app-service/). 

- [Piani di servizio App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) consentono di raggruppare più web App in stesso livello.
- È sempre possibile [cambiare livelli](web-sites-scale.md) dopo la creazione di un'applicazione web.

### <a name="configuration"></a>Configurazione

Utilizzare il [Portale di Azure](https://portal.azure.com/) per impostare varie opzioni di configurazione. Per informazioni dettagliate, vedere [configurare web apps in Azure App servizio](web-sites-configure.md). Ecco un rapido elenco di controllo:

- Selezionare **le versioni di runtime** per .NET, PHP, Java o Python, se necessario.
- Abilitare **WebSockets** se un'app web utilizza il protocollo WebSocket. (Include le applicazioni che utilizzano [ASP.NET SignalR](http://www.asp.net/signalr) o [socket.io](web-sites-nodejs-chat-app-socketio.md)).
- Esegue processi continuo web? In caso affermativo, abilitare **Sempre attiva**.
- Impostare il **documento predefinito**, ad esempio index.

Oltre a queste impostazioni di configurazione di base, può essere necessario configurare le operazioni seguenti:

- Crittografia **Secure Sockets Layer (SSL)** . Per utilizzare SSL con un nome di dominio personalizzato, è necessario ottenere un certificato SSL e configurare un'applicazione web per utilizzarlo. Vedere [Abilitare HTTPS per un'app web nel servizio App Azure](web-sites-configure-ssl-certificate.md).
- **Nome di dominio personalizzato.** App web è automaticamente un sottodominio in azurewebsites.net. È possibile associare un nome di dominio personalizzato, ad esempio contoso.com. Vedere [configurare un nome di dominio personalizzato in Azure App servizio](web-sites-custom-domain-name.md).

Configurazione specifiche della lingua:

- **PHP**: [configurazione di PHP in Azure App servizio Web Apps](web-sites-php-configure.md).
- **Python**: [configurazione Python con Azure App servizio Web Apps](web-sites-python-configure.md)


## <a name="while-your-web-app-is-running"></a>Durante l'esecuzione di un'applicazione web

Durante l'esecuzione di un'applicazione web, si desidera verificare che sia disponibile e che adatta per soddisfare il traffico utente. È anche necessario risolvere gli errori.

### <a name="monitoring"></a>Monitoraggio

- Tramite il portale di Azure, è possibile [aggiungere dati sulle prestazioni](web-sites-monitor.md) , ad esempio l'utilizzo della CPU e numero di richieste client.
- [Scala a un'applicazione web](web-sites-scale.md) in risposta al traffico. A seconda del livello, è possibile ridimensionare il numero di macchine virtuali e/o le dimensioni delle istanze macchine Virtuali. In livelli Standard e Premium, è possibile anche impostare il ridimensionamento automatico in modo che l'app web scale automaticamente a intervalli fissi o in risposta a caricare.  
 
### <a name="backups"></a>Esecuzione di backup

- Impostare [backup automatici](web-sites-backup.md) di un'applicazione web. Ulteriori informazioni sui backup in [questo video](https://azure.microsoft.com/documentation/videos/azure-websites-automatic-and-easy-backup/).
- Informazioni sulle opzioni per il [ripristino del database](../sql-database/sql-database-business-continuity.md) nel Database di SQL Azure.

### <a name="troubleshooting"></a>Risoluzione dei problemi

- In caso di errori, è possibile [risolvere i problemi di Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug), utilizzando registri diagnostici e debug attivo nel cloud. 
- All'esterno di Visual Studio, esistono diversi modi per raccogliere i registri diagnostici. Vedere [abilitare la registrazione diagnostica per App web di Azure App servizio](web-sites-enable-diagnostic-log.md).
- Per le applicazioni Node, vedere [come eseguire il debug di un'app web Node in Azure App servizio](web-sites-nodejs-debug.md).

### <a name="restoring-data"></a>Ripristino dei dati

- [Ripristinare](web-sites-restore.md) un'applicazione web che in precedenza è stato eseguito il backup.


## <a name="when-you-update-your-web-app"></a>Quando si aggiorna un'applicazione web

Se non è stata attivata backup automatici, è possibile creare un [backup manuale](web-sites-backup.md).

È possibile utilizzare [l'installazione in più fasi](web-sites-staged-publishing.md). Questa opzione consente di pubblicare gli aggiornamenti a una distribuzione di gestione temporanea che viene eseguito side-by-side con la distribuzione di produzione. 

Se si utilizza Visual Studio Team Services, è possibile impostare la distribuzione continua dal controllo origine:

- [Utilizzo di controllo della versione di Team Foundation (TFVC)](../cloud-services/cloud-services-continuous-delivery-use-vso.md) 
- [Uso fra](../cloud-services/cloud-services-continuous-delivery-use-vso-git.md)
 
<!-- Anchors. -->

[Before you deploy your site to production]: #before-you-deploy-your-site-to-production
[While your website is running]: #while-your-website-is-running
[When you update your website]: #when-you-update-your-website

  
