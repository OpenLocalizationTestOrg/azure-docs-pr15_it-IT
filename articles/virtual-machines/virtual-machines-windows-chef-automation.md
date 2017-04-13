<properties
   pageTitle="Distribuzione di Azure macchina virtuale con Chef | Microsoft Azure"
   description="Informazioni su come usare Chef per eseguire la distribuzione automatica macchina virtuale e configurazione in Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatizzare la distribuzione di Azure macchina virtuale con Chef

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef è un ottimo strumento per l'esecuzione di automazione e lo si desidera configurazioni di stato.

Con l'ultima versione di api cloud Chef fornisce un'integrazione perfetta con Azure, offrendo la possibilità di effettuare il provisioning e distribuire stati di configurazione con un unico comando.

In questo articolo verrà illustrato configurare l'ambiente Chef per effettuare il provisioning di macchine virtuali di Azure e guidano l'utente tramite la creazione di un criterio o "Libro di ricette" e la distribuzione di questo libro di ricette per una macchina virtuale Azure.

Per iniziare

## <a name="chef-basics"></a>Nozioni fondamentali sul Chef

Prima di iniziare, è consigliabile che esaminare i concetti di base di Chef. Esiste un ottimo materiale <a href="http://www.chef.io/chef" target="_blank">qui</a> ed è consigliabile avere una rapida lettura prima di provare questa procedura dettagliata. Tuttavia, verrà riepilogo le nozioni di base prima di iniziare.

Il diagramma seguente illustra l'architettura di alto livello Chef.

![][2]

Chef sono previsti tre passaggi principali architettonica: Chef Server, Chef Client (nodo) e Chef Workstation.

Il Chef Server è il punto di gestione e sono disponibili due opzioni per il Chef Server: una soluzione ospitata o una soluzione locale. Verranno utilizzate la soluzione ospitata.

Il Chef Client (nodo) è l'agente che si trova nel server che vengono gestiti.

Chef Workstation è il nostro workstation di amministrazione in cui è creare le regole ed eseguire i comandi di gestione. È eseguire il comando **coltello** da Chef Workstation per gestire l'infrastruttura.

È inoltre disponibile il concetto di "Cucina" e "Ricette". Si tratta in modo efficace i criteri è definire e applicare i server.

## <a name="preparing-the-workstation"></a>Preparazione della workstation

Prima di tutto, consente di preparare la workstation. Si sta utilizzando una workstation Windows standard. È necessario creare una directory per archiviare il file di configurazione e cucina.

Prima di tutto creare una directory denominata C:\chef.

Creare una seconda directory denominata c:\chef\cookbooks.

A questo punto è necessario scaricare il file di impostazioni Azure in modo Chef possano comunicare con l'abbonamento Azure.

Scaricare il pubblica impostazioni da [qui](https://manage.windowsazure.com/publishsettings/).

Salvare il file di impostazioni di pubblicazione in C:\chef.

##<a name="creating-a-managed-chef-account"></a>Creazione di un account Chef gestito

Iscriversi a un account Chef ospitato [qui](https://manage.chef.io/signup).

Durante il processo di iscrizione a, verrà richiesto di creare una nuova organizzazione.

![][3]

Dopo la creazione dell'organizzazione, scaricare starter kit.

![][4]

> [AZURE.NOTE] Se viene visualizzato un messaggio che informa che verranno reimpostate le chiavi, è ok per procedere come non abbiamo alcuna infrastruttura esistente come ancora configurato.

Starter kit zip contenente il file di configurazione dell'organizzazione e chiavi.

##<a name="configuring-the-chef-workstation"></a>Configurazione di workstation Chef

Estrarre il contenuto del chef-starter.zip a C:\chef.

Copiare tutti i file in repo di starter\chef chef\.chef alla directory c:\chef.

Directory dovrebbe essere simile al seguente.

![][5]

A questo punto è incluso il file di pubblicazione Azure nella radice del c:\chef quattro file.

I file PEM contengono organizzazione e chiavi private di amministratore per la comunicazione mentre il file knife.rb contiene la configurazione coltello. È necessario modificare il file knife.rb.

Aprire il file nell'editor di scelta e modificare "cookbook_path" rimuovendo il /.. / dal percorso in modo che venga visualizzato come indicato di seguito.

    cookbook_path  ["#{current_dir}/cookbooks"]

Aggiungere anche il seguente linea che riflettono il nome del Azure pubblica file di impostazioni.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Il file knife.rb dovrebbe essere simile al seguente.

![][6]

Queste linee garantisce che fa riferimento a directory cucina in c:\chef\cookbooks coltello e Usa anche il file di impostazioni di pubblicazione Azure durante le operazioni di Azure.

## <a name="installing-the-chef-development-kit"></a>L'installazione di Chef Development Kit

Successiva [scaricare e installare](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) per impostare la Chef Workstation.

![][7]

Installare il percorso predefinito dei c:\opscode. L'installazione richiederà circa 10 minuti.

Confermare che la variabile PATH contiene le voci per C:\opscode\chefdk\bin; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

Se non si trovano sono, assicurarsi di aggiungere questi percorsi!

*NOTA CHE L'ORDINE DEL TRACCIATO È IMPORTANTE!* Se i percorsi di opscode non sono nell'ordine corretto è problemi.

Riavviare la workstation prima di continuare.

Successivamente, verrà installato l'estensione coltello Azure. In questo modo coltello con il "Azure plug-in".

Eseguire il comando seguente.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] L'argomento pre-garantisce che si riceve l'ultima versione RC di plug-in di Azure coltello che fornisce l'accesso per il set più recente di API.

È probabile che un numero di dipendenze verrà anche installato nello stesso momento.

![][8]


Per assicurarsi che tutto sia configurato correttamente, eseguire il comando seguente.

    knife azure image list

Se tutti gli elementi è configurato correttamente, si verrà visualizzato un elenco di immagini di Azure disponibili scorrere.

Congratulazioni. Impostazione di workstation!

##<a name="creating-a-cookbook"></a>Creazione di un libro di ricette

Un libro di ricette usato Chef per definire un insieme di comandi che si desiderano eseguire su un client gestito. Creazione di un libro di ricette è semplice e si utilizza il comando **chef generare libro di ricette** per generare il modello di libro di ricette. Verrà utilizzato il server web ricettario che desidera un criterio che distribuisce automaticamente IIS.

In directory C:\Chef eseguire il comando seguente.

    chef generate cookbook webserver

Verrà generato un insieme di file nella directory C:\Chef\cookbooks\webserver. A questo punto è necessario definire l'insieme di comandi che è ad esempio cliente Chef da eseguire sul computer virtuale gestita in uso.

I comandi sono archiviati in default.rb file. In questo file, verrà definire un insieme di comandi che consente di installare IIS, inizia IIS e copia di un file modello nella cartella wwwroot.

Modificare il file C:\chef\cookbooks\webserver\recipes\default.rb e aggiungere le seguenti righe.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Salvare il file dopo aver.

## <a name="creating-a-template"></a>Creazione di un modello

Come indicato in precedenza, è necessario generare un file di modello che verrà utilizzato come la pagina default.

Eseguire il seguente comando per generare il modello.

    chef generate template webserver Default.htm

A questo punto, passare al file C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Modificare il file mediante l'aggiunta di un codice "Hello World" HTML semplice e quindi salvare il file.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Caricare le istruzioni per il Server Chef

In questo passaggio, stiamo eseguendo una copia del libro di ricette che è stata creata nel computer locale e caricarlo nel server ospitato Chef. Dopo aver caricato, il libro di ricette verrà visualizzate sotto la scheda **criteri** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Distribuire una macchina virtuale con coltello Azure

Verrà ora distribuire una macchina virtuale Azure e applicare il libro di ricette "Web" che consente l'installazione la pagina web servizio e predefinito del web IIS.

Per eseguire questa operazione, utilizzare il comando **coltello azure server consente di creare** .

È ad esempio il comando viene visualizzato accanto.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

I parametri sono facile comprensione. Sostituire le variabili particolare ed eseguire.

> [AZURE.NOTE] Tramite la riga di comando, sono anche automatizzare le regole di filtro endpoint rete utilizzando il parametro – tcp endpoint. Dopo avere aperto le porte 80 e 3389 per consentire l'accesso per la pagina web e sessione RDP.

Dopo aver eseguito il comando, passare al portale di Azure e verrà visualizzato il computer iniziare a disposizione.

![][13]

Viene visualizzato il prompt successivo.

![][10]

Al termine della distribuzione, dovrebbe essere possibile connettersi al servizio web tramite la porta 80 come è stato aperto la porta quando viene eseguito il provisioning la macchina virtuale con il comando coltello Azure. Come questa macchina virtuale la macchina virtuale sola il servizio cloud, è necessario connettersi con l'url del servizio cloud.

![][11]

Come si può notare, viene visualizzato spazio alla creatività con il codice HTML.

Non dimenticare che è anche possibile eseguire la connessione tramite una sessione RDP dal portale di classica Azure tramite la porta 3389.

Auguriamo che questo è stato utile! Accedere e iniziare a infrastruttura come viaggio codice con Azure oggi stesso!


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->
