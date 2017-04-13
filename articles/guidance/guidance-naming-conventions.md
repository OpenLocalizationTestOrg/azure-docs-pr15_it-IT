<properties
   pageTitle="Consigliati convenzioni di denominazione per le risorse Azure | Indicazioni | Microsoft Azure"
   description="Convenzioni di denominazione consigliate per le risorse Azure. Convenzioni di denominazione macchine virtuali, gli account di archiviazione, reti, reti virtuali, subnet e altre entità Azure"
   services=""
   documentationCenter="na"
   authors="bennage"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/27/2016"
   ms.author="christb"/>
   
# <a name="recommended-naming-conventions-for-azure-resources"></a>Convenzioni di denominazione consigliate per le risorse Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

La scelta di un nome per una risorsa in Microsoft Azure è importante perché:

- È difficile modificare un nome in un secondo momento.
- Nomi devono soddisfare i requisiti del proprio tipo di risorsa specifica.

Convenzioni di denominazione coerenti rendono più semplice individuare le risorse. Indicano il ruolo di una risorsa in una soluzione.

In questo articolo è un riepilogo delle regole di denominazione e restrizioni per le risorse Azure e un set di base di suggerimenti per le convenzioni di denominazione.  È possibile utilizzare questi suggerimenti come punto di partenza per le convenzioni specifiche per le proprie esigenze.  

Il segreto del successo con le convenzioni di denominazione è la definizione e il loro tra le applicazioni e organizzazioni. 

## <a name="naming-subscriptions"></a>Sottoscrizioni di denominazione

Assegnare un nome abbonamenti Azure, nomi dettagliati semplificano la comprensione il contesto e lo scopo di ciascuna sottoscrizione Cancella.  Quando si lavora in un ambiente con molti abbonamenti, è possibile migliorare chiarezza seguendo una convenzione di denominazione condivisa.

Un modello consigliato per gli abbonamenti denominazione è:

`<Company> <Department (optional)> <Product Line (optional)> <Environment>`

- Società in genere saranno gli stessi per ogni sottoscrizione. Alcune aziende possono avere tuttavia, società figlio all'interno della struttura dell'organizzazione. Queste società possono essere gestite da un gruppo IT centrale. In questi casi, potrebbe essere distinguerli facendo in modo che il nome della società di padre (*Contoso*) e il nome di società figlio (*Nord vento*).

- Reparto è un nome all'interno dell'organizzazione in cui usare un gruppo di utenti. Questo elemento nello spazio dei nomi come facoltativi.

- Linea di prodotti è un nome di un prodotto o una funzione che viene eseguita all'interno del reparto specifico.
Si tratta in genere facoltativa per i servizi e applicazioni interna. Tuttavia, si consiglia di utilizzare per i servizi di pubblico che richiedono la separazione semplice e identificazione (ad esempio nettamente di fatturazione record).

- Ambiente è il nome che descriva il ciclo di vita di distribuzione delle applicazioni o servizi, ad esempio Dev, domande e risposte o produzione.

| Società | Reparto | Linea di prodotti o servizi | Ambiente | Nome completo  |
----------| ---------- | ----------------------- | ----------- | ---------- |
| Contoso | SocialGaming | AwesomeService | Produzione | Contoso SocialGaming AwesomeService produzione |
| Contoso | SocialGaming | AwesomeService | DEV | Contoso SocialGaming AwesomeService Dev |
| Contoso | È | InternalApps | Produzione | Contoso IT InternalApps produzione |
| Contoso | È | InternalApps | DEV | Contoso IT InternalApps Dev |

<!-- TODO; include more information about organizing subscriptions for application deployment, pods, etc. -->

## <a name="use-affixes-to-avoid-ambiguity"></a>Utilizzare affissi per evitare ambiguità

Nomi delle risorse in Azure, è consigliabile utilizzare comuni prefisso o suffisso per identificare il tipo e il contesto della risorsa.  Durante tutte le informazioni sul tipo di metadati, contesto, è disponibile a livello di programmazione, l'applicazione affissi comuni semplifica l'identificazione visiva.  Quando si inseriscono affissi nella convenzione di denominazione, è importante chiaramente specificare se affisso all'inizio del nome (prefisso) o all'estremità (suffisso).  

Ecco, ad esempio, due nomi possibili per un servizio di hosting motore di calcolo:

- SvcCalculationEngine (prefisso)
- CalculationEngineSvc (suffisso)

Affissi possono fare riferimento ai diversi aspetti che descrivono le risorse specifiche. Nella tabella seguente sono illustrati alcuni esempi in genere utilizzati.

| Aspetto | Esempio | Note |
| ------ | ------- | ----- |
| Ambiente | DEV, produzione, domande e risposte | Identifica l'ambiente per la risorsa |
| Posizione | UW (Contattaci ovest), ue (EST Contattaci) | Identifica l'area geografica in cui la risorsa viene distribuita |
| Istanza | 01, 02 | Per le risorse che dispone di più istanze denominata (server web e così via). |
| Il prodotto o servizio | servizio | Identifica il prodotto, applicazione o servizio che supporta la risorsa |
| Ruolo | SQL, web, messaggistica | Identifica il ruolo della risorsa associata |

Quando si sviluppano una convenzione di denominazione specifica per la propria società o progetti, è importante per scegliere un insieme comune di affissi e la loro posizione (suffisso o prefisso).

## <a name="naming-rules-and-restrictions"></a>Regole di denominazione e limitazioni

Ogni tipo di risorsa o il servizio in Azure impone una serie di denominazione restrizioni e ambito. qualsiasi convenzione di denominazione o del motivo deve rispettare le regole di denominazione necessarie e l'ambito.  Ad esempio, mentre il nome di una macchina virtuale mappato a un nome DNS (e pertanto deve essere univoco in tutti Azure), il nome di un VNET ambito è il gruppo di risorse in cui viene creato all'interno.

In generale, evitare di caratteri speciali (`-` o `_`) come il primo o l'ultimo carattere qualsiasi nome. Questi caratteri impedirà la maggior parte delle regole di convalida avere esito negativo.

| Categoria | Servizio o entità | Ambito | Lunghezza | Maiuscole e minuscole | Caratteri validi | Modello suggerito | Esempio |
| ------------- | ----------------- | ----- | ------ | ------ | ---------------- | ----------------- | ------- |
| Gruppo di risorse | Gruppo di risorse | Globale | 1-64 | Maiuscole e minuscole | Alfanumerico, sottolineatura e segno meno | `<service short name>-<environment>-rg` | `profx-prod-rg` |
| Gruppo di risorse | Set di disponibilità | Gruppo di risorse | 1-80 | Maiuscole e minuscole | Alfanumerico, sottolineatura e segno meno | `<service-short-name>-<context>-as` | `profx-sql-as` |
| Generale | Tag | Entità associata | 512 (nome), 256 (valore) | Maiuscole e minuscole | Alfanumerico | `"key" : "value"` | `"department" : "Central IT"` |
| Calcolare | Macchina virtuale | Gruppo di risorse | 1-15 | Maiuscole e minuscole | Alfanumerico, sottolineatura e segno meno | `<name>-<role>-<instance>` | `profx-sql-001` |
| Spazio di archiviazione | Nome dell'account di archiviazione (dati) | Globale | 3-24 | Lettere minuscole | Alfanumerico | `<service short name><type><number>` | `profxdata001` |
| Spazio di archiviazione | Nome dell'account di archiviazione (dischi) | Globale | 3-24 | Lettere minuscole | Alfanumerico | `<vm name without dashes>st<number>` | `profxsql001st0` |
| Spazio di archiviazione | Nome del contenitore | Account di archiviazione | 3-63 |   Lettere minuscole | Alfanumerico e trattino | `<context>` | `logs` |
| Spazio di archiviazione | Nome BLOB | Contenitore | 1-1024 | Maiuscole / minuscole | Qualsiasi carattere URL | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Spazio di archiviazione | Nome coda | Account di archiviazione | 3-63 | Lettere minuscole | Alfanumerico e trattino | `<service short name>-<context>-<num>` | `awesomeservice-messages-001` |
| Spazio di archiviazione | Nome della tabella | Account di archiviazione | 3-63 |Maiuscole e minuscole | Alfanumerico | `<service short name>-<context>` | `awesomeservice-logs` |
| Spazio di archiviazione | Nome del file | Account di archiviazione | 3-63 | Lettere minuscole | Alfanumerico | `<variable based on blob usage>` | `<variable based on blob usage>` |
| Rete | Rete virtuale (VNet) | Gruppo di risorse | 2-64 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<service short name>-[section]-vnet` | `profx-vnet` |
| Rete | Subnet | VNet padre | 2-80 | Distinzione tra maiuscole e | Alfanumerico, sottolineatura, trattini e periodo | `<role>-subnet` | `gateway-subnet` |
| Rete | Interfaccia di rete | Gruppo di risorse | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<vmname>-<num>nic` | `profx-sql1-1nic` |
| Rete | Gruppo di sicurezza di rete | Gruppo di risorse | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<service short name>-<context>-nsg` | `profx-app-nsg` |
| Rete | Regola gruppo di sicurezza di rete | Gruppo di risorse | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<descriptive context>` | `sql-allow` |
| Rete | Indirizzo IP pubblico | Gruppo di risorse | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<vm or service name>-pip` | `profx-sql1-pip` |
| Rete | Bilanciamento del carico | Gruppo di risorse | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `<service or role>-lb` | `profx-lb` |
| Rete | Caricare Config regole non bilanciate | Bilanciamento del carico | 1-80 | Distinzione tra maiuscole e | Alfanumerico, trattini, sottolineatura e periodo | `descriptive context` | `http` |

<!-- TODO fill in the rest of these resources
| Networking | Azure Application Gateway | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `<service or role>-aag` | `profx-aag`
| Networking | Azure Application Gateway Connection | Azure Application Gateway | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | `` | TODO
| Networking | Traffic Manager Profile | Resource Group | 1-80 | Case-insensitive | Alphanumeric, dash, underscore, and period | TODO | TODO
-->

## <a name="organizing-resources-with-tags"></a>Organizzare le risorse con tag

Gestione risorse Azure supporta tagging entità con stringhe di testo non autorizzato per identificare contesto e semplificare l'automazione.  Ad esempio, il tag `"sqlVersion: "sql2014ee"` Impossibile identificare macchine virtuali in una distribuzione con SQL Server 2014 Enterprise Edition per l'esecuzione di uno script automatico in base a tali.  Tag da utilizzare per integrare e migliorare contesto lato convenzioni di denominazione scelto.

> [AZURE.TIP]Un altro vantaggio di tag è che tag estesi a gruppi di risorse, che consente di creare un collegamento e creare una relazione tra entità tra distribuzioni diverse.

Ogni risorsa o il gruppo può avere un massimo di **15** tag. Il nome di tag è limitato ai 512 caratteri e il valore di tag è limitato a 256 caratteri.

Per ulteriori informazioni sulla risorsa tagging, vedere [tramite tag per organizzare le risorse Azure](../resource-group-using-tags.md).

Alcuni dei casi di utilizzo comune tagging sono:

- **Fatturazione**; Raggruppamento delle risorse e associarli con la fatturazione o addebito indietro codici.
- **Identificazione del contesto di servizio**; Identificare i gruppi di risorse tra i gruppi di risorse per operazioni comuni e raggruppamento
- **Controllo dell'accesso e contesto di protezione**; Identificazione di ruoli amministrativi in base a portfolio, sistema, servizio, app, istanza e così via.

> [AZURE.TIP]Tag in anticipo - tag spesso.  Migliore per avere una previsione tagging combinazione in posizione e regolare sull'ora, anziché dover adeguare dopo aver fatto.  

Esempio di alcuni approcci tagging comuni:

| Nome tag | Chiave | Esempio | Commento |
| -------- | --- | ------- | ------- |
| Fattura per / interno ID rifiuto | billTo  | `IT-Chargeback-1234` | Un/o interno o codice di fatturazione |
| Operatore o sulla singola direttamente responsabile (UNI) | managedBy | `joe@contoso.com`  | Indirizzo di posta elettronica o l'alias |
| Nome del progetto | nome di progetto | `myproject`  | Nome della riga di progetto o un prodotto |
| Versione del progetto | versione del progetto | `3.4`  | Versione della riga di progetto o un prodotto |
| Ambiente | ambiente | `<Production, Staging, QA >` | Identificatore ambientale | 
| Livello | livello | `Front End, Back End, Data` | Identificazione di livello o ruolo/contesto |
| Profilo dei dati | dataProfile | `Public, Confidential, Restricted, Internal` | Riservatezza dei dati archiviati nella risorsa |
 
## <a name="tips-and-tricks"></a>Suggerimenti e consigli

Alcuni tipi di risorse possono richiedere ulteriore attenzione su denominazione e convenzioni.

### <a name="virtual-machines"></a>Macchine virtuali

In particolare in topologie più grande, con attenzione denominazione macchine virtuali semplificano che identifica il ruolo e lo scopo di ogni computer e attivare più prevedibili gli script.

> [AZURE.WARNING]Tutti i computer virtuali Azure ha un nome di risorsa Azure e un nome host del sistema operativo.  
> Se il nome di risorsa e il nome host sono diversi, la gestione di macchine virtuali potrebbe essere complessa ed evitare.
> Ad esempio, se una macchina virtuale viene creata da un VHD che già contiene un sistema operativo configurato con un nome host.

- [Convenzioni di denominazione per macchine virtuali di Windows Server](https://support.microsoft.com/en-us/kb/188997)

<!-- TODO - recommendations on naming VMs. -->

### <a name="storage-accounts-and-storage-entities"></a>Gli account di archiviazione ed entità di spazio di archiviazione

Esistono due casi di utilizzo principale per gli account di archiviazione - backup dischi per macchine virtuali e la memorizzazione dei dati in BLOB, code e tabelle.  Gli account di archiviazione utilizzati per dischi macchine Virtuali devono seguire la convenzione di denominazione di associarli a padre nome macchine Virtuali e con la potenziale necessità di più account di archiviazione per gli SKU macchine Virtuali high-end, applicare anche il suffisso del numero.

> [AZURE.TIP]Gli account di archiviazione - per dati o dischi - devono seguire una convenzione di denominazione che consente di più account di archiviazione permettere a (computer ovvero sempre con un suffisso numerico).

È possibile configurare un nome di dominio personalizzato per l'accesso ai dati blob nell'account di archiviazione Azure.
L'endpoint predefinito per il servizio Blob è `https://mystorage.blob.core.windows.net`.

Ma se si esegue il mapping di un dominio personalizzato (ad esempio www.contoso.com) all'endpoint blob per l'account di archiviazione, è anche possibile accedere blob dati nel proprio account di archiviazione con tale dominio. Ad esempio, con un nome di dominio personalizzato, `http://mystorage.blob.core.windows.net/mycontainer/myblob` l'accesso come `http://www.contoso.com/mycontainer/myblob`.

Per ulteriori informazioni sulla configurazione di questa caratteristica, fare riferimento a [Configura un nome di dominio personalizzato per l'endpoint di spazio di archiviazione Blob](../storage/storage-custom-domain-name.md).

Per ulteriori informazioni sulla denominazione BLOB, contenitori e tabelle:

- [Assegnare un nome e che fanno riferimento i contenitori, BLOB e i metadati](https://msdn.microsoft.com/library/dd135715.aspx)
- [Denominazione di code e dei metadati](https://msdn.microsoft.com/library/dd179349.aspx)
- [Denominazione di tabelle](https://msdn.microsoft.com/library/azure/dd179338.aspx)

Nome di un blob può contenere qualsiasi combinazione di caratteri, ma caratteri URL riservati devono essere codificati correttamente. Evitare nomi blob che terminano con un punto (.), una barra (/) o una sequenza o una combinazione dei due. Per convenzione, la barra è il separatore di directory **virtuale** . Non si utilizza una barra rovesciata (\) un nome blob. Il client API potrebbero consentita l'esecuzione, ma l'esito negativo a hashing correttamente e le firme non corrisponderanno.

Non è possibile modificare il nome di un account di archiviazione o il contenitore dopo averlo creato.
Se si desidera utilizzare un nuovo nome, è necessario eliminarla e crearne uno nuovo.

> [AZURE.TIP] È consigliabile stabilire una convenzione di denominazione per tutti gli account di archiviazione ed prima di iniziare sullo sviluppo di un nuovo servizio o applicazione.

## <a name="example---deploying-an-n-tier-service"></a>Esempio: distribuire un servizio a più livelli

In questo esempio si definisce una configurazione del servizio a più livelli, composta da server front-end IIS (ospitato in macchine virtuali di Windows Server), con SQL Server (ospitata in macchine virtuali di due Windows Server), un cluster di ElasticSearch (ospitato in macchine virtuali Linux 6) e gli account di archiviazione associato, le reti virtuali, delle risorse gruppo e bilanciamento del carico.

Iniziamo definendo le convenzioni di scelta rapida per l'applicazione:

| Entità | Convenzione | Descrizione  |
| ------ | ---------- | ------------ |  
| Nome del servizio | `profx` | Il nome breve dell'applicazione o servizio in fase di distribuzione |
| Ambiente | `prod` | Si tratta per la distribuzione di produzione (a differenza di domande e risposte, test, ecc.) |

Da tale previsione è possibile quindi eseguire il mapping fuori le convenzioni per tutti i tipi di risorse:

| Tipo di risorsa | Convenzione Base | Esempio |
| ------------- | --------------- | ------- |
| Abbonamento | `<Company> <Department (optional)> <Product Line (optional)> <Environment>` | `Contoso IT InternalApps Profx Production` |
| Gruppo di risorse | `servicename-rg` | `profx-rg` |
| Rete virtuale | `servicename-vnet` | `profx-vnet` |
| Subnet | `role-subnet` | `sql-vnet` |
| Bilanciamento del carico | `servicename-lb` | `profx-lb` |
| Macchina virtuale | `servicename-role[number]` | `profx-sql0` |
| Account di archiviazione | `<vmnamenodashes>st<num>` | `profxsql0st0` |

Come illustrato di seguito diagramma:

![diagramma della topologia applicazione] (media/guidance-naming-conventions/guidance-naming-convention-example.png "Topologia applicazione di esempio")

## <a name="sample---azure-cli-script-for-deploying-the-sample-above"></a>Esempio - script CLI Azure per la distribuzione nell'esempio precedente

```bash
#!/bin/sh

#####################################################################
# Sample script using the Azure CLI to build out an application 
# demonstrating naming conventions.  
#
# Note; this script is not intended for production deployment, as it does 
# not create availability sets, configure network security rules, etc.
#####################################################################

# Set up variables to build out the naming conventions for deploying
# the cluster  
LOCATION=eastus2
APP_NAME=profx
ENVIRONMENT=prod
USERNAME=testuser
PASSWORD="testpass"

# Set up the tags to associate with items in the application
TAG_BILLTO="InternalApp-ProFX-12345"
TAGS="billTo=${TAG_BILLTO}"

# Explicitly set the subscription to avoid confusion as to which subscription
# is active/default
SUBSCRIPTION=3e9c25fc-55b3-4837-9bba-02b6eb204331

# Set up the names of things using recommended conventions
RESOURCE_GROUP="${APP_NAME}-${ENVIRONMENT}-rg"
VNET_NAME="${APP_NAME}-vnet"

# Set up the postfix variables attached to most CLI commands
POSTFIX="--resource-group ${RESOURCE_GROUP} --location ${LOCATION} --subscription ${SUBSCRIPTION}"

##########################################################################################
# Set up the VM conventions for Linux and Windows images

# For Windows, get the list of URN's via 
# azure vm image list ${LOCATION} MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

# For Linux, get the list or URN's via 
# azure vm image list ${LOCATION} canonical ubuntuserver
LINUX_BASE_IMAGE=canonical:ubuntuserver:16.04.0-DAILY-LTS:16.04.201602130

#########################################################################################
## Define functions 
create_vm ()
{
    vm_name=$1
    vnet_name=$2
    subnet_name=$3
    os_type=$4
    vhd_path=$5
    vm_size=$6
    diagnostics_storage=$7

    # Create the network interface card for this VM
    azure network nic create --name "${vm_name}-0nic" --subnet-name ${subnet_name} --subnet-vnet-name ${vnet_name} \
        --tags="${TAGS}" ${POSTFIX}

    # Create the storage account for this vm's disks (premium locally redundant storage -> PLRS)
    # Note the ${var//-/} syntax to remove dashes from the vm name
    storage_account_name=${vm_name//-/}st01
    azure storage account create --type=PLRS --tags "${TAGS}" ${POSTFIX} "${storage_account_name}"

    # Map the name of the diagnostics storage account to a blob URI for boot diagnostics
    # This is (currently) required when deploying with a named premium storage account 
    diag_blob="https://${diagnostics_storage}.blob.core.windows.net/"

    # Create the VM
    azure vm create --name ${vm_name} --nic-name "${vm_name}-0nic" --os-type ${os_type} \
        --image-urn ${vhd_path} --vm-size ${vm_size} --vnet-name ${vnet_name} --vnet-subnet-name ${subnet_name} \
        --storage-account-name "${storage_account_name}" --storage-account-container-name vhds --os-disk-vhd "${vm_name}-osdisk.vhd" \
        --admin-username "${USERNAME}" --admin-password "${PASSWORD}" \
        --boot-diagnostics-storage-uri "${diag_blob}" \
        --tags="${TAGS}" ${POSTFIX} 
}

###################################################################################################
# Create resources

# Step 1 - create the enclosing resource group
azure group create --name "${RESOURCE_GROUP}" --location "${LOCATION}" --tags "${TAGS}" --subscription "${SUBSCRIPTION}"

# Step 2 - create the network security groups

# Step 3 - create the networks (VNet and subnets)
azure network vnet create --name "${VNET_NAME}" --address-prefixes="10.0.0.0/8" --tags "${TAGS}" ${POSTFIX}
# TODO - does subnet support tagging?
azure network vnet subnet create --name gateway-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.1.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-master-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.2.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name es-data-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.3.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}
azure network vnet subnet create --name sql-subnet --vnet-name "${VNET_NAME}" --address-prefix="10.0.4.0/24" --resource-group "${RESOURCE_GROUP}" --subscription ${SUBSCRIPTION}

# Step 4 - define the load balancer and network security rules
azure network lb create --name "${APP_NAME}-lb" ${POSTFIX}
# In a production deployment script, we'd create load balancer rules and 
# network security groups here

# Step 5 - create a diagnostics storage account
diagnostics_storage_account=${APP_NAME//-/}diag
azure storage account create --type=LRS --tags "${TAGS}" ${POSTFIX} "${diagnostics_storage_account}"

# Step 6.1 - Create the gateway VMs
for i in `seq 1 4`;
do
    create_vm "${APP_NAME}-gw${i}" "${APP_NAME}-vnet" "gateway-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}" 
done    

# Step 6.2 - Create the ElasticSearch master and data VMs
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-master${i}" "${APP_NAME}-vnet" "es-master-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done
for i in `seq 1 3`;
do
    create_vm "${APP_NAME}-es-data${i}" "${APP_NAME}-vnet" "es-data-subnet" "Linux" "${LINUX_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
done

# Step 6.3 - Create the SQL VMs
create_vm "${APP_NAME}-sql0" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
create_vm "${APP_NAME}-sql1" "${APP_NAME}-vnet" "sql-subnet" "Windows" "${WINDOWS_BASE_IMAGE}" "Standard_DS1" "${diagnostics_storage_account}"
```
