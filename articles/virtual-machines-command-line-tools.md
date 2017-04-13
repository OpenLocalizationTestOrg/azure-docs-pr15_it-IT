<properties
    pageTitle="Azure comandi CLI in modalità di gestione dei servizi | Microsoft Azure"
    description="Comandi di Azure interfaccia della riga di comando (CLI) in modalità di gestione dei servizi per gestire distribuzioni nel modello di distribuzione classica"
    services="virtual-machines-linux,virtual-machines-windows,mobile-services, cloud-services"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="danlep"/>

# <a name="azure-cli-commands-in-azure-service-management-asm-mode"></a>Azure comandi CLI in modalità di gestione dei servizi di Azure (asm)

[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-classic-include.md)]È anche possibile [leggere le informazioni relative a tutti i comandi del modello di Manager delle risorse](virtual-machines/azure-cli-arm-commands.md)e usare CLI per [eseguire la migrazione di risorse](virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md) da classico nel modello di Manager delle risorse.

Questo articolo fornisce sintassi e le opzioni per i comandi di Azure CLI in genere consente di creare e gestire le risorse Azure nel modello di distribuzione classica. Accedere ai comandi eseguendo CLI in modalità di gestione dei servizi di Azure (asm). Non è un riferimento completo e la versione CLI risulti leggermente diverso sui comandi o parametri. 

Per iniziare a, prima di [installare CLI Azure](xplat-cli-install.md) e [connettersi al proprio abbonamento Azure](xplat-cli-connect.md).

Per corrente sintassi dei comandi e opzioni nella riga di comando, digitare `azure help` o, per visualizzare la Guida per un determinato comando, `azure help [command]`. Trovare anche esempi CLI nella documentazione per la creazione e gestione dei servizi di Azure specifici.

Parametri facoltativi sono indicati tra parentesi quadre (ad esempio `[parameter]`). Tutti gli altri parametri necessari.

Oltre ai parametri facoltativi specifici ai comandi descritti di seguito, sono disponibili tre parametri facoltativi che possono essere utilizzati per visualizzare l'output dettagliato, ad esempio le opzioni di richiesta e codici di stato. Il `-v` parametro fornisce un output dettagliato e la `-vv` parametro fornisce ancora più dettagliate output dettagliato. Il `--json` opzione Visualizza i risultati nel formato json non elaborati.

## <a name="setting-asm-mode"></a>Impostazione della modalità di asm

Utilizzare il comando seguente per abilitare comandi in modalità di gestione dei servizi di Azure CLI.

    azure config mode asm

>[AZURE.NOTE] Modalità di gestione risorse Azure e gestione dei servizi di Azure la CLI si escludono a vicenda. Le risorse create in una modalità, ovvero non possono essere gestite da altre modalità.

## <a name="manage-your-account-information-and-publish-settings"></a>Gestire le informazioni dell'account e impostazioni di pubblicazione
Un modo che CLI possibile connettersi al proprio account è utilizzando le informazioni sull'abbonamento Azure. (Le altre opzioni, vedere [connettersi a un abbonamento Azure da CLI Azure](xplat-cli-connect.md) ). Queste informazioni possono essere ottenute dal portale di classica Azure in un file di impostazioni pubblica come descritto di seguito. È possibile importare il file di impostazioni di pubblicazione come persistente locale impostazione di configurazione che CLI viene utilizzato per le operazioni successive. È necessario importare le impostazioni di pubblicazione una sola volta.

**download di account [opzioni]**

Questo comando viene avviato un browser per scaricare il file publishsettings dal portale di classica Azure.

    ~$ azure account download
    info:   Executing command account download
    info:   Launching browser to https://windows.azure.com/download/publishprofile.aspx
    help:   Save the downloaded file, then execute the command
    help:   account import <file>
    info:   account download command OK

**account importazione [opzioni] &lt;file >**


Questo comando consente di importare un file publishsettings o un certificato in modo che può essere utilizzato per lo strumento sessioni in futuro.

    ~$ azure account import publishsettings.publishsettings
    info:   Importing publish settings file publishsettings.publishsettings
    info:   Found subscription: 3-Month Free Trial
    info:   Found subscription: Pay-As-You-Go
    info:   Setting default subscription to: 3-Month Free Trial
    warn:   The 'publishsettings.publishsettings' file contains sensitive information.
    warn:   Remember to delete it now that it has been imported.
    info:   Account publish settings imported successfully

> [AZURE.NOTE] Il file publishsettings può contenere i dettagli di (nome dell'abbonamento e ID) più di una sottoscrizione. Quando si importa file publishsettings, l'abbonamento prima viene utilizzato come descrizione predefinita. Per utilizzare una sottoscrizione diversa, eseguire il comando seguente:
<code>~$ azure config set subscription &lt;other-subscription-id&gt;</code>

**account deselezionare [opzioni]**

Questo comando rimuove publishsettings archiviate importati. Utilizzare questo comando se è già completato tramite lo strumento nel computer in uso e garantire lo strumento non può essere utilizzato con l'account in sessioni future.

    ~$ azure account clear
    Clearing account info.
    info:   OK

**elenco di account [opzioni]**

Elencare le sottoscrizioni importate

    ~$ azure account list
    info:    Executing command account list
    data:    Name                                    Id
           Current
    data:    --------------------------------------  -------------------------------
    -----  -------
    data:    Forums Subscription                     8679c8be-3b05-49d9-b8fb  true
    data:    Evangelism Team Subscription            9e672699-1055-41ae-9c36  false
    data:    MSOpenTech-Prod                         c13e6a92-706e-4cf5-94b6  false

**account impostato [opzioni] &lt;sottoscrizione&gt;**

Impostare l'abbonamento corrente

###<a name="commands-to-manage-your-affinity-groups"></a>Comandi per gestire i gruppi di affinità

**elenco gruppo affinità account [opzioni]**

Questo comando Elenca i gruppi di affinità Azure.

Quando un gruppo di macchine virtuali si estende su più computer fisici, è possibile impostare gruppi affinità. Gruppo affinità specifica che il computer deve essere quanto più vicino possibile, ridurre la latenza di rete tra loro.

    ~$ azure account affinity-group list
    + Fetching affinity groups
    data:   Name                                  Label   Location
    data:   ------------------------------------  ------  --------
    data:   535EBAED-BF8B-4B18-A2E9-8755FB9D733F  opentec  West US
    info:   account affinity-group list command OK

**gruppo di account affinità creare [opzioni] &lt;nome&gt;**

Questo comando crea un gruppo di affinità

    ~$ azure account affinity-group create opentec -l "West US"
    info:    Executing command account affinity-group create
    + Creating affinity group
    info:    account affinity-group create command OK

**gruppo di affinità account Mostra [opzioni] &lt;nome&gt;**

Questo comando Visualizza i dettagli del gruppo affinità

    ~$ azure account affinity-group show opentec
    info:    Executing command account affinity-group show
    + Getting affinity groups
    data:    $ xmlns "http://schemas.microsoft.com/windowsazure"
    data:    $ xmlns:i "http://www.w3.org/2001/XMLSchema-instance"
    data:    Name "opentec"
    data:    Label "b3BlbnRlYw=="
    data:    Description $ i:nil "true"
    data:    Location "West US"
    data:    HostedServices ""
    data:    StorageServices ""
    data:    Capabilities Capability 0 "PersistentVMRole"
    data:    Capabilities Capability 1 "HighMemory"
    info:    account affinity-group show command OK

**account gruppo affinità eliminare [opzioni] &lt;nome&gt;**

Questo comando Elimina il gruppo affinità specificato

    ~$ azure account affinity-group delete opentec
    info:    Executing command account affinity-group delete
    Delete affinity group opentec? [y/n] y
    + Deleting affinity group
    info:    account affinity-group delete command OK

###<a name="commands-to-manage-your-account-environment"></a>Comandi per gestire l'ambiente di account

**elenco di account env [opzioni]**

Elenco degli ambienti di account

    C:\windows\system32>azure account env list
    info:    Executing command account env list
    data:    Name
    data:    ---------------
    data:    AzureCloud
    data:    AzureChinaCloud
    info:    account env list command OK

**account env Mostra [opzioni] [ambiente]**

Visualizzare dettagli sull'ambiente di account

    ~$ azure account env show
    info:    Executing command account env show
    Environment name: AzureCloud
    data:    Environment publishingProfile  http://go.microsoft.com/fwlink/?LinkId=2544
    data:    Environment portal  http://go.microsoft.com/fwlink/?LinkId=2544
    info:    account env show command OK

**aggiunta di account env [opzioni] [ambiente]**

Questo comando consente di aggiungere un ambiente all'account

**account env impostare [opzioni] [ambiente]**

Questo comando imposta l'ambiente di account

**account env eliminare [opzioni] [ambiente]**

Questo comando Elimina l'ambiente specificato dall'account

## <a name="commands-to-manage-your-classic-virtual-machines"></a>Comandi per gestire le macchine virtuali classiche
Il diagramma seguente illustra la modalità classica Azure macchine virtuali sono ospitate nell'ambiente di produzione distribuzione di un servizio cloud Azure.

![Diagramma tecnico Azure](./media/virtual-machines-command-line-tools/architecturediagram.jpg)

**Crea nuovo** crea l'unità in archiviazione blob (vale a dire e:\ nel diagramma); **allegare** un disco già creato ma non collegato si connette a una macchina virtuale.

**creazione di macchine virtuali [opzioni] &lt;nome dns > &lt;immagine > &lt;nomeutente > [password]**

Questo comando crea una macchina virtuale Azure. Per impostazione predefinita, ogni macchine () viene creata nel proprio servizio cloud. È possibile specificare che una macchina virtuale deve essere aggiunto a un servizio cloud esistente tramite l'utilizzo dell'opzione - c come descritto di seguito.

La macchina virtuale creare comando, ad esempio il portale classico Azure, crea solo macchine virtuali nell'ambiente di distribuzione di produzione. È possibile creare una macchina virtuale nell'ambiente di gestione temporanea distribuzione di un servizio cloud. Se l'abbonamento non ha un account di archiviazione Azure esistente, il comando crea uno.

È possibile specificare un percorso tramite il - parametro posizione, oppure è possibile specificare un gruppo di affinità tramite--parametro gruppo affinità. Se non viene specificato, viene chiesto di fornire una da un elenco di percorsi validi.

La password deve essere 123 8 caratteri e soddisfare i requisiti del sistema operativo in uso per questa macchina virtuale complessità della password.

Se si prevede di utilizzare SSH per gestire una macchina virtuale Linux distribuita (come in genere le maiuscole/minuscole), è necessario attivare SSH tramite l'opzione -e quando si crea la macchina virtuale. Non è possibile attivare SSH dopo aver creata la macchina virtuale.

Macchine virtuali di Windows può attivare RDP in un secondo momento aggiungendo la porta 3389 come un endpoint.

Per questo comando sono supportati i parametri facoltativi seguenti:

**- c,-connettersi** creare la macchina virtuale all'interno di una distribuzione già creata in un servizio di hosting. Se - vmname non viene usata con questa opzione, il nome del nuovo computer virtuale viene generato automaticamente.<br />
**-n, nome-macchine virtuali** Specificare il nome della macchina virtuale. Questo parametro utilizza nome del servizio di hosting per impostazione predefinita. Se non viene specificato - vmname, il nome per la nuova macchina virtuale viene generato come &lt;nome servizio >&lt;id >, dove &lt;id > è il numero di macchine virtuali esistenti nel servizio + 1. Ad esempio, se si usa questo comando per aggiungere una macchina virtuale a un servizio di hosting servizio che contiene una macchina virtuale esistente, la nuova macchina virtuale denominata MyService2.<br />
**-u, --blob url** Specificare l'URL di spazio di archiviazione blob di destinazione in cui creare il disco di sistema macchina virtuale. <br />
**+ z, dimensione di memoria virtuale:** Specificare la dimensione della macchina virtuale. I valori validi sono: "ExtraSmall", "Piccola", "Medium", "Grandi", "ExtraLarge", "A5", "A6", "A7", "A8", "A9", "A10", "A11", "Basic_A0", "Basic_A1", "Basic_A2", "Basic_A3", "Basic_A4", "Standard_D1", "Standard_D2", "Standard_D3", "Standard_D4", "Standard_D11", "Standard_D12", "Standard_D13", "Standard_D14", "Standard_DS1", "Standard_DS2", "Standard_DS3", "Standard_DS4", "Standard_DS11", "Standard_DS12", "Standard_DS13", "Standard_DS14", "Standard_G1", "Standard_G2" , "Standard_G3", "Standard_G4", "Standard_G55". Il valore predefinito è "Piccola". <br />
**-r** Aggiunge la connettività RDP a una macchina virtuale di Windows. <br />
**-e,-ssh** Aggiunge SSH connettività a una macchina virtuale di Windows. <br />
**-t, --ssh certificato** Specifica il certificato SSH. <br />
**-s** L'abbonamento <br />
**-latina-community** L'immagine specificata è un'immagine della community <br />
**-w** Il nome di rete virtuale <br/>
**-l, --percorso** specifica la posizione (ad esempio, "North Central US"). <br />
**-, --gruppo affinità** specifica il gruppo di affinità.<br />
**-w, --nome di rete virtuale** Specificare la rete virtuale in cui aggiungere la nuova macchina virtuale. Reti virtuali possono configurare e gestite dal portale di classica Azure.<br />
**-b, --subnet nomi** Specifica i nomi di subnet per assegnare la macchina virtuale.

In questo esempio, MSFT__Win2K8R2SP1-120514-1520-141205-01-en-us-30GB è un'immagine della piattaforma. Per ulteriori informazioni sulle immagini del sistema operativo, vedere elenco di immagini di macchine virtuali.

    ~$ azure vm create my-vm-name MSFT__Windows-Server-2008-R2-SP1.11-29-2011 username --location "West US" -r
    info:   Executing command vm create
    Enter VM 'my-vm-name' password: ************
    info:   vm create command OK

**creare-da macchine virtuali &lt;nome dns > &lt;file ruolo >**

Questo comando crea una macchina virtuale Azure da un file di ruolo JSON.

    ~$ azure vm create-from my-vm example.json
    info:   OK

**elenco di macchine virtuali [opzioni]**

Questo comando Elenca macchine virtuali di Azure. L'opzione - json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm list
    info:   Executing command vm list
    data:   DNS Name                          VM Name      Status
    data:   --------------------------------  -----------  ---------
    data:   my-vm-name.cloudapp-preview.net        my-vm        ReadyRole
    info:   vm list command OK

**elenco di località macchine virtuali [opzioni]**

Questo comando Elenca tutti i percorsi di account Azure disponibili.

    ~$ azure vm location list
    info:   Executing command vm location list
    data:   Name                   Display Name
    data:   ---------------------  ------------
    data:   Azure Preview  West US
    info:   account location list command OK

**macchine virtuali Mostra [opzioni] &lt;nome >**

Questo comando Mostra i dettagli di una macchina virtuale Azure. L'opzione - json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm show my-vm
    info:   Executing command vm show
    data:   {
    data:       InstanceSize: 'Small',
    data:       InstanceStatus: 'ReadyRole',
    data:       DataDisks: [],
    data:       IPAddress: '10.26.192.206',
    data:       DNSName: 'my-vm.cloudapp.net',
    data:       InstanceStateDetails: {},
    data:       VMName: 'my-vm',
    data:       Network: {
    data:           Endpoints: [
    data:               {
    data:                   Protocol: 'tcp',
    data:                   Vip: '65.52.250.250',
    data:                   Port: '63238' ,
    data:                   LocalPort: '3389',
    data:                   Name: 'RemoteDesktop'
    data:               }
    data:           ]
    data:       },
    data:       Image: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       OSVersion: 'WA-GUEST-OS-1.18_201203-01'
    data:   }
    info:   vm show command OK

**macchine virtuali eliminare [opzioni] &lt;nome >**

Questo comando Elimina una macchina virtuale Azure. Per impostazione predefinita, questo comando non viene eliminato il blob Azure da cui vengono creati il disco di sistema operativo e il disco di dati. Per eliminare il blob e la macchina virtuale su cui si basa, specificare l'opzione -b.

    ~$ azure vm delete my-vm
    info:   Executing command vm delete
    info:   vm delete command OK

**iniziare a macchine virtuali [opzioni] &lt;nome >**

Questo comando avvia una macchina virtuale Azure.

    ~$ azure vm start my-vm
    info:   Executing command vm start
    info:   vm start command OK

**macchine virtuali riavviare [opzioni] &lt;nome >**

Questo comando riavvia una macchina virtuale Azure.

    ~$ azure vm restart my-vm
    info:   Executing command vm restart
    info:   vm restart command OK

**arresto macchine virtuali [opzioni] &lt;nome >**

Questo comando si arresta una macchina virtuale Azure. Si può utilizzare l'opzione -p per specificare che la risorsa di calcolo non vengono rilasciati alla chiusura.

```
~$ azure vm shutdown my-vm
info:   Executing command vm shutdown
info:   vm shutdown command OK  
```

**acquisizione di macchine virtuali &lt;nome macchina virtuale > &lt;nome di immagine destinazione >**

Questo comando consente di acquisire un'immagine di Azure macchina virtuale.

È possibile acquisire un'immagine di macchina virtuale solo se lo stato macchina virtuale è **interrotto**. Arrestare la macchina virtuale prima di continuare.

    ~$ azure.cmd vm capture my-vm mycaptureimagename --delete
    info:   Executing command vm capture
    + Fetching VMs
    + Capturing VM
    info:   vm capture command OK

**esportazione di macchine virtuali [opzioni] &lt;nome macchina virtuale > &lt;percorso del file >**

Questo comando Esporta un'immagine di Azure macchina virtuale in un file

    ~$ azure vm export "myvm" "C:\"
    info:    Executing command vm export
    + Getting virtual machines
    + Exporting the VM
    info:   vm export command OK

##  <a name="commands-to-manage-your-azure-virtual-machine-endpoints"></a>Comandi per gestire gli endpoint Azure macchina virtuale
Di seguito è illustrata l'architettura di una distribuzione tipica di più istanze di una macchina virtuale classica. In questo esempio, porta 3389 è aperta in ogni macchina virtuale (per l'accesso RDP). È inoltre disponibile un indirizzo IP interno (ad esempio 168.55.11.1) in ogni macchina virtuale che viene utilizzato il bilanciamento del carico per instradare il traffico verso la macchina virtuale. Questo indirizzo IP interno può essere utilizzato anche per le comunicazioni tra macchine virtuali di.

![azurenetworkdiagram](./media/virtual-machines-command-line-tools/networkdiagram.jpg)

Richieste esterne in macchine virtuali verranno illustrate un bilanciamento del carico. Per questi motivi, non è possibile specificare le richieste di rispetto a una determinata macchina virtuale nelle distribuzioni con più macchine virtuali. Per distribuzioni con più macchine virtuali, è necessario configurare mapping porta tra le macchine virtuali (macchine virtuali porta) e il bilanciamento del carico (porta kg).

**creare macchine virtuali endpoint &lt;nome macchina virtuale > &lt;kg porta > [macchine virtuali porta]**

Questo comando crea un endpoint macchina virtuale. È possibile utilizzare anche -u o --pubblicitario enable-ritorno server per specificare se abilitare diretto al server restituito sull'endpoint, disabilitate per impostazione predefinita.

    ~$ azure vm endpoint create my-vm 8888 8888
    azure vm endpoint create my-vm 8888 8888
    info:   Executing command vm endpoint create
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint create command OK

**macchine virtuali endpoint creare-multipli [opzioni] &lt;nome macchina virtuale > &lt;kg porta > [:&lt;macchine virtuali porta > [:&lt;protocollo > [:&lt;pubblicitario enable-ritorno server > [:&lt;nome del set di kg > [:&lt;verifica protocollo > [:&lt;verifica porta > [:&lt;percorso di sondaggio > [:&lt;nome interno kg >]]] {1 -*}**

Creare più macchine virtuali endpoint.

**endpoint macchine virtuali eliminare [opzioni] &lt;nome macchina virtuale > &lt;nome dell'endpoint >**

Questo comando Elimina un endpoint macchina virtuale.

    ~$ azure vm endpoint delete my-vm http
    azure vm endpoint delete my-vm http
    info:   Executing command vm endpoint delete
    + Fetching VM
    + Reading network configuration
    + Updating network configuration
    info:   vm endpoint delete command OK

**elenco endpoint macchine virtuali &lt;nome macchina virtuale >**

Questo comando Elenca tutti gli endpoint macchina virtuale. L'opzione - json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm endpoint list my-linux-vm
    data:   Name  External Port  Local Port
    data:   ----  -------------  ----------
    data:   ssh   22             22

**aggiornamento di endpoint macchine virtuali [opzioni] &lt;nome macchina virtuale > &lt;nome dell'endpoint >**

Questo comando Aggiorna un endpoint macchine virtuali ai nuovi valori utilizzando le opzioni.

    -n, --endpoint-name <name>          the new endpoint name
    -lo, --lb-port <port>                the new load balancer port
    -t, --vm-port <port>                the new local port
    -o, --endpoint-protocol <protocol>  the new transport layer protocol for port (tcp or udp)

**endpoint macchine virtuali Mostra [opzioni] &lt;nome macchina virtuale >**

Questo comando vengono visualizzati i dettagli degli endpoint in una macchina virtuale

    ~$ azure vm endpoint show "mycouchvm"
    info:    Executing command vm endpoint show
    + Getting virtual machines
    data:    Network Endpoints 0 LoadBalancedEndpointSetName "CouchDB_EP-5984"
    data:    Network Endpoints 0 LocalPort "5984"
    data:    Network Endpoints 0 Name "CouchDB_EP"
    data:    Network Endpoints 0 Port "5984"
    data:    Network Endpoints 0 Protocol "tcp"
    data:    Network Endpoints 0 Vip "168.61.9.97"
    data:    Network Endpoints 1 LoadBalancedEndpointSetName "CouchEP_2-2020"
    data:    Network Endpoints 1 LocalPort "2020"
    data:    Network Endpoints 1 Name "CouchEP_2"
    data:    Network Endpoints 1 Port "2020"
    data:    Network Endpoints 1 Protocol "tcp"
    data:    Network Endpoints 1 Vip "168.61.9.97"
    data:    Network Endpoints 2 LocalPort "3389"
    data:    Network Endpoints 2 Name "RemoteDesktop"
    data:    Network Endpoints 2 Port "3389"
    data:    Network Endpoints 2 Protocol "tcp"
    data:    Network Endpoints 2 Vip "168.61.9.97"
    info:    vm endpoint show command OK

## <a name="commands-to-manage-your-azure-virtual-machine-images"></a>Comandi per gestire le immagini di Azure macchina virtuale

Immagini di macchina virtuale sono contenuti acquisiti di macchine virtuali già configurate che possono essere replicate in base alle esigenze.

**elenco immagini macchine virtuali [opzioni]**

Consente di ottenere un elenco di immagini macchina virtuale. Esistono tre tipi di immagini: immagini create da Microsoft, che sono precedute da "MSFT", le immagini create da terze parti, che sono precedute dal nome del fornitore e immagini è creare. Per creare le immagini, è possibile acquisire una macchina virtuale esistente o creare un'immagine da un VHD personalizzato caricato in archiviazione blob. Per ulteriori informazioni sull'utilizzo di un VHD personalizzato, vedere immagine macchine virtuali create.
L'opzione - json specifica che i risultati vengono restituiti in formato JSON non elaborato.

    ~$ azure vm image list
    data:   Name                                                                   Category   OS
    data:   ---------------------------------------------------------------------  ---------  -------
    data:   CANONICAL__Canonical-Ubuntu-12-04-20120519-2012-05-19-en-us-30GB.vhd   Canonical  Linux
    data:   MSFT__Windows-Server-2008-R2-SP1.11-29-2011                            Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1-with-SQL-Server-2012-Eval.11-29-2011  Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.en-us.30GB.2012-03-22                      Microsoft  Windows
    data:   MSFT__Windows-Server-8-Beta.2-17-2012                                  Microsoft  Windows
    data:   MSFT__Windows-Server-2008-R2-SP1.en-us.30GB.2012-3-22                  Microsoft  Windows
    data:   OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd                 OpenLogic  Linux
    data:   SUSE__SUSE-Linux-Enterprise-Server-11SP2-20120521-en-us-30GB.vhd       SUSE       Linux
    data:   SUSE__OpenSUSE64121-03192012-en-us-15GB.vhd                            SUSE       Linux
    data:   WIN2K8-R2-WINRM                                                        User       Windows
    info:   vm image list command OK

**Mostra immagine macchine virtuali [opzioni] &lt;nome >**

Questo comando Mostra i dettagli di un'immagine di macchina virtuale.

    ~$ azure vm image show MSFT__Windows-Server-2008-R2-SP1.11-29-2011
    + Fetching VM image
    info:   Executing command vm image show
    data:   {
    data:       Label: 'Windows Server 2008 R2 SP1, Nov 2011',
    data:       Name: 'MSFT__Windows-Server-2008-R2-SP1.11-29-2011',
    data:       Description: 'Microsoft Windows Server 2008 R2 SP1',
    data:       @: { xmlns: 'http://schemas.microsoft.com/windowsazure', xmlns:i: 'http://www.w3.org/2001/XMLSchema-instance' },
    data:       Category: 'Microsoft',
    data:       OS: 'Windows',
    data:       Eula: 'http://www.microsoft.com',
    data:       LogicalSizeInGB: '30'
    data:   }
    info:   vm image show command OK

**eliminazione di immagine macchine virtuali [opzioni] &lt;nome >**

Questo comando consente di eliminare un'immagine di macchina virtuale.

    ~$ azure vm image delete my-vm-image
    info:   Executing command vm image delete
    info:   VM image deleted: my-vm-image
    info:   vm image delete command OK

**immagine della macchina virtuale creare &lt;nome > [percorso di origine]**

Questo comando crea un'immagine di macchina virtuale. I file con estensione vhd personalizzati vengono caricati per lo spazio di archiviazione blob e quindi l'immagine di macchina virtuale viene creata da tale posizione. È quindi possibile utilizzare questa immagine macchina virtuale per creare una macchina virtuale. I parametri percorso e il sistema operativo sono obbligatori.

>[AZURE.NOTE]Questo comando supporta attualmente solo il caricamento di file VHD fisso. Per caricare un file con estensione vhd dinamico, utilizzare le [utilità del disco rigido virtuale Azure per accedere](https://github.com/Microsoft/azure-vhd-utils-for-go).

Alcuni impongono limiti descrittore dei file per processo. Se si supera questo limite, verrà visualizzato un errore di limite descrittore file. È possibile eseguire il comando nuovo utilizzando -p &lt;numero > parametro per ridurre il numero massimo di caricamento in parallelo. Il numero massimo predefinito di caricamento in parallelo è 96.

    ~$ azure vm image create mytestimage ./Sample.vhd -o windows -l "West US"
    info:   Executing command vm image create
    + Retrieving storage accounts
    info:   VHD size : 13 MB
    info:   Uploading 13312.5 KB
    Requested:100.0% Completed:100.0% Running: 105 Time:    8s Speed:  1721 KB/s
    info:   http://myaccount.blob.core.azure.com/vm-images/Sample.vhd is uploaded successfully
    info:   vm image create command OK

## <a name="commands-to-manage-your-azure-virtual-machine-data-disks"></a>Comandi per gestire i dischi di dati di Azure macchina virtuale

Dati dischi sono file con estensione vhd in archiviazione blob che può essere utilizzato da una macchina virtuale. Per ulteriori informazioni su come dischi di dati vengono distribuiti nell'archiviazione blob, vedere il diagramma tecnico Azure mostrato in precedenza.

I comandi per allegare dischi di dati (allegare disco macchine virtuali azure e azure macchine virtuali disco allegare nuovo) assegnare un numero di unità logica (LUN) per il disco di dati allegato, come richiesto dal protocollo SCSI. Il primo disco dati associato a una macchina virtuale viene assegnato LUN 0, il successivo è assegnata LUN 1 e così via.

Quando si scollega un disco dati con il disco di azure macchine virtuali scollegare del comando, utilizzare la &lt;lun&gt; parametro per indicare il disco da scollegare.

>[AZURE.NOTE] Si devono sempre disconnettersi dischi di dati in ordine inverso, iniziando il LUN numero più alto che è stato assegnato. Il livello di Linux SCSI non supporta scollegamento un LUN numerato inferiore mentre un LUN valori più alti è ancora collegato. Ad esempio, si deve disconnettersi non LUN 0 se LUN 1 è ancora collegata.

**Mostra disco macchine virtuali [opzioni] &lt;nome >**

Questo comando Visualizza informazioni dettagliate su un disco Azure.

    ~$ azure vm disk show anucentos-anucentos-0-20120524070008
    info:   Executing command vm disk show
    data:   AttachedTo DeploymentName "mycentos"
    data:   AttachedTo HostedServiceName "myanucentos"
    data:   AttachedTo RoleName "myanucentos"
    data:   OS "Linux"
    data:   Location "Azure Preview"
    data:   LogicalDiskSizeInGB "30"
    data:   MediaLink "http://mystorageaccount.blob.core.azure-preview.com/vhd-store/mycentos-cb39b8223b01f95c.vhd"
    data:   Name "mycentos-mycentos-0-20120524070008"
    data:   SourceImageName "OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd"
    info:   vm disk show command OK

**elenco di dischi macchine virtuali [opzioni] [nome-macchine virtuali]**

Questo comando elenchi dischi Azure o dischi collegati a una macchina virtuale specificata. Se viene eseguita con un parametro di nome macchina virtuale, restituisce tutti i dischi associati alla macchina virtuale. LUN 1 viene creato con la macchina virtuale e altri dischi elencati sono collegate separatamente.

    ~$ azure vm disk list mycentos
    info:   Executing command vm disk list
    data:   Lun  Size(GB)  Blob-Name
    data:   ---  --------  --------------------------------
    data:   1    30        mycentos-cb39b8223b01f95c.vhd
    data:   2    10        mycentos-e3f0d717950bb78d.vhd
    info:   vm disk list command OK

Esecuzione del comando senza un parametro di nome macchina virtuale restituisce tutti i dischi.

    ~$ azure vm disk list
    data:   Name                                        OS
    data:   ------------------------------------------  -------
    data:   mycentos-mycentos-0-20120524070008          Linux
    data:   mycentos-mycentos-2-20120525055052
    data:   mywindows-winvm-20120522223119              Windows
    info:   vm disk list command OK

**Elimina disco macchine virtuali [opzioni] &lt;nome >**

Questo comando Elimina un disco Azure da un archivio personale. Prima che venga eliminato, è necessario disconnettere il disco dal computer virtuale.

    ~$ azure vm disk delete mycentos-mycentos-2-20120525055052
    info:   Executing command vm disk delete
    info:   Disk deleted: mycentos-mycentos-2-20120525055052
    info:   vm disk delete command OK

**creare macchine virtuali disco &lt;nome > [percorso di origine]**

Questo comando consente di caricare e registra un disco Azure. -blob url, --percorso, o --gruppo affinità deve essere specificato. Se si usa questo comando con [percorso di origine], caricamento di file con estensione vhd specificato e viene creata un'immagine. È quindi possibile allegare l'immagine a una macchina virtuale utilizzando macchine virtuali disco allegare.

Alcuni impongono limiti descrittore dei file per processo. Se si supera questo limite, verrà visualizzato un errore di limite descrittore file. È possibile eseguire il comando nuovo utilizzando -p &lt;numero > parametro per ridurre il numero massimo di caricamento in parallelo. Il numero massimo predefinito di caricamento in parallelo è 96.

    ~$ azure vm disk create my-data-disk ~/test.vhd --location "West US"
    info:   Executing command vm disk create
    info:   VHD size : 10 MB
    info:   Uploading 10240.5 KB
    Requested:100.0% Completed:100.0% Running:  81 Time:   11s Speed:   952 KB/s
    info:   http://account.blob.core.azure.com/disks/test.vhd is uploaded successfully
    info:   vm disk create command OK

**caricamento di disco macchine virtuali [opzioni] &lt;percorso di origine > &lt;blob url > &lt;chiave di account di archiviazione >**

Questo comando consente di caricare un disco macchine virtuali

    ~$ azure vm disk upload "http://sourcestorage.blob.core.windows.net/vhds/sample.vhd" "http://destinationstorage.blob.core.windows.net/vhds/sample.vhd" "DESTINATIONSTORAGEACCOUNTKEY"
    info:   Executing command vm disk upload
    info:   Uploading 12351.5 KB
    info:   vm disk upload command OK

**allegare disco macchine virtuali &lt;nome macchina virtuale > &lt;nome di immagine disco >**

Questo comando consente un disco esistente nell'archiviazione blob a una macchina virtuale esistente distribuita in un servizio cloud.

    ~$ azure vm disk attach my-vm my-vm-my-vm-2-201242418259
    info:   Executing command vm disk attach
    info:   vm disk attach command OK

**macchine virtuali disco allegare nuovi &lt;nome macchina virtuale > &lt;dimensioni in gb > [blob url]**

Questo comando consente un disco dati a una macchina virtuale Azure. In questo esempio 20 è la dimensione del disco nuovo in gigabyte da collegare. Facoltativamente, è possibile usare un URL blob come ultimo argomento per specificare in modo esplicito il blob di destinazione per creare. Se non si specifica un URL blob, viene generato automaticamente un oggetto blob.

    ~$ azure vm disk attach-new nick-test36 20 http://nghinazz.blob.core.azure-preview.com/vhds/vmdisk1.vhd
    info:   Executing command vm disk attach-new
    info:   vm disk attach-new command OK  

**scollegare del disco rigido macchine virtuali &lt;nome macchina virtuale > &lt;lun >**

Questo comando disconnette un disco dati associato a una macchina virtuale Azure. &lt;LUN > identifica il disco da disconnettere. Per ottenere un elenco di dischi associato a un disco prima di scollegare, utilizzare l'elenco dischi macchine virtuali &lt;nome macchina virtuale >.

    ~$ azure vm disk detach my-vm 2
    info:   Executing command vm disk detach
    info:   vm disk detach command OK

## <a name="commands-to-manage-your-azure-cloud-services"></a>Comandi per gestire i servizi cloud Azure

Servizi cloud Azure sono applicazioni e servizi ospitati in ruoli web ruoli e di lavoro. I comandi seguenti possono essere usati per gestire servizi cloud Azure.

**servizio creare [opzioni] &lt;nome >**

Questo comando crea un servizio cloud

    ~$ azure service create newservicemsopentech
    info:    Executing command service create
    + Getting locations
    help:    Location:
      1) East Asia
      2) Southeast Asia
      3) North Europe
      4) West Europe
      5) East US
      6) West US
      : 6
    + Creating cloud service
    data:    Cloud service name newservicemsopentech
    info:    service create command OK

**servizio Mostra [opzioni] &lt;nome >**

Questo comando Visualizza i dettagli di un servizio cloud Azure

    ~$ azure service show newservicemsopentech
    info:    Executing command service show
    + Getting cloud service
    data:    Name newservicemsopentech
    data:    Url https://management.core.windows.net/9e672699-1055-41ae-9c36-e85152f2e352/services/hostedservices/newservicemsopentech
    data:    Properties location West US
    data:    Properties label newservicemsopentech
    data:    Properties status Created
    data:    Properties dateCreated
    data:    Properties dateLastModified
    info:    service show command OK

**elenco di servizi [opzioni]**

Questo comando sono elencati i servizi cloud Azure.

    ~$ azure service list
    info:   Executing command service list
    data:   Name         Status
    data:   -----------  -------
    data:   service1     Created
    data:   service2     Created
    info:   service list command OK

**servizio eliminare [opzioni] &lt;nome >**

Questo comando Elimina un servizio cloud Azure.

    ~$ azure service delete myservice
    info:   Executing command service delete myservice
    info:   cloud-service delete command OK

Per forzare l'eliminazione, utilizzare la `-q` parametro.


## <a name="commands-to-manage-your-azure-certificates"></a>Comandi per gestire i certificati di Azure

Certificati di servizio Azure sono i certificati SSL connessi al proprio account Azure. Per ulteriori informazioni sui certificati Azure, vedere [Gestione certificati](http://msdn.microsoft.com/library/azure/gg981929.aspx).

**elenco di certificato servizi [opzioni]**

Questo comando Elenca i certificati Azure.

    ~$ azure service cert list
    info:   Executing command service cert list
    + Fetching cloud services
    + Fetching certificates
    data:   Service   Thumbprint                                Algorithm
    data:   --------  ----------------------------------------  ---------
    data:   myservice  262DBF95B5E61375FA27F1E74AC7D9EAE842916C  sha1
    info:   service cert list command OK

**certificato di servizio creare &lt;dns prefisso > &lt;file > [password]**

Questo comando consente di caricare un certificato. Lasciare vuoto il campo per i certificati che non sono protetti da password viene richiesta la password.

    ~$ azure service cert create nghinazz ~/publishSet.pfx
    info:   Executing command service cert create
    Cert password:
    + Creating certificate
    info:   service cert create command OK

**Elimina certificato servizio [opzioni] &lt;identificazione personale >**

Questo comando Elimina un certificato.

    ~$ azure service cert delete 262DBF95B5E61375FA27F1E74AC7D9EAE842916C
    info:   Executing command service cert delete
    + Deleting certificate
    info:   nghinazz : cert deleted
    info:   service cert delete command OK

## <a name="commands-to-manage-your-web-apps"></a>Comandi per gestire le app web

Un'app web Azure è una configurazione web accessibile da URI. App Web sono ospitati in macchine virtuali, ma non è necessario considerare i dettagli di creare e distribuire la macchina virtuale. Questi dettagli vengono gestiti automaticamente da Azure.

**elenco dei siti [opzioni]**

Questo comando Elenca le applicazioni web.

    ~$ azure site list
    info:   Executing command site list
    data:   Name            State    Host names
    data:   --------------  -------  --------------------------------------------------
    data:   mongosite       Running  mongosite.antdf0.antares.windows.net
    data:   myphpsite       Running  myphpsite.antdf0.antares.windows.net
    data:   mydrupalsite36  Running  mydrupalsite36.antdf0.antares.windows.net
    info:   site list command OK

**sito imposta [opzioni] [nome]**

Questo comando consente di impostare opzioni di configurazione per un'app web di [nome]

    ~$ azure site set
    info:    Executing command site set
    Web site name: mydemosite
    + Getting sites
    + Updating site config information
    info:    site set command OK

**sito deploymentscript [opzioni]**

Questo comando genera uno script di distribuzione personalizzato

    ~$ azure site deploymentscript --node
    info:    Executing command site deploymentscript
    info:    Generating deployment script for node.js Web Site
    info:    Generated deployment script files
    info:    site deploymentscript command OK

**creazione di siti [opzioni] [nome]**

Questo comando crea un'app web e una directory locale.

    ~$ azure site create mysite
    info:   Executing command site create
    info:   Using location northeuropewebspace
    info:   Creating a new web site
    info:   Created web site at  mysite.antdf0.antares.windows.net
    info:   Initializing repository
    info:   Repository initialized
    info:   site create command OK

> [AZURE.NOTE] Il nome del sito deve essere univoco. È possibile creare un sito con lo stesso nome DNS di un sito esistente.

**esplorazione di siti [opzioni] [nome]**

Questo comando si apre un'applicazione web in un browser.

    ~$ azure site browse mysite
    info:   Executing command site browse
    info:   Launching browser to http://mysite.antdf0.antares-test.windows-int.net
    info:   site browse command OK

**Mostra sito [opzioni] [nome]**

Questo comando Mostra i dettagli su un'applicazione web.

    ~$ azure site show mysite
    info:   Executing command site show
    info:   Showing details for site
    data:   Site AdminEnabled true
    data:   Site HostNames mysite.antdf0.antares-test.windows-int.net
    data:   Site Name mysite
    data:   Site Owner 00060000814EDDEE
    data:   Site RepositorySiteName mysite
    data:   Site SelfLink https://s1.api.antdf0.antares.windows.net:454/subscriptions/444e62ff-4c5f-4116-a695-5c803ed584a5/webspaces/northeuropewebspace/sites/mysite
    data:   Site State Running
    data:   Site UsageState Normal
    data:   Site WebSpace northeuropewebspace
    data:   Config AppSettings
    data:   Config ConnectionStrings
    data:   Config DefaultDocuments 0=Default.htm, 1=Default.asp, 2=index.htm, 3=index.html, 4=iisstart.htm, 5=default.aspx, 6=index.php, 7=hostingstart.aspx
    data:   Config DetailedErrorLoggingEnabled false
    data:   Config HttpLoggingEnabled false
    data:   Config Metadata
    data:   Config NetFrameworkVersion v4.0
    data:   Config NumberOfWorkers 1
    data:   Config PhpVersion 5.3
    data:   Config PublishingPassword rJ}[Er2v[Y]q16B6vTD]n$[C2z}Z.pvgLfRcLnAp%ax]xstiLny};o@vmMAote@d
    data:   Config RequestTracingEnabled false
    data:   Repository https://mysite.scm.antdf0.antares-test.windows-int.net/
    info:   site show command OK

**Elimina sito [opzioni] [nome]**

Questo comando Elimina un'app web.

    ~$ azure site delete mysite
    info:   Executing command site delete
    info:   Deleting site mysite
    info:   Site mysite has been deleted
    info:   site delete command OK

 **scambio di sito [opzioni] [nome]**

Questo comando Scambia due bande orarie app web.

Questo comando supporta l'opzione aggiuntiva seguente:

**- q o **-non interattiva * *: non richiede di confermare l'operazione. Usare questa opzione in script automatici.


**inizio sito [opzioni] [nome]**

Questo comando consente di avviare un'app web.

    ~$ azure site start mysite
    info:   Executing command site start
    info:   Starting site mysite
    info:   Site mysite has been started
    info:   site start command OK

**Interrompi sito [opzioni] [nome]**

Questo comando si arresta un'app web.

    ~$ azure site stop mysite
    info:   Executing command site stop
    info:   Stopping site mysite
    info:   Site mysite has been stopped
    info:   site stop command OK

**riavvio del sito [opzioni] [nome]**

Questo comando si arresta e quindi avviare un'app web specificato.

Questo comando supporta l'opzione aggiuntiva seguente:

**-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.


**elenco di località sito [opzioni]**

Questo comando sono elencati i percorsi di app web.

    ~$ azure site location list
    info:    Executing command site location list
    + Getting locations
    data:    Name
    data:    ----------------
    data:    West Europe
    data:    West US
    data:    North Central US
    data:    North Europe
    data:    East Asia
    data:    East US
    info:    site location list command OK

###<a name="commands-to-manage-your-web-app-application-settings"></a>Comandi per gestire le impostazioni di applicazione web app

**elenco appsetting sito [opzioni] [nome]**

Questo comando Visualizza l'impostazione di app aggiunta a app web.

    ~$ azure site appsetting list
    info:    Executing command site appsetting list
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Name  Value
    data:    ----  -----
    data:    test  value
    info:    site appsetting list command OK

**sito appsetting aggiungere [opzioni] &lt;keyvaluepair > [nome]**

Questo comando aggiunge un'impostazione di app per un'applicazione web come una coppia di valore chiave.

    ~$ azure site appsetting add test=value
    info:    Executing command site appsetting add
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    + Updating site config information
    info:    site appsetting add command OK

**sito appsetting eliminare [opzioni] &lt;chiave > [nome]**

Questo comando Elimina l'impostazione di app specificato da web app.

    ~$ azure site appsetting delete test
    info:    Executing command site appsetting delete
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    Delete application setting test? [y/n] y
    + Updating site config information
    info:    site appsetting delete command OK

**sito appsetting Mostra [opzioni] &lt;chiave > [nome]**

Questo comando consente di visualizzare i dettagli dell'impostazione di app specificato

    ~$ azure site appsetting show test
    info:    Executing command site appsetting show
    Web site name: mydemosite
    + Getting sites
    + Getting site config information
    data:    Value:  value
    info:    site appsetting show command OK

###<a name="commands-to-manage-your-web-app-certificates"></a>Comandi per gestire i certificati di app web

**elenco di certificato sito [opzioni] [nome]**

Questo comando consente di visualizzare un elenco di certificati di app web.

    ~$ azure site cert list
    info:    Executing command site cert list
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Subject                       Expiration Date                    Thumbprint
    data:    ----------------------------  -----------------------------------------
    ----------------  ----------------------------------------
    data:    *.msopentech.com              Fri Nov 28 2014 09:49:57 GMT-0800 (Pacific Standard Time)  A40E82D3DC0286D1F58650E570ECF8224F69A148
    data:    msopentech.azurewebsites.net  Fri Jun 19 2015 11:57:32 GMT-0700 (Pacific Daylight Time)  CE1CD6538852BF7A5DC32001C2E26A29B541F0E8
    info:    site cert list command OK

**certificato di sito Aggiungi [opzioni] &lt;percorso certificato > [nome]**

**eliminazione del certificato di sito [opzioni] &lt;identificazione personale > [nome]**

**sito Mostra certificato [opzioni] &lt;identificazione personale > [nome]**

Questo comando Visualizza i dettagli del certificato

    ~$ azure site cert show CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    Executing command site cert show
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    data:    Certificate hostNames 0=msopentech.azurewebsites.net
    data:    Certificate expirationDate
    data:    Certificate friendlyName msopentech.azurewebsites.net
    data:    Certificate issueDate
    data:    Certificate issuer CN=MSIT Machine Auth CA 2, DC=redmond, DC=corp, DC=microsoft, DC=com
    data:    Certificate subjectName msopentech.azurewebsites.net
    data:    Certificate thumbprint CE1CD65852B38DC32001C2E0E8F7A526A29B541F
    info:    site cert show command OK

###<a name="commands-to-manage-your-web-app-connection-strings"></a>Comandi per gestire le stringhe di connessione web app

**elenco di connectionstring sito [opzioni] [nome]**

**sito connectionstring aggiungere [opzioni] &lt;connectionname > &lt;valore > &lt;tipo > [nome]**

**sito connectionstring eliminare [opzioni] &lt;connectionname > [nome]**

**sito connectionstring Mostra [opzioni] &lt;connectionname > [nome]**

###<a name="commands-to-manage-your-web-app-default-documents"></a>Comandi per gestire i documenti di predefinito web app

**elenco defaultdocument sito [opzioni] [nome]**

**sito defaultdocument aggiungere [opzioni] &lt;documento > [nome]**

**sito defaultdocument eliminare [opzioni] &lt;documento > [nome]**

###<a name="commands-to-manage-your-web-app-deployments"></a>Comandi per gestire le distribuzioni di app web

**elenco di distribuzione sito [opzioni] [nome]**

**la distribuzione del sito Mostra [opzioni] &lt;commitId > [nome]**

**Ridistribuisci distribuzione sito [opzioni] &lt;commitId > [nome]**

**sito distribuzione github [opzioni] [nome]**

**insieme di utenti distribuzione sito [opzioni] [nomeutente] [passaggio]**

###<a name="commands-to-manage-your-web-app-domains"></a>Comandi per gestire i domini di applicazione web

**elenco dei domini sito [opzioni] [nome]**

**aggiunta del dominio sito [opzioni] &lt;nome distinto > [nome]**

**eliminazione di dominio sito [opzioni] &lt;nome distinto > [nome]**

###<a name="commands-to-manage-your-web-app-handler-mappings"></a>Comandi per gestire i mapping di gestore web app

**elenco di gestore del sito [opzioni] [nome]**

**gestore del sito aggiunti [opzioni] &lt;estensione > &lt;processore > [nome]**

**eliminazione di gestore del sito [opzioni] &lt;estensione > [nome]**

###<a name="commands-to-manage-your-web-jobs"></a>Comandi per gestire i processi Web

**elenco di processi sito [opzioni] [nome]**

Questo comando Elenca tutti i processi web in un'app web.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-tipo di processo** &lt;tipo di processo >: facoltativo. Il tipo del webjob. Valore valido è "attivate" o "Continua". Per impostazione predefinita restituiscono webjobs di tutti i tipi.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**Mostra il processo [opzioni] del sito &lt;NomeProcesso > &lt;jobType > [nome]**

Questo comando Mostra i dettagli di un processo web specifico.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-tipo di processo** &lt;tipo di processo >: obbligatorio. Il tipo del webjob. Valore valido è "attivate" o "Continua".
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**eliminazione di processo sito [opzioni] &lt;NomeProcesso > &lt;jobType > [nome]**

Questo comando Elimina il processo web specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo > necessari. Il nome della webjob.
+ **-tipo di processo** &lt;tipo di processo > necessari. Il tipo del webjob. Valore valido è "attivate" o "Continua".
+ **-q** o **-interattiva**: non richiede di confermare l'operazione. Usare questa opzione in script automatici.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**caricamento di processo sito [opzioni] &lt;NomeProcesso > &lt;jobType > <jobFile> [nome]**

Questo comando Elimina il processo web specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-tipo di processo** &lt;tipo di processo >: obbligatorio. Il tipo del webjob. Valore valido è "attivate" o "Continua".
+ **-file di processo** &lt;file di processo >: obbligatorio. File di processo.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**inizio del processo [opzioni] del sito &lt;NomeProcesso > &lt;jobType > [nome]**

Questo comando avvia il processo web specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-tipo di processo** &lt;tipo di processo >: obbligatorio. Il tipo del webjob. Valore valido è "attivate" o "Continua".
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**interruzione del processo di sito [opzioni] &lt;NomeProcesso > &lt;jobType > [nome]**

Questo comando interrompe il processo web specificato. Solo i processi continui possono interromperla.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

###<a name="commands-to-manage-your-web-jobs-history"></a>Comandi per gestire la cronologia di processi Web

**elenco cronologia del processo sito [opzioni] [nomeprocesso] [nome]**

Questo comando consente di visualizzare la cronologia di esecuzione del processo web specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

**cronologia dei processi sito Mostra [opzioni] [nomeprocesso] [runId] [nome]**

Consente di ottenere i dettagli del processo di esecuzione del processo di web specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-nome processo** &lt;nome processo >: obbligatorio. Il nome della webjob.
+ **-Esegui id** &lt;id Esegui >: facoltativo. Id della cronologia Esegui. Se non viene specificato, visualizzare le ultime Esegui.
+ **-intervallo aperto** &lt;intervallo aperto >: il nome dell'intervallo aperto per riavviare.

###<a name="commands-to-manage-your-web-app-diagnostics"></a>Comandi per la gestione del sistema di diagnostica web app

**Registro sito scaricare [opzioni] [nome]**

Scaricare un file ZIP che contiene la diagnostica dell'applicazione web.

    ~$ azure site log download
    info:    Executing command site log download
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    + Downloading diagnostic log to diagnostics.zip
    info:    site log download command OK

**tail log sito [opzioni] [nome]**

Questo comando si connette al terminale al servizio di trasmissione log.

    ~$ azure site log tail
    info:    Executing command site log tail
    Web site name: mydemosite
    + Getting sites
    + Getting site information
    2013-11-19T17:24:17  Welcome, you are now connected to log-streaming service.

**registro del sito imposta [opzioni] [nome]**

Questo comando consente di configurare le opzioni di diagnostiche per un'app web.

    ~$ azure site log set -a
    info:    Executing command site log set
    + Getting output options
    help:    Output:
      1) file
      2) storage
      : 1
    Web site name: mydemosite
    + Getting locations
    + Getting sites
    + Getting site information
    + Getting diagnostic settings
    + Updating diagnostic settings
    info:    site log set command OK

###<a name="commands-to-manage-your-web-app-repositories"></a>Comandi per la gestione dei repository app web

**diramazione repository sito [opzioni] &lt;ramo > [nome]**

**eliminazione di archivio sito [opzioni] [nome]**

**sincronizzazione di archivio del sito [opzioni] [nome]**

###<a name="commands-to-manage-your-web-app-scaling"></a>Comandi per gestire il ridimensionamento delle app web

**modalità di scala sito [opzioni] &lt;modalità > [nome]**

**scala sito istanze [opzioni] &lt;istanze > [nome]**


## <a name="commands-to-manage-azure-mobile-services"></a>Comandi per gestire i servizi di dispositivi mobili Windows Azure

Servizi mobili Azure Raggruppa un insieme di Azure servizi che consentono di back-end funzionalità per le app. Comandi di servizi di dispositivi mobili sono suddivisi nelle categorie seguenti:

+ [Comandi per gestire le istanze del servizio per dispositivi mobili](#Mobile_Services)
+ [Comandi per la gestione configurazione del servizio per dispositivi mobili](#Mobile_Configuration)
+ [Comandi per gestire le tabelle da servizi per dispositivi mobili](#Mobile_Tables)
+ [Comandi per gestire gli script di servizio per dispositivi mobili](#Mobile_Scripts)
+ [Comandi per gestire processi pianificati](#Mobile_Jobs)
+ [Comandi per scalare un servizio per dispositivi mobili](#Mobile_Scale)

Le opzioni seguenti si applicano alla maggior parte dei comandi di servizi mobili:

+ **-h** o **-Guida**: informazioni sull'utilizzo di output Visualizza.
+ **-s `<id>` ** o **-abbonamento `<id>` **: utilizzare una sottoscrizione specifica, specificata come `<id>`.
+ **+ v** o **-dettagliato**: scrivere output dettagliato.
+ **-json**: scrivere JSON output.

### <a name="Mobile_Services"></a>Comandi per gestire le istanze del servizio per dispositivi mobili

**posizioni per dispositivi mobili [opzioni]**

Questo comando Elenca geografiche supportate dai servizi Mobile.

    ~$ azure mobile locations
    info:    Executing command mobile locations
    info:    East US (default)
    info:    West US
    info:    North Europe

**Mobile creare [opzioni] [nome] [sqlAdminUsername] [sqlAdminPassword]**

Questo comando crea un servizio per dispositivi mobili insieme a un Database SQL server.

    ~$ azure mobile create todolist your_login_name Secure$Password
    info:    Executing command mobile create
    + Creating mobile service
    info:    Overall application state: Healthy
    info:    Mobile service (todolist) state: ProvisionConfigured
    info:    SQL database (todolist_db) state: Provisioned
    info:    SQL server (e96ean1c6v) state: ProvisionConfigured
    info:    mobile create command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **- r `<sqlServer>` ** o **SQL Server, ossia `<sqlServer>` **: utilizzare un server di Database SQL esistente, specificato come `<sqlServer>`.
+ **-d `<sqlDb>` ** o **- sqlDb `<sqlDb>` **: utilizzare un database SQL esistente, specificato come `<sqlDb>`.
+ **-l `<location>` ** o **-percorso `<location>` **: creare il servizio in una posizione specifica, specificata come `<location>`. Esegui azure posizioni per dispositivi mobili per ottenere posizioni disponibili.
+ **- sqlLocation `<location>` **: creare SQL server in una determinata `<location>`; impostazioni predefinite per il percorso del servizio per dispositivi mobili.

**Elimina Mobile [opzioni] [nome]**

Questo comando Elimina un servizio per dispositivi mobili e il Database di SQL e i server.

    ~$ azure mobile delete todolist -a -q
    info:    Executing command mobile delete
    data:    Mobile service todolist
    data:    SQL database todolistAwrhcL60azo1C401
    data:    SQL server fh1kvbc7la
    + Deleting mobile service
    info:    Deleted mobile service
    + Deleting SQL server
    info:    Deleted SQL server
    + Deleting mobile application
    info:    Deleted mobile application
    info:    mobile delete command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **+ d** o **- deleteData**: eliminare tutti i dati da questo servizio mobile dal database.
+ **-** oppure **- deleteAll**: eliminare il Database di SQL e server.
+ **-q** o **-interattiva**: non richiede di confermare l'operazione. Usare questa opzione in script automatici.

**elenco Mobile [opzioni]**

Questo comando sono elencati i servizi per dispositivi mobili.

    ~$ azure mobile list
    info:    Executing command mobile list
    data:    Name          State  URL
    data:    ------------  -----  --------------------------------------
    data:    todolist      Ready  https://todolist.azure-mobile.net/
    data:    mymobileapp   Ready  https://mymobileapp.azure-mobile.net/
    info:    mobile list command OK

**Mostra dispositivi mobili [opzioni] [nome]**

Questo comando consente di visualizzare i dettagli di un servizio per dispositivi mobili.

    ~$ azure mobile show todolist
    info:    Executing command mobile show
    + Getting information
    info:    Mobile application
    data:    status Healthy
    data:    Mobile service name todolist
    data:    Mobile service status ProvisionConfigured
    data:    SQL database name todolistAwrhcL60azo1C401
    data:    SQL database status Linked
    data:    SQL server name fh1kvbc7la
    data:    SQL server status Linked
    info:    Mobile service
    data:    name todolist
    data:    state Ready
    data:    applicationUrl https://todolist.azure-mobile.net/
    data:    applicationKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    masterKey XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    data:    webspace WESTUSWEBSPACE
    data:    region West US
    data:    tables TodoItem
    info:    mobile show command OK

**riavvio Mobile [opzioni] [nome]**

Questo comando riavvia un'istanza del servizio per dispositivi mobili.

    ~$ azure mobile restart todolist
    info:    Executing command mobile restart
    + Restarting mobile service
    info:    Service was restarted.
    info:    mobile restart command OK

**Registro Mobile [opzioni] [nome]**

Questo comando restituisce i registri del servizio per dispositivi mobili, filtrando tutti i tipi di log ma `error`.

    ~$ azure mobile log todolist -t error
    info:    Executing command mobile log
    data:
    data:    timeCreated 2013-01-07T16:04:43.351Z
    data:    type error
    data:    source /scheduler/TestingLogs.js
    data:    message This is an error.
    data:
    info:    mobile log command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **- r `<query>` ** o **-query `<query>` **: esegue la query log specificato.
+ **-t `<type>` ** o **-tipo `<type>` **: filtrare i registri di restituito dalla voce `<type>`, che può essere `information`, `warning`, o `error`.
+ **-k `<skip>` ** o **-ignorare `<skip>` **: ignora il numero di righe specificato dal `<skip>`.
+ **-p `<top>` ** o **-top `<top>` **: restituisce un numero specifico di righe specificato da `<top>`.

> [AZURE.NOTE] Il **-query** parametro avrà la priorità sul **-tipo**, **-ignorare**, e **-top**.

**Recupera Mobile [opzioni] [unhealthyservicename] [healthyservicename]**

Questo comando Ripristina un servizio per dispositivi mobili non corretti spostandolo in un servizio di dispositivi mobili integro in un'area diversa.

Questo comando supporta l'opzione aggiuntiva seguente:

**-q** o **-interattiva**: eliminare la richiesta di confermare l'operazione di ripristino.

**chiave per dispositivi mobili rigenerare [opzioni] [nome] [tipo]**

Questo comando Rigenera il tasto applicazione di servizio per dispositivi mobili.

    ~$ azure mobile key regenerate todolist application
    info:    Executing command mobile key regenerate
    info:    New application key is SmLorAWVfslMcOKWSsuJvuzdJkfUpt40
    info:    mobile key regenerate command OK

Tipi principali sono `master` e `application`.

> [AZURE.NOTE] Quando si rigenerare chiavi, client che utilizzano la chiave precedente sarà possibile accedere al servizio per dispositivi mobili. Quando si rigenera il tasto applicazione, è necessario aggiornare l'app con il nuovo valore chiave.

**set di chiavi per dispositivi mobili [opzioni] [nome] [tipo] [valore]**

Questo comando imposta la chiave del servizio mobile su un valore specifico.


### <a name="Mobile_Configuration"></a>Comandi per la gestione configurazione del servizio per dispositivi mobili

**elenco di configurazione per dispositivi mobili [opzioni] [nome]**

Questo comando Elenca le opzioni di configurazione di un servizio per dispositivi mobili.

    ~$ azure mobile config list todolist
    info:    Executing command mobile config list
    + Getting mobile service configuration
    data:    dynamicSchemaEnabled true
    data:    microsoftAccountClientSecret Not configured
    data:    microsoftAccountClientId Not configured
    data:    microsoftAccountPackageSID Not configured
    data:    facebookClientId Not configured
    data:    facebookClientSecret Not configured
    data:    twitterClientId Not configured
    data:    twitterClientSecret Not configured
    data:    googleClientId Not configured
    data:    googleClientSecret Not configured
    data:    apnsMode none
    data:    apnsPassword Not configured
    data:    apnsCertifcate Not configured
    info:    mobile config list command OK

**configurazione di dispositivi mobili ottenere [opzioni] [nome] [chiave]**

Consente di ottenere un'opzione di configurazione specifiche di un servizio per dispositivi mobili, in questo caso dinamiche dello schema.

    ~$ azure mobile config get todolist dynamicSchemaEnabled
    info:    Executing command mobile config get
    data:    dynamicSchemaEnabled true
    info:    mobile config get command OK

**gruppo di configurazione per dispositivi mobili [opzioni] [nome] [key] [valore]**

Questo comando consente di impostare un'opzione di configurazione specifiche di un servizio per dispositivi mobili, in questo caso dinamiche dello schema.

    ~$ azure mobile config set todolist dynamicSchemaEnabled false
    info:    Executing command mobile config set
    info:    mobile config set command OK


### <a name="Mobile_Tables"></a>Comandi per gestire le tabelle da servizi per dispositivi mobili

**elenco di tabella per dispositivi mobili [opzioni] [nome]**

Questo comando Elenca tutte le tabelle nel servizio di posta per dispositivi mobili.

    ~$azure mobile table list todolist
    info:    Executing command mobile table list
    data:    Name      Indexes  Rows
    data:    --------  -------  ----
    data:    Channel   1        0
    data:    TodoItem  1        0
    info:    mobile table list command OK

**Mostra tabella Mobile [opzioni] [nome] [tablename]**

Questo comando Visualizza restituisce i dettagli di una tabella specifica.

    ~$azure mobile table show todolist
    info:    Executing command mobile table show
    + Getting table information
    info:    Table statistics:
    data:    Number of records 5
    info:    Table operations:
    data:    Operation  Script       Permissions
    data:    ---------  -----------  -----------
    data:    insert     1900 bytes   user
    data:    read       Not defined  user
    data:    update     Not defined  user
    data:    delete     Not defined  user
    info:    Table columns:
    data:    Name  Type           Indexed
    data:    ----  -------------  -------
    data:    id    bigint(MSSQL)  Yes
    data:    text      string
    data:    complete  boolean
    info:    mobile table show command OK

**tabella per dispositivi mobili creare [opzioni] [nome] [tablename]**

Questo comando crea una tabella.

    ~$azure mobile table create todolist Channels
    info:    Executing command mobile table create
    + Creating table
    info:    mobile table create command OK

Questo comando supporta l'opzione aggiuntiva seguente:

+ **-p `&lt;permissions>` ** o **-autorizzazioni `&lt;permissions>` **: elenco delimitato da virgola di `<operation>` = `<permission>` coppie di parole, in cui `<operation>` è `insert`, `read`, `update`, o `delete` e `&lt;permissions>` è `public`, `application` (impostazione predefinita), `user`, o `admin`.

**leggere i dati per dispositivi mobili [opzioni] [nome] [tablename] [query]**

Questo comando legge i dati da una tabella.

    ~$azure mobile data read todolist TodoItem
    info:    Executing command mobile data read
    data:    id  text     complete
    data:    --  -------  --------
    data:    1   item #1  false
    data:    2   item #2  true
    data:    3   item #3  false
    data:    4   item #4  true
    info:    mobile data read command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-k `<skip>` ** o **-ignorare `<skip>` **: ignora il numero di righe specificato dal `<skip>`.
+ **-t `<top>` ** o **-top `<top>` **: restituisce un numero specifico di righe specificato da `<top>`.
+ **-l** o **-elenco**: restituisce dati in un formato di elenco.

**aggiornamento di tabella per dispositivi mobili [opzioni] [nome] [tablename]**

Il comando Elimina autorizzazioni su una tabella diventa disponibile solo per gli amministratori.

    ~$azure mobile table update todolist Channels -p delete=admin
    info:    Executing command mobile table update
    + Updating permissions
    info:    Updated permissions
    info:    mobile table update command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-p `&lt;permissions>` ** o **-autorizzazioni `&lt;permissions>` **: elenco delimitato da virgola di `<operation>` = `<permission>` coppie di parole, in cui `<operation>` è `insert`, `read`, `update`, o `delete` e `&lt;permissions>` è `public`, `application` (impostazione predefinita), `user`, o `admin`.
+ **- Elimina colonna `<columns>` **: elenco delimitato da virgola di colonne da eliminare, come `<columns>`.
+ **-q** o **-interattiva**: Elimina le colonne senza chiedere conferma.
+ **- addIndex `<columns>` **: elenco delimitato da virgola delle colonne da includere nell'indice analitico.
+ **- deleteIndex `<columns>` **: elenco delimitato da virgola di colonne da escludere dall'indice.

**tabella per dispositivi mobili eliminare [opzioni] [nome] [tablename]**

Questo comando consente di eliminare una tabella.

    ~$azure mobile table delete todolist Channels
    info:    Executing command mobile table delete
    Do you really want to delete the table (yes/no): yes
    + Deleting table
    info:    mobile table delete command OK

Specificare il parametro - q per eliminare la tabella senza conferma. Ripetere l'operazione per evitare il blocco di script di automazione.

**dati per dispositivi mobili troncano [opzioni] [nome] [tablename]**

Questo comando consente di rimuovere tutte le righe di dati della tabella.

    ~$azure mobile data truncate todolist TodoItem
    info:    Executing command mobile data truncate
    info:    There are 7 data rows in the table.
    Do you really want to delete all data from the table? (y/n): y
    info:    Deleted 7 rows.
    info:    mobile data truncate command OK


### <a name="Mobile_Scripts"></a>Comandi per gestire gli script

Comandi in questa sezione vengono utilizzati per gestire gli script del server che appartengono a un servizio per dispositivi mobili. Per ulteriori informazioni, vedere [lavorare con gli script di server in servizi mobili](https://github.com/Azure/azure-mobile-services/blob/master/docs/mobile-services-how-to-use-server-scripts.md).

**elenco di script per dispositivi mobili [opzioni] [nome]**

Questo comando Elenca gli script registrati, inclusi gli script di tabella e utilità di pianificazione.

    ~$azure mobile script list todolist
    info:    Executing command mobile script list
    + Getting script information
    info:    Table scripts
    data:    Name                   Size
    data:    ---------------------  ----
    data:    table/TodoItem.delete  256
    data:    table/Devices.insert   1660
    error:   Unable to get shared scripts
    info:    Scheduler scripts
    data:    Name                 Status     Interval   Last run   Next run
    data:    -------------------  ---------  ---------  ---------  ---------
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    data:    scheduler/undefined  undefined  undefined  undefined  undefined
    info:    mobile script list command OK

**download di script per dispositivi mobili [opzioni] [nome] [scriptname]**

Questo comando Scarica lo script Inserisci dalla tabella TodoItem in un file denominato `todoitem.insert.js` nel `table` sottocartella.

    ~$azure mobile script download todolist table/todoitem.insert.js
    info:    Executing command mobile script download
    info:    Saved script to ./table/todoitem.insert.js
    info:    mobile script download command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-p `<path>` ** o **-percorso `<path>` **: il percorso del file in cui salvare lo script in cui la cartella di lavoro corrente è il valore predefinito.
+ **-f `<file>` ** o **-file `<file>` **: il nome del file in cui salvare lo script.
+ **-o** o **-ignorare**: sovrascrivere un file esistente.
+ **-c** o **-console**: scrivere lo script nella console anziché in un file.

**caricamento di script per dispositivi mobili [opzioni] [nome] [scriptname]**

Questo comando consente di caricare uno script denominato `todoitem.insert.js` dalla `table` sottocartella.

    ~$azure mobile script upload todolist table/todoitem.insert.js
    info:    Executing command mobile script upload
    info:    mobile script upload command OK

Il nome del file deve essere composte dai nomi di tabella e il funzionamento. Deve trovarsi nella sottocartella tabella relativa al percorso in cui viene eseguito il comando. È inoltre possibile utilizzare il **-f `<file>` ** o **-file `<file>` ** parametro per specificare un nome di file diverso e il percorso del file che contiene lo script per registrare.


**eliminazione di script per dispositivi mobili [opzioni] [nome] [scriptname]**

Questo comando rimuove lo script Inserisci esistente dalla tabella TodoItem.

    ~$azure mobile script delete todolist table/todoitem.insert.js
    info:    Executing command mobile script delete
    info:    mobile script delete command OK

### <a name="Mobile_Jobs"></a>Comandi per gestire processi pianificati

Comandi in questa sezione vengono utilizzati per gestire processi pianificati che appartengono a un servizio per dispositivi mobili. Per ulteriori informazioni, vedere [pianificare processi](http://msdn.microsoft.com/library/windowsazure/jj860528.aspx).

**elenco di processi per dispositivi mobili [opzioni] [nome]**

Questo comando Elenca programmate.

    ~$azure mobile job list todolist
    info:    Executing command mobile job list
    info:    Scheduled jobs
    data:    Job name    Script name           Status    Interval     Last run              Next run
    data:    ----------  --------------------  --------  -----------  --------------------  --------------------
    data:    getUpdates  scheduler/getUpdates  enabled   15 [minute]  2013-01-14T16:15:00Z  2013-01-14T16:30:00Z
    info:    You can manipulate scheduled job scripts using the 'azure mobile script' command.
    info:    mobile job list command OK

**processo per dispositivi mobili creare [opzioni] [nome] [nomeprocesso]**

Questo comando crea un processo denominato `getUpdates` è pianificato per l'esecuzione ogni ora.

    ~$azure mobile job create -i 1 -u hour todolist getUpdates
    info:    Executing command mobile job create
    info:    Job was created in disabled state. You can enable the job using the 'azure mobile job update' command.
    info:    You can manipulate the scheduled job script using the 'azure mobile script' command.
    info:    mobile job create command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-i `<number>` ** o **-intervallo `<number>` **: intervallo del processo, come un numero intero. Il valore predefinito è `15`.
+ **-u `<unit>` ** o **- intervalUnit `<unit>` **: l'unità per l' _intervallo_, che può essere uno dei seguenti valori:
    + **minuto** (impostazione predefinita)
    + **ora**
    + **giorno**
    + **mese**
    + **Nessuno** (processi su richiesta)
+ **-t `<time>`** **- ora di inizio `<time>` ** L'ora di inizio del primo eseguire per lo script in formato ISO. Il valore predefinito è `now`.

> [AZURE.NOTE] Nuovi processi vengono creati nello stato disabilitato poiché uno script deve ancora essere caricato. Usare il comando **script mobile carica** per caricare uno script e il comando **Aggiorna processo per dispositivi mobili** per attivare il processo.

**aggiornamento di dispositivi mobili processo [opzioni] [nome] [nomeprocesso]**

Il comando seguente consente la disabilitazione `getUpdates` processo.

    ~$azure mobile job update -a enabled todolist getUpdates
    info:    Executing command mobile job update
    info:    mobile job update command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-i `<number>` ** o **-intervallo `<number>` **: intervallo del processo, come un numero intero. Il valore predefinito è `15`.
+ **-u `<unit>` ** o **- intervalUnit `<unit>` **: l'unità per l' _intervallo_, che può essere uno dei seguenti valori:
    + **minuto** (impostazione predefinita)
    + **ora**
    + **giorno**
    + **mese**
    + **Nessuno** (processi su richiesta)
+ **-t `<time>`** **- ora di inizio `<time>` ** L'ora di inizio del primo eseguire per lo script in formato ISO. Il valore predefinito è `now`.
+ **- `<status>` ** o **-stato `<status>` **: lo stato del processo, che può essere `enabled` o `disabled`.

**processo per dispositivi mobili eliminare [opzioni] [nome] [nomeprocesso]**

Questo comando rimuove getUpdates programmata dal server di elenco attività.

    ~$azure mobile job delete todolist getUpdates
    info:    Executing command mobile job delete
    info:    mobile job delete command OK

> [AZURE.NOTE] Eliminazione di un processo elimina anche lo script caricato.

### <a name="Mobile_Scale"></a>Comandi per scalare un servizio per dispositivi mobili

Comandi in questa sezione vengono utilizzati per ridimensionare un servizio per dispositivi mobili. Per ulteriori informazioni, vedere [ridimensionare un servizio per dispositivi mobili](http://msdn.microsoft.com/library/windowsazure/jj193178.aspx).

**scala mobile Mostra [opzioni] [nome]**

Questo comando consente di visualizzare informazioni sulla scala, inclusi modalità di calcolo corrente e il numero di istanze.

    ~$azure mobile scale show todolist
    info:    Executing command mobile scale show
    data:    webspace WESTUSWEBSPACE
    data:    computeMode Free
    data:    numberOfInstances 1
    info:    mobile scale show command OK

**scala mobile modificare [opzioni] [nome]**

Il comando modifica la scala del servizio per dispositivi mobili dalla versione gratuita alla modalità premium.

    ~$azure mobile scale change -c Reserved -i 1 todolist
    info:    Executing command mobile scale change
    + Rescaling the mobile service
    info:    mobile scale change command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **- c `<mode>` ** o **- computeMode `<mode>` **: la modalità di calcolo deve essere `Free` o `Reserved`.
+ **-i `<count>` ** o **- numberOfInstances `<count>` **: il numero di istanze utilizzato durante l'esecuzione in modalità riservata.

> [AZURE.NOTE] Quando si imposta la modalità di calcolo `Reserved`, tutti i servizi mobili nella stessa regione eseguito in modalità di premium.


###<a name="commands-to-enable-preview-features-for-your-mobile-service"></a>Comandi per abilitare le caratteristiche di anteprima per il servizio Mobile

**elenco di anteprima per dispositivi mobili [opzioni] [nome]**

Questo comando Visualizza le funzionalità di anteprima disponibili nel servizio specificato e se abilitate.

    ~$ azure mobile preview list mysite
    info:    Executing command mobile preview list
    + Getting preview features
    data:    Preview feature  Enabled
    data:    ---------------  -------
    data:    SourceControl    No
    data:    Users            No
    info:    You can enable preview features using the 'azure mobile preview enable' command.
    info:    mobile preview list command OK

**anteprima per dispositivi mobili abilitare [opzioni] [nome] [NomeFunzionalità]**

Questo comando consente la caratteristica anteprima specificata di un servizio per dispositivi mobili. Dopo l'abilitazione, le funzionalità preview non possono essere disabilitate per un servizio per dispositivi mobili.

###<a name="commands-to-manage-your-mobile-service-apis"></a>Comandi per gestire l'API del servizio per dispositivi mobili

**elenco di api per dispositivi mobili [opzioni] [nome]**

Questo comando consente di visualizzare un elenco di dispositivi mobili service API personalizzate creato per il servizio mobile.

    ~$ azure mobile api list mysite
    info:    Executing command mobile api list
    + Retrieving list of APIs
    info:    APIs
    data:    Name                  Get          Put          Post         Patch        Delete
    data:    --------------------  -----------  -----------  -----------  -----------  -----------
    data:    myCustomRetrieveAPI   application  application  application  application  application
    info:    You can manipulate API scripts using the 'azure mobile script' command.
    info:    mobile api list command OK

**api per dispositivi mobili di creare [opzioni] [nome] [NOMEAPI]**

Crea una API personalizzato servizio mobile

    ~$ azure mobile api create mysite myCustomRetrieveAPI
    info:    Executing command mobile api create
    + Creating custom API: 'myCustomRetrieveAPI'
    info:    API was created successfully. You can modify the API using the 'azure mobile script' command.
    info:    mobile api create command OK

Questo comando supporta l'opzione aggiuntiva seguente:

**-p** o **-autorizzazioni** &lt;autorizzazioni >: un elenco delimitato da virgola &lt;metodo > =&lt;autorizzazione > coppie di parole.

**aggiornamento di api per dispositivi mobili [opzioni] [nome] [NOMEAPI]**

Questo comando Aggiorna l'API di personalizzato specificato del servizio per dispositivi mobili.

Questo comando supporta l'opzione aggiuntiva seguente:

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-p** o **-autorizzazioni** &lt;autorizzazioni >: un elenco delimitato da virgola &lt;metodo > =&lt;autorizzazione > coppie di parole.
+ **-f** o **-forzare**: sostituisce tutte le modifiche al file di metadati autorizzazioni.

**api per dispositivi mobili eliminare [opzioni] [nome] [NOMEAPI]**

    ~$ azure mobile api delete mysite myCustomRetrieveAPI
    info:    Executing command mobile api delete
    + Deleting API: 'myCustomRetrieveAPI'
    info:    mobile api delete command OK

Questo comando Elimina l'API di personalizzato specificato del servizio per dispositivi mobili.

###<a name="commands-to-manage-your-mobile-application-app-settings"></a>Comandi per gestire le impostazioni di app per dispositivi mobili dell'applicazione

**elenco di dispositivi mobili appsetting [opzioni] [nome]**

Questo comando consente di visualizzare le impostazioni di app per dispositivi mobili dell'applicazione del servizio specificato.

    ~$ azure mobile appsetting list mysite
    info:    Executing command mobile appsetting list
    + Retrieving app settings
    data:    Name               Value
    data:    -----------------  -----
    data:    enablebetacontent  true
    info:    mobile appsetting list command OK

**dispositivi mobili appsetting aggiungere [opzioni] [nome] [nome] [valore]**

Questo comando aggiunge un'impostazione di applicazione personalizzata per il servizio mobile.

    ~$ azure mobile appsetting add mysite enablebetacontent true
    info:    Executing command mobile appsetting add
    + Retrieving app settings
    + Adding app setting
    info:    mobile appsetting add command OK

**eliminazione di dispositivi mobili appsetting [opzioni] [nome] [nome]**

Questo comando rimuove l'impostazione dell'applicazione specificato per il servizio mobile.

    ~$ azure mobile appsetting delete mysite enablebetacontent
    info:    Executing command mobile appsetting delete
    + Retrieving app settings
    + Removing app setting 'enablebetacontent'
    info:    mobile appsetting delete command OK

**Mostra appsetting Mobile [opzioni] [nome] [nome]**

Questo comando rimuove l'impostazione dell'applicazione specificato per il servizio mobile.

    ~$ azure mobile appsetting show mysite enablebetacontent
    info:    Executing command mobile appsetting show
    + Retrieving app settings
    info:    enablebetacontent: true
    info:    mobile appsetting show command OK

## <a name="manage-tool-local-settings"></a>Gestire le impostazioni locali strumento

Impostazioni locali sono l'ID di abbonamento e nome dell'Account di archiviazione predefinito.

**elenco di configurazione [opzioni]**

Questo comando consente di visualizzare le impostazioni di configurazione.

    ~$ azure config list
    info:   Displaying config settings
    data:   Setting                Value
    data:   ---------------------  ------------------------------------
    data:   subscription           32-digit-subscription-key
    data:   defaultStorageAccount  name

**configurazione imposta [opzioni] &lt;nome&gt;,&lt;valore&gt;**

Il comando modifica un'impostazione di configurazione.

    ~$ azure config set defaultStorageAccount myname
    info:   Setting 'defaultStorageAccount' to value 'myname'
    info:   Changes saved.

## <a name="commands-to-manage-service-bus"></a>Comandi per gestire Bus di servizio

Utilizzare questi comandi per gestire il proprio account di servizio Bus

**controllo dello spazio dei nomi SB [opzioni] &lt;nome >**

Verificare che uno spazio dei nomi di servizio bus legali e disponibile.

**spazio dei nomi SB creare &lt;nome > &lt;posizione >**

Crea uno spazio dei nomi Bus di servizio.

    ~$ azure sb namespace create mysbnamespacea-test "West US"
    info:    Executing command sb namespace create
    + Creating namespace mysbnamespacea-test in region West US
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Activating
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    _: [object Object]
    info:    sb namespace create command OK


**spazio dei nomi SB eliminare &lt;nome >**

Rimuovere uno spazio dei nomi.

    ~$ azure sb namespace delete mysbnamespacea-test
    info:    Executing command sb namespace delete
    Delete namespace mysbnamespacea-test? [y/n] y
    + Deleting namespace mysbnamespacea-test
    info:    sb namespace delete command OK

**elenco di nomi SB**

Elencare tutti gli spazi dei nomi creati per il proprio account.

    ~$ azure sb namespace list
    info:    Executing command sb namespace list
    + Getting namespaces
    data:    Name                 Region   Status
    data:    -------------------  -------  ------
    data:    mysbnamespacea-test  West US  Active
    info:    sb namespace list command OK


**elenco di località SB dello spazio dei nomi**

Visualizzare un elenco di tutte le posizioni disponibili dello spazio dei nomi.

    ~$ azure sb namespace location list
    info:    Executing command sb namespace location list
    + Getting locations
    data:    Name              Code
    data:    ----------------  ----------------
    data:    East Asia         East Asia
    data:    West Europe       West Europe
    data:    North Europe      North Europe
    data:    East US           East US
    data:    Southeast Asia    Southeast Asia
    data:    North Central US  North Central US
    data:    West US           West US
    data:    South Central US  South Central US
    info:    sb namespace location list command OK

**Mostra spazio dei nomi SB &lt;nome >**

Visualizzare i dettagli di un determinato spazio dei nomi.

    ~$ azure sb namespace show mysbnamespacea-test
    info:    Executing command sb namespace show
    + Getting namespace
    data:    Name: mysbnamespacea-test
    data:    Region: West US
    data:    DefaultKey: fBu8nQ9svPIesFfMFVhCFD+/sY0rRbifWMoRpYy0Ynk=
    data:    Status: Active
    data:    CreatedAt: 2013-11-14T16:23:29.32Z
    data:    AcsManagementEndpoint: https://mysbnamespacea-test-sb.accesscontrol.windows.net/
    data:    ServiceBusEndpoint: https://mysbnamespacea-test.servicebus.windows.net/

    data:    ConnectionString: Endpoint=sb://mysbnamespacea-test.servicebus.windows.
    net/;SharedSecretIssuer=owner;SharedSecretValue=fBu8nQ9svPIesFfMFVhCFD+/sY0rRbif
    WMoRpYy0Ynk=
    data:    SubscriptionId: 8679c8be3b0549d9b8fb4bd232a48931
    data:    Enabled: true
    data:    UpdatedAt: 2013-11-14T16:25:37.85Z
    info:    sb namespace show command OK

**spazio dei nomi SB verificare &lt;nome >**

Verificare se lo spazio dei nomi.

## <a name="commands-to-manage-your-storage-objects"></a>Comandi per gestire gli oggetti di archiviazione

###<a name="commands-to-manage-your-storage-accounts"></a>Comandi per gestire gli account di archiviazione

**elenco di account di archiviazione [opzioni]**

Questo comando Visualizza gli account di archiviazione dall'abbonamento.

    ~$ azure storage account list
    info:    Executing command storage account list
    + Getting storage accounts
    data:    Name             Label  Location
    data:    ---------------  -----  --------
    data:    mybasestorage           West US
    info:    storage account list command OK

**Mostra account lo spazio di archiviazione [opzioni]<name>**

Questo comando consente di visualizzare informazioni sull'account di archiviazione specificato incluse le URI e account.

**creazione di account di archiviazione [opzioni]<name>**

Questo comando crea un account di archiviazione in base alle opzioni specificate.

    ~$ azure storage account create mybasestorage --label PrimaryStorage --location "West US"
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-e** o **-etichetta** &lt;etichetta >: l'etichetta per l'account di archiviazione.
+ **-d** o **-Descrizione** &lt;descrizione >: l'account di archiviazione di descrizione.
+ **-l** o **-percorso** &lt;nome >: l'area geografica in cui si desidera creare l'account di archiviazione.
+ **-** o **-gruppo affinità** &lt;nome >: gruppo affinità a cui associare l'account di archiviazione. 
+ **-tipo**: indica il tipo di account da creare: uno spazio di archiviazione Standard con l'opzione ridondanza (LRS/ZRS/GRS/RAGRS) o lo spazio di archiviazione Premium (PLRS).

**account di archiviazione [opzioni]<name>**

Questo comando Aggiorna l'account di archiviazione specificato.

    ~$ azure storage account set mybasestorage --kind Storage --sku-name GRS
    info:    Executing command storage account set
    + Updating storage account
    info:    storage account set command OK

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-e** o **-etichetta** &lt;etichetta >: l'etichetta per l'account di archiviazione.
+ **-d** o **-Descrizione** &lt;descrizione >: l'account di archiviazione di descrizione.
+ **-l** o **-percorso** &lt;nome >: l'area geografica in cui si desidera creare l'account di archiviazione.
+ **-tipo**: indica il nuovo tipo di account: uno spazio di archiviazione Standard con l'opzione ridondanza (LRS/ZRS/GRS/RAGRS) o lo spazio di archiviazione Premium (PLRS).

**eliminazione di account di archiviazione [opzioni]<name>**

Questo comando Elimina l'account di archiviazione specificato.

Questo comando supporta l'opzione aggiuntiva seguente:

**-q** o **-interattiva**: non richiede di confermare l'operazione. Usare questa opzione in script automatici.

###<a name="commands-to-manage-your-storage-account-keys"></a>Comandi per la gestione delle chiavi account lo spazio di archiviazione

**chiavi per l'account archiviazione elenco [opzioni]<name>**

Questo comando Elenca le chiavi primarie e secondarie per l'account di archiviazione specificato.

**chiavi per l'account archiviazione rinnovare [opzioni]<name>**

###<a name="commands-to-manage-your-storage-container"></a>Comandi per gestire il contenitore di spazio di archiviazione

**elenco dei contenitori di spazio di archiviazione [opzioni] [prefisso]**

Questo comando Visualizza l'elenco di contenitore di spazio di archiviazione per un account di archiviazione specificato. Account di archiviazione specificato dalla stringa di connessione o la chiave account e nome account lo spazio di archiviazione.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione in modalità di debug.

**Mostra contenitore dello spazio di archiviazione [opzioni] [contenitore]**
**contenitore archiviazione creare [opzioni] [contenitore]**

Questo comando crea un contenitore di spazio di archiviazione per l'account di archiviazione specificato. Account di archiviazione specificato dalla stringa di connessione o la chiave account e nome account lo spazio di archiviazione.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione
+ **-debug**: esegue il comando di spazio di archiviazione in modalità di debug.

**Elimina contenitore dello spazio di archiviazione [opzioni] [contenitore]**

Questo comando Elimina il contenitore di archiviazione specificato. Account di archiviazione specificato dalla stringa di connessione o la chiave account e nome account lo spazio di archiviazione.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione in modalità di debug.

**contenitore di spazio di archiviazione imposta [opzioni] [contenitore]**

Questo comando imposta elenco di controllo di accesso per il contenitore di spazio di archiviazione. Account di archiviazione specificato dalla stringa di connessione o la chiave account e nome account lo spazio di archiviazione.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione in modalità di debug.

###<a name="commands-to-manage-your-storage-blob"></a>Comandi per gestire il blob di spazio di archiviazione

**elenco di spazio di archiviazione blob [opzioni] [contenitore] [prefisso]**

Questo comando restituisce un elenco di BLOB lo spazio di archiviazione nel contenitore di archiviazione specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione in modalità di debug.

**archivio blob di spazio di archiviazione Mostra [opzioni] [contenitore] [blob]**

Questo comando consente di visualizzare i dettagli del blob archiviazione specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-p** o **-prefisso** &lt;prefisso >: il prefisso del nome di spazio di archiviazione contenitore.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione di debug.

**archivio blob di spazio di archiviazione eliminare [opzioni] [contenitore] [blob]**

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-b** o **-blob** &lt;blobName >: il nome del blob lo spazio di archiviazione da eliminare.
+ **-q** o **-interattiva**: rimuovere il blob di spazio di archiviazione specificato senza conferma.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione di debug.

**caricamento di spazio di archiviazione blob [opzioni] [file] [contenitore] [blob]**

Questo comando caricare il file specificato blob di spazio di archiviazione specified\.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-b** o **-blob** &lt;blobName >: il nome del blob lo spazio di archiviazione da caricare.
+ **t -** oppure **- blobtype** &lt;blobtype >: il tipo di spazio di archiviazione blob: pagina o il blocco.
+ **-p** o **-proprietà** &lt;proprietà >: le proprietà di spazio di archiviazione blob per file caricato. Proprietà sono chiave = valore coppia s e separati da semicolon(;). Le proprietà disponibili sono contentType, contentEncoding, contentLanguage e cacheControl.
+ **-m** o **- metadati** &lt;metadati >: I metadati di spazio di archiviazione blob per file caricato. Metadati sono chiave = coppie una d separata da punti e virgola (;).
+ **-concurrenttaskcount** &lt;concurrenttaskcount >: il numero massimo di richieste simultanee carica.
+ **-q** o **-interattiva**: sovrascrivere il blob di spazio di archiviazione specificato senza conferma.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione di debug.

**download di spazio di archiviazione blob [opzioni] [contenitore] [blob] [destinazione]**

Questo comando consente di scaricare il blob di archiviazione specificato.

Questo comando supporta le seguenti opzioni aggiuntive:

+ **-contenitore** &lt;contenitore >: il nome del contenitore di spazio di archiviazione per creare.
+ **-b** o **-blob** &lt;blobName >: il nome di spazio di archiviazione blob.
+ **-d** o **-destinazione** [destinazione]: percorso file o una directory di destinazione di download.
+ **-m** o **-checkmd5**: md5sum controllo per il file scaricato.
+ **-concurrenttaskcount** &lt;concurrenttaskcount > il numero massimo di richieste simultanee carica
+ **-q** o **-interattiva**: sovrascrivere il file di destinazione senza conferma.
+ **-** o **-nome account** &lt;nome account >: il nome dell'account di archiviazione.
+ **-k** o **-chiave account** &lt;accountKey >: la chiave account di archiviazione.
+ **-c** o **-stringa di connessione** &lt;connectionString >: la stringa di connessione di spazio di archiviazione.
+ **-debug**: esegue il comando di spazio di archiviazione di debug.

## <a name="commands-to-manage-sql-databases"></a>Comandi per la gestione di database SQL

Utilizzare questi comandi per gestire il database di SQL Azure

###<a name="commands-to-manage-sql-servers"></a>Comandi per la gestione di SQL Server.

Utilizzare questi comandi per gestire il server SQL

**SQL server consente di creare &lt;administratorLogin > &lt;administratorPassword > &lt;posizione >**

Creare un server di database

    ~$ azure sql server create test T3stte$t "West US"
    info:    Executing command sql server create
    + Creating SQL Server
    data:    Server Name i1qwc540ts
    info:    sql server create command OK

**presentazione di SQL server &lt;nome >**

Visualizzare dettagli sul server.

    ~$ azure sql server show xclfgcndfg
    info:    Executing command sql server show
    + Getting SQL server
    data:    SQL Server Name xclfgcndfg
    data:    SQL Server AdministratorLogin msopentechforums
    data:    SQL Server Location West US
    data:    SQL Server FullyQualifiedDomainName xclfgcndfg.database.windows.net
    info:    sql server show command OK

**elenco di SQL server**

È possibile ottenere l'elenco dei server.

    ~$ azure sql server list
    info:    Executing command sql server list
    + Getting SQL server
    data:    Name        Location
    data:    ----------  --------
    data:    xclfgcndfg  West US
    info:    sql server list command OK

**SQL server Elimina &lt;nome >**

Elimina un server

    ~$ azure sql server delete i1qwc540ts
    info:    Executing command sql server delete
    Delete server i1qwc540ts? [y/n] y
    + Removing SQL Server
    info:    sql server delete command OK

###<a name="commands-to-manage-sql-databases"></a>Comandi per la gestione di database SQL

Utilizzare questi comandi per gestire il database di SQL.

**db SQL creare [opzioni] &lt;nomeserver > &lt;databaseName > &lt;administratorPassword >**

Crea un'istanza di database

    ~$ azure sql db create fr8aelne00 newdb test
    info:    Executing command sql db create
    Administrator password: ********
    + Creating SQL Server Database
    info:    sql db create command OK

**db SQL Mostra [opzioni] &lt;nomeserver > &lt;databaseName > &lt;administratorPassword >**

Visualizzare i dettagli di database.

    C:\windows\system32>azure sql db show fr8aelne00 newdb test
    info:    Executing command sql db show
    Administrator password: ********
    + Getting SQL server databases
    data:    Database _ ContentRootElement=m:properties, id=https://fr8aelne00.datab
    ase.windows.net/v1/ManagementService.svc/Server2('fr8aelne00')/Databases(4), ter
    m=Microsoft.SqlServer.Management.Server.Domain.Database, scheme=http://schemas.m
    icrosoft.com/ado/2007/08/dataservices/scheme, link=[rel=edit, title=Database, hr
    ef=Databases(4), rel=http://schemas.microsoft.com/ado/2007/08/dataservices/relat
    ed/Server, type=application/atom+xml;type=entry, title=Server, href=Databases(4)
    /Server, rel=http://schemas.microsoft.com/ado/2007/08/dataservices/related/Servi
    ceObjective, type=application/atom+xml;type=entry, title=ServiceObjective, href=
    Databases(4)/ServiceObjective, rel=http://schemas.microsoft.com/ado/2007/08/data
    services/related/DatabaseMetrics, type=application/atom+xml;type=entry, title=Da
    tabaseMetrics, href=Databases(4)/DatabaseMetrics, rel=http://schemas.microsoft.c
    om/ado/2007/08/dataservices/related/DatabaseCopies, type=application/atom+xml;ty
    pe=feed, title=DatabaseCopies, href=Databases(4)/DatabaseCopies], title=, update
    d=2013-11-18T19:48:27Z, name=
    data:    Database Id 4
    data:    Database Name newdb
    data:    Database ServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database AssignedServiceObjectiveId 910b4fcb-8a29-4c3e-958f-f7ba794388b2
    data:    Database ServiceObjectiveAssignmentState 1
    data:    Database ServiceObjectiveAssignmentStateDescription Complete
    data:    Database ServiceObjectiveAssignmentErrorCode
    data:    Database ServiceObjectiveAssignmentErrorDescription
    data:    Database ServiceObjectiveAssignmentSuccessDate
    data:    Database Edition Web
    data:    Database MaxSizeGB 1
    data:    Database MaxSizeBytes 1073741824
    data:    Database CollationName SQL_Latin1_General_CP1_CI_AS
    data:    Database CreationDate
    data:    Database RecoveryPeriodStartDate
    data:    Database IsSystemObject
    data:    Database Status 1
    data:    Database IsFederationRoot
    data:    Database SizeMB -1
    data:    Database IsRecursiveTriggersOn
    data:    Database IsReadOnly
    data:    Database IsFederationMember
    data:    Database IsQueryStoreOn
    data:    Database IsQueryStoreReadOnly
    data:    Database QueryStoreMaxSizeMB
    data:    Database QueryStoreFlushPeriodSeconds
    data:    Database QueryStoreIntervalLengthMinutes
    data:    Database QueryStoreClearAll
    data:    Database QueryStoreStaleQueryThresholdDays
    info:    sql db show command OK

**elenco dei database di SQL [opzioni] &lt;nomeserver > &lt;administratorPassword >**

Elencare i database.

    ~$ azure sql db list fr8aelne00 test
    info:    Executing command sql db list
    Administrator password: ********
    + Getting SQL server databases
    data:    Name    Edition  Collation                     MaxSizeInGB
    data:    ------  -------  ----------------------------  -----------
    data:    master  Web      SQL_Latin1_General_CP1_CI_AS  5
    info:    sql db list command OK

**db SQL eliminare [opzioni] &lt;nomeserver > &lt;databaseName > &lt;administratorPassword >**

Elimina un database.

    ~$ azure sql db delete fr8aelne00 newdb test
    info:    Executing command sql db delete
    Administrator password: ********
    Delete database newdb? [y/n] y
    + Getting SQL server databases
    + Removing database
    info:    sql db delete command OK

###<a name="commands-to-manage-your-sql-server-firewall-rules"></a>Comandi per gestire le regole firewall di SQL Server

Utilizzare questi comandi per gestire le regole firewall di SQL Server

**SQL firewallrule creare [opzioni] &lt;nomeserver > &lt;NomeRegola > &lt;startIPAddress > &lt;endIPAddress >**

Creare una regola del firewall per SQL Server.

    ~$ azure sql firewallrule create fr8aelne00 allowed 131.107.0.0 131.107.255.255
    info:    Executing command sql firewallrule create
    + Creating Firewall Rule
    info:    sql firewallrule create command OK

**SQL firewallrule Mostra [opzioni] &lt;nomeserver > &lt;NomeRegola >**

Mostra dettagli regola firewall.

    ~$ azure sql firewallrule show fr8aelne00 allowed
    info:    Executing command sql firewallrule show
    + Getting firewall rule
    data:    Firewall rule Name allowed
    data:    Firewall rule Type Microsoft.SqlAzure.FirewallRule
    data:    Firewall rule State Normal
    data:    Firewall rule SelfLink https://management.core.windows.net/9e672699-105
    5-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00/firewallrules/allowed
    data:    Firewall rule ParentLink https://management.core.windows.net/9e672699-1
    055-41ae-9c36-e85152f2e352/services/sqlservers/servers/fr8aelne00
    data:    Firewall rule StartIPAddress 131.107.0.0
    data:    Firewall rule EndIPAddress 131.107.255.255
    info:    sql firewallrule show command OK

**elenco di firewallrule SQL [opzioni] &lt;nomeserver >**

Elencare le regole del firewall.

    ~$ azure sql firewallrule list fr8aelne00
    info:    Executing command sql firewallrule list
    \data:    Name     Start IP address  End IP address
    data:    -------  ----------------  ---------------
    data:    allowed  131.107.0.0       131.107.255.255
    +
    info:    sql firewallrule list command OK

**SQL firewallrule eliminare [opzioni] &lt;nomeserver > &lt;NomeRegola >**

Questo comando Elimina una regola del firewall.

    ~$ azure sql firewallrule delete fr8aelne00 allowed
    info:    Executing command sql firewallrule delete
    Delete rule allowed? [y/n] y
    + Removing firewall rule
    info:    sql firewallrule delete command OK

## <a name="commands-to-manage-your-virtual-networks"></a>Comandi per gestire le reti virtuali

Utilizzare questi comandi per gestire le reti virtuali

**rete vnet creare [opzioni] &lt;posizione >**

Creare una rete virtuale.

    ~$ azure network vnet create vnet1 --location "West US" -v
    info:    Executing command network vnet create
    info:    Using default address space start IP: 10.0.0.0
    info:    Using default address space cidr: 8
    info:    Using default subnet start IP: 10.0.0.0
    info:    Using default subnet cidr: 11
    verbose: Address Space [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/8 (16777216)
    verbose: Subnet [Starting IP/CIDR (Max VM Count)]: 10.0.0.0/11 (2097152)
    verbose: Fetching Network Configuration
    verbose: Fetching or creating affinity group
    verbose: Fetching Affinity Groups
    verbose: Fetching Locations
    verbose: Creating new affinity group AG1
    info:    Using affinity group AG1
    verbose: Updating Network Configuration
    info:    network vnet create command OK

**Mostra vnet di rete &lt;nome >**

Visualizzare i dettagli di una rete virtuale.

    ~$ azure network vnet show vnet1
    info:    Executing command network vnet show
    + Fetching Virtual Networks
    data:    Name "vnet1"
    data:    Id "25786fbe-08e8-4e7e-b1de-b98b7e586c7a"
    data:    AffinityGroup "AG1"
    data:    State "Created"
    data:    AddressSpace AddressPrefixes 0 "10.0.0.0/8"
    data:    Subnets 0 Name "subnet-1"
    data:    Subnets 0 AddressPrefix "10.0.0.0/11"
    info:    network vnet show command OK

**elenco vnet social network**

Elenco di tutte le reti virtuale esistente.

    ~$ azure network vnet list
    info:    Executing command network vnet list
    + Fetching Virtual Networks
    data:    Name        Status   AffinityGroup
    data:    ----------  -------  -------------
    data:    vnet1      Created  AG1
    data:    vnet2      Created  AG1
    data:    vnet3      Created  AG1
    data:    vnet4      Created  AG1
    info:    network vnet list command OK


**eliminazione di rete vnet &lt;nome >**

Elimina la rete virtuale specificata.

    ~$ azure network vnet delete opentechvn1
    info:    Executing command network vnet delete
    + Fetching Network Configuration
    Delete the virtual network opentechvn1 ?  (y/n) y
    + Deleting the virtual network opentechvn1
    info:    network vnet delete command OK

**esportazione di rete [percorso file]**

Per la configurazione di rete avanzate, è possibile esportare la configurazione della rete locale. La configurazione di rete esportato include le impostazioni del server DNS, le impostazioni di rete virtuale, Impostazioni sito rete locale e altre impostazioni.

**importazione di rete [percorso file]**

Importare una configurazione di rete locale.

**rete ServerDNS registrare [opzioni] &lt;dnsIP >**

Registrare un server DNS che si prevede di utilizzare per la risoluzione dei nomi nella configurazione di rete.

    ~$ azure network dnsserver register 98.138.253.109 --dns-id FrontEndDnsServer
    info:    Executing command network dnsserver register
    + Fetching Network Configuration
    + Updating Network Configuration
    info:    network dnsserver register command OK

**elenco ServerDNS social network**

Elencare tutti i server DNS registrati nella configurazione di rete.

    ~$ azure network dnsserver list
    info:    Executing command network dnsserver list
    + Fetching Network Configuration
    data:    DNS Server ID         DNS Server IP
    data:    --------------------  --------------
    data:    DNS-bb39b4ac34d66a86  44.55.22.11
    data:    FrontEndDnsServer     98.138.253.109
    info:    network dnsserver list command OK

**annullare la registrazione di rete ServerDNS [opzioni] &lt;dnsIP >**

Rimuove una voce del server DNS dalla configurazione di rete.

    ~$ azure network dnsserver unregister 77.88.99.11
    info:    Executing command network dnsserver unregister
    + Fetching Network Configuration
    Delete the DNS server entry dns-4 ( 77.88.99.11 ) %s ? (y/n) y
    + Deleting the DNS server entry dns-4 ( 77.88.99.11 )
    info:    network dnsserver unregister command OK

