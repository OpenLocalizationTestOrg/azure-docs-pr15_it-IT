<properties 
pageTitle="Comunicazioni per i ruoli di servizi Cloud | Microsoft Azure" 
description="Istanze del ruolo nei servizi Cloud possono contenere i punti finali (http, https, tcp, udp) definiti per essi in grado di comunicare con l'esterno o tra le altre istanze del ruolo." 
services="cloud-services" 
documentationCenter="" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="enable-communication-for-role-instances-in-azure"></a>Attivare la comunicazione per istanze del ruolo di azure

Ruoli del servizio cloud comunicano tramite connessioni interne ed esterne. Connessioni esterne vengono chiamate **endpoint input** durante le connessioni interne sono denominate **endpoint interno**. In questo argomento viene descritto come modificare la [definizione di servizio](cloud-services-model-and-package.md#csdef) per creare i punti finali.


## <a name="input-endpoint"></a>Endpoint di input
L'endpoint di input viene utilizzato per esporre una porta verso l'esterno. Specificare il tipo di protocollo e la porta dell'endpoint che viene applicato per entrambe le porte interne ed esterne per il punto finale. Se si desidera, è possibile specificare un'altra porta interna per l'endpoint con l'attributo [porta locale](https://msdn.microsoft.com/library/azure/gg557552.aspx#InputEndpoint) .

L'endpoint di input è possibile utilizzare i seguenti protocolli: **http, https, tcp e udp**.

Per creare un endpoint di input, aggiungere l'elemento figlio **InputEndpoint** all'elemento **endpoint** del ruolo di lavoro o web.

```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
</Endpoints> 
```

## <a name="instance-input-endpoint"></a>Endpoint input istanza
Endpoint input istanza sono simili a endpoint ma consente di input mappare le porte pubblico specifiche per ogni istanza del ruolo singoli tramite l'inoltro di porta nel servizio di bilanciamento del carico. È possibile specificare un'unica porta pubblico o un intervallo di porte.

L'endpoint di input istanza possibile utilizzare solo **tcp** o **udp** come protocollo.

Per creare un endpoint input istanza, aggiungere l'elemento figlio **InstanceInputEndpoint** all'elemento **endpoint** del ruolo di lavoro o web.

```xml
<Endpoints>
  <InstanceInputEndpoint name="Endpoint2" protocol="tcp" localPort="10100">
    <AllocatePublicPortFrom>
      <FixedPortRange max="10109" min="10105" />
    </AllocatePublicPortFrom>
  </InstanceInputEndpoint>
</Endpoints>
```

## <a name="internal-endpoint"></a>Endpoint interno
Sono disponibili per la comunicazione istanza per istanza endpoint interno. La porta è facoltativa e se omesso, viene assegnata una porta dinamica all'endpoint. Un intervallo di porte può essere utilizzato. È previsto un limite di cinque endpoint interno per ogni ruolo.

L'endpoint interno possa utilizzare i seguenti protocolli: **http, tcp e udp, qualsiasi**.

Per creare un endpoint di input interno, aggiungere l'elemento figlio **InternalEndpoint** all'elemento **endpoint** del ruolo di lavoro o web.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" port="8999" />
</Endpoints> 
```

È anche possibile utilizzare un intervallo di porte.

```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any">
    <FixedPortRange max="8995" min="8999" />
  </InternalEndpoint>
</Endpoints>
```


## <a name="worker-roles-vs-web-roles"></a>Ruoli di lavoro e ruoli Web

C'è una differenza secondaria con i punti finali quando si utilizzano lavoro e ruoli web. Il ruolo web deve avere almeno un unico endpoint input mediante il protocollo **HTTP** .


```xml
<Endpoints>
  <InputEndpoint name="StandardWeb" protocol="http" port="80" localPort="80" />
  <!-- more endpoints may be declared after the first InputEndPoint -->
</Endpoints>
```

## <a name="using-the-net-sdk-to-access-an-endpoint"></a>Utilizzando .NET SDK per accedere a un estremo
La raccolta gestite Azure fornisce metodi per istanze del ruolo di comunicare in fase di esecuzione. Dal codice in esecuzione all'interno di un'istanza del ruolo, è possibile recuperare informazioni l'esistenza di altre istanze del ruolo e i punti finali e le informazioni relative all'istanza corrente di ruolo.

> [AZURE.NOTE] È possibile recuperare solo informazioni istanze del ruolo che eseguono del servizio cloud e che definiscono almeno un endpoint interno. È possibile ottenere dati relativi a istanze di ruolo in esecuzione in un servizio diverso.

La proprietà [istanze](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.role.instances.aspx) per recuperare istanze di un ruolo. Prima di tutto usare [CurrentRoleInstance](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.currentroleinstance.aspx) per restituire un riferimento all'istanza corrente di ruolo e quindi utilizzare la proprietà [ruolo](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.role.aspx) per restituire un riferimento al ruolo stesso.

Quando ci si connette a un'istanza di ruolo a livello di programmazione tramite .NET SDK, è relativamente semplice accedere alle informazioni di endpoint. Dopo aver già collegato a un ambiente di ruolo specifico, ad esempio, è possibile ottenere la porta di uno specifico endpoint con questo codice:

```csharp
int port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["StandardWeb"].IPEndpoint.Port;
```

La proprietà **istanze** restituisce un insieme di oggetti **RoleInstance** . Questo insieme contiene sempre l'istanza corrente. Se il ruolo non viene definito un endpoint interno, l'insieme include l'istanza corrente, ma non altre istanze. Il numero di istanze del ruolo nella raccolta viene mantenuto 1 nel caso in cui nessun endpoint interno definito per il ruolo. Se il ruolo definisce un endpoint interno, le istanze sono individuabili in fase di esecuzione e il numero di istanze dell'insieme corrisponderà al numero di istanze di specificate per il ruolo di file di configurazione del servizio.

> [AZURE.NOTE] La raccolta di Azure gestite non consentono di determinare lo stato delle altre istanze di ruolo, ma è possibile implementare tali valutazioni integrità personalmente se questa funzionalità è necessario che il servizio. È possibile utilizzare [Azure diagnostica](cloud-services-dotnet-diagnostics.md) per ottenere informazioni sull'esecuzione di istanze del ruolo.

Per determinare il numero di porta per un endpoint interno di un'istanza del ruolo, è possibile utilizzare la proprietà [InstanceEndpoints](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstance.instanceendpoints.aspx) per restituire un oggetto dizionario che contiene i nomi degli endpoint e le porte e indirizzi IP corrispondenti. La proprietà [IPEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleinstanceendpoint.ipendpoint.aspx) restituisce l'indirizzo IP e porte per un endpoint specificato. La proprietà **PublicIPEndpoint** restituisce la porta per un endpoint di bilanciamento del carico. Parte di indirizzo IP della proprietà **PublicIPEndpoint** non viene usata.

Ecco un esempio che scorre istanze del ruolo.

```csharp
foreach (RoleInstance roleInst in RoleEnvironment.CurrentRoleInstance.Role.Instances)
{
    Trace.WriteLine("Instance ID: " + roleInst.Id);
    foreach (RoleInstanceEndpoint roleInstEndpoint in roleInst.InstanceEndpoints.Values)
    {
        Trace.WriteLine("Instance endpoint IP address and port: " + roleInstEndpoint.IPEndpoint);
    }
}
```

Ecco un esempio di un ruolo di lavoro che ottiene l'endpoint esposto tramite la definizione di servizio e viene avviata l'attesa per le connessioni.

> [AZURE.WARNING] Questo codice funziona solo per un servizio distribuito. Durante l'esecuzione nell'emulatore di calcolo Azure, elementi di configurazione del servizio che creano endpoint porta diretta (**InstanceInputEndpoint** elementi) vengono ignorati.

```csharp
using System;
using System.Diagnostics;
using System.Linq;
using System.Net;
using System.Net.Sockets;
using System.Threading;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.Diagnostics;
using Microsoft.WindowsAzure.ServiceRuntime;
using Microsoft.WindowsAzure.StorageClient;

namespace WorkerRole1
{
  public class WorkerRole : RoleEntryPoint
  {
    public override void Run()
    {
      try
      {
        // Initialize method-wide variables
        var epName = "Endpoint1";
        var roleInstance = RoleEnvironment.CurrentRoleInstance;
        
        // Identify direct communication port
        var myPublicEp = roleInstance.InstanceEndpoints[epName].PublicIPEndpoint;
        Trace.TraceInformation("IP:{0}, Port:{1}", myPublicEp.Address, myPublicEp.Port);

        // Identify public endpoint
        var myInternalEp = roleInstance.InstanceEndpoints[epName].IPEndpoint;
                
        // Create socket listener
        var listener = new Socket(
          myInternalEp.AddressFamily, SocketType.Stream, ProtocolType.Tcp);
                
        // Bind socket listener to internal endpoint and listen
        listener.Bind(myInternalEp);
        listener.Listen(10);
        Trace.TraceInformation("Listening on IP:{0},Port: {1}",
          myInternalEp.Address, myInternalEp.Port);

        while (true)
        {
          // Block the thread and wait for a client request
          Socket handler = listener.Accept();
          Trace.TraceInformation("Client request received.");

          // Define body of socket handler
          var handlerThread = new Thread(
            new ParameterizedThreadStart(h =>
            {
              var socket = h as Socket;
              Trace.TraceInformation("Local:{0} Remote{1}",
                socket.LocalEndPoint, socket.RemoteEndPoint);

              // Shut down and close socket
              socket.Shutdown(SocketShutdown.Both);
              socket.Close();
            }
          ));

          // Start socket handler on new thread
          handlerThread.Start(handler);
        }
      }
      catch (Exception e)
      {
        Trace.TraceError("Caught exception in run. Details: {0}", e);
      }
    }

    public override bool OnStart()
    {
      // Set the maximum number of concurrent connections 
      ServicePointManager.DefaultConnectionLimit = 12;

      // For information on handling configuration changes
      // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.
      return base.OnStart();
    }
  }
}
```

## <a name="network-traffic-rules-to-control-role-communication"></a>Regole di traffico di rete per controllare la comunicazione ruolo
Dopo aver definito i punti finali interni, è possibile aggiungere regole di traffico di rete (in base i punti finali creato) per controllare come istanze del ruolo possano comunicare con loro. Il diagramma seguente illustra alcuni scenari comuni per il controllo delle comunicazioni di ruolo:

![Scenari di regole il traffico di rete] (./media/cloud-services-enable-communication-role-instances/scenarios.png "Scenari di regole il traffico di rete")

Nell'esempio seguente mostra le definizioni di ruoli per i ruoli mostrati nella figura precedente. Ogni definizione di ruolo include almeno un endpoint interno definito:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalTCP1" protocol="tcp" />
    </Endpoints>
  </WebRole>
  <WorkerRole name="WorkerRole1">
    <Endpoints>
      <InternalEndpoint name="InternalTCP2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
  <WorkerRole name="WorkerRole2">
    <Endpoints>
      <InternalEndpoint name="InternalTCP3" protocol="tcp" />
      <InternalEndpoint name="InternalTCP4" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

> [AZURE.NOTE] Limitazione delle comunicazioni tra i ruoli può verificarsi con endpoint interno di entrambi fissa e assegnato automaticamente le porte.

Per impostazione predefinita, dopo avere definito un endpoint interno, può avvenire la comunicazione da un ruolo all'endpoint interno di un ruolo senza restrizioni. Per limitare la comunicazione, è necessario aggiungere un elemento **NetworkTrafficRules** all'elemento **ServiceDefinition** nel file di definizione del servizio.

### <a name="scenario-1"></a>Scenario 1
Consenti solo il traffico di rete da **WebRole1** per **WorkerRole1**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-2"></a>Scenario 2
Consente solo il traffico di rete da **WebRole1** **WorkerRole1** e **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-3"></a>Scenario 3
Consente solo il traffico di rete da **WebRole1** **WorkerRole1**e **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

### <a name="scenario-4"></a>Scenario di 4
Consente solo il traffico di rete da **WebRole1** **WorkerRole1**, **WebRole1** a **WorkerRole2**e **WorkerRole1** a **WorkerRole2**.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo>
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP2" roleName="WorkerRole1"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP3" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WorkerRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
  <NetworkTrafficRules>
    <OnlyAllowTrafficTo >
      <Destinations>
        <RoleEndpoint endpointName="InternalTCP4" roleName="WorkerRole2"/>
      </Destinations>
      <AllowAllTraffic/>
      <WhenSource matches="AnyRule">
        <FromRole roleName="WebRole1"/>
      </WhenSource>
    </OnlyAllowTrafficTo>
  </NetworkTrafficRules>
</ServiceDefinition>
```

Un riferimento di schema XML per gli elementi usato in precedenza sono disponibili [qui](https://msdn.microsoft.com/library/azure/gg557551.aspx).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul servizio Cloud [modello](cloud-services-model-and-package.md).