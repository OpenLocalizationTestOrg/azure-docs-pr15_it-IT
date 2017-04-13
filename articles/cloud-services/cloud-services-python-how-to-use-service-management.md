<properties
    pageTitle="Come usare l'API (Python) - Guida alle funzionalità di gestione di servizio"
    description="Informazioni su come eseguire a livello di programmazione comuni di gestione dei servizi da Python."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Come usare Gestione dei servizi da Python

> [AZURE.NOTE] API del servizio di gestione è sostituita con la nuova risorsa gestione API, attualmente disponibile in una versione di anteprima.  Vedere la [documentazione per la gestione delle risorse Azure](http://azure-sdk-for-python.readthedocs.org/) per informazioni dettagliate sull'utilizzo la nuova API di gestione delle risorse da Python.

Questa guida viene illustrato come eseguire a livello di programmazione comuni di gestione dei servizi da Python. La classe **ServiceManagementService** in [Azure SDK per Python](https://github.com/Azure/azure-sdk-for-python) supporta l'accesso a molte delle funzionalità di relative alla gestione dei servizi che è disponibile nel [portale classica Azure] [ management-portal] (ad esempio **creazione, aggiornamento ed eliminazione servizi cloud, distribuzioni, servizi di gestione dati e macchine virtuali di**). Questa funzionalità può essere utile per la creazione di applicazioni che richiedono l'accesso a gestione dei servizi.

## <a name="WhatIs"> </a>Che cos'è la gestione dei servizi
API del servizio di gestione fornisce l'accesso a molte delle funzionalità di gestione dei servizi disponibili tramite il [portale classica Azure][management-portal]. Azure SDK per Python consente di gestire i servizi cloud e gli account di archiviazione.

Per usare l'API di gestione del servizio, è necessario [creare un account Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Concetti
Azure SDK per Python vada a capo l' [API di gestione del servizio di Azure][svc-mgmt-rest-api], ovvero una API REST. Tutte le operazioni di API vengono eseguite tramite SSL e si escludono a autenticano tramite x. 509 v3. Il servizio di gestione può essere utilizzato da all'interno di un servizio in esecuzione in Azure o direttamente da Internet da qualsiasi applicazione che può inviare una richiesta HTTPS e ricevere una risposta HTTPS.

## <a name="Installation"> </a>Installazione

Tutte le funzionalità descritte in questo articolo sono disponibili nel `azure-servicemanagement-legacy` pacchetto, è possibile installare utilizzando pip. Per maggiori dettagli sull'installazione (ad esempio, se si ha familiarità con Python), consultare questo articolo: [l'installazione di Python e Azure SDK](../python-how-to-install.md)

## <a name="Connect"> </a>Procedura: connettersi alla gestione del servizio
Per connettere l'endpoint di gestione dei servizi, è necessario l'ID di abbonamento Azure e un certificato di gestione valido. È possibile ottenere l'ID di abbonamento tramite il [portale classica Azure][management-portal].

> [AZURE.NOTE] Poiché Azure SDK per Python v0.8.0, è ora possibile utilizzare i certificati creati con OpenSSL durante l'esecuzione in Windows.  Richiede l'esecuzione di Python 2.7.4 o versione successiva. È consigliabile agli utenti di utilizzare OpenSSL anziché pfx, poiché il supporto per PFX certificati probabile che vengono rimosse in futuro.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certificati di gestione su Windows o Mac o Linux (OpenSSL)
È possibile utilizzare [OpenSSL](http://www.openssl.org/) per creare il certificato di gestione.  È davvero necessario creare due certificati, uno per il server (un `.cer` file) e uno per il client (un `.pem` file). Per creare il `.pem` file, eseguire:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Per creare il `.cer` certificato, eseguire:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Per ulteriori informazioni sui certificati Azure, vedere [Panoramica dei certificati per servizi Cloud Windows Azure](./cloud-services-certs-create.md). Per una descrizione completa dei parametri OpenSSL, vedere la documentazione nella [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Dopo aver creato questi file, è necessario caricare il `.cer` il file da Azure tramite l'azione "Carica" della scheda "Impostazioni" del [portale classica Azure][management-portal], ed è necessario prendere nota di cui è stato salvato il `.pem` file.

Dopo aver ottenuto l'ID di abbonamento, creato un certificato e caricati la `.cer` file a Azure, è possibile connettersi all'endpoint gestione Azure passando l'id di abbonamento e il percorso di `.pem` il file da **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio precedente, `sms` è un oggetto **ServiceManagementService** . La classe **ServiceManagementService** è la classe primaria utilizzata per gestire i servizi Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificati di gestione in Windows (MakeCert)

È possibile creare un certificato autofirmato gestione sul computer tramite `makecert.exe`.  Aprire un **prompt dei comandi di Visual Studio** come **amministratore** e usare il comando seguente, sostituendo *AzureCertificate* con il nome del certificato che si desidera utilizzare.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Il comando crea la `.cer` file e viene installata nell'archivio certificati **personali** . Per ulteriori informazioni, vedere [Panoramica dei certificati per servizi Cloud Windows Azure](./cloud-services-certs-create.md).

Dopo aver creato il certificato, è necessario caricare il `.cer` il file da Azure tramite l'azione "Carica" della scheda "Impostazioni" del [portale classica Azure][management-portal].

Dopo aver ottenuto l'ID di abbonamento, creato un certificato e caricare il `.cer` file a Azure, è possibile connettersi all'endpoint gestione Azure passando l'id di abbonamento e il percorso del certificato nell'archivio certificati **personali** **ServiceManagementService** (nuovo sostituisce *AzureCertificate* con il nome del certificato):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

Nell'esempio precedente, `sms` è un oggetto **ServiceManagementService** . La classe **ServiceManagementService** è la classe primaria utilizzata per gestire i servizi Azure.

## <a name="ListAvailableLocations"> </a>Come: elenco posizioni disponibili

Per elencare le posizioni disponibili per l'hosting di servizi, utilizzare la **elenco\_percorsi** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando si crea un servizio cloud o il servizio di archiviazione è necessario specificare un percorso valido. Il **elenco\_percorsi** metodo restituisce sempre un elenco aggiornato delle posizioni disponibili. In questo modo, posizioni disponibili sono:

- Europa occidentale
- Europa Nord America
- Area Asia sudorientale
- Asia orientale
- Centrale USA
- America del Nord centrale USA
- Sud centrale USA
- Usa ovest
- Stati Uniti orientali
- Giappone est
- Giappone ovest
- Brasile sud
- Australia orientale
- Australia sudorientale

## <a name="CreateCloudService"> </a>Procedura: creare un servizio cloud

Quando si crea un'applicazione ed eseguire in Azure, il codice e configurazione insieme costituiscono un Azure [servizio cloud][] (noto come un *servizio ospitato* nelle versioni precedenti di Azure). Il **creare\_ospitato\_servizio** metodo consente di creare un nuovo servizio di hosting fornendo un nome di servizio di hosting (che deve essere univoco in Azure), un'etichetta (codificati automaticamente in base 64), una descrizione e un percorso.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

È possibile elencare tutti i servizi ospitati per l'abbonamento con il **elenco\_ospitato\_servizi** metodo:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se si desidera ottenere informazioni relative a un servizio di hosting specifico, è possibile eseguire questa operazione passando il nome del servizio di hosting per il **ottenere\_ospitato\_servizio\_proprietà** metodo:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Dopo aver creato un servizio cloud, è possibile distribuire il codice per il servizio con la **creare\_distribuzione** metodo.

## <a name="DeleteCloudService"> </a>Come: eliminare un servizio cloud

È possibile eliminare un servizio cloud passando il nome del servizio per la **eliminare\_ospitato\_servizio** metodo:

    sms.delete_hosted_service('myhostedservice')

Prima di poter eliminare un servizio, è necessario prima di tutto eliminare tutte le distribuzioni per il servizio. (Vedere [come: eliminare una distribuzione](#DeleteDeployment) per informazioni dettagliate.)

## <a name="DeleteDeployment"> </a>Come: eliminare una distribuzione

Per eliminare una distribuzione, utilizzare la **eliminare\_distribuzione** metodo. Nell'esempio seguente viene illustrato come eliminare una distribuzione denominata `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Procedura: creare un servizio di archiviazione

Un [servizio di archiviazione](../storage/storage-create-storage-account.md) consente di accedere a Azure [BLOB](../storage/storage-python-how-to-use-blob-storage.md), [tabelle](../storage/storage-python-how-to-use-table-storage.md)e [code](../storage/storage-python-how-to-use-queue-storage.md). Per creare un servizio di archiviazione, è necessario un nome per il servizio (da 3 a 24 caratteri minuscoli e univoco all'interno di Azure), una descrizione, un'etichetta (fino a 100 caratteri codificati automaticamente in base 64) e un percorso. Nell'esempio seguente viene illustrato come creare un servizio di archiviazione specificando un percorso.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Nota Nell'esempio precedente che lo stato del **creare\_lo spazio di archiviazione\_account** operazione può essere recuperato passando il risultato restituito da **creare\_lo spazio di archiviazione\_account** per il **ottenere\_operazione\_stato** metodo.  

È possibile elencare gli account di archiviazione e le relative proprietà con la **elenco\_lo spazio di archiviazione\_account** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Come: eliminare un servizio di archiviazione

È possibile eliminare un servizio di archiviazione passando il nome del servizio di archiviazione per la **eliminare\_lo spazio di archiviazione\_account** metodo. Se si elimina un servizio di archiviazione, tutti i dati archiviati nel servizio (BLOB, tabelle e code).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Come: elenco sistemi operativi disponibili

Per elencare i sistemi operativi che sono disponibili per l'hosting di servizi, utilizzare la **elenco\_operativo\_sistemi** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

In alternativa, è possibile utilizzare il **elenco\_operativo\_sistema\_famiglie** metodo che raggruppa i sistemi operativi per famiglia:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Procedura: creare un'immagine del sistema operativo

Per aggiungere un'immagine del sistema operativo per l'archivio di immagini, utilizzare la **aggiungere\_os\_immagine** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Per elencare le immagini del sistema operativo disponibili, utilizzare la **elenco\_os\_immagini** metodo. Include tutte le immagini di piattaforma e immagini utente:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Come: eliminare un'immagine del sistema operativo

Per eliminare un'immagine di utente, utilizzare la **eliminare\_os\_immagine** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Procedura: creare una macchina virtuale

Per creare una macchina virtuale, è innanzitutto necessario creare un [servizio cloud](#CreateCloudService).  Creare la distribuzione di macchina virtuale utilizzando il **creare\_virtuali\_computer\_distribuzione** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Come: Elimina una macchina virtuale

Per eliminare una macchina virtuale, è prima di tutto eliminare distribuzione utilizzando il **eliminare\_distribuzione** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Il servizio cloud potrà essere eliminato utilizzando il **eliminare\_ospitato\_servizio** metodo:

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Procedura: Creare una macchina virtuale da un'immagine acquisita macchina virtuale

Per acquisire un'immagine di macchine Virtuali, è innanzitutto necessario chiamare il **acquisire\_macchine virtuali\_immagine** metodo:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

A questo punto, per assicurarsi che siano state acquisite correttamente l'immagine, utilizzare la **elenco\_macchine virtuali\_immagini** api e verificare che l'immagine viene visualizzata nei risultati:

    images = sms.list_vm_images()

Per creare infine la macchina virtuale utilizzando l'immagine acquisita, utilizzare la **creare\_virtuali\_computer\_distribuzione** metodo di prima, ma questa volta passato il vm_image_name invece

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Per ulteriori informazioni su come acquisire una macchina virtuale Linux, vedere [come acquisire una macchina virtuale Linux.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Per ulteriori informazioni su come acquisire una macchina virtuale di Windows, vedere [come acquisire una macchina virtuale di Windows.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Passaggi successivi

Dopo aver appreso le nozioni di base della gestione dei servizi, è possibile accedere alla [documentazione di riferimento API completa per Azure Python SDK](http://azure-sdk-for-python.readthedocs.org/) ed eseguire attività complesse facilmente per gestire l'applicazione di python.

Per ulteriori informazioni, vedere il [Centro per sviluppatori di Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[servizio cloud]:/services/cloud-services/

