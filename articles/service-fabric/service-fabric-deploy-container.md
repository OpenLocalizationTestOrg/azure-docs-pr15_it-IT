<properties
   pageTitle="Servizio tessuti e contenitori distribuzione | Microsoft Azure"
   description="Infrastruttura di servizio e l'utilizzo di contenitori per distribuire le applicazioni di microservice. In questo articolo descrive le funzionalità offerte da tessuti servizi per i contenitori e su come distribuire un'immagine di contenitore di Windows in un cluster"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Anteprima: Distribuire un contenitore di Windows per tessuti servizio

> [AZURE.SELECTOR]
- [Distribuire il contenitore di Windows](service-fabric-deploy-container.md)
- [Distribuire Docker contenitore](service-fabric-deploy-container-linux.md)

In questo articolo illustra la creazione di servizi nei contenitori nei contenitori di Windows. 

>[AZURE.NOTE] Questa caratteristica è in anteprima per Linux e non è attualmente disponibile per l'utilizzo con Windows Server 2016. Dopo che questo valore sarà disponibili in preview per Windows Server 2016 nella prossima versione del servizio tessuti e supportati nelle versioni successive.

Servizio tessuti sono diverse funzionalità contenitore utili per le applicazioni che sono costituiti di microservices che sono in contenitore. Si tratta di servizi nei contenitori. 

Includono le funzionalità;

- Attivazione e la distribuzione di immagini contenitore
- Governance delle risorse
- Autenticazione archivio
- Contenitore mapping porta all'host porta
- Comunicazioni e individuazione contenitore a
- Consente di configurare e impostare le variabili di ambiente

Consente di esaminare ognuna delle funzionalità, a sua volta quando un servizio nei contenitori da includere nell'applicazione di imballaggio.

## <a name="packaging-a-windows-container"></a>Imballaggio un contenitore di Windows

Quando un contenitore di imballaggio, è possibile scegliere l'opzione per usare un modello di progetto di Visual Studio o [creare manualmente il pacchetto dell'applicazione](#manually). Utilizzando Visual Studio, la struttura del pacchetto di applicazioni e file manifesto vengono creati per la creazione guidata nuovo progetto dell'utente (disponibile nella versione successiva).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>Utilizzo di Visual Studio per comprimere un'immagine contenitore esistente

>[AZURE.NOTE] Nelle versioni future di Visual Studio utensili SDK, sarà possibile aggiungere un contenitore a un'applicazione in modo analogo è possibile aggiungere guest eseguibile oggi. Vedere l'argomento relativo a [Distribuisci guest eseguibile servizio tessuti](service-fabric-deploy-existing-app.md) . Attualmente è necessario eseguire imballaggio manuale come descritto di seguito.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Creazione manuale del pacchetto e distribuzione di un contenitore
Il processo di creazione manuale del pacchetto di un servizio nei contenitori si basa sulla procedura seguente:

1. Pubblicare i contenitori per l'archivio.
2. Creare la struttura di directory pacchetto.
3. Modificare il file manifesto servizio.
4. Modificare il file manifesto dell'applicazione.

## <a name="container-image-deployment-and-activation"></a>Attivazione e la distribuzione di immagini contenitore.
Nel servizio tessuti [modello di applicazione di](service-fabric-application-model.md)un contenitore rappresenta un'applicazione host in cui vengono inserite più repliche di servizio. Per distribuire e attivare un contenitore, inserire il nome dell'immagine contenitore in un `ContainerHost` elemento nel manifesto di servizio.

Nel manifesto servizio aggiungere una `ContainerHost` per la voce scegliere e impostare il `ImageName` il nome di archivio di contenitore e l'immagine. Manifesto parziale seguente viene illustrato un esempio della distribuzione il contenitore chiamato *myimage:v1* da un repository denominato *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

È possibile fornire inpui comandi per l'immagine contenitore specificando facoltativa `Commands` elemento con un punto e virgola delimitato insieme di comandi per l'esecuzione all'interno del contenitore. 

## <a name="resource-governance"></a>Governance delle risorse
Governance delle risorse è una funzionalità del contenitore e limita le risorse utilizzate il contenitore nell'host. Il `ResourceGovernancePolicy`, specificato nel manifesto dell'applicazione, offre la possibilità di dichiarare limiti delle risorse per un pacchetto di codice servizio. È possibile impostare limiti delle risorse per;

- Memoria
- MemorySwap
- CpuShares (spessore relativo CPU)
- MemoryReservationInMB  
- BlkioWeight (spessore relativo BlockIO). 

>[AZURE.NOTE] Nelle versioni future, supporto per specificare i limiti di IO blocco specifico, ad esempio IOPs, lettura/scrittura BPS e altri sarà possibili.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticazione archivio
Per scaricare un contenitore, potrebbe essere necessario fornire le credenziali di accesso all'archivio contenitore. Le credenziali di accesso specificate nel manifesto *dell'applicazione* vengono utilizzate per specificare le informazioni di accesso o una chiave SSH, per il download dell'immagine contenitore dal repository immagine.  Nell'esempio seguente mostra un account denominato *TestUser* insieme la password in testo non crittografato. Questa operazione **non** è consigliabile.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

La password possa e dovrebbe essere crittografata utilizzando un certificato distribuito al computer.

Nell'esempio seguente mostra un account denominato *TestUser* con la password crittografata con un certificato denominato *NomeCert*. È possibile utilizzare il `Invoke-ServiceFabricEncryptText` comando Powershell per creare il testo di crittografia segreta per la password. Vedere l'articolo [gestione delle informazioni riservate nelle applicazioni di servizio tessuti](service-fabric-application-secret-management.md) per informazioni dettagliate su come. La chiave privata del certificato di decrittografare la password deve essere distribuita sul computer locale in un metodo fuori banda (in Azure questa operazione consiste nell'utilizzare ARM). Quindi, quando servizio tessuti distribuisce il pacchetto di servizio al computer, è possibile decrittografare il segreto e insieme il nome dell'account, eseguire l'autenticazione con archivio contenitore usando le credenziali.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Contenitore mapping porta all'host porta
È possibile configurare una porta host utilizzata per comunicare con il contenitore specificando un `PortBinding` in manifesto dell'applicazione. Associazione porta cartine porta che il servizio è in ascolto all'interno del contenitore di una porta nell'host.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Comunicazioni e individuazione contenitore a
Utilizzo di `PortBinding` criteri è possibile mappare una porta contenitore per un `Endpoint` nel manifesto di servizio, come illustrato nell'esempio seguente. L'endpoint `Endpoint1` possibile specificare una porta fissa, ad esempio la porta 80 o non specificare alcuna porta, nel qual caso scelti una porta casuale dall'intervallo di porte i cluster applicazione, ad esempio.

Per i contenitori di guest, che specifica un `Endpoint` come nel manifesto servizio sarà tessuti servizio pubblicare automaticamente l'endpoint del servizio di denominazione in modo che altri servizi in esecuzione nel cluster di individuare il contenitore utilizzando le query di resto del servizio di risoluzione. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

La registrazione con il servizio di denominazione, è possibile eseguire facilmente contenitore a comunicazioni nel codice all'interno del contenitore mediante il [proxy inverso](service-fabric-reverseproxy.md). È sufficiente è specificare la porta di attesa http proxy inverso e un nome dei servizi che si desidera comunicare mediante l'impostazione come variabili. Come eseguire questa operazione, vedere la sezione successiva.  

## <a name="configure-and-set-environment-variables"></a>Configurare e impostare le variabili di ambiente
Le variabili di ambiente possono essere specificato un nemico ogni pacchetto di codice nel servizio manifesto per entrambi i servizi distribuiti in contenitori o come eseguibili processi/guest. Questi valori variabile di ambiente possono essere sovrascritti specificamente nel manifesto dell'applicazione o specificati durante la distribuzione come parametri dell'applicazione.

Frammento di codice XML manifesto del servizio seguente è illustrato un esempio su come specificare le variabili di ambiente per un pacchetto di codice. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Queste variabili di ambiente possono essere ignorate livello manifesto dell'applicazione:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

Nell'esempio precedente è stato specificato un valore esplicito per il `HttpGateway` variabile di ambiente (19000) mentre il valore di `BackendServiceName` parametro è impostato tramite il `[BackendSvc]` parametro relativo all'applicazione. In questo modo è possibile specificare il valore di `BackendServiceName`il valore in fase di distribuzione dell'applicazione e non hanno un valore fisso nel manifesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Esempi di applicazioni e servizi manifesto completi
Di seguito è un manifesto dell'applicazione di esempio che illustra le caratteristiche di contenitore.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


Di seguito è illustrato un esempio manifesto servizio (specificato nel manifesto dell'applicazione precedente) che illustra le caratteristiche di contenitore

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>
