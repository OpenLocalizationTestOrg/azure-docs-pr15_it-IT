<properties
    pageTitle="Che cos'è un modello dei servizi Cloud e pacchetto | Microsoft Azure"
    description="Descrive il modello dei servizi cloud (.csdef, cscfg) e (cspkg) in Azure"
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

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Che cos'è il modello dei servizi Cloud e come pacchetto?
Un servizio cloud viene creato da tre componenti, la definizione di servizio _(.csdef)_, la configurazione di servizio _(. cscfg)_e un pacchetto di servizio _(cspkg)_. I **ServiceDefinition.csdef** e **ServiceConfig.cscfg** sono basati su XML e descrivono la struttura del servizio cloud e come è configurato. collettivamente il modello. **ServicePackage.cspkg** è un file zip che viene generato da **ServiceDefinition.csdef** e tra le altre cose, contiene tutte le dipendenze basato su codice binario necessari. Azure crea un servizio cloud da **ServicePackage.cspkg** e **ServiceConfig.cscfg**.

Dopo il servizio cloud è in esecuzione in Azure, è possibile riconfigurare tramite il file **ServiceConfig.cscfg** , ma non è possibile modificare la definizione.

## <a name="what-would-you-like-to-know-more-about"></a>Che cosa si desidera saperne di più?

* Si desiderano ulteriori informazioni sui file [ServiceDefinition.csdef](#csdef) e [ServiceConfig.cscfg](#cscfg) .
* Già conoscere, che forniscono [alcuni esempi](#next-steps) su come è possibile configurare.
* Si desidera creare [ServicePackage.cspkg](#cspkg).
* Utilizzo di Visual Studio e si desidera.
    * [Creare un nuovo servizio cloud][vs_create]
    * [Riconfigurare un servizio cloud esistente][vs_reconfigure]
    * [Distribuire un progetto di servizio Cloud][vs_deploy]
    * [Desktop remoto in un'istanza di servizio cloud][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Il file **ServiceDefinition.csdef** specifica le impostazioni utilizzate da Azure per configurare un servizio cloud. Lo [Schema di definizione del servizio di Azure (.csdef File)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fornisce il formato consentito per un file di definizione del servizio. Nell'esempio seguente mostra le impostazioni definite per i ruoli Web e di lavoro:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
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
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

È possibile fare riferimento a [[Schema di definizione di servizio] per comprendere meglio lo schema XML utilizzato qui, ecco una rapida spiegazione di alcuni degli elementi:

**Siti**  
Contiene le definizioni di applicazioni web o siti Web ospitati in IIS7.

**InputEndpoints**  
Contiene le definizioni per gli endpoint utilizzati per contattare il servizio cloud.

**InternalEndpoints**  
Contiene le definizioni per gli endpoint utilizzati da istanze del ruolo di comunicare con loro.

**ConfigurationSettings**  
Contiene le definizioni di impostazione per le caratteristiche di un ruolo specifico.

**Certificati**  
Contiene le definizioni di certificati sono necessari per un ruolo. Nell'esempio precedente mostra un certificato utilizzato per la configurazione di Azure Connect.

**LocalResources**  
Contiene le definizioni delle risorse di archiviazione locale. Una risorsa di archiviazione locale è una directory riservata nel file system della macchina virtuale durante l'esecuzione di un'istanza di un ruolo.

**Importa**  
Contiene le definizioni di moduli importati. Nell'esempio precedente mostra i moduli per la connessione Desktop remoto e Azure connettersi.

**Avvio**  
Contiene le attività eseguite all'avvio il ruolo. Le attività sono definite in un file eseguibile o cmd.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration. cscfg
La configurazione delle impostazioni per il servizio cloud viene determinata in base ai valori file **ServiceConfiguration. cscfg** . Specificare il numero di istanze che si desidera distribuire per ogni ruolo nel file. I valori per le impostazioni di configurazione definite in file di definizione del servizio vengono aggiunti al file di configurazione del servizio. Identificazioni personali per gli eventuali certificati di gestione associati al servizio cloud aggiunti anche al file. Lo [Schema di configurazione del servizio di Azure (File. cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fornisce il formato consentito per un file di configurazione del servizio.

Il file di configurazione del servizio non è compresso con l'applicazione, ma caricato in Azure come file separato e viene utilizzato per configurare il servizio cloud. È possibile caricare un nuovo file di configurazione del servizio senza ridistribuire il servizio cloud. I valori di configurazione per il servizio cloud possono essere modificati mentre è in esecuzione il servizio cloud. Nell'esempio seguente mostra le impostazioni di configurazione che possono essere definite per i ruoli Web e di lavoro:

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

È possibile fare riferimento allo [Schema di configurazione del servizio](https://msdn.microsoft.com/library/azure/ee758710.aspx) per comprendere meglio lo schema XML utilizzato qui, ecco una rapida descrizione degli elementi:

**Istanze**  
Configura il numero di istanze del ruolo in esecuzione. Per evitare che il servizio cloud potenzialmente non sono più disponibili durante l'aggiornamento, è consigliabile distribuire più istanze dei ruoli basata sul web. In questo modo sono conformi alle linee guida di [Azure calcolare livello contratto di servizio](http://azure.microsoft.com/support/legal/sla/), che garantisce 99,95% connettività esterna per i ruoli esposto a Internet quando vengono distribuite due o più istanze del ruolo di un servizio.

**ConfigurationSettings**  
Consente di configurare le impostazioni per le istanze in esecuzione per un ruolo. Il nome della `<Setting>` elementi devono corrispondere le definizioni di impostazione nel file di definizione del servizio.

**Certificati**  
Configura i certificati utilizzati dal servizio. Nell'esempio precedente viene illustrato come definire il certificato per il modulo di accesso remoto. Impostare il valore dell'attributo *identificazione personale* per l'identificazione del certificato digitale da utilizzare.

<p/>

 >[AZURE.NOTE] Identificazione personale per il certificato può essere aggiunti al file di configurazione in un editor di testo o il valore può essere aggiunti nella scheda **certificati** della pagina **delle proprietà** del ruolo in Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Definizione di istanze del ruolo porte
Azure consente solo un punto di ingresso a un ruolo web. Questo errore indica che tutto il traffico avviene tramite un indirizzo IP. È possibile configurare i siti Web per condividere una porta configurando l'intestazione host per indirizzare le richieste nella posizione appropriata. È inoltre possibile configurare le applicazioni per ascoltare porte note sull'indirizzo IP.

Nell'esempio seguente viene illustrata la configurazione per un ruolo web con un'applicazione web e sito Web. Il sito Web è configurato come la posizione di immissione predefinita sulla porta 80 e le applicazioni web sono configurate per ricevere richieste da un'intestazione host alternativo che è denominata "mail.mysite.cloudapp.net".

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>La modifica della configurazione di un ruolo
È possibile aggiornare la configurazione del servizio cloud mentre è in esecuzione in Azure, senza disconnettere il servizio. Per modificare le informazioni di configurazione, è possibile caricare un nuovo file di configurazione, o modificare il file di configurazione in posizione e applicarla al servizio in esecuzione. La configurazione di un servizio, è possono effettuare le seguenti modifiche:

- **Modificare i valori delle impostazioni di configurazione**  
Quando una configurazione le modifiche alle impostazioni, un'istanza del ruolo possibile applicare le modifiche mentre l'istanza sia online o per Cestino normalmente l'istanza e applicare le modifiche mentre l'istanza è offline.

- **Modificare la topologia del servizio di istanze del ruolo**  
Topologia modifiche non interessano le istanze in esecuzione, tranne nel punto in cui viene rimosso un'istanza. Tutte le istanze rimanenti in genere non è necessario essere riutilizzata; Tuttavia, si può scegliere di Cestino istanze del ruolo in risposta a una modifica della topologia.

- **Modificare l'identificazione personale del certificato**  
È possibile aggiornare solo un certificato quando un'istanza del ruolo è offline. Se un certificato viene aggiunto, eliminato o modificato mentre un'istanza del ruolo è in linea, Azure normalmente avrà l'istanza offline per aggiornare il certificato e riportare in linea dopo la modifica è stata completata.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Gestione delle modifiche di configurazione con eventi di Runtime del servizio
La [Libreria di Runtime Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) include lo spazio dei nomi [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) che fornisce le classi per interagire con l'ambiente di Azure dal codice in esecuzione in un'istanza di un ruolo. La classe [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) definisce gli eventi seguenti vengono generati prima e dopo ogni modifica della configurazione:

- **Evento di [Modifica](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)**  
Si verifica prima che venga applicata la modifica di configurazione per un'istanza specificata di un ruolo che consente di istanze del ruolo se necessario.
- **Evento [Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Si verifica dopo la modifica di configurazione viene applicata a una determinata istanza di un ruolo.

> [AZURE.NOTE] Poiché le modifiche di certificato sempre disconnettere le istanze di un ruolo, tali elementi non generano gli eventi RoleEnvironment.Changing o RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Per distribuire un'applicazione come un servizio cloud di Azure, è necessario prima di tutto creare un pacchetto dell'applicazione nel formato appropriato. È possibile utilizzare lo strumento della riga di comando **CSPack** (installato con [Azure SDK](https://azure.microsoft.com/downloads/)) per creare il pacchetto con estensione alternativo per Visual Studio.

**CSPack** Usa il contenuto del file di definizione di servizio e file di configurazione del servizio per definire il contenuto del pacchetto. **CSPack** genera un file di pacchetto dell'applicazione (cspkg) che è possibile caricare in Azure tramite il [portale di Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Per impostazione predefinita, viene assegnato il pacchetto `[ServiceDefinitionFileName].cspkg`, ma è possibile specificare un nome diverso tramite il `/out` possibilità di **CSPack**.

È in genere situate nella **CSPack**  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (in windows) è disponibile, eseguire il collegamento di **Microsoft Azure al prompt dei comandi** installata con SDK.  
>  
>Eseguire il programma CSPack.exe autonomamente per vedere tutti i comandi e opzioni possibili.

<p />

>[AZURE.TIP]
Eseguire il servizio cloud localmente nell' **Emulatore di calcolo di Microsoft Azure**, utilizzare l'opzione **/copyonly** che questa opzione consente di copiare i file binari per un'applicazione di un layout di directory da cui possono essere eseguiti nell'emulatore di calcolo.

### <a name="example-command-to-package-a-cloud-service"></a>Comando di esempio per creare un pacchetto di un servizio cloud
Nell'esempio seguente viene creato un pacchetto dell'applicazione che contiene le informazioni per un ruolo web. Il comando specifica il file di definizione del servizio da utilizzare, la directory in cui è possono trovare file binari, e il nome del file di pacchetto.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Se l'applicazione contiene un ruolo web e un ruolo di lavoro, viene utilizzato il comando seguente:

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Nel punto in cui le variabili sono i seguenti:

| Variabile                  | Valore |
| ------------------------- | ----- |
| \[DirectoryName\]         | Sottodirectory nella directory principale del progetto che contiene il file .csdef del progetto Azure.|
| \[ServiceDefinition\]     | Il nome del file di definizione del servizio. Per impostazione predefinita, il file è denominato ServiceDefinition.csdef.  |
| \[OutputFileName\]        | Il nome del file di pacchetto generato. In genere questo è il nome dell'applicazione. Se non viene specificato alcun nome file, viene creato il pacchetto di applicazione come \[NomeApplicazione\]cspkg.|
| \[RoleName\]              | Il nome del ruolo come definito nel file di definizione del servizio.|
| \[RoleBinariesDirectory] | Il percorso di file binari per il ruolo.|
| \[VirtualPath\]           | Directory fisica per ogni percorso definito nella sezione siti della definizione del servizio.|
| \[PhysicalPath\]          | Directory fisiche del contenuto per ogni percorso definito il nodo del sito della definizione del servizio.|
| \[RoleAssemblyName\]      | Il nome del file binario per il ruolo.| 


## <a name="next-steps"></a>Passaggi successivi

Sto creando un pacchetto di servizi cloud e si desidera.

* [Configurazione desktop remoto per un'istanza del servizio cloud][remotedesktop]
* [Distribuire un progetto di servizio Cloud][deploy]

Utilizzo di Visual Studio e si desidera.

* [Creare un nuovo servizio cloud][vs_create]
* [Riconfigurare un servizio cloud esistente][vs_reconfigure]
* [Distribuire un progetto di servizio Cloud][vs_deploy]
* [Configurazione desktop remoto per un'istanza del servizio cloud][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md
