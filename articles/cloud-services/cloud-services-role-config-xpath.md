<properties 
pageTitle="Finestra delle irregolarità XPath configurazione ruolo servizi cloud | Microsoft Azure" 
description="Le varie impostazioni XPath è possibile usare nel file config ruolo di servizio cloud per esporre impostazioni come una variabile di ambiente." 
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
ms.date="08/10/2016" 
ms.author="adegeo"/>

# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Esporre le impostazioni di configurazione ruolo come una variabile di ambiente con XPath

Nel file di definizione del servizio web ruolo o lavoro servizio cloud, è possibile esporre i valori di configurazione di runtime come variabili. Sono supportati i seguenti valori di XPath (che corrispondono ai valori API).

Sono disponibili tramite la raccolta [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) anche questi valori XPath. 

## <a name="app-running-in-emulator"></a>App in esecuzione nell'emulatore

Indica che l'app sia in esecuzione nell'emulatore.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/Deployment/@emulated" |
| Codice  | var x = RoleEnvironment.IsEmulated; |


## <a name="deployment-id"></a>ID di distribuzione

Recupera l'ID di distribuzione per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/Deployment/@id" |
| Codice  | var deploymentId = RoleEnvironment.DeploymentId; |


## <a name="role-id"></a>ID ruolo 

Recupera l'ID di ruolo corrente per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/@id" |
| Codice  | var id = RoleEnvironment.CurrentRoleInstance.Id; |


## <a name="update-domain"></a>Dominio di aggiornamento

Recupera il dominio di aggiornamento dell'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Codice  | var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |


## <a name="fault-domain"></a>Dominio di guasto

Recupera il dominio guasto dell'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Codice  | var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |


## <a name="role-name"></a>Nome del ruolo

Recupera il nome del ruolo delle istanze.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Codice  | var rname = RoleEnvironment.CurrentRoleInstance.Role.Name;  |


## <a name="config-setting"></a>Impostazione di configurazione

Recupera il valore dell'impostazione di configurazione specificato.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Codice  | impostazione di var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |
 
## <a name="local-storage-path"></a>Percorso di archiviazione locale

Recupera il percorso di archiviazione locale per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Codice  | var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |


## <a name="local-storage-size"></a>Dimensioni dello spazio di archiviazione locale

Recupera le dimensioni dell'archivio locale per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Codice  | var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocollo endpoint 

Recupera il protocollo dell'endpoint per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Codice  | protocollo var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocollo; |

## <a name="endpoint-ip"></a>Endpoint IP

Ottiene l'indirizzo IP dell'endpoint specificato.

| Tipo | Esempio |
| ----- | ---- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Codice  | indirizzo var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndPoint. Address |

## <a name="endpoint-port"></a>Porta endpoint 

Recupera la porta dell'endpoint per l'istanza.

| Tipo  | Esempio |
| ----- | ------- |
| Espressione XPath | xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Codice  | porta var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndPoint. Port; |





## <a name="example"></a>Esempio

Ecco un esempio di un ruolo di lavoro che consente di creare un'attività di avvio con una variabile di ambiente `TestIsEmulated` impostato sul [ @emulated valore xpath](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui file [ServiceConfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Creare un pacchetto [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Attivare [desktop remoto](cloud-services-role-enable-remote-desktop.md) per un ruolo.
