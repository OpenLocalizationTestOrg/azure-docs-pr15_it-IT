<properties
   pageTitle="Specificare gli endpoint del servizio di assistenza tessuti | Microsoft Azure"
   description="Come descrivere le risorse endpoint nel manifesto servizio, ad esempio come configurare i punti finali HTTPS"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>

# <a name="specify-resources-in-a-service-manifest"></a>Specificare le risorse in un manifesto di servizio

## <a name="overview"></a>Panoramica

Manifesto servizio consente di risorse che vengono usate dal servizio per essere dichiarati modificata senza modificare il codice compilato. Azure tessuti servizio supporta la configurazione di risorse endpoint del servizio. Tramite SecurityGroup nel manifesto dell'applicazione, è possibile controllare l'accesso alle risorse che specificato nel manifesto di servizio. La dichiarazione di risorse consente queste risorse essere modificato in fase di distribuzione, ovvero che il servizio non è necessario presentare un nuovo meccanismo di configurazione. Definizione dello schema per il file ServiceManifest.xml viene installata con il servizio tessuti SDK e strumenti per *C:\Programmi\Microsoft c:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*.

## <a name="endpoints"></a>Punti finali

Se una risorsa endpoint è stata definita nel manifesto di servizio, tessuti servizio assegna porte dall'intervallo di porte applicazione riservate quando una porta non viene specificata in modo esplicito. Ad esempio, osservare l'endpoint *ServiceEndpoint1* specificato nel manifesto frammento fornito dopo il paragrafo. Inoltre, i servizi possono richiedere anche una porta specifica in una risorsa. Repliche di servizio in esecuzione su nodi cluster diversi possono essere assegnate numeri di porta diversi, mentre le repliche di un servizio in esecuzione sullo stesso nodo condividono la porta. Repliche servizio è quindi possono utilizzare queste porte in base alle esigenze per la replica e attendere le richieste di client.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Fare riferimento alla [configurazione con informazioni sullo stato servizi affidabili](service-fabric-reliable-services-configuration.md) per leggere altre informazioni su riferimento endpoint dalle impostazioni di configurazione del pacchetto di file (Settings).

## <a name="example-specifying-an-http-endpoint-for-your-service"></a>Esempio: specifica un endpoint HTTP per il servizio

Nel manifesto del servizio seguente definisce una risorsa endpoint TCP e due HTTP endpoint le risorse di &lt;risorse&gt; elemento.

Endpoint HTTP vengono automaticamente che ACL sarebbe dal servizio tessuti.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## <a name="example-specifying-an-https-endpoint-for-your-service"></a>Esempio: specifica un endpoint HTTPS per il servizio

Il protocollo HTTPS fornisce autenticazione server e viene usato anche per la crittografia delle comunicazioni client / server. Per attivare il servizio di assistenza tessuti HTTPS, specificare il protocollo nel *risorse -> endpoint -> Endpoint* sezione del manifesto servizio, come illustrato in precedenza per l'endpoint *ServiceEndpoint3*.

>[AZURE.NOTE] Impossibile modificare il protocollo del servizio durante l'aggiornamento dell'applicazione senza che che costituiscono un sostanziale cambia.


Ecco un esempio ApplicationManifest che è necessario impostare per HTTPS. Identificazione personale per il certificato deve essere fornito. Il EndpointRef è un riferimento a EndpointResource in ServiceManifest, per il quale si imposta il protocollo HTTPS. È possibile aggiungere più EndpointCertificate.  

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```
