<properties
   pageTitle="Ridimensionare il cluster ACS con CLI Azure | Microsoft Azure"
   description="Come ridurre i cluster di Azure contenitore servizio utilizzando CLI Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/03/2016"
   ms.author="timlt"/>

# <a name="scale-an-azure-container-service"></a>Ridimensionare un servizio di Azure contenitore

È possibile ridimensionare il numero dei nodi utilizzando lo strumento di Azure CLI sono previsti i Azure contenitore Service (ACS). Quando si usa CLI Azure scalare, lo strumento restituisce un nuovo file di configurazione che rappresenta la variazione applicata al contenitore.

## <a name="about-the-command"></a>Sul comando

CLI Azure deve essere in modalità di gestione di risorse Azure per è possibile interagire con i contenitori di Azure. È possibile passare alla modalità di gestione risorse chiamando `azure config mode arm`. Il `acs` comando dispone di un comando figlio denominato `scale` che esegue tutte le operazioni di ridimensionamento di un servizio contenitore. È possibile ottenere assistenza sui vari parametri utilizzati nel comando Ridimensiona eseguendo `azure acs scale --help`, che restituisce sarà simile alla seguente:

```azurecli
azure acs scale --help

help:    The operation to scale a container service.
help:
help:    Usage: acs scale [options] <resource-group> <name> <new-agent-count>
help:
help:    Options:
help:      -h, --help                               output usage information
help:      -v, --verbose                            use verbose output
help:      -vv                                      more verbose with debug output
help:      --json                                   use json output
help:      -g, --resource-group <resource-group>    resource-group
help:      -n, --name <name>                        name
help:      -o, --new-agent-count <new-agent-count>  New agent count
help:      -s, --subscription <subscription>        The subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="use-the-command-to-scale"></a>Usare il comando dimensioni

Per ridimensionare un servizio contenitore, è innanzitutto necessario conoscere il **gruppo di risorse** e il **nome di Azure contenitore Service (ACS)**e anche specificare il nuovo numero di agenti. Se si utilizza una quantità inferiore o superiore, è possibile ridimensionare verso il basso o verso l'alto rispettivamente.

Può essere necessario sapere quali il conteggio corrente di agenti di un servizio contenitore prima ridimensionate. Utilizzare la `azure acs show <resource group> <ACS name>` comando per restituire la configurazione di ACS. Nota Il risultato di <mark>conteggio</mark> .

#### <a name="see-current-count"></a>Vedere conteggio corrente

```azurecli
azure acs show containers-test containerservice-containers-test

info:    Executing command acs show
data:
data:     Id                 : /subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test
data:     Name               : containerservice-containers-test
data:     Type               : Microsoft.ContainerService/ContainerServices
data:     Location           : westus
data:     ProvisioningState  : Succeeded
data:     OrchestratorProfile
data:       OrchestratorType : DCOS
data:     MasterProfile
data:       Count            : 1
data:       DnsPrefix        : myprefixmgmt
data:       Fqdn             : myprefixmgmt.westus.cloudapp.azure.com
data:     AgentPoolProfiles
data:       #0
data:         Name           : agentpools
data:         <mark>Count          : 1</mark>
data:         VmSize         : Standard_D2
data:         DnsPrefix      : myprefixagents
data:         Fqdn           : myprefixagents.westus.cloudapp.azure.com
data:     LinuxProfile
data:       AdminUsername    : azureuser
data:       Ssh
data:         PublicKeys
data:           #0
data:             KeyData    : ssh-rsa <ENCODED VALUE>
data:     DiagnosticsProfile
data:       VmDiagnostics
data:         Enabled        : true
data:         StorageUri     : https://<storageid>.blob.core.windows.net/
```  

#### <a name="scale-to-new-count"></a>Ridimensionare al numero di nuovo

Come è probabilmente già evidenti, è possibile ridimensionare il servizio contenitore chiamando `azure acs scale` e specificando il **gruppo di risorse**, **nome ACS**e **conteggio agente**. Durante il ridimensionamento di un servizio contenitore, CLI Azure restituisce una stringa JSON che rappresenta la nuova configurazione del servizio contenitore, incluso il conteggio dei agente nuovo.

```azurecli
azure acs scale containers-test containerservice-containers-test 10

info:    Executing command acs scale
data:    {
data:        id: '/subscriptions/<guid>/resourceGroups/containers-test/providers/Microsoft.ContainerService/containerServices/containerservice-containers-test',
data:        name: 'containerservice-containers-test',
data:        type: 'Microsoft.ContainerService/ContainerServices',
data:        location: 'westus',
data:        provisioningState: 'Succeeded',
data:        orchestratorProfile: { orchestratorType: 'DCOS' },
data:        masterProfile: {
data:            count: 1,
data:            dnsPrefix: 'myprefixmgmt',
data:            fqdn: 'myprefixmgmt.westus.cloudapp.azure.com'
data:        },
data:        agentPoolProfiles: [
data:            {
data:                name: 'agentpools',
data:                <mark>count: 10</mark>,
data:                vmSize: 'Standard_D2',
data:                dnsPrefix: 'myprefixagents',
data:                fqdn: 'myprefixagents.westus.cloudapp.azure.com'
data:            }
data:        ],
data:        linuxProfile: {
data:            adminUsername: 'azureuser',
data:            ssh: {
data:                publicKeys: [
data:                    { keyData: 'ssh-rsa <ENCODED VALUE>' }
data:                ]
data:            }
data:        },
data:        diagnosticsProfile: {
data:            vmDiagnostics: { enabled: true, storageUri: 'https://<storageid>.blob.core.windows.net/' }
data:        }
data:    }
info:    acs scale command OK
``` 

## <a name="next-steps"></a>Passaggi successivi

- [Distribuire un cluster](container-service-deployment.md)