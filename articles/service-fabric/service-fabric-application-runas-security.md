<properties
   pageTitle="Informazioni sui criteri di sicurezza di servizio e applicazione di servizio tessuti | Microsoft Azure"
   description="Cenni preliminari sull'esecuzione di un'applicazione di servizio tessuti in sistema e gli account di sicurezza locale, incluso il punto di SetupEntry in un'applicazione deve eseguire un'azione privilegiata prima di iniziare"
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
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurare i criteri di sicurezza per un'applicazione
Azure tessuti servizio consente di proteggere le applicazioni in esecuzione in un cluster di account utente diversi. Servizio tessuti protegge anche le risorse utilizzate dalle applicazioni in fase di distribuzione con l'account utente, ad esempio file, directory e certificati. In questo modo le applicazioni in esecuzione, anche in un ambiente host condiviso, sicura rispetto a altro. 

Per impostazione predefinita, le applicazioni di servizio tessuti eseguite con l'account di cui viene eseguito il processo di Fabric.exe. Servizio tessuti offre la possibilità di eseguire applicazioni con un account utente locale o di un account di sistema locale, nel manifesto dell'applicazione. Tipi di account di sistema locale supportato per sono **LocalUser**, **servizio di rete**, **LocalService**e **LocalSystem**.

 Durante l'esecuzione di tessuti servizio Windows Server nel centro dati tramite il programma di installazione autonoma, è possibile utilizzare gli account di dominio Active Directory (AD).

Gruppi di utenti possono essere definiti e creati in modo che uno o più utenti possono essere aggiunti a ogni gruppo da gestire insieme. Questo è utile quando sono presenti più utenti per i punti di ingresso servizio diverso ed è necessario avere determinati privilegi comuni che sono disponibili a livello di gruppo.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurare i criteri per servizio SetupEntryPoint

Come descritto nel [modello di applicazione](service-fabric-application-model.md) **SetupEntryPoint** è un punto di ingresso privilegi che viene eseguita con le stesse credenziali servizio tessuti (in genere l'account di *servizio di rete* ) prima di qualsiasi altro punto di ingresso. Il file eseguibile specificato dal **punto di ingresso** è in genere l'host del servizio di esecuzione prolungata, in modo che una voce di installazione separati scegliere consente di evitare la necessità di eseguire l'host del servizio eseguibile con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato dal **punto di ingresso** viene eseguito dopo **SetupEntryPoint** chiusura correttamente. Il processo risultante è monitorare e si riavvia, iniziando nuovamente **SetupEntryPoint**se mai termina o si blocca.

Di seguito è un esempio di manifesto servizio semplice che mostra la SetupEntryPoint e il punto di ingresso principale per il servizio.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurare i criteri utilizzando un account locale

Dopo aver configurato il servizio per scegliere una voce di configurazione, è possibile modificare le autorizzazioni di sicurezza utilizzabile nel manifesto dell'applicazione. Nell'esempio followin viene illustrato come configurare il servizio venga eseguito con privilegi di account utente amministratore.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Prima di tutto creare una sezione di **identità** con un nome utente, ad esempio SetupAdminUser. Indica che l'utente sia un membro del gruppo Administrators sistema.

Quindi, nella sezione **ServiceManifestImport** configurare un criterio per applicare questa capitale a **SetupEntryPoint**. In questo modo servizio tessuti quando viene eseguito il file **MySetup.bat** , deve essere RunAs con privilegi di amministratore. Dato che si dispone di un criterio *non* applicato al punto di ingresso principale, il codice di **MyServiceHost.exe** viene eseguito il sistema di account di **servizio di rete** . Questo è l'account predefinito che tutti i punti di ingresso servizio vengono eseguiti come.

Aggiungere ora il file MySetup.bat al progetto di Visual Studio per testare i privilegi di amministratore. In Visual Studio, fare clic sul progetto di servizio e aggiungere un nuovo file denominato MySetup.bat.

Poi, assicurarsi che il file MySetup.bat sia incluso nel pacchetto di servizio. Per impostazione predefinita, non è. Selezionare il file, pulsante destro del mouse per visualizzare il menu di scelta rapida e scegliere **proprietà**. Nella finestra di dialogo delle proprietà verificare che **copiare Directory di Output** sia impostato su **Copia se più recente**. Visualizzare la schermata seguente riportata di seguito.

![Visual Studio CopyToOutput per file batch SetupEntryPoint][image1]

A questo punto, aprire il file MySetup.bat e aggiungere i comandi seguenti:

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Successivamente, creare e distribuire la soluzione in un cluster di sviluppo locale.  Dopo l'avvio del servizio, come illustrato in Esplora tessuti servizio, è possibile vedere che il MySetup.bat è stata stabilita in due modi. Aprire un prompt dei comandi di PowerShell e digitare:

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Annotare il nome del nodo in cui il servizio è stato distribuito e avviato in Esplora aree tessuti di servizio, ad esempio, il nodo 2. Passare alla cartella di lavoro istanza dell'applicazione per trovare il file out. txt che mostra il valore di **TestVariable**. Per esempio se il servizio è stato distribuito al nodo 2, sarà possibile accedere a questo percorso per **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurare i criteri di utilizzo di account di sistema locale
Spesso, è preferibile eseguire lo script di avvio con un account di sistema locale anziché un account di amministratori illustrata precedente. Esecuzione di RunAs criteri come amministratori in genere non funzionano bene poiché computer hanno accesso controllo (utente) attivata per impostazione predefinita. In tal caso, **il suggerimento consiste nell'eseguire il SetupEntryPoint come LocalSystem invece di un utente locale aggiunto al gruppo administrators**. Nell'esempio seguente è illustrata l'impostazione SetupEntryPoint per l'esecuzione come LocalSystem.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Comandi di PowerShell da un SetupEntryPoint di avvio
Per eseguire PowerShell dal punto d' **SetupEntryPoint** , è possibile eseguire **PowerShell.exe** in un file batch che punta a un file di PowerShell. Prima di tutto, aggiungere un file di PowerShell per il progetto di servizio, ad esempio **MySetup.ps1**. È necessario ricordare di impostare la proprietà *Copia se più recente* , in modo che il file è incluso anche nel pacchetto di servizio. Nell'esempio seguente Mostra file batch di esempio per avviare un file di PowerShell denominato MySetup.ps1, che consente di impostare una variabile di ambiente di sistema denominata **TestVariable**.


MySetup.bat di avviare file PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Nel file di PowerShell, aggiungere le operazioni seguenti per impostare una variabile di ambiente di sistema:

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Nota:** Per impostazione predefinita quando viene eseguito il file batch esamina cartella dell'applicazione denominata **funziona** per i file. In questo caso, quando viene eseguito MySetup.bat vogliamo per trovare il MySetup.ps1 nella stessa cartella, ovvero la cartella di **pacchetto codice** applicazione. Per modificare questa cartella, impostare la cartella di lavoro, come illustrato di seguito.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>Usare il reindirizzamento console per il debug locale
In alcuni casi è utile per visualizzare l'output esecuzione di script per il debug. A questo scopo è possibile impostare un criterio di reindirizzamento console, che scrive l'output in un file. L'output del file è scritto nella cartella dell'applicazione invitata **log** il nodo in cui l'applicazione è distribuito ed eseguire (vedere dove si trova nell'esempio precedente).

**Nota: non è mai** utilizzare il criterio di reindirizzamento console in un'applicazione all'ambiente di produzione poiché questo può influire sulla failover dell'applicazione. **Solo** utilizzare questo comando per lo sviluppo locale e il debug.  

Nell'esempio seguente è illustrata l'impostazione di reindirizzamento della console con un valore FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Se si cambia ora il file MySetup.ps1 per scrivere un comando **eco** , questo verrà scritto nel file di output per il debug.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Una volta debug di uno script, rimuovere immediatamente il criterio di reindirizzamento console**

## <a name="configure-policy-for-service-code-packages"></a>Configurare i criteri per i pacchetti di codice servizio 
Nella procedura precedente è stato illustrato come applicare criteri RunAs a SetupEntryPoint. Osserviamo approfondita in come creare diversi principali che possono essere applicati come criteri del servizio.

### <a name="create-local-user-groups"></a>Creare gruppi di utenti locali
Gruppi di utenti possono essere definiti e creati che consentono di uno o più utenti da aggiungere a un gruppo. Questa operazione è particolarmente utile se sono presenti più utenti per i punti di ingresso servizio diverso ed è necessario avere determinati privilegi comuni che sono disponibili a livello di gruppo. Nell'esempio seguente mostra un gruppo locale denominato **LocalAdminGroup** con privilegi di amministratore. Due utenti, Customer1 e Customer2, sono stati progettati i membri del gruppo locale.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Creare gli utenti locali
È possibile creare un utente locale che può essere utilizzato per proteggere un servizio all'interno dell'applicazione. Quando viene specificato un tipo di account **LocalUser** nella sezione identità del manifesto dell'applicazione, servizio tessuti crea account utente locali nel computer in cui viene distribuita. Per impostazione predefinita, questi account non dispone degli stessi nomi di quelli specificati nel manifesto dell'applicazione (ad esempio, Customer3 nell'esempio seguente). Se, tuttavia, si vengono generati dinamicamente e presentano password casuali.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Assegnare i criteri per i pacchetti di codice servizio
La sezione **RunAsPolicy** per un **ServiceManifestImport** specifica l'account dalla sezione identità che deve essere utilizzata per eseguire un pacchetto di codice. Inoltre, associa pacchetti di codice dal servizio manifesto con gli account utente nella sezione identità. È possibile specificare questo per l'installazione o i punti di ingresso principale oppure è possibile specificare `All` essere applicato a entrambi. Nell'esempio seguente mostra diversi criteri applicati:

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Se non viene specificato **EntryPointType** , il valore predefinito è impostato su EntryPointType = "Principale". Se si specifica **SetupEntryPoint** è particolarmente utile quando si desidera eseguire alcune operazione di installazione di privilegi elevati con un account di sistema. Il codice servizio effettivo può essere eseguito in un account con privilegi inferiore.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Applicare un criterio predefinito per tutti i pacchetti di codice servizio
La sezione **DefaultRunAsPolicy** viene utilizzata per specificare un account utente predefinito per tutti i pacchetti di codice che non dispone di una specifica **RunAsPolicy** definiti. Se la maggior parte dei pacchetti codice specificati nel manifesto servizio utilizzato da un'applicazione necessarie per l'esecuzione con lo stesso nome utente, l'applicazione appena possibile definire criteri RunAs predefiniti con tale account utente. Nell'esempio seguente specifica che se un pacchetto di codice non dispone di un **RunAsPolicy** specificato, il pacchetto di codice deve essere eseguita in **MyDefaultAccount** specificato nella sezione identità.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>Utilizzo di un utente o gruppo di dominio Active Directory
Per tessuti servizio installato in Windows Server con il programma di installazione autonomo, eseguire il servizio in base alle credenziali per un utente di Active Directory (AD) o un account di gruppo. Nota: Questo è Active Directory locale all'interno del dominio e non con Azure Active Directory (AAD). Se si utilizza un dominio utente o gruppo, è possibile accedere quindi altre risorse del dominio (ad esempio, condivisioni file) che sono state concesse le autorizzazioni.

Nell'esempio seguente mostra un utente di Active Directory denominato *TestUser* con la propria password dominio crittografate utilizzando un certificato denominato *NomeCert*. È possibile utilizzare il `Invoke-ServiceFabricEncryptText` comando Powershell per creare il testo crittografato segreta. Vedere l'articolo [gestione delle informazioni riservate nelle applicazioni di servizio tessuti](service-fabric-application-secret-management.md) per informazioni dettagliate su come. La chiave privata del certificato di decrittografare la password deve essere distribuita sul computer locale in un metodo fuori banda (in Azure tratta tramite Manager delle risorse). Quindi, quando servizio tessuti distribuisce il pacchetto di servizio al computer, è possibile decrittografare il segreto e insieme al nome utente, eseguire l'autenticazione con Active Directory per l'esecuzione in tali credenziali.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Assegnare SecurityAccessPolicy per gli endpoint HTTP e HTTPS
Se si applica un criterio di RunAs a un servizio e manifesto servizio dichiara risorse endpoint con il protocollo HTTP, è necessario specificare un **SecurityAccessPolicy** per assicurarsi che le porte a questi endpoint correttamente elencato per l'account utente RunAs quale viene eseguito il servizio controllo di accesso. In caso contrario, **Sys** non dispone dell'accesso al servizio e viene visualizzato errori alle chiamate dal client. Nell'esempio followin riguarda l'account Customer3 un endpoint chiamato **ServiceEndpointName**, assegnando diritti di accesso completo.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Per l'endpoint HTTPS, è necessario indicare il nome del certificato per tornare al client. È possibile eseguire questa operazione utilizzando **EndpointBindingPolicy**, con il certificato definito in una sezione di certificati nel manifesto dell'applicazione.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Un esempio di manifesto applicazione completa
Manifesto dell'applicazione seguenti sono riportate molte delle diverse impostazioni:

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sul modello di applicazione](service-fabric-application-model.md)
* [Specificare le risorse in un manifesto di servizio](service-fabric-service-manifest-resources.md)
* [Distribuire un'applicazione](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
